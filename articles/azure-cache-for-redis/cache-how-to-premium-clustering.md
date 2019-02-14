---
title: Redis-fürtözés egy prémium szintű Azure Cache redis konfigurálása |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre és kezelhet a Redis-példány a prémium szintű Azure Cache a Redis-fürtözés
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 62208eec-52ae-4713-b077-62659fd844ab
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 06/13/2018
ms.author: yegu
ms.openlocfilehash: 602d77f3d4e8ed10c2c964462bc2dc21240cef5c
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56235716"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Redis-fürtözés egy prémium szintű Azure Cache redis konfigurálása
Az Azure Cache redis rendelkezik másik Cache gyorsítótárazási szolgáltatások, ami rugalmasságot biztosít a gyorsítótár méretét és a szolgáltatásait, beleértve a Prémiumszintű funkciókkal, például a fürtözés, az adatmegőrzés és a virtuálishálózat-támogatást is nyújt. Ez a cikk azt ismerteti, hogy egy prémium szintű Azure Cache Redis-példányt a fürtözés konfigurálása.

Más prémiumszintű gyorsítótár funkcióival kapcsolatos tudnivalókért lásd: [Bevezetés az Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md).

## <a name="what-is-redis-cluster"></a>Mi az Redis-fürt?
Az Azure Cache redis kínál a Redis-fürt [redis megvalósított](https://redis.io/topics/cluster-tutorial). Redis-fürt akkor a következő előnyöket kínálja: 

* Lehetővé teszi az adatkészlet több csomópont között automatikusan felosztása. 
* Is használhatja, amikor egy részét a csomópontok operations hibákat tapasztal, vagy nem tudnak kommunikálni a fürt többi részétől. 
* További átviteli sebesség: Átviteli sebesség szegmensei számának növelésével költségráfordításokkal egyenes arányban növekszik. 
* További memória mérete: Szegmensei számának növelésével növeli a költségráfordításokkal egyenes arányban.  

Fürtszolgáltatás nem növeli a fürtözött Cache-gyorsítótárhoz elérhető kapcsolatok száma. Méret, teljesítmény és a sávszélesség a prémium gyorsítótárak kapcsolatos további információkért lásd: [milyen az Azure Cache a Redis-ajánlatot és -méretet használjam?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)

Az Azure-ban a Redis-fürttel, ahol minden egyes szegmens van egy elsődleges/replika pár replikációs ahol a replikáció által felügyelt Azure Cache Redis szolgáltatás elsődleges/replika modell érhető el. 

## <a name="clustering"></a>Fürtözés
A fürtözés engedélyezve van a **új Azure Cache redis** panel gyorsítótár létrehozása során. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

A fürtszolgáltatás van konfigurálva a **Redis-fürt** panelen.

![Fürtözés][redis-cache-clustering]

A fürt legfeljebb 10 szegmenssel rendelkezhet. Kattintson a **engedélyezve** , és húzza a csúszkát, vagy adjon meg egy számot 1 és 10 közötti **szegmensszám** kattintson **OK**.

Minden egyes szegmens egy Azure által kezelt elsődleges/replika gyorsítótár-pár, és a gyorsítótár teljes mérete szorzata. a szegmensek száma szerint a kiválasztott tarifacsomag kiválasztása a gyorsítótár méretét. 

![Fürtözés][redis-cache-clustering-selected]

A gyorsítótár létrehozása után kapcsolódhat hozzá, és azt csak, például egy nem fürtözött gyorsítótárat, és a Redis használata osztja el a gyorsítótár szegmensek során az adatokat. Ha diagnosztikai [engedélyezve](cache-how-to-monitor.md#enable-cache-diagnostics), metrikák külön-külön mindegyik szegmenshez rögzített, és [megtekintett](cache-how-to-monitor.md) Redis panel az Azure Cache-ben. 

> [!NOTE]
> 
> Az ügyfélalkalmazásban található megadása kötelező, ha konfigurálva van a fürtszolgáltatás néhány kisebb különbségek vannak. További információkért lásd: [van a fürtszolgáltatás használandó ügyfélalkalmazásomnak semmilyen módosítást?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 

A mintakód a fürtszolgáltatás a StackExchange.Redis ügyféllel dolgozik, lásd: a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) része a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>Módosítsa a fürt méretét, egy futó a prémium szintű gyorsítótár
Módosítsa a fürt méretét a futó prémium szintű gyorsítótár-fürtözés engedélyezve van, kattintson a **Redis-fürt mérete** származó a **erőforrás menüben**.

> [!NOTE]
> Az általánosan elérhető az Azure Cache redis Cache prémium szint kiadása, míg a Redis-fürt mérete szolgáltatás jelenleg előzetes verzióban érhető el.
> 
> 

![Redis-fürt mérete][redis-cache-redis-cluster-size]

A fürt méretének módosításához a csúszka, vagy adjon meg egy számot 1 és 10 között a **szegmensszám** szövegmezőbe, majd kattintson **OK** mentéséhez.

A fürt méretének növelése növeli a maximális átviteli sebesség és a gyorsítótár méretét. A fürtméret növelése nem növeli a maximális száma. az ügyfelek számára elérhető kapcsolatok.

> [!NOTE]
> Fürt méretezése futtatja a [ÁTTELEPÍTÉSE](https://redis.io/commands/migrate) parancs, amely egy költséges parancs, így csak minimális hatással van, fontolja meg a művelet során nem csúcsidőre futtatása. Az áttelepítési folyamat során látni fogja a kiszolgáló terhelése ugrásszerű. Fürt méretezése egy hosszú ideig fut-e folyamat, és igénybe vett idő mennyisége a kulcsok száma és mérete ezeknek a kulcsoknak társított értékek függ.
> 
> 

## <a name="clustering-faq"></a>Fürtszolgáltatás – gyakori kérdések
Az alábbi lista a Redis-fürtözés az Azure Cache kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

* [Kell saját ügyfélalkalmazás használata a fürtszolgáltatás semmilyen módosítást?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hogyan oszlanak meg kulcsokat egy fürtben?](#how-are-keys-distributed-in-a-cluster)
* [Mi az a legnagyobb gyorsítótár mérete, is létrehozhatok?](#what-is-the-largest-cache-size-i-can-create)
* [A Redis-ügyfelek támogatják a fürtszolgáltatás?](#do-all-redis-clients-support-clustering)
* [Hogyan létesíthetek kapcsolatot a gyorsítótár, ha a fürtözés engedélyezve van?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Képes közvetlenül kapcsolódni a gyorsítótár az egyes szegmensek?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Beállítható egy korábban létrehozott gyorsítótár fürtözésének?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Beállítható egy alapszintű vagy standard szintű gyorsítótár fürtözésének?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Használható a Redis az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár-szolgáltatók a fürtszolgáltatás?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Érkeznek meg hozzám a MOVE-kivételek StackExchange.Redis használatakor, és a fürtszolgáltatást, Mit tegyek?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Kell saját ügyfélalkalmazás használata a fürtszolgáltatás semmilyen módosítást?
* Ha a fürtözés engedélyezve van, csak adatbázis 0 érhető el. Ha az ügyfélalkalmazás több adatbázist használ, és megkísérli annak 0 helyadatbázisától eltérő adatbázis írására vagy olvasására, a következő kivétel történt. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  További információkért lásd: [Redis Clusteru Glyphs - megvalósított részhalmazát](https://redis.io/topics/cluster-spec#implemented-subset).
* Ha használ [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/), 1.0.481 kell használnia, vagy később. Ugyanazzal a gyorsítótárhoz csatlakozik [végpontokat, a portok és a kulcsok](cache-configure.md#properties) telepítésekor, amely nem rendelkezik a fürtözés engedélyezve van egy cache szolgáltatáshoz való csatlakozáskor használt. Az egyetlen különbség, hogy minden olvasási és írási kell végezni adatbázis 0.
  
  * Előfordulhat, hogy az ügyfelek számára különböző követelmények vonatkoznak. Lásd: [Redis-ügyfelek támogatják a fürtszolgáltatás?](#do-all-redis-clients-support-clustering)
* Ha az alkalmazás több kulcsfontosságú műveletek egyetlen paranccsal történő kötegelt használja, minden kulcs ugyanabban a szegmensben kell működnie. Keresse meg a kulcsot az ugyanabban a szegmensben, lásd: [hogyan kulcsok oszlanak meg egy fürtben?](#how-are-keys-distributed-in-a-cluster)
* Ha használja a Redis az ASP.NET-munkamenetállapot-szolgáltatója 2.0.1 kell használnia, vagy magasabb. Lásd: [használható meg, hogy a Redis az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár-szolgáltatók a fürtszolgáltatás?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hogyan oszlanak meg kulcsokat egy fürtben?
A Redis kiszolgálónként [kulcsok telepítési modell](https://redis.io/topics/cluster-spec#keys-distribution-model) dokumentáció: A fő területet oszlik, 16384 tárolóhelyek. Minden egyes kulcs kivonatolt, és a egy ezek a tárhelyek és a fürt csomópontjai között oszlanak meg. Beállíthatja, hogy mely kulcs része, ezzel a győződjön meg arról, hogy több kulcs kivonata címkék használatával ugyanabban a szegmensben találhatók.

* Ha a kulcs bármely részét közé van zárva, egy ujjlenyomat - kulcsok `{` és `}`, a kivonatoló tárolóhely kulcs meghatározása céljából kivonatolt kulcs csak az része. Például a következő 3 kulcsokat kellene található ugyanabban a szegmensben: `{key}1`, `{key}2`, és `{key}3` mivel csak a `key` kivonatolt a név egy részét. Kulcsok kivonatoló címke leírások teljes listáját lásd: [kulcsok ujjlenyomat-címkék](https://redis.io/topics/cluster-spec#keys-hash-tags).
* Kivonatoló kulcsok kivonatoló címke – a teljes kulcsnév nélkül használható. Az eredmény egy statisztikailag egyenletes eloszlás a gyorsítótár a szegmensek között.

A legjobb teljesítmény és átviteli sebesség javasoljuk, egyenletes elosztása a kulcsokat. A kulcsok használata az alkalmazás feladata annak biztosítása érdekében a kulcsok kivonatoló címke lesznek elosztva.

További információkért lásd: [kulcsok telepítési modell](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis-fürt adatokat horizontális skálázási](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding), és [kulcsok ujjlenyomat-címkék](https://redis.io/topics/cluster-spec#keys-hash-tags).

Mintakód a fürtszolgáltatás és a kulcsok keresése ugyanabban a szegmensben a StackExchange.Redis ügyféllel dolgozik, lásd: a [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) része a [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Mi az a legnagyobb gyorsítótár mérete, is létrehozhatok?
A legnagyobb prémium szintű gyorsítótár mérete 53 GB-os. Legfeljebb 10 szegmenssel 530 GB maximális mérettel így hozhat létre. Ha egy nagyobb méretű kell [további kérelem](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase). További információkért lásd: [Azure Cache Redis díjszabási](https://azure.microsoft.com/pricing/details/cache/).

### <a name="do-all-redis-clients-support-clustering"></a>A Redis-ügyfelek támogatják a fürtszolgáltatás?
Jelenleg nem minden ügyfél támogatja a Redis-fürtözés. StackExchange.Redis, amelyik támogatja azt. Az ügyfelek számára további információkért lásd: a [játszik és a fürt](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) szakaszában a [Redis-fürt oktatóanyagát](https://redis.io/topics/cluster-tutorial). 

A Redis protokoll nevű fürtözési minden ügyféllel csatlakozzon az egyes szegmensek közvetlenül a csoportosítási mód szükséges. Próbál használni, amely nem támogatja a fürtképző lesz valószínűleg sok ügyfél [át lettek HELYEZVE átirányítás kivételek](https://redis.io/topics/cluster-spec#moved-redirection).

> [!NOTE]
> Ha az ügyfél a StackExchange.Redis használ, győződjön meg arról, a legújabb verzióját használja, [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 vagy újabb, a fürtszolgáltatás megfelelően működjenek. Ha bármilyen problémába ütközik a move-kivételek, [kivételek áthelyezése](#move-exceptions) további információt.
> 
> 

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hogyan létesíthetek kapcsolatot a gyorsítótár, ha a fürtözés engedélyezve van?
Csatlakozhat a gyorsítótárat használó [végpontok](cache-configure.md#properties), [portok](cache-configure.md#properties), és [kulcsok](cache-configure.md#access-keys) telepítésekor, amely nem rendelkezik a fürtözés engedélyezve van egy cache szolgáltatáshoz való csatlakozáskor használt. A redis kezeli a háttérbeli fürtözés, így nem kell kezelni az ügyfélről.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Képes közvetlenül kapcsolódni a gyorsítótár az egyes szegmensek?
A fürtszolgáltatás protokoll szükséges, hogy az ügyfél ellenőrizze a megfelelő szegmensre kapcsolatokat. Így az ügyfél tegye ezt, megfelelően az Ön számára. Az adott mondta minden egyes szegmens egy elsődleges/replika gyorsítótár pár összefoglaló néven gyorsítótárpéldány áll. Ezek a redis-cli segédprogram a cache-példányokban csatlakozhat a [instabil](https://redis.io/download) a Redis főága, a github webhelyen. Ez a verzió alapszintű támogatás indításakor a valósítja meg a `-c` váltani. További információ: [játszik és a fürt](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) a [ https://redis.io ](https://redis.io) a a [Redis-fürt oktatóanyagát](https://redis.io/topics/cluster-tutorial).

A nem ssl használja a következő parancsokat.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Az SSL-hez, cserélje le a `1300N` a `1500N`.

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Beállítható egy korábban létrehozott gyorsítótár fürtözésének?
Jelenleg csak engedélyezheti a fürtözés, amikor a gyorsítótár létrehozása. Után a gyorsítótár létrejött, de nem egy prémium szintű gyorsítótár a fürt hozzáadása vagy eltávolítása a fürtszolgáltatás a prémium szintű gyorsítótár, a gyorsítótár létrehozása után módosíthatja a fürt méretét. Egy prémium szintű gyorsítótár a fürtözés engedélyezve van, és csak egy szegmens nem fürtözési azonos méretű prémium szintű gyorsítótár eltér.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Beállítható egy alapszintű vagy standard szintű gyorsítótár fürtözésének?
Fürtszolgáltatás lehetőség csak a prémium gyorsítótárak.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Használható a Redis az ASP.NET munkamenet-állapot és a kimeneti gyorsítótár-szolgáltatók a fürtszolgáltatás?
* **Redis Cache kimeneti gyorsítótár-szolgáltatóját** – nincs szükség módosítására.
* **A redis munkamenetállapot-szolgáltatója** – fürtözés, használandó kell használnia [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 vagy magasabb vagy kivétel lépett fel. Ez a használhatatlanná tévő változást; További információ: [v2.0.0 használhatatlanná tévő változás részletei](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details).

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Érkeznek meg hozzám a MOVE-kivételek StackExchange.Redis használatakor, és a fürtszolgáltatást, Mit tegyek?
Ha a StackExchange.Redis használata kap `MOVE` kivételek fürtözés használata esetén győződjön meg arról, hogy használja [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) vagy újabb. A StackExchange.Redis használatára .NET-alkalmazások konfigurálása, lásd: [a gyorsítótárügyfelek konfigurálása](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients).

## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan használja a további prémiumszintű gyorsítótár funkcióival.

* [Bevezetés az Azure Cache redis Cache prémium szint](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png







