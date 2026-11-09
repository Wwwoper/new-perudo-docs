# Learning Product Gaps and Decisions

**Status:** decision register; proposed priorities, not approved rules.

## Purpose

Record product questions that must be answered to turn the S1–S9 catalogue into a consistent MVP. This document separates confirmed project contracts from recommendations and unresolved decisions.

## Confirmed foundations

The following are treated as established by the current product documentation:

- the learning model contains skills S1–S9;
- S2, S3, and S4 have separate responsibilities for perception, counting, and probability;
- S5 validates bid mechanics; S6 reads bid history; S7 analyzes observable opponent behaviour; S8 evaluates decisions; S9 addresses match strategy;
- the first learning strategy uses transparent rules rather than ML;
- hints, corrections, first/final accuracy, and exercise attempts are distinct learning evidence;
- the MVP is a local single-profile product without required registration or cloud synchronization.

## Decision register

| ID | Question | Why it matters | Proposed resolution | Priority |
|---|---|---|---|---|
| LP-01 | What exact bid-transition rules are in scope for MVP? | S1/S5 expected answers depend on them | Approve a complete rules reference table before authoring scored examples | Blocker |
| LP-02 | Which Palifico exceptions must appear in the first release? | Affects S1, S3, S4, S5, and S9 | Include only cases already unambiguously specified; mark the rest blocked | Blocker for affected cases |
| LP-03 | What is the minimum evidence for a behavioural pattern in S7? | Avoids overconfident opponent labels | Use qualitative evidence bands initially; do not publish a universal numeric threshold without validation | High |
| LP-04 | Which objectives may S8 exercises optimize? | “Best decision” is undefined without an objective | State the objective in each exercise; do not assume a universal utility function | High |
| LP-05 | What counts as a valid S9 plan? | Strategy can otherwise be subjective | Require a goal, assumptions, conditional branches, and a revision trigger | High |
| LP-06 | Which skill evidence is required to mark a skill MASTERED? | Controls progression and recommendations | Define per-skill evidence thresholds using repeated independent performance, not a single correct answer | High |
| LP-07 | How should mixed-skill exercises attribute progress? | Prevents double counting | One primary skill per exercise; secondary evidence only when separately observable | High |
| LP-08 | Which S1–S9 exercise variants are release blockers? | Controls MVP scope | Use `mvp-exercise-scope.md` as the release floor; defer breadth, not core coverage | Medium |
| LP-09 | What exact feedback detail appears by default? | Affects cognitive load and answer disclosure | Show the primary error first; make detailed reasoning available on demand | Medium |
| LP-10 | How is the current probability prototype threshold treated? | Prototype thresholds could be mistaken for game rules | Keep thresholds local to the exercise and label them as configurable exercise criteria | Medium |

## Recommended resolution order

1. LP-01 and LP-02: freeze the rules cases used by scored MVP exercises.
2. LP-04 and LP-05: define the objective and acceptance shape for S8/S9.
3. LP-03: agree how S7 expresses evidence strength without unsupported precision.
4. LP-06 and LP-07: define mastery and mixed-skill attribution.
5. LP-08 through LP-10: finalize release scope and feedback conventions.

## Product recommendations

### Avoid false precision

Do not invent universal confidence percentages for S7 or universal utility scores for S8/S9. A number is useful only when its interpretation, evidence base, and calibration method are agreed.

### Keep the MVP complete but narrow

A thin exercise family for every skill is preferable to a polished probability-only trainer that leaves the rest of the learning path absent. Advanced variants can follow after the end-to-end learning loop is validated.

### Treat the prototype as a focused component

The supplied probability trainer can inform S3/S4 exercise interaction and selected S8 examples. It should not be treated as evidence that the wider S1–S9 product requirements are already met.

## Exit criteria for the product-specification phase

The product specification can be considered ready for implementation handoff when:

- all release-blocking rules questions are resolved;
- every MVP skill has accepted exercise examples and answer criteria;
- mastery and progress attribution are defined;
- S7 uncertainty and S8/S9 objective handling are agreed;
- the release acceptance checklist has concrete evidence for each mandatory item;
- deferred features are explicitly distinguished from missing MVP requirements.
