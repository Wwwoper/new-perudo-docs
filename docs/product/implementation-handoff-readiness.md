# Implementation handoff readiness

**Статус:** подготовительный документ; не является разрешением начинать реализацию и не означает, что release gates пройдены.

## 1. Условия передачи

| Gate | Требуемое доказательство | Текущий статус |
|---|---|---|
| H-01 Ruleset | утверждённый первичный источник и закрытые BID/PAL решения либо документированный narrow scope | PASS — documentation decision; implementation vectors PLANNED |
| H-02 Exercise content | каждый MVP example имеет review record и статус APPROVED | NOT_VERIFIED |
| H-03 S4 math | точные эталоны и тест-векторы независимо пересчитаны | PARTIAL — арифметика шести примеров проверена; реализация нет |
| H-04 Evaluation | рубрика, accepted alternatives и optional criteria утверждены | NOT_VERIFIED |
| H-05 Progress | mastery/review пороги и миграция исторических evidence утверждены | NOT_VERIFIED |
| H-06 UX | критические UX-сценарии и accessibility checks выполнены | NOT_RUN |
| H-07 Privacy | публичные DTO/payload не содержат закрытых значений | NOT_VERIFIED |
| H-08 Reliability | сохранение попытки, идемпотентность и восстановление проверены | NOT_VERIFIED |
| H-09 Scope | deferred items исключены из MVP acceptance и рекомендаций | PARTIAL |
| H-10 Ownership | у каждого blocker есть владелец и дата решения | NOT_VERIFIED |

## 2. Что можно передать как вход в техническое проектирование

- согласованные доменные и учебные границы;
- DTO и event contracts в пределах их текущего статуса;
- каталог planned tests и S4 exact test vectors;
- UX session scripts;
- реестр открытых решений и явные release blockers.

Передача этих материалов не разрешает реализовать заблокированные правила как будто они утверждены.

## 3. Что нельзя объявлять готовым

- все 41 примера: 40 `READY_FOR_REVIEW`, один `BLOCKED_BY_RULE_DECISION` — это не 40 approved examples;
- полную механику ставок и Palifico;
- реализацию probability engine;
- локальное хранение, backup/restore, безопасность и privacy;
- accessibility или usability;
- mastery thresholds и adaptive strategy.

## 4. Sign-off

| Role | Name | Decision | Date | Evidence / comments |
|---|---|---|---|---|
| Product owner | TBD | OPEN | TBD | TBD |
| Rules reviewer | TBD | OPEN | TBD | TBD |
| Learning reviewer | TBD | OPEN | TBD | TBD |
| UX reviewer | TBD | OPEN | TBD | TBD |
| Technical reviewer | TBD | OPEN | TBD | TBD |

## 5. Разрешение на следующий этап

После утверждения ruleset 2.0.0 и текущих границ MVP техническое проектирование продуктового scope разрешено. Это не разрешение на выпуск. Skill-level contract inventory теперь присутствует в `docs/contracts/README.md`; фиксация детальных API/DTO требует отдельной traceability-проверки и не должна вводить новые продуктовые решения. Реализация scored exercise content дополнительно требует `H-02`, `H-04` и `H-05`. Остальные gates остаются release acceptance criteria и должны быть проверены на работающем продукте.

Сводная сверка и карта нормативных контрактов: `docs/product/review/mvp-final-scope-and-handoff.md`.
