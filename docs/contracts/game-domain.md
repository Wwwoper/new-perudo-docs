# Game Domain — Perudo Classic Zygomatic 2022

**Contract status:** normative product contract for MVP ruleset `PERUDO_CLASSIC_ZYGOMATIC_2022`.
**Ruleset version:** `2.0.0`.
**Evidence:** official Zygomatic *Perudo Classic — English Rules*, layout `USN_RULES_2022.indd`, 29/08/2022, pp. 3–6. Source extract: `docs/product/review/ruleset/zygomatic-2022-source-extract.md`.
**Scope note:** source-derived rules and explicit project normalizations are distinguished below. This contract does not claim that a software implementation has passed tests.

## 1. Game baseline

- 2–6 participants; each starts with five dice.
- The ruleset is the 2022 Zygomatic Classic edition.
- Calza is disabled in MVP.
- A player with zero dice is eliminated. Eliminated players remain in historical game state and presentation, but are excluded from active turn order and all active-dice calculations.
- The game ends when one active player remains.
- The player who lost the previous round starts the next round if still active; otherwise the next active player clockwise starts.
- `BID_PLACED` records a bid. `TURN_ADVANCED` advances the turn exactly once; the two events are not interchangeable.

## 2. Bid representation and common validation

A bid is `(quantity, face)`, where `quantity` is a positive integer and `face` is one of `1..6`. Face `1` is called Paco in bid notation. A bid must not exceed the total dice held by active players. This upper bound is a project rule, applied in addition to the source transition rules.

Validation order:

1. validate shape, positive quantity and face domain;
2. validate the project upper bound against active dice;
3. validate whether the bid is permitted as an opening bid;
4. validate the transition from the previous bid under the current round mode and bidder's persistent Palifico permission;
5. only then append `BID_PLACED` and update the current bid.

A rejected bid has no state mutation and emits no bid event.

## 3. NORMAL bid progression

### 3.1 Opening bid

- Any positive quantity and face `1..6` is allowed subject to the active-dice upper bound.
- An opening Paco bid is prohibited.

### 3.2 Ordinary face to ordinary face

Given previous bid `(q, f)`, a candidate `(q2, f2)` is legal iff:

- `q2 == q` and `f2 > f`; or
- `q2 > q` and `f2` may be any face `1..6`.

All other ordinary-to-ordinary transitions are rejected. In particular, a lower quantity is never compensated by a higher face, and an identical bid is not an increase.

### 3.3 Ordinary face to Paco

Given previous ordinary bid quantity `q`, candidate Paco quantity `p` is legal iff:

`p >= ceil(q / 2)`.

The common active-dice upper bound still applies.

### 3.4 Paco to Paco

Given previous Paco quantity `p`, a new Paco quantity `p2` is legal iff `p2 > p`.

### 3.5 Paco to ordinary face

Given previous Paco quantity `p`, a candidate ordinary face `(q, f)` is legal iff:

`q >= 2 * p + 1`.

The candidate may use any face `1..6`, subject to the common upper bound.

## 4. PALIFICO activation and duration

### 4.1 Trigger

A player who loses their fourth die (transition from two dice to one) declares Palifico and becomes the starter of the next round, subject to the two-player exception below.

### 4.2 Project normalization: active-player checkpoint

The active-player count is evaluated after Dudo has been resolved atomically, the loser's die count has been updated, and any player reaching zero dice has been marked eliminated; it is evaluated immediately before creation of the next round.

- If exactly two active players remain, the next round is `NORMAL`.
- If more than two active players remain and the round loser moved from two dice to one, the next round is `PALIFICO` and that player starts it.
- Otherwise the next round is `NORMAL`.

This checkpoint is a project normalization of the source's two-player exception, not a verbatim sequencing instruction from the rulebook. A state with fewer than two active players is terminal and creates no next round.

### 4.3 Duration

`PALIFICO` applies to exactly the next round. On completion of that round, the following round is `NORMAL`, unless a new qualifying trigger occurs under the same rule.

## 5. PALIFICO bid progression

### 5.1 Mode semantics

- Paco is not a wild face in Palifico.
- A bid on a face counts only dice showing that exact face; Paco does not augment any other face.
- An opening Paco bid is permitted in Palifico. This is the source's stated exception to the NORMAL opening restriction.

### 5.2 Bidder without prior Palifico declaration

A bidder whose `has_declared_palifico` is false must keep the face equal to the previous bid's face and strictly increase quantity. A face change is rejected.

### 5.3 Bidder with prior Palifico declaration

A player who has actually declared Palifico earlier in the same game retains the permission to change face in later Palifico rounds. The permission is represented by persistent `has_declared_palifico=true`; it is not inferred from current dice count.

**Project normalization for quantity/face progression:** for this bidder, use the ordinary face-to-face increase relation from §3.2: same quantity with a higher face, or higher quantity with any face. This grants a usable, monotonic legal-bid relation without inventing a separate ranking. The bid remains under PALIFICO counting semantics (Paco is not wild). Do not apply NORMAL Paco conversion formulas (§3.3–3.5) inside PALIFICO; all six faces are treated uniformly for this PALIFICO progression.

The source confirms the permission to change face but does not specify this complete quantitative ordering. The ordering above is an explicit project interpretation, not a source quotation.

## 6. Persistent Palifico history

Set `has_declared_palifico=true` only when the player actually declares Palifico as the qualifying round loser. Once true, it remains true until the game ends. Current dice count, current round mode, and merely reaching one die without a qualifying Palifico declaration do not independently set the flag.

## 7. Dudo and counting modes

- In NORMAL, for a bid on face `2..6`, count that face plus all Paco dice.
- In NORMAL, for a bid on face `1`, count Paco dice only.
- In PALIFICO, count only the exact bid face; Paco is not wild, including for bids on faces `2..6`.
- If the bid is true, the challenger loses one die; otherwise the bidder loses one die.
- Dudo resolution, die loss, elimination, and the resulting round outcome are one atomic domain transition.

## 8. Round and command invariants

- `ROUND_COMPLETED` closes the current round; creating the next round is a separate command.
- Every state-changing command has a `command_id`.
- Repeating the same `command_id` with the same payload returns the prior result; the same ID with a different payload is a conflict.
- State change and corresponding events are persisted atomically.
- Unknown event type, schema version, or payload shape stops replay; it is not silently skipped.
- Ruleset, event schema, and exercise generator versions are independent identifiers.

## 9. Normative transition summary

| Mode | Previous bid | Candidate | Legal condition |
|---|---|---|---|
| NORMAL | none | any face | any `q>=1`, face `1..6`; not Paco opening |
| NORMAL | ordinary `(q,f)` | ordinary `(q2,f2)` | `(q2=q and f2>f) or q2>q` |
| NORMAL | ordinary `(q, f)` | Paco `p` | `p>=ceil(q/2)` |
| NORMAL | Paco `p` | Paco `p2` | `p2>p` |
| NORMAL | Paco `p` | ordinary `(q,f)` | `q>=2p+1` |
| PALIFICO | none | any face | any `q>=1`, face `1..6`, including Paco |
| PALIFICO | any previous | any face; no prior declaration | same face and higher quantity |
| PALIFICO | any previous | any face; prior declaration | same quantity and higher face, or higher quantity and any face |

All rows are additionally subject to positive integer quantity, face domain, and active-dice upper bound.

## 10. Explicitly deferred

- Calza and all optional Calza scoring.
- Multiplayer/network play, server authority, account sync and cloud backup.
- Any rules edition other than Zygomatic 2022.
- Alternative house rules and variants.

## 11. Change control

Any change to §§3–7 requires a ruleset version change, updated transition vectors, impact review of affected examples, and evaluator compatibility review. Existing attempts retain the ruleset/evaluator versions under which they were created; historical progress is not silently re-evaluated.
