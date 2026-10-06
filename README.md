# Pong — покрокова інструкція

Покрокове проходження проєкту [games50/pong](https://github.com/games50/pong) (CS50 2D, LÖVE2D): від порожнього вікна (`pong-0`) до готової гри (`pong-final`).

Кожен файл описує **один крок** між двома версіями і містить:

1. **Загальний опис завдання** — що додає ця версія і навіщо.
2. **Невеликі кроки** — як перейти від попередньої версії до цієї, з кодом.

## З чого почати

Стартовий файл — [main.lua](main.lua) (це версія `pong-0`). Створіть нову папку проєкту, скопіюйте туди `main.lua` і запустіть командою `love .`. Далі рухайтеся по кроках з таблиці нижче.

## Кроки

| # | Файл | Версія | Що додається |
|---|---|---|---|
| 1 | [diff-0-1.md](diff-0-1.md) | pong-1 — «The Low-Res Update» | Віртуальна роздільна здатність з бібліотекою `push`, чіткі пікселі, вихід клавішею Escape |
| 2 | [diff-1-2.md](diff-1-2.md) | pong-2 — «The Rectangle Update» | Ретрошрифт, колір фону, ракетки й м'яч у вигляді прямокутників |
| 3 | [diff-2-3.md](diff-2-3.md) | pong-3 — «The Paddle Update» | Ракетки рухаються клавішами W/S та ↑/↓, відображення рахунку |
| 4 | [diff-3-4.md](diff-3-4.md) | pong-4 — «The Ball Update» | Рухомий м'яч, стани гри `start`/`play`, ракетки не виходять за екран |
| 5 | [diff-4-5.md](diff-4-5.md) | pong-5 — «The Class Update» | Рефакторинг у класи `Paddle` і `Ball` (бібліотека `class.lua`) |
| 6 | [diff-5-6.md](diff-5-6.md) | pong-6 — «The FPS Update» | Заголовок вікна, повернення рахунку, лічильник FPS, метод перевірки зіткнень |
| 7 | [diff-6-7.md](diff-6-7.md) | pong-7 — «The Collision Update» | М'яч відбивається від ракеток і стін |
| 8 | [diff-7-8.md](diff-7-8.md) | pong-8 — «The Score Update» | Нарахування очок, коли м'яч вилітає за екран |
| 9 | [diff-8-9.md](diff-8-9.md) | pong-9 — «The Serve Update» | Стан `serve` — подає гравець, який пропустив м'яч |
| 10 | [diff-9-10.md](diff-9-10.md) | pong-10 — «The Victory Update» | Умова перемоги, стан `done`, перезапуск |
| 11 | [diff-10-11.md](diff-10-11.md) | pong-11 — «The Audio Update» | Звукові ефекти, гра до 10 очок |
| 12 | [diff-11-12.md](diff-11-12.md) | pong-12 — «The Resize Update» | Правильне масштабування при зміні розміру вікна |
| 13 | [diff-12-final.md](diff-12-final.md) | pong-final | Прибирання коду, без нових можливостей |

## Ресурси (assets)

Сторонні бібліотеки та ресурси лежать у папці [assets/](assets/). Їх **не потрібно писати самостійно** — лише скопіювати у свій проєкт на потрібному кроці.

| Файл | Що це | Потрібен з кроку | Джерело / ліцензія |
|---|---|---|---|
| [assets/push.lua](assets/push.lua) | Бібліотека віртуальної роздільної здатності | [diff-0-1](diff-0-1.md) | [Ulydev/push](https://github.com/Ulydev/push), MIT |
| [assets/font.ttf](assets/font.ttf) | Піксельний шрифт «04b03» | [diff-1-2](diff-1-2.md) | Yuji Oshimoto, [04.jp.org](http://www.04.jp.org) |
| [assets/class.lua](assets/class.lua) | Бібліотека класів (hump.class) | [diff-4-5](diff-4-5.md) | [vrld/hump](https://github.com/vrld/hump), MIT |
| [assets/sounds/paddle_hit.wav](assets/sounds/paddle_hit.wav) | Звук удару по ракетці | [diff-10-11](diff-10-11.md) | [games50/pong](https://github.com/games50/pong) |
| [assets/sounds/wall_hit.wav](assets/sounds/wall_hit.wav) | Звук удару об стіну | [diff-10-11](diff-10-11.md) | [games50/pong](https://github.com/games50/pong) |
| [assets/sounds/score.wav](assets/sounds/score.wav) | Звук здобутого очка | [diff-10-11](diff-10-11.md) | [games50/pong](https://github.com/games50/pong) |

> **Важливо:** у проєкті гри ці файли мають лежати **поруч із `main.lua`** (а звуки — у підпапці `sounds/`), бо саме за такими шляхами їх завантажує код (`require 'push'`, `'font.ttf'`, `'sounds/score.wav'`).

## Файли проєкту за версіями

| Версія | Файли |
|---|---|
| pong-0 | `main.lua` |
| pong-1 – pong-4 | + `push.lua`, `font.ttf` (з pong-2) |
| pong-5 – pong-10 | + `class.lua`, `Paddle.lua`, `Ball.lua` |
| pong-11 – pong-final | + папка `sounds/` |
