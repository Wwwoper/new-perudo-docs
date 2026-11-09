# End-to-end learning loop contract

**Status:** normative logical workflow contract for MVP technical design. Product policies marked `PROPOSED`, `OPEN` or `REQUIRES_APPROVAL` remain non-normative until approved in their owning decision records.
**Depends on:** `data-contracts.md`, `docs/contracts/learning-core.md`, S1–S9 skill contracts, the approved ruleset version, exercise catalogue and learning policy documents.

## 1. Purpose and invariants

This contract connects Exercise Engine, Attempt Lifecycle, Evaluation, Feedback, Learning Progress and Learning Strategy. It defines the order of operations and the evidence passed between owners; it does not choose physical classes, storage technology, transport or UI framework.

Normative invariants:

1. An exercise instance is generated and persisted, including its private data, before any public prompt is returned to Presentation.
2. `PublicExercise` is safe to disclose before completion. `PrivateExerciseData` is never included in a public response, cache entry, export view or presentation model.
3. Every learner submission is immutable evidence. A correction is another submission, never an update to the previous answer.
4. Evaluation is deterministic for the same submission, private exercise data, rubric and evaluator versions.
5. Feedback communicates an evaluation; it does not determine correctness or mutate progress.
6. Progress consumes persisted evidence and policy versions. Strategy recommends an activity but cannot edit evidence or progress facts.
7. The expected answer is revealed only after the exercise's required completion condition has been met. A per-question check may reveal correctness and the primary error, but not the complete answer key or future-step answers.
8. Optional steps are recorded but do not contribute to required completion or required score unless the exercise definition explicitly marks them required.
9. A failure between workflow steps must be safely retryable. Retrying a command does not create duplicate submissions, evaluations, feedback or progress evidence.
10. A policy that has not been approved cannot be silently treated as a product rule. Affected activities are either excluded from scored MVP use or evaluated only under an explicitly approved fallback.

## 2. Logical workflow

```mermaid
sequenceDiagram
    participant A as Application
    participant X as Exercise Engine
    participant T as Attempt Lifecycle
    participant E as Evaluation
    participant F as Feedback
    participant P as Learning Progress
    participant S as Learning Strategy
    A->>X: GenerateExercise(config, provenance)
    X-->>A: Exercise + PublicExercise + PrivateExerciseData refs
    A->>X: Persist immutable exercise bundle
    A->>T: StartAttempt(exercise_id, profile_id)
    T-->>A: ExerciseAttempt STARTED
    A-->>A: Present PublicExercise only
    A->>T: SubmitAnswer(attempt_id, question_id, payload)
    T-->>A: Append AnswerSubmission; update attempt revision
    A->>E: Evaluate(submission_id, pinned private data, rubric)
    E-->>A: Persist EvaluationResult
    A->>F: CreateFeedback(evaluation_id, reveal policy)
    F-->>A: Persist FeedbackResult
    A-->>A: Present primary feedback; accept correction or next step
    A->>T: CompleteAttempt(attempt_id)
    T-->>A: COMPLETED (only when required steps satisfied)
    A->>F: Create final feedback / reveal request
    F-->>A: Reveal permitted expected answers
    A->>P: RecordLearningEvidence(attempt + evaluations + hints)
    P-->>A: SkillProgress revision
    A->>S: RecommendNext(profile_id, progress snapshot)
    S-->>A: LearningRecommendation or NO_ELIGIBLE_ACTIVITY
```

The sequence is a logical order, not a requirement for network calls or asynchronous infrastructure. An implementation may execute adjacent steps in one local application transaction where ownership and atomicity remain clear.

## 3. Use-case contracts

### 3.1 GenerateExercise

**Owner:** Exercise Engine. **Caller:** Application.

Input:
- requested `skill_id`, eligible `exercise_type_id` and version;
- approved `ExerciseConfig` and difficulty constraints;
- `ruleset_id` and version where game semantics apply;
- generator identity/version and reproducibility seed/provenance;
- eligibility context supplied by Strategy or explicit learner selection.

Output:
- immutable `Exercise` envelope;
- separate `PublicExercise` and `PrivateExerciseData` references;
- coverage attribution: primary skill and optional secondary skill IDs;
- validation status and generator provenance.

Preconditions and behavior:
- type, example family, ruleset and any scoring rubric must be eligible for the requested use (scored vs unscored practice);
- generator validates its output against the exercise type schema and required invariants before persistence;
- generation failure returns a stable error and creates no presentable exercise;
- a generated instance is persisted before `StartAttempt` is allowed;
- regeneration creates a new `exercise_id`; it never mutates a previously shown instance.

### 3.2 StartAttempt / ResumeAttempt

**Owner:** Attempt Lifecycle.

`StartAttempt` creates a new `attempt_id` for a persisted exercise and profile. The default is one active attempt per explicit learner start; repeated clicks/retries with the same command ID return the same attempt. A genuinely new attempt requires a new explicit start action and policy permission.

`ResumeAttempt` returns the existing non-terminal attempt and its completed required steps, submissions and feedback-view state. It must not regenerate the exercise or reset its history.

Allowed transitions:

| From | Operation | To | Condition |
|---|---|---|---|
| — | StartAttempt | STARTED | Exercise exists and is eligible |
| STARTED | first accepted submission | IN_PROGRESS | Submission passes structural validation |
| IN_PROGRESS | accepted submission | IN_PROGRESS | Attempt is not terminal |
| STARTED / IN_PROGRESS | CompleteAttempt | COMPLETED | All required steps satisfy completion policy |
| STARTED / IN_PROGRESS | AbandonAttempt | ABANDONED | Explicit abandon or approved inactivity policy |
| COMPLETED | any mutating attempt command | rejected | Terminal |
| ABANDONED | ResumeAttempt | rejected by default | Reopening requires explicit product policy |

An invalid transition returns `LIFECYCLE_CONFLICT` and changes nothing. `COMPLETED` and `ABANDONED` are terminal in the MVP baseline.

### 3.3 SubmitAnswer

**Owner:** Attempt Lifecycle for append + sequence; Evaluation owns interpretation.

Input includes command envelope, attempt/question/step IDs, typed answer payload, and explicit hint-event references that occurred before submission.

Validation:
- attempt exists and is `STARTED` or `IN_PROGRESS`;
- question belongs to the referenced exercise and is currently answerable;
- answer payload matches the question's declared type and schema;
- required identifiers and command idempotency data are valid.

On acceptance, append `AnswerSubmission`, increment the attempt revision/sequence, and set status to `IN_PROGRESS` atomically. Do not evaluate by mutating the submission. The same command ID and canonical payload returns the original result; a new command ID represents a new learner action even if its payload is identical.

A submission after completion, for a future locked step, or for a question from another exercise is rejected without mutation.

### 3.4 EvaluateSubmission

**Owner:** Evaluation.

Input references one persisted submission, the matching private exercise data, the rubric version pinned to that exercise and the evaluator version.

Output is one immutable `EvaluationResult` with:
- outcome `CORRECT`, `PARTIALLY_CORRECT`, `INCORRECT` or `UNSCORABLE`;
- criterion-level outcomes and normalized interpretation;
- stable error/diagnostic codes;
- required vs optional score contribution;
- evaluated timestamp and exact evaluator/rubric versions.

Rules:
- Evaluation cannot access private data for a different exercise or a different question.
- Missing, malformed or contradictory expected data yields `UNSCORABLE` or a validation failure; it must not guess an expected answer.
- Accepted alternatives and numeric tolerance come only from the approved rubric/example contract. No global tolerance is inferred.
- Re-evaluation under a new evaluator/rubric creates a new versioned result linked to the original submission; it does not overwrite the earlier result.
- Evaluation does not decide mastery, recommendation, feedback wording or answer reveal timing.

### 3.5 CreateFeedback / RevealAnswer

**Owner:** Feedback. **Source of correctness:** EvaluationResult.

For an ordinary step response, Feedback may expose:
- whether the submitted response meets the evaluated criteria;
- one primary error/message code and concise explanation;
- an optional learner-requested detail section allowed by the feedback policy.

Before the required exercise completion condition, Feedback must not expose:
- the full expected answer;
- accepted alternatives that would disclose a future answer;
- private generator state or hidden scenario facts unrelated to the current permitted explanation.

After `ExerciseAttempt.status == COMPLETED`, final feedback may include the expected answers and full rationale for the exercise's required and optional steps, subject to the pinned reveal policy. A reveal request before completion returns `LIFECYCLE_CONFLICT` (or a dedicated `ANSWER_REVEAL_NOT_ALLOWED` code if adopted consistently) and reveals nothing.

Feedback viewed state is separate presentation evidence. Generating feedback does not imply it was displayed or read.

### 3.6 RecordLearningEvidence

**Owner:** Learning Progress.

Progress consumes references to immutable evidence, including:
- exercise/attempt identity, primary and secondary skill attribution;
- first and final submissions and their evaluation references;
- correction count and submission order;
- hint events and their timing relative to submissions;
- completion/abandonment and feedback-view evidence;
- exercise, ruleset, evaluator, rubric and progress-policy versions.

Progress must preserve distinctions between first-submission accuracy, final accuracy, independent accuracy, hint-assisted work and optional-step performance. It must not infer independence solely from a correct final answer. The exact eligibility rule for independent evidence is controlled by the approved mastery policy.

Progress updates are idempotent by evidence identity and policy version. Reprocessing the same evidence cannot count it twice. Recalculation under a new policy creates a new derived revision and retains links to the original evidence.

### 3.7 RecommendNext

**Owner:** Learning Strategy.

Input is a read-only profile/progress snapshot, due-review candidates, learner-selected constraints and a versioned strategy policy.

Output is either:
- immutable `LearningRecommendation` with selected skill/activity/config, reason codes, referenced evidence and policy version; or
- explicit `NO_ELIGIBLE_ACTIVITY` with reason codes and a safe product fallback (e.g. show skill catalogue or explain that the current MVP catalogue is exhausted).

Strategy cannot:
- change `SkillProgress` or any submission/evaluation;
- select an exercise family/example marked blocked or not approved for scored use;
- fabricate evidence or claim mastery without the progress policy;
- conceal why an activity was selected.

Learner override is permitted only as defined by the approved strategy policy; an override changes the next request context, not historical recommendation evidence.

## 4. Events and idempotency

Minimum logical event names (payloads reference IDs and versions; private answer payloads are never emitted to general presentation subscribers):

| Event | Aggregate | When emitted | Consumer purpose |
|---|---|---|---|
| `EXERCISE_CREATED` | Exercise | Immutable bundle persisted | Attempt lifecycle / audit |
| `ATTEMPT_STARTED` | Attempt | New attempt persisted | Presentation / progress evidence |
| `ANSWER_SUBMITTED` | Attempt | Submission appended | Evaluation |
| `ANSWER_EVALUATED` | Submission | Evaluation result persisted | Feedback / progress |
| `FEEDBACK_CREATED` | Feedback | Feedback result persisted | Presentation |
| `FEEDBACK_VIEWED` | Attempt interaction evidence | Learner actually views feedback | Progress evidence |
| `ATTEMPT_COMPLETED` | Attempt | Required completion satisfied | Reveal / progress |
| `ATTEMPT_ABANDONED` | Attempt | Explicit or approved abandon | Progress evidence |
| `PROGRESS_RECALCULATED` | Profile-skill progress | Derived revision persisted | Strategy / presentation |
| `RECOMMENDATION_CREATED` | Recommendation | Selection persisted | Presentation |

Events are emitted only after the owning write is committed. Consumers track processed event/evidence IDs or use idempotent upserts scoped by immutable identity and policy version. A consumer retry must not duplicate a score, hint, attempt, or recommendation.

## 5. Cross-module ownership and forbidden shortcuts

| Module | May write | Must not write |
|---|---|---|
| Exercise Engine | Exercise bundle and generator provenance | Attempts, submissions, evaluation, progress |
| Attempt Lifecycle | Attempt state and append-only submission/hint references | Expected answers, evaluation outcomes, progress metrics |
| Evaluation | EvaluationResult | Submission payload, feedback, progress, recommendation |
| Feedback | FeedbackResult and reveal/view evidence | Correctness outcome, score, progress |
| Learning Progress | Derived SkillProgress revisions and evidence links | Source submissions/evaluations, strategy selection |
| Learning Strategy | LearningRecommendation | SkillProgress, exercise history, evaluation |
| Application | Workflow orchestration and command dispatch | Domain-owned records except through owner use cases |
| Presentation | View state and explicit user interaction evidence | Private exercise data, authoritative domain state |

No module imports another module's persistence implementation. Cross-boundary communication uses logical contracts/use cases and stable identifiers.

## 6. Failure and recovery behavior

| Failure point | Required behavior | Retry behavior |
|---|---|---|
| Generation/validation | No presentable exercise; stable error | New generation command creates a new instance |
| Persist exercise bundle | All three logical records persist or none are exposed | Retry same command idempotently |
| Start attempt | No partial attempt | Retry same command returns same attempt |
| Append submission | Submission and attempt revision update atomically | Retry same command returns original submission |
| Evaluation | Submission remains; no fabricated result | Retry evaluation idempotently for same evaluator/rubric version |
| Feedback | Evaluation remains authoritative | Retry feedback creation without changing evaluation |
| Progress update | Evidence remains available; derived progress may lag | Reprocess evidence idempotently |
| Recommendation | Progress remains unchanged | Retry selection with same policy/input snapshot or create a new explicitly versioned recommendation |

The UI must distinguish a saved answer awaiting evaluation from a failed answer save. It must never tell the learner that an answer was recorded if the append operation failed.

## 7. Required acceptance scenarios

The following scenarios are mandatory logical tests for the MVP implementation. They supplement, and do not replace, the S1–S9 fixture validation catalogue.

| ID | Given / When | Expected result |
|---|---|---|
| LL-01 | Generate a scored exercise whose example is blocked | Generation is rejected for scored use; no attempt starts |
| LL-02 | Generate a valid exercise | Exercise, public view and private data are persisted before presentation |
| LL-03 | Inspect serialized PublicExercise before completion | No expected answer, hidden dice or private rubric data is present |
| LL-04 | Submit a structurally valid answer | One immutable submission is appended and attempt becomes IN_PROGRESS |
| LL-05 | Retry the same command ID and payload | Original submission is returned; no duplicate evidence |
| LL-06 | Submit same answer with a new command ID | A second submission is recorded as a distinct learner action |
| LL-07 | Submit to a locked future step | Rejected; no submission or attempt mutation |
| LL-08 | Evaluate the same submission twice with same versions | Same logical result; no duplicate score evidence |
| LL-09 | Evaluate under a new evaluator version | New linked result; original result remains intact |
| LL-10 | Request full answer before required completion | Reveal denied; no private answer leaks |
| LL-11 | Complete all required steps while optional step is unanswered | Attempt completes; optional step does not block required completion |
| LL-12 | Complete attempt then submit another answer | Rejected as terminal lifecycle conflict |
| LL-13 | Record same evaluation evidence twice | Progress evidence is counted once for the same policy version |
| LL-14 | Correct final answer follows an incorrect first answer | First and final accuracy remain distinct; not silently classified as independent success |
| LL-15 | Recommendation candidates include blocked exercise | Blocked candidate is excluded from scored recommendation |
| LL-16 | No eligible activity exists | Explicit no-eligible state and safe fallback are returned |
| LL-17 | Feedback is created but not displayed | No `FEEDBACK_VIEWED` evidence is recorded |
| LL-18 | Crash after submission persistence but before evaluation | Attempt and submission survive; evaluation can resume without duplicate submission |

## 8. Policy dependencies that remain approval-gated

This contract deliberately does not decide the following product policies:

- exact numeric tolerance and accepted alternatives for each exercise example;
- whether and how partial criterion results contribute to a final score;
- independent-evidence definition and mastery threshold;
- review intervals, time anchor and restoration threshold;
- strategy candidate priority, prerequisite behavior and learner override;
- qualitative evidence bands and learner-facing wording for S7;
- scenario-specific objective/trade-off evaluation for S8/S9.

The owning policy/decision documents remain authoritative. Until approval, an affected exercise/policy path is not release-eligible for scored MVP. The implementation must expose a clear blocked/unavailable state rather than substitute a guessed default.

## 9. Detailed-design exit checklist

- [ ] Every use case has an owner, typed input/output, preconditions and stable errors.
- [ ] Attempt state transitions and terminal behavior are implemented as specified.
- [ ] Public/private payload separation is tested at serialization and export boundaries.
- [ ] Submission, evaluation and feedback are independently persisted and linked by IDs.
- [ ] Answer reveal is guarded by required completion, not by a presentation-only condition.
- [ ] Progress evidence is idempotent and policy-versioned.
- [ ] Strategy only selects eligible activities and has an explicit empty-candidate result.
- [ ] Crash/retry scenarios LL-01–LL-18 have automated tests and recorded evidence.
- [ ] Every approval-gated policy is either approved or its affected exercise paths are excluded from scored release scope.
