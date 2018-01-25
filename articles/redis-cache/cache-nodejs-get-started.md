---
title: "Az Azure Redis Cache használata a Node.js környezettel | Microsoft Docs"
description: "Bevezetés az Azure Redis Cache használatába a Node.js és a node_redis alkalmazásával."
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: hero-article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: wesmc
ms.openlocfilehash: e8b4818ad2b876ce4d55cf8aa2aa0e0162dea963
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-use-azure-redis-cache-with-nodejs"></a>Az Azure Redis Cache használata a Node.js környezettel
> [!div class="op_single_selector"]
> * [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
> * [ASP.NET](cache-web-app-howto.md)
> * [Node.js](cache-nodejs-get-started.md)
> * [Java](cache-java-get-started.md)
> * [Python](cache-python-get-started.md)
> 
> 

Az Azure Redis Cache hozzáférést biztosít egy biztonságos, dedikált Redis Cache gyorsítótárhoz, amelyet a Microsoft felügyel. A gyorsítótár a Microsoft Azure összes alkalmazásából elérhető.

Ez a témakör segítséget nyújt az első lépések megtételében az Azure Redis Cache használatakor Node.js környezetben. 

## <a name="prerequisites"></a>Előfeltételek
Telepítse a [node_redis](https://github.com/mranney/node_redis) ügyfelet:

    npm install redis

Ez az oktatóanyag a [node_redis](https://github.com/mranney/node_redis) ügyfelet használja. Az egyéb Node.js-ügyfeleket használó példákért tekintse meg az egyes Node.js-ügyfelek dokumentációját a [Node.js Redis-ügyfeleket](http://redis.io/clients#nodejs) felsoroló weblapon.

## <a name="create-a-redis-cache-on-azure"></a>Redis Cache gyorsítótár létrehozása az Azure-ban
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## <a name="retrieve-the-host-name-and-access-keys"></a>Állomásnév és hívóbetűk lekérése
[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="connect-to-the-cache-securely-using-ssl"></a>Biztonságos csatlakozás a gyorsítótárhoz SSL használatával
A [node_redis](https://github.com/mranney/node_redis) legújabb buildjei támogatást nyújtanak az Azure Redis Cache-hez SSL használatával való kapcsolódáshoz. Az alábbi példa bemutatja, hogyan csatlakozhat az Azure Redis Cache-hez a 6380-as SSL-végpont használatával. A `<name>` helyére a gyorsítótár nevét, a `<key>` helyére pedig az előző, [Állomásnév és hívóbetűk lekérése](#retrieve-the-host-name-and-access-keys) című szakaszban ismertetett elsődleges vagy másodlagos kulcsot írja be.

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

> [!NOTE]
> A nem SSL-port le van tiltva az új Azure Redis Cache-példányokban. Ha az SSL-t nem támogató, egyéb ügyfelet használ, tekintse meg a következőt: [A nem SSL-port engedélyezése](cache-configure.md#access-ports).
> 
> 

## <a name="add-something-to-the-cache-and-retrieve-it"></a>Elemek hozzáadása és lekérése a gyorsítótárból
Az alábbi példa bemutatja, hogyan csatlakozhat egy Azure Redis Cache-példányhoz, valamint hogyan menthet egy elemet a gyorsítótárban, majd kérheti le azt onnan. További példák a Redis használatára a [node_redis](https://github.com/mranney/node_redis) ügyféllel: [http://redis.js.org/](http://redis.js.org/).

     var redis = require("redis");

      // Add your cache name and access key.
    var client = redis.createClient(6380,'<name>.redis.cache.windows.net', {auth_pass: '<key>', tls: {servername: '<name>.redis.cache.windows.net'}});

    client.set("key1", "value", function(err, reply) {
            console.log(reply);
        });

    client.get("key1",  function(err, reply) {
            console.log(reply);
        });

Kimenet:

    OK
    value


## <a name="next-steps"></a>További lépések
* [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát.
* Olvassa el a hivatalos [Redis dokumentációt](http://redis.io/documentation).

