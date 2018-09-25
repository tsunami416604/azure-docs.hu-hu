---
title: Azure Redis Cache konfigurálása |} A Microsoft Docs
description: Az Azure Redis Cache Redis alapértelmezett konfigurációinak megértését, és megtudhatja, hogyan konfigurálhatja az Azure Redis Cache-példány
services: redis-cache
documentationcenter: na
author: wesmc7777
manager: cfowler
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: wesmc
ms.openlocfilehash: 58e913aa2b4a89a573f6d901803979e662c27af2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954041"
---
# <a name="how-to-configure-azure-redis-cache"></a>Azure Redis Cache konfigurálása
Ez a témakör ismerteti az Azure Redis Cache-példány érhető el a konfigurációkat. Ez a témakör azt is tartalmazza, az alapértelmezett Redis kiszolgáló konfigurálása az Azure Redis Cache-példányokban.

> [!NOTE]
> Konfigurálása és használata a prémiumszintű gyorsítótár funkcióival további információkért lásd: [adatmegőrzés konfigurálása](cache-how-to-premium-persistence.md), [fürtözés konfigurálása](cache-how-to-premium-clustering.md), és [Virtuálishálózat-támogatást konfigurálása ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Redis gyorsítótár beállításainak konfigurálása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Az Azure Redis Cache beállításai tekinthetők meg, és konfigurálni a **Redis Cache** panel használatával a **erőforrás menüben**.

![Redis gyorsítótár beállításai](./media/cache-configure/redis-cache-settings.png)

Megtekintheti és használatával a következő beállításokat konfigurálhatja a **erőforrás menüben**.

* [Áttekintés](#overview)
* [Tevékenységnapló](#activity-log)
* [Hozzáférés-vezérlés (IAM)](#access-control-iam)
* [Címkék](#tags)
* [Problémák diagnosztizálása és megoldása](#diagnose-and-solve-problems)
* [Beállítások](#settings)
    * [Hozzáférési kulcsok](#access-keys)
    * [Speciális beállítások](#advanced-settings)
    * [Redis Cache Advisor](#redis-cache-advisor)
    * [Méretezés](#scale)
    * [Redis-fürt mérete](#cluster-size)
    * [Redis-adatmegőrzés](#redis-data-persistence)
    * [Frissítések ütemezése](#schedule-updates)
    * [Georeplikáció](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Tűzfal](#firewall)
    * [Tulajdonságok](#properties)
    * [Zárolások](#locks)
    * [Automation-szkript](#automation-script)
* [Felügyelet](#administration)
    * [Adatok importálása](#importexport)
    * [Adatok exportálása](#importexport)
    * [Újraindítás](#reboot)
* [Monitorozás](#monitoring)
    * [Redis-metrikák](#redis-metrics)
    * [Riasztási szabályok](#alert-rules)
    * [Diagnosztika](#diagnostics)
* [Támogatás és hibaelhárítás beállításai](#support-amp-troubleshooting-settings)
    * [A Resource health](#resource-health)
    * [Új támogatási kérelem](#new-support-request)


## <a name="overview"></a>Áttekintés

**Áttekintés** biztosít, és alapvető információkat szeretne a gyorsítótár, például nevét, a portok, a tarifacsomag, és kijelölt gyorsítótár-metrikák.

### <a name="activity-log"></a>Tevékenységnapló

Kattintson a **tevékenységnapló** művelet elvégezhető a gyorsítótár megtekintéséhez. Is használhatja szűrés bontsa ki a más erőforrások, ezt a nézetet. A vizsgálati naplók használatáról további információkért lásd: [auditálási műveletek a Resource Manager](../azure-resource-manager/resource-group-audit.md). Azure Redis Cache események figyelésével kapcsolatos további információkért lásd: [műveletek és a riasztások](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlés (IAM)** szakasz támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Portalon. Ez a konfiguráció segít a szervezeteknek felel meg a hozzáférési követelményeket, egyszerűen és pontosan. További információkért lásd: [szerepköralapú hozzáférés-vezérlés az Azure Portalon](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Címkék

A **címkék** szakasz segítséget nyújt az erőforrások rendszerezéséhez. További információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Problémák diagnosztizálása és megoldása

Kattintson a **diagnosztizálása és a problémák megoldásához** megoldásának őket adni az általános problémákat és stratégiák.



## <a name="settings"></a>Beállítások
A **beállítások** szakasz lehetővé teszi, hogy elérheti, és adja meg a következő beállításokat a gyorsítótárhoz.

* [Hozzáférési kulcsok](#access-keys)
* [Speciális beállítások](#advanced-settings)
* [Redis Cache Advisor](#redis-cache-advisor)
* [Méretezés](#scale)
* [Redis-fürt mérete](#cluster-size)
* [Redis-adatmegőrzés](#redis-data-persistence)
* [Frissítések ütemezése](#schedule-updates)
* [Georeplikáció](#geo-replication)
* [Virtual Network](#virtual-network)
* [Tűzfal](#firewall)
* [Tulajdonságok](#properties)
* [Zárolások](#locks)
* [Automation-szkript](#automation-script)



### <a name="access-keys"></a>Elérési kulcs
Kattintson a **hozzáférési kulcsok** megtekintéséhez vagy újbóli létrehozásához a gyorsítótár hozzáférési kulcsait. Ezek a kulcsok segítségével az ügyfelek a cache szolgáltatáshoz való csatlakozáskor.

![Redis gyorsítótár elérési kulcsainak](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Speciális beállítások
A következő beállításokat a **speciális beállítások** panelen.

* [Hozzáférési portok](#access-ports)
* [Memória-szabályzatok](#memory-policies)
* [Kulcstérértesítések (Speciális beállítások)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Hozzáférési portok
A nem SSL hozzáférés alapértelmezés szerint le van tiltva az új gyorsítótárakhoz. A nem SSL port engedélyezéséhez kattintson **nem** a **engedélyezi a hozzáférést csak SSL használatával** a a **speciális beállítások** panelen, majd kattintson **mentése**.

![A redis Cache hozzáférési portok](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Memória-szabályzatok
A **Maxmemory házirend**, **maxmemory fenntartott**, és **maxfragmentationmemory fenntartott** beállításait a **speciális beállítások** panelen konfigurálja a memória a gyorsítótárhoz.

![Redis Cache a Maxmemory-házirend](./media/cache-configure/redis-cache-maxmemory-policy.png)

**A Maxmemory házirend** konfigurálja a gyorsítótár kiürítési szabályzatát, és lehetővé teszi, hogy a következő kiürítési szabályzat közül választhat:

* `volatile-lru` – Ez az alapértelmezett kiürítési szabályzatát.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

További információ `maxmemory` házirendek, lásd: [kiürítési szabályzat](http://redis.io/topics/lru-cache#eviction-policies).

A **maxmemory fenntartott** beállítással memória mennyisége (MB), nem gyorsítótárazási műveletek, például a replikáció, feladatátvétel során tart fenn. Ha az érték lehetővé teszi, hogy a Redis kiszolgáló egységesebb, ha változik a terhelés. Ez az érték nagyobb számítási feladatokhoz, hogy van írási (nagy erőforrásigényű) kell állítani. Memória ilyen műveletek számára van fenntartva, ha nem érhető el a gyorsítótárazott adatok tárolására.

A **maxfragmentationmemory fenntartott** -beállítással konfigurálható a memória mennyisége (MB), amely a memória töredezettségét terhelésének megfelelően van fenntartva. Az érték lehetővé teszi, hogy a Redis kiszolgáló egységesebb, amikor a gyorsítótár teljes vagy közel teljes és a töredezettség aránya túl magas. Memória ilyen műveletek számára van fenntartva, ha nem érhető el a gyorsítótárazott adatok tárolására.

Egy új memória foglalás értéket kiválasztásakor érdemes (**maxmemory fenntartott** vagy **maxfragmentationmemory fenntartott**) milyen ezt a módosítást, amely már fut a gyorsítótár hatással lehet a nagy mennyiségű adattal. Ha rendelkezik egy 53 GB-os gyorsítótár 49 GB adatot tartalmazó, majd módosítsa a foglalási 8 GB, például a módosítás csökken, a maximális rendelkezésre álló memória 45 GB le a rendszer. Ha az aktuális `used_memory` vagy a `used_memory_rss` értékek magasabb, mint az új határértéket 45 GB, akkor a rendszer lesz, amíg mindkettő adatainak fürtből `used_memory` és `used_memory_rss` 45 GB alatt. A kizárási növelheti a kiszolgáló terhelését és a memória töredezettség. További információ a gyorsítótár-metrikák például `used_memory` és `used_memory_rss`, lásd: [rendelkezésre álló metrikák és a jelentéskészítés időközök](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> A **maxmemory fenntartott** és **maxfragmentationmemory fenntartott** beállítások csak érhetők el a Standard és prémium szintű gyorsítótárazza.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Kulcstérértesítések (Speciális beállítások)
Kulcstér értesítések be vannak állítva, a redis a **speciális beállítások** panelen. Kulcstérértesítések engedélyezése az ügyfelek értesítéseket kaphat, amikor bizonyos események történnek.

![Redis gyorsítótár speciális beállításai](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Kulcstérértesítések, és a **értesítése kulcstér-események** beállítás csak Standard és prémium gyorsítótárak esetében érhető el.
> 
> 

További információkért lásd: [Kulcstérértesítések Redis](http://redis.io/topics/notifications). A mintakódot, tekintse meg a [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fájlt a [Helló, világ](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor
A **a Redis Cache Advisor** panel jeleníti meg a javaslatok a gyorsítótár számára. A normál működés során nincsenek javaslatok jelennek meg. 

![Javaslatok](./media/cache-configure/redis-cache-no-recommendations.png)

Azokat a feltételeket például magas memóriahasználat, a hálózati sávszélesség vagy a kiszolgáló terhelését a gyorsítótár a műveletek során fordul elő, ha egy riasztás jelenik meg a **Redis Cache** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations-alert.png)

További információk találhatók a **javaslatok** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations.png)

Ezeket a metrikákat a figyelheti a [figyelési diagramokat](cache-how-to-monitor.md#monitoring-charts) és [használati diagramok](cache-how-to-monitor.md#usage-charts) szakaszait a **Redis Cache** panelen.

Minden egyes tarifacsomagja az ügyfélkapcsolatokat, a memória és a sávszélesség eltérő korlátokkal rendelkeznek. A gyorsítótár maximális kapacitás, a következő metrikákhoz megközelíti a huzamosabb ideig keresztül, ha egy javaslatra jön létre. További információ a metrikák és a véleményező korlátok a **javaslatok** eszközt, az alábbi táblázatban foglaltuk össze:

| Redis Cache metrika | További információ |
| --- | --- |
| Hálózati sávszélesség-használat |[Gyorsítótár teljesítmény - rendelkezésre álló sávszélesség](cache-faq.md#cache-performance) |
| Csatlakozott ügyfelek |[A Redis-kiszolgáló konfiguráció - maxclients alapértelmezett](#maxclients) |
| Kiszolgáló terhelése |[Használati diagramok - Redis-kiszolgáló terhelése](cache-how-to-monitor.md#usage-charts) |
| Memóriahasználat |[Gyorsítótár teljesítmény - mérete](cache-faq.md#cache-performance) |

Frissítse a gyorsítótárat, kattintson a **Frissítsen most** a tarifacsomag módosítása és [méretezési](#scale) a gyorsítótárat. A tarifacsomag kiválasztásáról további információkért lásd: [melyik Redis Cache-ajánlatot és -méretet használjam?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Méretezés
Kattintson a **méretezési** megtekintéséhez, vagy módosítsa a tarifacsomagot a gyorsítótárhoz. Méretezéssel kapcsolatos további információkért lásd: [How to Azure Redis Cache méretezése](cache-how-to-scale.md).

![A redis Cache-tarifacsomag](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-fürt mérete
Kattintson a **(előzetes verzió) Redis-fürt mérete** módosítása egy futó szektorcsoportmérete prémium szintű gyorsítótár-fürtözés engedélyezve van.

> [!NOTE]
> Vegye figyelembe, hogy közben az Azure Redis Cache prémium szintjének most már általánosan elérhető általános rendelkezésre állás, a Redis-fürt mérete a szolgáltatás jelenleg előzetes verzióban érhető el.
> 
> 

![Redis-fürt mérete](./media/cache-configure/redis-cache-redis-cluster-size.png)

A fürt méretének módosításához a csúszka, vagy adjon meg egy számot 1 és 10 között a **szegmensszám** szövegmezőbe, majd kattintson **OK** mentéséhez.

> [!IMPORTANT]
> A redis-fürtözés lehetőség csak a prémium gyorsítótárak. További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md) (Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
> 
> 


### <a name="redis-data-persistence"></a>Redis-adatmegőrzés
Kattintson a **Redis-adatmegőrzés** engedélyezéséhez letiltja, vagy konfigurálja a prémium szintű gyorsítótár adatainak megőrzését. Az Azure Redis Cache Redis megőrzési funkciója segítségével biztosít [RDB-fájlba való megőrzését](cache-how-to-premium-persistence.md#configure-rdb-persistence) vagy [AOF adatmegőrzés](cache-how-to-premium-persistence.md#configure-aof-persistence).

További információkért lásd: [prémium szintű Azure Redis Cache-gyorsítótárhoz adatmegőrzés konfigurálása](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Redis-adatmegőrzés csak a prémium gyorsítótárak érhető el. 
> 
> 

### <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezése** panel lehetővé teszi, hogy a Redis kiszolgáló frissítései a gyorsítótár egy karbantartási időszakot. 

> [!IMPORTANT]
> A karbantartási időszak csak azokra a Redis-kiszolgáló frissítéseit, és nem segítségével bármely Azure-frissítések, vagy frissíti a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének.
> 
> 

![Frissítések ütemezése](./media/cache-configure/redis-schedule-updates.png)

Adja meg a karbantartási időszak, ellenőrizze a kívánt napok és adja meg a karbantartási időszak kezdő órája minden nap, és kattintson **OK**. Vegye figyelembe, hogy a karbantartási ablak időpontja (UTC). 

> [!IMPORTANT]
> A **frissítések ütemezése** funkció csak prémium szinten gyorsítótárak rendelkezésre áll. További információk és utasítások: [Azure Redis Cache felügyeleti - frissítések ütemezése](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georeplikáció

A **georeplikációs** panelen két prémium szintű Azure Redis Cache-példányokban kapcsolásának mechanizmust biztosít. Egy gyorsítótár az elsődleges társított gyorsítótár, a másik pedig a csatolt másodlagos gyorsítótáraként van kijelölve. A másodlagos összekapcsolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatok replikálódnak a másodlagos összekapcsolt gyorsítótár. Ez a funkció segítségével gyorsítótár replikálása az Azure-régiók között.

> [!IMPORTANT]
> **Georeplikáció** csak prémium szinten gyorsítótárak esetében érhető el. További információk és utasítások: [georeplikáció konfigurálása az Azure Redis Cache](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
A **virtuális hálózat** szakasz lehetővé teszi, hogy konfigurálja a virtuális hálózati beállításait a gyorsítótárhoz. Támogatja a virtuális hálózattal egy prémium szintű gyorsítótár létrehozásával kapcsolatos információkat és a beállítások frissítése, lásd: [konfigurálása a virtuális hálózat támogatja a prémium szintű Azure Redis Cache-gyorsítótárhoz](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> A virtuális hálózati beállítások csak érhetők el a prémium gyorsítótárak konfigurált a VNET-támogatás gyorsítótár létrehozása során. 
> 
> 

### <a name="firewall"></a>Tűzfal

Tűzfal-szabályok konfiguráció minden Azure Redis Cache-szint esetében érhető el.

Kattintson a **tűzfal** megtekintéséhez és a gyorsítótár tűzfalszabályok konfigurálása.

![Tűzfal](./media/cache-configure/redis-firewall-rules.png)

A tűzfalszabályok segítségével adható meg egy kezdő és záró IP-címtartomány. Ha tűzfalszabályok vannak beállítva, csak a megadott IP-címtartományok érkező ügyfélkapcsolatokat is csatlakozás a gyorsítótárhoz. Ha olyan tűzfalszabályt, van egy kis ideig várakozni ahhoz a szabály érvényben. Ez a késleltetés van általában kevesebb egy percnél.

> [!IMPORTANT]
> Az Azure Redis Cache rendszerek figyelése kapcsolatok mindig engedélyezettek, még akkor is, ha tűzfalszabályok vannak konfigurálva.
> 
> 

### <a name="properties"></a>Tulajdonságok
Kattintson a **tulajdonságok** a gyorsítótár, beleértve a gyorsítótár végpontját és a portok kapcsolatos információk megtekintéséhez.

![Redis Cache-gyorsítótár tulajdonságai](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zárolások
A **zárolja** szakasz lehetővé teszi, hogy egy előfizetés, erőforráscsoport vagy erőforrás véletlen törlését vagy módosítását kiemelt fontosságú erőforrásait a munkahely más felhasználóinak megelőzése érdekében zárolja. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automation-szkript

Kattintson a **Automation-szkript** hozhat létre, és a telepített erőforrások a későbbiekben a sablon exportálása. Sablonok használatának ismertetését lásd: [erőforrások Azure Resource Manager-sablonok üzembe helyezése](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Felügyeleti beállítások
A beállítások a **felügyeleti** szakasz lehetővé teszi, hogy a gyorsítótár a következő felügyeleti feladatokat hajthat végre. 

![Adminisztráció](./media/cache-configure/redis-cache-administration.png)

* [Adatok importálása](#importexport)
* [Adatok exportálása](#importexport)
* [Újraindítás](#reboot)


### <a name="importexport"></a>Import/Export
Importálás/exportálás szolgáltatás egy Azure Redis Cache-adatok felügyeleti művelet, amely lehetővé teszi, hogy az adatok importálása és exportálása a gyorsítótárban képes importálni, és a egy prémium szintű gyorsítótár egy Redis Cache-adatbázis (RDB) pillanatkép exportálása egy lapblob egy Azure Storage-fiókot. Importálási/exportálási lehetővé teszi különböző Azure Redis Cache-példány közötti áttelepítéséhez, vagy töltse fel a gyorsítótárat az adatokat használat előtt.

Importálás a Redis-kompatibilis RDB-fájlok viszi, minden bármilyen felhőben vagy a környezetben, beleértve a Linux, Windows vagy bármely más szolgáltatónál, például az Amazon Web Services és a többi futó Redis futó Redis-kiszolgálóról is használható. A rendszer egyszerűen hozzon létre egy gyorsítótárat az adatokkal előre feltöltött adatok importálása. Az importálási folyamat során az Azure Redis Cache RDB-fájlba való fájlokat az Azure storage-ból betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi, hogy exportálja az adatokat az Azure Redis Cache a redis Cache RDB-fájlba való kompatibilis fájlokat tárolja. Ez a funkció segítségével az adatok áthelyezése egy Azure Redis Cache-példányról egy másikra, vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gépen, amelyen az Azure Redis Cache-server-példányt, és a fájlt a kijelölt tárfiók töltenek fel. Az exportálási művelet befejeződik, vagy sikeres állapotú vagy sikertelen, amikor az ideiglenes fájl törlődik.

> [!IMPORTANT]
> Importálási/exportálási csak prémium szinten gyorsítótárak érhető el. További információk és utasítások: [Azure Redis Cache az adatok importálása és exportálása](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Újraindítás
A **újraindítás** panel lehetővé teszi, hogy indítsa újra a csomópontokat, a gyorsítótár. Az újraindítás funkció lehetővé teszi, hogy tesztelje az alkalmazást a rugalmassághoz, ha sikertelen. a gyorsítótár-csomópont.

![Újraindítás](./media/cache-configure/redis-cache-reboot.png)

Ha egy prémium szintű gyorsítótár-fürtözés engedélyezve van, kiválaszthatja, melyik szegmensben indítani a gyorsítótár.

![Újraindítás](./media/cache-configure/redis-cache-reboot-cluster.png)

Indítsa újra a gyorsítótár egy vagy több csomópontot, válassza ki a kívánt csomópontokat, és kattintson a **újraindítás**. Ha egy prémium szintű gyorsítótár-fürtözés engedélyezve van, válassza ki az újraindítás, majd kattintson a szegmensről **újraindítás**. Néhány perc múlva a kijelölt csomópont újraindítása, és a rendszer ismét elérhető néhány perc múlva.

> [!IMPORTANT]
> Újraindítás most már az összes tarifacsomag érhető el. További információk és utasítások: [Azure Redis Cache felügyeleti - újraindítás](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Figyelés

A **figyelés** szakasz lehetővé teszi, diagnosztika és a Redis Cache-gyorsítótárhoz figyelés konfigurálása. Az Azure Redis Cache monitorozási és diagnosztikai további információkért lásd: [figyelése az Azure Redis Cache](cache-how-to-monitor.md).

![Diagnosztika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-metrikák](#redis-metrics)
* [Riasztási szabályok](#alert-rules)
* [Diagnosztika](#diagnostics)

### <a name="redis-metrics"></a>Redis-metrikák
Kattintson a **Redis-metrikák** való [megtekintheti a metrikákat](cache-how-to-monitor.md#view-cache-metrics) a gyorsítótárhoz.

### <a name="alert-rules"></a>Riasztási szabályok

Kattintson a **riasztási szabályok** konfigurálása a Redis Cache-metrikák alapján. További információkért lásd: [riasztások](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnosztika

Alapértelmezés szerint a gyorsítótár-metrikák az Azure monitorban vannak [30 napig](../monitoring/monitoring-data-collection.md#metrics) majd törli. A gyorsítótár-metrikák 30 napnál hosszabb ideig megőrizni, kattintson a **diagnosztikai** való [konfigurálása a tárfiók](cache-how-to-monitor.md#export-cache-metrics) fogja tárolni a gyorsítótár-diagnosztikát.

>[!NOTE]
>A gyorsítótár-metrikák tárolóba archiválás, mellett is [azok streamelésére az Eseményközpontok felé, vagy küldje el azokat a Log Analytics](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Támogatás és hibaelhárítás beállításai
A beállítások a **támogatás + hibaelhárítás** szakaszban adja meg, hogy van lehetőség a gyorsítótár szolgáltatással kapcsolatos problémák megoldásában.

![Támogatás + hibaelhárítás](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [A Resource health](#resource-health)
* [Új támogatási kérelem](#new-support-request)

### <a name="resource-health"></a>Erőforrás állapota
**Erőforrás állapota** az erőforrás figyeli, és jelzi, hogy ha a várt módon fut. Az Azure Resource health szolgáltatással kapcsolatos további információkért lásd: [Azure Resource health áttekintése](../resource-health/resource-health-overview.md).

> [!NOTE]
> Erőforrás állapota jelenleg nem a virtuális hálózatban lévő üzemeltetett Azure Redis Cache-példányok állapotát jelenti. További információkért lásd: [minden gyorsítótár-szolgáltatás a virtuális hálózat gyorsítótár üzemeltetése esetén működnek?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Új támogatási kérelem
Kattintson a **új támogatási kérelem** , nyisson egy támogatási kérelmet a gyorsítótár számára.





## <a name="default-redis-server-configuration"></a>Az alapértelmezett érték a Redis kiszolgáló konfigurációja
Új Azure Redis Cache-példányokban vannak konfigurálva, a következő alapértelmezett Redis konfigurációs értékeket:

> [!NOTE]
> Ebben a szakaszban a beállításokat nem lehet módosítani, használja a `StackExchange.Redis.IServer.ConfigSet` metódust. Ha a metódus meghívása az ebben a szakaszban a parancsok egyikét, az az alábbi példához hasonló kivétel történt:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Azokat az értékeket, amelyek konfigurálhatók, például **maximális memória-szabályzat**, az Azure Portalon vagy a parancssori felügyeleti eszközök, például az Azure CLI vagy a PowerShell használatával konfigurálhatók.
> 
> 

| Beállítás | Alapértelmezett érték | Leírás |
| --- | --- | --- |
| `databases` |16 |Az adatbázisok alapértelmezett száma 16, de beállíthatja, hogy egy másik számot, a tarifacsomag alapján. <sup>1</sup> az alapértelmezett adatbázis DB 0, kiválaszthatja, hogy egy másik kapcsolati alapját használatával `connection.GetDatabase(dbid)` ahol `dbid` közötti szám `0` és `databases - 1`. |
| `maxclients` |A tarifacsomag függ<sup>2</sup> |Ez az érték egyszerre engedélyezett csatlakoztatott ügyfelek maximális számát. A korlát elérésekor a Redis bezárul az új kapcsolatok az összes olyan ügyfelek maximális számát elérte hiba újra. |
| `maxmemory-policy` |`volatile-lru` |A Maxmemory házirend beállítás a hogyan Redis választja ki, mit és mikor `maxmemory` (a mérete a gyorsítótárban a gyorsítótár létrehozásakor kiválasztott ajánlat) elérésekor. Az alapértelmezett beállítás: az Azure Redis Cache `volatile-lru`, amely a kulcsok távolítja el, az elévülés LRU algoritmus használatával. Ez a beállítás konfigurálható az Azure Portalon. További információkért lásd: [memória házirendek](#memory-policies). |
| `maxmemory-samples` |3 |A memóriahasználat a LRU és a minimális TTL algoritmusok helyett pontos algoritmusok közelítő algoritmusok is. Alapértelmezés szerint a Redis-ellenőrzések három kulcsot, és kiválasztja azt, amelyik utoljára kevésbé használt. |
| `lua-time-limit` |5000 |Maximális végrehajtási ideje (MS) egy Lua-parancsprogram. Ha eléri a maximális végrehajtási időt, a Redis naplózza, hogy egy parancsfájl továbbra is a végrehajtási, a maximális engedélyezett idő után, és elindítja egy hiba miatt lekérdezések megválaszolására. |
| `lua-event-limit` |500 |Parancsfájl esemény üzenetsor maximális méretét. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 032mb, 8 MB méretű 60 0 |Az ügyfél kimeneti puffer korlátok használható leválasztásának az ügyfelek, amelyek a rendszer nem történő adatkiolvasás elég gyors a kiszolgáló valamilyen okból (gyakori oka az, hogy egy Pub/Sub-ügyfél nem tudja fogadni üzenetek a lehető leghamarabb a közzétevő hozhatnak, azokat) kényszerítése. Tovább információ: [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>maximális `databases` minden egyes Azure Redis Cache tarifacsomag esetében eltérő, és a gyorsítótár létrehozásakor állítható. Ha nincs `databases` beállítás meg van adva gyorsítótár létrehozása során, az alapértelmezett érték 16.

* Alapszintű és Standard gyorsítótárak
  * C0 (250 MB) gyorsítótár - legfeljebb 16 adatbázisok
  * C1 (1 GB) gyorsítótár - legfeljebb 16 adatbázisok
  * C2 (2,5 GB-os) gyorsítótár - legfeljebb 16 adatbázisok
  * C3 csomag (6 GB) gyorsítótár - legfeljebb 16 adatbázisok
  * C4 (13 GB) gyorsítótár - legfeljebb 32 adatbázisok
  * C5 csomag (26 GB) gyorsítótár - legfeljebb 48 adatbázisok
  * C6 (53 GB feletti) gyorsítótár - legfeljebb 64 adatbázisok
* Prémium gyorsítótárak
  * P1 (6 GB – 60 GB) – legfeljebb 16 adatbázisok
  * P2 (13 GB - 130 GB) – legfeljebb 32 adatbázisok
  * P3 (26 GB – 260 GB) – legfeljebb 48 adatbázisok
  * P4 (53 GB - 530 GB) – legfeljebb 64 adatbázisok
  * Redis-fürttel kompatibilis – minden prémium gyorsítótárak a Redis-fürttel csak támogatja 0-adatbázis használatát tehát a `databases` korlátozása minden prémium szintű gyorsítótár engedélyezve van a Redis-fürttel hatékonyan értéke 1 és a [kiválasztása](http://redis.io/commands/select) parancs nem engedélyezett. További információkért lásd: [van a fürtszolgáltatás használandó ügyfélalkalmazásomnak semmilyen módosítást?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Adatbázisokkal kapcsolatos további információkért lásd: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` beállítás lehet, csak a cache létrehozásakor konfigurált, és csak a PowerShell, a parancssori felület vagy a más felügyeleti ügyfeleket. Példa konfigurálása `databases` gyorsítótár létrehozása során PowerShell-lel, tekintse meg a [New-azurermrediscache parancsmag esetében](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` eltér az egyes Azure Redis Cache tarifacsomag.

* Alapszintű és Standard gyorsítótárak
  * C0 (250 MB) gyorsítótár - legfeljebb 256 kapcsolatok
  * C1 (1 GB-os) gyorsítótár - legfeljebb 1000 kapcsolatot
  * C2 (2,5 GB-os) gyorsítótár - legfeljebb 2000 kapcsolatok
  * C3 csomag (6 GB) gyorsítótár - legfeljebb 5 000 kapcsolódás
  * C4 (13 GB) gyorsítótár – legfeljebb 10 000 kapcsolódás
  * C5 csomag (26 GB) gyorsítótár - legfeljebb 15 000 kapcsolatok
  * C6 (53 GB feletti) gyorsítótár - legfeljebb 20 000 kapcsolatok
* Prémium gyorsítótárak
  * P1 (6 GB – 60 GB) – akár 7500 kapcsolatok
  * P2 (13 GB - 130 GB) – legfeljebb 15 000 kapcsolatok
  * P3 (26 GB – 260 GB) – legfeljebb 30 000 kapcsolatok
  * P4 (53 GB - 530 GB) – legfeljebb 40 000 kapcsolatok

> [!NOTE]
> Miközben lehetővé teszi, hogy minden egyes gyorsítótár mérete *akár* bizonyos számú kapcsolatok, minden kapcsolat Redis többletterhelést társított azt. Egy példa az ilyen terhelés miatt a TLS/SSL-titkosítást Processzor- és használati lenne. A maximális kapcsolathoz megadott korlátot, egy adott gyorsítótár méretének feltételezi, hogy egy kevésbé terhelt gyorsítótár. Ha a kapcsolat terhelés betöltése *plusz* Ügyfélműveletek terhelés meghaladja a kapacitását, a rendszer, a gyorsítótár kapacitással kapcsolatos problémákat tapasztalhatnak, még akkor is, ha nem túllépte a kapcsolathoz megadott korlátot, a jelenlegi gyorsítótár mérete a.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Redis parancsok az Azure Redis Cache-ben nem támogatott
> [!IMPORTANT]
> Mivel a Microsoft által felügyelt konfigurálása és felügyelete az Azure Redis Cache-példány, az alábbi parancsok le vannak tiltva. Ha megpróbálja meghívni őket, egy ehhez hasonló hibaüzenetet kap `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * ÁTTELEPÍTÉSE
> * MENTÉS
> * LEÁLLÍTÁS
> * SLAVEOF
> * FÜRT - fürt írási parancsokat le vannak tiltva, de csak olvasható fürt parancsok használata engedélyezett.
> 
> 

A Redis-parancsokkal kapcsolatos további információkért lásd: [ http://redis.io/commands ](http://redis.io/commands).

## <a name="redis-console"></a>Redis konzol
Az Azure Redis Cache-példányok használatával biztonságosan kiadhat parancsokat a **Redis konzol**, gyorsítótár minden szint esetében az Azure Portalon elérhető.

> [!IMPORTANT]
> - A Redis-konzol nem működik a [VNET](cache-how-to-premium-vnet.md). Ha a gyorsítótár része virtuális Hálózatnak, csak a virtuális hálózaton lévő ügyfelek hozzáférhet a gyorsítótárban. Redis konzol fut a helyi böngészőben, amely a virtuális hálózaton kívülről, mert azt nem lehet csatlakozni a gyorsítótár.
> - Nem minden Redis parancsok az Azure Redis Cache-ben támogatottak. Le vannak tiltva, az Azure Redis Cache Redis-parancsok listáját lásd az előző [parancsok nem támogatott az Azure Redis Cache Redis](#redis-commands-not-supported-in-azure-redis-cache) szakaszban. A Redis-parancsokkal kapcsolatos további információkért lásd: [ http://redis.io/commands ](http://redis.io/commands).
> 
> 

A Redis-konzol eléréséhez kattintson **konzol** származó a **Redis Cache** panelen.

![Redis konzol](./media/cache-configure/redis-console-menu.png)

Adja ki a gyorsítótárpéldány irányuló parancsot, egyszerűen írja be a kívánt parancsot a konzolba.

![Redis konzol](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>A Redis-konzol használata egy fürtözött prémium szintű gyorsítótár

A Redis konzol segítségével egy prémium szintű fürtözött gyorsítótár, amikor egy szegmenst a gyorsítótár számára küldhet parancsokat. Adja ki a parancsot egy bizonyos szegmensre, először csatlakoznia kell a kívánt szegmens a szilánkleképezés-választó kattintva.

![Redis konzol](./media/cache-configure/redis-console-premium-cluster.png)

Ha megpróbálja a csatlakoztatott szegmens, mint egy másik szegmensben tárolt kulcs eléréséhez, az alábbihoz hasonló hibaüzenetet kapja:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Az előző példában 1 szegmens a kiválasztott szegmens, de `myKey` aszinkronitást 0, a szegmensben található a `(shard 0)` része a hibaüzenet. Ebben a példában eléréséhez `myKey`szegmenstérkép használatával a szilánkleképezés-választó 0, között, majd adja ki a kívánt parancsot.


## <a name="move-your-cache-to-a-new-subscription"></a>Helyezze át a gyorsítótár egy új előfizetést
Továbbléphet a gyorsítótár egy új előfizetést kattintva **áthelyezése**.

![Helyezze át a Redis Cache-re](./media/cache-configure/redis-cache-move.png)

Erőforrások áthelyezése az egyik erőforráscsoportból egy másikba, és a egy előfizetésből egy másikba kapcsolatos információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>További lépések
* Redis parancsok való használatáról további információkért lásd: [hogyan futtathatok Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)

