---
title: Redis-fürtözés konfigurálása – prémium szintű Azure cache a Redis
description: Ismerje meg, hogyan hozhat létre és kezelhet Redis-fürtözést a prémium szintű Azure cache Redis-példányokhoz
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 761c464730096eba36bc7c04227745cf362e5cc6
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714465"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>A Redis-fürtözés konfigurálása prémium szintű Azure cache-Redis
A Redis készült Azure cache különböző gyorsítótár-ajánlatokat tartalmaz, amelyek rugalmasságot biztosítanak a gyorsítótár méretének és funkcióinak, beleértve a prémium szintű funkciókat, például a fürtözést, az adatmegőrzést és a virtuális hálózatok támogatását. Ez a cikk azt ismerteti, hogyan konfigurálható a fürtözés a prémium szintű Azure cache-ben a Redis-példányhoz.

További információ a prémium szintű gyorsítótár-funkciókról: [Bevezetés az Azure cache for Redis Premium](cache-premium-tier-intro.md)csomagba.

## <a name="what-is-redis-cluster"></a>Mi az a Redis-fürt?
A Redis-hez készült Azure cache a [Redis-ben implementált Redis-](https://redis.io/topics/cluster-tutorial)fürtöt kínál. A Redis-fürttel a következő előnyöket érheti el: 

* Lehetővé teszi az adatkészlet automatikus felosztását több csomópont között. 
* A műveletek folytatásának lehetősége, ha a csomópontok egy részhalmaza hibát észlel, vagy nem tud kommunikálni a fürt többi részével. 
* További átviteli sebesség: az átviteli sebesség a szegmensek számának növelésével lineárisan növekszik. 
* További memória mérete: a szegmensek számának növelésével lineárisan növekszik.  

A fürtözés nem fokozza a fürtözött gyorsítótár számára elérhető kapcsolatok számát. A mérettel, az átviteli sebességgel és a sávszélességgel kapcsolatos további információkért lásd: [Mi az Azure cache a Redis-ajánlathoz és a mérethez?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Az Azure-ban a Redis-fürt elsődleges/replika modellként érhető el, ahol az egyes szegmensek olyan elsődleges/replika párokkal rendelkeznek, ahol a replikálást a Redis szolgáltatáshoz tartozó Azure cache felügyeli. 

## <a name="clustering"></a>Fürtszolgáltatás
A fürtözés engedélyezve van a **Redis panel új Azure-gyorsítótárában** a gyorsítótár létrehozásakor. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

A fürtözés a **Redis-fürt** panelen van konfigurálva.

![Fürtszolgáltatás][redis-cache-clustering]

A fürtben legfeljebb 10 szegmens lehet. Kattintson az **engedélyezve** elemre, és csúsztassa a csúszkát, vagy írjon be egy 1 és 10 közötti számot a szegmensek **száma** mezőbe, és kattintson **az OK**gombra.

Az egyes szegmensek az Azure által kezelt elsődleges/replika gyorsítótár-párok, a gyorsítótár teljes mérete pedig a díjszabási szinten kiválasztott gyorsítótár-méret szorzatával számítható ki. 

![Fürtszolgáltatás][redis-cache-clustering-selected]

Miután létrehozta a gyorsítótárat, csatlakozik hozzá, és ugyanúgy használható, mint a nem fürtözött gyorsítótár, és a Redis elosztja az összes gyorsítótár-szegmenst. Ha a diagnosztika [engedélyezve](cache-how-to-monitor.md#enable-cache-diagnostics)van, a metrikák külön lesznek rögzítve az egyes szegmensekhez, és megtekinthetők a Redis panelhez készült Azure cache [-ben.](cache-how-to-monitor.md) 

> [!NOTE]
> 
> A fürtözés konfigurálásakor kisebb eltérések szükségesek az ügyfélalkalmazás számára. További információkért lásd: [az ügyfélalkalmazás használatának módosítása a fürtözéshez?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

A StackExchange. Redis ügyféllel való fürtözéssel kapcsolatos mintakód a ["Helló világ!" alkalmazás](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta [Clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) részében található.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>A fürt méretének módosítása egy futó prémium szintű gyorsítótárban
Ha módosítani szeretné a fürt méretét egy futó prémium szintű gyorsítótárban, amelyen engedélyezve van a fürtözés, kattintson a **fürt mérete** elemre az **erőforrás menüben**.

![Redis-fürt mérete][redis-cache-redis-cluster-size]

A fürt méretének módosításához használja a csúszkát, vagy írjon be egy 1 és 10 közötti számot a szegmensek **száma** szövegmezőbe, majd kattintson **az OK** gombra a mentéshez.

A fürt méretének növelése növeli a maximális átviteli sebességet és a gyorsítótár méretét. A fürt méretének növelése nem növeli a maximumot. az ügyfelek számára elérhető kapcsolatok.

> [!NOTE]
> A fürt méretezése az [áttelepítési](https://redis.io/commands/migrate) parancsot futtatja, amely egy költséges parancs, ezért a minimális hatás érdekében érdemes lehet ezt a műveletet a nem csúcsidőben töltött órákban futtatni. Az áttelepítési folyamat során egy tüske jelenik meg a kiszolgáló terhelésében. A fürt méretezése hosszú ideig futó folyamat, és az igénybe vett idő a kulcsok számától és méretétől függ.
> 
> 

## <a name="clustering-faq"></a>Fürtözés – gyakori kérdések
Az alábbi lista az Azure cache szolgáltatással történő Redis-fürtözéssel kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Kell-e módosítani az ügyfélalkalmazás számára a fürtözést?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hogyan történik a kulcsok elosztása a fürtben?](#how-are-keys-distributed-in-a-cluster)
* [Mi az a legnagyobb gyorsítótár-méret, amelyet Létrehozhatok?](#what-is-the-largest-cache-size-i-can-create)
* [Az összes Redis-ügyfél támogatja a fürtözést?](#do-all-redis-clients-support-clustering)
* [Hogyan csatlakozni a gyorsítótárhoz a fürtözés engedélyezésekor?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Csatlakozhatok közvetlenül a gyorsítótár egyedi szegmenséhez?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Beállíthat fürtszolgáltatást egy korábban létrehozott gyorsítótárhoz?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Konfigurálható az alapszintű vagy a standard szintű gyorsítótár fürtözése?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Használhatok fürtözést a Redis ASP.NET munkamenet-állapotával és a kimeneti gyorsítótárazási szolgáltatókkal?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Kivételeket kapok a StackExchange. Redis és fürtözési műveletek használatakor?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Kell-e módosítani az ügyfélalkalmazás számára a fürtözést?
* Ha a fürtözés engedélyezve van, csak az adatbázis 0 érhető el. Ha az ügyfélalkalmazás több adatbázist használ, és nem 0-nál több adatbázist próbál meg olvasni vagy írni, a következő kivételt kell eldobni. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  További információ: [Redis-fürt specifikációjának implementált részhalmaza](https://redis.io/topics/cluster-spec#implemented-subset).
* Ha a [StackExchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/)-t használja, akkor a 1.0.481 vagy újabb verziót kell használnia. A gyorsítótárhoz a fürtöket nem engedélyező gyorsítótárhoz való csatlakozáskor használt [végpontok, portok és kulcsok](cache-configure.md#properties) használatával csatlakozhat. Az egyetlen különbség, hogy minden olvasást és írást el kell végezni a 0. adatbázison.
  
  * Más ügyfelek eltérő követelményekkel rendelkezhetnek. Lásd: [az összes Redis-ügyfél támogatja a fürtözést?](#do-all-redis-clients-support-clustering)
* Ha az alkalmazás több kulcsfontosságú műveletet használ egyetlen parancsba, az összes kulcsnak ugyanabban a szegmensben kell lennie. Ha a kulcsokat ugyanabban a szegmensben szeretné megkeresni, tekintse meg, [hogyan oszlanak meg a kulcsok a fürtben?](#how-are-keys-distributed-in-a-cluster)
* Ha a Redis ASP.NET-szolgáltatót használja, a 2.0.1-es vagy újabb verzióját kell használnia. Lásd: használhatok [fürtözést a Redis ASP.NET munkamenet-állapotához és a kimeneti gyorsítótárazási szolgáltatókhoz?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hogyan történik a kulcsok elosztása a fürtben?
A Redis- [kulcsok terjesztési modellje](https://redis.io/topics/cluster-spec#keys-distribution-model) dokumentációja: a kulcs területe 16384 bővítőhelyre van bontva. Az egyes kulcsok kivonatolása és hozzárendelése az egyik ilyen tárolóhelyhez történik, amelyek a fürt csomópontjai között oszlanak meg. Beállíthatja, hogy a kulcs melyik részét használja a rendszer a kivonattal annak biztosítására, hogy több kulcs is ugyanabban a szegmensben legyen a kivonatoló címkék használatával.

* Kulcsok egy kivonatoló címkével – ha a kulcs bármely része `{` és `}`van lefoglalva, akkor a kulcs kivonatának meghatározásakor a rendszer csak a kulcs egy részét használja kivonatként. A következő 3 kulcs például ugyanabban a szegmensben található: `{key}1`, `{key}2`és `{key}3`, mert csak a név `key` része kerül kivonatolásra. A kulcsok kivonatoló címke specifikációinak teljes listáját lásd: [kulcsok kivonatának](https://redis.io/topics/cluster-spec#keys-hash-tags)címkéje.
* Kivonatoló címke nélküli kulcsok – a rendszer a teljes kulcs nevét használja a kivonatoláshoz. Ez statisztikailag egyenletes eloszlást eredményez a gyorsítótár szegmensei között.

A legjobb teljesítmény és átviteli sebesség érdekében javasoljuk, hogy egyenletesen osztja el a kulcsokat. Ha kivonatoló címkével rendelkező kulcsokat használ, akkor az alkalmazás feladata annak biztosítása, hogy a kulcsok egyenletesen legyenek elosztva.

További információ: [Keys Distribution Model](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis-fürt adatainak](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)horizontális felskálázása és [kulcsok kivonatának címkéje](https://redis.io/topics/cluster-spec#keys-hash-tags).

A fürtözés és a kulcsok megkeresése a StackExchange. Redis ügyféllel című szakaszban található mintakód a ["Helló világ!" alkalmazás](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta [Clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) részét képezi.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Mi az a legnagyobb gyorsítótár-méret, amelyet Létrehozhatok?
A prémium gyorsítótár legnagyobb mérete 120 GB. Akár 10 szegmenst is létrehozhat, amelyek maximális mérete 1,2 TB GB. Ha nagyobb méretre van szüksége, [további kérések](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase)is megadhatók. További információt az [Azure cache Redis díjszabását](https://azure.microsoft.com/pricing/details/cache/)ismertető témakörben talál.

### <a name="do-all-redis-clients-support-clustering"></a>Az összes Redis-ügyfél támogatja a fürtözést?
Nem minden ügyfél támogatja a Redis-fürtözést! Ellenőrizze a használt könyvtár dokumentációját, és ellenőrizze, hogy a fürtözést támogató könyvtárat és verziót használ-e. A StackExchange. Redis egy olyan függvénytár, amely támogatja a fürtözést az újabb verziókban. További információ a többi ügyfélről: a [Redis-fürt oktatóanyagának](https://redis.io/topics/cluster-tutorial) [fürttel való lejátszása](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) . 

A Redis-fürtszolgáltatási protokollhoz minden ügyfélnek hozzá kell kapcsolódnia az egyes szegmensekhez közvetlenül a fürtözési módban, valamint új hibaüzeneteket is definiál, például az "áthelyezett" Na "CROSSSLOTS". Ha olyan ügyfelet próbál meg használni, amely nem támogatja a fürtözési módú gyorsítótárazást, akkor az [áthelyezett átirányítási kivételek nagy részét képezik](https://redis.io/topics/cluster-spec#moved-redirection), vagy csak akkor szakíthatja meg az alkalmazást, ha több-kulcsot használó többkulcsos kérelmeket hajt végre.

> [!NOTE]
> Ha a StackExchange. Redis ügyfelet használja, ügyeljen arra, hogy a [StackExchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 vagy újabb verziójának legújabb verzióját használja a fürtözés megfelelő működéséhez. Ha problémák merülnek fel a kivételek áthelyezésével kapcsolatban, tekintse meg a [kivételek áthelyezése](#move-exceptions) című témakört.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hogyan csatlakozni a gyorsítótárhoz a fürtözés engedélyezésekor?
A gyorsítótárhoz a fürtözést nem engedélyező gyorsítótárhoz való csatlakozáskor használt [végpontok](cache-configure.md#properties), [portok](cache-configure.md#properties)és [kulcsok](cache-configure.md#access-keys) használatával csatlakozhat. A Redis kezeli a fürtözést a háttérben, így nem kell az ügyféltől kezelnie.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Csatlakozhatok közvetlenül a gyorsítótár egyedi szegmenséhez?
A fürtözési protokoll megköveteli, hogy az ügyfél a megfelelő szegmensű kapcsolatokat hozza létre. Így az ügyfélnek megfelelően kell ezt megtennie Önnek. Ez azt jelenti, hogy az egyes szegmensek egy elsődleges/replika gyorsítótár pár, a közösen gyorsítótár-példányként ismertek. A Redis-CLI segédprogrammal csatlakozhat ezekhez a gyorsítótár-példányokhoz a GitHubon a Redis-tárház [instabil](https://redis.io/download) ágában. Ez a verzió alapszintű támogatást valósít meg a `-c` kapcsolóval való elindulásakor. További információt a [Redis-fürt oktatóanyaga](https://redis.io/topics/cluster-tutorial) [https://redis.io](https://redis.io) a [fürttel való lejátszásáról](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) szóló témakörben talál.

Nem SSL esetén használja az alábbi parancsokat.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Az SSL esetében cserélje le a `1300N`t a `1500N`ra.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Beállíthat fürtszolgáltatást egy korábban létrehozott gyorsítótárhoz?
Igen. Először ellenőrizze, hogy a gyorsítótár prémium szintű-e, ha nem. Ezután meg kell tudnia tekinteni a fürtkonfiguráció beállításait, beleértve a fürt engedélyezésének lehetőségét is. A fürt méretét a gyorsítótár létrehozása után, vagy a fürtözés első alkalommal történő engedélyezése után módosíthatja.

   >[!IMPORTANT]
   >A fürtözés engedélyezése nem vonható vissza. És egy olyan gyorsítótár, amelyen engedélyezve van a fürtözés, és csak egy szegmens működik *, mint az* azonos méretű, fürtözés *nélküli* gyorsítótár.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Konfigurálható az alapszintű vagy a standard szintű gyorsítótár fürtözése?
A fürtözés csak a prémium szintű gyorsítótárak esetében érhető el.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Használhatok fürtözést a Redis ASP.NET munkamenet-állapotával és a kimeneti gyorsítótárazási szolgáltatókkal?
* **Redis kimeneti gyorsítótár-szolgáltató** – nincs szükség módosításra.
* **Redis munkamenet-szolgáltató** – a fürtözés használatához a [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1-es vagy újabb verzióját kell használnia, vagy kivételt kell eldobni. Ez egy megszakítási változás; További információ: [v 2.0.0 Breaking Change details](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Kivételeket kapok a StackExchange. Redis és fürtözési műveletek használatakor?
Ha a StackExchange. Redis-t használja, és a fürtözés használatakor `MOVE` kivételeket kap, győződjön meg arról, hogy a [StackExchange. Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) vagy újabb verziót használja. A .NET-alkalmazások StackExchange. Redis használatára való konfigurálásával kapcsolatos utasításokért lásd: [a gyorsítótár-ügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>Következő lépések
További információ a prémium szintű gyorsítótár-funkciók használatáról.

* [A prémium szintű Redis készült Azure cache bemutatása](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
