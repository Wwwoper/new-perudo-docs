# Финальный MVP implementation handoff

**Статус:** PRODUCT AND CONTRACT SPECIFICATION BASELINE COMPLETE; IMPLEMENTATION / RELEASE GATES OPEN.
**Дата:** 2026-11-09.
**Ruleset:** `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`.
**Назначение:** единая точка входа для реализации MVP. При расхождении кратких исторических handoff-заметок приоритет имеют актуальные нормативные контракты и этот документ; этот документ не переписывает их.

## 1. Решение о готовности

Продуктовый scope и нормативная документация достаточны, чтобы начать реализацию MVP без дальнейшего расширения продуктовой модели. Не требуется ещё один цикл архитектурных абстракций.

Это решение означает:

- требования MVP и их владельцы сведены в `review/mvp-traceability-matrix.md`;
- доменные и skill-level контракты S1–S9, Learning Core, data contracts и end-to-end learning loop доступны в `docs/contracts/` и `docs/architecture/contracts/`;
- MVP scope, правила, UX-сценарии, exercise catalog, review policies и acceptance criteria заданы;
- оставшиеся вопросы относятся преимущественно к owner/reviewer approval и проверке реализации, а не к отсутствию базовой архитектурной модели.

Это **не** означает, что реализация существует, тесты выполнены или релиз разрешён. До получения фактических результатов все implementation/release gates остаются открытыми.

## 2. Зафиксированный MVP — не расширять без отдельного решения

### Включено

- Web-приложение для desktop и mobile; PWA — необязательный способ установки.
- Browser-only local-first, один локальный профиль на browser profile.
- Нет обязательной регистрации, backend, cloud sync, network multiplayer или social features.
- Сохранение упражнений, попыток, ответов и прогресса между запусками.
- Offline после первого успешного открытия.
- Ручной backup export/import до публичного релиза.
- Учебный продукт S1–S9 и один сквозной learning loop.
- `PERUDO_CLASSIC_ZYGOMATIC_2022@2.0.0`; Calza выключено.
- Локальная проверка `TRUSTED_LOCAL` с понятным предупреждением о доступности эталонов владельцу устройства.
- Прозрачная rule-based Learning Strategy; без ML.

### Исключено

- Аккаунты, серверная синхронизация, облачный backup, multiplayer, социальные функции.
- Calza и альтернативные ruleset.
- ML-персонализация.
- Автоматическое использование пользовательских исторических партий без отдельной политики consent/provenance.
- Превращение демонстрационных preset в новые игровые режимы.

Не добавлять функции в MVP «по пути». Любое расширение требует явного scope decision и обновления traceability/acceptance.

## 3. Нормативные документы — источник истины

| Область | Источник истины |
|---|---|
| Общие правила игры | `docs/contracts/game-domain.md` |
| Навыки | `docs/contracts/s1-rules-and-mechanics.md` … `s9-match-strategy.md`; общий индекс — `docs/contracts/README.md` |
| Learning Core | `docs/contracts/learning-core.md` |
| DTO, public/private boundary, events, versioning | `docs/architecture/contracts/data-contracts.md` |
| Сквозной цикл | `docs/architecture/contracts/learning-loop-contract.md` |
| Требования и ownership | `docs/product/review/mvp-traceability-matrix.md` |
| MVP scope | `docs/product/review/mvp-final-scope-and-handoff.md` и данный handoff |
| Упражнения | `docs/learning/exercise-catalog.md`, `mvp-exercise-scope.md`, `skill-exercise-map.md` и `learning/examples/` |
| Evaluation / progress / strategy | `answer-evaluation-rubric.md`, `mastery-and-review-policy.md`, `learning-strategy-selection-policy.md` |
| UX и acceptance | `docs/product/learning-ux-acceptance.md`, `learning-ux-scenarios.md`, `mvp-acceptance-criteria.md` |
| Ruleset evidence | `docs/product/review/ruleset/` и `ruleset-owner-decision-record.md` |

Старые документы могут содержать исторические статусы или формулировки «контракт отсутствует» на дату их создания. После появления соответствующего нормативного файла такие записи трактуются как исторические; они не отменяют новый контракт. Изменения нормативных документов выполняются отдельным reviewable patch.

## 4. Минимальный порядок реализации

Рекомендуемый порядок — вертикальные этапы с работающим результатом на каждом шаге, а не разработка всех модулей изолированно.

| Этап | Результат | Обязательные проверки до перехода |
|---|---|---|
| I-0 Contract freeze | Зафиксированы версии ruleset, contracts, generator/evaluator и MVP fixture set | Traceability review; open owner decisions assigned; deferred scope excluded |
| I-1 Domain foundation | Game Domain, правила ставок, Palifico, counting/probability primitives | Ruleset transition vectors; S3/S4 exact vectors; invalid-command no-mutation; active-player boundaries |
| I-2 Exercise vertical slice | Один полностью работающий тип упражнения: generate → persist → public view → attempt → answer submission | Deterministic fixture; public/private serialization negative tests; reload/resume; duplicate submission behavior |
| I-3 Evaluation and feedback | Версионированная оценка, accepted alternatives, primary feedback, controlled reveal | Rubric fixtures; optional criteria; correction history; no early answer reveal |
| I-4 Progress and strategy | Независимое evidence, mastery/review transitions, next recommendation | Threshold approval; repeat/replay protection; deterministic strategy fixtures; no-eligible/resume UX |
| I-5 Product shell | Local profile, responsive UI, persistence, offline behavior, backup/restore | Fresh profile; browser restart; offline smoke; backup round-trip and invalid/interrupted import |
| I-6 Release validation | Все включённые навыки и сценарии собраны в продукт; QA evidence и sign-off | Все release gates закрыты; accessibility/privacy/reliability/UX results recorded |

После I-2 следует расширять продукт по одному skill/exercise family, используя один и тот же learning loop. Не создавать отдельную реализацию попыток, feedback или progress для каждого навыка.

## 5. Решения, которые нельзя принимать молча

Следующие пункты требуют явного владельца и записи результата. До решения соответствующие возможности/примеры должны быть исключены из scored release scope либо использовать только уже утверждённое поведение.

| Тема | Требуемое решение / доказательство | Влияние |
|---|---|---|
| Exercise examples | Персональный review record для каждого включённого fixture, точный expected answer и accepted alternatives | Не утверждать `READY_FOR_REVIEW` как `APPROVED` |
| Evaluation rubric | Утверждение рубрики, partial/optional criteria и evaluator version | Блокирует scored exercises без принятой рубрики |
| Mastery/review | Утверждение порогов, интервалов, anchor time, overdue/grace и миграции исторических evidence | Блокирует mastery/review release claims |
| S7 | Evidence bands, provenance и consent wording | Не делать выводы о мотивах; неподтверждённые наблюдения не использовать как факты |
| S8 | MVP objectives и правила допустимых альтернатив | Не оценивать решение по hindsight или случайному исходу |
| S9 | Обязательность rubric dimensions по сложности | Не заявлять mastery на неполном критерии |
| UX/accessibility | Фактические desktop/mobile/keyboard/screen-reader проверки | Статическая документация не является UX sign-off |
| Privacy | Проверка сериализуемых payload, локального предупреждения и backup contents | Release blocker при утечке private expected data |
| Reliability | Persistence, transaction, idempotency, crash recovery, migration, backup/restore tests | Release blocker при потере/дублировании данных |

BID/PAL продуктовые решения утверждены для ruleset 2.0.0. Это закрывает неопределённость правил, но не заменяет исполнение transition vectors в реализации.

## 6. Release gates и критерий готовности

Статусы фиксируются по фактическим evidence, не по наличию документа.

- `SPECIFIED` — контракт утверждён как нормативный.
- `FIXTURED` — есть детерминированные acceptance fixtures.
- `REVIEWED` — reviewer одобрил fixtures и accepted alternatives.
- `IMPLEMENTED` — код существует, указан commit/build.
- `EXECUTED` — тест реально запускался, указан run/result.
- `RELEASE_ACCEPTED` — gate подписан ответственным reviewer.

Публичный релиз запрещён, пока не закрыты: ruleset transition implementation, все включённые scored examples, evaluator, progress/strategy policy, privacy, reliability/recovery, UX/accessibility и owner sign-off. `PLANNED`, `NOT_RUN`, `PARTIAL`, `READY_FOR_REVIEW` не эквивалентны `PASS`.

Минимальный release evidence bundle:

1. Commit/build идентификатор и воспроизводимая инструкция запуска.
2. Автоматизированные результаты domain/ruleset/counting/probability tests.
3. Review register по каждому включённому exercise fixture.
4. Evaluation/progress/strategy test results с версиями контрактов.
5. Public/private payload inspection и privacy negative tests.
6. Persistence/recovery/idempotency/backup migration results.
7. Desktop/mobile/keyboard/accessibility UX results.
8. Заполненные product, rules, learning, UX и implementation/QA sign-offs.

## 7. Технологические решения — отдельная задача

Данный handoff не выбирает язык, framework, database, hosting, package layout или конкретную browser storage library. Это намеренное разделение: реализация должна удовлетворять контрактам, а не подменять продуктовые решения выбранным инструментом. Технологический design proposal можно подготовить отдельно после выбора владельцем/техническим ответственным; все варианты должны пройти traceability и NFR review.

## 8. Итог

**Документационная продуктовая и контрактная база для начала MVP implementation готова.** Следующий основной поток работы — реализация вертикального среза и закрытие перечисленных owner/reviewer/QA gates. Новые архитектурные документы не являются обязательным предварительным условием для старта.

**Текущий статус релиза:** `NOT RELEASE-READY` до фактического выполнения и подписания gate evidence. Никакие реализационные тесты этим документом не объявляются пройденными.
