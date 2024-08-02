---
title: 'Yii2 URL manager cache class'
date: 2020-04-09T13:00:00+08:00
draft: false
url: /2020/04/yii2-url-manager-cache-class.html
tags:
- url manager
- yii2
cover:
  image: "https://upload.wikimedia.org/wikipedia/en/6/6b/Yii-logo-transparent.png"
  alt: "Yii"
  relative: false
---

You might come across the below warning if you have not set a cache to your Yii2 application while using URL manager.

```
Unable to use cache for URL manager: Failed to instantiate component or class "cache".
```

In order to remove that warning, setup a cache in your application's configuration.

We are using the DummyCache class here as we have yet to decide on one that we want to use.

```
'components' => [
    'cache' => [
        'class' => 'yii\caching\DummyCache', // FIXME
    ],
],
```