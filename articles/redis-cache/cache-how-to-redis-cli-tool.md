---
title: A redis-cli használata az Azure Redis Cache |} A Microsoft Docs
description: Ismerje meg az Azure Redis Cache redis-cli használatával.
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 03/22/2018
ms.author: wesmc
ms.openlocfilehash: facc3e2079c8f5e19266f5379762d71754a6ed84
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38299091"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>Az Azure Redis Cache a Redis parancssori eszköz használata

*a redis-cli.exe* egy Redis Cache-ügyfélként folytatott interakcióra szolgáló népszerű parancssori eszköz. Ezzel az eszközzel is használható az Azure Redis Cache érhető el.

Az eszköz érhető el Windows platformokon úgy, hogy letölti a [Redis parancssori eszközei a Windows](https://github.com/MSOpenTech/redis/releases/). 

Ha azt szeretné, a parancssori eszköz futtatásához egy másik platformon, töltse le a Redis Cache [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Gyorsítótár hozzáférési adatainak összegyűjtése

Három módszerrel gyorsítótár-hozzáféréshez szükséges információkat gyűjthet:

1. Az Azure CLI használatával [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Az Azure PowerShell használatával [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Az Azure portal használatával.

Ebben a szakaszban az Azure Portalról lesz a kulcsok lekéréséhez.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>A redis-cli.exe hozzáférésének engedélyezése

Az Azure Redis Cache, csak az SSL-portot (6380) alapértelmezés szerint engedélyezve van. A `redis-cli.exe` parancssori eszköz nem támogatja az SSL. Két konfigurációs lehetőség közül használhatja azt:

1. [A nem SSL port (6379) engedélyezése](cache-configure.md#access-ports) - **Ez a konfiguráció nem ajánlott** mivel ebben a konfigurációban a hozzáférési kulcsok tiszta szöveges TCP-n keresztül küld. Ez a változás kedvezőtlenül befolyásolhatja a gyorsítótárhoz való hozzáférést. Az egyetlen forgatókönyv, ahol érdemes lehet a konfigurációs van csak tesztelési gyorsítótár elérésére.

2. Töltse le és telepítse [stunnel](https://www.stunnel.org/downloads.html).

    Futtatás **stunnel grafikus felhasználói felületének indítása** a kiszolgáló elindítására.

    Kattintson a jobb gombbal a tálca ikonjára a stunnel-kiszolgáló, és kattintson a **napló ablak megjelenítése**.

    Kattintson a stunnel napló ablak menü **konfigurációs** > **-konfiguráció szerkesztése** az aktuális konfigurációs fájl megnyitása.

    Adja hozzá a következő bejegyzés *a redis-cli.exe* alatt a **definíciók szolgáltatás** szakaszban. Helyezze be a tényleges gyorsítótár neve helyére `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Mentse és zárja be a konfigurációs fájlt. 
  
    Kattintson a stunnel napló ablak menü **konfigurációs** > **Újrabetöltés konfigurációs**.


## <a name="connect-using-the-redis-command-line-tool"></a>Kapcsolódás a Redis parancssori eszközzel.

Stunnel használata esetén futtassa *a redis-cli.exe*, és továbbíthatja azt csak a *port*, és *hívóbetű* (elsődleges vagy másodlagos) való csatlakozás a gyorsítótárhoz.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![a redis-cli stunnel](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Teszt gyorsítótár használata a **nem biztonságos** nem SSL port, futtassa `redis-cli.exe` és adja át a *állomásnév*, *port*, és *hívóbetű*(elsődleges vagy másodlagos) a teszt gyorsítótár csatlakozni.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![a redis-cli stunnel](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>További lépések

További információ a [Redis konzol](cache-configure.md#redis-console) parancsokat.

