# -from telegram.ext import Updater, CommandHandler, MessageHandler, Filters
import yt_dlp
import os

# توكن البوت الذي تحصل عليه من BotFather على Telegram
TOKEN = '7242547563:AAH03mge8mPqgp-doZe7ApioXHhHkED8X2w'

def download_video(url):
    ydl_opts = {
        'format': 'best',
        'outtmpl': 'downloads/%(title)s.%(ext)s',
    }
    with yt_dlp.YoutubeDL(ydl_opts) as ydl:
        info_dict = ydl.extract_info(url, download=True)
        video_file = ydl.prepare_filename(info_dict)
    return video_file

def start(update, context):
    update.message.reply_text('أرسل لي رابط الفيديو لتنزيله.')

def handle_message(update, context):
    url = update.message.text
    update.message.reply_text("جاري تنزيل الفيديو...")
    try:
        video_file = download_video(url)
        with open(video_file, 'rb') as video:
            update.message.reply_video(video=video)
        update.message.reply_text("تم التنزيل بنجاح!")
        os.remove(video_file)  # لحذف الفيديو بعد الإرسال
    except Exception as e:
        update.message.reply_text(f"حدث خطأ: {e}")

def main():
    updater = Updater(TOKEN, use_context=True)

    dp = updater.dispatcher
    dp.add_handler(CommandHandler("start", start))
    dp.add_handler(MessageHandler(Filters.text & ~Filters.command, handle_message))

    updater.start_polling()
    updater.idle()

if __name__ == '__main__':
    main()
كود لبوت تليمرام يقوم بتنزيل الفيديوهات من مواقع التواصل
