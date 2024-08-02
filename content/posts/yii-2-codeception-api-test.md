---
title: 'Yii 2 Codeception API Test'
date: 2021-06-30T21:00:00.016+08:00
draft: false
url: /2021/06/yii-2-codeception-api-test.html
tags:
- yii2
- dotenv
- codeception
cover:
  image: "https://upload.wikimedia.org/wikipedia/en/6/6b/Yii-logo-transparent.png"
  alt: "Yii"
  relative: false
---

My API uses the Yii2 advanced template. It uses symfony/dotenv to read the .env. The .env is loaded in all the configurations.

```
$dotenv = new Symfony\Component\Dotenv\Dotenv;
$dotenv->load(__DIR__ . '/../../.env');
```

While running the Codeception api test, I came across this error.

```
[PHPUnit\Framework\Exception] Undefined index: HOST at ../common/config/main-local.php:7
```

After much digging around, I've realized that **I need to load Symfony\\Component\\Dotenv\\Dotenv in common/config/codeception-local.php** Thus, I have updated that file in the environment folder, and reran **php init**.

```
<?php
# environments/dev/common/config/codeception-local.php

# Add these 2 lines below
$dotenv = new Symfony\Component\Dotenv\Dotenv;
$dotenv->load(__DIR__ . '/../../.env.example');

return yii\helpers\ArrayHelper::merge(
    require __DIR__ . '/main.php',
    require __DIR__ . '/main-local.php',
    require __DIR__ . '/test.php',
    require __DIR__ . '/test-local.php',
    [
        'components' => [
        ],
    ]
);
```