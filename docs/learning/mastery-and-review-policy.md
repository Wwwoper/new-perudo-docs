# Mastery and Review Policy for MVP

**Status:** proposed policy for product-owner approval. Thresholds below are initial product recommendations, not validated learning-science claims.

## 1. Purpose

This policy defines what evidence is sufficient to mark a skill `MASTERED`, how hints and corrections affect that evidence, and how a mastered skill returns to review. It is designed to avoid awarding mastery from one lucky answer or from a corrected answer whose solution has already been revealed.

The policy applies to S1–S9. Skill-specific acceptance criteria remain defined by the relevant exercise contracts and the [answer evaluation rubric](answer-evaluation-rubric.md).

## 2. Evidence terms

### 2.1 Independent first submission

A submission counts as independent evidence only when all of the following are true:

- it is the first submission for that exercise instance;
- no hint for that instance was shown before submission;
- no answer or correctness-revealing feedback for that instance was shown before submission;
- the exercise instance is not a replay of an already completed instance;
- all required evaluation criteria can be evaluated.

A hint requested after an incorrect first submission does not erase the first-submission record. It does mean that the later corrected submission is assisted and cannot replace the original submission as independent evidence.

### 2.2 Successful independent result

A result is successful when every required criterion passes on the independent first submission. Optional criteria do not affect this decision. For S7–S9, every mandatory evidence, objective, and planning criterion in the exercise rubric must pass; a plausible conclusion with an unsupported premise is not a pass.

### 2.3 Distinct exercise instance

An instance is distinct when its stored task data and expected answer are independently generated or authored. Reopening, resetting, or resubmitting the same stored instance does not create a new instance.

## 3. Recommended MVP mastery threshold

**Recommendation:** mark a skill `MASTERED` after three successful independent first submissions on three distinct exercise instances, completed across at least two learning sessions.

Additional conditions:

- all three results must belong to the same primary skill;
- at least two materially different scenario variants should be represented when the MVP family supports them;
- each instance must use an approved ruleset and evaluator version;
- a combined-skill exercise contributes to the primary skill only; secondary evidence is recorded only when its criteria are independently observable and explicitly mapped;
- an exercise blocked by an unresolved rules decision cannot contribute to mastery.

This is a practical release candidate, not a claim that three attempts are universally sufficient. The product owner should approve, adjust, or reject the threshold before implementation handoff.

## 4. Why hints and corrections do not count as independent mastery evidence

| Learning event | Correctness record | Independent mastery evidence |
|---|---|---|
| Correct first submission, no prior hint | Successful first result | Yes |
| Incorrect first submission, then hint and correction | First result remains incorrect; later result is assisted | No for the corrected submission |
| Hint before first submission, then correct answer | Correct assisted result | No |
| Multiple submissions after feedback | Preserve every submission and result | Only the original eligible first submission may count |
| Replay of the same stored instance | Preserve replay history | No additional distinct-instance evidence |
| Correct answer with an unsupported explanation | Field-level result retained | No if a required reasoning criterion fails |

Hints remain a learning aid, not a penalty. They should not subtract points from answer correctness; they only distinguish assisted performance from independent performance.

## 5. Skill-specific mastery evidence

The common threshold is applied to skill-specific criteria, not to a generic quiz score.

| Skill | Required evidence for a successful instance |
|---|---|
| S1 | Correctly applies the rules and derives the resulting game consequence; rule-dependent cases must be approved |
| S2 | Correctly reads visible state and explicitly distinguishes hidden or unavailable information |
| S3 | Correctly calculates own matches, active hidden dice, and required matches for the stated mode |
| S4 | Correctly interprets the requested event (exactly / at least) and calculates the probability under the stated mode |
| S5 | Correctly classifies bid legality and identifies the governing approved transition rule |
| S6 | Reconstructs the public event sequence and limits conclusions to the available history |
| S7 | Separates facts from hypotheses, handles comparability, and avoids unsupported certainty |
| S8 | Separates action legality, decision-time evidence, stated objective, and reasoning quality |
| S9 | Provides a goal, assumptions, conditional branch(es), and an observable revision trigger |

The exercise-specific rubric determines which criteria are mandatory in a particular instance. This table does not add new game rules or strategic objectives.

## 6. Skill status transitions

The following transition behavior is recommended for MVP:

| Current status | Evidence or event | Next status |
|---|---|---|
| `NOT_STARTED` | Learner begins the skill | `LEARNING` |
| `LEARNING` | Learner completes an initial exercise and has usable evidence | `PRACTICING` |
| `PRACTICING` | Mastery threshold is met | `MASTERED` |
| `MASTERED` | A scheduled review becomes due | `NEEDS_REVIEW` |
| `MASTERED` | A review exercise is attempted and a required criterion fails | `NEEDS_REVIEW` |
| `NEEDS_REVIEW` | Two successful independent first submissions on two distinct review instances | `MASTERED` |

A failed review should not delete prior mastery evidence or reset the learner's history. It changes the current recommendation and status while preserving the evidence that led to the earlier mastery decision.

If the learner has not attempted a due review, the product may label the skill as due for review without claiming that the learner has forgotten it. The status transition to `NEEDS_REVIEW` represents a product scheduling state, not a diagnosis of memory loss.

## 7. Review scheduling

The existing interval sequence of 1, 3, 7, 14, and 30 days remains a starting hypothesis for product calibration. It is not presented as a scientifically established optimum.

Recommended initial behavior:

1. After mastery, schedule the first review using the first interval.
2. After a successful independent review, advance to the next interval.
3. After the final interval, continue at the final interval until the product has evidence to justify another policy.
4. After an unsuccessful review, keep the skill in `NEEDS_REVIEW`, provide targeted remediation, and require two independent successful review instances to restore `MASTERED`.
5. A hint-assisted or corrected review may support learning, but it does not advance the independent review interval.

The exact anchor time (completion time versus session end), missed-review grace period, and behavior after a long absence remain product-owner decisions. Do not silently choose a timezone or calendar-day interpretation in the product contract.

## 8. Progress facts that must remain separate

The learning-progress record should preserve at least:

- first-submission correctness;
- final-submission correctness;
- independent versus assisted result;
- hint count and timing;
- submission and correction counts;
- distinct exercise instances and variants;
- session association;
- primary and separately evidenced secondary skills;
- current skill status and next review due time;
- the ruleset, exercise, and evaluator versions used.

Do not infer mastery from final accuracy alone. Do not erase a first error when a later answer is correct.

## 9. Calibration and review safeguards

After MVP use, review the policy using observable product evidence:

- how often a skill is marked mastered and then fails its first review;
- how many attempts and sessions are typically required;
- whether one exercise family disproportionately controls mastery;
- whether hint use creates an unintended barrier to progress;
- whether review scheduling causes repeated, low-value exercises.

Any threshold change must be versioned. Existing evidence should not be silently reinterpreted under a new threshold; the product must define whether re-evaluation is prospective or requires an explicit recalculation.

## 10. Decisions requiring approval

| Decision | Current recommendation | Status |
|---|---|---|
| Mastery evidence count | 3 independent successful first submissions | Proposed |
| Instance diversity | 3 distinct instances across at least 2 sessions | Proposed |
| Variant diversity | At least 2 variants when available | Proposed |
| Restoration after review failure | 2 independent successful review instances | Proposed |
| Review intervals | 1, 3, 7, 14, 30 days as calibration starting points | Hypothesis |
| Missed-review grace period | Not defined | Open |
| Review interval anchor | Not defined | Open |
| Threshold migration for existing users | Explicit versioned decision required | Open |

No threshold in this document should be treated as approved until the product owner records the decision and the corresponding product contract is updated.
