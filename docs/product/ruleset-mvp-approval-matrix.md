# MVP Ruleset Approval Matrix

**Status:** APPROVED for ruleset `PERUDO_CLASSIC_ZYGOMATIC_2022` version `2.0.0`. Normative rules are in `docs/contracts/game-domain.md`; decision rationale is in `docs/product/review/ruleset/ruleset-owner-decision-record.md`.

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

## 3. Decision closure

The formerly open BID/PAL decisions are closed for ruleset `2.0.0`:

| Decision family | Approved outcome | Evidence / normative location |
|---|---|---|
| BID-01…BID-04 | NORMAL progression, Paco conversion formulas, opening-bid restriction | `docs/contracts/game-domain.md` §§3–3.5 |
| PAL-01…PAL-03 | Activation, one-round duration, non-wild Paco, historical permission | `docs/contracts/game-domain.md` §§4–6 |
| PAL-04…PAL-06 | Quantitative progression for historical permission, Paco treated as ordinary PAL face, active-player checkpoint | `docs/contracts/game-domain.md` §5 and §4.2; project interpretations are labelled |
| PAL-07 | Persistent `has_declared_palifico` set only on actual declaration | `docs/contracts/game-domain.md` §6 |

No unresolved BID/PAL rule remains in the selected MVP ruleset. The distinction between source-derived rules and project interpretations is mandatory in future changes.

## 4. Scored exercise release gate

A rule being approved does not automatically approve an exercise. Before an exercise receives `APPROVED`:

- all rules it relies on map to the normative Game Domain contract;
- the example stores ruleset version `2.0.0`;
- the expected answer can be derived without hidden values unavailable to the learner;
- exceptions are represented in the public scenario when they affect the answer;
- evaluator vectors cover positive, negative and boundary outcomes;
- a reviewer has recorded the actual review result.

Examples still marked `READY_FOR_REVIEW` or `BLOCKED_BY_RULE_DECISION` do not become approved merely because the rules decision is closed.

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

BID/PAL decisions are recorded as approved in `docs/product/review/ruleset/ruleset-owner-decision-record.md` (2026-11-09). The ruleset is `2.0.0`. Implementation and exercise validation remain separate gates.
