# Реестр решений и назначений для реализации

**Статус:** OPEN REGISTER. Это рабочий реестр; он не заменяет решения владельца продукта и не назначает людей автоматически.

| ID | Решение / назначение | Владелец | Reviewer | Статус | Требуемое evidence | Gate / влияние |
|---|---|---|---|---|---|---|
| DEC-01 | Выбрать browser runtime: TypeScript, Python/WASM или обоснованный hybrid | TBD | Technical reviewer TBD | OPEN | ADR-013, сравнительный spike на одинаковом вертикальном срезе | I-1; влияет на I-2/I-5 |
| DEC-02 | Подтвердить текущие project interpretations PAL-04/05/06 | Product owner TBD | Rules reviewer TBD | APPROVED IN OWNER RECORD; INDEPENDENT REVIEW OPEN | явное подтверждение owner record и review результата | H-01 / rules fixtures |
| DEC-03 | Утвердить набор scored MVP exercise fixtures | Product owner TBD | Learning reviewer TBD | OPEN | fixture-level review record, expected answer, accepted alternatives, ruleset/generator version | H-02; блокирует scored content |
| DEC-04 | Утвердить evaluation rubric и optional/partial criteria | Learning owner TBD | Learning reviewer TBD | OPEN | versioned rubric и fixtures | H-04; блокирует scored content |
| DEC-05 | Утвердить mastery/review thresholds и anchor-time semantics | Product owner TBD | Learning reviewer TBD | OPEN | calibration plan, approved thresholds, migration policy | H-05; блокирует mastery/review release |
| DEC-06 | Подтвердить privacy warning и границы `TRUSTED_LOCAL` | Product owner TBD | UX/Technical reviewer TBD | OPEN | approved UI copy, threat-model note, privacy acceptance | H-07 / UX |
| DEC-07 | Назначить владельцев и даты для H-02/H-04/H-05/H-10 | Product owner TBD | — | OPEN | имена, сроки, ссылки на решения | H-10 |
| DEC-08 | Утвердить browser support/offline/storage/backup matrix | Technical owner TBD | Technical reviewer TBD | OPEN | browser matrix, persistence and backup test evidence | H-06/H-08/I-5 |
| DEC-09 | Сверить crosswalk идентификаторов BID/PAL между всеми реестрами | Rules owner TBD | Rules reviewer TBD | OPEN | inventory всех ID и единый crosswalk без перенумерации по догадке | H-01 |
| DEC-10 | Подтвердить окончательный release scope и трактовку локальной партии в MVP | Product owner TBD | Product/Technical reviewers TBD | OPEN | явный scope sign-off и обновлённая traceability | I-0 / I-6 |

## Правила ведения

- `TBD` — отсутствие назначения, не фиктивное имя.
- `OPEN` и `NOT_VERIFIED` не являются разрешением на реализацию/релиз.
- Решение владельца продукта и независимая техническая/предметная проверка — разные evidence.
- Гипотезы о mastery, интервалы 1/3/7/14/30 дней и критерий «3 успешные первые отправки на 3 экземплярах в 2 сессиях» не утверждаются этим реестром.
- При изменении решения фиксировать дату, автора, reviewer, rationale, версию затронутого контракта и migration impact.
