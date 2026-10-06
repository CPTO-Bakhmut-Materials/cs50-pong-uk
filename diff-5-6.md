# Pong: від `pong-5` до `pong-6` — «The FPS Update»

## 1. Загальний опис завдання

`pong-5` реорганізував код у класи, але сховав рахунок.

У `pong-6` додаємо невеликі, але корисні речі:

- вікно отримує **заголовок** («Pong»);
- **рахунок** повертається на екран (обидва значення поки що 0);
- **лічильник FPS** (кадрів за секунду) виводиться зеленим у лівому верхньому куті — зручно для перевірки продуктивності;
- клас `Ball` отримує метод **`collides(paddle)`**, який перевіряє, чи перетинається м'яч із ракеткою. Він ще не використовується — це буде в `pong-7`.

### Файли

| Файл | Що відбувається |
|---|---|
| `main.lua` | Змінюється |
| `Ball.lua` | Змінюється — новий метод `collides` |
| `Paddle.lua` | Без змін |
| [`class.lua`](assets/class.lua), [`push.lua`](assets/push.lua), [`font.ttf`](assets/font.ttf) | Сторонні, без змін |

## 2. Кроки від `pong-5` до `pong-6`

### Крок 1. Оновіть коментар-заголовок

```lua
    pong-6
    "The FPS Update"
```

### Крок 2. Задайте заголовок вікна

У `love.load()`, після `setDefaultFilter`:

```lua
    love.window.setTitle('Pong')
```

### Крок 3. Поверніть шрифт для рахунку

У `love.load()`, після `smallFont`:

```lua
    scoreFont = love.graphics.newFont('font.ttf', 32)
```

### Крок 4. Поверніть змінні рахунку

У `love.load()`, після `push.setupScreen(...)` і перед створенням ракеток:

```lua
    player1Score = 0
    player2Score = 0
```

### Крок 5. Виведіть рахунок

У `love.draw()`, після тексту «Hello … State!» і перед малюванням ракеток:

```lua
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50,
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
```

### Крок 6. Напишіть функцію `displayFPS`

У самому кінці `main.lua` додайте нову функцію:

```lua
function displayFPS()
    love.graphics.setFont(smallFont)
    love.graphics.setColor(0, 1, 0, 1)
    love.graphics.print('FPS: ' .. tostring(love.timer.getFPS()), 10, 10)
    love.graphics.setColor(1, 1, 1, 1)
end
```

> - `love.timer.getFPS()` повертає поточну кількість кадрів за секунду.
> - `..` з'єднує (конкатенує) рядки в Lua.
> - `setColor(0, 1, 0, 1)` = зелений (червоний, зелений, синій, прозорість; значення 0–1). Після виведення повертаємо білий колір, інакше **все, що малюється далі**, теж стане зеленим.

### Крок 7. Викличте `displayFPS` у `love.draw()`

Одразу після `ball:render()` і перед `push.finish()`:

```lua
    displayFPS()
```

### Крок 8. Додайте перевірку зіткнень у `Ball.lua`

У `Ball.lua`, між `Ball:init` і `Ball:reset`, додайте:

```lua
function Ball:collides(paddle)
    -- чи один прямокутник повністю лівіше/правіше за інший?
    if self.x >= paddle.x + paddle.width or paddle.x >= self.x + self.width then
        return false
    end

    -- чи один прямокутник повністю вище/нижче за інший?
    if self.y >= paddle.y + paddle.height or paddle.y >= self.y + self.height then
        return false
    end

    -- інакше вони перетинаються
    return true
end
```

> Це **AABB-зіткнення** (axis-aligned bounding boxes — прямокутники, вирівняні за осями). Два прямокутники *не* торкаються, якщо один повністю збоку від іншого або повністю над/під ним. Якщо жодна з умов не виконується — вони перетинаються.

### Крок 9. Запустіть і перевірте

Запустіть `love .`. Заголовок вікна — «Pong», обидва рахунки показують 0, а в лівому верхньому куті з'являється зелений напис «FPS: 60» (або подібний). М'яч усе ще пролітає крізь ракетки — перевірку зіткнень підключимо на наступному кроці.
