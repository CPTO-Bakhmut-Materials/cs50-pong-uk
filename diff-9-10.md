# Pong: від `pong-9` до `pong-10` — «The Victory Update»

## 1. Загальний опис завдання

У `pong-9` у грі є подача й рахунок, але вона ніколи не закінчується — очки просто ростуть.

У `pong-10` додаємо **умову перемоги** й четвертий стан гри — **`done`**:

- коли гравець набирає переможну кількість очок, гра показує **«Player N wins!»** шрифтом середнього розміру;
- натискання **Enter** перезапускає гру: рахунок обнуляється, а першим подає **той, хто програв** (заради справедливості).

```
start → serve → play → (очко) → serve …
                  └→ (переможне очко) → done → Enter → serve
```

> В оригінальному `pong-10` переможна кількість очок — **2**, щоб можна було швидко перевірити екран перемоги (хоча коментар у коді каже 10). У `pong-11` її змінено на 10.

Також переносимо перевірку очок **всередину** блоку `play`, щоб очки могли нараховуватися лише під час гри.

### Файли

| Файл | Що відбувається |
|---|---|
| `main.lua` | Змінюється (уся робота цього кроку) |
| `Ball.lua`, `Paddle.lua` | Без змін |
| [`class.lua`](assets/class.lua), [`push.lua`](assets/push.lua), [`font.ttf`](assets/font.ttf) | Сторонні, без змін |

## 2. Кроки від `pong-9` до `pong-10`

### Крок 1. Оновіть коментар-заголовок

```lua
    pong-10
    "The Victory Update"
```

### Крок 2. Додайте шрифт середнього розміру

У `love.load()` згрупуйте шрифти разом і додайте `largeFont` (розмір 16):

```lua
    smallFont = love.graphics.newFont('font.ttf', 8)
    largeFont = love.graphics.newFont('font.ttf', 16)
    scoreFont = love.graphics.newFont('font.ttf', 32)
    love.graphics.setFont(smallFont)
```

### Крок 3. Перенесіть перевірку очок усередину блоку `play`

У `love.update(dt)` виріжте два блоки `if ball.x < 0 … end` та `if ball.x > VIRTUAL_WIDTH … end` і вставте їх **усередину** `elseif gameState == 'play' then`, одразу після перевірки нижньої стіни. `end`, що закривав блок play, переміщується нижче за них.

### Крок 4. Перевірка переможця — лівий край

Перепишіть блок лівого краю так, щоб він перевіряв перемогу:

```lua
        if ball.x < 0 then
            servingPlayer = 1
            player2Score = player2Score + 1

            if player2Score == 2 then
                winningPlayer = 2
                gameState = 'done'
            else
                gameState = 'serve'
                ball:reset()
            end
        end
```

> Якщо гравець 2 щойно набрав переможну кількість очок, переходимо в `done` і запам'ятовуємо переможця в новій глобальній змінній `winningPlayer`. Інакше продовжуємо, як раніше.

### Крок 5. Перевірка переможця — правий край

Те саме для гравця 1:

```lua
        if ball.x > VIRTUAL_WIDTH then
            servingPlayer = 2
            player1Score = player1Score + 1

            if player1Score == 2 then
                winningPlayer = 1
                gameState = 'done'
            else
                gameState = 'serve'
                ball:reset()
            end
        end
```

### Крок 6. Перезапуск зі стану `done` клавішею Enter

У `love.keypressed` додайте третю гілку до логіки Enter:

```lua
        elseif gameState == 'done' then
            gameState = 'serve'

            ball:reset()

            player1Score = 0
            player2Score = 0

            -- першим подає той, хто програв
            if winningPlayer == 1 then
                servingPlayer = 2
            else
                servingPlayer = 1
            end
        end
```

### Крок 7. Виведіть повідомлення про перемогу

У `love.draw()` додайте гілку для `done` у кінець ланцюжка `if` з повідомленнями станів:

```lua
    elseif gameState == 'done' then
        love.graphics.setFont(largeFont)
        love.graphics.printf('Player ' .. tostring(winningPlayer) .. ' wins!',
            0, 10, VIRTUAL_WIDTH, 'center')
        love.graphics.setFont(smallFont)
        love.graphics.printf('Press Enter to restart!', 0, 30, VIRTUAL_WIDTH, 'center')
    end
```

### Крок 8. Запустіть і перевірте

Запустіть `love .` і пограйте. Коли один із гравців набере 2 очки, гра зупиниться й покаже «Player N wins!». Натисніть **Enter** — рахунок обнулиться, і подаватиме той, хто програв.
