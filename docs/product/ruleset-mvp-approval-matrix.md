# MVP Ruleset Approval Matrix

**Status:** product-owner review artifact. It records the currently agreed baseline and the remaining rule decisions; it does not introduce new game rules.

## 1. Purpose

Scored exercises must be derived from one explicit ruleset. This matrix identifies which rule areas are safe to use in MVP examples and which must remain excluded until the authoritative Game Domain wording and boundary examples are approved.

The matrix is intentionally conservative. A rule being familiar from another Perudo edition is not sufficient evidence that it belongs to the project's selected ruleset.

## 2. Agreed baseline available to the learning product

The following points are treated as existing project decisions and may be used when the exercise states the relevant game mode and state:

| Area | Agreed baseline | Exercise use |
|---|---|---|
| Ruleset identity | `Perudo Classic — Zygomatic 2022` | Every scored example identifies or inherits this ruleset version |
| Calza | Disabled | Do not offer Calza as an action in MVP exercises |
| Participants and dice | 2–6 participants; 5 dice at the start | May be used in setup and state-reading exercises |
| Ordinary round | Ones are wild for bids on faces 2–6 | S3/S4 may use the established normal-mode counting and probability contracts |
| Bid on ones in ordinary mode | Counts ones only | Do not count other faces as matches |
| Dudo | If the bid is true, the challenger loses one die; otherwise the bidder loses one die | S1/S8 may use the established resolution rule |
| Elimination | A player with zero dice is eliminated | Keep the player in historical presentation; exclude them from active turn order and calculations |
| Next round starter | The round loser starts if still active; otherwise the next active player clockwise starts | Use only when the prior round's loser and active order are explicit |
| Game end | One active player remains | May be used in S1/S9 end-state examples |
| Palifico activation | A player reaching one die activates Palifico for the next round only when more than two players are active at that round's start | Use only when the activation facts are explicit in the exercise state |
| Palifico duration | The special mode applies to the next round only | Do not carry the mode into later rounds |
| Declared Palifico | Once a player has actually declared Palifico, `has_declared_palifico` remains true for the rest of the game | Do not derive this flag from current dice count alone |
| Bid upper bound | A bid above the total number of active dice is prohibited | May be used for this boundary check only; it does not define bid-to-bid progression |
| Event semantics | `BID_PLACED` records a bid; `TURN_ADVANCED` advances the turn exactly once | S6 examples must keep these event meanings separate |

## 3. Rule areas that remain blocked or need explicit examples

| Decision ID | Rule area | What is not yet sufficiently specified in this learning packet | Required approval artifact | Scored MVP status |
|---|---|---|---|---|
| RULE-MVP-01 | Ordinary bid progression | Complete ordering and legal transition rules between quantity/face pairs, including all boundary cases | Authoritative transition table plus examples for each transition class | Block transitions that depend on the missing table; retain only independently established checks |
| RULE-MVP-02 | Transition to/from ones | Exact legal transition conditions and ordering relative to other faces | Explicit table rows and boundary examples for both directions | Block affected S1/S5/S8 answers |
| RULE-MVP-03 | Palifico bid progression | Exact legal quantity/face transitions while Palifico is active | Mode-specific transition table | Block affected S1/S5/S9 answers |
| RULE-MVP-04 | Palifico two-player exception | Exact trigger, duration, and effect of the exception in a two-player game | State-transition examples covering entry, active round, and exit | Block exception-specific scoring |
| RULE-MVP-05 | Palifico face-change permission | Exact interaction between the active Palifico round and the persistent `has_declared_palifico` permission | Decision table with player state, current mode, and permitted action | Block ambiguous permission cases |
| RULE-MVP-06 | Palifico activation edge cases | Exact behavior when a player reaches one die at different active-player counts and at round boundaries | State table covering active count at the specified decision point | Use only the already agreed activation condition; block unlisted edge cases |

A missing transition rule must not be reconstructed from a familiar edition, an interface convention, or a likely strategic interpretation.

## 4. Required format for a rules approval

Each approved rule row must contain:

1. stable `rule_id`;
2. ruleset identifier and version;
3. preconditions, including game mode and active-player count where relevant;
4. action or event being evaluated;
5. allowed and prohibited outcomes;
6. boundary examples immediately below, at, and above the relevant limit where meaningful;
7. explicit exceptions and their precedence;
8. at least one positive and one negative example;
9. owner approval and approval date;
10. links to affected exercise examples and acceptance checks.

A rule is not approved merely because its prose has been drafted. The transition table and boundary examples must agree.

## 5. Exercise release gate

Before an exercise receives `APPROVED`:

- all rules it relies on map to approved rows in this matrix or to an existing authoritative contract;
- the example stores the applicable ruleset version;
- the expected answer can be derived without hidden values unavailable to the learner;
- exceptions are represented in the public scenario when they affect the answer;
- the evaluator does not silently infer an omitted rule.

If a rule is unresolved, mark only the affected exercise variant `BLOCKED_BY_RULE_DECISION`. Do not block unrelated exercises that rely exclusively on agreed rules.

## 6. Approval record

| Decision ID | Owner | Status (`OPEN` / `APPROVED` / `REJECTED`) | Approved ruleset version | Evidence / review reference | Date |
|---|---|---|---|---|---|
| RULE-MVP-01 | TBD | OPEN | — | — | — |
| RULE-MVP-02 | TBD | OPEN | — | — | — |
| RULE-MVP-03 | TBD | OPEN | — | — | — |
| RULE-MVP-04 | TBD | OPEN | — | — | — |
| RULE-MVP-05 | TBD | OPEN | — | — | — |
| RULE-MVP-06 | TBD | OPEN | — | — | — |

This document is a review matrix, not a substitute for the authoritative Game Domain contract. Once approved, decisions must be transferred to that contract and versioned there.
