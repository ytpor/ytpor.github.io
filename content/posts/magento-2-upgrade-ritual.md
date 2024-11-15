---
title: 'Magento 2 Upgrade Ritual'
date: 2017-02-28T12:20:00.003+08:00
draft: false
url: /2017/02/magento-2-upgrade-ritual.html
tags:
- update
- upgrade
- magento 2
---

Assuming that you are on Magento version 2.0.6 and would like to upgrade to 2.0.7

```
composer require magento/product-community-edition 2.0.7 --no-update
composer update
rm -rf var/di var/generation/* var/view_preprocessed/* var/cache/* var/page_cache
php bin/magento cache:clean
php bin/magento cache:flush
php bin/magento setup:upgrade
php bin/magento setup:di:compile
php bin/magento indexer:reindex
```