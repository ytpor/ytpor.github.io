---
title: 'Testing Yii with Codeception'
date: 2016-02-02T18:19:00.001+08:00
draft: false
url: /2016/02/testing-yii-with-codeception.html
tags:
- yii
- codeception
---

We have an application written in Yii. Up til this point, it has been manually tested. Since it's more efficient to automate the testing of this application, we have decided to set up Codeception to assist us with this task.

Set up was easy and straight forward - this said after spending countless hours understanding the correct way to set it up.

To illustrate the set up process, we'll create a skeleton Yii app.

```
php /path/to/yii/framework/yiic.php webapp /path/to/webapp
```

Next, download a copy of [codecept.phar](http://codeception.com/codecept.phar) and put it in **/path/to/webapp/protected**, and run this

```
cd /path/to/webapp/protected
php codecept.phar bootstrap
```

Various files would be created in **/path/to/webapp/protected/tests**

Look up the file **/path/to/webapp/protected/tests/acceptance.suite.yml** and update **url** to **http://localhost/webapp**

You might not believe it, but set up is done. We'll proceed to verify our set up has been done correctly.

Create a file **LoginCept.php** in **/path/to/webapp/protected/tests/acceptance**

```
<?php
$I = new AcceptanceTester($scenario);
$I->wantTo('Test index page');
$I->amOnPage('/index.php');
$I->see('My Web Application','#header #logo');
$I->click('Login');
$I->see('Login','h1');
$I->see('Username');
$I->fillField('#LoginForm_username','demo');
$I->fillField('#LoginForm_password','demo');
$I->click('#login-form input[type="submit"]');
$I->seeLink('Logout (demo)');
$I->click('Logout (demo)');
$I->seeLink('Login');
```

Run the acceptance test.

```
cd /path/to/webapp/protected
php codecept.phar run acceptance
```

You should see this **OK (1 test, 5 assertions)** if the setup and test went according to plan.

### References

[https://github.com/Codeception/Codeception](https://github.com/Codeception/Codeception)
[http://codeception.com/docs/modules/Yii1](http://codeception.com/docs/modules/Yii1)