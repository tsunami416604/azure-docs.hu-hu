<properties
    pageTitle="Az Azure Redis Cache használata a Node.js környezettel | Microsoft Azure"
    description="Bevezetés az Azure Redis Cache használatába a Node.js és a node_redis alkalmazásával."
    services="redis-cache"
    documentationCenter=""
    authors="steved0x"
    manager="douge"
    editor="v-lincan"/>

<tags
    ms.service="cache"
    ms.devlang="nodejs"
    ms.topic="hero-article"
    ms.tgt_pltfrm="cache-redis"
    ms.workload="tbd"
    ms.date="05/31/2016"
    ms.author="sdanie"/>

# Az Azure Redis Cache használata a Node.js környezettel

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.js](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Az Azure Redis Cache hozzáférést biztosít egy biztonságos, dedikált Redis Cache gyorsítótárhoz, amelyet a Microsoft felügyel. A gyorsítótár a Microsoft Azure összes alkalmazásából elérhető.

Ez a témakör segítséget nyújt az első lépések megtételében az Azure Redis Cache használatakor Node.js környezetben. Egy másik példa az Azure Redis Cache használatára Node.js környezetben: [Node.js-csevegőalkalmazás létrehozása a Socket.IO segítségével egy Azure-webhelyen](../app-service-web/web-sites-nodejs-chat-app-socketio.md).


## Előfeltételek

Telepítse a [node_redis](https://github.com/mranney/node_redis) ügyfelet:

    npm install redis

Ez az oktatóanyag a [node_redis](https://github.com/mranney/node_redis) használatát mutatja be, de a [http://redis.io/clients](http://redis.io/clients) webhelyen felsorolt Node.js-ügyfelek bármelyike használható.

## Redis Cache gyorsítótár létrehozása az Azure-ban

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Állomásnév és hívóbetűk lekérése

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Nem SSL végpont engedélyezése

Egyes Redis-ügyfelek nem támogatják az SSL-t, és alapértelmezés szerint a [nem SSL port le van tiltva az új Azure Redis Cache-példányokban](cache-configure.md#access-ports). Az oktatóanyag összeállításakor a [node_redis](https://github.com/mranney/node_redis) ügyfél nem támogatja az SSL-t. 

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Elemek hozzáadása és lekérése a gyorsítótárból

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


## Következő lépések

- [Engedélyezze a gyorsítótár-diagnosztikát,](cache-how-to-monitor.md#enable-cache-diagnostics) hogy [megfigyelhesse](cache-how-to-monitor.md) a gyorsítótár állapotát.
- Olvassa el a hivatalos [Redis dokumentációt](http://redis.io/documentation).






<!--HONumber=jun16_HO2-->


