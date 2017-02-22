---
title: "Az Azure Redis Cache használata Javával | Microsoft Docs"
description: "Bevezetés az Azure Redis Cache és a Java együttes használatába"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 29275a5e-2e39-4ef2-804f-7ecc5161eab9
ms.service: cache
ms.devlang: java
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 5369dcd6ad1ec93c63eb442db9fc5ffdcca37375
ms.openlocfilehash: b95f37db90b105962c01545e25c8e14c53257ebc


---
# <a name="how-to-use-azure-redis-cache-with-java"></a>Az Azure Redis Cache használata Javával
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Az Azure Redis Cache hozzáférést biztosít egy dedikált Redis Cache gyorsítótárhoz, amelyet a Microsoft felügyel. A gyorsítótár a Microsoft Azure összes alkalmazásából elérhető.

Ez a témakör segítséget nyújt az első lépések megtételében a Javát alkalmazó Azure Redis Cache használatakor.

## <a name="prerequisites"></a>Előfeltételek
[Jedis](https://github.com/xetorthio/jedis) - Java-ügyfél a Redishez

Ez az oktatóanyag a Jedis használatát mutatja be, de a [http://redis.io/clients](http://redis.io/clients) webhelyen felsorolt Java-ügyfelek bármelyike használható.

## <a name="create-a-redis-cache-on-azure"></a>Redis Cache gyorsítótár létrehozása az Azure-ban
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Állomásnév és hívóbetűk lekérése
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Biztonságos csatlakozás a gyorsítótárhoz SSL használatával
A [jedis](https://github.com/xetorthio/jedis) legújabb buildjei támogatást nyújtanak ahhoz, ha SSL-lel szeretne kapcsolódni az Azure Redis Cache-hez. Az alábbi példa bemutatja, hogyan csatlakozhat az Azure Redis Cache-hez a 6380-as SSL-végpont használatával. A `<name>` helyére a gyorsítótár nevét, a `<key>` helyére pedig az előző, [Állomásnév és hívóbetűk lekérése](#retrieve-the-host-name-and-access-keys) című szakaszban ismertetett elsődleges vagy másodlagos kulcsot írja be.

    boolean useSsl = true;
    /* In this line, replace <name> with your cache name: */
    JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
    shardInfo.setPassword("<key>"); /* Use your access key. */

> [!NOTE]
> A nem SSL-port le van tiltva az új Azure Redis Cache-példányokban. Ha az SSL-t nem támogató, egyéb ügyfelet használ, tekintse meg a következőt: [A nem SSL-port engedélyezése](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Elemek hozzáadása és lekérése a gyorsítótárból
    package com.mycompany.app;
    import redis.clients.jedis.Jedis;
    import redis.clients.jedis.JedisShardInfo;

    public class App
    {
      public static void main( String[] args )
      {
        boolean useSsl = true;
        /* In this line, replace <name> with your cache name: */
        JedisShardInfo shardInfo = new JedisShardInfo("<name>.redis.cache.windows.net", 6379, useSsl);
        shardInfo.setPassword("<key>"); /* Use your access key. */
        Jedis jedis = new Jedis(shardInfo);
        jedis.set("foo", "bar");
        String value = jedis.get("foo");
      }
    }


## <a name="next-steps"></a>Következő lépések
* [Engedélyezze a gyorsítótár-diagnosztikát,](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics) hogy [megfigyelhesse](https://msdn.microsoft.com/library/azure/dn763945.aspx) a gyorsítótár állapotát.
* Olvassa el a hivatalos [Redis dokumentációt](http://redis.io/documentation).



<!--HONumber=Feb17_HO2-->


