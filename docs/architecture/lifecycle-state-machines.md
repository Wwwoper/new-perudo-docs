# Perudo — жизненные циклы и переходы состояний

Версия: 1.0
Статус: логический контракт состояний, согласующий Game Domain, Exercise Engine и Learning Progress.

## 1. Принципы

- У каждого состояния есть один владелец.
- Переход выполняется только командой/use case, который проверяет предусловия.
- Повтор операции с тем же устойчивым ID и payload возвращает прежний результат.
- Неизвестное или запрещённое состояние не исправляется молча.
- Время и статусы хранятся как факты; UI не является источником истины.

## 2. Exercise instance

Владелец: Exercise Engine.

```text
CANDIDATE (transient)
  -> VALIDATED (transient)
  -> PERSISTED
  -> AVAILABLE
```

Ошибочные/невалидные кандидаты завершаются как `REJECTED` и не показываются пользователю. `PERSISTED` означает, что immutable экземпляр сохранён вместе с `PublicExercise`, `PrivateExerciseData`, seed/config и ссылками версий. `AVAILABLE` означает, что публичное представление может быть выдано клиенту.

Правила:

- candidate не является упражнением, доступным пользователю;
- повтор `CreateExercise(operation_id, same payload)` возвращает тот же `exercise_id`;
- тот же operation_id с другим payload даёт IDEMPOTENCY_CONFLICT;
- содержимое сохранённого Exercise не меняется; исправление генератора создаёт новую версию/экземпляр;
- удаление/архивация экземпляров в MVP не определены и не должны реализовываться как неявная очистка.

## 3. ExerciseAttempt

Владелец: Learning Progress.

```text
             +------------------+
             |                  v
NOT_CREATED -> STARTED -> IN_PROGRESS -> COMPLETED
                    \              \
                     \--------------> ABANDONED
```

Допустимые переходы:

| Из | В | Условие |
|---|---|---|
| отсутствует | STARTED | явный StartExerciseAttempt, упражнение существует и доступно |
| STARTED | IN_PROGRESS | первая сохранённая отправка или другое зафиксированное действие в упражнении |
| STARTED | COMPLETED | только если все обязательные критерии уже выполнены (редкий допустимый случай) |
| STARTED | ABANDONED | явный отказ/прерывание по разрешённому сценарию |
| IN_PROGRESS | COMPLETED | все обязательные шаги удовлетворяют completion criteria |
| IN_PROGRESS | ABANDONED | явный отказ/прерывание по разрешённому сценарию |

Запрещено:

- `COMPLETED -> IN_PROGRESS/STARTED`;
- `ABANDONED -> IN_PROGRESS/COMPLETED`;
- повторный старт существующего attempt_id как новая попытка;
- автоматическое создание попытки только из-за генерации/предзагрузки упражнения.

Повторное прохождение — новый ExerciseAttempt и новый Exercise instance того же типа/сложности согласно принятому контракту повторения.

## 4. AnswerSubmission

Владелец: Learning Progress.

```text
RECEIVED -> EVALUATED -> APPLIED_TO_PROGRESS
    |           |
    +-----------+----> FAILED_RETRYABLE (операционный статус обработки)
```

`FAILED_RETRYABLE` — не новый пользовательский факт и не замена исходного этапа; это метаданные обработки, позволяющие повторить незавершённую стадию. Терминальный отказ по несовместимой версии/нарушению контракта должен сохранять исходную отправку и отдельный безопасный код ошибки.

Правила:

- `submission_id` уникален в области попытки; повтор с тем же payload возвращает исходную запись;
- повтор с тем же ID и иным payload — конфликт;
- Evaluation выполняется по сохранённым exercise/evaluator versions;
- `EvaluationResult` прикрепляется один раз; конфликтующие повторные результаты не перезаписывают сохранённый результат;
- применение к Progress выполняется идемпотентно по submission_id;
- Feedback показывается после сохранения EvaluationResult; исправление доступно после feedback;
- отправка после COMPLETED запрещена, если отдельный продуктовый сценарий не введёт post-completion review без изменения оценки.

## 5. ReviewTask

Владелец: Learning Progress.

```text
PENDING -> IN_PROGRESS -> COMPLETED
    |           |
    +----------> SKIPPED
    +----------> CANCELLED
```

Рекомендации:

- повторная генерация одной и той же причины review не создаёт дубль активной задачи;
- завершение задачи связывается с новым упражнением/попыткой;
- SKIPPED не означает, что навык освоен;
- CANCELLED применяется при утрате актуальности/сбросе состояния по явной политике.

## 6. Game lifecycle

Владелец: Game Domain.

```text
CREATED -> IN_PROGRESS -> COMPLETED
                   \----> ABANDONED
```

Внутри IN_PROGRESS действуют отдельные состояния раунда и хода, описанные в `game-domain.md`. `ROUND_COMPLETED` завершает раунд, но не партию. Новая команда начинает следующий раунд. `COMPLETED` наступает при одном активном участнике.

Для ставки:

```text
BID_PLACED -> TURN_ADVANCED
```

Это два самостоятельных доменных события. Между ними допускается сохранённое промежуточное состояние. Application сохраняет/восстанавливает прикладную операцию `PlaceBidAndAdvanceTurn`; `AdvanceTurn` идемпотентен и не переводит очередь более одного раза.

Для Dudo последовательность разрешения и последствия потери кубика фиксируются атомарно одной доменной операцией. Частично завершённый Dudo не является допустимым внешним состоянием.

## 7. Согласование статусов и видимости

| Факт | Кто меняет | Кто может читать |
|---|---|---|
| Exercise status | Exercise Engine | Application; Presentation получает public view |
| Attempt status | Learning Progress | Application, разрешённые read queries |
| Submission processing status | Learning Progress | Application; пользовательский статус — через безопасный DTO |
| EvaluationResult | Learning Progress хранит; Evaluation вычисляет | Feedback/Application; Presentation только безопасное представление |
| ReviewTask status | Learning Progress | Learning Strategy/Application |
| Game/round/turn status | Game Domain | PlayerGameView/ExerciseGameView через адаптер |

## 8. Минимальный набор тестов переходов

- таблица допустимых/запрещённых переходов для каждого агрегата;
- повтор команды с тем же ID/payload;
- конфликт того же ID с другим payload;
- сбой после каждого durable этапа отправки и последующее восстановление;
- повтор CompleteAttempt и ApplySubmissionToProgress;
- отсутствие полного эталона до COMPLETED;
- попытка отправки после COMPLETED;
- сбой между BID_PLACED и TURN_ADVANCED;
- атомарность Dudo;
- восстановление партии и попытки после перезапуска.
