# ✅ Урок 42: Створення команд для TelegramBot

---
<img src="main_image.png" alt="pygame" width="1500">

## Зміст уроку:

1. [Сьогодні на уроці](#1-сьогодні-на-уроці)
2. [Реалізація команди `/films`](#2-реалізація-команди-films)
3. [Файл `data.json`](#3-файл-datajson)
4. [Модуль `data.py`](#4-модуль-datapy)
5. [Модуль `keyboards.py`](#5-модуль-keyboardspy)
6. [Реалізація `Menu`](#6-реалізація-menu)
7. [Реалізація перегляду детальної інформації про фільми](#7-реалізація-перегляду-детальної-інформації-про-фільми)
8. [Модуль `models.py`](#8-модуль-modelspy)
9. [Підведення підсумків 🚀](#9-підведення-підсумків-)

> 🔗 Useful Links:

- [JSON (JavaScript Object Notation)](https://uk.wikipedia.org/wiki/JSON)

---

## 1. Сьогодні на уроці

> 💡 На попередньому уроці ми створили базового **TelegramBot** та налаштували команду `/start`.

На цьому уроці ми навчимося створювати команди для перегляду списку фільмів у **TelegramBot**, а також реалізувати
функцію для відображення детальної інформації про кожен фільм.

Користувачі зможуть переглядати список доступних фільмів, а також отримати більше інформації про кожен з них (**назву
фільму, опис, рейтинг, жанр, список акторів та постер**).

### 🧩 Основні завдання на сьогодні:

- За допомогою класу `Command` створити команду `/films`, яка дозволить користувачам переглядати список фільмів.
- Додати функцію, яка відображатиме перелік фільмів, використовуючи спеціальну клавіатуру (`Keyboard Builder`), що
  зробить вибір простішим та зручнішим.
- Додати **меню** з доступними командами, щоб користувачі мали можливість переходити між різними функціями бота.
- Створити окрему команду, яка дозволить користувачам обирати фільм та отримувати детальну інформацію про нього.

[Повернутися до змісту](#зміст-конспекту)

---

## 2. Реалізація команди `/films`

> 💡 Модуль `commands.py` буде зберігати всі необхідні команди в нашому проекті.

Створимо файл `commands.py`.

```python
from aiogram.filters import Command

FILMS_COMMAND = Command('films')
```

У файлі `bot.py` створимо нову функцію `films()` - обробник подій для команди `/films` (перегляд фільмів).

Функцію `films()` залишимо без реалізації (`pass`).

```python
# Обробник для команди /start
@dp.message(Command("start"))
async def start(message: Message) -> None:
    await message.answer(
        f"Hello🖐, {html.bold(message.from_user.full_name)}!\n"
        "I'm your first Telegram Bot 🥳"
    )


# Обробник для команди /films
@dp.message(FILMS_COMMAND)
async def films(message: Message) -> None:
    pass
```

[Повернутися до змісту](#зміст-конспекту)

---

## 3. Файл `data.json`

> 💡 Створимо файл `data.json` в корені нашого проєкту, який буде зберігати детальну інформацію про фільми.

Структура `json` формату [(JavaScript Object Notation)](https://uk.wikipedia.org/wiki/JSON) проста і зрозуміла та
ідеально підходить для реалізації схожих завдань.

Додамо список з фільмами до файлу `data.json`

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

## 4. Модуль `data.py`

> 💡 Щоб забезпечити модульність нашого застосунку, варто розбивати задачі на функції, які їх будуть виконувати.

Створимо модуль `data.py` в корені нашого проєкту:

- Створимо функцію `get_films()` для отримання списку фільмів.
- Імпортуємо в модуль `data.py` бібліотеку `json`

```python
import json


#  Функція для отримання списку фільмів
def get_films(file_path: str = "data.json", film_id: int | None = None) -> list[dict] | dict:
    with open(file_path, 'r') as fp:
        films = json.load(fp)
        if film_id is not None and film_id < len(films):
            return films[film_id]
        return films
```

[Повернутися до змісту](#зміст-конспекту)

---

## 5. Модуль `keyboards.py`

Створимо модуль `keyboards.py` в корені нашого проєкту:

Імпортуємо класи `InlineKeyboardBuilder` та `CallbackData`:

- Клас `InlineKeyboardBuilder` використовується для створення інтерактивних кнопок у **Telegram** ботах, які можуть
  бути прикріплені до повідомлень і виконувати різні дії при натисканні.
- Клас `CallbackData` допомагає створювати та обробляти дані, які повертаються при натисканні на кнопки інтерактивної
  клавіатури.

Створимо клас `FilmCallback` для реалізації властивостей `id` та `name`:

- Клас `FilmCallback` використовується для створення об'єктів, які містять дані про фільми (`id`, `name`).
- `id`: Ідентифікатор фільму (`int`).
- `name`: Назва фільму (`str`).

Реалізуємо функцію `films_keyboard_markup()`:

- Функція `films_keyboard_markup` створює інтерактивну клавіатуру з кнопками для кожного фільму зі списку `films_list`.
- `films_list` - це список словників, де кожен словник містить інформацію про фільм.
- `offset` та `skip` - це необов'язкові параметри, які можуть використовуватися для пагінації або пропуску певних
  елементів у списку фільмів.

```python
from aiogram.utils.keyboard import InlineKeyboardBuilder
from aiogram.filters.callback_data import CallbackData


class FilmCallback(CallbackData, prefix="film", sep=";"):
    id: int
    name: str


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

    # Повертаємо клавіатуру у вигляді розмітки
    builder.adjust(1, repeat=True)
    return builder.as_markup()
```

Функція проходить по списку фільмів, створює кнопку для кожного фільму з його назвою та додає цю кнопку до клавіатури.

Клавіатура повертається у вигляді розмітки, яку отримує користувач, як результат на свій запит.

Наступним кроком, необхідно внести зміни в код модуля `bot.py`:

- Додаємо імпорт створених функцій `get_films()` та `films_keyboard_markup()`.
- Додаємо імпорт `FILMS_COMMAND` з модуля `commands.py`.
- Реалізуємо функцію-обробник команди `/films`
- Також, необхідно доповнити функцію `films()`, яку ми створили без реалізації.

```python
# Додаємо імпорт створених функцій
from commands import FILMS_COMMAND
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
        f"<b>Список фільмів: 🎬</b>\nОберіть фільм, щоб отримати інформацію про нього.",
        reply_markup=markup
    )
```

Перевіримо, як тепер виглядає файл `bot.py`

### Code ✅

```python
# Імпортуємо необхідні модулі
import asyncio  # Для асинхронного програмування
import logging  # Для логування подій
import sys  # Для доступу до деяких змінних та функцій, пов'язаних з інтерпретатором Python

# Імпортуємо токен бота з конфігураційного файлу
from config import BOT_TOKEN as TOKEN

# Імпортуємо необхідні класи та функції з бібліотеки aiogram
from aiogram import Bot, Dispatcher, html
from aiogram.client.default import DefaultBotProperties
from aiogram.enums import ParseMode
from aiogram.filters import Command
from aiogram.types import Message
from commands import FILMS_COMMAND
from data import get_films
from keyboards import films_keyboard_markup

# Ініціалізуємо диспетчер для обробки оновлень
dp = Dispatcher()


# Обробник для команди /start
@dp.message(Command("start"))
async def start(message: Message) -> None:
    await message.answer(
        f"Hello🖐, {html.bold(message.from_user.full_name)}!\n"
        "I'm your first Telegram Bot 🥳"
    )


# Обробник для команди /films
@dp.message(FILMS_COMMAND)
async def films(message: Message) -> None:
    data = get_films()
    markup = films_keyboard_markup(films_list=data)
    await message.answer(
        f"<b>Список фільмів: 🎬</b>\nОберіть фільм, щоб отримати інформацію про нього.",
        reply_markup=markup
    )


# Головна асинхронна функція для запуску бота
async def main() -> None:
    # Ініціалізуємо екземпляр бота з токеном та властивостями за замовчуванням
    bot = Bot(token=TOKEN, default=DefaultBotProperties(parse_mode=ParseMode.HTML))

    # Запускаємо цикл опитування для отримання оновлень
    await dp.start_polling(bot)


# Перевіряємо, чи скрипт запускається напряму
if __name__ == "__main__":
    # Налаштовуємо базове логування для виведення інформаційних повідомлень у стандартний потік виведення
    logging.basicConfig(level=logging.INFO, stream=sys.stdout)
    # Запускаємо головну асинхронну функцію
    asyncio.run(main())
```

[Повернутися до змісту](#зміст-конспекту)

---

## 6. Реалізація `Menu`

Створимо `Menu` в нашому **TelegramBot** та додамо в нього команду `/films`.

```python
from aiogram.filters import Command
from aiogram.types.bot_command import BotCommand

START_COMMAND = Command('start')
FILMS_COMMAND = Command('films')

BOT_COMMANDS = [
    BotCommand(command='start', description="Почати розмову"),
    BotCommand(command='films', description="Перегляд списку фільмів")
]
```

- Клас `Command` використовується для створення об'єктів, що представляють команди, які бот може отримувати від
  користувачів. Ці об'єкти використовуються для фільтрації повідомлень, щоб визначити, яка функція має бути викликана
  при отриманні певної команди.
- Клас `BotCommand` використовується для визначення команд, які будуть відображатися в інтерфейсі Telegram. Кожна
  команда має назву та опис, який допомагає користувачам зрозуміти, для чого вона призначена.
- Об'єкти `FILMS_COMMAND` та `START_COMMAND` представляють команди `/films` та `/start`. Вони використовуються для
  фільтрації повідомлень у функціях-обробниках, щоб визначити, яка функція має бути викликана при отриманні відповідної
  команди від користувача.
- Об'єкти `BOT_COMMANDS` визначають команди, що будуть відображатися в інтерфейсі Telegram.
  Кожен об'єкт має два поля:
    - `command`: Назва команди, яка буде введена користувачем (наприклад, `/films` або `/start`).
    - `description`: Опис команди, який допомагає користувачам зрозуміти, для чого вона призначена.

Необхідно змінити код в модулі `commands.py`:

```python
# Old code
from aiogram.filters import Command

FILMS_COMMAND = Command('films')
```

```python
# New code
from aiogram.filters import Command
from aiogram.types.bot_command import BotCommand

START_COMMAND = Command('start')
FILMS_COMMAND = Command('films')

BOT_COMMANDS = [
    BotCommand(command='start', description="Почати розмову"),
    BotCommand(command='films', description="Перегляд списку фільмів")
]
```

Класи `Command` і `BotCommand` можуть здатися схожими, але вони виконують різні функції в контексті роботи з бібліотекою
aiogram для **Telegram** ботів:

- `Command` використовується для фільтрації повідомлень. Коли бот отримує повідомлення від користувача, він може
  визначити, яка функція-обробник має бути викликана на основі отриманої команди.
- `BotCommand` використовується для налаштування списку команд, які будуть відображатися в інтерфейсі Telegram. Це
  допомагає користувачам зрозуміти, які команди доступні і як їх використовувати.

Необхідно імпортувати створені команди в модуль `bot.py`:

```python
# Old string
from commands import FILMS_COMMAND

# New string
from commands import FILMS_COMMAND, START_COMMAND, BOT_COMMANDS
```

Також додамо створені команди в головну функцію `main()` модуля `bot.py`:

```python
# Old code
async def main() -> None:
    # Ініціалізуємо екземпляр бота з токеном та властивостями за замовчуванням
    bot = Bot(token=TOKEN, default=DefaultBotProperties(parse_mode=ParseMode.HTML))

    # Запускаємо цикл опитування для отримання оновлень
    await dp.start_polling(bot)
```

```python
# New code
async def main() -> None:
    # Ініціалізуємо екземпляр бота з токеном та властивостями за замовчуванням
    bot = Bot(token=TOKEN, default=DefaultBotProperties(parse_mode=ParseMode.HTML))
    # Встановлюємо команди бота
    await bot.set_my_commands(BOT_COMMANDS)

    # Запускаємо цикл опитування для отримання оновлень
    await dp.start_polling(bot)
```

Необхідно **запустити** нашу програму та **перевірити зміни**, які ми додали.

Бажано це робити з **Desktop** версії **Telegram**.

[Повернутися до змісту](#зміст-конспекту)

---

## 7. Реалізація перегляду детальної інформації про фільми

> 💡 `FilmCallback` - це клас, який використовується для обробки даних зворотного виклику (`callback data`), коли
> користувач натискає на кнопку інтерактивної клавіатури.

- Клас `FilmCallback` допомагає визначити, яка дія має бути виконана при натисканні кнопки.
- Наприклад, `FilmCallback` може містити інформацію про `id` фільму, який обрав користувач.

Змінимо наступний рядок імпорту клавіатури у файлі `bot.py`:

```python
# Old string
from keyboards import films_keyboard_markup

# New string
from keyboards import films_keyboard_markup, FilmCallback
```

> 💡 `CallbackQuery` - це клас, який представляє зворотний виклик від інтерактивної клавіатури.

- Клас`CallbackQuery` містить інформацію про те, яка кнопка була натиснута та які дані пов'язані з цим натисканням.
- Використовується для обробки дій користувача, пов'язаних з інтерактивними елементами, та виконання необхідних дій у
  відповідь.

Змінимо наступний рядок імпорту у файлі `bot.py`:

```python
# Old string
from aiogram.types import Message

# New string
from aiogram.types import Message, CallbackQuery
```

Реалізуємо **функцію** `callback_film()`, яка повертає повідомлення з інформацією про фільм.

Наступний код необхідно додати в модуль `bot.py`:

```python
# Обробник зворотного виклику для фільмів
@dp.callback_query(FilmCallback.filter())
async def callback_film(callback: CallbackQuery, callback_data: FilmCallback) -> None:
    # Отримуємо ID фільму з даних зворотного виклику
    film_id = callback_data.id
    # Отримуємо дані про конкретний фільм за його ID
    film_data = get_films(film_id=film_id)
    # Створюємо об'єкт фільму
    film = Film(**film_data)

    # Формуємо текст повідомлення з деталями про фільм
    text = (
        f"<b>Фільм:</b> {film.name}\n"
        f"<b>Опис:</b> {film.description}\n"
        f"<b>Рейтинг:</b> {film.rating}\n"
        f"<b>Жанр:</b> {film.genre}\n"
        f"<b>Актори:</b> {', '.join(film.actors)}\n"
    )

    # Відправляємо фото з постером фільму та текстом з деталями
    await callback.message.answer_photo(
        caption=text,
        photo=URLInputFile(
            film.poster,
            filename=f"{film.name}_poster.{film.poster.split('.')[-1]}"
        )
    )
```

### 🧩 Розглянемо кожен рядок цього коду:

### Декоратор:

```python
@dp.callback_query(FilmCallback.filter())
```

- `@dp.callback_query` - це декоратор, який використовується для обробки зворотних викликів (`callback queries`) у
  **Telegram** ботах. Він вказує, що функція, яка йде після нього, буде викликана, коли користувач натискає на кнопку
  інтерактивної клавіатури.
- `FilmCallback.filter()` - це фільтр, який вказує, що функція буде викликана тільки тоді, коли зворотний виклик
  відповідає певним критеріям, визначеним у класі `FilmCallback`.

### Оголошення функції:

```python
async def callback_film(callback: CallbackQuery, callback_data: FilmCallback) -> None:
```

- `async def` - це оголошення асинхронної функції. Асинхронні функції дозволяють виконувати операції, які можуть
  виконуватись певний час (наприклад, мережеві запити), не блокуючи виконання інших частин програми.
- `callback_film` - це ім'я функції, яка буде обробляти зворотний виклик.
- `callback: CallbackQuery` - це параметр функції, який представляє об'єкт зворотного виклику, отриманого від
  користувача.
- `CallbackQuery` - це клас з бібліотеки aiogram, який містить інформацію про зворотний виклик.
- `callback_data: FilmCallback` - це параметр функції, який представляє дані, пов'язані з зворотним викликом.
- `FilmCallback` - це клас, який визначає структуру цих даних.
- `-> None` - це вказує, що функція не повертає жодного значення.

### Отримання даних про фільм:

```python
film_id = callback_data.id
film_data = get_films(film_id=film_id)
film = Film(**film_data)
```

- `film_id = callback_data.id`: Отримуємо `id` фільму з даних зворотного виклику.
- `film_data = get_films(film_id=film_id)`: Викликаємо функцію `get_films` для отримання даних про конкретний фільм за
  його `id`.
- `film = Film(**film_data)`: Створюємо об'єкт фільму, передаючи отримані дані у конструктор класу `Film`.

### Формування тексту повідомлення:

```python
text = (
    f"<b>Фільм:</b> {film.name}\n"
    f"<b>Опис:</b> {film.description}\n"
    f"<b>Рейтинг:</b> {film.rating}\n"
    f"<b>Жанр:</b> {film.genre}\n"
    f"<b>Актори:</b> {', '.join(film.actors)}\n"
)
```

- Тут формується текст повідомлення з деталями про фільм, використовуючи дані з об'єкта `film`.
- Текст містить назву фільму, опис, рейтинг, жанр та список акторів.

### Відправлення повідомлення з фото:

```python
await callback.message.answer_photo(
    caption=text,
    photo=URLInputFile(
        film.poster,
        filename=f"{film.name}_poster.{film.poster.split('.')[-1]}"
    )
)
```

- `await callback.message.answer_photo` - це метод, який використовується для відправлення фото з текстом у відповідь на
  зворотний виклик.
- `caption=text:` Текст, який буде відображатися разом з фото.
- `photo=URLInputFile(...)` - це об'єкт, який представляє фото, яке буде відправлено. Він містить URL постеру фільму та
  ім'я файлу, яке формується на основі назви фільму та розширення файлу постеру.

Отже, цей код обробляє зворотний виклик від користувача, отримує дані про фільм, формує текст з деталями про фільм і
відправляє користувачу фото з постером фільму разом з текстом.

[Повернутися до змісту](#зміст-конспекту)

---

## 8. Модуль `models.py`

Створимо модуль `models.py` з класом `Film()` всередині, для якісного відображення інформації по кожному фільму.

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

Клас `Film` - це модель даних, яка представляє інформацію про фільм та має наступні поля:

- `name`: Назва фільму (`str`).
- `description`: Опис фільму (`str`).
- `rating`: Рейтинг фільму (`float`).
- `genre`: Жанр фільму (`str`).
- `actors`: Список акторів, які знімалися у фільмі (`list[str]`).
- `poster`: URL постеру фільму (`str`).

Модель класу `Film(BaseModel)` дозволяє легко створювати об'єкти фільмів з валідацією даних, що допомагає уникнути
помилок, пов'язаних з неправильними типами даних.

> 💡 Бібліотека `pydantic` використовується для валідації даних та управління налаштуваннями за допомогою анотацій типів
> Python.

> 💡 Клас `BaseModel` - це базовий клас з бібліотеки `pydantic`, який використовується для створення моделей даних, та
> дозволяє визначити структуру даних та автоматично виконує валідацію даних.

### Додамо наступні рядки імпорту у файл `bot.py`:

```python
from models import Film
```

Імпорт `from models import Film` імпортує клас `Film` з файлу `models.py`, щоб його можна було використовувати в
`bot.py` для створення об'єктів фільмів.

```python
# Old string
from aiogram.types import Message, CallbackQuery

# New string
from aiogram.types import Message, CallbackQuery, URLInputFile
```

Клас `URLInputFile` використовується для відправки файлів за **URL** та дозволяє відправляти зображення, документи та
інші типи файлів, які доступні за певним **URL**.

Запускаємо нашу програму та перевіряємо зміни в **TelegramBot**.

[Повернутися до змісту](#зміст-конспекту)

---

## 9. Підведення підсумків 🚀

> На цьому уроці ми вивчили наступні теми:

- Створили команди для перегляду списку фільмів у **TelegramBot**.
- Додали функціональну клавіатуру для зручності користувачів.
- Реалізували функцію перегляду детальної інформації про фільми.

Тепер наш **TelegramBot** може не тільки вітати користувачів, але й надавати їм корисну інформацію про фільми.

[Повернутися до змісту](#зміст-конспекту)

---
