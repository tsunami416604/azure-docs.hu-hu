---
title: 'Rövid útmutató: Azure-gyorsítótár használata a Redis-hez a Node.js fájllal'
description: Ebben a rövid útmutatóban megtudhatja, hogyan használhatja az Azure Cache for Redis node.js és node_redis.
author: yegu-ms
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: yegu
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 07e2d6f174e5af4af9bdcac73dc74f5cf061ed41
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300485"
---
# <a name="quickstart-use-azure-cache-for-redis-with-nodejs"></a>Rövid útmutató: Azure-gyorsítótár használata a Redis-hez a Node.js fájllal

Ebben a rövid útmutatóban a Redis-gyorsítótárat egy Node.js alkalmazásba építheti be, hogy hozzáférjen egy biztonságos, dedikált gyorsítótárhoz, amely az Azure-on belül bármely alkalmazásból elérhető.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis), amelyet a `npm install redis`paranccsal telepíthet. 

Az egyéb Node.js-ügyfeleket használó példákért tekintse meg az egyes Node.js-ügyfelek dokumentációját a [Node.js Redis-ügyfeleket](https://redis.io/clients#nodejs) felsoroló weblapon.

## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Adjon hozzá környezeti változókat a **GAZDAGÉP NEVE** és **Elsődleges** hozzáférési kulcs megadásához. A kód ezen változóit fogja használni ahelyett, hogy közvetlenül a kódban tárolná a bizalmas információkat.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz

A [node_redis](https://github.com/mranney/node_redis) legújabb buildjei támogatást nyújtanak az Azure Cache for Redis SSL használatával történő csatlakoztatásához. A következő példa bemutatja, hogyan csatlakozhat az Azure Cache for Redis az SSL-végpont 6380 használatával. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Ne hozzon létre új kapcsolatokat a kód minden műveletéhez. Ehelyett a lehető legtöbbször hasznosítsa újra a kapcsolatokat. 

## <a name="create-a-new-nodejs-app"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy *redistest.js* nevű új szkriptfájlt. A parancs `npm install redis bluebird` segítségével telepítse a szükséges csomagokat.

Adja hozzá a következő példa JavaScriptet a fájlhoz. Ez a kód bemutatja, hogyan csatlakozhat egy Azure-gyorsítótárredis-példányhoz a gyorsítótár-állomásnév és a kulcsfontosságú környezeti változók használatával. A kód emellett tárolja és lekéri gyorsítótár egyik sztringértékét. A rendszer a `PING` és a `CLIENT LIST` parancsot is végrehajtja. További példák a Redis használatára a [node_redis](https://github.com/mranney/node_redis) ügyféllel: [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the SSL port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Futtassa a szkriptet a Node.js-sel.

```
node redistest.js
```

Az alábbi példában a `Message` kulcsot láthatja. A kulcsnak korábban gyorsítótárazott értéke volt, amely az Azure Portalon, a Redis Console használatával lett beállítva. Az alkalmazás frissítette ezt a gyorsítótárazott értéket. Az alkalmazás továbbá végrehajtotta a `PING` és a `CLIENT LIST` parancsot.

![A Redis Cache alkalmazás befejeződött](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem visszaállítható; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

A **Név szerint szűrés** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az erőforráscsoportban az eredménylistában válassza **a ...** majd **az Erőforráscsoport törlése**lehetőséget.

![Azure-erőforráscsoport törlése](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. Írja be a megerősítendő erőforráscsoport nevét, és válassza a **Törlés gombot.**

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja az Azure Cache for Redis-t egy Node.js alkalmazásból. Folytassa a következő rövid útmutatót az Azure Cache for Redis használatával egy ASP.NET webalkalmazással.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET webalkalmazást, amely egy Azure-gyorsítótárat használ a Redis számára.](./cache-web-app-howto.md)
