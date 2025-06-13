# ✅ Урок 42: Створення команд для TelegramBot

---
<img src="main_image.png" alt="pygame" width="1500">

## Зміст уроку:

1. [Сьогодні на уроці](#1-сьогодні-на-уроці)
2. [Реалізація команди `/films`](#2-реалізація-команди-films)
3. [Функція `Keyboard builder`](#3-функція-keyboard-builder)
4. [Створення `Menu` для **TelegramBot**](#4-створення-menu-для-telegrambot)
5. [Реалізація перегляду інформації про фільми](#5-реалізація-перегляду-інформації-про-фільми)
6. [Підведення підсумків 🚀](#6-підведення-підсумків)

> 🔗 Useful Links:

- [JavaScript Object Notation](https://uk.wikipedia.org/wiki/JSON)

---

## 1. Сьогодні на уроці

> 💡 На попередньому уроці ми створили базового **TelegramBot** та налаштували його так, щоб він реагував на команду
`/start`.

На цьому уроці ми навчимося створювати команди для перегляду списку фільмів у **TelegramBot**, а також реалізувати
функцію для відображення детальної інформації про кожен фільм, що дозволить розширити функціонал нашого бота та зробити
його корисним і цікавим для користувачів.

Користувачі зможуть легко переглядати список доступних фільмів, а також отримати більше інформації про кожен з них,
зокрема **назву, опис, рейтинг, жанр, акторів** і навіть побачити **постер** фільму.

### Основні завдання на сьогодні:

- За допомогою класу `Command` створимо команду `/films`, яка дозволить користувачам переглядати список фільмів.
- Додамо функцію, яка відображатиме перелік фільмів, використовуючи спеціальну клавіатуру (`Keyboard Builder`), що
  зробить вибір простішим та зручнішим.
- Додамо меню з доступними командами, щоб користувачі мали можливість переходити між різними функціями бота.
- Створимо окрему команду, яка дозволить користувачам обирати фільм та отримувати детальну інформацію про нього.
- Визначимо, як отримати `id` фільму, коли користувач натискає кнопку, щоб завантажити всю потрібну інформацію з нашого
  джерела.
- Навчимося відображати деталі про фільм, такі як **назва, опис, рейтинг, жанр, актори та постер**.

[Повернутися до змісту](#зміст-конспекту)

---

## 2. Реалізація команди `/films`

> 💡 Модуль `commands.py` зберігає всі необхідні команди та їх фільтри.

Створимо файл `commands.py` у нашому проєкті.

```python
# Модуль, в якому оголошені всі необхідні команди та їх фільтри
from aiogram.filters import Command

FILMS_COMMAND = Command('films')
```

У файлі `bot.py` створимо новий обробник подій для команди `/films` (перегляд фільмів), код якого буде знаходитися під
обробником команди `/start`.

Обробник команди `/films` залишимо без реалізації (`pass`).

```python
@dp.message(Command("start"))
async def start(message: Message) -> None:
    await message.answer(
        f"Hello🖐, {html.bold(message.from_user.full_name)}!\n"
        "I'm your first Telegram Bot 🥳"
    )


@dp.message(FILMS_COMMAND)
async def films(message: Message) -> None:
    pass
```

Створимо файл `data.json` в корені нашого проєкту, який буде зберігати детальну інформацію про фільми.

Структура `json` формату [(JavaScript Object Notation)](https://uk.wikipedia.org/wiki/JSON) проста і зрозуміла та
ідеально підходить для реалізації схожих завдань.

Додамо список з фільмами до нашого `json` файлу.

```json
[
  {
    "name": "Inception",
    "description": "Злодій, який краде корпоративні секрети за допомогою технології спільного сну, отримує завдання вбудувати ідею в розум генерального директора.",
    "rating": 8.8,
    "genre": "Action, Adventure, Sci-Fi",
    "actors": [
      "Leonardo DiCaprio",
      "Joseph Gordon-Levitt",
      "Elliot Page"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/2/2e/Inception_%282010%29_theatrical_poster.jpg"
  },
  {
    "name": "Fight Club",
    "description": "Страждаючий безсонням офісний працівник і безтурботний виробник мила створюють підпільний бойовий клуб, який переростає в щось набагато більше.",
    "rating": 8.8,
    "genre": "Drama",
    "actors": [
      "Brad Pitt",
      "Edward Norton",
      "Helena Bonham Carter"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/f/fc/Fight_Club_poster.jpg"
  },
  {
    "name": "The Silence of the Lambs",
    "description": "Молодому агенту ФБР необхідно отримати допомогу від ув'язненого і маніпулятивного вбивці-канібала, щоб спіймати іншого серійного вбивцю, божевільного, який знімає шкіру зі своїх жертв.",
    "rating": 8.6,
    "genre": "Crime, Drama, Thriller",
    "actors": [
      "Jodie Foster",
      "Anthony Hopkins",
      "Lawrence A. Bonney"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/8/86/The_Silence_of_the_Lambs_poster.jpg"
  },
  {
    "name": "Interstellar",
    "description": "Команда дослідників подорожує через кротову нору в просторі у спробі забезпечити виживання людства.",
    "rating": 8.6,
    "genre": "Adventure, Drama, Sci-Fi",
    "actors": [
      "Matthew McConaughey",
      "Anne Hathaway",
      "Jessica Chastain"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/b/bc/Interstellar_film_poster.jpg"
  },
  {
    "name": "Jurassic Park",
    "description": "Мільярдер-ентузіаст створює парк розваг з клонованими динозаврами, але все йде не за планом, коли динозаври вириваються на волю і починають полювати на відвідувачів парку.",
    "rating": 8.1,
    "genre": "Adventure, Sci-Fi, Thriller",
    "actors": [
      "Sam Neill",
      "Laura Dern",
      "Jeff Goldblum"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/e/e7/Jurassic_Park_poster.jpg"
  },
  {
    "name": "The Dark Knight Rises",
    "description": "Бетмен повертається, щоб врятувати Готем від нового ворога Бейна, який загрожує знищити місто. Бетмен повинен подолати свої фізичні та емоційні випробування, щоб зупинити Бейна.",
    "rating": 8.4,
    "genre": "Action, Thriller",
    "actors": [
      "Christian Bale",
      "Tom Hardy",
      "Anne Hathaway"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/8/83/Dark_knight_rises_poster.jpg"
  },
  {
    "name": "The Avengers: Endgame",
    "description": "Після руйнівних подій, спричинених Таносом, залишки Месників та їхні союзники збираються разом, щоб відновити Всесвіт і зупинити Таноса раз і назавжди.",
    "rating": 8.4,
    "genre": "Action, Adventure, Sci-Fi",
    "actors": [
      "Robert Downey Jr.",
      "Chris Evans",
      "Mark Ruffalo"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/0/0d/Avengers_Endgame_poster.jpg"
  },
  {
    "name": "The Revenant",
    "description": "Історія виживання мисливця Х'ю Гласса, який залишається наодинці в дикій природі після нападу ведмедя. Він виживає завдяки своїй силі волі та бажанню помститися тим, хто покинув його померти.",
    "rating": 8.0,
    "genre": "Adventure, Drama, Thriller",
    "actors": [
      "Leonardo DiCaprio",
      "Tom Hardy",
      "Domhnall Gleeson"
    ],
    "poster": "https://upload.wikimedia.org/wikipedia/en/b/b6/The_Revenant_2015_film_poster.jpg"
  }
]
```

[Повернутися до змісту](#зміст-конспекту)

---

## 3. Функція `Keyboard builder`

> 💡 Щоб забезпечити модульність нашого застосунку, варто розбивати задачі на функції, які їх будуть виконувати.

Створимо функцію `get_films` для отримання списку фільмів:

- Створимо модуль `data.py` в корені нашого проєкту.
- Імпортуємо бібліотеку `json`.

```python
# Імпорт бібліотеки json
import json


#  Функція для отримання списку фільмів
def get_films(file_path: str = "data.json", film_id: int | None = None) -> list[dict] | dict:
    with open(file_path, 'r') as fp:
        films = json.load(fp)
        if film_id is not None and film_id < len(films):
            return films[film_id]
        return films
```

Створимо модуль `keyboards.py` в корені нашого проєкту:

- Імпортуємо функції `InlineKeyboardBuilder()` та `CallbackData()`.
- Створимо клас `FilmCallback` для реалізації властивостей `id` та `name`.
- Реалізуємо функцію для створення клавіатури `films_keyboard_markup`.

```python
# Імпортуємо необхідні модулі з бібліотеки aiogram
# InlineKeyboardBuilder використовується для створення клавіатури
# CallbackData використовується для створення структури даних, які будуть повертатися при натисканні кнопки
from aiogram.utils.keyboard import InlineKeyboardBuilder
from aiogram.filters.callback_data import CallbackData


# Створимо клас FilmCallback, який успадковує CallbackData
# Цей клас використовується для створення callback даних, які будуть повертатися при натисканні кнопки
# prefix="film" - префікс, який буде використовуватися для розпізнавання типу callback даних
# sep=";" - роздільник, який буде використовуватися для розділення даних в callback
class FilmCallback(CallbackData, prefix="film", sep=";"):
    id: int  # Ідентифікатор фільму
    name: str  # Назва фільму


# Функція для створення клавіатури з фільмами
def films_keyboard_markup(films_list: list[dict], offset: int | None = None, skip: int | None = None):
    # Створюємо об'єкт InlineKeyboardBuilder для побудови клавіатури
    builder = InlineKeyboardBuilder()
    # Установлюємо кількість кнопок у рядку та повторюємо цей шаблон
    builder.adjust(1, repeat=True)

    # Проходимо по списку фільмів та додаємо кнопки для кожного фільму
    for index, film_data in enumerate(films_list):
        # Створюємо об'єкт FilmCallback з даними фільму
        callback_data = FilmCallback(id=index, **film_data)
        # Додаємо кнопку з текстом назви фільму та callback даними
        builder.button(
            text=f"{callback_data.name}",  # Текст кнопки - назва фільму
            callback_data=callback_data.pack()  # Callback дані, які будуть повертатися при натисканні кнопки
        )

    # Установлюємо кількість кнопок у рядку та повторюємо цей шаблон
    builder.adjust(1, repeat=True)
    # Повертаємо клавіатуру у вигляді розмітки
    return builder.as_markup()

```

Необхідно внести зміни в код модуля `bot.py`:

- Додаємо імпорт створених функцій.
- Реалізуємо обробник команди `/films`
- Необхідно доповнити функцію `films()`, яку ми створили без реалізації.

```python
# Додаємо імпорт створених функцій.
from data import get_films
from keyboards import films_keyboard_markup
```

```python
# Реалізуємо обробник команди `/films`
@dp.message(FILMS_COMMAND)
async def films(message: Message) -> None:
    data = get_films()
    markup = films_keyboard_markup(films_list=data)
    await message.answer(
        f"<b>Список фільмів: 🎞</b>\nОберіть фільм, щоб отримати інформацію про нього.",
        reply_markup=markup
    )
```

Перевіримо, як тепер виглядає файл `bot.py`

```python
import asyncio
import logging
import sys
from config import BOT_TOKEN as TOKEN

from aiogram import Bot, Dispatcher, html
from aiogram.client.default import DefaultBotProperties
from aiogram.enums import ParseMode
from aiogram.filters import CommandStart, Command
from aiogram.types import Message
from commands import FILMS_COMMAND
from data import get_films
from keyboards import films_keyboard_markup

dp = Dispatcher()


@dp.message(Command("start"))
async def start(message: Message) -> None:
    await message.answer(
        f"Hello🖐, {html.bold(message.from_user.full_name)}!\n"
        "I'm your first Telegram Bot 🥳"
    )


@dp.message(FILMS_COMMAND)
async def films(message: Message) -> None:
    data = get_films()
    markup = films_keyboard_markup(films_list=data)
    await message.answer(
        f"Перелік фільмів. Натисніть на назву фільму для отримання деталей.",
        reply_markup=markup
    )


async def main() -> None:
    # Initialize Bot instance with default bot properties which will be passed to all API calls
    bot = Bot(token=TOKEN, default=DefaultBotProperties(parse_mode=ParseMode.HTML))

    # And the run events dispatching
    await dp.start_polling(bot)


if __name__ == "__main__":
    logging.basicConfig(level=logging.INFO, stream=sys.stdout)
    asyncio.run(main())
```

[Повернутися до змісту](#зміст-конспекту)

---

## 4. Створення `Menu` для TelegramBot

> 💡 Створимо екземпляр класу `BotCommand`, щоб додати необхідну команду, та опис функції цієї команди, у меню.

Необхідно внести зміни в код модуля `commands.py`.

```python
# Модуль, в якому оголошені всі необхідні команди та їх фільтри
from aiogram.filters import Command
from aiogram.types.bot_command import BotCommand

FILMS_COMMAND = Command('films')
START_COMMAND = Command('start')

FILMS_BOT_COMMAND = BotCommand(command='films', description="Перегляд списку фільмів")
START_BOT_COMMAND = BotCommand(command='start', description="Почати розмову")
```

Необхідно імпортувати створені команди в модуль `bot.py`.

```python
# Old string
from commands import FILMS_COMMAND

# New string
from commands import (FILMS_COMMAND, START_COMMAND, FILMS_BOT_COMMAND, START_BOT_COMMAND)
```

Також додамо створені команди у функцію `main()` модуля `bot.py`

```python
# Головна асинхронна функція для запуску бота
async def main() -> None:
    # Ініціалізуємо екземпляр бота з токеном та властивостями за замовчуванням
    bot = Bot(token=TOKEN, default=DefaultBotProperties(parse_mode=ParseMode.HTML))
    # Встановлюємо команди бота
    await bot.set_my_commands([FILMS_BOT_COMMAND, START_BOT_COMMAND])

    # Запускаємо цикл опитування для отримання оновлень
    await dp.start_polling(bot)
```

Необхідно **запустити** нашу програму та **перевірити зміни**, які ми додали.

Бажано це робити з **Desktop** версії **Telegram**.

[Повернутися до змісту](#зміст-конспекту)

---

## 5. Реалізація перегляду інформації про фільми

> 💡 Для перегляду детальної інформації про обраний фільм необхідно додати обробника події `FilmCallback`, для реалізації
> можливості натискання на кнопку з фільмом.

Змінимо рядок імпорту клавіатури в файлі `bot.py`

```python
# Old string
from keyboards import films_keyboard_markup

# New string
from keyboards import films_keyboard_markup, FilmCallback
```

Необхідно імпортувати функцію `CallbackQuery()` для обробки подій з клавіатури.

Змінимо наступний рядок імпорту в файлі `bot.py`

```python
# Old string
from aiogram.types import Message

# New string
from aiogram.types import Message, CallbackQuery
```

Також додамо **функцію** `callback_film()`

Реалізуємо повідомлення з інформацією про фільм та отримання необхідного фільму по `id`.

```python
@dp.callback_query(FilmCallback.filter())
async def callback_film(callback: CallbackQuery, callback_data: FilmCallback) -> None:
    # Отримуємо ID фільму з даних зворотного виклику
    film_id = callback_data.id
    # Отримуємо дані про конкретний фільм за його ID
    film_data = get_films(film_id=film_id)
    # Створюємо об'єкт фільму
    film = Film(**film_data)

    # Формуємо текст повідомлення з деталями про фільм
    text = f"<b>Фільм:</b> {film.name}\n" \
           f"<b>Опис:</b> {film.description}\n" \
           f"<b>Рейтинг:</b> {film.rating}\n" \
           f"<b>Жанр:</b> {film.genre}\n" \
           f"<b>Актори:</b> {', '.join(film.actors)}\n"

    # Відправляємо фото з постером фільму та текстом з деталями
    await callback.message.answer_photo(
        caption=text,
        photo=URLInputFile(
            film.poster,
            filename=f"{film.name}_poster.{film.poster.split('.')[-1]}"
        )
    )
```

Створимо файл `models.py` з класом `Film(BaseModel)`всередині, для якісного відображення інформації по кожному фільму.

```python
from pydantic import BaseModel


class Film(BaseModel):
    name: str
    description: str
    rating: float
    genre: str
    actors: list[str]
    poster: str
```

Додамо наступні `imports` в файл `bot.py`

```python
from models import Film
```

```python
# Old string
from aiogram.types import Message, CallbackQuery

# New string
from aiogram.types import Message, CallbackQuery, URLInputFile
```

Запускаємо нашу програму та перевіряємо зміни в **TelegramBot**.

[Повернутися до змісту](#зміст-конспекту)

---

## 6. Підведення підсумків 🚀

> Сьогодні ми навчилися:

- Створювати команди для перегляду списку фільмів у **TelegramBot**.
- Додавати функціональну клавіатуру для зручності користувачів.
- Реалізовувати функцію перегляду детальної інформації про фільми.

Тепер наш **TelegramBot** може не тільки вітати користувачів, але й надавати їм корисну інформацію про фільми.

[Повернутися до змісту](#зміст-конспекту)

---
