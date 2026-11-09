# S1–S9 Release Acceptance Checklist

**Status:** proposed product acceptance checklist.

This checklist translates the S1–S9 catalogue and MVP scope into reviewable release evidence. It does not prescribe implementation details.

## 1. Rules and scenario integrity

- [ ] Every exercise declares the ruleset/version it uses.
- [ ] The scenario is valid under that ruleset.
- [ ] All active players, dice counts, turn order, current bid, and game mode are internally consistent.
- [ ] Eliminated players remain visible only where the scenario requires them and are excluded from active calculations.
- [ ] Hidden dice values are not present in learner-visible task data.
- [ ] No scored answer relies on an unresolved rule decision.

## 2. Learning objective and skill ownership

- [ ] Exactly one primary skill is declared for scoring.
- [ ] Secondary skills are listed as prerequisites/context, not silently double-counted.
- [ ] The learner action directly measures the stated objective.
- [ ] The exercise is placed at an appropriate point in the learning path.
- [ ] A prerequisite gap leads to a useful refresher or explanation, not a dead end.

## 3. Answer and evaluation

- [ ] Required answer fields are explicit.
- [ ] Optional fields are identified and do not affect mandatory score.
- [ ] Accepted answer forms and tolerances are explicit.
- [ ] Boundary cases are covered.
- [ ] “Insufficient information” is accepted where appropriate.
- [ ] Evaluation uses only the information the learner is entitled to know.
- [ ] S8/S9 evaluation does not infer answer quality solely from the random game outcome.

## 4. Feedback and learning evidence

- [ ] The first feedback identifies the primary error without overwhelming the learner.
- [ ] A fuller explanation is available on request or after completion.
- [ ] The expected answer is not exposed before the exercise is complete.
- [ ] Hints are recorded separately from answer correctness.
- [ ] First-attempt and final correctness are both preserved.
- [ ] Corrections and repeated submissions are distinguishable.
- [ ] Progress evidence is attributed to the primary skill unless distinct secondary evidence is explicitly defined.

## 5. Skill-specific acceptance evidence

| Skill | Minimum evidence required for release review |
|---|---|
| S1 | Ordinary round, Dudo resolution, elimination/turn progression, and Palifico rule examples |
| S2 | Correct reading of visible state; no leakage of hidden dice |
| S3 | Correct own-match/required-match calculation in normal, ones, and Palifico cases |
| S4 | Exact/at-least probability plus zero and impossible boundaries |
| S5 | Legal/illegal bid examples for each included bid mode |
| S6 | Correct event order, current/previous bid, and incomplete-history handling |
| S7 | Fact/hypothesis separation, repeated evidence, alternative explanation, insufficient sample |
| S8 | Decision comparison with explicit objective and contemporaneous information |
| S9 | Conditional plan, resource/position consideration, and revision trigger |

## 6. UX and session continuity

- [ ] The learner can identify the current task and the one primary action.
- [ ] Instructions are separate from the exercise data.
- [ ] The same task remains understandable on desktop and mobile.
- [ ] Error messages explain what needs correction.
- [ ] The learner can leave and resume without losing a started attempt or progress evidence.
- [ ] A completed exercise provides a clear next step without forcing a long session.

## 7. Release review outcome

For each checklist section, record one of:

- **Pass** — supported by a concrete exercise/example and review evidence.
- **Fail** — contradicts a contract or produces an incorrect/unsafe learning result.
- **Not applicable** — justified because the exercise does not cover that behaviour.
- **Blocked** — depends on an unresolved product or rules decision.

A release candidate is not accepted while any mandatory item is Fail or Blocked. Not-applicable decisions require a short rationale.
