# Telegram Alert Bot

A simple asynchronous Telegram bot for sending alerts and notifications with message queuing capabilities.

## Features

- Asynchronous message handling
- Message queuing system
- Error handling for Telegram API communications
- Support for mentioning other bots in messages
- Logging functionality

## Installation

```bash
pip install telegram-alert-bot

```

## Prerequisites

- Python 3.7+
- python-telegram-bot library
- A Telegram Bot Token (obtained from [@BotFather](https://t.me/botfather))
- Your Telegram User ID

## Usage

### Basic Setup

```python
from telegram_alert_bot import TelegramAlertBot
import asyncio

# Initialize the bot
bot = TelegramAlertBot(
    token="YOUR_BOT_TOKEN",
    user_id="YOUR_TELEGRAM_USER_ID"
)

# Create an async function to run your bot
async def main():
    # Start the background polling
    polling_task = asyncio.create_task(bot.background_bot_polling())
    
    # Trigger an event (send a message)
    await bot.event_trigger("Hello, World!", "mybot")
    
    # Wait for the message to be processed
    await bot.message_queue.join()
    
# Run the bot
asyncio.run(main())
```

### API Reference

#### TelegramAlertBot Class

The main class for handling Telegram alerts. Implementation can be found in:
```python:src/telegram_alert_bot/bot.py
startLine: 6
endLine: 31
```

##### Methods

1. `__init__(token: str, user_id: str)`
   - `token`: Your Telegram bot token
   - `user_id`: The Telegram user ID to send messages to

2. `async send_telegram_message(message: str)`
   - Sends a message directly to the specified user
   - Handles Telegram API errors and logs the outcome

3. `async background_bot_polling()`
   - Runs continuously in the background
   - Processes messages from the queue
   - Should be run in a separate task

4. `async event_trigger(message: str, bot: str)`
   - Queues a new message for sending
   - Automatically adds bot mention in format `@bot`
   - Messages are processed in FIFO order

## Error Handling

The bot includes built-in error handling for Telegram API communications. All errors are logged using Python's logging module. Error handling implementation can be found in:
```python:src/telegram_alert_bot/bot.py
startLine: 14
endLine: 18
```

## Logging

The bot uses Python's built-in logging module to track:
- Message sending attempts
- Successful message delivery
- Error states
- Event triggers
- Bot polling status

## Version

Current version: 0.1.0

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Support

For support, please open an issue in the GitHub repository.

## Authors

pudjo_jotaro 
https://github.com/pudjojotaro
