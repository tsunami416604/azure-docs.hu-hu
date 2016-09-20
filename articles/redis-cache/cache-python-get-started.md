<properties
    pageTitle="Az Azure Redis Cache használata Pythonnal | Microsoft Azure"
    description="Bevezetés az Azure Redis Cache használatába Python alkalmazásával"
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="python"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="08/16/2016"
    ms.author="sdanie"/>

# Az Azure Redis Cache használata Pythonnal

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Ez a témakör segítséget nyújt az első lépések megtételében az Azure Redis Cache és a Python használatakor.


## Előfeltételek

Telepítse a [redis-py](https://github.com/andymccurdy/redis-py) ügyfelet.


## Redis Cache gyorsítótár létrehozása az Azure-ban

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Állomásnév és hívóbetűk lekérése

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Nem SSL végpont engedélyezése

Egyes Redis-ügyfelek nem támogatják az SSL-t, és alapértelmezés szerint a [nem SSL port le van tiltva az új Azure Redis Cache-példányokban](cache-configure.md#access-ports). Az oktatóanyag összeállításakor a [redis-py](https://github.com/andymccurdy/redis-py) nem támogatja az SSL-t. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Elemek hozzáadása és lekérése a gyorsítótárból


    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'


Cserélje le a `<name>` elemet a gyorsítótár nevére, és a `key` elemet a hívóbetűre.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png



<!--HONumber=sep16_HO1-->


