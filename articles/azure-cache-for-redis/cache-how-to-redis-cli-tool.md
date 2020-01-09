---
title: A Redis-CLI használata az Azure cache használatával a Redis
description: Megtudhatja, hogyan használhatja a *Redis-CLI. exe* parancssori eszközt az Redis-hez készült Azure cache-vel való interakcióhoz.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: a48e69f19db88c7823365964c2fe9c0629a078bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75412673"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>A Redis parancssori eszköz használata az Azure cache használatával a Redis

a *Redis-CLI. exe* egy népszerű parancssori eszköz, amely egy Azure cache-t biztosít a Redis ügyfélként való működéséhez. Ez az eszköz a Redis készült Azure cache szolgáltatással is használható.

Az eszköz a Windows-platformokhoz is elérhető a [Windows Redis parancssori eszközeinek](https://github.com/MSOpenTech/redis/releases/)letöltésével. 

Ha egy másik platformon szeretné futtatni a parancssori eszközt, töltse le az Azure cache-t a Redis [https://redis.io/download ról ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Gyorsítótár-hozzáférési információk összegyűjtése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A gyorsítótár eléréséhez a következő három módszer használatával gyűjthet adatokat:

1. Azure CLI [az az Redis List-Keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) használatával
2. Azure PowerShell a [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey) használatával
3. Az Azure Portalon.

Ebben a szakaszban a kulcsokat a Azure Portal fogja lekérni.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>A Redis-CLI. exe elérésének engedélyezése

Az Azure cache for Redis csak az SSL-portot (6380) engedélyezi alapértelmezés szerint. Az `redis-cli.exe` parancssori eszköz nem támogatja az SSL használatát. Két konfigurációs lehetőség közül választhat:

1. [A nem SSL port (6379) engedélyezése](cache-configure.md#access-ports) - **Ez a konfiguráció nem ajánlott** , mert ebben a konfigurációban a hozzáférési kulcsok küldése a TCP-n keresztül, tiszta szövegként történik. Ez a módosítás a gyorsítótárhoz való hozzáférést is veszélyeztetheti. Az egyetlen forgatókönyv, ahol érdemes megfontolni ezt a konfigurációt, ha csak egy tesztelési gyorsítótárhoz fér hozzá.

2. Töltse le és telepítse a [stunnel](https://www.stunnel.org/downloads.html).

    Futtassa a **stunnel grafikus felhasználói felületét** , és indítsa el a kiszolgálót.

    Kattintson a jobb gombbal a stunnel-kiszolgáló tálcán látható ikonjára, majd kattintson a **napló megjelenítése ablakra**.

    A stunnel-napló ablak menüjében kattintson a **konfiguráció** > **konfiguráció szerkesztése** elemre az aktuális konfigurációs fájl megnyitásához.

    Adja hozzá a következő bejegyzést a *Redis-CLI. exe fájlhoz* a **szolgáltatási definíciók** szakaszban. Szúrja be a tényleges gyorsítótár nevét `yourcachename`helyén. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Mentse és zárjuk be a konfigurációs fájlt. 
  
    A stunnel-napló ablak menüjében kattintson a **konfiguráció** > **újratöltési konfiguráció**elemre.


## <a name="connect-using-the-redis-command-line-tool"></a>Kapcsolódjon a Redis parancssori eszköz használatával.

A stunnel használatakor futtassa a *Redis-CLI. exe fájlt*, és csak a *portot*és a *hozzáférési kulcsot* (elsődleges vagy másodlagos) adja át a gyorsítótárhoz való csatlakozáshoz.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel és Redis – parancssori felület](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Ha tesztelési gyorsítótárat használ **a nem SSL** -porton keresztül, futtassa `redis-cli.exe`, és adja át az *állomásnevet*, a *portot*és a *hozzáférési kulcsot* (elsődleges vagy másodlagos) a tesztelési gyorsítótárhoz való csatlakozáshoz.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel és Redis – parancssori felület](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Következő lépések

További információ a Redis- [konzolnak](cache-configure.md#redis-console) a parancsok kiküldéséhez való használatáról.

