# TelegramBotTaxi

# This is a Telegram Bot made with Python that mainly initiates a conversation to order a Taxi through Telegram, by logging in to the Taxi account and ordering it.

# INSTRUCTIONS:
1. Create a Telegram bot, and get the bot token.
2. Create a secret_tokens.py file in the same folder, use the ideal file below, or define your own bot_token, commandPassword, clickPassword, deletePassword, dbName, screenshotFileName.
3. Execute taxi_kg-telegram-bot


<img src="https://user-images.githubusercontent.com/87446059/148723613-206ec111-006f-4efd-a173-bca447791785.jpg" alt="Telegram Bot" width="300" height="350">

# Вызов такси:
<p float="left">
  <img src="https://user-images.githubusercontent.com/87446059/148842911-429582bd-46a9-4c4e-9266-9a3531135755.jpg" width="200" />
  <img src="https://user-images.githubusercontent.com/87446059/148844691-4ccfaa03-07f7-449b-9bff-a77ea1f2012e.jpg" width="200" /> 
  <img src="https://user-images.githubusercontent.com/87446059/148844809-f5bda044-8f96-4815-9ca1-e9c059b164f1.jpg" width="200" />
  <img src="hhttps://user-images.githubusercontent.com/87446059/148844812-a855cb7f-2462-4314-8be4-783e93fc293b.jpg" width="190" />
</p>
fun main() {
    val token = "<TOKEN>"
    val username = "<BOT USERNAME>"
    val bot = Bot.createPolling(username, token)

    bot.chain("/start") { msg -> bot.sendMessage(msg.chat.id, "Hi! What is your name?") }
        .then { msg -> bot.sendMessage(msg.chat.id, "Nice to meet you, ${msg.text}! Send something to me") }
        .then { msg -> bot.sendMessage(msg.chat.id, "Fine! See you soon") }
        .build()

    bot.chain(
        label = "location_chain",
        predicate = { msg -> msg.location != null },
        action = { msg ->
            bot.sendMessage(
                msg.chat.id,
                "Fine, u've sent me a location. Is this where you want to order a taxi?(yes|no)"
            )
        })
        .then("answer_choice") { msg ->
            when (msg.text) {
                "yes" -> bot.jumpToAndFire("order_taxi", msg)
                "no" -> bot.jumpToAndFire("cancel_ordering", msg)
                else -> {
                    bot.sendMessage(msg.chat.id, "Oops, I don't understand you. Just answer yes or no?")
                    bot.jumpTo("answer_choice", msg)
                }
            }
        }
        .then("order_taxi", isTerminal = true) { msg -> 
            bot.sendMessage(msg.chat.id, "Fine! Taxi is coming") 
        }
        .then("cancel_ordering", isTerminal = true) { msg -> 
            bot.sendMessage(msg.chat.id, "Ok! See you next time") 
        }
        .build()

    bot.start()
}
