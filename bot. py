import telebot
import re
import os

TOKEN = os.environ.get("TELEGRAM_BOT_TOKEN")
bot = telebot.TeleBot(TOKEN)

@bot.message_handler(func=lambda message: True)
def check_message(message):
    if message.chat.type != "supergroup":
        return

    if message.from_user is None or message.from_user.username is None:
        return

    user_id = message.from_user.id
    username = "@" + message.from_user.username

    is_admin = bot.get_chat_member(message.chat.id, user_id).status in ["administrator", "creator"]
    contains_phone = bool(re.search(r"\+?\d{7,}", message.text))
    contains_link = bool(re.search(r"(https?://|www\.)", message.text))

    if (contains_phone or contains_link) and not is_admin:
        bot.delete_message(message.chat.id, message.message_id)
        bot.restrict_chat_member(message.chat.id, user_id, permissions=telebot.types.ChatPermissions(can_send_messages=False))
        bot.send_message(
            message.chat.id,
            f"⚠️ Message deleted and user {username} has been **restricted** for sending prohibited content.",
            parse_mode="Markdown"
        )

bot.polling()
