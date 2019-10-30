---
title: Az Azure cache Redis-adatvesztéssel kapcsolatos hibáinak megoldása | Microsoft Docs
description: Ismerje meg, Hogyan oldhatók meg az Azure cache-sel kapcsolatos adatvesztési problémák a Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/17/2019
ms.author: yegu
ms.openlocfilehash: 4fee7c84b394e84369b28d2a4191d0e581f3beba
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/29/2019
ms.locfileid: "73044367"
---
# <a name="troubleshoot-azure-cache-for-redis-data-loss"></a>Az Azure cache Redis-adatvesztéssel kapcsolatos hibáinak megoldása

Ez a szakasz azt ismerteti, hogyan diagnosztizálhatja az Azure cache-ben esetlegesen előforduló tényleges vagy észlelt adatvesztést a Redis.

- [Kulcsok részleges elvesztése](#partial-loss-of-keys)
- [Kulcsok jelentős vagy teljes elvesztése](#major-or-complete-loss-of-keys)

> [!NOTE]
> Az útmutatóban ismertetett hibaelhárítási lépések többek között a Redis-parancsok futtatására és a különböző teljesítmény-mérőszámok figyelésére vonatkozó utasításokat tartalmaznak. További információkért és útmutatásért tekintse meg a [További információ](#additional-information) szakasz cikkeit.
>

## <a name="partial-loss-of-keys"></a>Kulcsok részleges elvesztése

A Redis nem távolítja el véletlenszerűen a kulcsokat, miután a memóriában tárolták őket. A kulcsok azonban le lesznek távolítva a lejárati vagy kizárási szabályzatokra, valamint explicit kulcs-törlési parancsokra válaszul. Emellett előfordulhat, hogy a Redis prémium vagy standard szintű Azure cache-ben a fő csomópontba írt kulcsok nem érhetők el azonnal a replikán. Az adatok replikálása a főkiszolgálóról a replikára aszinkron és nem blokkoló módon történik.

Ha azt tapasztalja, hogy a kulcsok eltűntek a gyorsítótárból, a következő lehetőséggel megtekintheti, hogy melyik oka lehet:

| Ok | Leírás |
|---|---|
| [Kulcs lejárata](#key-expiration) | A kulcsok el lesznek távolítva a rájuk beállított időtúllépés miatt |
| [Kulcs kizárása](#key-eviction) | A kulcsok el lesznek távolítva a memória nyomása alatt |
| [Kulcs törlése](#key-deletion) | A kulcsok eltávolítása explicit törlési parancsokkal történik |
| [Aszinkron replikáció](#async-replication) | A kulcsok nem érhetők el replikán az adatreplikálási késések miatt |

### <a name="key-expiration"></a>Kulcs lejárata

A Redis automatikusan eltávolítja a kulcsot, ha időtúllépést rendel hozzá, és az adott időszakot letelt. A Redis kulcs lejáratáról a [lejárati parancs](http://redis.io/commands/expire) dokumentációjában talál további információt. Az időtúllépési értékek a [set](http://redis.io/commands/set), a [SETEX](https://redis.io/commands/setex), a [GETSET](https://redis.io/commands/getset)és az Other \*Store parancsokon keresztül is megadhatók.

Az [info](http://redis.io/commands/info) paranccsal lekérheti, hogy hány kulcs lejárt. A *statisztika* szakasz a lejárt kulcsok teljes számát jeleníti meg. A *szóköz* szakasz további információkat tartalmaz az időtúllépésekkel rendelkező kulcsok számáról és az átlagos időtúllépési értékről.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Emellett megtekintheti a gyorsítótárhoz tartozó diagnosztikai mérőszámokat is, és megtekintheti, hogy van-e korreláció a kulcs hiánya és a lejárt kulcsok egy tüske között. Az ilyen típusú problémák hibakereséséhez tekintse meg a következő témakört [: a Space](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) Notifications vagy a monitor használatával kapcsolatos információk.

### <a name="key-eviction"></a>Kulcs kizárása

Az Redis lemezterületet igényel az adattároláshoz. Ha szükséges, a rendszer törli a kulcsokat a rendelkezésre álló memória felszabadításához. Ha a **used_memory** vagy a **Used_memory_rss** értéke az [info](http://redis.io/commands/info) parancsban megközelíti a konfigurált **maxmemory** beállítást, a Redis a [gyorsítótár-házirend](http://redis.io/topics/lru-cache)alapján megkezdi a kulcsok kizárását a memóriából.

Az [info](http://redis.io/commands/info) parancs használatával megfigyelheti a kizárt kulcsok számát.

```
# Stats

evicted_keys:13224
```

Emellett megtekintheti a gyorsítótárhoz tartozó diagnosztikai mérőszámokat is, és megtekintheti, hogy van-e korreláció a kulcs hiánya és a kizárt kulcsok egy tüske között. Az ilyen típusú problémák hibakereséséhez tekintse meg a következő témakört [: a Space](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) Notifications vagy a monitor használatával kapcsolatos információk.

### <a name="key-deletion"></a>Kulcs törlése

A Redis-ügyfelek kiadhatják a [del](http://redis.io/commands/del) vagy a [HDEL](http://redis.io/commands/hdel) parancsot, hogy explicit módon eltávolítsanak kulcsokat a Redis. Az [info](http://redis.io/commands/info) parancs használatával nyomon követheti a törlési műveletek számát. Ha a DEL vagy a HDEL parancs meghívása megtörtént, azok a *Commandstats* szakaszban lesznek felsorolva.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Aszinkron replikáció

A standard vagy prémium szinten található Redis bármely Azure-gyorsítótára egy fő csomóponttal és legalább egy replikával van konfigurálva. Az adatok a főkiszolgálóról egy replikába való másolása aszinkron módon, háttérben futó folyamat használatával történik. A [Redis.IO](http://redis.io/topics/replication) webhely leírja, hogyan működik az Redis adatreplikációja. Olyan forgatókönyvek esetén, ahol az ügyfelek gyakran Redis, a részleges adatvesztés oka az lehet, hogy a replikáció azonnali. Ha például a főkiszolgáló leáll, _miután_ egy ügyfél egy kulcsot ír, de _mielőtt_ a háttérben elküldi a kulcsot a replikának, a kulcs elvész, amikor a replika átveszi az új főkiszolgálót.

## <a name="major-or-complete-loss-of-keys"></a>Kulcsok jelentős vagy teljes elvesztése

Ha azt tapasztalja, hogy a legtöbb vagy az összes kulcs eltűnt a gyorsítótárból, a következő lehetőséggel megtekintheti, hogy melyik oka lehet:

| Ok | Leírás |
|---|---|
| [Kulcs kiürítése](#key-flushing) | A kulcsok manuális kiürítése megtörtént |
| [Helytelen adatbázis-kijelölés](#incorrect-database-selection) | A Redis nem alapértelmezett adatbázis használatára van beállítva |
| [Redis-példány hibája](#redis-instance-failure) | A Redis-kiszolgáló nem érhető el |

### <a name="key-flushing"></a>Kulcs kiürítése

Az ügyfelek meghívhatják a [FLUSHDB](http://redis.io/commands/flushdb) parancsot egy **önálló** adatbázis vagy [FLUSHALL](http://redis.io/commands/flushall) összes kulcsának eltávolítására egy Redis-gyorsítótárban lévő **összes adatbázis** kulcsának eltávolításához. Megtudhatja, hogy a kulcsok el lettek-e ürítve az [info](http://redis.io/commands/info) parancs használatával. Ekkor megjelenik, hogy megtörtént-e a FLUSH parancs meghívása a *Commandstats* szakaszban.

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Helytelen adatbázis-kijelölés

A Redis-hez készült Azure cache alapértelmezés szerint a **db0** adatbázist használja. Ha átvált egy másik adatbázisra (például db1), és megpróbálja beolvasni a kulcsokat, a Redis nem fogja tudni megkeresni őket, mert minden adatbázis egy logikailag különálló egység, és más adatkészletet tárol. A [Select](http://redis.io/commands/select) parancs használatával más elérhető adatbázisokat is használhat, és mindegyikben megkeresheti a kulcsokat.

### <a name="redis-instance-failure"></a>Redis-példány hibája

A Redis egy memóriában tárolt adattároló. Az adat a Redis üzemeltető fizikai vagy virtuális gépeken marad. Az alapszintű csomag Redis-példányához tartozó Azure cache csak egyetlen virtuális gépen (VM) fut. Ha a virtuális gép nem érhető el, a gyorsítótárban tárolt összes adattal elvész. A standard és a prémium szintű gyorsítótárak sokkal nagyobb rugalmasságot biztosítanak az adatvesztés ellen, ha két virtuális gépet használ egy replikált konfigurációban. Ha egy ilyen gyorsítótárban lévő főcsomópont meghibásodik, a replika csomópont átveszi az adatok automatikus kiszolgálását. Ezek a virtuális gépek különálló meghibásodási és frissítési tartományokon találhatók, így a lehető legkevesebb eséllyel elérhetetlenné válnak. Jelentős adatközpont-kimaradás esetén azonban a virtuális gépek továbbra is lemehetnek. Ezekben a ritka esetekben az adatai elvesznek.

Érdemes figyelembe venni a [Redis adatmegőrzést](http://redis.io/topics/persistence) és a [geo-replikálást](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) az adatvédelem ezen infrastrukturális hibák elleni védelme érdekében.

## <a name="additional-information"></a>További információk

- [Az Azure cache hibaelhárítása a Redis-kiszolgálókkal kapcsolatos problémák esetén](cache-troubleshoot-server.md)
- [Milyen Azure cache-t használ a Redis-ajánlat és-méret használatához?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Az Azure cache figyelése a Redis](cache-how-to-monitor.md)
- [Hogyan Futtathatok Redis-parancsokat?](cache-faq.md#how-can-i-run-redis-commands)
