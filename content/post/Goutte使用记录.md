+++
date = "2016-09-23T17:53:08+08:00"
description = "goutte"
title = "Goutte使用记录"

+++

工作项目需要把前端抓取网页的部分改写到后端抓取，使用了 Goutte。

   1. 设置header

   ```php
      $header = array(\'Origin: http://www.meilishuo.com\');
      $this->goutte_client->getClient()->setDefaultOption(\'config/curl/\'.CURLOPT_HTTPHEADER, $header);
      // 与 curl 设置类似
      $crawler = $this->goutte_client->request(\'GET\', $url);
   ```

   2. 过滤元素

   ```php
      $title = $crawler->filter(\'title\')->eq(0)->text(); // 支持选择器写法
      $html = $crawler->html(); // 获取全部html
   ```

   3. 模拟点击与提交表单

   ```php
      $login_url = \'http://login.m.taobao.com/login.htm?v=0&ttid=h5\';

      $crawler = $this->goutte_client->request(\'GET\', $login_url);
      $form = $crawler->selectButton(\'登 录\')->form();
      $crawler = $this->goutte_client->submit($form, array(\'TPL_username\' => \'x\', \'TPL_password\' => \'y\'));

      var_dump($this->goutte_client->getCookieJar());
      var_dump($this->goutte_client->getResponse()->getHeaders());
   ```

  像淘宝这种登录前要跑js的还是没辙，需要CasperJS 或 Selenium。

