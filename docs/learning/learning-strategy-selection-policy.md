# Learning Strategy Selection Policy for MVP

**Status:** proposed deterministic product policy for review. It is a selection policy, not an implementation design and not an ML model.

## 1. Purpose

Learning Progress records evidence about what the learner has done. Learning Strategy uses that evidence to recommend the next useful exercise. The two responsibilities must remain separate:

- Learning Progress stores attempts, correctness, hints, corrections, skill status, and review dates.
- Learning Strategy selects a next action from the available approved exercise families and explains the reason.

The MVP uses transparent rules. A recommendation must be explainable from the learner's current state and must not imply more certainty than the evidence supports.

## 2. Inputs

The selection policy may use:

- learner-selected skill or explicit request to continue the recommended path;
- skill status (`NOT_STARTED`, `LEARNING`, `PRACTICING`, `MASTERED`, `NEEDS_REVIEW`);
- prerequisite relationships from the approved learning path;
- due review date and review outcome;
- first-submission and final correctness;
- primary error category;
- hint and correction evidence;
- exercise family, variant, and difficulty already completed;
- availability and approval status of exercise instances.

It must not use hidden opponent dice, inferred personal traits, or an opaque user score.

## 3. Candidate eligibility

An exercise is eligible only when:

- its family is in the approved MVP scope;
- its example and rules dependencies are approved;
- its exercise, ruleset, and evaluator versions are available;
- the learner is allowed to attempt it under the current learning-path policy;
- it is not a replay of the same stored instance when independent evidence is required.

`DRAFT`, `READY_FOR_REVIEW`, `BLOCKED_BY_RULE_DECISION`, and `RETIRED` examples are not eligible for scored recommendations. A `READY_FOR_REVIEW` item may be shown to a reviewer, but not to a learner as an approved scored exercise.

If no eligible exercise exists for a requested skill, explain that the skill has no approved exercise available. Do not silently substitute an unrelated skill or a blocked example.

## 4. Recommended selection priority

When the learner has not explicitly selected a different skill, apply this order:

1. **Due review:** select an eligible review exercise for a skill whose review is due or whose status is `NEEDS_REVIEW`.
2. **Targeted remediation:** address a recent primary error that is still relevant and has an approved remediation exercise.
3. **Prerequisite support:** if the next path skill depends on an unstarted or weak prerequisite, offer a short prerequisite exercise.
4. **Current skill continuation:** continue the skill currently being practiced, varying the instance where possible.
5. **Next learning-path skill:** recommend the next eligible skill whose prerequisites are sufficiently supported.
6. **Learner choice:** if no recommendation is clearly preferable, let the learner choose among eligible skills.

This is an ordering of recommendation candidates, not a hard prohibition on learner choice. The learner may open another available skill unless a separate product safety or content rule prevents it.

## 5. Choosing among exercises within a skill

Within the selected skill:

1. Prefer an exercise that addresses the latest relevant primary error.
2. Otherwise choose an uncompleted or materially different variant.
3. Prefer a difficulty supported by recent independent performance; do not increase difficulty solely because the final answer became correct after hints or corrections.
4. Avoid repeating the same stored instance for mastery evidence.
5. If no suitable variant exists, repeat the family with a new instance only when the generator can produce a meaningfully distinct scenario; otherwise explain the limitation.

Do not use a hidden randomized score to choose difficulty. If the evidence is insufficient to distinguish two candidates, use a stable documented tie-breaker, such as the learning-path order.

## 6. Error remediation

A remediation recommendation must connect to an observed error category or failed required criterion.

Examples:

- `COUNT_ERROR` may lead to an S3 exercise that isolates own matches and required hidden matches.
- `PROBABILITY_ERROR` may lead to an S4 exercise that distinguishes “exactly” from “at least”.
- `EVIDENCE_OVERREACH` may lead to an S7 exercise about sample limits and alternative explanations.
- `OBJECTIVE_MISMATCH` may lead to an S8 exercise with an explicit objective and multiple acceptable trade-offs.
- `PLAN_INCOMPLETE` may lead to an S9 exercise that asks for a missing assumption or revision trigger.

Do not send every error to a generic easier exercise. The remediation must preserve the skill boundary and target the failed concept.

## 7. Prerequisites and learning-path behavior

Prerequisites guide recommendations but do not automatically lock the entire product.

- A learner may explore an available skill even if a recommended prerequisite is not mastered.
- If the learner struggles with a dependent exercise, recommend the smallest relevant prerequisite refresher.
- Do not require mastery of every earlier skill before opening a later skill.
- A prerequisite recommendation should explain the dependency in plain language.
- The learning path may recommend a return to an earlier skill without describing the learner as having regressed.

For a combined-skill exercise, select and report one primary skill. Secondary evidence is recorded only for separately observable criteria and must not inflate mastery counts for the primary skill.

## 8. Recommendation explanation

Every recommendation should have a short reason based on visible learning evidence, for example:

- “Повторим S3: в последнем задании была ошибка подсчёта совпадений.”
- “Пора проверить S4: после освоения навыка наступил срок повторения.”
- “Можно перейти к S6: базовые навыки, нужные для чтения истории, уже доступны.”

Avoid labels such as “weak player”, “poor strategist”, or “low ability”. Explain the next action, not a judgment about the learner.

## 9. Session behavior

A short learning session should:

- begin with the learner's explicit choice or one clear recommendation;
- present one primary exercise at a time;
- avoid forcing completion of all nine skills;
- offer a relevant next step after completion;
- allow the learner to stop and resume later;
- preserve unfinished exercise state according to the existing lifecycle contract.

A recommendation must not automatically launch an exercise or discard an unfinished attempt.

## 10. No eligible next exercise

If no eligible exercise can be selected:

1. preserve the learner's current progress;
2. explain the reason at the appropriate level (for example, all relevant examples are awaiting approval);
3. offer another eligible skill or a non-scored rules/reference view if one exists;
4. do not present a blocked or draft exercise as scored content;
5. do not claim that the learner has completed the product merely because no exercise is available.

## 11. Determinism and explainability

Given the same learner evidence, approval catalogue, and policy version, the MVP should produce the same recommendation, except where an explicitly approved rotation among equivalent exercise variants is used.

For each recommendation, retain enough rationale to explain:

- the selected skill and exercise family;
- the triggering evidence (review due, error, prerequisite, or continuation);
- why higher-priority candidates were unavailable or not applicable;
- the policy version used.

This rationale is a product requirement; it does not prescribe how the implementation stores or computes it.

## 12. Approval checklist

The policy is ready for implementation handoff when:

- the priority order is approved;
- the prerequisite map is approved and non-blocking behavior is confirmed;
- review timing is approved together with the mastery policy;
- remediation categories map to actual MVP exercise families;
- learner choice and unfinished-attempt behavior are accepted;
- the no-eligible-exercise state has an approved UX treatment;
- recommendation explanations are reviewed for clarity and non-judgmental language.
