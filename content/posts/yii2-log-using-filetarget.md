---
title: 'Yii2 Log using FileTarget'
date: 2020-04-09T12:00:00+08:00
draft: false
url: /2020/04/yii2-log-using-filetarget.html
tags:
- log
cover:
  image: "https://upload.wikimedia.org/wikipedia/en/6/6b/Yii-logo-transparent.png"
  alt: "Yii"
  relative: false
---

Here's a sample Yii2 FileTarget configuration (to be added to the application's configuration) used for logging. This configuration overwrite the default log file location, doesn't capture the global PHP variables and also, adds a prefix to the log entry.

```
'bootstrap' => [
    'log',
],
'components' => [
    'log' => [
        'targets' => [
            [
                'class' => 'yii\log\FileTarget',
                'levels' => ['error', 'info'],
                // Custom log file. Default log file will appear in @runtime/logs
                'logFile' => dirname(__DIR__) . '/log/app.log',
                //
                'logVars' => [],
                // Custom prefix.
                'prefix' => function ($message) {
                    $request = Yii::$app->getRequest();
                    $ip = $request instanceof yii\base\Request ? $request->getUserIP() : '-';
                    $controller = Yii::$app->controller->id;
                    $action = Yii::$app->controller->action->id;
                    return "[$ip][$controller][$action]";
                }
            ],
        ],
    ],
],
```