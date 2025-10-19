import telebot
import random

TOKEN = ''  # Замени на свой токен из BotFather
bot = telebot.TeleBot(TOKEN)

# Списки для шуток и фактов (можно расширить)
jokes = [
    "Почему курица перешла дорогу? Чтобы попасть на другую сторону! 😂",
    "Что сказал один ноль другому? Ты крутой! 😎",
    "Почему смеются йоги? Потому что они в позе лотоса! 🧘‍♂️"
]

facts = [
    "Дельфины могут распознавать себя в зеркале, как люди.",
    "Свет от Солнца доходит до Земли за 8 минут.",
    "У осьминогов три сердца!"
]

# Игра в угадывание числа (простая, без сохранения состояния)
secret_number = random.randint(1, 10)

@bot.message_handler(commands=['start'])
def start(message):
    bot.reply_to(message, "Привет! Я крутой Telegram-бот с классными командами. Используй /help для списка.")

@bot.message_handler(commands=['help'])
def help_command(message):
    bot.reply_to(message, "Команды:\n/start - Приветствие\n/help - Справка\n/echo [текст] - Повторить текст\n/info - Информация о боте\n/joke - Случайная шутка\n/fact - Случайный факт\n/dice - Бросить кубик (1-6)\n/rps [rock/paper/scissors] - Игра камень-ножницы-бумага\n/guess [число] - Угадай число от 1 до 10 (новое число каждый раз)")

@bot.message_handler(commands=['echo'])
def echo(message):
    try:
        text_to_echo = message.text.split(' ', 1)[1]
        bot.reply_to(message, f"Эхо: {text_to_echo}")
    except IndexError:
        bot.reply_to(message, "Используй /echo [текст], например /echo Привет!")

@bot.message_handler(commands=['info'])
def info(message):
    bot.reply_to(message, "Я бот на Python . Добавил классные команды для веселья! Можешь форкнуть и доработать.")

@bot.message_handler(commands=['joke'])
def joke(message):
    bot.reply_to(message, random.choice(jokes))

@bot.message_handler(commands=['fact'])
def fact(message):
    bot.reply_to(message, random.choice(facts))

@bot.message_handler(commands=['dice'])
def dice(message):
    # Используем встроенный дайс Telegram (анимированный кубик)
    bot.send_dice(message.chat.id)

@bot.message_handler(commands=['rps'])
def rps(message):
    try:
        user_choice = message.text.split(' ', 1)[1].lower()
        if user_choice not in ['rock', 'paper', 'scissors']:
            bot.reply_to(message, "Выбери rock, paper или scissors!")
            return
        bot_choice = random.choice(['rock', 'paper', 'scissors'])
        if user_choice == bot_choice:
            result = "Ничья! 🤝"
        elif (user_choice == 'rock' and bot_choice == 'scissors') or \
             (user_choice == 'paper' and bot_choice == 'rock') or \
             (user_choice == 'scissors' and bot_choice == 'paper'):
            result = "Ты выиграл! 🎉"
        else:
            result = "Я выиграл! 😏"
        bot.reply_to(message, f"Твой выбор: {user_choice.capitalize()}\nМой выбор: {bot_choice.capitalize()}\n{result}")
    except IndexError:
        bot.reply_to(message, "Используй /rps [rock/paper/scissors], например /rps rock")

@bot.message_handler(commands=['guess'])
def guess(message):
    global secret_number  # Новое число каждый раз для простоты
    try:
        user_guess = int(message.text.split(' ', 1)[1])
        if user_guess == secret_number:
            bot.reply_to(message, f"Угадал! Число было {secret_number}. Новое число сгенерировано! 🎯")
            secret_number = random.randint(1, 10)  # Генерируем новое
        elif user_guess < secret_number:
            bot.reply_to(message, "Больше! 🔼")
        else:
            bot.reply_to(message, "Меньше! 🔽")
    except (IndexError, ValueError):
        bot.reply_to(message, "Используй /guess [число от 1 до 10], например /guess 5")

# Обработчик для любых текстовых сообщений (не команд)
@bot.message_handler(func=lambda message: True)
def handle_text(message):
    bot.reply_to(message, "Не понял команду. Попробуй /help для списка!")

bot.polling()
