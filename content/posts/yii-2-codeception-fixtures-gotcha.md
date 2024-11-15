---
title: 'Yii 2 Codeception Fixtures Gotcha'
date: 2021-07-17T13:00:00.017+08:00
draft: false
url: /2021/07/yii-2-codeception-fixtures-gotcha.html
tags:
- fixture
- yii2
- codeception
---

We use MongoDB (important point) with our Yii 2 applicaton.

While setting up our API test fixtures in our Cest file, **$I->grabFixture** was not able to find the data we wanted eventhough we have defined it within the dataFile.

```
[yii\base\ErrorException] Trying to get property 'client_id' of non-object
```

Here's what the dataFile looks like.

```
<?php
// This doesn't work.
return [
    'client_one' =>
    [
        'client_id' => 'mytestclient',
    ],
];
```

After some experimentation, we've found that we need to set the **\_id** within the dataFile in order to get **$I->grabFixture** to work properly.

```
<?php
// This works.
return [
    'client_one' =>
    [
        '_id' => new MongoDB\BSON\ObjectId('60ea4a7db868d02fe478b6a4'),
        'client_id' => 'mytestclient',
    ],
];
```