# Learning Policy Integration and Handoff Review

**Status:** integration checklist for product-owner review. It consolidates proposed policy documents without approving their open decisions.

## 1. Policy document map

| Concern | Primary document | Authority boundary |
|---|---|---|
| Rules used by scored MVP exercises | `ruleset-mvp-approval-matrix.md` | Review matrix only; approved rules must be transferred to the authoritative Game Domain contract |
| Answer correctness and feedback | `../learning/answer-evaluation-rubric.md` | Common evaluation policy; skill contracts define domain-specific criteria |
| Mastery and review evidence | `../learning/mastery-and-review-policy.md` | Proposed thresholds; requires product-owner approval |
| Next exercise recommendation | `../learning/learning-strategy-selection-policy.md` | Proposed deterministic selection policy; requires product-owner approval |
| MVP exercise floor | `../learning/mvp-exercise-scope.md` | Defines minimum skill coverage and release exclusions |
| Example review and statuses | `../learning/examples/mvp-example-coverage-and-review.md` | Controls readiness of individual examples |
| Open learning product decisions | `learning-product-gaps-and-decisions.md` | Decision register; this document does not replace it |

## 2. End-to-end learning loop

The MVP learning loop is considered coherent only when each responsibility has a defined input and output:

1. **Catalogue approval:** only approved exercise families and examples are eligible for scored use.
2. **Instance creation:** the exercise instance is reproducible and its public task data is separated from private expected answers.
3. **Answer submission:** each submission is retained; first and later submissions are not overwritten.
4. **Evaluation:** required criteria, optional criteria, accepted alternatives, and insufficient-information behavior are applied deterministically.
5. **Feedback:** the primary error is shown first; detailed reasoning is available without exposing unrelated private answer data.
6. **Progress evidence:** correctness, hints, corrections, independence, skill attribution, and versions remain distinct.
7. **Mastery decision:** only eligible independent evidence contributes to the approved mastery threshold.
8. **Recommendation:** Learning Strategy selects an eligible next exercise and provides a short rationale.
9. **Review:** due skills receive an appropriate review; failure triggers targeted remediation without erasing history.

No step may silently substitute its own interpretation for another step's contract.

## 3. Product decisions still requiring approval

| Decision group | Required decision | Current state | Affected documents |
|---|---|---|---|
| Rules | Complete ordinary bid transition table | Open / release blocker for affected cases | Ruleset matrix; Game Domain; S1/S5 examples |
| Rules | Exact transition to and from ones | Open / release blocker for affected cases | Ruleset matrix; Game Domain; S1/S5/S8 examples |
| Rules | Palifico transition and two-player exception details | Open / release blocker for affected cases | Ruleset matrix; Game Domain; S1/S3/S4/S5/S9 examples |
| Evaluation | Approve atomic criterion model and treatment of partial answers | Proposed | Answer evaluation rubric |
| Evaluation | Approve numeric precision and tolerance per numeric exercise | Per-example review required | Answer evaluation rubric; S3/S4 examples |
| Mastery | Approve independent evidence threshold | Proposed: 3 independent successful first submissions | Mastery and review policy |
| Mastery | Approve review restoration threshold | Proposed: 2 independent successful review instances | Mastery and review policy |
| Review | Approve interval sequence and time anchor | 1/3/7/14/30 days remains a hypothesis; anchor open | Mastery and review policy |
| Strategy | Approve candidate priority order | Proposed | Learning strategy selection policy |
| Strategy | Approve prerequisite behavior and learner override | Proposed, non-blocking | Learning strategy selection policy |
| S7 | Approve qualitative evidence bands and UX wording | Open; no universal numeric sample threshold proposed | S7 exercise specification and examples |
| S8/S9 | Approve scenario-specific objectives and accepted trade-offs | Required per example; no universal utility function | S8/S9 specifications and examples |

## 4. Recommended approval order

1. Approve the rules matrix entries that are necessary for the chosen MVP exercise set and transfer them into the authoritative Game Domain contract.
2. Review every MVP example against the ruleset version and assign `APPROVED` or a blocking status.
3. Approve the evaluation rubric, including numeric answer precision and structured/free-text criteria.
4. Approve the mastery threshold, independent-evidence definition, and review restoration behavior.
5. Approve review interval behavior and the due-date anchor.
6. Approve Learning Strategy priority order, prerequisite behavior, and learner override.
7. Re-run the S1–S9 release acceptance checklist using only approved exercises and policies.

This order prevents mastery and recommendation behavior from being finalized against unstable exercise semantics.

## 5. MVP release gate

The learning-policy portion of MVP is ready for implementation handoff when all of the following are true:

- the selected scored exercise set depends only on approved Game Domain rules;
- every MVP skill S1–S9 has at least one `APPROVED` exercise family and example;
- every approved example has deterministic required criteria and explicit accepted alternatives;
- numeric precision and insufficient-information behavior are specified where relevant;
- first submission, final submission, hints, corrections, and independent evidence are distinguishable;
- the mastery policy and review schedule have product-owner approval;
- the recommendation policy has product-owner approval and a defined no-eligible-exercise state;
- primary and secondary skill evidence cannot be double-counted;
- end-to-end scenarios X-E01 through X-E04 still satisfy privacy and skill-boundary constraints;
- the release acceptance checklist has observable evidence for each mandatory item.

An unresolved policy may remain in the product backlog only if all affected exercise variants are excluded from scored MVP use and the remaining MVP loop is still complete.

## 6. Handoff packet contents

The implementation handoff should include, at minimum:

- approved Game Domain ruleset and version;
- approved S1–S9 MVP examples and their expected answers;
- the approved answer-evaluation rubric;
- the approved mastery and review policy;
- the approved recommendation policy;
- the example status register and blocked variants;
- the S1–S9 release acceptance checklist;
- the list of explicitly deferred product capabilities.

A document marked `proposed`, `draft`, or `open` must not be represented to the implementation team as an approved requirement.

## 7. Product-owner sign-off

| Area | Reviewer | Decision (`APPROVED` / `CHANGES_REQUIRED` / `DEFERRED`) | Version / reference | Date |
|---|---|---|---|---|
| Ruleset and affected MVP cases | TBD | — | — | — |
| Example catalogue | TBD | — | — | — |
| Evaluation rubric | TBD | — | — | — |
| Mastery and review | TBD | — | — | — |
| Learning Strategy | TBD | — | — | — |
| End-to-end release acceptance | TBD | — | — | — |

## 8. Дополнительные документы приёмки

Следующие документы уточняют процедуру проверки и не заменяют нормативные контракты:

| Concern | Document | Use |
|---|---|---|
| Статусы всех MVP fixtures | `../learning/examples/mvp-example-review-register.md` | Отслеживать READY_FOR_REVIEW, APPROVED и BLOCKED_BY_RULE_DECISION по каждому example_id |
| Проверка эталонов | `../learning/examples/mvp-example-validation-plan.md` | Готовить correct, incorrect, partial, boundary, alternative, insufficient и privacy checks |
| UX-приёмка | `learning-ux-acceptance.md` | Проверять иерархию задачи, терминологию, обратную связь и desktop/mobile поведение |
| Решения владельца продукта | `product-owner-decision-packet.md` | Фиксировать OPEN/APPROVED/CHANGED/DEFERRED решения с reviewer и датой |
| Release gates | `mvp-release-validation-plan.md` | Проводить G1–G8 и собирать доказательства приёмки |

## 9. Уточнённый порядок финальной проверки

1. Закрыть необходимые решения Game Domain и перенести их в authoritative contract.
2. Обновить статусы fixtures в review register; не считать READY_FOR_REVIEW утверждённым содержанием.
3. Пройти validation plan для каждого кандидата scored MVP.
4. Утвердить rubric, mastery/review и Learning Strategy policies.
5. Проверить UX acceptance и end-to-end сценарии.
6. Провести release gates G1–G8 и заполнить sign-off.

Наличие этих документов повышает проверяемость процесса, но не означает, что сами gates уже пройдены.

## 10. Материалы независимого сквозного аудита

Дополнительные документы текущего review этапа:

| Concern | Document | Use |
|---|---|---|
| HTML и скриншоты-прототипы | `review/source-material-and-prototype-review.md` | Разделять подтверждённые возможности reference artifacts и неподтверждённые продуктовые требования |
| Сквозные связи S1–S9 | `review/cross-document-consistency-matrix.md` | Проверять границы навыков, examples, evaluation, progress и UX на уровне инвариантов |
| Открытые решения и scope | `review/decision-closure-and-scope-register.md` | Не закрывать правила и policy decisions без владельца и evidence; исключать blocked cases из scored scope |
| Финальный handoff | `review/final-product-audit-and-handoff.md` | Собирать release gates, доказательства и sign-off без заявления о непроведённых тестах |

Эти документы являются review и handoff artifacts. Они не имеют приоритета над Game Domain, skill contracts или утверждёнными policy documents. Если обнаружено противоречие, его следует оформить как finding и исправить в authoritative документе отдельным согласованным изменением.

## 11. Обязательный порядок закрытия review findings

1. Зафиксировать finding с точной ссылкой на затронутый контракт/example и указать severity.
2. Определить, является ли расхождение ошибкой документа, открытым продуктовым решением или непроверенным поведением реализации.
3. Для правила или policy получить явное решение владельца продукта; для реализации назначить проверку и приложить фактический результат.
4. Исправить authoritative document и затронутые examples/evaluator criteria согласованными патчами.
5. Повторно пройти все связанные строки consistency matrix и сохранить evidence.
6. Не менять `OPEN`, `BLOCKED` или `NOT_VERIFIED` на `APPROVED` только на основании наличия нового документа.
