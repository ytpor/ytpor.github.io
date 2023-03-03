---
title: 'Send Telegram Message in Group using VB Script'
date: 2018-08-17T12:45:00.000+08:00
draft: false
url: /2018/08/send-telegram-message-in-group-using-vb.html
tags:
- telegram
- excel
- telegram bot
- vbs
- bot
---

Here's a vb script snippet that I have used to send Telegram message in a group from an Excel sheet.

```
Sub sendTelegramMessage()

Set objHttp = CreateObject("MSXML2.ServerXMLHTTP")
' Change these accordingly
strChat = "<chat_id>"
strText = "*Test*\r\nThis is a message"
strUrl = "https://api.telegram.org/bot<token>/sendMessage"

' Leave these alone
strMode = "markdown"
strJson = "{""chat_id"": """ & strChat & """, ""text"": """ & strText & """, ""parse_mode"": """ & strMode & """}"

objHttp.Open "POST", strUrl, False
objHttp.setRequestHeader "Content-type", "application/json"
objHttp.send strJson

End Sub
```