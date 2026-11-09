# MVP Exercise Scope for S1–S9

**Status:** proposed minimum product scope; subject to product-owner approval.

## Scope principle

The MVP should demonstrate a complete learning journey across S1–S9 without attempting to ship every exercise variant in the catalogue. Each skill needs at least one independently assessable exercise family, clear feedback, and evidence that can be used by the learning-progress model.

The MVP is not required to teach every advanced strategic concept or provide exhaustive scenario diversity.

## Minimum exercise set

| Skill | MVP exercise family | Required learner action | MVP acceptance boundary |
|---|---|---|---|
| S1 | Rule application | Resolve a short scenario under the agreed rules | Covers ordinary bidding, Dudo outcome, turn progression, elimination, and a basic Palifico scenario; unresolved rule variants are excluded |
| S2 | Read the table | Identify current visible state and distinguish known from hidden information | No hidden dice values are exposed; facts and unavailable information are separated |
| S3 | Count required matches | Calculate own matches, unknown dice, and required hidden matches | Includes normal face bids, ones bids, and Palifico counting semantics |
| S4 | Interpret probability | Calculate or select exact probability for a required count | Includes exact and at-least interpretation, zero-needed and impossible-needed boundaries |
| S5 | Validate a bid | Classify a proposed bid as legal/illegal and identify the governing rule | Uses only fully agreed bid-transition rules; no unresolved rule case is scored |
| S6 | Read bid history | Reconstruct event order and identify the current/previous bid | Distinguishes `BID_PLACED` from `TURN_ADVANCED`; includes a short incomplete-history case |
| S7 | Evidence-based observation | Separate observed facts from hypotheses and identify insufficient evidence | At least one repeated pattern and one insufficient-data case; no hidden information |
| S8 | Compare decisions | Choose between available actions and justify the choice against an explicit objective | Evaluated using only information available at decision time; outcome does not determine correctness |
| S9 | Conditional match plan | Choose a strategic priority and state a condition that triggers plan revision | At least one multi-round scenario; plan is conditional, not a single guaranteed line |

## What is deliberately deferred

The following are valuable but not MVP release blockers:

- broad procedural generation with many interchangeable templates per exercise family;
- advanced opponent profiles and fine-grained behavioural statistics;
- long-horizon strategic simulations;
- adaptive difficulty beyond transparent prerequisite and performance rules;
- large mixed-skill exercise sets;
- exhaustive variants of every exercise type;
- comparative leaderboards or social progression.

Deferral does not remove these items from the product direction. It keeps the first release focused on a coherent, testable learning loop.

## MVP session shape

A minimal session should support this sequence:

1. Select a skill or continue the recommended path.
2. See a concise learning objective and one worked example where needed.
3. Complete one exercise instance.
4. Submit an answer and receive the primary correction.
5. Optionally inspect the explanation and request a hint.
6. Complete the exercise and see a short result summary.
7. Continue to a prerequisite refresher, another exercise, or the next recommended skill.

The product should not force a learner to complete all nine skills in one sitting.

## Exercise quality floor

Every MVP exercise must have:

- a declared primary skill and learning objective;
- a known ruleset and exercise-generator version;
- a reproducible stored instance;
- public task data separated from private expected answers;
- explicit required and optional answer fields;
- deterministic evaluation criteria for the stored instance;
- at least one useful explanation for the primary error;
- an explicit “insufficient information” answer where the scenario is underdetermined;
- accessibility of the task and answer controls on desktop and mobile layouts.

## MVP completion criteria

The MVP exercise scope is complete when:

- every skill S1–S9 has at least one accepted exercise family;
- all mandatory rules used by those exercises are approved and represented in the ruleset contract;
- each exercise has at least one correct, incorrect, boundary, and insufficient-information test example where applicable;
- primary and secondary skill responsibilities are unambiguous;
- feedback does not reveal private expected answers before completion;
- attempts, hints, corrections, and first/final correctness are distinguishable in progress evidence;
- a learner can finish a short session and resume later without losing the current learning state.

## Release-blocking exclusions

Do not include an exercise in the release gate if its expected answer depends on an unresolved rules decision, an undisclosed hidden value, an ungrounded opponent-intent assumption, or an unstated strategic objective.
