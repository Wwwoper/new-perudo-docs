# Answer Evaluation Rubric for MVP Exercises

**Status:** proposed product policy for review. This document defines evaluation behavior, not a storage schema or implementation technology.

## 1. Purpose

Every scored MVP exercise needs a predictable relationship between the learner's answer, the expected answer, the evaluation result, and the feedback shown. This rubric standardizes that relationship across S1–S9 while preserving each skill's distinct responsibility.

The evaluator must judge the submitted answer against the stored exercise instance and its versioned criteria. It must not use hidden information that was not part of the learner-facing task.

## 2. Evaluation unit: atomic criteria

An exercise is evaluated through explicit criteria rather than an undocumented impression of the whole answer.

Each criterion is defined before the exercise is released and records:

- the answer field or reasoning element it checks;
- whether it is required or optional;
- the expected value, accepted alternatives, or rubric;
- the applicable ruleset and exercise version;
- the primary error category, if failed;
- the explanation or feedback reference.

Conceptual criterion outcomes:

| Outcome | Meaning |
|---|---|
| `PASS` | The submitted field satisfies the criterion |
| `FAIL` | The submitted field contradicts the criterion or omits a required answer |
| `NOT_APPLICABLE` | The criterion does not apply to this exercise instance |
| `NOT_EVALUATED` | The answer does not provide enough material to evaluate the criterion |

These labels describe product semantics. They do not mandate literal enum names in a future implementation contract.

## 3. Exercise-level result

The exercise result must preserve the criterion-level outcomes and distinguish at least these cases:

- all required criteria pass;
- some required criteria pass and some fail;
- required criteria cannot be evaluated because the answer is incomplete;
- the answer correctly identifies that the scenario is underdetermined;
- the learner has not submitted an answer.

Do not collapse these cases into one percentage or a single boolean before the product owner approves a scoring scale. Optional criteria never change the required-result status.

A response can be partially correct. The learner should see the primary error first, while detailed criterion feedback remains available on request, consistent with the product's low-clutter feedback direction.

## 4. Answer matching and accepted alternatives

### 4.1 Exact answers

Use exact matching when the answer is a discrete rule outcome, event type, player role, state flag, or finite choice. Examples include the player who loses a die after Dudo and whether a bid is legal under a fully approved rule.

### 4.2 Numeric and probability answers

- Prefer exact rational values for probability exercises when the contract provides exact arithmetic.
- If a presentation format permits decimal answers, define the accepted precision and rounding rule in the exercise itself.
- Do not accept a rounded value by an implicit global tolerance.
- Compare quantities, counts, and identifiers exactly unless the exercise explicitly defines a tolerance.

### 4.3 Structured answers

Evaluate each required field independently. For example, an S8 answer may contain both action legality and reasoning quality; passing one must not automatically pass the other.

### 4.4 Free-text answers

Free text is acceptable only when the exercise provides a finite, reviewable rubric. The rubric must identify required semantic elements and prohibited claims. Avoid open-ended scoring such as “sounds reasonable”.

### 4.5 Multiple acceptable answers

When more than one answer is valid, enumerate the accepted alternatives or define a finite criterion-based rubric before release. Do not make the evaluator infer a new acceptable answer after seeing a learner's submission.

## 5. Insufficient information is a first-class answer

If the scenario does not support a unique conclusion, the expected answer must explicitly allow “insufficient information” or its equivalent.

The evaluator distinguishes:

- correctly identifying insufficient information;
- giving a plausible but unsupported guess;
- omitting the answer without explaining the limitation.

For S6, a partial history does not prove that earlier events did not occur. For S7, an isolated observation does not establish a stable pattern. For S8/S9, an unstated objective or missing state information must not be silently invented.

## 6. Skill-specific evaluation boundaries

| Skill | Required evaluation focus | Must not be used as a shortcut |
|---|---|---|
| S1 | Rule interpretation and resulting state/event | Familiarity with another edition |
| S2 | Correct reading of visible state and explicit unknowns | Hidden dice values |
| S3 | Own matches, active hidden-dice count, and required matches | Probability or action recommendation |
| S4 | Correct probability for the supplied mode, face, and count | Strategic preference or action selection |
| S5 | Legality and mechanics under the approved transition rules | Opponent motive or strategic quality |
| S6 | Event order, bid sequence, and supported factual summary | Intent attribution |
| S7 | Evidence quality, comparability, alternative explanations, and uncertainty | Hidden values or one-event personality labels |
| S8 | Legality, decision-time information, stated objective, and reasoning | Round outcome as a proxy for decision quality |
| S9 | Goal, assumptions, conditional branches, and revision trigger | Prediction of random future outcomes |

## 7. Error diagnosis

Every scored exercise should map common failures to a primary diagnostic category. Categories should describe the mistake, not label the learner.

Suggested reusable categories:

- `RULE_MISREAD` — a rule or exception was interpreted incorrectly;
- `STATE_MISREAD` — visible state or event order was read incorrectly;
- `COUNT_ERROR` — a count or required-match calculation was incorrect;
- `PROBABILITY_ERROR` — the probability model or event interpretation was incorrect;
- `BID_MECHANICS_ERROR` — a bid transition or legality rule was misapplied;
- `EVIDENCE_OVERREACH` — a conclusion exceeded the observations;
- `UNSUPPORTED_ASSUMPTION` — the answer used information or a premise not supplied;
- `OBJECTIVE_MISMATCH` — the decision did not follow the stated objective;
- `PLAN_INCOMPLETE` — a strategic plan omitted a required branch, assumption, or revision trigger;
- `INCOMPLETE_RESPONSE` — a required answer field was omitted.

An exercise may define a more specific diagnostic code. These suggested categories are not a requirement to force every error into a generic taxonomy.

## 8. Submission, hint, and correction evidence

Evaluation must preserve separate evidence for:

- the first submitted answer;
- each later submission or correction;
- whether a hint was requested and when it was shown;
- the final answer;
- the criterion-level result for each submission.

A later corrected answer must not overwrite the first-submission result. A hint does not automatically reduce the correctness of the submitted answer, but it changes whether that answer qualifies as independent evidence for mastery.

## 9. Feedback contract

Default feedback should:

1. identify the primary error or confirm the key success;
2. explain the governing rule, calculation, evidence limitation, or objective mismatch;
3. avoid revealing unrelated private expected-answer fields;
4. offer detailed reasoning only when useful or requested;
5. keep the factual outcome separate from the quality of the learner's reasoning.

For a partially correct response, acknowledge the passed required elements before explaining the primary failed element. Do not expose the full answer key before the exercise is completed.

## 10. Minimum evaluation test set

Each exercise family should include, where applicable:

- a fully correct answer;
- an answer with one required field wrong;
- an answer with one required field missing;
- a valid alternative answer;
- an optional-field omission;
- an insufficient-information answer;
- an answer based on unavailable or hidden information;
- a corrected second submission after feedback.

The exercise review record must explain any inapplicable test category rather than silently omitting it.

## 11. Approval checklist

An exercise can move to `APPROVED` only when:

- every required criterion is atomic and observable;
- accepted alternatives are enumerated or covered by a finite rubric;
- numeric precision is explicit;
- optional criteria cannot affect the required result;
- the insufficient-information path is handled where relevant;
- diagnostics and primary feedback are defined;
- first and final submissions remain distinguishable;
- the evaluator uses only information available under the exercise's privacy boundary;
- all rules-dependent criteria reference an approved ruleset decision.

This rubric is a proposed common policy. Skill-specific contracts remain authoritative for domain calculations and exercise semantics.
