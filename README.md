# Travel-with-us
from telegram import Update
from telegram.ext import ApplicationBuilder, CommandHandler, MessageHandler, filters, ConversationHandler, ContextTypes

BUDGET, DESTINATION = range(2)

# Start
async def start(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("👋 Welcome to Global Travel Bot!\n\nPlease enter your total budget in USD 💰 (e.g. 500)")
    return BUDGET

# Budget received
async def get_budget(update: Update, context: ContextTypes.DEFAULT_TYPE):
    context.user_data['budget'] = update.message.text
    await update.message.reply_text("🌍 Great! Where would you like to travel? (e.g. Istanbul, Dubai, Tokyo)")
    return DESTINATION

# Destination received
async def get_destination(update: Update, context: ContextTypes.DEFAULT_TYPE):
    budget = context.user_data['budget']
    destination = update.message.text

    # Demo hotel and flight suggestion (static content for now)
    hotel_suggestion = f"""
🏨 Hotel Suggestions in {destination} under ${budget}:
1. Empress Zoe – $110/night ⭐️4.6  
   👉 [Book Now](https://www.booking.com)

2. Sirkeci Mansion – $99/night ⭐️4.4  
   👉 [Book Now](https://www.booking.com)
"""

    flight_suggestion = f"""
✈️ Flight Options to {destination} under ${budget}:
1. PIA – $450 roundtrip  
   👉 [Book Flight](https://www.skyscanner.com)

2. Turkish Airlines – $480 roundtrip  
   👉 [Book Flight](https://www.skyscanner.com)
"""

    await update.message.reply_text(hotel_suggestion, parse_mode="Markdown")
    await update.message.reply_text(flight_suggestion, parse_mode="Markdown")
    return ConversationHandler.END

# Cancel handler
async def cancel(update: Update, context: ContextTypes.DEFAULT_TYPE):
    await update.message.reply_text("❌ Travel bot cancelled. See you again!")
    return ConversationHandler.END

# Main
def main():
    app = ApplicationBuilder().token("AAGeV5ny9GzjnCppmpT129LvHHXTtQ9uIZk").build()

    conv_handler = ConversationHandler(
        entry_points=[CommandHandler("start", start)],
        states={
            BUDGET: [MessageHandler(filters.TEXT & ~filters.COMMAND, get_budget)],
            DESTINATION: [MessageHandler(filters.TEXT & ~filters.COMMAND, get_destination)],
        },
        fallbacks=[CommandHandler("cancel", cancel)],
    )

    app.add_handler(conv_handler)
    print("✅ Bot is running...")
    app.run_polling()

if __name__ == "__main__":
    main()
