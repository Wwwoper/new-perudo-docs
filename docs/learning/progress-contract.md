# Learning Progress Contract

Версия: 0.1. Согласовано.

## Решения

- автоматический переход в `NEEDS_REVIEW` по настраиваемым интервалам;
- повторное освоение требует нескольких успешных упражнений разных типов;
- повторения рекомендованы, но не блокируют обучение;
- правильность и уверенность учитываются отдельно;
- прогресс ведётся по навыкам, общий уровень пользователя не рассчитывается;
- сброс навыка сохраняет историю.

## Модели и метрики

`ExerciseAttempt` — попытка прохождения одного экземпляра, содержащая несколько `AnswerSubmission`. Статусы: `STARTED`, `IN_PROGRESS`, `COMPLETED`, `ABANDONED`.

Сохраняются все отправки, EvaluationResult и подсказки. Метрики: `first_submission_accuracy`, `final_accuracy`, `submission_count`, `correction_count`, `hint_count`, `independent_accuracy`.

`SkillProgress`: user_id, skill_id, status, mastery_score, total_attempts, completed_attempts, first_attempt_accuracy, final_accuracy, independent_accuracy, last_practiced_at, last_mastered_at, next_review_at. Статусы: `NOT_STARTED`, `LEARNING`, `PRACTICING`, `MASTERED`, `NEEDS_REVIEW`.

`SkillMastery` учитывает самостоятельность, разнообразие типов, повторяемость ошибок, устойчивость во времени и точность. `SkillProgressHistory` хранит изменения статуса и score. `ErrorStatistics` — частоту, первичные/зависимые ошибки, последнее появление и признак устранения.

## Повторение и сессии

Типы расписания: `MASTERY_REINFORCEMENT`, `FORGETTING_PREVENTION`, `ERROR_REMEDIATION`, `PREREQUISITE_REVIEW`. Используется простая прозрачная модель интервалов. `ReviewTask`: `PENDING`, `IN_PROGRESS`, `COMPLETED`, `SKIPPED`, `CANCELLED`; дубликаты исключаются.

`SkillDependency` задаёт рекомендательные, не блокирующие зависимости. `LearningSession`: `LEARNING`, `PRACTICE`, `REVIEW`, `MIXED`. `LearningProfile` не содержит лишних персональных характеристик.

Learning Progress хранит факты и состояние; Learning Strategy выбирает следующее упражнение и сложность. Сброс меняет актуальное состояние, но не удаляет историю.
