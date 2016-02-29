<properties
    pageTitle="如何搭配使用 Azure Redis 快取與 Python | Microsoft Azure"
    description="開始搭配使用 Azure Redis 快取與 Python"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="dwrede"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="12/03/2015"
    ms.author="sdanie"/>

# 如何搭配使用 Azure Redis 快取與 Python

> [AZURE.SELECTOR]
- [.Net](cache-dotnet-how-to-use-azure-redis-cache.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

本主題說明如何搭配使用 Azure Redis 快取與 Python。


## 先決條件

安裝 [redis py](https://github.com/andymccurdy/redis-py)。


## 在 Azure 上建立 Redis 快取

在 [Azure 入口網站](http://go.microsoft.com/fwlink/?LinkId=398536), ，按一下 [ **新增**, ，**資料 + 儲存體**, ，然後選取 **Redis 快取**。

  ![][1]

輸入 DNS 主機名稱。 它的形式為 `<name>
  .redis.cache.windows.net`。 按一下 [ **建立**。

  ![][2]

  一旦您建立快取中， [瀏覽至該](cache-configure.md#configure-redis-cache-settings) 若要檢視快取設定。 您將需要：

  - **主機名稱。**您在建立快取時輸入了這個名稱。
  - **連接埠。**按一下下方的連結 **連接埠** 若要檢視的連接埠。 請使用 SSL 連接埠。
  - **存取金鑰。**按一下下方的連結 **金鑰** ，並將複製的主索引鍵。

  ## 在快取中加入項目並擷取該項目

  >>> 匯入 redis
  >>> r = redis。StrictRedis(host='<name>。.redis.cache.windows.net '，
          連接埠 = 6380，db = 0 時，密碼 ='<key>，ssl = True)
    >>> r.set ('foo'、 '列')
    True
    >>> r.get('foo')
    b'bar'

取代 *& lt; 名稱 & gt;* 使用快取名稱和 *(& s) lt; 金鑰 & gt;* 以存取金鑰。


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

