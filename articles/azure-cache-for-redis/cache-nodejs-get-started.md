---
title: 'Gyors útmutató: Azure Cache használata a Node.js redis |} A Microsoft Docs'
description: Ebben a rövid útmutatóban megismerheti, hogyan Azure Cache használata a Node.js és a node_redis redis lesz.
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: v-lincan
ms.assetid: 06fddc95-8029-4a8d-83f5-ebd5016891d9
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.tgt_pltfrm: azure-cache-for-redis
ms.workload: tbd
ms.date: 05/21/2018
ms.author: wesmc
ms.custom: mvc
ms.openlocfilehash: f47b1d35ada061601233506222903742393c6830
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54101751"
---
# <a name="quickstart-how-to-use-azure-cache-for-redis-with-nodejs"></a>Gyors útmutató: Az Azure Cache használata a Node.js redis



Az Azure Cache redis hozzáférést biztosít egy biztonságos, dedikált Azure Cache redis, a Microsoft felügyeli. A gyorsítótár a Microsoft Azure összes alkalmazásából elérhető.

Ez a témakör bemutatja, hogyan kezdheti el az Azure Cache redis Node.js használatával. 

A rövid útmutató lépései bármilyen szövegszerkesztővel elvégezhetők. A [Visual Studio Code](https://code.visualstudio.com/) például jó választás lehet, és Windows, macOS és Linux platformokon is használható.

![Kész gyorsítótár-alkalmazás](./media/cache-nodejs-get-started/cache-app-complete.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Előfeltételek
Telepítse a [node_redis](https://github.com/mranney/node_redis) ügyfelet:

    npm install redis

Ez az oktatóanyag a [node_redis](https://github.com/mranney/node_redis) ügyfelet használja. Az egyéb Node.js-ügyfeleket használó példákért tekintse meg az egyes Node.js-ügyfelek dokumentációját a [Node.js Redis-ügyfeleket](https://redis.io/clients#nodejs) felsoroló weblapon.


## <a name="create-a-cache"></a>Gyorsítótár létrehozása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](../../includes/redis-cache-access-keys.md)]


Adjon hozzá környezeti változókat a **GAZDAGÉP NEVE** és **Elsődleges** hozzáférési kulcs megadásához. A kód ezen változóit fogja használni ahelyett, hogy közvetlenül a kódban tárolná a bizalmas információkat.

```
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```


## <a name="connect-to-the-cache"></a>Csatlakozás a gyorsítótárhoz

A legújabb buildjei [node_redis](https://github.com/mranney/node_redis) támogatást nyújt a Redis SSL-lel az Azure Cache való csatlakozáshoz. Az alábbi példa bemutatja, hogyan csatlakozhat az Azure Cache redis 6380 SSL-végpont használatával. 

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

Ne hozzon létre új kapcsolatokat a kód minden műveletéhez. Ehelyett a lehető legtöbbször hasznosítsa újra a kapcsolatokat. 

## <a name="create-a-new-nodejs-app"></a>Új Node.js-alkalmazás létrehozása

Hozzon létre egy *redistest.js* nevű új szkriptfájlt.

Adja hozzá a következő példa JavaScriptet a fájlhoz. Ez a kód bemutatja, hogyan csatlakozhat egy Azure Cache a Redis-példányt a gyorsítótár-állomás nevének és a kulcs környezeti változók használatával. A kód emellett tárolja és lekéri gyorsítótár egyik sztringértékét. A rendszer a `PING` és a `CLIENT LIST` parancsot is végrehajtja. További példák a Redis használatára a [node_redis](https://github.com/mranney/node_redis) ügyféllel: [https://redis.js.org/](https://redis.js.org/).

```js
var redis = require("redis");
var bluebird = require("bluebird");

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

![Kész gyorsítótár-alkalmazás](./media/cache-nodejs-get-started/cache-app-complete.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy a következő oktatóanyaggal folytatja, megtarthatja és újból felhasználhatja az ebben a rövid útmutatóban létrehozott erőforrásokat.

Ha azonban befejezte az oktatóanyag mintaalkalmazásának használatát, a díjak elkerülése érdekében törölheti az ebben a rövid útmutatóban létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza; az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha a jelen minta üzemeltetését végző erőforrásokat egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforrásokat az erőforráscsoport törlése helyett külön-külön törölheti a megfelelő panelekről.
>

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és kattintson az **Erőforráscsoportok** elemre.

A **Szűrés név alapján...** mezőbe írja be az erőforráscsoport nevét. A jelen cikk utasításai egy *TestResources* nevű erőforráscsoportot használtak. Az eredménylistában kattintson a **…** ikonra az erőforráscsoport mellett, majd kattintson az **Erőforráscsoport törlése** elemre.

![Törlés](./media/cache-nodejs-get-started/cache-delete-resource-group.png)

A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be az erőforráscsoport nevét, és kattintson a **Törlés** gombra.

A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.



## <a name="next-steps"></a>További lépések

Ebből a gyors útmutatóból megtudhatta, hogyan Azure Cache a Redis használata a Node.js-alkalmazás. Folytassa a következő Azure Cache a Redis használata ASP.NET-webalkalmazás.

> [!div class="nextstepaction"]
> [Hozzon létre egy ASP.NET-webalkalmazás, amely egy Azure Cache Redis használ.](./cache-web-app-howto.md)



