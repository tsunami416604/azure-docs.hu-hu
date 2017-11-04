---
title: "Azure Redis Cache konfigurálása |} Microsoft Docs"
description: "Azure Redis Cache Redis alapértelmezett konfigurációjának megértéséhez, valamint megtudhatja, hogyan konfigurálhatja az Azure Redis Cache példányt"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 08/22/2017
ms.author: sdanie
ms.openlocfilehash: 0274e58eb2e83202d4dbc58da0c67d0fdde22ede
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-azure-redis-cache"></a>Azure Redis Cache konfigurálása
Ez a témakör ismerteti, hogyan lehet felülvizsgálata és aktualizálása céljából az Azure Redis Cache példány konfigurációját, és hozzá van rendelve az alapértelmezett Redis kiszolgálókonfiguráció az Azure Redis Cache példány.

> [!NOTE]
> Konfigurálásához és használatához prémium gyorsítótár-funkciók további információkért lásd: [adatmegőrzési konfigurálása](cache-how-to-premium-persistence.md), [hogyan konfigurálhatja a fürtözést](cache-how-to-premium-clustering.md), és [virtuális hálózat támogatásának konfigurálása ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Redis gyorsítótár beállításainak konfigurálása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Az Azure Redis Cache-gyorsítótár beállításai vannak tekinthetők meg és konfigurálni a **Redis Cache** panel használatával a **erőforrás menü**.

![Redis gyorsítótár beállításai](./media/cache-configure/redis-cache-settings.png)

Megtekintheti és a következő beállításokat használja a **erőforrás menü**.

* [Áttekintés](#overview)
* [Tevékenységnapló](#activity-log)
* [Hozzáférés-vezérlés (IAM)](#access-control-iam)
* [Címkék](#tags)
* [Problémák diagnosztizálása és megoldása](#diagnose-and-solve-problems)
* [Beállítások](#settings)
    * [Tárelérési kulcsok](#access-keys)
    * [Speciális beállítások](#advanced-settings)
    * [Redis gyorsítótár Advisor](#redis-cache-advisor)
    * [Méretezés](#scale)
    * [Redis foglalásiegység-méret](#cluster-size)
    * [Redis-adatmegőrzés](#redis-data-persistence)
    * [Frissítések ütemezése](#schedule-updates)
    * [Georeplikáció](#geo-replication)
    * [Virtuális hálózat](#virtual-network)
    * [Tűzfal](#firewall)
    * [Tulajdonságok](#properties)
    * [Zárolások feloldása](#locks)
    * [Automatizálási parancsfájl](#automation-script)
* [Felügyeleti](#administration)
    * [Adatok importálása](#importexport)
    * [Adatok exportálása](#importexport)
    * [Újraindítás](#reboot)
* [Figyelés](#monitoring)
    * [Redis metrikák](#redis-metrics)
    * [A riasztási szabályok](#alert-rules)
    * [Diagnosztika](#diagnostics)
* [Támogatási és hibaelhárítási beállítások](#support-amp-troubleshooting-settings)
    * [Erőforrás állapota](#resource-health)
    * [Új támogatási kérelem](#new-support-request)


## <a name="overview"></a>Áttekintés

**Áttekintés** biztosít alapvető információkat, például nevét, a gyorsítótár, a portok, a tarifacsomag, és kijelölt gyorsítótár metrikákat.

### <a name="activity-log"></a>Tevékenységnapló

Kattintson a **tevékenységnapló** művelet elvégezhető a gyorsítótár megtekintéséhez. Is segítségével szűrés bontsa ki az ebben a nézetben más erőforrások. A vizsgálati naplók munkáról bővebben lásd: [naplózási műveletek a Resource Manager](../azure-resource-manager/resource-group-audit.md). Azure Redis Cache események figyelésével kapcsolatos további információkért lásd: [műveletek és a riasztások](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlés (IAM)** szakasz támogatja a szerepköralapú hozzáférés-vezérlést (RBAC) a szervezetek számára követelményeknek a access management egyszerűen és pontosan Azure-portálon. További információkért lásd: [az Azure portál szerepköralapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md).

### <a name="tags"></a>Címkék

A **címkék** szakasz segítséget nyújt az erőforrások rendszerezése. További információkért lásd: [az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Problémák diagnosztizálása és megoldása

Kattintson a **Diagnosztizálás és problémák megoldására** megoldása érdekében ezeket a gyakori problémák és stratégiák meg kell adni.



## <a name="settings"></a>Beállítások
A **beállítások** szakasz lehetővé teszi a eléréséhez, és a következő beállításokat a gyorsítótárhoz.

* [Tárelérési kulcsok](#access-keys)
* [Speciális beállítások](#advanced-settings)
* [Redis gyorsítótár Advisor](#redis-cache-advisor)
* [Méretezés](#scale)
* [Redis foglalásiegység-méret](#cluster-size)
* [Redis-adatmegőrzés](#redis-data-persistence)
* [Frissítések ütemezése](#schedule-updates)
* [Georeplikáció](#geo-replication)
* [Virtuális hálózat](#virtual-network)
* [Tűzfal](#firewall)
* [Tulajdonságok](#properties)
* [Zárolások feloldása](#locks)
* [Automatizálási parancsfájl](#automation-script)



### <a name="access-keys"></a>Elérési kulcs
Kattintson a **hívóbetűk** megtekintéséhez vagy a gyorsítótár elérési kulcsainak újragenerálása. Ezeket a kulcsokat a gyorsítótárhoz kapcsolódó ügyfelek által használt.

![Redis gyorsítótár elérési kulcsainak](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Speciális beállítások
A következő beállításokat a **speciális beállítások** panelen.

* [Hozzáférési portok](#access-ports)
* [Memória házirendek](#memory-policies)
* [Kulcstérértesítések használatával értesítések (Speciális beállítások)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Hozzáférési portok
A nem SSL hozzáférés alapértelmezés szerint le van tiltva az új gyorsítótárakhoz. A nem SSL port engedélyezéséhez kattintson **nem** a **engedélyezi a hozzáférést csak SSL keresztül** a a **speciális beállítások** panel megnyitásához, és kattintson **mentése**.

![A redis Cache hozzáférési portok](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Memória házirendek
A **Maxmemory házirend**, **maxmemory fenntartott**, és **maxfragmentationmemory fenntartott** beállításait a **speciális beállítások** panelen konfigurálhatja a memória-házirendeket, a gyorsítótár.

![Redis gyorsítótár Maxmemory házirend](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory házirend** a kiürítés irányelvet konfigurál a gyorsítótárban, és lehetővé teszi a következő kiürítési házirendek közül választhat:

* `volatile-lru`-Ez az alapértelmezett beállítás.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

További információ `maxmemory` házirendek, lásd: [kiürítés házirendek](http://redis.io/topics/lru-cache#eviction-policies).

A **maxmemory fenntartott** beállítással memória MB-ban, amely nem gyorsítótár műveletek, például a feladatátvétel során replikációs számára van fenntartva. Ha az érték lehetővé teszi, hogy a Redis server egységesebb, amikor változik a terhelés. Ez az érték nagyobb munkaterhelésekhez, amelyek írni a gyakori kell beállítani. Amikor memória a műveletek számára van fenntartva, nem érhető el a gyorsítótárazott adatok tárolására.

A **maxfragmentationmemory fenntartott** beállítással memória MB-ban, amely a memória töredezettségét befogadásához van fenntartva. Ha az érték lehetővé teszi egy egységesebb Redis-kiszolgálót, ha a gyorsítótár megtelt vagy megközelíti a teljes telepítési és a töredezettséget arány is nagy. Amikor memória a műveletek számára van fenntartva, nem érhető el a gyorsítótárazott adatok tárolására.

Érdemes egy új foglalás memóriaméretnél kiválasztásakor (**maxmemory fenntartott** vagy **maxfragmentationmemory fenntartott**) milyen hatással van a módosítás az egy gyorsítótár, amely már fut. nagy mennyiségű adatot. Például ha 49 GB adatot 53 GB gyorsítótár rendelkezik, majd módosítsa a Foglalás 8 GB, ezzel eldobja a maximális rendelkezésre álló memória, a rendszer le 45 GB. Ha az aktuális `used_memory` vagy a `used_memory_rss` értékek magasabbak, mint az új 45 GB-os korlátját, akkor a rendszer lesz, amíg adatok kizárása `used_memory` és `used_memory_rss` 45 GB alatt van. A kiürítési növelheti a kiszolgáló terhelés és a memória töredezettsége. További információt a gyorsítótár mérőszámokat például `used_memory` és `used_memory_rss`, lásd: [elérhető és a jelentéskészítés intervallumok](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> A **maxmemory fenntartott** és **maxfragmentationmemory fenntartott** beállítások csak érhetők el a Standard és Premium gyorsítótárazza.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Kulcstérértesítések használatával értesítések (Speciális beállítások)
Kulcstérértesítések használatával értesítések konfigurálása a redis a **speciális beállítások** panelen. Kulcstérértesítések használatával értesítések engedélyezése az ügyfelek értesítéseket meghatározott események bekövetkezésekor.

![Redis gyorsítótár speciális beállításai](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Kulcstérértesítések használatával értesítések és a **értesítés kulcstérértesítések használatával-események** beállítás csak akkor érhetők Standard és Premium gyorsítótárak esetében.
> 
> 

További információkért lásd: [Redis kulcstérértesítések használatával értesítések](http://redis.io/topics/notifications). Mintakód, tekintse meg a [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fájlt a [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis gyorsítótár Advisor
A **Redis gyorsítótár Advisor** csempe megjeleníti a gyorsítótár javaslatok. A normál működés során nincsenek ajánlatok jelennek meg. 

![Javaslatok](./media/cache-configure/redis-cache-no-recommendations.png)

Ha például magas memóriahasználat, a hálózati sávszélesség vagy a kiszolgáló terhelését a gyorsítótár a műveletek során a feltételek teljesülnek egy riasztás jelenik meg a **Redis Cache** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations-alert.png)

További információ található a **javaslatok** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations.png)

A metrikák a figyelheti a [diagramok figyelési](cache-how-to-monitor.md#monitoring-charts) és [használati diagramok](cache-how-to-monitor.md#usage-charts) szakasza a **Redis Cache** panelen.

Minden tarifacsomag különböző korlátai ügyfélkapcsolatokat, a memória és a sávszélesség rendelkezik. Ha a gyorsítótár maximális kapacitás metrikákat megközelíti egy huzamosabb ideig keresztül, az ajánlás jön létre. További információ a metrikák és korlátai vizsgálja felül a **javaslatok** eszköz, a következő táblázatban találja:

| Redis gyorsítótár metrika | További információ |
| --- | --- |
| A sávszélesség-használat |[Gyorsítótár teljesítmény - rendelkezésre álló sávszélesség](cache-faq.md#cache-performance) |
| Csatlakozott ügyfelek |[Alapértelmezett Redis-kiszolgáló konfiguráció - maxclients](#maxclients) |
| A kiszolgálóterhelés |[Használati diagramok - Redis-kiszolgáló terhelését](cache-how-to-monitor.md#usage-charts) |
| Memóriahasználat |[Gyorsítótár teljesítmény - mérete](cache-faq.md#cache-performance) |

Frissítse a gyorsítótárat, kattintson a **frissítés most** a tarifacsomag módosítása és [méretezési](#scale) a gyorsítótárhoz. Tarifacsomag kiválasztásáról további információkért lásd: [milyen Redis Cache-ajánlatot és méretet használjam?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use)


### <a name="scale"></a>Méretezés
Kattintson a **méretezési** lehet megtekinteni vagy módosítani a gyorsítótár tarifacsomag. A méretezés további információkért lásd: [Scale Azure Redis Cache hogyan](cache-how-to-scale.md).

![Redis gyorsítótár tarifacsomag](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis foglalásiegység-méret
Kattintson a **(előzetes verzió) Redis fürtméret** futó a fürt méretének módosításához prémium gyorsítótár fürtözési engedélyezve van.

> [!NOTE]
> Vegye figyelembe, hogy közben az Azure Redis Cache prémium szintjének kiadott általános rendelkezésre állási, a fürtméret Redis funkció jelenleg előzetes verzióban érhetők.
> 
> 

![Redis foglalásiegység-méret](./media/cache-configure/redis-cache-redis-cluster-size.png)

A fürt méretének módosításához használja a csúszkát, vagy írjon be egy számot 1 és 10 között a **Shard száma** szöveges értéket, majd kattintson **OK** mentéséhez.

> [!IMPORTANT]
> Redis fürtszolgáltatás csak érhető el prémium gyorsítótárak esetében. További információk: [How to configure clustering for a Premium Azure Redis Cache](cache-how-to-premium-clustering.md) (Fürtözés konfigurálása prémium szintű Azure Redis Cache-gyorsítótárhoz).
> 
> 


### <a name="redis-data-persistence"></a>Redis-adatmegőrzés
Kattintson a **Redis-adatmegőrzés** engedélyezése, letiltása, vagy konfigurálja a prémium szintű gyorsítótár adatainak megőrzését. Azure Redis Cache Redis-adatmegőrzés használatával biztosít [Rekordadatbázis adatmegőrzési](cache-how-to-premium-persistence.md#configure-rdb-persistence) vagy [AOF adatmegőrzési](cache-how-to-premium-persistence.md#configure-aof-persistence).

További információkért lásd: [konfigurálása prémium szintű Azure Redis Cache megőrzését](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Prémium szintű gyorsítótárak redis-adatmegőrzés csak érhető el. 
> 
> 

### <a name="schedule-updates"></a>Frissítések ütemezése
A **frissítések ütemezése** panel lehetővé teszi, hogy a gyorsítótár Redis-kiszolgáló frissítések karbantartási időszakot. 

> [!IMPORTANT]
> A karbantartási időszak csak a kiszolgálói frissítések Redis vonatkozik, és nem az összes Azure-bA frissít, vagy frissíti az operációs rendszer, a gyorsítótár üzemeltető virtuális gépek.
> 
> 

![Frissítések ütemezése](./media/cache-configure/redis-schedule-updates.png)

Adja meg a karbantartási időszak, ellenőrizze a kívánt napok, és adja meg a karbantartási időszak kezdő időpontja minden nap, és kattintson **OK**. Vegye figyelembe, hogy a karbantartási ablak időpontja UTC szerint. 

> [!IMPORTANT]
> A **frissítések ütemezése** funkciót csak érhető el prémium réteghez gyorsítótárainak. További információt és útmutatást lásd: [Azure Redis Cache felügyeleti - frissítések ütemezése](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georeplikáció

A **georeplikáció** panel lehetővé teszi a csatolás két Premium szint Azure Redis Cache példányt. Egy gyorsítótár az elsődleges társított gyorsítótár, míg a másik a másodlagos csatolt gyorsítótár van kijelölve. A másodlagos csatolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatokat a rendszer replikálja a másodlagos csatolt gyorsítótár. Ez a funkció a gyorsítótár Azure-régiók közötti replikáció használható.

> [!IMPORTANT]
> **A georeplikáció** lehetőség csak a prémium szintű réteghez gyorsítótárainak. További információt és útmutatást lásd: [georeplikáció konfigurálása az Azure Redis Cache](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
A **virtuális hálózati** szakasz lehetővé teszi, hogy a virtuális hálózat beállításait a gyorsítótárhoz. A prémium szintű gyorsítótár virtuális hálózaton létrehozásával kapcsolatos információkat támogatja, és a beállítások frissítése, lásd: [konfigurálása a virtuális hálózat támogatja a Premium Azure Redis Cache](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Virtuális hálózati beállítások csak érhetők el a prémium szintű gyorsítótárak konfigurált VNET támogató gyorsítótár létrehozása során. 
> 
> 

### <a name="firewall"></a>Tűzfal

Kattintson a **tűzfal** megtekintéséhez és a Premium Azure Redis Cache tűzfalszabályok konfigurálása.

![Tűzfal](./media/cache-configure/redis-firewall-rules.png)

A tűzfalszabályok kezdő és záró IP-címtartománnyal rendelkező adhatja meg. Tűzfalszabályok konfigurálásakor csak a megadott IP-címtartományok érkező ügyfélkapcsolatokat is elérheti a gyorsítótárban. Egy tűzfalszabály mentésekor a rendszer nincs rövid késleltetés előtt a szabály érvényben. Ez a késés van általában kevesebb mint egy perc.

> [!IMPORTANT]
> Azure Redis Cache rendszerek figyelése a kapcsolatok mindig engedélyezettek, még akkor is, ha a tűzfal-szabályok úgy vannak konfigurálva.
> 
> Tűzfalszabályok esetén csak prémium szintű réteghez gyorsítótárainak érhetők el.
> 
> 

### <a name="properties"></a>Tulajdonságok
Kattintson a **tulajdonságok** a gyorsítótárhoz, beleértve a gyorsítótár végpontjához és portok vonatkozó információk megtekintése.

![Redis gyorsítótár tulajdonságai](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zárolások
A **zárolja** szakasz lehetővé teszi, hogy egy előfizetés, erőforráscsoportból vagy erőforrás véletlen törlése vagy a kritikus erőforrásokat módosítása a munkahely más felhasználóinak megelőzése érdekében zárolja. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Automatizálási parancsfájl

Kattintson a **automatizálási parancsfájl** felépítéséhez és az üzembe helyezett erőforrások a későbbi telepítési sablon exportálása. A sablonok használatának kapcsolatos további információkért lásd: [telepítése Azure Resource Manager-sablonok erőforrások](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Felügyeleti beállítások
A beállítások a **felügyeleti** szakasz lehetővé teszi a gyorsítótár a következő felügyeleti feladatokat hajthat végre. 

![Adminisztráció](./media/cache-configure/redis-cache-administration.png)

* [Adatok importálása](#importexport)
* [Adatok exportálása](#importexport)
* [Újraindítás](#reboot)


### <a name="importexport"></a>Import/Export
Importálási/exportálási egy Azure Redis Cache adatok felügyeleti művelet, amely lehetővé teszi a gyorsítótárban lévő adatok importálása és exportálása Redis gyorsítótár-adatbázis (Rekordadatbázis) pillanatkép prémium gyorsítótárában egy Azure Storage-fiók oldalakra vonatkozó blob exportálására és importálásra. Importálási/exportálási lehetővé teszi különböző Azure Redis Cache-példányok közötti áttelepítése vagy a gyorsítótár adatokkal használat előtt.

Importálás a Redis-kiszolgáló futtatja a felhő vagy a környezet, beleértve a futó Linux, Windows vagy bármely felhőalapú szolgáltató, például az Amazon Web Services, míg mások Redis vinnie a Redis-kompatibilis Rekordadatbázis fájlok használható. Adatok importálása egyszerű módja a gyorsítótár létrehozásához előre megadott adatokkal. Az importálási folyamat során az Azure Redis Cache Rekordadatbázis fájlokat az Azure storage betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi az Azure Redis Cache Redis kompatibilis Rekordadatbázis fájlok a tárolt adatok exportálását. Ez a szolgáltatás segítségével tárolt adatok mozgatása egy Azure Redis Cache példányt, vagy egy másik Redis-kiszolgálóhoz. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális Gépen, amelyen az Azure Redis Cache server-példányt, és a fájl feltöltése a kijelölt tárfiókkal. Az exportálási művelet befejezése után a vagy állapota sikeres végrehajtásával vagy hibajelzéssel az ideiglenes fájl törlődik.

> [!IMPORTANT]
> Prémium szintű réteghez gyorsítótárainak importálási/exportálási csak érhető el. További információt és útmutatást lásd: [importálhat és exportálhat adatokat az Azure Redis Cache](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Újraindítás
A **újraindítás** panel lehetővé teszi, hogy a gyorsítótár a csomópontok újraindítását. A rendszer újraindítása funkció lehetővé teszi a rugalmasságot az alkalmazás tesztelése, ha sikertelen. a gyorsítótár-csomópont.

![Újraindítás](./media/cache-configure/redis-cache-reboot.png)

Ha a prémium szintű gyorsítótár fürtözési engedélyezve van, mely szilánkok újraindítását a gyorsítótár választhatja meg.

![Újraindítás](./media/cache-configure/redis-cache-reboot-cluster.png)

Indítsa újra a gyorsítótár egy vagy több csomópontot, válassza ki a kívánt csomópontokat, majd **újraindítás**. Ha a prémium szintű gyorsítótár fürtözési engedélyezve van, jelölje ki a shard(s) újraindíthatja, és kattintson a **újraindítás**. Néhány perc múlva a kijelölt csomópont újraindítása, és amelyeket biztonsági online néhány percen belül.

> [!IMPORTANT]
> Minden tarifacsomagok Újraindítás most érhető el. További információt és útmutatást lásd: [Azure Redis Cache felügyeleti - újraindítás](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Figyelés

A **figyelés** szakasz lehetővé teszi a diagnosztika és a Redis Cache-figyelés konfigurálása. Azure Redis Cache-figyelés és diagnosztika további információkért lásd: [figyelése Azure Redis Cache](cache-how-to-monitor.md).

![Diagnosztika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis metrikák](#redis-metrics)
* [A riasztási szabályok](#alert-rules)
* [Diagnosztika](#diagnostics)

### <a name="redis-metrics"></a>Redis metrikák
Kattintson a **metrikák Redis** való [metrikák megtekintése](cache-how-to-monitor.md#view-cache-metrics) a gyorsítótárhoz.

### <a name="alert-rules"></a>A riasztási szabályok

Kattintson a **riasztási szabályok** Redis Cache mérőszámok alapján értesítések konfigurálása. További információkért lásd: [riasztások](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnosztika

Gyorsítótár-metrikát a Azure figyelő alapesetben [30 napig tárolja a](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) , majd törölni. Továbbra is fennáll, a 30 napnál hosszabb gyorsítótár metrikáját, kattintson a **diagnosztika** való [a tárfiók konfigurálása](cache-how-to-monitor.md#export-cache-metrics) gyorsítótár diagnosztika tárolására.

>[!NOTE]
>Mellett a gyorsítótár metrikákat Storage archiválás, is [adatfolyamként küldje el azokat az Eseményközpontok felé, vagy küldje el a Naplóelemzési](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

## <a name="support--troubleshooting-settings"></a>Támogatási és hibaelhárítási beállítások
A beállítások a **támogatási + hibaelhárítási** szakasz a gyorsítótár kapcsolatos problémák megoldása lehetőségeket biztosít.

![Támogatási + hibaelhárítása](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Erőforrás állapota](#resource-health)
* [Új támogatási kérelem](#new-support-request)

### <a name="resource-health"></a>Erőforrás állapota
**Erőforrás állapota** az erőforrás figyeli, és jelzi, hogy ha a várt módon fut. Az Azure Resource health szolgáltatással kapcsolatos további információkért lásd: [Azure-erőforrás állapotának áttekintése](../resource-health/resource-health-overview.md).

> [!NOTE]
> Erőforrás állapota jelenleg nem készíthető jelentés a virtuális hálózat található Azure Redis Cache példány állapotának. További információkért lásd: [minden gyorsítótár-funkciók esetén egy virtuális hálózat a gyorsítótárhoz működik?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Új támogatási kérelem
Kattintson a **új támogatja a kérelem** a gyorsítótárhoz támogatási kérést nyithat.





## <a name="default-redis-server-configuration"></a>Alapértelmezett Redis-kiszolgálókonfiguráció
Új Azure Redis Cache példányt az alábbi alapértelmezett értékekkel Redis konfigurációs vannak konfigurálva.

> [!NOTE]
> Ebben a szakaszban a beállítások használatával nem módosítható a `StackExchange.Redis.IServer.ConfigSet` metódust. Ha ez a módszer egy ebben a szakaszban szereplő parancsok nevezik, az alábbihoz hasonló kivétel történik:  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Bármely értékek, amelyek konfigurálhatók, például **maximális memória-házirenddel**, az Azure portálon vagy a parancssori felügyeleti eszközök, például az Azure parancssori felület vagy a PowerShell segítségével konfigurálható.
> 
> 

| Beállítás | Alapértelmezett érték | Leírás |
| --- | --- | --- |
| `databases` |16 |Az adatbázis alapértelmezett érték 16, de be lehet állítani egy másik számot, a tarifacsomag alapján. <sup>1</sup> az alapértelmezett adatbázis DB 0, kiválaszthatja, hogy egy másik kapcsolati alapját használatával `connection.GetDatabase(dbid)` ahol `dbid` közötti szám `0` és `databases - 1`. |
| `maxclients` |Ez a tarifacsomag függ<sup>2</sup> |Ez az egyidejűleg engedélyezett kapcsolódó ügyfelek maximális számát. A korlát elérését követően a Redis bezárul minden új kapcsolatot, és a "ügyfelek maximális száma elérte a" hiba újra. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory házirend az beállítása a hogyan Redis választja ki, mit és mikor `maxmemory` (ajánlat választotta, a gyorsítótár létrehozása után a gyorsítótár méretét) elérésekor. Azure Redis Cache segítségével az alapértelmezett beállítás: `volatile-lru`, amely a kulcsok távolítja el a jelszólejárat LRU algoritmus használatával. Ezt a beállítást kell megadni az Azure portálon. További információkért lásd: [memória házirendek](#memory-policies). |
| `maxmemory-samples` |3 |A memóriahasználat LRU és minimális TTL algoritmusok közelítő algoritmusok pontos algoritmusok helyett. Alapértelmezés szerint Redis ellenőrzések három kulcsok és kivételezések azt, amelyik kevesebb nemrég lett megadva. |
| `lua-time-limit` |5,000 |Maximális végrehajtási idő ezredmásodpercben Lua parancsfájlra. Ha eléri a maximális végrehajtási ideje, Redis naplózza, hogy egy parancsfájl még végrehajtása után a maximális engedélyezett idő, és hiba történt a lekérdezések megválaszolásához kezdődik. |
| `lua-event-limit` |500 |Parancsfájl esemény sor maximális mérete. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 032mb 8mb 60 0 |Az ügyfél kimeneti puffer korlátok segítségével kényszerítheti az ügyfelek, amelyek nem adatainak olvasása elég gyors kiszolgálóról (gyakori oka az, hogy Pub/Sub ügyfél nem lehet felhasználni a lehető leghamarabb a közzétevő születik őket üzenetek) valamilyen okból megszakad. További információkért lásd: [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>vonatkozó korlát `databases` eltér az egyes Azure Redis Cache IP-címek és a gyorsítótár létrehozásakor állítható be. Ha nincs `databases` beállítás során megadott gyorsítótár létrehozását, az alapértelmezett érték 16.

* Basic és Standard gyorsítótárak
  * C0 csomag (250 MB) gyorsítótár - legfeljebb 16 adatbázisok
  * C1 (1 GB-os) gyorsítótár - legfeljebb 16 adatbázisok
  * C2 csomag (2,5 GB) gyorsítótár - legfeljebb 16 adatbázisok
  * C3 csomag (6 GB) gyorsítótár - legfeljebb 16 adatbázisok
  * C4 (13 GB) gyorsítótár - legfeljebb 32 adatbázisok
  * C5 (26 GB) gyorsítótár - legfeljebb 48 adatbázisok
  * C6 (53 GB) gyorsítótár - legfeljebb 64 adatbázisok
* Prémium szintű gyorsítótárak
  * Legfeljebb 16 adatbázisok (6 GB - 60 GB) – P1
  * Legfeljebb 32 adatbázisok (13 GB - 130 GB) – P2
  * P3 (26 GB - 260 GB) – akár 48 adatbázisok
  * P4 (53 GB - 530 GB) – akár 64 adatbázisok
  * Engedélyezve - Redis-fürt minden prémium gyorsítótárak Redis-fürt csak használatát támogatja az adatbázis 0, a `databases` korlátozza a prémium szintű gyorsítótár engedélyezve van a Redis-fürt hatékonyan értéke 1 és a [kiválasztása](http://redis.io/commands/select) parancs nem engedélyezett. További információkért lásd: [van saját ügyfélalkalmazás fürtszolgáltatással használandó végezze el a módosításokat?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Adatbázisokkal kapcsolatos további információkért lásd: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` beállítás csak a gyorsítótár létrehozása során konfigurált, és csak a PowerShell, a parancssori felületen vagy a más felügyeleti ügyfelek lehet. Példa konfigurálása `databases` gyorsítótár létrehozása PowerShell használatával, lásd: [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` eltér az egyes Azure Redis Cache tarifacsomagra vált.

* Basic és Standard gyorsítótárak
  * C0 csomag (250 MB) gyorsítótár - legfeljebb 256 kapcsolatok
  * C1 (1 GB-os) gyorsítótár - legfeljebb 1000 kapcsolatok
  * C2 csomag (2,5 GB) gyorsítótár - legfeljebb 2000 kapcsolatok
  * C3 csomag (6 GB) gyorsítótár - legfeljebb 5000 kapcsolatok
  * C4 (13 GB) gyorsítótár - legfeljebb 10 000 kapcsolatok
  * C5 (26 GB) gyorsítótár - legfeljebb 15 000 kapcsolatok
  * C6 (53 GB) gyorsítótár - legfeljebb 20 000 kapcsolatok
* Prémium szintű gyorsítótárak
  * P1 (6 GB - 60 GB) – akár 7500 kapcsolatok
  * P2 (13 GB - 130 GB) – akár 15 000 kapcsolatok
  * P3 (26 GB - 260 GB) – akár 30 000 kapcsolatok
  * P4 (53 GB - 530 GB) – akár 40 000 kapcsolatok

> [!NOTE]
> Miközben lehetővé teszi, hogy minden egyes gyorsítótár méretének *legfeljebb* bizonyos számú kapcsolatok, minden egyes való kapcsolat rendelkezik terhet társítva. Erre példa ilyen terheléssel lehet Processzor- és memóriahasználatról miatt a TLS/SSL-titkosítást. A megadott gyorsítótárméret maximális kapcsolódási korlátja azt feltételezi, hogy egy némileg betöltött gyorsítótár. Ha tölthető be a kapcsolat terhelés *plus* az Ügyfélműveletek terhelése meghaladja a rendszer a kapacitás, a gyorsítótár is kapacitás problémákba ütközhetnek, még akkor is, ha nem lépik túl a gyorsítótár jelenlegi mérete a kapcsolathoz megadott korlátot.
> 
> 



## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>A parancsok nem támogatott az Azure Redis Cache redis
> [!IMPORTANT]
> Microsoft által felügyelt konfigurálása és kezelése az Azure Redis Cache példányt, mert a következő parancsok le vannak tiltva. Ha megpróbálja meghívni őket, hasonló hibaüzenetet kap `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * HIBAKERESÉSI
> * ÁTTELEPÍTÉSE
> * MENTÉSE
> * LEÁLLÍTÁS
> * SLAVEOF
> * FÜRT - fürt írási parancsokat le vannak tiltva, de csak olvasható fürt parancsot.
> 
> 

Redis parancsokkal kapcsolatos további információkért lásd: [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Redis-konzol
Az Azure Redis Cache példány használatával biztonságosan kiadhatja parancsok a **Redis konzol**, az Azure portálon, minden gyorsítótár rétege számára elérhető.

> [!IMPORTANT]
> - A Redis-konzol nem tud együttműködni [VNET](cache-how-to-premium-vnet.md). Ha a gyorsítótár egy virtuális hálózat része, csak a virtuális hálózaton lévő ügyfelek is gyorsítótár-hozzáféréshez. Konzol Redis fut a helyi böngészőben, amely a virtuális hálózaton kívül, mert nem tud kapcsolódni a gyorsítótárhoz.
> - Nem minden Redis-parancsok az Azure Redis Cache támogatottak. Le vannak tiltva, az Azure Redis Cache Redis-parancsok listáját lásd az előző [parancsok nem támogatott az Azure Redis Cache Redis](#redis-commands-not-supported-in-azure-redis-cache) szakasz. Redis parancsokkal kapcsolatos további információkért lásd: [http://redis.io/commands](http://redis.io/commands).
> 
> 

A Redis-konzol megnyitásához kattintson a **konzol** a a **Redis Cache** panelen.

![Redis-konzol](./media/cache-configure/redis-console-menu.png)

A gyorsítótárpéldány elleni parancsok kiadását, egyszerűen csak írja be a kívánt parancsot a konzolba.

![Redis-konzol](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>A Redis-konzol használata egy fürtözött prémium gyorsítótár

A Redis-konzol használata a prémium fürtözött gyorsítótár, ha a gyorsítótár egyetlen shard a parancsok adhat ki. A parancs kiadni egy adott shard, először kapcsolódnia kell a kívánt shard a shard kiválasztójának kattintva.

![Redis-konzol](./media/cache-configure/redis-console-premium-cluster.png)

Ha megkísérli a csatlakoztatott shard mint egy másik shard tárolt hívóbetű, az alábbihoz hasonló hibaüzenetet kap.

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Az előző példában a shard 1 a kijelölt shard, de `myKey` található shard 0, ahogy azt a `(shard 0)` a hibaüzenet a következő része. Ebben a példában eléréséhez `myKey`, és válassza ki a shard 0 a shard objektumválasztó használata, majd adja ki a kívánt parancsot.


## <a name="move-your-cache-to-a-new-subscription"></a>Helyezze át a gyorsítótár egy új előfizetés
Egy új előfizetést a gyorsítótár a gombra kattintva áthelyezheti **áthelyezése**.

![Helyezze át a Redis gyorsítótár](./media/cache-configure/redis-cache-move.png)

Az erőforrások áthelyezése az egyik erőforráscsoportból a másikba, és a másik egy előfizetés információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Következő lépések
* További információ a Redis-parancsok használatával: [hogyan futtathatom Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)

