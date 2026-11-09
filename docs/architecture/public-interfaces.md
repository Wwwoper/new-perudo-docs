# Perudo — минимальный каталог публичных межмодульных интерфейсов

Версия: 1.0
Статус: предлагаемый логический контракт; имена и сигнатуры не являются утверждённым Python API.

## 1. Принципы

1. Межмодульный интерфейс описывает бизнес-намерение, а не внутреннюю структуру хранения.
2. Команды изменяют состояние; запросы возвращают read-only представления.
3. DTO неизменяемы на границах модулей.
4. Private data не включается в публичные DTO.
5. Идентификаторы версий передаются явно там, где важна воспроизводимость.
6. Вызовы выполняются синхронно в модульном монолите MVP; интерфейсы не предполагают брокер или HTTP.
7. Ошибки контракта типизированы и не содержат закрытых данных.

## 2. Общие типы

```python
@dataclass(frozen=True)
class OperationMeta:
    operation_id: str
    correlation_id: str

@dataclass(frozen=True)
class VersionRef:
    schema_version: str
    ruleset_id: str | None = None
    ruleset_version: str | None = None

@dataclass(frozen=True)
class ErrorInfo:
    code: str
    retryable: bool
```

`operation_id` — устойчивый идентификатор конкретного бизнес-запроса. Для доменных команд Game Domain он передаётся как `command_id`; для отправки ответа — как `submission_id`. `correlation_id` связывает шаги одного пользовательского сценария, но сам по себе не обеспечивает идемпотентность.

## 3. Game Domain

### Commands

```python
CreateGame(command_id, players, ruleset_ref) -> GameCommandResult
StartGame(command_id, game_id) -> GameCommandResult
StartRound(command_id, game_id) -> GameCommandResult
RollDice(command_id, game_id, player_id) -> GameCommandResult
PlaceBid(command_id, game_id, player_id, bid) -> GameCommandResult
AdvanceTurn(command_id, game_id) -> GameCommandResult
DeclareDudo(command_id, game_id, player_id) -> DudoResolutionResult
AbandonGame(command_id, game_id, actor_id) -> GameCommandResult
```

`PlaceBid` не переключает ход. `AdvanceTurn` — отдельная команда. `DeclareDudo` атомарно разрешает ставку и применяет все последствия.

### Queries

```python
GetGameState(game_id) -> GameStateView       # только доверенный внутренний запрос
GetPlayerGameView(game_id, player_id) -> PlayerGameView
GetGameHistory(game_id, after_sequence=None) -> GameHistoryView
```

`GameStateView` не является интерфейсом Presentation. Для клиента используется только `PlayerGameView`.

### Внутренний порт для упражнений

```python
BuildExerciseSituation(config, random_source) -> ExerciseSituationCandidate
```

Этот порт не возвращает `GameState` наружу. Он может вернуть закрытый synthetic context только Exercise Engine в доверенном контексте; Exercise Engine обязан сформировать `PublicExercise` и `PrivateExerciseData` до выдачи результата клиенту.

## 4. S2 Perception

S2 не владеет игровым состоянием и не вызывает Game Domain для вычисления правил.

```python
BuildPerceptionTask(source_view, task_config) -> PerceptionTaskData
EvaluatePerceptionAnswer(task_data, typed_answer) -> SkillEvaluation
BuildTableView(source_view, layout_config) -> TableView
BuildTableTransition(previous_view, current_view, events) -> TableTransition
```

`source_view` — только `PlayerGameView` или `ExerciseGameView`. `PerceptionTaskData` не содержит эталон; эталон хранится в private criteria упражнения.

## 5. S3 Counting

```python
@dataclass(frozen=True)
class CountContext:
    own_player_id: str
    own_dice: tuple[int, ...]
    active_players: tuple[PlayerDice, ...]
    bid: Bid
    round_mode: RoundMode
    ruleset_id: str
    ruleset_version: str

CalculateCount(context: CountContext) -> CountResult
```

Инварианты входа:

- `own_player_id` присутствует ровно один раз в `active_players`;
- `own_dice` содержит ровно `dice_count` значений собственного игрока;
- значения кубиков принадлежат диапазону 1–6;
- `active_players` содержит только активных участников;
- `total_dice_count` вычисляется из `active_players`, а не принимается вторым независимым числом;
- `hidden_dice_count = total_dice_count - own_dice_count`.

S3 не принимает значения чужих кубиков.

## 6. S4 Probability

```python
@dataclass(frozen=True)
class ProbabilityRequest:
    count_result: CountResult
    event_kind: ProbabilityEventKind

CalculateProbability(request: ProbabilityRequest) -> ProbabilityResult
CompareProbabilities(events) -> ProbabilityComparisonResult
```

`ProbabilityRequest` не содержит исходные значения кубиков. S4 проверяет согласованность версии правил, режима и входного `CountResult`. `ProbabilityResult` содержит точную рациональную вероятность; форматирование процентов выполняет Presentation/Feedback adapter.

## 7. Exercise Engine

### Commands

```python
CreateExercise(operation_id, config) -> PublicExerciseRef
StartExerciseAttempt(operation_id, exercise_id, user_id) -> ExerciseAttemptRef
GetPublicExercise(exercise_id, exercise_version) -> PublicExercise
GetExerciseReviewView(attempt_id) -> ExerciseReviewView
```

`GetExerciseReviewView` разрешён только для завершённой попытки. Application сначала проверяет статус через Learning Progress; Exercise Engine повторно проверяет разрешённое условие выдачи. В серверном профиле авторизация выполняется на доверенной стороне.

### Generator port

```python
GenerateCandidate(config, random_source) -> ExerciseCandidate
ValidateCandidate(candidate) -> ValidationResult
PersistExercise(operation_id, candidate) -> ExerciseRef
```

Генератор навыка создаёт candidate и criteria. Exercise Engine управляет общим жизненным циклом, сохранением, версионированием и public/private split.

## 8. Evaluation

```python
@dataclass(frozen=True)
class EvaluationRequest:
    exercise_id: str
    exercise_version: str
    question_id: str
    submission_id: str
    typed_answer: SubmittedAnswer
    evaluator_version: str

EvaluateAnswer(request, private_criteria) -> EvaluationResult
```

`private_criteria` передаётся только из доверенного Exercise Engine в Evaluation; клиент его не передаёт. Evaluation не сохраняет ExerciseAttempt и не изменяет SkillProgress.

Результат включает field/step results, score, primary diagnostic, dependent diagnostics, `feedback_key`, `is_correct` и `is_terminal_for_question`. Полный expected answer в результат не включается.

## 9. Feedback

```python
BuildFeedback(evaluation_result, public_context, feedback_version) -> FeedbackView
BuildExerciseReview(exercise_review_view, attempt_summary) -> ExerciseReviewView
```

Feedback не меняет оценку, прогресс и состояние попытки. До завершения упражнения не возвращает полный эталон.

## 10. Learning Progress

### Commands

```python
CreateAttempt(operation_id, user_id, exercise_id, exercise_version) -> AttemptRef
RecordSubmission(submission_id, attempt_id, typed_answer, hint_events) -> SubmissionRef
AttachEvaluationResult(submission_id, evaluation_result) -> AttemptSnapshot
CompleteAttempt(operation_id, attempt_id, completion_context) -> AttemptSnapshot
AbandonAttempt(operation_id, attempt_id) -> AttemptSnapshot
ResetSkillProgress(operation_id, user_id, skill_id) -> SkillProgressSnapshot
```

`RecordSubmission` фиксирует пользовательский факт. `AttachEvaluationResult` идемпотентно прикрепляет результат проверки к уже сохранённой отправке. Реализация может объединить эти два шага в один Application use case, но не должна терять возможность восстановиться после сбоя.

### Queries

```python
GetAttempt(attempt_id) -> AttemptSnapshot
GetSkillProgress(user_id, skill_id) -> SkillProgressSnapshot
GetLearningContext(user_id, skill_ids=None) -> LearningContext
GetReviewTasks(user_id, filters) -> ReviewTaskList
```

Learning Progress — единственный владелец `ExerciseAttempt`, `AnswerSubmission` как факта отправки, `SkillProgress`, `ErrorStatistics` и `ReviewTask`.

## 11. Learning Strategy

```python
SelectNextExercise(learning_context, selection_constraints) -> ExercisePlan
RecommendReviewTasks(learning_context) -> ReviewRecommendationList
```

Learning Strategy — чистая политика выбора либо компонент с версионированной конфигурацией; он не изменяет попытки и прогресс.

## 12. Application use cases

Application координирует последовательность, но не владеет предметными инвариантами модулей.

Минимальные сценарии:

- `StartLearningExercise` — получить план, создать/сохранить упражнение, создать попытку, вернуть public view;
- `SubmitExerciseAnswer` — сохранить отправку, получить критерии, выполнить Evaluation, сохранить результат, обновить Progress, построить Feedback;
- `CompleteExerciseAttempt` — проверить completion criteria, завершить попытку, получить разрешение на review view;
- `PlaceBidAndAdvanceTurn` — выполнить PlaceBid, затем надёжно завершить AdvanceTurn;
- `ResolveDudo` — вызвать одну атомарную доменную команду DeclareDudo;
- `RestoreGame` — восстановить Game Domain из snapshot и event log.

## 13. Общие ошибки

Минимальный набор кодов на границах:

- `VALIDATION_ERROR` — нарушен контракт входа;
- `NOT_FOUND` — сущность отсутствует;
- `VERSION_UNAVAILABLE` — требуемая версия недоступна;
- `IDEMPOTENCY_CONFLICT` — один operation ID использован с разным payload;
- `INVALID_STATE_TRANSITION` — команда недопустима в текущем состоянии;
- `GENERATION_FAILED` — кандидат не прошёл генерацию/валидацию;
- `EVALUATION_UNAVAILABLE` — нельзя выполнить проверку по требуемой версии;
- `PERSISTENCE_FAILED` — результат сохранения не подтверждён;
- `RECOVERY_FAILED` — безопасное восстановление невозможно.

Внешнее сообщение не содержит private payload, expected answer или закрытые значения кубиков.

## 14. Не входит в этот каталог

Не определяются конкретные Python-модули, пути импортов, протоколы сериализации, БД, ORM, транспорт, async/sync API и точные типы идентификаторов. Это будет решаться после принятия владельцев данных и спорных транзакционных сценариев.
