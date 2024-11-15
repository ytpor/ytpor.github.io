---
title: 'Add and Allow New Telegram Bot to Send Message in Group'
date: 2018-02-05T12:14:00+08:00
draft: false
url: /2018/02/add-and-allow-new-telegram-bot-to-send.html
tags:
- telegram
- telegram bot
- bot
---

1.  Talk to @BotFather, and request to create a new bot. (hint: /help)
2.  Give the new bot a name, and a unique username.
3.  Take note of the bot token given by the @BotFather
4.  Add the new bot to a group.
5.  Run this - https://api.telegram.org/bot<bot\_token>/getUpdates , to get the group's chat id that contains the new bot.
6.  In the group, type /start @username to activate the new bot.