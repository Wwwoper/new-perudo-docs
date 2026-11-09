# Data contracts and trust boundaries

**Статус:** нормативные logical contracts для технического проектирования MVP. Физическая схема, сериализация, БД и transport намеренно не выбраны.
**Baseline:** `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`; local-first; evaluation mode `TRUSTED_LOCAL`.

## 1. Contract rules

1. Logical DTO names and semantics are stable independently of JSON, database tables, Python classes or transport.
2. Every persisted record has a stable identifier, schema version and creation/update provenance where applicable.
3. Ruleset version, event schema version, exercise generator version and evaluator version are separate fields. Never overload one version for another.
4. Public DTOs contain only information the learner is allowed to see. Private expected answers and hidden dice values are never embedded and then hidden by presentation code.
5. A module may persist only records it owns. Cross-module writes go through the owning module's command/use-case boundary.
6. Immutable historical evidence is append-only. Corrections append a new submission; they do not rewrite earlier submissions.
7. All timestamps are UTC instants at the logical contract level; UI localizes them. Ordering-critical streams also carry a monotonically increasing sequence within their aggregate.
8. Unknown required enum values or unsupported schema versions fail closed. They must not be silently coerced to a default.

## 2. Data classification

| Class | Examples | Permitted exposure | Persistence rule |
|---|---|---|---|
| PUBLIC_EXERCISE | Prompt, visible dice, visible bids, participant labels, public rules context | Presentation and learner | May be exported as exercise content if no private fields are joined |
| PRIVATE_ANSWER | Expected answer, accepted alternatives, scoring rubric, hidden generator state | Exercise Engine, Evaluation and Feedback only after reveal policy permits | Separate private record; never serialized into public view |
| PLAYER_PRIVATE | Learner's own hand, local profile identifiers, personal notes | Learner's local session and explicitly authorized skill logic | Local-only; do not include in public opponent projection |
| OPPONENT_HIDDEN | Other players' concealed dice values | Domain simulation/generator only when needed; never learner-facing | Must not cross into PublicExercise/PlayerGameView |
| LEARNING_EVIDENCE | Submissions, hints, feedback viewed, skill metrics, recommendation rationale | Local learner profile and learning modules | Append-only evidence; user can reset/export under product policy |
| USER_HISTORY | Imported real-game records and behavioral observations | Only after explicit user action/consent and provenance labeling | Separate from generated exercise fixtures; never silently used for opponent profiling |
| OPERATIONAL | command_id, event_id, schema versions, migration metadata | Application/Persistence diagnostics | No secret material; stable enough for recovery and deduplication |

## 3. Core aggregate and record contracts

### 3.1 GameState

Owned by Game Domain. Represents complete authoritative state for one match.

Required logical fields:

- `game_id`, `ruleset_id`, `ruleset_version`;
- `game_status` and `round_id`;
- ordered participants with stable `player_id`, dice count, active/eliminated status and persistent `has_declared_palifico`;
- `round_mode` (`NORMAL` or `PALIFICO`), starter and current turn owner;
- current bid and bid sequence reference;
- aggregate revision / event sequence.

Rules:
- Complete state may contain concealed dice values only in a trusted simulation or real-game domain context where those values are legitimately known to the game engine. It is never a presentation DTO.
- Active turn order and active-dice totals are derived from active participants; eliminated participants remain in history.
- Mutations occur only through domain commands. Presentation must not patch state directly.

### 3.2 PlayerGameView

Owned by Presentation projection from Game Domain. Contains the information visible to one player in a real game.

Required logical fields:
- `game_id`, `viewer_player_id`, `round_id`, `round_mode`;
- participant identity/order, visible dice counts, active status;
- viewer's own dice values only;
- public bid/history projection and current turn;
- view revision / source event sequence.

Forbidden:
- other players' concealed dice values;
- generator seed, expected answer or private exercise annotations;
- internal evaluator rubric or hidden opponent profile.

### 3.3 Exercise and its views

Owned by Exercise Engine.

`Exercise` is the immutable persisted instance envelope:
- `exercise_id`, `exercise_type_id`, `exercise_type_version`;
- `skill_id`, `ruleset_id`, `ruleset_version`;
- `generator_id`, `generator_version`, `generator_config_version`;
- `seed` or equivalent reproducibility provenance, where generation is seeded;
- `created_at`, content revision and lifecycle metadata.

`PublicExercise` contains only prompt, visible context, allowed learner actions, ordered questions/steps and public feedback timing rules. It must be safe to serialize to Presentation before completion.

`PrivateExerciseData` contains expected answers, accepted alternatives, private validation facts, scoring criteria and any hidden scenario state. It is stored separately from `PublicExercise` and only passed to authorized Evaluation/Feedback use cases.

`ExerciseGameView` is a constrained game-like projection embedded in an exercise. It is not a `GameState` and cannot issue real game commands. It contains only the public exercise context and explicitly permitted learner-private data (e.g. own dice).

The three records share `exercise_id` but must not be merged into one public serialization object.

### 3.4 ExerciseAttempt

Owned by Exercise / Attempt lifecycle.

Required fields:
- `attempt_id`, `exercise_id`, `profile_id`;
- `status`: `STARTED`, `IN_PROGRESS`, `COMPLETED`, `ABANDONED`;
- `started_at`, `completed_at?`, `last_activity_at`;
- ordered submission references and completed required-step identifiers;
- `attempt_revision`.

Rules:
- One generated exercise instance may have multiple attempts only when product flow explicitly starts a fresh attempt; retrying a question within an attempt creates a new AnswerSubmission.
- Attempt completion is terminal. Reopening an abandoned attempt requires an explicit product policy; do not silently convert it to in-progress.
- Attempt state and its appended submission reference update atomically.

### 3.5 AnswerSubmission

Owned by Evaluation / Attempt lifecycle; immutable append-only.

Required fields:
- `submission_id`, `attempt_id`, `question_id`, `step_id?`;
- `answer_payload` in a question-type-specific typed shape;
- `submitted_at`, `submission_sequence`;
- `hint_ids_used_before_submission` or explicit hint event references;
- `correction_of_submission_id?` only when the product models an explicit correction relation.

Rules:
- A correction is a new submission; original answer remains queryable.
- Raw answer payload is preserved as submitted. Normalized interpretation belongs in EvaluationResult.
- Repeated identical submissions remain distinct user actions unless a command idempotency key identifies a transport retry of the same action.

### 3.6 EvaluationResult

Owned by Evaluation. Immutable per submission and evaluator version.

Required fields:
- `evaluation_id`, `submission_id`, `evaluator_id`, `evaluator_version`;
- `outcome` (`CORRECT`, `PARTIALLY_CORRECT`, `INCORRECT`, `UNSCORABLE`);
- criterion-level results, normalized answer and error codes;
- required score contribution and optional-step marker;
- `evaluated_at`.

Rules:
- Evaluation scores the answer against the private expected data and rubric version pinned to the exercise.
- Optional steps do not affect required final score.
- Hints do not automatically subtract score; hint usage remains separate evidence.
- Evaluation does not author explanatory feedback or mutate SkillProgress.

### 3.7 FeedbackResult

Owned by Feedback. May be generated from EvaluationResult and the learner's prior interaction context.

Required fields:
- `feedback_id`, `evaluation_id`, `feedback_policy_version`;
- primary error/message code, concise explanation and optional detail sections;
- `answer_reveal_allowed` and revealed expected-answer payload only when lifecycle policy permits;
- `created_at`, `viewed_at?` as separate presentation evidence.

Rules:
- Feedback is not the source of truth for correctness; EvaluationResult is.
- Before exercise completion, feedback must not reveal private expected answers.
- Default feedback contains one primary correction; expanded detail is opt-in.

### 3.8 SkillProgress and LearningRecommendation

`SkillProgress` is owned by Learning Progress and stores derived state plus links to immutable evidence:
- `profile_id`, `skill_id`, `status` (`NOT_STARTED`, `LEARNING`, `PRACTICING`, `MASTERED`, `NEEDS_REVIEW`);
- first/final/independent accuracy and supporting sample references;
- attempts, submissions, corrections, hints and review evidence;
- `progress_policy_version`, `updated_at`.

`LearningRecommendation` is owned by Learning Strategy:
- `recommendation_id`, `profile_id`, selected `skill_id` and exercise type/config;
- reason codes, referenced progress evidence, policy version and creation time.

Progress stores evidence and derived mastery facts; Strategy chooses the next activity. Strategy cannot rewrite submissions or scores. A recommendation must be explainable from recorded inputs and policy version.

## 4. Command and event envelopes

### 4.1 CommandEnvelope

Every state-changing application command carries:

- `command_id` (globally unique within the profile/application scope);
- `command_type`, `command_schema_version`;
- `aggregate_id`, `expected_revision` where optimistic concurrency is required;
- `issued_at`, `payload`.

Idempotency behavior:
- same `command_id` + same canonical payload: return the original result without repeating effects;
- same `command_id` + different payload: return `IDEMPOTENCY_KEY_CONFLICT`;
- stale `expected_revision`: return `CONCURRENCY_CONFLICT` and do not mutate;
- validation failure: return typed error and do not append domain events.

### 4.2 DomainEventEnvelope

Every event carries:
- `event_id`, `event_type`, `event_schema_version`;
- `aggregate_id`, `aggregate_sequence`;
- `occurred_at`, `command_id`;
- `payload`.

Events are immutable. The event stream is ordered by `aggregate_sequence`, not wall-clock timestamp. Unknown event type/schema or invalid payload stops replay for that aggregate and surfaces a recoverable data error; it must not be skipped.

### 4.3 Event storage and snapshots

- Persist event(s) and resulting aggregate revision atomically.
- Snapshot after each completed round and match completion; snapshot is an optimization, not the source of truth.
- A corrupt newest snapshot may fall back to the previous valid snapshot and replay subsequent events.
- If event history is corrupt or unsupported, stop; never silently reconstruct a plausible state.
- Migration functions are explicit and versioned. No automatic reinterpretation of old ruleset events under a new ruleset.

## 5. Ownership and transaction boundaries

| Operation | Owning boundary | Atomic unit |
|---|---|---|
| Place bid / resolve Dudo | Game Domain via Application | command result + emitted events + new GameState revision |
| Generate exercise | Exercise Engine via Application | immutable Exercise + PublicExercise + PrivateExerciseData persisted before presentation |
| Start/resume attempt | Attempt lifecycle | attempt state transition + initial/resume metadata |
| Submit answer | Attempt/Evaluation boundary | append AnswerSubmission + update attempt sequence/status |
| Evaluate answer | Evaluation | EvaluationResult persisted against pinned evaluator/rubric version |
| Create feedback | Feedback | FeedbackResult persisted; no score/progress mutation |
| Update progress | Learning Progress | evidence references + derived SkillProgress revision |
| Select next exercise | Learning Strategy | immutable LearningRecommendation; no mutation of source evidence |
| Import backup | Backup/Persistence | validate entire package before atomic replace/merge decision; no partial import |

Cross-module operations use an application-level workflow. They do not create shared mutable records owned by multiple modules.

## 6. Error contract

Use stable machine-readable error codes; human-readable text is presentation-owned. Minimum codes:

| Code | Meaning | State mutation |
|---|---|---|
| `VALIDATION_ERROR` | Malformed or semantically invalid payload | None |
| `ILLEGAL_GAME_TRANSITION` | Bid/turn/round transition violates ruleset | None |
| `ACTIVE_DICE_LIMIT_EXCEEDED` | Bid quantity exceeds active dice | None |
| `IDEMPOTENCY_KEY_CONFLICT` | Same command ID used with different payload | None |
| `CONCURRENCY_CONFLICT` | Aggregate revision is stale | None |
| `NOT_FOUND` | Referenced aggregate/attempt/exercise missing | None |
| `LIFECYCLE_CONFLICT` | Operation invalid for current attempt/exercise state | None |
| `PRIVATE_DATA_ACCESS_DENIED` | Caller is not authorized for private answer data | None |
| `UNSUPPORTED_SCHEMA_VERSION` | Required schema version cannot be read | None; fail closed |
| `CORRUPT_EVENT_STREAM` | Event sequence/payload cannot be replayed safely | No further replay/write for aggregate |
| `BACKUP_VALIDATION_FAILED` | Backup package fails validation | No partial import |

## 7. Version compatibility policy

- `ruleset_version` pins game semantics for a game and generated exercise.
- `event_schema_version` pins the payload shape of each event type.
- `exercise_type_version` and `generator_version` pin exercise definition and generation behavior.
- `evaluator_version` and rubric version pin scoring interpretation.
- `progress_policy_version` and `strategy_policy_version` pin derived progress and recommendations.
- A new version never silently rewrites historical submissions, evaluation results or game events. Re-evaluation/recalculation, if ever needed, creates new versioned evidence linked to the original.
- Backup import must either support the embedded versions, run an explicit migration, or fail with a clear unsupported-version error.

## 8. Explicitly deferred technical choices

This contract does not select:
- programming language or framework;
- physical package/module layout;
- IndexedDB or another concrete persistence technology;
- JSON/CBOR/other serialization;
- event sourcing as a universal implementation pattern beyond the specified event/history semantics;
- encryption/key management strategy;
- hosting, analytics or telemetry provider.

Those choices must preserve the logical boundaries, atomicity, privacy classifications and version behavior above. Any proposed deviation requires an ADR and impact review against the traceability matrix.

## 9. Acceptance checklist for detailed design

- [ ] Every record has one owner and a stable identity.
- [ ] Public/private exercise data are physically separated at the serialization boundary.
- [ ] PlayerGameView never contains opponent concealed values.
- [ ] Command idempotency and aggregate concurrency behavior are defined.
- [ ] Event sequence and unsupported-version behavior are defined.
- [ ] Submission history is append-only and attempt transitions are explicit.
- [ ] Evaluation, Feedback, Progress and Strategy have separate write ownership.
- [ ] Backup import is all-or-nothing and version-aware.
- [ ] Each contract maps to at least one fixture/test in `mvp-traceability-matrix.md`.
