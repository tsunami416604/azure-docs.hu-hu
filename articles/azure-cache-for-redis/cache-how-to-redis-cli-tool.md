---
title: A redis-cli használata az Azure Cache for Redis segítségével
description: Ismerje meg, hogyan használhatja a *redis-cli.exe-t* parancssori eszközként a Redis Azure-gyorsítótárával ügyfélként való interakcióhoz.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: a48e69f19db88c7823365964c2fe9c0629a078bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412673"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>A Redis parancssori eszköz használata az Azure Cache for Redis alkalmazással

*A redis-cli.exe* egy népszerű parancssori eszköz a Redis Azure-gyorsítótárával ügyfélként való interakcióhoz. Ez az eszköz az Azure Cache for Redis használatával is használható.

Az eszköz a [Windows Redis parancssori eszközeinek](https://github.com/MSOpenTech/redis/releases/)letöltésével érhető el a Windows rendszerhez. 

Ha a parancssori eszközt egy másik platformon szeretné futtatni, [https://redis.io/download](https://redis.io/download)töltse le az Azure Cache for Redis szolgáltatást a alkalmazásból.

## <a name="gather-cache-access-information"></a>Gyorsítótár-hozzáférési adatok gyűjtése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A gyorsítótár eléréséhez szükséges információkat három módszerrel gyűjtheti össze:

1. Azure CLI az [redis listakulcsok](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys) használatával
2. Azure PowerShell a [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey) használatával
3. Az Azure Portalon.

Ebben a szakaszban a kulcsokat az Azure Portalon.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Hozzáférés engedélyezése a redis-cli.exe programhoz

Az Azure Cache for Redis, csak az SSL-port (6380) alapértelmezés szerint engedélyezve van. A `redis-cli.exe` parancssori eszköz nem támogatja az SSL-t. Két konfigurációs lehetőség közül választhat:

1. [A nem SSL-port engedélyezése (6379)](cache-configure.md#access-ports) - **Ez a konfiguráció nem ajánlott,** mert ebben a konfigurációban a hozzáférési kulcsok at TCP-n keresztül, titkosítva küldik. Ez a módosítás veszélyeztetheti a gyorsítótárhoz való hozzáférést. Az egyetlen forgatókönyv, ahol érdemes figyelembe venni ezt a konfigurációt, amikor csak egy teszt gyorsítótár eléréséhez.

2. Töltse le és telepítse [stunnel](https://www.stunnel.org/downloads.html).

    **Futtassa a stunnel GUI Start programot** a kiszolgáló elindításához.

    Kattintson a jobb gombbal a stunnel kiszolgáló tálcaikonjára, és válassza a **Naplóablak megjelenítése parancsot.**

    Az aktuális konfigurációs fájl megnyitásához kattintson a Stunnel Naplóablak menü **Konfigurációs** > **konfigurációs szerkesztése parancsára.**

    Adja hozzá a következő bejegyzést a *redis-cli.exe-hez* a **Szolgáltatásdefiníciók** szakaszban. A tényleges gyorsítótár nevének `yourcachename`beszúrása a helyére. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Mentse és zárja be a konfigurációs fájlt. 
  
    Kattintson a Stunnel Naplóablak **menüKonfiguráció** > **újratöltése parancsra.**


## <a name="connect-using-the-redis-command-line-tool"></a>Csatlakozás a Redis parancssori eszközzel.

Stunnel használataesetén futtassa a *redis-cli.exe*programot, és csak a *portot*adja át, és a *hozzáférési kulcsot* (elsődleges vagy másodlagos) adja át a gyorsítótárhoz való csatlakozáshoz.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Ha nem **biztonságos** Nem SSL-porttal rendelkező tesztgyorsítótárat `redis-cli.exe` használ, futtassa és adja át az *állomásnevét,* *a portot*és a *hozzáférési kulcsot* (elsődleges vagy másodlagos) a tesztgyorsítótárhoz való csatlakozáshoz.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>További lépések

További információ arról, hogy miként adhat ki parancsokat a [Redis konzolhasználatával.](cache-configure.md#redis-console)

