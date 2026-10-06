# Pong: від `pong-2` до `pong-3` — «The Paddle Update»

## 1. Загальний опис завдання

У `pong-2` ракетки й м'яч намальовані, але нічого не рухається.

У `pong-3` гравці можуть **рухати свої ракетки вгору і вниз**:

- Гравець 1 (ліворуч) — клавіші **W** / **S**.
- Гравець 2 (праворуч) — клавіші **↑** / **↓**.

Рух виконується в `love.update(dt)` і множиться на `dt` (дельта часу), щоб швидкість була однаковою на швидких і повільних комп'ютерах. Також ми додаємо змінні рахунку й виводимо рахунок великим шрифтом посередині екрана (поки що він завжди 0).

> Примітка: у цій версії ракетки ще можуть виїжджати за межі екрана. Це виправимо пізніше.

### Файли

| Файл | Що відбувається |
|---|---|
| `main.lua` | Змінюється (уся робота цього кроку) |
| [`push.lua`](assets/push.lua) | Стороння бібліотека, без змін |
| [`font.ttf`](assets/font.ttf) | Сторонній шрифт, без змін |

## 2. Кроки від `pong-2` до `pong-3`

### Крок 1. Оновіть коментар-заголовок

```lua
    pong-3
    "The Paddle Update"
```

### Крок 2. Додайте константу швидкості ракетки

Під `VIRTUAL_WIDTH` / `VIRTUAL_HEIGHT`:

```lua
-- швидкість руху ракетки; в update множиться на dt
PADDLE_SPEED = 200
```

> 200 означає «200 віртуальних пікселів за секунду».

### Крок 3. Створіть більший шрифт для рахунку

У `love.load()`, одразу після створення `smallFont`:

```lua
    scoreFont = love.graphics.newFont('font.ttf', 32)
```

### Крок 4. Задайте початковий рахунок і позиції ракеток

У кінці `love.load()`:

```lua
    player1Score = 0
    player2Score = 0

    -- позиції ракеток по осі Y (вони рухаються лише вгору-вниз)
    player1Y = 30
    player2Y = VIRTUAL_HEIGHT - 50
```

> Це ті самі значення Y, що були жорстко прописані в `pong-2`; тепер вони зберігаються у змінних, щоб їх можна було змінювати.

### Крок 5. Додайте `love.update(dt)`

Додайте нову функцію після `love.load()`. LÖVE викликає її кожен кадр і передає `dt` — час у секундах від попереднього кадру.

```lua
function love.update(dt)
    -- рух гравця 1
    if love.keyboard.isDown('w') then
        player1Y = player1Y + -PADDLE_SPEED * dt
    elseif love.keyboard.isDown('s') then
        player1Y = player1Y + PADDLE_SPEED * dt
    end

    -- рух гравця 2
    if love.keyboard.isDown('up') then
        player2Y = player2Y + -PADDLE_SPEED * dt
    elseif love.keyboard.isDown('down') then
        player2Y = player2Y + PADDLE_SPEED * dt
    end
end
```

> `love.keyboard.isDown` повертає true весь час, поки клавішу утримують, — на відміну від `love.keypressed`, який спрацьовує один раз на натискання. У LÖVE вісь Y спрямована вниз, тому «вгору» означає віднімати від Y.

### Крок 6. Встановіть малий шрифт перед вітальним текстом

Ми перемикатимемо шрифти в `love.draw()`, тому явно встановіть малий шрифт перед виведенням «Hello Pong!»:

```lua
    love.graphics.setFont(smallFont)
    love.graphics.printf('Hello Pong!', 0, 20, VIRTUAL_WIDTH, 'center')
```

### Крок 7. Виведіть рахунок

Одразу після вітального тексту перемкніться на великий шрифт і виведіть обидва рахунки ближче до центру:

```lua
    love.graphics.setFont(scoreFont)
    love.graphics.print(tostring(player1Score), VIRTUAL_WIDTH / 2 - 50,
        VIRTUAL_HEIGHT / 3)
    love.graphics.print(tostring(player2Score), VIRTUAL_WIDTH / 2 + 30,
        VIRTUAL_HEIGHT / 3)
```

> `tostring` перетворює число на текст для виведення.

### Крок 8. Малюйте ракетки у змінних позиціях

Замініть жорстко задані значення Y новими змінними:

```lua
    love.graphics.rectangle('fill', 10, player1Y, 5, 20)
    love.graphics.rectangle('fill', VIRTUAL_WIDTH - 10, player2Y, 5, 20)
```

Рядок із м'ячем залишається без змін.

### Крок 9. Запустіть і перевірте

Запустіть `love .`. Ви побачите «0» і «0» великими цифрами. Утримуйте **W/S** та **↑/↓** — ракетки плавно рухаються. Зверніть увагу: вони можуть виїхати за екран; це виправимо пізніше.
