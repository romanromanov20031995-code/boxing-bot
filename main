import telebot
import os

TOKEN = os.getenv("TOKEN")
ADMIN_ID = int(os.getenv("ADMIN_ID"))

bot = telebot.TeleBot(TOKEN)

user_data = {}

@bot.message_handler(commands=['start'])
def start(message):
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add("🥊 Курс", "📩 Связаться")

    bot.send_message(
        message.chat.id,
        "🥊 Welcome to Almaskhan Boxing System\n\n"
        "If you’re just starting boxing and don’t know where to begin — you’re in the right place.\n"
        "Choose an option below 👇",
        reply_markup=markup
    )

@bot.message_handler(func=lambda message: message.text == "🥊 Курс")
def course(message):
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add("💳 Купить доступ", "⏳ Мои уроки (скоро)")
    bot.send_message(message.chat.id, "Выберите действие:", reply_markup=markup)

@bot.message_handler(func=lambda message: message.text == "💳 Купить доступ")
def buy(message):
    markup = telebot.types.ReplyKeyboardMarkup(resize_keyboard=True)
    markup.add("📤 Отправить чек")

    bot.send_message(
        message.chat.id,
        "Инструкция по оплате:\n\nПосле оплаты нажмите кнопку ниже 👇",
        reply_markup=markup
    )

@bot.message_handler(func=lambda message: message.text == "📤 Отправить чек")
def send_check(message):
    bot.send_message(message.chat.id, "Отправьте чек (фото или скрин)")
    bot.register_next_step_handler(message, process_check)

def process_check(message):
    bot.forward_message(ADMIN_ID, message.chat.id, message.message_id)
    bot.send_message(message.chat.id, "Чек отправлен на проверку ✅")

@bot.message_handler(commands=['approve'])
def approve(message):
    if message.chat.id == ADMIN_ID:
        try:
            user_id = int(message.text.split()[1])

            markup = telebot.types.InlineKeyboardMarkup()
            markup.add(telebot.types.InlineKeyboardButton(
                "👉 Join Private Channel",
                url="https://t.me/your_channel"
            ))

            bot.send_message(
                user_id,
                "🥊 Thank you for joining\n\n"
                "The Beginner Boxing System is currently in production.\n\n"
                "You will get full access very soon.\n\n"
                "Stay tuned 👇",
                reply_markup=markup
            )

            bot.send_message(user_id, "What do you struggle with in boxing?")
            bot.register_next_step_handler_by_chat_id(user_id, ask_email)

        except:
            bot.send_message(message.chat.id, "Ошибка")

def ask_email(message):
    user_data[message.chat.id] = {"problem": message.text}
    bot.send_message(message.chat.id, "Your email:")
    bot.register_next_step_handler(message, ask_phone)

def ask_phone(message):
    user_data[message.chat.id]["email"] = message.text
    bot.send_message(message.chat.id, "Your phone (optional):")
    bot.register_next_step_handler(message, finish)

def finish(message):
    user_data[message.chat.id]["phone"] = message.text

    data = user_data[message.chat.id]

    bot.send_message(
        ADMIN_ID,
        f"Новый пользователь:\n\n"
        f"Проблема: {data['problem']}\n"
        f"Email: {data['email']}\n"
        f"Телефон: {data['phone']}"
    )

    bot.send_message(message.chat.id, "Спасибо! Данные сохранены ✅")

bot.infinity_polling()
