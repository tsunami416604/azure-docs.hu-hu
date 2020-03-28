---
title: Azure Cache for Redis-minták
description: 'Ismerje meg, hogyan használhatja az Azure Cache for Redis-t ezekkel a kódmintákkal: gyorsítótárhoz való csatlakozás, adatok olvasása és írása a gyorsítótárban, ASP.NET Azure Cache redis-szolgáltatók számára.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433404"
---
# <a name="azure-cache-for-redis-samples"></a>Azure Cache for Redis-minták
Ez a témakör az Azure Cache redis-minták listáját tartalmazza, amely olyan forgatókönyveket tartalmaz, mint például a gyorsítótárhoz való csatlakozás, az adatok olvasása és írása egy gyorsítótárba, valamint a redis-szolgáltatók ASP.NET Azure-gyorsítótár használata. Egyes minták letölthető projektek, és néhány lépésről-lépésre útmutatást nyújt, és tartalmazza a kódrészleteket, de nem kapcsolódik egy letölthető projekthez.

## <a name="hello-world-samples"></a>Hello világ minták
Ebben a szakaszban a minták bemutatják az alapokat az Azure-gyorsítótár redis-példányhoz való csatlakozáshoz, valamint az adatok olvasásához és a gyorsítótárba való írásához különböző nyelvek és Redis-ügyfelek használatával.

A [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta bemutatja, hogyan hajthatja végre a különböző gyorsítótár-műveleteket a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET ügyfél használatával.

Ez a minta bemutatja, hogyan:

* Különböző csatlakozási beállítások használata
* Objektumok olvasása és írása a gyorsítótárba és a gyorsítótárból szinkron és aszinkron műveletek használatával
* A megadott kulcsok értékeinek visszaadására a Redis MGET/MSET parancsok használata
* Redis tranzakciós műveletek végrehajtása
* Redis-listák és rendezett készletek munkája
* .NET-objektumok tárolása JsonConvert szerializálókkal
* A Címkézés megvalósítása Redis-készletek használatával
* A Redis Cluster szolgáltatás sal

További információ: [A StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentációa a GitHubon, és további használati forgatókönyvek a [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) egység tesztek.

[Az Azure Cache for Redis használata a Pythonnal](cache-python-get-started.md) bemutatja, hogyan kezdheti el az Azure Cache for Redis python és a [redis-py](https://github.com/andymccurdy/redis-py) ügyfél használatával.

[A gyorsítótárban lévő .NET-objektumok használata](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) a .NET-objektumok szerializálásának egyik módját mutatja, így a Redis-példány Azure Cache-ből írhatja és olvashatja őket. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Az Azure Cache for Redis használata kibővített hátsíkként ASP.NET SignalR számára
Az [Azure Cache for Redis, mint egy horizontális háttérsík ASP.NET SignalR-minta](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) bemutatja, hogyan használhatja az Azure Cache for Redis signalr backplane. A hátlapról további információt a [SignalR Scaleout with Redis című](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)témakörben talál.

## <a name="azure-cache-for-redis-customer-query-sample"></a>Azure-gyorsítótár a Redis ügyféllekérdezési mintájához
Ez a minta bemutatja a gyorsítótárból származó adatok elérésének és az adatmegőrzési tárolóból való hozzáférés közötti összehasonlítási teljesítményt. Ez a minta két projektből rendelkezik.

* [Bemutató, hogy az Azure Cache for Redis hogyan javíthatja a teljesítményt az adatok gyorsítótárazásával](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Az adatbázis és a gyorsítótár elmagossá tévő demójának magvetése](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET munkamenet-állapot és kimeneti gyorsítótárazás
A [Redis azure-gyorsítótárának használata ASP.NET SessionState és OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) minta tárolására bemutatja, hogyan használhatja az Azure Cache for Redis-t ASP.NET munkamenet- és kimeneti gyorsítótár ának tárolására a SessionState és outputCache-szolgáltatók redis-hez használatával.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Az Azure Cache for Redis kezelése MAML-rel
A [Redis manage Azure Cache for Redis](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) minta bemutatja, hogyan használhatja az Azure Management Libraries-t a gyorsítótár kezeléséhez – (Létrehozás/ frissítés/ törlés). 

## <a name="custom-monitoring-sample"></a>Egyéni figyelési minta
Az [Access Azure Cache for Redis monitoring adatminta](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) bemutatja, hogyan érheti el az Azure Cache for Redis figyelési adatait az Azure Portalon kívül.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>A Twitter-stílusú klón php és Redis használatával írt
A [Retwis](https://github.com/SyntaxC4-MSFT/retwis) minta a Redis Hello World. Ez egy minimális Twitter-stílusú szociális hálózat klón írt redis és php segítségével [Predis kliens.](https://github.com/nrk/predis) A forráskód úgy van kialakítva, hogy nagyon egyszerű legyen, és ugyanakkor különböző Redis adatstruktúrákat jelenítsen meg.

## <a name="bandwidth-monitor"></a>Sávszélesség-figyelő
A [Sávszélesség-figyelő](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) minta lehetővé teszi az ügyfélen használt sávszélesség figyelése. A sávszélesség méréséhez futtassa a mintát a gyorsítótár-ügyfélgépen, kezdeményezzen hívásokat a gyorsítótárba, és figyelje meg a sávszélesség-figyelő minta által jelentett sávszélességet.
