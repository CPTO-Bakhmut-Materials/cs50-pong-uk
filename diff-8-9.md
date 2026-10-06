# Pong: від `pong-8` до `pong-9` — «The Serve Update»

## 1. Загальний опис завдання

У `pong-8` очки нараховуються, але після кожного очка гра повертається в `'start'`, і м'яч летить у випадковому напрямку — ніхто насправді не «подає».

У `pong-9` додаємо повноцінну фазу **подачі**. Тепер у гри три стани:

```
start  --Enter-->  serve  --Enter-->  play
                     ^                  |
                     +----- очко -------+
```

- **start** — «Welcome to Pong! Press Enter to begin!»
- **serve** — «Player N's serve! Press Enter to serve!» М'яч полетить **від** гравця, що подає, у бік суперника.
- **play** — без повідомлень, м'яч у русі.

Наступним подає гравець, **який пропустив** очко. Виведення рахунку також виноситься в окрему функцію `displayScore()`.

### Файли

| Файл | Що відбувається |
|---|---|
| `main.lua` | Змінюється (уся робота цього кроку) |
| `Ball.lua`, `Paddle.lua` | Без змін |
| [`class.lua`](assets/class.lua), [`push.lua`](assets/push.lua), [`font.ttf`](assets/font.ttf) | Сторонні, без змін |

## 2. Кроки від `pong-8` до `pong-9`

### Крок 1. Оновіть коментар-заголовок

```lua
    pong-9
    "The Serve Update"
```

### Крок 2. Задайте гравця, що подає

У `love.load()`, після змінних рахунку:

```lua
    -- 1 або 2; наступним подає той, хто пропустив очко
    servingPlayer = 1
```

### Крок 3. Готуйте напрямок м'яча в стані `serve`

На початку `love.update(dt)` перетворіть `if gameState == 'play' then` на конструкцію `if / elseif`:

```lua
    if gameState == 'serve' then
        ball.dy = math.random(-50, 50)
        if servingPlayer == 1 then
            ball.dx = math.random(140, 200)
        else
            ball.dx = -math.random(140, 200)
        end
    elseif gameState == 'play' then
        -- (наявний код зіткнень із ракетками й стінами залишається тут)
    end
```

> Гравець 1 знаходиться ліворуч, тож його подача летить **праворуч** (додатний `dx`). Подача гравця 2 летить **ліворуч**. Подача також швидша, ніж раніше (140–200 px/с).

### Крок 4. Після очка переходьте в `serve`

У двох блоках «м'яч вилетів за екран» замініть `gameState = 'start'` на:

```lua
        gameState = 'serve'
```

(і в блоці `ball.x < 0`, і в блоці `ball.x > VIRTUAL_WIDTH`).

### Крок 5. Перепишіть логіку клавіші Enter

У `love.keypressed` замініть гілку Enter на:

```lua
    elseif key == 'enter' or key == 'return' then
        if gameState == 'start' then
            gameState = 'serve'
        elseif gameState == 'serve' then
            gameState = 'play'
        end
    end
```

> Enter більше не скидає м'яч під час гри — старий виклик `ball:reset()` і гілку `else` прибрано. Скидання відбувається лише після очка.

### Крок 6. Винесіть виведення рахунку в `displayScore()`

У кінець `main.lua` додайте:

```lua
function displayScore()
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50,
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
end
```

У `love.draw()` видаліть старі рядки виведення рахунку й поставте на їхнє місце виклик:

```lua
    displayScore()
```

### Крок 7. Показуйте повідомлення для кожного стану

У `love.draw()`, одразу після `displayScore()`:

```lua
    if gameState == 'start' then
        love.graphics.setFont(smallFont)
        love.graphics.printf('Welcome to Pong!', 0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press Enter to begin!', 0, 20, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'serve' then
        love.graphics.setFont(smallFont)
        love.graphics.printf('Player ' .. tostring(servingPlayer) .. "'s serve!",
            0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.printf('Press Enter to serve!', 0, 20, VIRTUAL_WIDTH, 'center')
    elseif gameState == 'play' then
        -- під час гри повідомлень немає
    end
```

> Знову встановлюємо `smallFont`, бо `displayScore()` щойно перемкнула шрифт на `scoreFont`. Рядок `"'s serve!"` записано в подвійних лапках, щоб він міг містити апостроф.

> Порада: тексти на екрані можна перекласти українською (наприклад, `'Гравець ' .. tostring(servingPlayer) .. ' подає!'`), але шрифт `font.ttf` містить лише латиницю — для кирилиці потрібен інший піксельний шрифт.

### Крок 8. (Необов'язково) Колір FPS

Оригінальний репозиторій змінює колір FPS на `love.graphics.setColor(0, 255, 0, 255)`. У LÖVE 11+ значення понад 1 сприймаються як 1, тож це той самий зелений — можна залишити `(0, 1, 0, 1)`.

### Крок 9. Запустіть і перевірте

Запустіть `love .`. Ви побачите «Welcome to Pong!». **Enter** → «Player 1's serve!». **Enter** → м'яч летить праворуч. Коли хтось пропускає, рахунок оновлюється, а гравець, який пропустив, показується як наступний, хто подає.
