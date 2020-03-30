---
title: Az Azure Cache for Redis adatvesztéssel járó hibáinak elhárítása
description: Ismerje meg, hogyan oldhatja meg az adatvesztési problémákat az Azure Cache for Redis használatával, például a kulcsok részleges elvesztése, a kulcsok lejárata vagy a kulcsok teljes elvesztése.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530901"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Az Azure Cache for Redis adatvesztéssel járó hibáinak elhárítása

Ez a cikk ismerteti, hogyan diagnosztizálhatja a tényleges vagy észlelt adatveszteségek, amelyek előfordulhatnak az Azure Cache for Redis.

> [!NOTE]
> Ebben az útmutatóban a hibaelhárítási lépések közé tartozik a Redis-parancsok futtatására és a különböző teljesítménymutatók figyelésére vonatkozó utasítások. További információt és útmutatást a [További információk](#additional-information) című részben található cikkben talál.
>

## <a name="partial-loss-of-keys"></a>A kulcsok részleges elvesztése

A Redis Azure Cache nem törli véletlenszerűen a kulcsokat, miután azok a memóriában vannak tárolva. Azonban eltávolítja a kulcsokat a lejárati vagy kilakoltatási házirendek és explicit kulcstörlési parancsok válaszul. Kulcsok, amelyek a prémium szintű vagy standard szintű Azure-gyorsítótár redis-példányban a fő csomópontra írt kulcsok is előfordulhat, hogy nem érhető el a replika azonnal. Az adatok replikálása a kópiából a kópiába aszinkron és nem blokkoló módon történik.

Ha úgy találja, hogy a kulcsok eltűntek a gyorsítótárból, ellenőrizze a következő lehetséges okokat:

| Ok | Leírás |
|---|---|
| [Kulcs lejárata](#key-expiration) | A kulcsok at a rajtuk beállított időmegtánc miatt távolítja el a rendszer. |
| [Kulcs kilakoltatás](#key-eviction) | A kulcsokat memórianyomás alatt távolítja el a rendszer. |
| [Kulcs törlése](#key-deletion) | A kulcsokat explicit törlési parancsokkal távolítja el a rendszer. |
| [Aszinkron replikáció](#async-replication) | A kulcsok adatreplikációs késések miatt nem érhetők el a kópián. |

### <a name="key-expiration"></a>Kulcs lejárata

A Redis Azure Cache automatikusan eltávolítja a kulcsot, ha a kulcs idő-meghosszabbítása van rendelve, és ez az időszak lejárt. A Redis-kulcs lejáratáról további információt az [EXPIRE](https://redis.io/commands/expire) parancs dokumentációjában talál. Az időtúllépésre értékek a [SET,](https://redis.io/commands/set) [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset)és más ** \*STORE** parancsok használatával is beállíthatók.

Ha statisztikát szeretne kapni arról, hogy hány kulcs járt le, használja az [INFO](https://redis.io/commands/info) parancsot. A `Stats` szakasz a lejárt kulcsok teljes számát mutatja. A `Keyspace` szakasz további információt nyújt az időtúllépésekkel rendelkező kulcsok számáról és az átlagos időtúllépési értékről.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

A gyorsítótár diagnosztikai metrikáit is megtekintheti, hogy van-e összefüggés a kulcs eltűnésének és a lejárt kulcsok kiugrása között. A kulcstér-értesítések **használatával** kapcsolatos információkért tekintse meg a [Hibakeresési Redis Keyspace hiányzások](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) függelékét, illetve az ilyen típusú problémák hibakereséséről.

### <a name="key-eviction"></a>Kulcs kilakoltatás

A Redis Azure Cache szolgáltatása az adatok tárolásához memóriaterületet igényel. Törli a kulcsokat, hogy szükség esetén felszabadítsa a rendelkezésre álló memóriát. Amikor **az** USED_MEMORY vagy **used_memory_rss** értékek az [INFO](https://redis.io/commands/info) parancsban megközelíti a beállított **maxmemória-beállítást,** az Azure Cache for Redis a [gyorsítótár-házirend](https://redis.io/topics/lru-cache)alapján elindítja a kulcsok kizárását a memóriából.

A kilakoltatott kulcsok számát az [INFO](https://redis.io/commands/info) paranccsal figyelheti:

```
# Stats

evicted_keys:13224
```

A gyorsítótár diagnosztikai metrikáit is megtekintheti, hogy van-e összefüggés a kulcs eltűnésének és a kizárt kulcsok kiugró nyílása között. A kulcstér-értesítések **használatával** kapcsolatos információkért tekintse meg a [Hibakeresési Redis Keyspace hiányzások](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) függelékét, illetve az ilyen típusú problémák hibakereséséről.

### <a name="key-deletion"></a>Kulcs törlése

A Redis-ügyfelek kiadhatják a [DEL](https://redis.io/commands/del) vagy [HDEL](https://redis.io/commands/hdel) parancsot, hogy explicit módon eltávolítsák a kulcsokat az Azure Cache for Redis-ből. A törlési műveletek számát az [INFO](https://redis.io/commands/info) paranccsal követheti nyomon. Ha **DEL** vagy **HDEL** parancsokat hívtak meg, `Commandstats` azok a szakaszban jelennek meg.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Aszinkron replikáció

A Standard vagy prémium szintű szinten a Redis-példány bármely Azure-gyorsítótárazása fő csomóponttal és legalább egy replikával van konfigurálva. Az adatokat a mesteroldalból aszinkron módon, háttérfolyamattal másolja a kópiába. A [redis.io](https://redis.io/topics/replication) webhely általában leírja, hogyan működik a Redis adatreplikáció. Olyan esetekben, amikor az ügyfelek gyakran írnak a Redis-nek, részleges adatvesztés fordulhat elő, mert ez a replikáció garantáltan azonnali. Ha például a főkiszolgáló leáll, *miután* az ügyfél kulcsot ír hozzá, de *mielőtt* a háttérfolyamatnak lehetősége lenne a kópia elküldésére, a kulcs elvész, amikor a kópia átveszi az új mestert.

## <a name="major-or-complete-loss-of-keys"></a>A kulcsok jelentős vagy teljes elvesztése

Ha a legtöbb vagy az összes kulcs eltűnt a gyorsítótárból, ellenőrizze a következő lehetséges okokat:

| Ok | Leírás |
|---|---|
| [Kulcs öblítése](#key-flushing) | A kulcsok manuálisan lettek törölve. |
| [Helytelen adatbázis-kijelölés](#incorrect-database-selection) | A Redis Azure Cache úgy van beállítva, hogy nem alapértelmezett adatbázist használjon. |
| [Redis-példány hibája](#redis-instance-failure) | A Redis-kiszolgáló nem érhető el. |

### <a name="key-flushing"></a>Kulcs öblítése

Az ügyfelek meghívhatják a [FLUSHDB](https://redis.io/commands/flushdb) *parancsot,* hogy eltávolítsák az összes kulcsot egyetlen adatbázisban, vagy [a FLUSHALL-t,](https://redis.io/commands/flushall) hogy eltávolítsák az összes kulcsot a Redis *gyorsítótárösszes* adatbázisából. Ha meg szeretné tudni, hogy a kulcsok at kiürítették-e, használja az [INFO](https://redis.io/commands/info) parancsot. A `Commandstats` szakasz azt mutatja, hogy valamelyik **FLUSH** parancs lett-e meghívva:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Helytelen adatbázis-kijelölés

A Redis Azure Cache alapértelmezés szerint a **db0-adatbázist** használja. Ha átvált egy másik adatbázisra (például **db1),** és megpróbálja olvasni a kulcsokat belőle, az Azure Cache for Redis nem fogja megtalálni őket. Minden adatbázis logikailag különálló egység, és más adatkészletet tartalmaz. A [SELECT paranccsal](https://redis.io/commands/select) más rendelkezésre álló adatbázisokat is használhat, és mindegyikben kulcsokat kereshet.

### <a name="redis-instance-failure"></a>Redis-példány hibája

A Redis egy memórián belüli adattár. Az adatok a Redis-gyorsítótárat üzemeltető fizikai vagy virtuális gépeken maradnak. Az Alapszintű rétegRedis-gyorsítótárazott Azure-gyorsítótárazási példánya csak egyetlen virtuális gépen (VM) fut. Ha a virtuális gép nem érhető el, a gyorsítótárban tárolt összes adat elvész. 

A standard és prémium szintű gyorsítótárak sokkal nagyobb rugalmasságot biztosítanak az adatvesztéssel szemben két virtuális gép használatával egy replikált konfigurációban. Ha egy ilyen gyorsítótárban a fő csomópont meghibásodik, a replikacsomópont átveszi az adatok automatikus kiszolgálását. Ezek a virtuális gépek külön tartományokban találhatók a hibák és a frissítések, hogy minimálisra csökkenjen az esélye, hogy mindkettő egyszerre elérhetetlenné válik. Ha egy nagyobb adatközpont-kimaradás történik, azonban a virtuális gépek továbbra is leállhat nak együtt. Az ön adatai elvesznek ezekben a ritka esetekben.

Fontolja meg [a Redis adatmegőrzési](https://redis.io/topics/persistence) és [georeplikációs](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) használatával az adatok infrastruktúra-hibák elleni védelmének javítása érdekében.

## <a name="additional-information"></a>További információ

- [Az Azure Cache for Redis kiszolgálóoldali hibáinak elhárítása](cache-troubleshoot-server.md)
- [Milyen Azure-gyorsítótárat kell használni a Redis-ajánlathoz és a mérethez?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Az Azure-gyorsítótár figyelése a Redis számára](cache-how-to-monitor.md)
- [Hogyan futtathatom a Redis parancsokat?](cache-faq.md#how-can-i-run-redis-commands)
