# Telegram Alert Bot

[![PyPI version](https://img.shields.io/pypi/v/telegram-alert-bot.svg)](https://pypi.org/project/telegram-alert-bot/)
[![Python versions](https://img.shields.io/pypi/pyversions/telegram-alert-bot.svg)](https://pypi.org/project/telegram-alert-bot/)
[![License](https://img.shields.io/github/license/pudjojotaro/telegram-alert-bot)](https://github.com/pudjojotaro/telegram-alert-bot/blob/main/LICENSE)
[![Downloads](https://img.shields.io/pypi/dm/telegram-alert-bot)](https://pypi.org/project/telegram-alert-bot/)
[![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

A simple asynchronous Telegram bot for sending alerts and notifications with message queuing capabilities.

## Features

- Asynchronous message handling
- Message queuing system
- Error handling for Telegram API communications
- Support for mentioning other bots in messages
- Logging functionality
- Optional message merging based on a specified pattern

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

1. `__init__(token: str, user_id: str, merge_pattern: str = None)`
   - `token`: Your Telegram bot token
   - `user_id`: The Telegram user ID to send messages to
   - `merge_pattern`: Optional string to detect messages for merging

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

## Message Merging

The bot supports optional message merging functionality, which allows you to combine consecutive messages that match a specific pattern. This is particularly useful for status updates or repeated notifications.

### Basic Message Merging Setup

```python
from telegram_alert_bot import TelegramAlertBot
import asyncio

# Initialize bot with message merging enabled
bot = TelegramAlertBot(
    token="YOUR_BOT_TOKEN",
    user_id="YOUR_TELEGRAM_USER_ID",
    merge_pattern="No profitable items found between"
)

async def main():
    polling_task = asyncio.create_task(bot.background_bot_polling())
    
    # These messages will be merged if sent consecutively
    await bot.event_trigger(
        "No profitable items found between 2024-01-01 10:00:00 and 2024-01-01 10:15:00.",
        "status_bot"
    )
    await bot.event_trigger(
        "No profitable items found between 2024-01-01 10:15:00 and 2024-01-01 10:30:00.",
        "status_bot"
    )
    
    # Result in chat will be:
    # "No profitable items found between 2024-01-01 10:00:00 and 2024-01-01 10:30:00."
    # "@status_bot"
    
    await bot.message_queue.join()

asyncio.run(main())
```

### Message Merging Behavior

1. **Pattern Matching**: Only messages that start with the specified `merge_pattern` will be considered for merging.

2. **Consecutive Messages**: Merging only occurs for consecutive messages. If other messages appear in between, the merge chain breaks.

3. **Time Extraction**: The bot automatically extracts and combines time ranges from messages.

### Advanced Usage Examples

#### Custom Pattern Merging

```python
# Initialize with custom pattern
bot = TelegramAlertBot(
    token="YOUR_BOT_TOKEN",
    user_id="YOUR_TELEGRAM_USER_ID",
    merge_pattern="Status update:"
)

async def main():
    polling_task = asyncio.create_task(bot.background_bot_polling())
    
    # These messages will be merged
    await bot.event_trigger("Status update: System check at 09:00", "system_bot")
    await bot.event_trigger("Status update: System check at 10:00", "system_bot")
    
    # This message won't be merged (different pattern)
    await bot.event_trigger("Alert: New user registered", "system_bot")
    
    await bot.message_queue.join()
```


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

Current version: 0.2.1

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Support

For support, please open an issue in the GitHub repository.

## Authors

pudjo_jotaro 
https://github.com/pudjojotaro

