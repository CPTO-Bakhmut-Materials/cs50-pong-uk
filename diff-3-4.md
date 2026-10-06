# Pong: від `pong-3` до `pong-4` — «The Ball Update»

## 1. Загальний опис завдання

У `pong-3` ракетки рухаються, але м'яч просто стоїть по центру, а ракетки можуть виїжджати за екран.

У `pong-4`:

- **м'яч рухається** у випадковому напрямку після старту гри;
- з'являється **стан гри** (`'start'` або `'play'`), який перемикається клавішею **Enter**;
- натискання Enter під час гри **повертає** м'яч у центр із новим випадковим напрямком;
- ракетки **обмежені** межами екрана.

Щоб зосередитися на м'ячі, виведення рахунку з `pong-3` поки що **прибираємо** (шрифт і змінні рахунку повернуться в одній із наступних версій).

> Примітка: м'яч ще ні від чого не відбивається — він вилітає за екран. Натисніть Enter двічі, щоб повернути його.

### Файли

| Файл | Що відбувається |
|---|---|
| `main.lua` | Змінюється (уся робота цього кроку) |
| [`push.lua`](assets/push.lua) | Стороння бібліотека, без змін |
| [`font.ttf`](assets/font.ttf) | Сторонній шрифт, без змін |

## 2. Кроки від `pong-3` до `pong-4`

### Крок 1. Оновіть коментар-заголовок

```lua
    pong-4
    "The Ball Update"
```

### Крок 2. Ініціалізуйте генератор випадкових чисел

У `love.load()`, одразу після `setDefaultFilter`:

```lua
    math.randomseed(os.time())
```

> Без «зерна» (seed) `math.random` щоразу видає ту саму «випадкову» послідовність. Зерно з поточного часу робить кожен запуск іншим.

### Крок 3. Тимчасово приберіть рахунок

У `love.load()` видаліть:
- рядок `scoreFont = ...`;
- рядки `player1Score = 0` і `player2Score = 0`.

У `love.draw()` видаліть блок, який встановлює `scoreFont` і виводить два рахунки.

### Крок 4. Додайте змінні позиції та швидкості м'яча

У кінці `love.load()`, після позицій ракеток:

```lua
    -- м'яч починає з центру
    ballX = VIRTUAL_WIDTH / 2 - 2
    ballY = VIRTUAL_HEIGHT / 2 - 2

    -- випадкова початкова швидкість
    ballDX = math.random(2) == 1 and 100 or -100
    ballDY = math.random(-50, 50)
```

> `DX` / `DY` = зміна X / Y за секунду (швидкість).
> `math.random(2) == 1 and 100 or -100` — це аналог тернарного оператора в Lua: 50% шанс полетіти праворуч (100), 50% — ліворуч (−100).
> `math.random(-50, 50)` дає випадкове ціле число від −50 до 50, тож м'яч летить угору чи вниз під випадковим кутом.

### Крок 5. Додайте стан гри

Також у кінці `love.load()`:

```lua
    gameState = 'start'
```

> Стан гри — це проста змінна, яка повідомляє решті коду, в якому «режимі» зараз гра. Ми перевірятимемо її в `update` і `draw`.

### Крок 6. Обмежте рух ракеток межами екрана

У `love.update(dt)` обгорніть кожне обчислення позиції ракетки в `math.max` (верхній край) або `math.min` (нижній край):

```lua
    -- рух гравця 1
    if love.keyboard.isDown('w') then
        player1Y = math.max(0, player1Y + -PADDLE_SPEED * dt)
    elseif love.keyboard.isDown('s') then
        player1Y = math.min(VIRTUAL_HEIGHT - 20, player1Y + PADDLE_SPEED * dt)
    end

    -- рух гравця 2
    if love.keyboard.isDown('up') then
        player2Y = math.max(0, player2Y + -PADDLE_SPEED * dt)
    elseif love.keyboard.isDown('down') then
        player2Y = math.min(VIRTUAL_HEIGHT - 20, player2Y + PADDLE_SPEED * dt)
    end
```

> `math.max(0, y)` не дає Y стати меншим за 0 (верх). `math.min(VIRTUAL_HEIGHT - 20, y)` не дає верхньому краю ракетки опуститися нижче, ніж «висота екрана мінус висота ракетки (20)».

### Крок 7. Рухайте м'яч під час гри

У кінці `love.update(dt)`:

```lua
    if gameState == 'play' then
        ballX = ballX + ballDX * dt
        ballY = ballY + ballDY * dt
    end
```

### Крок 8. Перемикайте стани клавішею Enter

У `love.keypressed(key)` додайте гілку `elseif` після перевірки Escape:

```lua
    elseif key == 'enter' or key == 'return' then
        if gameState == 'start' then
            gameState = 'play'
        else
            gameState = 'start'

            -- повертаємо м'яч у центр
            ballX = VIRTUAL_WIDTH / 2 - 2
            ballY = VIRTUAL_HEIGHT / 2 - 2

            -- нова випадкова швидкість
            ballDX = math.random(2) == 1 and 100 or -100
            ballDY = math.random(-50, 50) * 1.5
        end
    end
```

> Перевіряємо і `'enter'`, і `'return'`, бо основна клавіша Enter у LÖVE називається `'return'`, а `'enter'` покриває деякі інші клавіатури.

### Крок 9. Показуйте поточний стан на екрані

У `love.draw()` замініть рядок «Hello Pong!» на:

```lua
    love.graphics.setFont(smallFont)

    if gameState == 'start' then
        love.graphics.printf('Hello Start State!', 0, 20, VIRTUAL_WIDTH, 'center')
    else
        love.graphics.printf('Hello Play State!', 0, 20, VIRTUAL_WIDTH, 'center')
    end
```

### Крок 10. Малюйте м'яч у його позиції

Замініть жорстко заданий прямокутник м'яча на:

```lua
    love.graphics.rectangle('fill', ballX, ballY, 4, 4)
```

### Крок 11. Запустіть і перевірте

Запустіть `love .`. На екрані — «Hello Start State!». Натисніть **Enter** — текст зміниться на «Hello Play State!», і м'яч полетить у випадковому напрямку. Натисніть **Enter** ще раз, щоб повернути його. Ракетки тепер зупиняються біля верхнього й нижнього країв.
