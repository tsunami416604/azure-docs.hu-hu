---
title: Azure Redis Cache-minták |} Microsoft Docs
description: Azure Redis Cache használata
services: redis-cache
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 1f8d210c-ee09-4fe2-b63f-1e69246a27d8
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2017
ms.author: wesmc
ms.openlocfilehash: 0b6f89807d3252b750bd5208a7f758a06c9903d6
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
ms.locfileid: "27909696"
---
# <a name="azure-redis-cache-samples"></a>Azure Redis Cache-minták
Ez a témakör az Azure Redis Cache mintavételt, például a Kapcsolódás a gyorsítótár, olvasási és számára, és az adatok írásakor a gyorsítótárból, és az ASP.NET Redis Cache-szolgáltatókat használ forgatókönyvek listája. A minták között letölthető projektek, és néhány részletes útmutatást és kódtöredékek tartalmazza, de ne csatolja a letölthető projekt.

## <a name="hello-world-samples"></a>Hello world – minták
Ebben a szakaszban a minták megjelenítése csatlakozás az Azure Redis Cache példány és a gyorsítótárba, a különböző nyelveken használó adatok írásakor vagy olvasásakor alapjait, és a Redis-ügyfelek.

A [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) a példa bemutatja, hogyan hajthat végre különböző gyorsítótár-műveletekhez használatával a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET ügyfél.

Ez a példa bemutatja, hogyan:

* Használjon különböző kapcsolati lehetőségek
* Olvasási és írási objektumok számára, és a gyorsítótárból szinkron és aszinkron műveletek
* Redis MGET/MSET parancsok segítségével megadott kulcsok értéket ad vissza
* A Redis tranzakciós műveletek végrehajtása
* A Redis-listák és rendezett beállítása
* .NET-objektumok használatával JsonConvert objektumszerializáló tárolásához
* A Redis-készletek használata megvalósításához a címkézés
* A Redis-fürt használata

További információkért lásd: a [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) dokumentáció a githubon, és további használati forgatókönyvek esetén tekintse meg a [StackExchange.Redis.Tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/StackExchange.Redis.Tests) egység teszteket.

[Azure Redis Cache használata Python](cache-python-get-started.md) bemutatja, hogyan Ismerkedés az Azure Redis Cache segítségével a Python és a [redis-másolása](https://github.com/andymccurdy/redis-py) ügyfél.

[A gyorsítótárban .NET objektumok](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) .NET-objektumokat szerializálni, így írja azokat, és az Azure Redis Cache példány olvashatja őket egy módszert mutat. 

## <a name="use-redis-cache-as-a-scale-out-backplane-for-aspnet-signalr"></a>Redis gyorsítótár használata egy Csatlakozópanel kibővítési ASP.NET SignalR
A [Redis Cache-használja, az ASP.NET SignalR Csatlakozópanel egy kibővítési](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) példa bemutatja, hogyan használható az Azure Redis Cache, egy SignalR csatlakozópanel. Csatlakozópanel kapcsolatos további információkért lásd: [SignalR Scaleout a redis gyorsítótárral](http://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="redis-cache-customer-query-sample"></a>Redis gyorsítótár ügyfél lekérdezés minta
Adatok elérése a gyorsítótárból, és az adatmegőrzési storage adatokhoz hozzáférő közötti hasonlítja össze teljesítményét mutatja be. Ez a minta két projektet tartalmaz.

* [Hogyan Redis Cache a jobb teljesítmény érdekében az adatokat bemutató](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [Az adatbázis és a gyorsítótár a bemutató a kezdőérték](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>Az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár
A [használata Azure Redis Cache ASP.NET SessionState és OutputCache tárolására](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) minta bemutatja, hogyan Azure Redis Cache segítségével tárolja az ASP.NET munkamenet és a kimeneti gyorsítótár a SessionState és OutputCache szolgáltatók használata a Redis.

## <a name="manage-azure-redis-cache-with-maml"></a>Azure Redis gyorsítótár MAML kezelése
A [kezelése Azure Redis Cache használata Azure kezelési kódtárakat](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) minta bemutatja, hogyan lehet Azure kezelési kódtárakat kezeléséhez - (létrehozása / frissítése / törlése) a gyorsítótárhoz. 

## <a name="custom-monitoring-sample"></a>Egyéni minta figyelése
A [Redis gyorsítótár-figyelés adatok](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) példa bemutatja, hogyan férhet hozzá a figyelési adatok az Azure portálon kívül az Azure Redis Cache.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>A PHP és a Redis használatával készítettek Twitter-stílusú másolat
A [Retwis](https://github.com/SyntaxC4-MSFT/retwis) minta a Redis Hello World. A Redis és a PHP használatával írt minimális Twitter-stílusú közösségi hálózati másolat használatával a [Predis](https://github.com/nrk/predis) ügyfél. A forráskód célja, hogy nagyon egyszerűek legyenek, és egyszerre különböző megjelenítendő Redis adatstruktúrák.

## <a name="bandwidth-monitor"></a>Sávszélesség-figyelő
A [sávszélesség figyelő](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) minta lehetővé teszik az ügyfélen használt sávszélesség figyelését. A sávszélesség mérését, a minta futtatásához a gyorsítótár ügyfélszámítógépen, hívásokat a gyorsítótárba, és figyelje meg a sávszélesség a sávszélesség-figyelő minta által jelentett.

