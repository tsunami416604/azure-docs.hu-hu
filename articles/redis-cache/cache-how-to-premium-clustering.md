---
title: "Prémium szintű Azure Redis Cache Redis Fürtszolgáltatás konfigurálása |} Microsoft Docs"
description: "Megtudhatja, hogyan hozhatja létre és kezelheti a fürtszolgáltatás a prémium csomagban Azure Redis Cache példány Redis"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: wesmc
ms.openlocfilehash: 16281cca4e4bc95e145317365d42382ab11fde93
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-redis-cache"></a>Prémium szintű Azure Redis Cache Redis Fürtszolgáltatás konfigurálása
Azure Redis Cache rendelkezik másik gyorsítótármappa ajánlatokat, amelyek gyorsítótár mérete és a funkciót, beleértve a prémium réteg szolgáltatások, például a fürtszolgáltatás, az adatmegőrzésre és a virtuális hálózat támogatásának rugalmasságot biztosítanak. Ez a cikk ismerteti, hogyan konfigurálhatja a fürtözést prémium szintű Azure Redis Cache példányt.

Más prémium gyorsítótár funkciókról további információért lásd: [az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Mi az a fürt Redis?
Azure Redis Cache Redis-fürt kínál [Redis megvalósított](http://redis.io/topics/cluster-tutorial). Redis-fürthöz töltse le a következő előnyöket biztosítja: 

* Automatikusan ossza fel a dataset több csomópont között lehetővé teszi. 
* Műveletek során egy részét a csomópontok folytathatja hibákat tapasztal, vagy nem tud kommunikálni a fürt többi. 
* További átviteli sebesség: átviteli lineárisan növeli, mivel a szilánkok számának növeléséhez. 
* További memória mérete: lineárisan növeli, mivel a szilánkok számának növeléséhez.  

Méret, átviteli sebesség és a prémium szintű gyorsítótárak sávszélesség kapcsolatos további információkért lásd: [milyen Redis Cache-ajánlatot és méretet használjam?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)

Az Azure Redis-fürt érhető el egy elsődleges vagy replika modell, ahol minden shard rendelkezik egy elsődleges vagy replika pár replikációs ahol a replikáció Azure Redis Cache szolgáltatás kezeli. 

## <a name="clustering"></a>Fürtözés
Fürtszolgáltatás engedélyezve van a **új Redis Cache** panel gyorsítótár létrehozása során. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Fürtszolgáltatás konfigurálva van a **Redis fürt** panelen.

![Fürtözés][redis-cache-clustering]

A fürtben legfeljebb 10 szilánkok lehet. Kattintson a **engedélyezve** és húzza a csúszkát, vagy írjon be egy számot 1 és 10 közötti **Shard száma** kattintson **OK**.

Minden shard egy elsődleges vagy replika gyorsítótár pár Azure kezeli, és a gyorsítótár teljes mérete szorzata. a szilánkok száma szerint a gyorsítótár méretét, a tarifacsomag kiválasztott. 

![Fürtözés][redis-cache-clustering-selected]

A gyorsítótár létrehozása után hozzá csatlakozni, és használja az ebben az esetben, például egy nem fürtözött gyorsítótárat, és Redis továbbítja az adatokat a gyorsítótár szilánkok egész. Ha diagnosztikai [engedélyezve](cache-how-to-monitor.md#enable-cache-diagnostics), metrikák külön-külön az egyes shard rögzíti és lehet [tekinthetők](cache-how-to-monitor.md) a Redis Cache paneljén. 

> [!NOTE]
> 
> Van néhány kisebb különbség az kötelező az ügyfél-alkalmazásban, ha a fürtszolgáltatás van beállítva. További információkért lásd: [van saját ügyfélalkalmazás fürtszolgáltatással használandó végezze el a módosításokat?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

Mintakód a StackExchange.Redis-ügyféllel fürtözési használatáról, tekintse meg a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) része a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Módosítsa a fürt méretét, a futó prémium gyorsítótár
A fürt méretét, a futó módosítása prémium gyorsítótár fürtözési engedélyezve van, kattintson a **fürtméret Redis** a a **erőforrás menü**.

> [!NOTE]
> Az Azure Redis Cache prémium szintjének általánosan rendelkezésre álló kiadott, amíg a fürtméret Redis funkció jelenleg előzetes verzió.
> 
> 

![Redis foglalásiegység-méret][redis-cache-redis-cluster-size]

A fürt méretének módosításához használja a csúszkát, vagy írjon be egy számot 1 és 10 között a **Shard száma** szöveges értéket, majd kattintson **OK** mentéséhez.

> [!NOTE]
> Egy fürt skálázás futtatja a [ÁTTELEPÍTÉSE](https://redis.io/commands/migrate) parancs, amely egy drága parancs, ezért a gyakorolt minimális hatás mellett, fontolja meg a művelet során nem csúcsidőre futtatása. Az áttelepítési folyamat során látni fogja a csúcs az igények kiszolgáló terhelését. Egy fürt skálázás folyamat fut egy hosszú, és a szükséges idő mértékét kulcsok száma és mérete ezeknek a kulcsoknak tartozó értékek.
> 
> 

## <a name="clustering-faq"></a>Fürtszolgáltatás – gyakori kérdések
Az alábbi lista tartalmazza az Azure Redis Cache fürtszolgáltatás gyakran feltett kérdésekre adott válaszok.

* [Van a fürtszolgáltatással használandó ügyfélalkalmazást végezze el a módosításokat?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hogyan el a fürt kulcsok?](#how-are-keys-distributed-in-a-cluster)
* [Mi az a legnagyobb gyorsítótárméret létre tudok hozni?](#what-is-the-largest-cache-size-i-can-create)
* [Minden Redis-ügyfelek támogatják fürtszolgáltatás?](#do-all-redis-clients-support-clustering)
* [Hogyan kapcsolódni a gyorsítótár Ha engedélyezve van a fürtszolgáltatás?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Képes közvetlenül kapcsolódni a gyorsítótár egyedi szilánkok?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Beállítható, egy korábban létrehozott gyorsítótár fürtszolgáltatás?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Beállítható, alapszintű vagy standard gyorsítótár fürtszolgáltatás?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Használható a Redis az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár-szolgáltatók fürtszolgáltatásra?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Azért kapom áthelyezés kivételek, StackExchange.Redis használata esetén, és a fürtszolgáltatás, mi a teendő?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Van a fürtszolgáltatással használandó ügyfélalkalmazást végezze el a módosításokat?
* Amikor a fürtszolgáltatás engedélyezve van, csak 0 adatbázis érhető el. Ha több adatbázist használ az ügyfélalkalmazás, és megkísérli annak olvasására vagy írására 0 eltérő adatbázis, a következő kivétel történt. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  További információkért lásd: [Redis betűcsoport-specifikációjában - megvalósított részhalmaza](http://redis.io/topics/cluster-spec#implemented-subset).
* Ha használ [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), 1.0.481 kell használnia, vagy később. Csatlakozás a gyorsítótárhoz azonos [végpontok, portokat és a kulcsok](cache-configure.md#properties) használni, amikor csatlakozik egy gyorsítótár, amely nem rendelkezik a fürtszolgáltatás engedélyezve van. Az egyetlen különbség, hogy az összes olvasási és írási azért van szükség adatbázis 0.
  
  * Más ügyfelek követelményei lehetnek. Lásd: [Redis-ügyfelek támogatják fürtszolgáltatás?](#do-all-redis-clients-support-clustering)
* Ha az alkalmazás be egy parancs kötegelni több kulcsú műveletek, minden kulcs ugyanazt a shard kell lennie. Ugyanazt a shard kulcsainak megkereséséhez lásd: [kulcsok terjesztését ismertető fürtben?](#how-are-keys-distributed-in-a-cluster)
* A Redis ASP.NET munkamenetállapot-szolgáltató használata használnia kell a 2.0.1 vagy újabb verzióját. Lásd: [használható meg, hogy a Redis az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár-szolgáltatók fürtszolgáltatásra?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hogyan el a fürt kulcsok?
A Redis / [kulcsok telepítési modell](http://redis.io/topics/cluster-spec#keys-distribution-model) dokumentáció: A kulcsfontosságú terület oszlik 16384 tárolóhelye. Minden kulcs kivonatolt és rendelt egyik, amelyek a fürt csomópontjai között. Beállíthatja, hogy melyik a kulcs részét kivonatolja győződjön meg arról, hogy ugyanazt a shard kivonatoló címkék használatával több kulcs található.

* Ha valamely része szerint a kulcs szimpla címkével ellátott kivonat - kulcsok `{` és `}`, a kulcs csak az része kivonatolja a kivonat tárolóhelye kulcs meghatározása céljából. Például a következő 3 kulcsok volna található ugyanazt a shard: `{key}1`, `{key}2`, és `{key}3` mivel csak a `key` funkció kivonatolja a név egy részét. Kulcsok kivonatoló címke specifikációk teljes listáját lásd: [kulcsok kivonat-címkék](http://redis.io/topics/cluster-spec#keys-hash-tags).
* Kulcsok nélkül egy ujjlenyomat - a teljes kulcsnév kivonatoláshoz használt. Az eredmény egy még akkor is, statisztikailag terjesztési gyorsítótár szilánkok között.

A legjobb teljesítmény és a teljesítményt javasoljuk, a kulcsok egyenletes elosztása. Kulcsok használata az alkalmazás felelőssége annak biztosítása érdekében a kulcsok kivonatoló címke egyenletes elosztása.

További információkért lásd: [kulcsok telepítési modell](http://redis.io/topics/cluster-spec#keys-distribution-model), [Redis fürt adatok horizontális](http://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), és [kulcsok kivonat-címkék](http://redis.io/topics/cluster-spec#keys-hash-tags).

A fürtszolgáltatás és a kulcsok helyének ugyanazt a shard a StackExchange.Redis-ügyféllel a mintakódot, tekintse meg a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) része a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Mi az a legnagyobb gyorsítótárméret létre tudok hozni?
A legnagyobb prémium gyorsítótár mérete 53 GB. Létrehozhat legfeljebb 10 szilánkok felkínálva 530 GB maximális mérettel. Ha nagyobb méretű képes [kérelem több](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). További információkért lásd: [Azure Redis Cache szolgáltatás díjszabása](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Minden Redis-ügyfelek támogatják fürtszolgáltatás?
Jelenleg nem minden ügyfél támogatja a Redis a fürtszolgáltatás. StackExchange.Redis egyike, amelyek támogatják azt. A más ügyfelekkel további információkért lásd: a [játszik és a fürt](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) szakasza a [Redis-fürt oktatóanyag](http://redis.io/topics/cluster-tutorial).

> [!NOTE]
> Ha az ügyfél StackExchange.Redis használ, győződjön meg arról, a legújabb verzióját használja [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 vagy újabb, a fürtszolgáltatás megfelelően működjön. Ha problémák merülnek fel a move-kivételek, olvassa el [helyezze át a kivételek](#move-exceptions) további információt.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hogyan kapcsolódni a gyorsítótár Ha engedélyezve van a fürtszolgáltatás?
Csatlakozhat a gyorsítótár azonos [végpontok](cache-configure.md#properties), [portok](cache-configure.md#properties), és [kulcsok](cache-configure.md#access-keys) használni, amikor csatlakozik egy gyorsítótár, amely nem rendelkezik a fürtszolgáltatás engedélyezve van. A redis kezeli a háttérkiszolgálón fürtszolgáltatással, így nem kell azt az ügyfél kezeléséhez.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Képes közvetlenül kapcsolódni a gyorsítótár egyedi szilánkok?
Ez hivatalosan nem támogatott. Az említett, minden egyes shard elsődleges vagy replika gyorsítótár párból áll, a gyorsítótárpéldány összefoglaló néven. Ezek a gyorsítótár példányok a redis-cli segédprogram használatával kapcsolódhat a [instabil](http://redis.io/download) ágat a github webhelyen a Redis-tárház. Ebben a verzióban megvalósítja az alapszintű támogatás indítva a `-c` váltani. További információ: [játszik és a fürt](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) a [http://redis.io](http://redis.io) a a [Redis-fürt oktatóanyag](http://redis.io/topics/cluster-tutorial).

Nem ssl használja a következő parancsokat.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Az SSL-hez, cserélje le a `1300N` rendelkező `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Beállítható, egy korábban létrehozott gyorsítótár fürtszolgáltatás?
Jelenleg csak engedélyezheti a fürtszolgáltatás a gyorsítótár létrehozásakor. Után a gyorsítótár létrejött, de nem adja hozzá a prémium szintű gyorsítótárba fürtszolgáltatás, vagy távolítsa el a fürtszolgáltatás prémium gyorsítótárában, a gyorsítótár létrehozása után módosíthatja a fürt méretét. Fürtszolgáltatás engedélyezve van, és csak egy shard prémium gyorsítótár eltér a prémium szintű gyorsítótár nincs fürtözési azonos méretűnek.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Beállítható, alapszintű vagy standard gyorsítótár fürtszolgáltatás?
Fürtszolgáltatás csak érhető el prémium gyorsítótárak esetében.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Használható a Redis az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár-szolgáltatók fürtszolgáltatásra?
* **Kimeneti gyorsítótár-szolgáltató redis** -nem igényelt módosításokat.
* **A redis munkamenetállapot-szolgáltató** - fürtszolgáltatással használandó kell használnia [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 vagy magasabb vagy kivétel lépett fel. Ez az használhatatlanná tévő változást; További információ: [v2.0.0 Megtörje Változásrészletek](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Azért kapom áthelyezés kivételek, StackExchange.Redis használata esetén, és a fürtszolgáltatás, mi a teendő?
Ha a StackExchange.Redis használ, és fogadni `MOVE` kivételek fürtözés használata esetén győződjön meg arról, hogy használja [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) vagy újabb. A .NET-alkalmazások StackExchange.Redis az konfigurálása, lásd: [a gyorsítótár-ügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan további premium gyorsítótár-funkciók használatára.

* [Az Azure Redis Cache prémium szintjének bemutatása](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







