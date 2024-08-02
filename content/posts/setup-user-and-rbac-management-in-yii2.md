---
title: 'Setup User and RBAC Management in Yii2'
date: 2019-08-26T23:05:00.000+08:00
draft: false
url: /2019/08/setup-user-and-rbac-management-in-yii2.html
tags:
- user
- rbac
- yii2
cover:
  image: "https://upload.wikimedia.org/wikipedia/en/6/6b/Yii-logo-transparent.png"
  alt: "Yii"
  relative: false
---

My goal for this exercise is to have User and RBAC management in my Yii 2 application.

I've started out with the Yii 2 Basic Project Template and I would be adding the Yii2-user and Yii2-rbac extensions to my web application.

Here are some of the steps I have taken to install and configure the extensions.

### Step 1: Installation

Install the packages.

```
composer require dektrium/yii2-user
composer require dektrium/yii2-rbac
```

### Step 2: Configure the web application

NOTE: Make sure that you don't have user component configuration in your config files.
Add the user and rbac module to the web applcation config.

```
'modules' => [
    ...
    'rbac' => 'dektrium\rbac\RbacWebModule',
    'user' => [
        'class' => 'dektrium\user\Module',
    ],
    ...
],
```

### Step 3: Configure the console application

Add the user and rbac module to the console applcation config.

```
'modules' => [
    ...
    'rbac' => 'dektrium\rbac\RbacConsoleModule',
    'user' => [
        'class' => 'dektrium\user\Module',
    ],
    ...
],
```

### Step 4: Update the database schema

Update the database scheme

```
php yii migrate/up --migrationPath=@vendor/dektrium/yii2-user/migrations
php yii migrate/up --migrationPath=@yii/rbac/migrations
```

### Step 5: Create Super User

Create my first admin user

```
php yii user/create admin@myapp.com admin password
```