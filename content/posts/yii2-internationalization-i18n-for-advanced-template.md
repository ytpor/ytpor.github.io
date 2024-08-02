---
title: 'Yii2 Internationalization (i18n) for advanced template'
date: 2023-05-12T13:00:00+08:00
draft: false
url: /2023/05/yii2-internationalization-i18n-for-advanced-template.html
tags:
- internationalization
- i18n
- translation
- yii2
cover:
  image: "https://upload.wikimedia.org/wikipedia/en/6/6b/Yii-logo-transparent.png"
  alt: "Yii"
  relative: false
---

We are working on a project that makes use of Yii2 advanced template. This project needs to support Internationalization (i18n).

In each of our application, we have a translation configuration. This is needed to generate the translation. The files would be generated in `backend/messages`, `common/messages` or `frontend/messages`.

Here's a sample.

```
<?php
// backend/config/i18n.php
// common/config/i18n.php
// frontend/config/i18n.php

return [
    'sourcePath' => __DIR__ . DIRECTORY_SEPARATOR . '..',
    'languages' => [
        'zh-CN',
    ],
    'translator' => 'Yii::t',
    'sort' => true,
    'removeUnused' => true,
    'markUnused' => true,
    'only' => ['*.php'],
    'except' => [
        '.svn',
        '.git',
        '.gitignore',
        '.gitkeep',
        '.hgignore',
        '.hgkeep',
        '/assets',
        '/command',
        '/config',
        '/mail',
        '/messages',
        '/modules',
        '/runtime',
        '/tests',
        '/vagrant',
        '/vendor',
        '/views/user/settings',
        '/web',
        '/widgets',
    ],
    'format' => 'php',
    'messagePath' => __DIR__ . DIRECTORY_SEPARATOR . '..' . DIRECTORY_SEPARATOR . 'messages',
    'overwrite' => true,
];
```

Here are some samples of how the text should be wrapped.

```
// You'll use this in common
Yii::t('app', 'Hello world!');

// You'll use this in backend
Yii::t('backend', 'Hello world!');

// You'll use this in frontend
Yii::t('frontend', 'Hello world!');
```

Here's how we configured our application to work with the translation.

```
// common/config/main.php

'components' => [
    // ...
    'i18n' => [
        'translations' => [
            'app*' => [
                'class' => 'yii\i18n\PhpMessageSource',
                'basePath' => '@common/messages',
                'sourceLanguage' => 'en-US',
            ],
            'backend*' => [
                'class' => 'yii\i18n\PhpMessageSource',
                'basePath' => '@backend/messages',
                'sourceLanguage' => 'en-US',
            ],
            'frontend*' => [
                'class' => 'yii\i18n\PhpMessageSource',
                'basePath' => '@frontend/messages',
                'sourceLanguage' => 'en-US',
            ],
        ],
    ],
    // ...
],
```
