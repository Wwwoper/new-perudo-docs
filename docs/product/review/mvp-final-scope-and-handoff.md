# Финальная сверка MVP и пакет передачи в техническое проектирование

**Статус:** DOCUMENTATION HANDOFF READY WITH OPEN IMPLEMENTATION GATES.
**Дата сверки:** 2026-11-09.
**Ruleset:** `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`.

## 1. Итог

Продуктовая спецификация MVP S1–S9 в целом согласована на уровне целей, границ навыков, жизненного цикла упражнений, evaluation/feedback/progress/strategy, локального профиля и UX-принципов. BID/PAL правила теперь закрыты нормативным контрактом и решением владельца продукта. Это позволяет передать утверждённый продуктовый scope в техническое проектирование.

Это **не** sign-off реализации и **не** утверждение, что все упражнения, evaluator, UX, persistence, privacy или accessibility прошли проверку.

## 2. Зафиксированный MVP scope

### Включено

- Web desktop/mobile; PWA — дополнительный способ установки.
- Browser-only local-first продукт без обязательной регистрации, backend и cloud sync.
- Один локальный профиль на browser profile.
- Сохранение прогресса и попыток между запусками; IndexedDB остаётся рекомендацией хранения, а не принятым технологическим решением.
- Offline после первого успешного открытия.
- Ручной export/import backup до публичного релиза.
- Один пользовательский учебный поток с навыками S1–S9.
- Ruleset `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`, Calza выключено.
- Локальная проверка `TRUSTED_LOCAL` с явным предупреждением, что эталоны доступны владельцу устройства.
- Прозрачная rule-based Learning Strategy; без ML.
- Сохранение каждой отправки, first/final/independent accuracy, hints и corrections раздельно.

### Не входит

- Network multiplayer, social features, аккаунты, серверная синхронизация и облачный backup.
- Calza и альтернативные редакции правил.
- ML-персонализация.
- Использование непроверенных исторических партий пользователя как обучающего evidence без явного consent/data policy.
- Автоматическое превращение сценарных preset в игровые режимы.

## 3. Ruleset decision closure

BID-01…BID-04 и PAL-01…PAL-07 утверждены; нормативные правила и проектные нормализации находятся в `docs/contracts/game-domain.md` и `docs/product/review/ruleset/ruleset-owner-decision-record.md`.

Особо важные distinctions:

- В NORMAL face 1 — Paco и подчиняется специальным переходам; обычные грани для ordinary-to-ordinary — 2..6.
- В PALIFICO Paco — не wild и трактуется как обычная грань для PAL progression.
- Историческое право менять грань сохраняется через `has_declared_palifico`; количественная лестница для этого права — проектная интерпретация.
- Двухигровое исключение проверяется после атомарного Dudo и изменения активного состава, перед созданием следующего раунда.

## 4. Состояние S1–S9 и эталонного контента

| Область | Итог | Следующий обязательный шаг |
|---|---|---|
| S1 Rules | Domain rules approved | Review fixtures S1-E01…E07 и boundary tests |
| S2 Perception | Product boundary documented | Verify public DTO/payload and responsive behavior |
| S3 Counting | NORMAL/PAL semantics defined | Execute mode-specific fixtures, including S3-E05 |
| S4 Probability | Exact Fraction contract and independent math review partly documented | Independently verify all fixture answers; implement exact arithmetic tests |
| S5 Bid mechanics | Ruleset 2.0.0 transitions approved | Execute transition vectors and review S5-E01…E05 |
| S6 History | Event semantics documented | Verify complete/incomplete event sequences and S6-E06 |
| S7 Opponent analysis | Evidence/uncertainty principles documented | Approve evidence bands and data provenance/consent policy |
| S8 Decision quality | Decision-time evidence principle documented | Review legal action sets and objectives, including S8-E06 |
| S9 Match strategy | Conditional plan principle documented | Review Palifico branch and trigger behavior, including S9-E06 |

Existing examples are not automatically approved. Original 40 examples were `READY_FOR_REVIEW` and one blocked; S5-E03 is now `READY_FOR_REVIEW` after ruleset closure. Nine additional ruleset 2.0.0 fixtures are also `READY_FOR_REVIEW`. No claim of `APPROVED` is made by this handoff.

## 5. UX and prototype findings incorporated

The supplied static screenshots support the proposed structure for probability decisions, bid history, Paco rule learning, opponent-hand prediction and repeated probabilistic decisions. They also expose important scope boundaries:

- scenario presets must not be confused with actual `round_mode`;
- thresholds 0.40/0.50/0.55 are exercise parameters, not universal strategy rules;
- a rounded expected value is not a guaranteed probability threshold;
- ten observations or ten repeated decisions are not automatically mastery evidence;
- historical-game analysis needs explicit data provenance and consent.

See `docs/product/review/source-material-and-prototype-review.md` and `docs/product/learning-ux-acceptance.md`.

## 6. Handoff package inventory and gaps

The current documentation checkout contains the product, learning, review and ruleset documents listed below. `docs/contracts/game-domain.md` has been added as the authoritative Game Domain contract. The other skill-level contract files referenced by older handoff tables are not present in this checkout and must be supplied or recreated before implementation-level interface design is considered complete.

| Artifact | Status |
|---|---|
| Game Domain ruleset 2.0.0 | PRESENT / APPROVED |
| BID/PAL decision record | PRESENT / APPROVED |
| Ruleset transition vectors | PRESENT / PLANNED, not executed |
| S1–S9 example catalog and new ruleset fixtures | PRESENT / REVIEW REQUIRED |
| S1–S9 validation test catalog | PRESENT / PLANNED |
| S4 independent math audit | PRESENT / PARTIAL |
| Evaluation rubric | PRESENT / REVIEW REQUIRED |
| Mastery/review and learning strategy policy | PRESENT / REVIEW REQUIRED |
| UX acceptance and session scripts | PRESENT / NOT EXECUTED |
| Cross-document consistency matrix | PRESENT / requires final reviewer pass |
| Skill-level contracts S1–S9 and shared learning-core contracts | NOT PRESENT IN CURRENT CHECKOUT |
| Technical package/API/storage decisions | INTENTIONALLY NOT SELECTED |

## 7. Gates before implementation/release

| Gate | Status | Why |
|---|---|---|
| H-01 Ruleset BID/PAL | PASS — documentation decision | Owner decision and normative contract exist; implementation vectors remain unexecuted |
| H-02 Exercise content | OPEN | Individual fixture review and accepted alternatives incomplete |
| H-03 S4 math | PARTIAL | Six prior arithmetic results reviewed; all new fixtures and implementation remain unverified |
| H-04 Evaluation | OPEN | Rubric/alternatives require owner and reviewer sign-off |
| H-05 Progress/mastery | OPEN | Thresholds, scheduling anchor and historical evidence policy require approval |
| H-06 UX/accessibility | NOT RUN | Static images are not behavioral or accessibility evidence |
| H-07 Privacy | OPEN | Payload inspection, local profile warning and historical data consent need verification |
| H-08 Reliability/recovery | NOT RUN | Persistence, idempotency, backup/restore and crash recovery need tests |
| H-09 Scope | PASS — documentation | Included and deferred features are stated here |
| H-10 Ownership/sign-off | OPEN | Named reviewers and dated review records remain absent |

## 8. Technical-design authorization

**Authorized now:** technical design may begin against the approved product scope and Game Domain 2.0.0 contract. The design must preserve the stated module boundaries, public/private data split, local-first constraints and versioning rules.

**Not authorized by this document:** declaring implementation ready, approving all scored exercise content, closing privacy/accessibility/reliability gates, or beginning release acceptance. Missing skill-level contracts must be reconciled before detailed API/DTO design is frozen.

The technical design must not silently choose a programming language, framework, database, hosting provider or physical package layout as if those were product decisions. Such choices belong to a separately requested technical-design stage.

## 9. Required handoff outputs

1. Technical design traceability matrix from each product requirement to module/API/storage responsibility.
2. Explicit list of missing S1–S9 and Learning Core contracts, with owners and resolution plan.
3. Ruleset 2.0.0 transition vectors mapped to future implementation tests.
4. Example review register with individual decisions and accepted alternatives.
5. Data classification and privacy plan for local trusted evaluation and user-supplied history.
6. UX interaction specification for desktop/mobile, keyboard and assistive technology.
7. Persistence, backup/restore, idempotency and recovery design.
8. Deferred-scope register and release-gate evidence plan.

## 10. Final status

**Product rules decision:** APPROVED.
**Product documentation handoff:** READY WITH OPEN GATES.
**Implementation readiness:** NOT ESTABLISHED.
**Release readiness:** NOT ESTABLISHED.
**Implementation tests executed:** NONE CLAIMED.
