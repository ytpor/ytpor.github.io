---
title: 'Amazon Cloudwatch alarm to Jaconda - a Telegram Smartbot'
date: 2017-02-15T01:00:00+08:00
draft: false
url: /2017/02/we-have-setup-amazon-cloudwatch-alarms.html
tags:
- sns
- jaconda
- telegram
- amazon
- cloudwatch
- aws
- lambda
cover:
  image: "https://upload.wikimedia.org/wikipedia/commons/8/83/Telegram_2019_Logo.svg"
  alt: "Telegram"
  relative: false
---

We have setup Amazon Cloudwatch alarms to keep track of various metrics. In a situation where a particular alarm is triggered, a notification is sent to a Amazon Notification Topic. Within that Amazon Notification Topic, one of the subcription uses the lambda protocol. It is through this subscription which we would get Amazon Lambda to send a message to [Jaconda](https://jaconda.im/) - a Telegram Smartbot.

Here's a sample Node.js script that we have used.

```
const https = require('https');
const jacondaPath = process.env.jacondaPath;
const jacondaToken = process.env.jacondaToken;
const jacondaUrl = process.env.jacondaUrl;

function postMessage(message) {
    jsonObject = JSON.stringify({
        'token' : jacondaToken,
        'text' : message
    });

    // prepare the header
    var postheaders = {
        'Content-Type' : 'application/json',
        'Content-Length' : Buffer.byteLength(jsonObject, 'utf8')
    };

    // the post options
    var optionspost = {
        host : jacondaUrl,
        port : 443,
        path : jacondaPath,
        method : 'POST',
        headers : postheaders
    };

    console.info('Options prepared:');
    console.info(optionspost);
    console.info('Do the POST call');

    // do the POST call
    var reqPost = https.request(optionspost, function(res) {
        console.log("statusCode: ", res.statusCode);

        res.on('data', function(d) {
            console.info('POST result:\n');
            process.stdout.write(d);
            console.info('\n\nPOST completed');
        });
    });

    // write the json data
    reqPost.write(jsonObject);
    reqPost.end();
    reqPost.on('error', function(e) {
        console.error(e);
    });
}

exports.handler = function(event, context, callback) {
    const message = event.Records[0].Sns.Message;
    console.log('From SNS:', message);
    callback(null, message);

    postMessage(message);
}
```