# Normative transition test vectors — ruleset 2.0.0

**Status:** PLANNED implementation tests. These vectors are derived from `docs/contracts/game-domain.md`; no implementation tests have been run.
**Ruleset:** `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`.

## 1. Test conventions

- `B(q,f)` — bid quantity `q` on face `f` (`1` is Paco).
- `P(p)` — shorthand for `B(p,1)` when discussing NORMAL Paco transitions only.
- `N` — NORMAL; `PAL` — PALIFICO.
- Unless a vector says otherwise, the active-dice upper bound is high enough for both bids.
- Expected result concerns bid-transition legality only; common shape/domain/upper-bound validation is separately tested.

## 2. Common validation and NORMAL opening

| ID | Mode | Previous | Candidate | Expected | Reason |
|---|---|---|---|---|---|
| BID-C-001 | N | none | `B(1,1)` | ACCEPT | ordinary opening on Paco face is a face-1 bid, not a Paco conversion |
| BID-C-002 | N | none | `B(1,6)` | ACCEPT | any face may open |
| BID-C-003 | N | none | `P(1)` | REJECT | Paco opening prohibited in NORMAL |
| BID-C-004 | PAL | none | `B(1,1)` | ACCEPT | Palifico opening permitted |
| BID-C-005 | PAL | none | `B(1,6)` | ACCEPT | Palifico opening permitted |
| BID-C-006 | N | none | `B(0,4)` | REJECT | quantity must be positive |
| BID-C-007 | N | none | `B(1,7)` | REJECT | face outside 1..6 |
| BID-C-008 | N | none | `B(total_active_dice+1,4)` | REJECT | project active-dice upper bound |

## 3. NORMAL ordinary-to-ordinary

| ID | Previous | Candidate | Expected | Boundary/rationale |
|---|---|---|---|---|
| BID-N-001 | `B(5,2)` | `B(5,3)` | ACCEPT | same quantity, higher face |
| BID-N-002 | `B(5,5)` | `B(6,2)` | ACCEPT | quantity increases; any face |
| BID-N-003 | `B(5,4)` | `B(6,1)` | ACCEPT | quantity increases; face 1 is included |
| BID-N-004 | `B(5,4)` | `B(5,4)` | REJECT | identical bid |
| BID-N-005 | `B(5,4)` | `B(5,3)` | REJECT | same quantity, lower face |
| BID-N-006 | `B(5,4)` | `B(4,6)` | REJECT | lower quantity cannot be compensated by face |
| BID-N-007 | `B(5,6)` | `B(5,1)` | REJECT | no higher face exists at equal quantity |

## 4. NORMAL ordinary-to-Paco

| ID | Previous | Candidate | Expected | Calculation |
|---|---|---|---|---|
| BID-P-001 | `B(7,4)` | `P(4)` | ACCEPT | `ceil(7/2)=4` exact threshold |
| BID-P-002 | `B(7,4)` | `P(3)` | REJECT | one below threshold |
| BID-P-003 | `B(8,6)` | `P(4)` | ACCEPT | exact half |
| BID-P-004 | `B(8,6)` | `P(3)` | REJECT | one below exact half |
| BID-P-005 | `B(1,6)` | `P(1)` | ACCEPT | minimum positive Paco quantity |

## 5. NORMAL Paco-to-Paco and Paco-to-ordinary

| ID | Previous | Candidate | Expected | Calculation |
|---|---|---|---|---|
| BID-P-006 | `P(4)` | `P(5)` | ACCEPT | Paco quantity increases |
| BID-P-007 | `P(4)` | `P(4)` | REJECT | no increase |
| BID-P-008 | `P(4)` | `P(3)` | REJECT | quantity decreases |
| BID-P-009 | `P(4)` | `B(9,2)` | ACCEPT | `2*4+1=9` exact threshold |
| BID-P-010 | `P(4)` | `B(8,6)` | REJECT | one below threshold |
| BID-P-011 | `P(4)` | `B(9,6)` | ACCEPT | threshold met; any ordinary face |
| BID-P-012 | `P(4)` | `B(10,1)` | ACCEPT | threshold met; face 1 is ordinary face notation in candidate |

## 6. PALIFICO progression

| ID | Bidder previously declared PAL | Previous | Candidate | Expected | Rationale |
|---|---|---|---|---|---|
| PAL-B-001 | false | `B(4,3)` | `B(5,3)` | ACCEPT | same face, higher quantity |
| PAL-B-002 | false | `B(4,3)` | `B(4,4)` | REJECT | face change prohibited |
| PAL-B-003 | false | `B(4,3)` | `B(5,2)` | REJECT | face change prohibited even with quantity increase |
| PAL-B-004 | true | `B(4,3)` | `B(4,4)` | ACCEPT | same quantity, higher face |
| PAL-B-005 | true | `B(4,3)` | `B(5,2)` | ACCEPT | higher quantity, any face |
| PAL-B-006 | true | `B(4,3)` | `B(4,2)` | REJECT | same quantity, lower face |
| PAL-B-007 | true | `B(4,3)` | `B(3,6)` | REJECT | lower quantity |
| PAL-B-008 | true | `B(4,6)` | `B(4,1)` | REJECT | same quantity, no higher face |
| PAL-B-009 | false | `B(4,1)` | `B(5,1)` | ACCEPT | Paco is an ordinary fixed face in PAL |
| PAL-B-010 | false | `B(4,1)` | `B(5,2)` | REJECT | face change prohibited |

These PAL progression vectors validate the explicit project interpretation in Game Domain §5.3. They must not be described as a complete verbatim rulebook table.

## 7. PALIFICO counting mode

| ID | Mode | Bid face | Own dice | Expected own matches | Rule |
|---|---|---:|---|---:|---|
| PAL-CNT-001 | PAL | 3 | `[3,1,1,4,6]` | 1 | Paco does not augment face 3 |
| PAL-CNT-002 | PAL | 1 | `[3,1,1,4,6]` | 2 | count Paco only |
| PAL-CNT-003 | N | 3 | `[3,1,1,4,6]` | 3 | Paco augments ordinary face |
| PAL-CNT-004 | N | 1 | `[3,1,1,4,6]` | 2 | face 1 counts ones only |

## 8. Activation, duration and two-player exception

| ID | State after atomic Dudo resolution | Expected next round | Starter | Rule |
|---|---|---|---|---|
| PAL-A-001 | loser 2→1; 4 active players remain | PAL | loser | qualifying loss, 3+ active |
| PAL-A-002 | loser 2→1; exactly 3 active players remain | PAL | loser | boundary above two |
| PAL-A-003 | loser 2→1; exactly 2 active players remain | NORMAL | normal starter rule | two-player exception |
| PAL-A-004 | loser 3→2; 3+ active remain | NORMAL | normal starter rule | no fourth-die loss |
| PAL-A-005 | PAL round completes; 3+ active remain; no new trigger | NORMAL | normal starter rule | PAL lasts one round |
| PAL-A-006 | loser 2→1; only one active player remains | no next round; game ends | — | terminal state has precedence |
| PAL-A-007 | player has `has_declared_palifico=true`, current dice count >1 | flag remains true | — | persistent historical permission |
| PAL-A-008 | player reaches one die but did not actually declare PAL | flag remains false | — | do not infer history from dice count |

## 9. State mutation and event properties

1. Every rejected candidate leaves state, event log and current bid unchanged.
2. Every accepted candidate emits exactly one `BID_PLACED`.
3. `BID_PLACED` does not itself advance the turn.
4. `TURN_ADVANCED` advances exactly once and is not emitted by bid validation.
5. Dudo resolution, die loss and elimination are atomic.
6. The active-player checkpoint for PAL is evaluated only after that atomic transition and before next-round creation.
7. Duplicate `command_id` with identical payload returns the prior result; different payload conflicts.

## 10. Test execution record

All vectors above are `PLANNED`. A future execution record must include implementation commit, test command, runtime, date, result, and reviewer. Documentation consistency checks do not count as implementation test execution.
