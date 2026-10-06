# Pong: від `pong-7` до `pong-8` — «The Score Update»

## 1. Загальний опис завдання

У `pong-7` м'яч відбивається, але коли гравець пропускає, м'яч просто назавжди вилітає за екран.

У `pong-8` додаємо **нарахування очок**:

- якщо м'яч виходить за **лівий** край, очко отримує гравець 2;
- якщо за **правий** — гравець 1;
- після очка м'яч повертається в центр, а гра — у стан `'start'` (натисніть Enter, щоб продовжити).

Також запам'ятовуємо, хто подаватиме наступним (`servingPlayer`), — поки що це не використовується, але готує наступний крок. Налагоджувальний текст «Hello Start/Play State!» прибираємо.

### Файли

| Файл | Що відбувається |
|---|---|
| `main.lua` | Змінюється (уся робота цього кроку) |
| `Ball.lua`, `Paddle.lua` | Без змін |
| [`class.lua`](assets/class.lua), [`push.lua`](assets/push.lua), [`font.ttf`](assets/font.ttf) | Сторонні, без змін |

## 2. Кроки від `pong-7` до `pong-8`

### Крок 1. Оновіть коментар-заголовок

```lua
    pong-8
    "The Score Update"
```

### Крок 2. Визначайте вихід м'яча за лівий край

У `love.update(dt)`, **після** блоку зіткнень `if gameState == 'play' then … end` і **перед** кодом керування ракетками:

```lua
    if ball.x < 0 then
        servingPlayer = 1
        player2Score = player2Score + 1
        ball:reset()
        gameState = 'start'
    end
```

> Гравець 1 пропустив, тож очко отримує гравець 2, а гравець 1 (який програв розіграш) подаватиме наступним.

### Крок 3. Визначайте вихід м'яча за правий край

Одразу нижче:

```lua
    if ball.x > VIRTUAL_WIDTH then
        servingPlayer = 2
        player1Score = player1Score + 1
        ball:reset()
        gameState = 'start'
    end
```

> `servingPlayer` — нова глобальна змінна. Поки що вона лише встановлюється тут; використовуватиметься в `pong-9`.

### Крок 4. Приберіть налагоджувальний текст стану

У `love.draw()` видаліть увесь блок:

```lua
    if gameState == 'start' then
        love.graphics.printf('Hello Start State!', 0, 20, VIRTUAL_WIDTH, 'center')
    else
        love.graphics.printf('Hello Play State!', 0, 20, VIRTUAL_WIDTH, 'center')
    end
```

(Рядок `love.graphics.setFont(smallFont)` над ним залиште.)

### Крок 5. Запустіть і перевірте

Запустіть `love .` і натисніть **Enter**. Пропустіть м'яч повз одну з ракеток: рахунок суперника збільшиться на 1, м'яч повернеться в центр, і гра чекатиме. Натисніть **Enter**, щоб подати знову.
