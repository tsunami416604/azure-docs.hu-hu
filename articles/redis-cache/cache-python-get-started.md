---
title: "Az Azure Redis Cache használata Pythonnal | Microsoft Docs"
description: "Bevezetés az Azure Redis Cache használatába Python alkalmazásával"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: v-lincan
ms.assetid: f186202c-fdad-4398-af8c-aee91ec96ba3
ms.service: cache
ms.devlang: python
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: cdbee52574d0ffbe82ef3dc98f2848f4d00ba2ff


---
# <a name="how-to-use-azure-redis-cache-with-python"></a>Az Azure Redis Cache használata Pythonnal
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Ez a témakör segítséget nyújt az első lépések megtételében az Azure Redis Cache és a Python használatakor.

## <a name="prerequisites"></a>Előfeltételek
Telepítse a [redis-py](https://github.com/andymccurdy/redis-py) ügyfelet.

## <a name="create-a-redis-cache-on-azure"></a>Redis Cache gyorsítótár létrehozása az Azure-ban
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Állomásnév és hívóbetűk lekérése
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="enable-the-non-ssl-endpoint"></a>Nem SSL végpont engedélyezése
Egyes Redis-ügyfelek nem támogatják az SSL-t, és alapértelmezés szerint a [nem SSL port le van tiltva az új Azure Redis Cache-példányokban](cache-configure.md#access-ports). Az oktatóanyag összeállításakor a [redis-py](https://github.com/andymccurdy/redis-py) nem támogatja az SSL-t. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Elemek hozzáadása és lekérése a gyorsítótárból
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



<!--HONumber=Feb17_HO2-->


