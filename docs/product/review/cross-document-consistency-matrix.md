# Матрица сквозной согласованности S1–S9

**Статус:** контрольная матрица для независимой ревизии; статусы не означают, что проверки реализации выполнены.

## 1. Назначение и статусная модель

Матрица связывает навыки, контракты, эталонные примеры, evaluation, прогресс и UX. Она предназначена для выявления отсутствующих связей и конфликтов до handoff.

Статусы:

- `DOCUMENTED` — соответствующее требование описано;
- `PARTIAL` — есть описание, но не закрыты все случаи или доказательства;
- `BLOCKED` — нельзя утвердить без внешнего решения или недостающего источника;
- `NOT_VERIFIED` — документация задаёт проверку, но её выполнение не подтверждено;
- `N/A` — неприменимо к согласованному scope с записанным обоснованием.

Наличие строки в этой матрице не заменяет запись результата в example review register.

## 2. Сквозная матрица навыков

| Skill | Source of truth / boundary | MVP evidence family | Evaluation focus | Progress attribution | UX acceptance | Current audit status |
|---|---|---|---|---|---|---|
| S1 | Game Domain; правила и последствия | `s1-s5-mvp-examples.md` | Правильное применение утверждённого правила и последствие | Primary S1; blocked rule cases не дают mastery evidence | Ясный режим, событие и вопрос о последствии | BLOCKED для неутверждённых переходов |
| S2 | S2 Perception contract; public view | `s1-s5-mvp-examples.md` | Видимые факты и недоступная информация | Primary S2; не засчитывать скрытые значения | Различать видимое состояние и скрытые данные | PARTIAL до проверки learner payload |
| S3 | S3 Counting contract | `s1-s5-mvp-examples.md` | Own matches, hidden dice, required matches | Primary S3; отдельно normal/Palifico | Подписывать числа и режим подсчёта | DOCUMENTED; fixtures требуют проверки |
| S4 | S4 Probability contract | `s1-s5-mvp-examples.md` | Exact/at-least, точная вероятность, edge cases | Primary S4; only approved ruleset fixtures | Процент сопровождается определением события | DOCUMENTED; независимый пересчёт не подтверждён |
| S5 | Game Domain + S5 contract | `s1-s5-mvp-examples.md` | Legal/illegal и конкретная строка перехода | Primary S5; no evidence from blocked transitions | Показывать причину незаконности и применимое правило | BLOCKED для отсутствующих transition rows |
| S6 | Event/history contracts | `s6-s7-mvp-examples.md` | Порядок событий, неполнота истории, отсутствие выдуманных ставок | Primary S6 | Видно, какие события доступны и какой фрагмент неполон | PARTIAL; нужен прогон неполных последовательностей |
| S7 | S7 contract + observation rubric | `s6-s7-mvp-examples.md` | FACT/ASSUMPTION/CONTRADICTED, comparability, evidence strength | Primary S7; qualitative confidence only | Нейтральный язык и видимая ограниченность данных | PARTIAL; критерии требуют owner approval |
| S8 | S8 contract + objective per scenario | `s8-s9-mvp-examples.md` | Legality, decision-time evidence, stated objective, reasoning | Primary S8; no outcome-based scoring | Цель и набор доступных действий до ответа | PARTIAL; допустимые цели требуют approval |
| S9 | S9 contract + planning rubric | `s8-s9-mvp-examples.md` | Goal, assumptions, conditional branch, revision trigger | Primary S9; no guaranteed outcome | План и условия пересмотра читаемы отдельно | PARTIAL; обязательность рубрики требует approval |

## 3. Общие сквозные связи

| Link ID | From → To | Invariant | Evidence required | Current status |
|---|---|---|---|---|
| X-01 | Game Domain → S1/S5 | Scored rule example references an approved ruleset rule and transition row | Rule ID + approved example + boundary tests | BLOCKED where transition table is absent |
| X-02 | S2 → S3 | Only public state and learner's own dice are exposed | Payload inspection and hidden-data negative test | NOT_VERIFIED |
| X-03 | S3 → S4 | `own_matches`, `hidden_dice`, `required_matches`, mode and probability parameter agree | Cross-module fixture with exact expected values | NOT_VERIFIED |
| X-04 | S4 → S8 | Probability is an input to decision evaluation, not the decision itself | Exercise objective, action set and independent evaluator criteria | PARTIAL |
| X-05 | S5 → S6 | Bid legality and event-history reconstruction remain separate | Valid/invalid bid fixtures plus ordered event stream | NOT_VERIFIED |
| X-06 | S6 → S7 | Behavioural claims use only available, ordered observations | Incomplete and contradictory history fixtures | NOT_VERIFIED |
| X-07 | S7 → S8 | Opponent evidence may inform a decision only as evidence with explicit strength | Decision fixture with and without opponent evidence | PARTIAL |
| X-08 | S8 → S9 | Single decision evidence may inform but cannot replace match-level planning | Multi-round scenario with changed state and revision trigger | NOT_VERIFIED |
| X-09 | Exercise → Evaluation → Feedback | Expected answer remains hidden until completion; feedback does not mutate evaluation evidence | Multi-step attempt and answer-disclosure test | NOT_VERIFIED |
| X-10 | Evaluation → Progress | First, final, assisted, and independent results remain distinct | Submission event ledger and mastery eligibility tests | NOT_VERIFIED |
| X-11 | Progress → Strategy | Progress facts are inputs; strategy selects recommendation without rewriting evidence | Recommendation fixtures and no-eligible state | NOT_VERIFIED |
| X-12 | UX → all skills | One primary action, clear task, explainable result, next step | Desktop/mobile scenario walkthroughs and accessibility checks | NOT_VERIFIED |

## 4. Инварианты для каждого scored example

Перед `APPROVED` у каждого примера должны быть связаны:

1. уникальный `example_id` и family/skill;
2. публичное условие и learner-visible data;
3. private expected answer и версия evaluator;
4. ruleset version и ссылки на все затронутые rule IDs;
5. primary skill и явно обозначенные secondary skills;
6. обязательные/optional criteria и accepted alternatives;
7. проверочные ответы и граничные/негативные cases;
8. feedback policy и момент раскрытия эталона;
9. progress eligibility (может ли пример давать independent mastery evidence);
10. UX scenario и критерий завершения;
11. reviewer, дата и результат проверки.

Если любое правило, которое влияет на эталон, имеет статус OPEN/DEFERRED, пример получает `BLOCKED_BY_RULE_DECISION`, а не `APPROVED`.

## 5. Системные риски согласованности

| ID | Риск | Severity | Обязательное действие |
|---|---|---|---|
| CONS-01 | Ruleset и переходы ставок не имеют полного утверждённого набора строк | BLOCKER | Не принимать зависимые S1/S5/S8/S9 examples |
| CONS-02 | Пороги из HTML-прототипа могут быть приняты за универсальную стратегию | HIGH | Оставить их параметрами отдельных упражнений |
| CONS-03 | Полевая точность/счёт прототипа не равны независимому mastery evidence | BLOCKER | Использовать submission ledger и mastery policy |
| CONS-04 | Скрытые значения могут быть замаскированы только в UI, но передаваться в payload | BLOCKER | Проверить DTO/payload на отсутствие скрытых значений |
| CONS-05 | Числовые эталоны S4 повторены в checklist без независимого пересчёта | HIGH | Зафиксировать независимый пересчёт и точные дроби |
| CONS-06 | Смешанные навыки могут получить двойной зачёт одного ответа | HIGH | Один primary skill; secondary evidence только по независимому критерию |
| CONS-07 | Документированные UX-состояния могут быть ошибочно приняты за поведенческую доступность | HIGH | Проверить клавиатуру, фокус, мобильную компоновку и ошибки в реализации |

## 6. Критерии закрытия матрицы

Матрица может быть переведена в финальный audit result только когда:

- все BLOCKER либо закрыты подтверждённым решением и evidence, либо исключены из явно ограниченного MVP scope;
- все `NOT_VERIFIED` имеют фактический результат проверки или остаются открытыми с владельцем и сроком;
- каждый scored example имеет связанный review record;
- каждый skill имеет хотя бы один approved MVP example либо явно записан как исключённый из release scope;
- ни один открытый decision не маскируется предположением.

На момент создания документа проверки реализации не выполнялись; `NOT_VERIFIED` остаётся честным исходным статусом.
