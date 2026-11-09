# Финальный продуктовый аудит и handoff S1–S9

**Статус:** handoff checklist; не является sign-off и не подтверждает готовность реализации.

## 1. Вывод аудита

Текущая спецификация описывает связную модель обучения S1–S9 и достаточно подробно задаёт разделение домена игры, навыков, упражнений, evaluation, feedback, progress, strategy и presentation. Однако наличие документов не означает, что MVP готов к реализации или приёмке.

Критические условия до release:

1. закрыть или исключить из scored scope неутверждённые переходы ставок и Palifico cases;
2. проверить эталонные примеры независимыми review records;
3. подтвердить отсутствие скрытых данных в learner-facing payload;
4. независимо проверить математические эталоны S4;
5. утвердить evaluation, mastery/review и recommendation policies;
6. выполнить поведенческие UX и end-to-end проверки;
7. зафиксировать фактические результаты и owner sign-off.

## 2. Пакет передачи

| Область | Authoritative / primary docs | Handoff evidence |
|---|---|---|
| Game rules | `docs/contracts/game-domain.md`, ruleset decisions | Approved ruleset, transition tables, Palifico boundary cases |
| Skill boundaries | `docs/contracts/s1-*` … `s9-*` и skill contracts | Boundary review, prerequisite map |
| Exercise scope | `docs/learning/mvp-exercise-scope.md`, `skill-exercise-map.md` | Approved example IDs and fixtures |
| Example validation | `docs/learning/examples/mvp-example-validation-plan.md`, review register | Completed per-example review records |
| Evaluation | `answer-evaluation-rubric.md` | Accepted alternatives, negative tests, evaluator version |
| Feedback | Learning core and feedback contracts | Hint/reveal/correction behavior evidence |
| Progress | `mastery-and-review-policy.md` and progress contracts | Independent evidence, status transition and recovery tests |
| Strategy | `learning-strategy-selection-policy.md` | Priority, override, no-eligible and resume tests |
| UX | `learning-ux-acceptance.md`, `learning-ux-scenarios.md` | Desktop/mobile walkthrough, keyboard/focus and error-state evidence |
| Source references | `review/source-material-and-prototype-review.md` | Explicit disposition of HTML and screenshot references |
| Cross-document audit | `review/cross-document-consistency-matrix.md` | Closed invariants or owned open findings |

## 3. Release gate checklist

### G1 — Game Domain

- [ ] Ruleset edition and scope are recorded.
- [ ] All scored bid transitions reference approved rows.
- [ ] Palifico entry, duration, exceptions and persistent rights are explicit.
- [ ] Boundary cases for 2, 3 and more active players are reviewed.
- [ ] Any deferred rule-dependent exercise is excluded from scored MVP.

### G2 — S1–S9 coverage

- [ ] Each included skill has an approved MVP exercise family and at least one approved example.
- [ ] Primary skill and secondary evidence are explicit.
- [ ] Prerequisite links do not silently block learner choice.
- [ ] S7 evidence strength, S8 objectives and S9 plan rubric are approved.

### G3 — Example and evaluator quality

- [ ] Every included example has a complete validation record.
- [ ] Exact expected answers and accepted alternatives are independently checked.
- [ ] Invalid, boundary, incomplete-data and blocked-rule cases are covered.
- [ ] Expected answers remain private until the defined reveal point.

### G4 — Progress and strategy

- [ ] First/final/assisted/independent outcomes are distinct.
- [ ] Reopening or replaying an instance cannot create new independent evidence.
- [ ] Mastery and review thresholds are approved and versioned.
- [ ] Recommendation does not overwrite evidence or auto-start an exercise.
- [ ] No-eligible and overdue states have defined UX.

### G5 — UX and accessibility

- [ ] One primary action and clear task are present in each exercise state.
- [ ] Feedback distinguishes the primary error from optional detailed explanation.
- [ ] Keyboard navigation, visible focus, labels and error announcements are checked.
- [ ] Mobile layouts preserve task/action/result hierarchy.
- [ ] Static screenshots are not used as substitutes for behavioral evidence.

### G6 — End-to-end, privacy and recovery

- [ ] Hidden dice values are absent from learner-visible data, not merely hidden visually.
- [ ] S2→S3→S4 values remain consistent for each supported mode.
- [ ] S6→S7 uses ordered, available observations only.
- [ ] S8 scoring uses decision-time information and stated objective.
- [ ] S9 plans include a revision trigger and do not promise random outcomes.
- [ ] Interrupted attempts, backup restore and duplicate commands are tested.

### G7 — Handoff sign-off

- [ ] All blockers are closed or explicitly excluded from scope.
- [ ] Deferred items have an owner and a future decision point.
- [ ] Spec, ruleset, exercise and evaluator versions are recorded.
- [ ] Evidence links and actual test outcomes are attached.
- [ ] Product owner and implementation reviewer sign off.

## 4. Open items that must not be silently closed

| ID | Open item | Owner | Required evidence | Release impact |
|---|---|---|---|---|
| H-01 | Bid transition tables | Product owner / rules reviewer | Selected edition and approved table | Blocks affected scored examples |
| H-02 | Palifico edge cases | Product owner / rules reviewer | Approved state/transition table | Blocks affected scored examples |
| H-03 | S7 evidence bands and learner wording | Product owner | Approved rubric and reviewed examples | Blocks S7 mastery claim |
| H-04 | S8 MVP objective set | Product owner | Approved objective/action policy | Blocks affected S8 scored cases |
| H-05 | S9 mandatory rubric by difficulty | Product owner | Approved rubric | Blocks affected S9 mastery claim |
| H-06 | Mastery/review thresholds and scheduling anchor | Product owner | Approved versioned policy | Blocks progress release gate |
| H-07 | Learning Strategy priorities and no-eligible state | Product owner | Approved policy + UX scenario | Blocks recommendation release gate |
| H-08 | Independent S4 numeric verification | Reviewer | Exact arithmetic review record | Blocks affected S4 examples |
| H-09 | Learner payload privacy | Implementation reviewer | Payload inspection and negative test | Blocks release |
| H-10 | UX/accessibility behavior | UX/QA reviewer | Executed desktop/mobile and keyboard checks | Blocks UX gate |

## 5. Sign-off record

| Role | Name | Decision | Scope / version | Date | Evidence |
|---|---|---|---|---|---|
| Product owner | TBD | OPEN | TBD | TBD | TBD |
| Rules reviewer | TBD | OPEN | TBD | TBD | TBD |
| Learning/evaluation reviewer | TBD | OPEN | TBD | TBD | TBD |
| UX/accessibility reviewer | TBD | OPEN | TBD | TBD | TBD |
| Implementation/QA reviewer | TBD | OPEN | TBD | TBD | TBD |

## 6. Definition of done for this documentation stage

Документационный этап можно считать завершённым, когда:

- матрица согласованности заполнена фактическими review results;
- открытые решения имеют явный статус и владельца;
- все изменения нормативных контрактов внесены отдельными reviewable patches;
- MVP scope не содержит blocked scored exercises;
- handoff пакет подписан владельцем продукта.

До выполнения этих условий корректный статус продукта — **SPECIFICATION IN REVIEW / NOT SIGNED OFF**. Реальные тесты реализации не заявляются и не подразумеваются данным документом.

## 9. Дополнительный gate: подтверждение переходов ставок и Palifico

До handoff требуется закрыть пакет `ruleset/ruleset-owner-decision-packet.md`:

- [ ] Владелец подтвердил целевую редакцию Zygomatic 2022.
- [ ] Решения BID-01…BID-04 имеют `APPROVE`, `CHANGE` или `DEFER` с reviewer/date.
- [ ] Решения PAL-01…PAL-07 имеют `APPROVE`, `CHANGE` или `DEFER` с reviewer/date.
- [ ] Проектные нормализации явно отделены от формулировок источника.
- [ ] Затронутые примеры просмотрены по `ruleset/affected-exercise-impact-map.md`.
- [ ] Тест-векторы `ruleset/ruleset-transition-test-vectors.md` перенесены в реализационные тесты.
- [ ] Ни один заблокированный переход не используется как scored mastery evidence.

Найденный первичный источник повышает доказательную базу документации, но не является свидетельством реализации или прохождения тестов.
