# S1–S9 Product Audit

**Status:** product review; recommendations require product-owner approval where marked.

## Purpose

This document checks the coherence of the S1–S9 learning model as a product: skill boundaries, progression, exercise coverage, feedback, and the relationship between the documented learning experience and the supplied `perudo_trainer.html` prototype.

This is not an implementation design and does not select a technology stack.

## Executive assessment

The nine-skill model forms a coherent progression from understanding the game to making decisions over a full match. The strongest separation is between:

- observing the current table state (S2), counting known dice and deriving missing matches (S3), and calculating probability (S4);
- validating bid mechanics (S5) and reconstructing bid history (S6);
- describing opponent behaviour (S7), evaluating a decision with information available at the time (S8), and planning across rounds (S9).

The main product risk is not a missing skill. It is asking learners to perform several skills at once before each component has been taught and assessed independently. Combined exercises should therefore be introduced only after their prerequisite skills have sufficient evidence of competence.

## Skill boundary audit

| Skill | Primary learner question | Must not take ownership of |
|---|---|---|
| S1 Rules and mechanics | What happens under the agreed rules? | Probability or strategic recommendation |
| S2 Table perception | What is visible in the current state? | Hidden dice values, counting, or inference about intent |
| S3 Counting | How many relevant dice are known, and how many matches are still needed? | Probability or decision selection |
| S4 Probability and risk | How likely is the required count? | Choosing a bid or deciding whether to challenge |
| S5 Bid mechanics and evaluation | Is this bid legal, and how does it compare mechanically? | Reading opponent intent or long-term planning |
| S6 Bid-history reading | What happened, and in what order? | Explaining why an opponent acted |
| S7 Opponent analysis | What behavioural pattern is supported by observations? | Treating assumptions as facts or selecting the learner's action |
| S8 Decision-making | Which available action is better under the stated objective and information? | Rewriting the rules or judging only by outcome |
| S9 Match strategy | What longer-term plan best serves the stated match objective? | Claiming certainty about random outcomes |

## Progression and prerequisite audit

Recommended learning order:

1. S1 — establish the rules vocabulary and legal game transitions.
2. S2 — read a static table state and distinguish facts from missing information.
3. S3 — count own matches and derive the remaining requirement.
4. S4 — interpret exact probability and risk.
5. S5 — validate and compare bids using the agreed rules.
6. S6 — reconstruct and summarize a bidding sequence.
7. S7 — form cautious observations from repeated, comparable behaviour.
8. S8 — choose and justify an action using the available information.
9. S9 — plan across rounds and revise the plan when the state changes.

Prerequisites are instructional dependencies, not hard navigation locks. A learner may open any skill, but the product should explain missing prerequisites and offer a short refresher rather than silently marking the learner as incapable.

| Skill | Required foundations | Suggested readiness evidence |
|---|---|---|
| S1 | None | Correctly resolves basic turn, bid, challenge, and round-transition scenarios |
| S2 | S1 vocabulary | Correctly identifies visible facts and explicitly marks unavailable information |
| S3 | S1, S2 | Correctly calculates own matches and required hidden matches across normal and Palifico cases |
| S4 | S3 | Correctly interprets exact/at-least probability and edge cases |
| S5 | S1, S2 | Distinguishes legal from illegal bids, including mode-specific rules |
| S6 | S1, S2, S5 | Reconstructs event order and detects missing or contradictory history information |
| S7 | S2, S6 | Separates observations, hypotheses, counter-explanations, and confidence |
| S8 | S1–S6; S7 where opponent evidence is supplied | Compares actions using a stated objective and contemporaneous information |
| S9 | S1–S8 as relevant to the scenario | Produces a conditional plan and identifies when it should be revisited |

## Exercise coverage risks

The documented exercise families cover all nine skills at a catalogue level. The following areas still need explicit acceptance examples before they can be treated as release-ready:

- S1: exact scenarios for rule exceptions, especially Palifico and the two-player exception.
- S5: authoritative bid-transition examples for every supported bid mode; do not publish exercises whose expected answer depends on unresolved rule details.
- S6: incomplete history fragments and event-order edge cases.
- S7: minimum evidence for a pattern, comparability of observations, and examples where the correct answer is “insufficient data”.
- S8: explicit objective and available-action set in every exercise; scoring must not depend on the eventual random outcome.
- S9: plans with clear assumptions, conditional branches, and revision triggers; avoid presenting one strategic line as universally optimal.

## Prototype alignment review

The supplied `perudo_trainer.html` is a focused probability/counting trainer rather than an end-to-end S1–S9 learning product. It visibly supports:

- own-hand dice and total dice count;
- own-match and unknown-dice calculations;
- probability estimates for face bids and ones bids;
- a challenge/acceptance threshold exercise;
- a maximum-bid exercise using a displayed probability threshold;
- exact probability tables and an approximate mental-estimation aid.

This maps primarily to S3 and S4, with a narrow S8 decision component. It does not, in its current visible flow, demonstrate the full learning path, skill selection, S1 rule instruction, S5 bid legality, S6 history reconstruction, S7 opponent analysis, or S9 match planning.

The prototype uses a 40% threshold for the “believe / do not believe” decision and a 55% threshold for the maximum-bid exercise. These are prototype exercise conventions, not established universal strategy rules. Product documentation should keep such thresholds explicit, scenario-bound, and revisable; they must not be presented as game rules.

The prototype's local score counts correct fields across submissions. Before using this as mastery evidence, the product must distinguish first-attempt correctness, final correctness, hints, corrections, and repeated attempts as already required by the learning-progress model.

## Cross-skill exercise policy

A combined exercise must declare:

- one primary skill whose learning objective determines the score;
- secondary skills used as prerequisites or context;
- which answer fields are required and which are optional;
- whether a secondary-skill error blocks completion or only produces a diagnostic;
- which information is available to the learner at the moment of the decision.

Do not count the same answer field as independent evidence for multiple skills unless the evaluation criteria explicitly define distinct observable behaviours.

## Audit outcome

**Recommendation:** retain S1–S9 as the product's complete skill model. For MVP, implement a thin, independently assessable exercise path through every skill, while keeping advanced variants and broad scenario diversity out of the release gate. The exact MVP exercise subset and release checks are specified in the companion documents.

This audit is a product-level recommendation, not evidence that the existing prototype or implementation already satisfies the requirements.
