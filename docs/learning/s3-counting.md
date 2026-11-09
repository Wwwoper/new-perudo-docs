# S3 — Подсчёт кубиков

Версия: 0.1. S3 считает собственные совпадения, необходимое количество на скрытых кубиках и полный количественный результат. Вероятность относится к S4.

## Правила

NORMAL: для ставок на грани 2–6 считаются выбранная грань и единицы; ставка на единицы учитывает только единицы. PALIFICO: единицы не джокеры, учитывается только выбранная грань.

## Поднавыки

- **S3.1:** вводный подсчёт собственных совпадений без джокеров.
- **S3.2:** учёт единиц-джокеров, ставки на единицы, сравнение, поиск ошибок, смешанные комбинации и смена значения.
- **S3.3:** `required_matches = max(0, bid_quantity - own_matches)` — сколько совпадений нужно на скрытых кубиках.
- **S3.4:** полный подсчёт общего количества, скрытых кубиков, собственных совпадений и требуемых совпадений. Поддерживаются разные количества кубиков у игроков. Промежуточные поля проверяются отдельно; верный итог при неверных промежуточных ответах не засчитывается полностью.

## Модели

```python
@dataclass(frozen=True)
class PlayerDice:
    player_id: str
    dice_count: int

@dataclass(frozen=True)
class Bid:
    quantity: int
    face: int

@dataclass(frozen=True)
class GameSituation:
    players: tuple[PlayerDice, ...]
    current_player_id: str
    own_dice: tuple[int, ...]
    bid: Bid
```

`Ruleset` содержит `ruleset_id`, `version`, `ones_are_wildcards`; классический профиль — `CLASSIC_PERUDO_V1`.

Математическое ядро: `validate_dice`, `count_face_matches`, `count_bid_matches`, `count_total_dice`, `count_hidden_dice`, `required_matches`, `calculate_full_count`. Результат — `CountResult`; чужие скрытые значения не передаются.

## Диагностика

S3.2: `S3_WILDCARD_OMITTED`, `S3_WILDCARD_EXTRA`, `S3_FACE_CONFUSION`, `S3_COUNT_MISS`, `S3_COUNT_DUPLICATE`, `S3_BID_RULE_CONFUSION`. S3.3: `S3_OWN_DICE_IGNORED`, `S3_REMAINING_NEGATIVE`, `S3_REMAINING_CALC_ERROR`.

Генераторы S3.1–S3.4 реализуют общий `ExerciseGenerator`; сохраняются seed, версия генератора и экземпляр. Предварительно для S3.2: минимум 12 заданий и около 90%, с последующей калибровкой.

## Тесты

Подсчёт граней, джокеры NORMAL, отсутствие джокеров PALIFICO, ставки на единицы, нулевое требование, разные количества кубиков, валидация, дубликаты/пропуски, повторная отправка, промежуточные поля и отсутствие утечки скрытых значений.
