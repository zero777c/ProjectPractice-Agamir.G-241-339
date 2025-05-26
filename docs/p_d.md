# Документация
## Базовая часть
Для начала, скачиваем Git и Hugo, для того, чтобы сделать статический веб-сайт. После чего, заходим на сайт с темами для сайта Hugo и ищем устраивающую нас тему с интересным дизайном. После этого, заходим в PowerShell и начинаем создавать сайт согласно курсу Hugo Quick Start: https://gohugo.io/getting-started/quick-start/.

После выполнения курса Hugo Quick Start, вводим команду hugo, сайт сохраняется в папку public. После этого загружаем файлы на GitHub в папку site_folder.
# Вариативная часть
## Практическая реализация Telegram-бота для напоминаний на Python
## 1. Выбор стека технологий
    Язык программирования: Python 3.9+
    
    Фреймворк для бота: python-telegram-bot (или aiogram)

    База данных: SQLite (для хранения напоминаний)

    Дополнительные библиотеки:

    apscheduler – для планирования задач.

    datetime – работа с датами и временем.

    logging – логирование действий бота.

 ## 2. Исследование предметной области
    Перед разработкой изучите:

    Как работает Telegram Bot API.

    Как боты обрабатывают команды и callback-запросы.

    Как хранить данные пользователей (SQLite, JSON).

    Как планировать задачи.

    Пример исследования:

    markdown
    1. **Telegram Bot API**: Позволяет создавать ботов через `@BotFather`.
    2. **Long Polling vs Webhooks**: Для учебного проекта используем Long Polling.
    3. **Хранение данных**: SQLite – лёгкая и встроенная в Python БД.
    4. **Планировщик задач**: `apscheduler` умеет выполнять функции по расписанию.
## 3. Техническое руководство по созданию бота
    Шаг 1: Создание бота в Telegram
    Откройте Telegram, найдите @BotFather.

    Выполните команду /newbot, задайте имя бота.

    Получите API-токен.

    Шаг 2: Настройка проекта
    bash
    mkdir reminder-bot
    cd reminder-bot
    python -m venv venv
    source venv/bin/activate  # Активация виртуального окружения
    pip install python-telegram-bot apscheduler python-dotenv
    Шаг 3: Базовый код бота (bot.py)
    python
    import os
    from telegram import Update, Bot
    from telegram.ext import Updater, CommandHandler, CallbackContext
    from apscheduler.schedulers.background import BackgroundScheduler
    from datetime import datetime
    
    TOKEN = os.getenv("TELEGRAM_TOKEN")
    bot = Bot(token=TOKEN)

    # База данных (упрощённый вариант)
    reminders = {}

def start(update: Update, context: CallbackContext) -> None:
    update.message.reply_text("Привет! Я бот для напоминаний. Используй /remind <время> <текст>")

def set_reminder(update: Update, context: CallbackContext) -> None:
    chat_id = update.message.chat_id
    args = context.args
    if len(args) < 2:
        update.message.reply_text("Используйте: /remind <время> <текст>")
        return
    
    time_str, *text = args
    reminder_text = " ".join(text)
    
    try:
        reminder_time = datetime.strptime(time_str, "%H:%M").time()
        reminders[chat_id] = (reminder_time, reminder_text)
        update.message.reply_text(f"Напоминание установлено на {time_str}: {reminder_text}")
    except ValueError:
        update.message.reply_text("Неверный формат времени. Используйте HH:MM")

def check_reminders():
    now = datetime.now().time().strftime("%H:%M")
    for chat_id, (reminder_time, text) in reminders.items():
        if reminder_time.strftime("%H:%M") == now:
            bot.send_message(chat_id, f"⏰ Напоминание: {text}")

scheduler = BackgroundScheduler()
scheduler.add_job(check_reminders, "interval", minutes=1)
scheduler.start()

def main():
    updater = Updater(TOKEN)
    dp = updater.dispatcher
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(CommandHandler("remind", set_reminder))
    updater.start_polling()
    updater.idle()

if __name__ == "__main__":
    main()
Шаг 4: Запуск бота
bash
echo "TELEGRAM_TOKEN=ваш_токен" > .env
python bot.py
## 4. Визуализация (3+ иллюстрации)
![Снимок экрана 2025-05-27 021647](https://github.com/user-attachments/assets/d6a9ce7c-5b7c-44e8-b326-7a895113b8ba)

![Снимок экрана 2025-05-27 021641](https://github.com/user-attachments/assets/1902b499-b98d-44e4-ab8c-336633917984)

Схема архитектуры бота
Architecture
Описание: Бот использует Telegram API, хранит данные в SQLite и проверяет напоминания каждую минуту.

Пример работы бота
Bot Example
Пользователь отправляет /remind 12:30 Позвонить маме, бот подтверждает напоминание.

Диаграмма состояний (UML)

Diagram
Code
## 5. Дополнения и улучшения (творческий пункт)

Поддержка нескольких напоминаний у одного пользователя.

Добавить БД SQLite для хранения напоминаний между перезапусками.

Web-интерфейс (Flask) для управления напоминаниями.

Интеграция с Google Calendar через API.

Пример обновления кода для SQLite:

python
import sqlite3

def init_db():
    conn = sqlite3.connect("reminders.db")
    cursor = conn.cursor()
    cursor.execute("CREATE TABLE IF NOT EXISTS reminders (chat_id INTEGER, time TEXT, text TEXT)")
    conn.commit()
    conn.close()
## Итоговый отчёт
    Хронология работы:

    Неделя 1: Исследование Telegram Bot API.

    Неделя 2: Разработка MVP.

    Неделя 3: Добавление SQLite и тестирование.

    Индивидуальные планы участников (если проект командный).
