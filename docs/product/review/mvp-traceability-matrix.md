# MVP traceability matrix

**Статус:** нормативная карта прослеживаемости для технического проектирования; не является доказательством реализации или прохождения release gates.
**Baseline:** `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`; web desktop/mobile, local-first, один локальный профиль, S1–S9.

## 1. Как читать матрицу

- `Requirement` — обязательство продукта или домена, а не конкретный способ реализации.
- `Owner` — единственный логический владелец поведения/данных. Другие модули могут читать опубликованный контракт, но не дублируют решение.
- `Evidence` — артефакт, который должен существовать для приёмки. `PLANNED` означает, что проверка ещё не выполнена.
- `Gate` — ссылка на handoff/release gate из `implementation-handoff-readiness.md` и `mvp-release-validation-plan.md`.
- `TBD` не означает свободу реализации: решение должно быть принято до соответствующего design freeze.

## 2. Product and platform requirements

| ID | Требование / инвариант | Владелец | Потребители | Evidence / acceptance | Gate |
|---|---|---|---|---|---|
| PR-01 | Web desktop и mobile; PWA только дополнительная установка | Presentation / Application | UX, QA | Responsive сценарии + browser compatibility run | H-06 |
| PR-02 | Первый запуск без обязательной регистрации, backend и cloud sync | Application | Presentation, Persistence | Первый запуск в чистом browser profile без сети после загрузки приложения | H-07 |
| PR-03 | Один локальный профиль на browser profile | Profile / Persistence | Learning Progress, Game History | Создание, повторное открытие, изоляция другого browser profile | H-08 |
| PR-04 | Попытки и прогресс переживают перезапуск | Persistence | Exercise, Evaluation, Progress | Close/reopen recovery test; persisted state consistency | H-08 |
| PR-05 | Offline после первого успешного открытия | Application / Presentation | Persistence | Offline smoke suite после cache warm-up | H-06, H-08 |
| PR-06 | Ручной backup export/import до публичного релиза | Backup / Persistence | Application, QA | Round-trip, invalid file, older schema, interrupted import tests | H-08 |
| PR-07 | Локальная проверка `TRUSTED_LOCAL`; пользователь предупреждён, что эталоны доступны владельцу устройства | Evaluation / Presentation | Privacy, Product | Warning acceptance + inspection of public/private boundary | H-07 |
| PR-08 | Без аккаунтов, multiplayer, social, cloud sync и ML в MVP | Product Scope | All | Deferred-scope checks; no UI/API dependency | H-09 |
| PR-09 | Прозрачная rule-based стратегия обучения | Learning Strategy | Learning Progress, Exercise Engine | Deterministic policy fixtures with explanation codes | H-05 |
| PR-10 | Каждая отправка ответа сохраняется отдельно; first/final/independent accuracy, hints и corrections различимы | Exercise / Evaluation / Progress | Feedback, Strategy | Multi-submission lifecycle fixtures and persisted event assertions | H-04, H-05, H-08 |

## 3. Game Domain and ruleset requirements

| ID | Требование / инвариант | Владелец | Потребители | Evidence / acceptance | Gate |
|---|---|---|---|---|---|
| GD-01 | Единственный MVP ruleset `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`; Calza выключено | Game Domain | S1, S3, S4, S5, S8, S9, Exercise Generator | Ruleset decision record + ruleset transition vectors | H-01 |
| GD-02 | NORMAL counting: faces 2–6 include Paco; face 1 counts Paco only | Game Domain / Counting | S1, S3, S4, Evaluation | NORMAL fixtures incl. all six faces | H-01, H-03 |
| GD-03 | PALIFICO counting: exact face only; Paco not wild | Game Domain / Counting | S1, S3, S4, S5 | PALIFICO fixtures for faces 1–6 | H-01, H-03 |
| GD-04 | NORMAL bid transitions and Paco conversion rules follow normative contract | Game Domain | S1, S5, Exercise Generator | Transition vectors incl. boundaries and upper bound | H-01 |
| GD-05 | PALIFICO trigger, duration, two-player checkpoint and persistent declaration flag follow explicit project normalization | Game Domain | S1, S5, S9, Exercise Generator | Trigger matrix + two-player/elimination edge tests | H-01 |
| GD-06 | Bid quantity cannot exceed active dice; rejected command has no mutation/event | Game Domain | S1, S5, Application | Boundary tests; state/event unchanged on rejection | H-01, H-08 |
| GD-07 | Dudo, die loss, elimination and round outcome are atomic | Game Domain | Game History, S1, S2, S9 | Atomic transition tests and crash consistency | H-08 |
| GD-08 | `BID_PLACED` records bid; `TURN_ADVANCED` advances once | Game Domain / Event Store | S2, S6, S8 | Event-sequence tests incl. duplicate/out-of-order events | H-08 |
| GD-09 | Eliminated players remain in historical/presentation state but not active turn order or calculations | Game Domain | S2, S3, S6, S9 | Active/inactive participant fixtures | H-01, H-03 |
| GD-10 | Every state-changing command has idempotency key; same key+payload replays result, different payload conflicts | Application / Command Handler | Game Domain, Persistence | Retry and key-conflict tests | H-08 |
| GD-11 | Ruleset, event schema, exercise generator and evaluator versions are independent identifiers | Version Registry / Persistence | All versioned modules | Compatibility and migration matrix | H-08 |

## 4. Skill requirements

| ID | Skill | Normative responsibility | Inputs owned elsewhere | Acceptance evidence | Gate |
|---|---|---|---|---|---|
| SK-01 | S1 Rules and Mechanics | Explain and validate ruleset facts, phases, legal transitions and exceptions | Game Domain state/ruleset | S1 fixtures, ruleset transition vectors, accepted-answer review | H-01, H-02 |
| SK-02 | S2 Table Perception | Read visible table state and temporal changes; classify FACT / ASSUMPTION / CONTRADICTED | Public player/exercise view | Hidden-value non-disclosure, identity stability, event ordering fixtures | H-07 |
| SK-03 | S3 Counting | Count own matches and required matches by mode; exclude eliminated dice | Public state + own hand + bid | `required_matches=max(0,bid_quantity-own_matches)`; NORMAL/PAL fixtures | H-03 |
| SK-04 | S4 Probability | Compute exact probability from hidden dice count, required matches and mode-specific p | S3 CountResult | Exact Fraction/binomial vectors; p=1/3 or 1/6 by mode/face | H-03 |
| SK-05 | S5 Bid Mechanics | Validate candidate bid legality and explain transition | Game Domain ruleset and current bid | S5 transition fixtures and boundary tests | H-01, H-02 |
| SK-06 | S6 Bid History | Reconstruct and interpret bid sequence from ordered events | Public event projection | Complete/incomplete sequence and ordering fixtures | H-02 |
| SK-07 | S7 Opponent Analysis | Separate observations, hypotheses and behavioral patterns; never present inference as fact | Consented, provenance-tagged observations | Evidence sufficiency, provenance and uncertainty review | H-02, H-07 |
| SK-08 | S8 Decision Making | Evaluate decision using only information available at decision time | Legal actions, decision-time view, objective | No hindsight leakage; legal action and objective fixtures | H-02 |
| SK-09 | S9 Match Strategy | Evaluate conditional longer-term plans against current match context | Game state, history, strategy assumptions | Branch/trigger fixtures incl. Palifico and elimination | H-02 |

## 5. Learning lifecycle requirements

| ID | Requirement | Owner | Required contract / evidence | Gate |
|---|---|---|---|---|
| LR-01 | Exercise instance is persisted before it is shown and remains immutable | Exercise Engine / Persistence | Exercise identity, generator provenance, immutable instance test | H-08 |
| LR-02 | Public exercise view never contains private expected answers | Exercise Engine | Public/private DTO inspection and serialization tests | H-07 |
| LR-03 | Multi-step attempt supports STARTED, IN_PROGRESS, COMPLETED, ABANDONED | Exercise / Attempt | State transition tests incl. reload/resume | H-08 |
| LR-04 | Every answer submission is append-only; corrections do not overwrite prior submissions | Evaluation / Attempt | Submission history and retry tests | H-04, H-08 |
| LR-05 | Expected answer is not revealed before exercise completion | Feedback / Presentation | Multi-step reveal policy tests | H-04 |
| LR-06 | Optional steps do not change required score | Evaluation | Rubric fixtures | H-04 |
| LR-07 | Hints are recorded separately and do not automatically reduce score | Attempt / Progress | Hint-use fixtures and metric assertions | H-05 |
| LR-08 | Feedback is separate from scoring; first show primary error, details on demand | Feedback | Feedback payload/UX acceptance | H-04, H-06 |
| LR-09 | Progress stores evidence; Strategy chooses next exercise | Learning Progress / Learning Strategy | Ownership test + deterministic recommendation fixtures | H-05 |
| LR-10 | Mastery/review status uses approved policy; no unapproved sample-size shortcut | Learning Progress | Mastery policy sign-off and review schedule fixtures | H-05 |

## 6. Architecture ownership map

| Capability | Single owner | Must not own | Public contract / key artifact |
|---|---|---|---|
| Rules and match state | Game Domain | UI state, storage mechanics, exercise scoring | `GameState`, commands, domain events |
| Skill reasoning | Skills S1–S9 | Persistence, UI orchestration, profile progress | Skill-specific input/output contracts |
| Exercise generation | Exercise Engine | Evaluation decisions, mastery state | `Exercise`, `PublicExercise`, `PrivateExerciseData` |
| Answer scoring | Evaluation | Feedback wording, progress scheduling | `AnswerSubmission`, `EvaluationResult` |
| Feedback presentation content | Feedback | Recomputing score, mutating progress | `FeedbackResult` |
| Historical learning evidence | Learning Progress | Selecting next exercise | `SkillProgress`, attempt metrics |
| Next-step selection | Learning Strategy | Rewriting evidence or score | `LearningRecommendation` |
| UI rendering and interaction | Presentation | Domain rules and scoring | View models / presentation commands |
| Use-case orchestration | Application | Reimplementing domain invariants | Application commands and result envelopes |
| Durable storage | Persistence | Owning domain policy | Versioned records, transactions, migrations |

## 7. Required evidence chain

A requirement is not `PASS` merely because a document exists. Use the following evidence states consistently:

- `SPECIFIED` — normative behavior and owner are documented.
- `FIXTURED` — at least one deterministic acceptance fixture exists.
- `REVIEWED` — product/domain reviewer approved the fixture and accepted alternatives.
- `IMPLEMENTED` — code exists; this status requires a commit/build reference.
- `EXECUTED` — automated or scripted test ran; include run identifier/result.
- `RELEASE_ACCEPTED` — relevant release gate signed off.

Allowed progression is monotonic unless a ruleset, contract or fixture version changes. A changed normative contract invalidates downstream review/execution evidence for affected rows until revalidated.

## 8. Current blockers and shortest closure path

1. Reconcile the S1–S9 and Learning Core contract inventory against the current repository checkout; every skill must have one authoritative contract path and version.
2. Approve individual MVP fixtures and accepted alternatives; `READY_FOR_REVIEW` is not `APPROVED`.
3. Close evaluator rubric, mastery/review thresholds and evidence consent policy.
4. Complete the data contracts and end-to-end exercise lifecycle mapping.
5. Run implementation, privacy, recovery, accessibility and UX gates on the actual product.

This matrix is the traceability baseline for technical design. It does not authorize a technology choice or claim that any implementation gate has passed.
