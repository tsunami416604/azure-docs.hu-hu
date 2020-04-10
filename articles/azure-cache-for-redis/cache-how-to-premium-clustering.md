---
title: Redis-fürtözés konfigurálása – Prémium szintű Azure-gyorsítótár a Redis számára
description: Ismerje meg, hogyan hozhat létre és kezelhet Redis-fürtözést a prémium szintű Azure-gyorsítótárredis-példányokhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 4a0e5b0c18264e1f7a98e81bcdfd56a7159235da
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010919"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>A Redis-fürtözés beállítása prémium szintű Azure-gyorsítótárhoz a Redis számára
A Redis-gyorsítótár különböző gyorsítótár-ajánlatokkal rendelkezik, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és szolgáltatásainak kiválasztásában, beleértve a prémium szintű funkciókat, például a fürtözést, az adatmegőrzést és a virtuális hálózati támogatást. Ez a cikk ismerteti, hogyan konfigurálhatja a fürtözést egy prémium szintű Azure-gyorsítótár redis-példányban.

Az egyéb prémium szintű gyorsítótár-funkciókról [a Bevezetés az Azure Cache for Redis Premium tier című témakörbe.](cache-premium-tier-intro.md)

## <a name="what-is-redis-cluster"></a>Mi az a Redis Cluster?
A Redis Azure Cache for Redis redis-fürtöt kínál [a Redis-ben megvalósítva.](https://redis.io/topics/cluster-tutorial) A Redis Cluster a következő előnyökkel jár: 

* Az adatkészlet automatikus felosztása több csomópont között. 
* A műveletek folytatásának lehetősége, ha a csomópontok egy részhalmaza hibákat tapasztal, vagy nem tud kommunikálni a fürt többi részével. 
* Nagyobb átviteli: Átviteli szint lineárisan növekszik, ahogy növeli a szegmensek számát. 
* Több memóriaméret: A szilánkok számának növelésével lineárisan növekszik.  

A fürtözés nem növeli a fürtözött gyorsítótárhoz elérhető kapcsolatok számát. A méretről, az átviteli ról és a prémium szintű gyorsítótárakkal kapcsolatos sávszélességről a [Mi az Azure-gyorsítótár a Redis-ajánlatokhoz és -mérethez?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Az Azure-ban a Redis-fürt elsődleges/replikamodellként érhető el, ahol minden szegmens rendelkezik egy elsődleges/replika-pár replikációval, ahol a replikációt az Azure Cache for Redis szolgáltatás kezeli. 

## <a name="clustering"></a>Fürtözés
Fürtözés engedélyezve van az **új Azure-gyorsítótár redis** panel gyorsítótár létrehozása során. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

A fürtözés a **Redis cluster** panelen van konfigurálva.

![Fürtözés][redis-cache-clustering]

Legfeljebb 10 szilánkok a fürtben. Kattintson az **Engedélyezve gombra,** és csúsztassa a csúszkát, vagy írjon be egy számot 1 és 10 közé **a szegmensek számához,** majd kattintson az **OK**gombra.

Minden szegmens egy azure által kezelt elsődleges/replika gyorsítótár-pár, és a gyorsítótár teljes méretét úgy számítja ki, hogy megszorozza a szegmensek számát a tarifacsomagban kiválasztott gyorsítótár méretével. 

![Fürtözés][redis-cache-clustering-selected]

A gyorsítótár létrehozása után csatlakozik hozzá, és használja, mint egy nem fürtözött gyorsítótár, és redis osztja az adatokat a gyorsítótár szilánkok. Ha a diagnosztika [engedélyezve](cache-how-to-monitor.md#enable-cache-diagnostics)van, a metrikák minden egyes szegmenshez külön-külön kerülnek rögzítésre, és [megtekinthetők](cache-how-to-monitor.md) az Azure Cache for Redis panelen. 

> [!NOTE]
> 
> A fürtözés konfigurálásakor az ügyfélalkalmazásban kisebb különbségekre van szükség. További információ: [Az ügyfélalkalmazáson módosításokat kell végeznem a fürtözés használatához?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

A mintakódot a fürtözés a StackExchange.Redis ügyfél, tekintse meg a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) részét a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>A fürt méretének módosítása egy futó prémium szintű gyorsítótárban
Ha a fürtözés engedélyezésével rendelkező, működő prémium szintű gyorsítótárban szeretné módosítani a fürtméretet, kattintson az **Erőforrás menü** **Fürtméret parancsára.**

![Redis fürt mérete][redis-cache-redis-cluster-size]

A fürt méretének módosításához használja a csúszkát, vagy írjon be egy 1 és 10 közötti számot a **Szegmensszám** mezőbe, és kattintson az **OK** gombra a mentéshez.

A fürt méretének növelése növeli a maximális átviteli sebesség és a gyorsítótár méretét. A fürt méretének növelése nem növeli a maximális értéket. az ügyfelek számára elérhető kapcsolatokat.

> [!NOTE]
> A fürt méretezése futtatja a [MIGRATE](https://redis.io/commands/migrate) parancsot, amely egy költséges parancs, ezért a minimális hatás érdekében fontolja meg a művelet futtatását csúcsidőn kívüli időszakokban. Az áttelepítési folyamat során megjelenik egy csúcs a kiszolgáló terhelése. A fürt méretezése hosszú folyamat, és a szükséges idő a kulcsok számától és a kulcsokhoz társított értékek méretétől függ.
> 
> 

## <a name="clustering-faq"></a>Gyakori kérdések a fürtözésről
Az alábbi lista az Azure-gyorsítótár redis-fürtözéssel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Kell-e módosításokat végeznem az ügyfélalkalmazáson a fürtözés használatához?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hogyan történik a kulcsok elosztása a fürtben?](#how-are-keys-distributed-in-a-cluster)
* [Mi a legnagyobb gyorsítótárméret, amit létrehozhatok?](#what-is-the-largest-cache-size-i-can-create)
* [Minden Redis-ügyfél támogatja a fürtözést?](#do-all-redis-clients-support-clustering)
* [Hogyan csatlakozhatok a gyorsítótárhoz, ha a fürtözés engedélyezve van?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Közvetlenül csatlakozhatok a gyorsítótár egyes szegmenseihez?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Konfigurálhatom a fürtözést egy korábban létrehozott gyorsítótárhoz?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Konfigurálhatom a fürtözést alapszintű vagy szabványos gyorsítótárhoz?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Használhatok fürtözést a Redis ASP.NET munkamenet-állapot- és kimeneti gyorsítótárazási szolgáltatókkal?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Move kivételeket kapok a StackExchange.Redis és a fürtözés használatakor, mit tegyek?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Kell-e módosításokat végeznem az ügyfélalkalmazáson a fürtözés használatához?
* Ha a fürtözés engedélyezve van, csak a 0-s adatbázis érhető el. Ha az ügyfélalkalmazás több adatbázist használ, és a 0-tól eltérő adatbázisba próbál olvasni vagy írni, a következő kivétel jelenik meg. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  További információ: [Redis Cluster Specification - Implemented subset](https://redis.io/topics/cluster-spec#implemented-subset).
* [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)használata esetén az 1.0.481-es vagy újabb verziót kell használnia. A gyorsítótárhoz ugyanazokat a [végpontokat, portokat és kulcsokat használja,](cache-configure.md#properties) amelyeket akkor használ, amikor olyan gyorsítótárhoz csatlakozik, amelyen nincs engedélyezve a fürtözés. Az egyetlen különbség az, hogy minden olvasási és írási kell tenni, hogy adatbázis 0.
  
  * Más ügyfelek eltérő követelményeknek lehetnek. Lásd: [Minden Redis-ügyfél támogatja a fürtözést?](#do-all-redis-clients-support-clustering)
* Ha az alkalmazás több kulcsműveletet használ egyetlen parancsba kötegelve, az összes kulcsnak ugyanabban a szegmensben kell lennie. A kulcsok megkereséséhez ugyanabban a szegmensben, lásd: [Hogyan vannak elosztva a fürtben?](#how-are-keys-distributed-in-a-cluster)
* Ha redis ASP.NET munkamenet-állapot szolgáltatót használ, akkor a 2.0.1-es vagy újabb verziót kell használnia. Lásd: [Használhatom a fürtözést a Redis ASP.NET munkamenet-állapot- és kimeneti gyorsítótárazás-szolgáltatókkal?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hogyan történik a kulcsok elosztása a fürtben?
A Redis [Keys terjesztési modell](https://redis.io/topics/cluster-spec#keys-distribution-model) dokumentációja szerint: A kulcsterület 16384 bővítőhelyre van felosztva. Minden kulcs kivonatolt, és hozzá van rendelve az egyik ilyen bővítőhely, amely a fürt csomópontjai között oszlik meg. Beállíthatja, hogy a kulcs melyik része kivonatolt annak érdekében, hogy több kulcs található ugyanabban a szegmensben kivonatcímkék használatával.

* Kivonatcímkével ellátott billentyűk – ha a kulcs `{` bármely `}`része be van zárva, és a kulcsnak csak azt a részét kivonatolja a kulcs kivonatolása a kulcs kivonatolása céljából. Például a következő 3 kulcsok lenne található ugyanabban `{key}2`a `{key}3` szegmensben: `{key}1`, , és mivel csak a `key` része a név kivonatolt. A kulcsok kivonatoló címkéinek teljes listáját a [Keys kivonatoló címkéi ben](https://redis.io/topics/cluster-spec#keys-hash-tags)található.
* Kivonatcímke nélküli billentyűk – a teljes kulcsnév kivonatolásra szolgál. Ennek eredménye a gyorsítótár szegmensei közötti statisztikailag egyenletes eloszlás.

A legjobb teljesítmény és átviteli teljesítmény érdekében javasoljuk a kulcsok egyenletes elosztását. Ha kivonatcímkével rendelkező kulcsokat használ, az alkalmazás felelőssége a kulcsok egyenletes elosztása.

További információ: [Keys distribution model](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster data sharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), and [Keys hash tags](https://redis.io/topics/cluster-spec#keys-hash-tags).

A mintakódot a fürtözési és a kulcsok ugyanazon szegmensben a StackExchange.Redis ügyfél, tekintse meg a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) részét a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Mi a legnagyobb gyorsítótárméret, amit létrehozhatok?
A legnagyobb prémium szintű gyorsítótár mérete 120 GB. Legfeljebb 10 szilánkot hozhat létre, így legfeljebb 1,2 TB GB-os méretet biztosít. Ha nagyobb méretre van szüksége, [többet kérhet.](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) További információ: [Azure Cache for Redis Pricing](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>Minden Redis-ügyfél támogatja a fürtözést?
Nem minden ügyfél támogatja a Redis fürtözést! Ellenőrizze a használt tár dokumentációjában, hogy a fürtözést támogató tárat és -verziót használja-e. StackExchange.Redis egy könyvtár, amely támogatja a fürtözést, az újabb verziókban. A többi ügyfélről a [Redis-fürtoktató](https://redis.io/topics/cluster-tutorial) [program Játék a fürttel](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) című részében talál további információt. 

A Redis fürtkezelési protokoll megköveteli, hogy minden ügyfél közvetlenül fürtözési módban csatlakozzon az egyes szegmensekhez, és új hibaválaszokat is meghatároz, például "MOVED" na 'CROSSSLOTS'. Olyan ügyfél használatára tett kísérlet, amely nem támogatja a fürthálózati gyorsítótárral történő fürtözést, sok [ÁTHELYEZÉS-átirányítási kivételt](https://redis.io/topics/cluster-spec#moved-redirection)eredményezhet, vagy egyszerűen csak megszakíthatja az alkalmazást, ha többkulcsos kéréseket hajt elő.

> [!NOTE]
> Ha a StackExchange.Redis-t használja ügyfélként, győződjön meg arról, hogy a [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 vagy újabb verziójának legújabb verzióját használja a fürtözés megfelelő működéséhez. Ha bármilyen probléma merül fel az áthelyezési kivételekkel kapcsolatban, további információt az [áthelyezési kivételek](#move-exceptions) című témakörben talál.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hogyan csatlakozhatok a gyorsítótárhoz, ha a fürtözés engedélyezve van?
A gyorsítótárhoz ugyanazokat a [végpontokat,](cache-configure.md#properties) [portokat](cache-configure.md#properties)és [kulcsokat](cache-configure.md#access-keys) használva csatlakozhat, amelyeket akkor használ, ha fürtözés nem engedélyezve van. Redis kezeli a fürtözést a háttérrendszer, így nem kell kezelni az ügyféltől.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Közvetlenül csatlakozhatok a gyorsítótár egyes szegmenseihez?
A fürtözési protokoll megköveteli, hogy az ügyfél a megfelelő shard kapcsolatokat. Tehát az ügyfél nek ezt helyesen az Ön számára. Ezzel azt mondta, minden shard áll egy elsődleges/replika gyorsítótár pár, együttesen ismert gyorsítótár-példány. Ezekhez a gyorsítótárpéldányokhoz a redis-cli segédprogrammal csatlakozhat a GitHub Redis-tárházának [instabil](https://redis.io/download) ágában. Ez a verzió a kapcsolóval `-c` való indításkor megvalósítja az alapvető támogatást. További információ: [A fürt lejátszása](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [https://redis.io](https://redis.io) a [Redis-fürt oktatóanyagában.](https://redis.io/topics/cluster-tutorial)

Nem TLS esetén használja a következő parancsokat.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

A TLS `1300N` esetében `1500N`cserélje ki a helyébe.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Konfigurálhatom a fürtözést egy korábban létrehozott gyorsítótárhoz?
Igen. Először győződjön meg arról, hogy a gyorsítótár prémium, méretezés, ha nem. Ezután látnia kell a fürt konfigurációs beállításait, beleértve a fürt engedélyezésének lehetőségét is. A gyorsítótár létrehozása után vagy a fürtözés első alkalommal történő engedélyezése után módosíthatja a fürtméretet.

   >[!IMPORTANT]
   >A fürtözés engedélyezése nem vonható vissza. És a gyorsítótár fürtözés engedélyezve van, és csak egy shard másképp *viselkedik,* mint egy azonos méretű gyorsítótár fürtözés *nélkül.*

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Konfigurálhatom a fürtözést alapszintű vagy szabványos gyorsítótárhoz?
A fürtözés csak prémium szintű gyorsítótárak esetén érhető el.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Használhatok fürtözést a Redis ASP.NET munkamenet-állapot- és kimeneti gyorsítótárazási szolgáltatókkal?
* **Redis kimeneti gyorsítótár szolgáltató** – nincs szükség módosításra.
* **Redis session state provider** - a fürtözés használatához [a RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 vagy újabb verziót kell használnia, vagy kivételt kell tennie. Ez egy törés változás; További információ: [v2.0.0 Breaking Change Details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Move kivételeket kapok a StackExchange.Redis és a fürtözés használatakor, mit tegyek?
Ha StackExchange.Redis-t használ, és kivételeket kap `MOVE` a fürtözés használatakor, győződjön meg arról, hogy a [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) vagy újabb verziót használja. A .NET-alkalmazások StackExchange.Redis használatára történő konfigurálásával kapcsolatos [tudnivalókat a Gyorsítótár-ügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)című témakörben találja.

## <a name="next-steps"></a>További lépések
További információ a további prémium szintű gyorsítótár-funkciók használatáról.

* [Bevezetés az Azure Cache for Redis premium szintű rétegbe](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
