# Pong: від `pong-4` до `pong-5` — «The Class Update»

## 1. Загальний опис завдання

У `pong-4` ракетки й м'яч — це купа окремих глобальних змінних (`player1Y`, `ballX`, `ballDX`, …), а вся їхня логіка знаходиться всередині `main.lua`.

У `pong-5` ми **рефакторимо** код за допомогою **класів** (об'єктно-орієнтоване програмування):

- клас `Paddle` зберігає позицію, розмір і швидкість ракетки та вміє сам себе оновлювати й малювати;
- клас `Ball` робить те саме для м'яча, плюс має метод `reset()`;
- `main.lua` тепер лише створює об'єкти (`player1`, `player2`, `ball`) і викликає їхні методи.

У Lua немає вбудованих класів, тому підключаємо сторонню бібліотеку **hump.class**. Гра виглядає й працює майже так само, як `pong-4`, — цей крок про організацію коду.

### Файли

| Файл | Що відбувається |
|---|---|
| [`class.lua`](assets/class.lua) | **Новий.** Стороння бібліотека (hump.class, автор Matthias Richter, [github.com/vrld/hump](https://github.com/vrld/hump/blob/master/class.lua), ліцензія MIT) — скопіюйте, не пишіть самі |
| `Paddle.lua` | **Новий.** Наш власний код — напишіть його |
| `Ball.lua` | **Новий.** Наш власний код — напишіть його |
| `main.lua` | Змінюється |
| [`push.lua`](assets/push.lua) | Стороння бібліотека, без змін |
| [`font.ttf`](assets/font.ttf) | Сторонній шрифт, без змін |

## 2. Кроки від `pong-4` до `pong-5`

### Крок 1. Додайте бібліотеку класів

Скопіюйте [`assets/class.lua`](assets/class.lua) у папку проєкту. Вона дає функцію `Class{}` для оголошення класів.

### Крок 2. Створіть `Paddle.lua`

Створіть новий файл `Paddle.lua`:

```lua
Paddle = Class{}

function Paddle:init(x, y, width, height)
    self.x = x
    self.y = y
    self.width = width
    self.height = height
    self.dy = 0
end

function Paddle:update(dt)
    if self.dy < 0 then
        -- рух угору: не виходимо за верхній край екрана
        self.y = math.max(0, self.y + self.dy * dt)
    else
        -- рух униз: не виходимо за нижній край екрана
        self.y = math.min(VIRTUAL_HEIGHT - self.height, self.y + self.dy * dt)
    end
end

function Paddle:render()
    love.graphics.rectangle('fill', self.x, self.y, self.width, self.height)
end
```

> - `init` виконується один раз під час створення нового об'єкта — як конструктор.
> - `self` — це «саме ця ракетка». Кожен об'єкт-ракетка має власні `x`, `y` тощо.
> - `Paddle:update(dt)` — скорочений запис для `Paddle.update(self, dt)`.
> - Тепер ракетка зберігає свою **швидкість** (`dy`), а не `main.lua` переміщує її напряму. Обмеження межами екрана з `pong-4` переїхало сюди й тепер використовує `self.height` замість жорстко заданого 20.

### Крок 3. Створіть `Ball.lua`

Створіть новий файл `Ball.lua`:

```lua
Ball = Class{}

function Ball:init(x, y, width, height)
    self.x = x
    self.y = y
    self.width = width
    self.height = height

    self.dy = math.random(2) == 1 and -100 or 100
    self.dx = math.random(-50, 50)
end

function Ball:reset()
    self.x = VIRTUAL_WIDTH / 2 - 2
    self.y = VIRTUAL_HEIGHT / 2 - 2
    self.dy = math.random(2) == 1 and -100 or 100
    self.dx = math.random(-50, 50)
end

function Ball:update(dt)
    self.x = self.x + self.dx * dt
    self.y = self.y + self.dy * dt
end

function Ball:render()
    love.graphics.rectangle('fill', self.x, self.y, self.width, self.height)
end
```

> Примітка: порівняно з `pong-4`, в оригінальному коді тут `dx` і `dy` поміняні місцями — фіксована швидкість ±100 припадає на вісь **Y**, тож м'яч тепер летить переважно вгору-вниз. Саме так поводиться `pong-5` в оригінальному репозиторії; швидша горизонтальна швидкість з'являється в `pong-7`.

### Крок 4. Оновіть коментар-заголовок у `main.lua`

```lua
    pong-5
    "The Class Update"
```

### Крок 5. Підключіть бібліотеку класів і нові класи

У `main.lua`, одразу після `push = require 'push'`:

```lua
Class = require 'class'

require 'Paddle'
require 'Ball'
```

> `class.lua` повертає функцію `Class`, тому ми її зберігаємо. `Paddle.lua` і `Ball.lua` самі створюють глобальні `Paddle` і `Ball`, тож достатньо простого `require`. `Class` потрібно завантажити **раніше** за них, бо вони його використовують.

### Крок 6. Замініть змінні об'єктами в `love.load()`

Видаліть `player1Y`, `player2Y`, `ballX`, `ballY`, `ballDX`, `ballDY` і натомість напишіть:

```lua
    player1 = Paddle(10, 30, 5, 20)
    player2 = Paddle(VIRTUAL_WIDTH - 10, VIRTUAL_HEIGHT - 30, 5, 20)

    ball = Ball(VIRTUAL_WIDTH / 2 - 2, VIRTUAL_HEIGHT / 2 - 2, 4, 4)
```

> Виклик `Paddle(...)` створює новий об'єкт і виконує `Paddle:init(...)`. (Гравець 2 тепер стартує з `VIRTUAL_HEIGHT - 30`, трохи нижче, ніж раніше.)

### Крок 7. Нехай клавіші задають швидкість ракетки, а не позицію

У `love.update(dt)` перепишіть блоки руху:

```lua
    -- рух гравця 1
    if love.keyboard.isDown('w') then
        player1.dy = -PADDLE_SPEED
    elseif love.keyboard.isDown('s') then
        player1.dy = PADDLE_SPEED
    else
        player1.dy = 0
    end

    -- рух гравця 2
    if love.keyboard.isDown('up') then
        player2.dy = -PADDLE_SPEED
    elseif love.keyboard.isDown('down') then
        player2.dy = PADDLE_SPEED
    else
        player2.dy = 0
    end
```

> Нова гілка `else` важлива: коли жодну клавішу не натиснуто, швидкість має повернутися до 0, інакше ракетка продовжувала б ковзати.

### Крок 8. Викликайте методи `update` об'єктів

Замініть рух м'яча й додайте оновлення ракеток у кінці `love.update(dt)`:

```lua
    if gameState == 'play' then
        ball:update(dt)
    end

    player1:update(dt)
    player2:update(dt)
```

### Крок 9. Використовуйте `ball:reset()` при натисканні Enter

У `love.keypressed` замініть чотири рядки, що скидають позицію і швидкість м'яча, на:

```lua
            ball:reset()
```

### Крок 10. Малюйте методами `render()`

У `love.draw()` замініть три рядки `love.graphics.rectangle` на:

```lua
    player1:render()
    player2:render()

    ball:render()
```

### Крок 11. Запустіть і перевірте

Запустіть `love .`. Усе працює як у `pong-4`: Enter запускає й скидає гру, ракетки рухаються й не виходять за екран. М'яч тепер рухається переважно вертикально (див. примітку в кроці 3).
