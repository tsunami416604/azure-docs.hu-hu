---
title: Azure Redis Cache redis-cli használata |} Microsoft Docs
description: Útmutató az Azure Redis Cache redis-cli használandó.
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
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-redis-cache"></a>A Redis parancssori eszköz használata az Azure Redis Cache

*a redis-cli.exe* egy Redis gyorsítótárhoz ügyfélként való kommunikáció népszerű parancssori eszköz. Ezzel az eszközzel való használathoz az Azure Redis Cache is érhető el.

Az eszköz érhető el a Windows rendszerek úgy, hogy letölti a [Windows parancssori eszközök Redis](https://github.com/MSOpenTech/redis/releases/). 

Ha azt szeretné, a parancssori eszköz futtatására más platformon, töltse le a Redis Cache [ http://redis.io/download ](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Gyorsítótár-hozzáférés adatainak összegyűjtése

Három módszerrel gyorsítótár-hozzáféréshez szükséges információkat gyűjthet:

1. Az Azure parancssori felület használatával [az redis listázása](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Az Azure PowerShell használatával [Get-AzureRmRedisCacheKey](https://docs.microsoft.com/powershell/module/azurerm.rediscache/Get-AzureRmRedisCacheKey?view=azurermps-4.4.1)
3. Az Azure portál használatával.

Ebben a szakaszban a kulcsok kér le az Azure portálról.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Engedélyezze a hozzáférést a redis-cli.exe

Az Azure Redis Cache, csak az SSL port (6380) alapértelmezés szerint engedélyezve van. A `redis-cli.exe` parancssori eszköz nem támogatja az SSL. A használatára két konfigurációs lehetősége van:

1. [A nem SSL port (6379) engedélyezése](cache-configure.md#access-ports) - **Ez a konfiguráció nem ajánlott** mivel ebben a konfigurációban a hívóbetűk küldése TCP-n keresztül a tiszta szöveges. Ez a változás kedvezőtlenül befolyásolhatja a gyorsítótár a hozzáférést. Az egyetlen forgatókönyv, ahol érdemes ezt a konfigurációt akkor, ha csak egy teszt gyorsítótár elérésére.

2. Töltse le és telepítse [stunnel](https://www.stunnel.org/downloads.html).

    Futtatás **stunnel grafikus felhasználói felületének indítása** a kiszolgáló elindítására.

    Kattintson a jobb gombbal a tálcán ikonra a stunnel kiszolgáló, és kattintson a **napló ablak megjelenítése**.

    A stunnel napló ablakban menüben kattintson **konfigurációs** > **szerkesztése konfigurációs** az aktuális konfigurációs fájl megnyitása.

    Adja hozzá a következő bejegyzés *redis-cli.exe* alatt a **definíciók szolgáltatás** szakasz. Helyezze be a tényleges gyorsítótár neve helyett `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Mentse és zárja be a konfigurációs fájlt. 
  
    Kattintson a napló ablakban stunnel menü, **konfigurációs** > **Újrabetöltés konfigurációs**.


## <a name="connect-using-the-redis-command-line-tool"></a>Csatlakoztassa a Redis parancssori eszközzel.

Stunnel használata esetén futtassa *redis-cli.exe*, és adja át a csak a *port*, és *a hozzáférési kulcsot* (elsődleges vagy másodlagos) a gyorsítótárban való kapcsolódáshoz.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![a redis-cli stunnel](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Teszt gyorsítótár használata a **nem biztonságos** nem SSL port futtatása `redis-cli.exe` , és adja át a *állomásnév*, *port*, és *hozzáférési kulcs*(elsődleges vagy másodlagos) a test-gyorsítótárban való kapcsolódáshoz.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![a redis-cli stunnel](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>További lépések

További információ a [Redis konzol](cache-configure.md#redis-console) parancsokat.

