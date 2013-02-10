---
layout: post
title: "Serving Up Skin Images from Magento"
date: 2011-02-03 00:13
comments: true
categories: [Magento, PHP]
---
{% img center /assets/images/magento-skin.png %}
While integrating a shipping solution with Magento for multiple websites, we needed a way to get a logo for the invoice for each store in a generic way.  The problem is that ShipWorks only knows the store URL and has no knowledge of Magento's skin directory. So I came up with a simple PHP script that sits in the root of the Magento install and handles all the skin sanity (that's right) inside of Magento for me. Enjoy:

``` php
<?php
  require_once 'app/Mage.php';
  $mageRunCode = isset($_SERVER['MAGE_RUN_CODE']) ? $_SERVER['MAGE_RUN_CODE'] : '';
  Mage::app($mageRunCode);
  $file = Mage::getDesign()->getSkinUrl('images/logo-offline.gif');

  //Header
  header('Content-Type: image/gif');
  header('Content-Transfer-Encoding: binary');
  header('Expires: 0');
  header('Cache-Control: must-revalidate, post-check=0, pre-check=0');
  header('Pragma: public');
  header('Content-Length: ' . filesize($file));
  ob_clean();
  flush();

  //Contents
  readfile($file);
  exit;
?>
```
