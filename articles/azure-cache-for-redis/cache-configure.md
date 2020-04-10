---
title: Az Azure Cache beállítása a Redis-hez
description: Ismerje meg az Azure Cache for Redis alapértelmezett Redis-konfigurációját, és ismerje meg, hogyan konfigurálhatja az Azure-gyorsítótárat a Redis-példányokra
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 3f0de52782694e6cbc8fdb6b55d545191dbbb350
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010307"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Az Azure Cache beállítása a Redis-hez
Ez a témakör ismerteti az Azure-gyorsítótár redis-példányok elérhető konfigurációk. Ez a témakör ismerteti az alapértelmezett Redis-kiszolgáló konfigurációaz Azure Cache redis példányok.

> [!NOTE]
> A prémium szintű gyorsítótár-szolgáltatások konfigurálásáról és használatáról a [Megőrzés konfigurálása](cache-how-to-premium-persistence.md), [A fürtözés konfigurálása](cache-how-to-premium-clustering.md)és [a Virtuális hálózati támogatás konfigurálása](cache-how-to-premium-vnet.md)című témakörben talál további információt.
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Az Azure Cache konfigurálása a Redis-beállításokhoz
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

A Redis-beállítások Azure Cache for The Resource (Azure Cache for Redis) az **Erőforrás menü**használatával tekinthető meg és konfigurálható az Azure Cache **for Redis** panelen.

![Azure-gyorsítótár a Redis-beállításokhoz](./media/cache-configure/redis-cache-settings.png)

A következő beállításokat az **Erőforrás menü**segítségével tekintheti meg és állíthatja be.

* [Áttekintés](#overview)
* [Tevékenységnapló](#activity-log)
* [Hozzáférés-vezérlés (IAM)](#access-control-iam)
* [Címkék](#tags)
* [Problémák diagnosztizálása és megoldása](#diagnose-and-solve-problems)
* [Beállítások](#settings)
    * [Elérési kulcs](#access-keys)
    * [Speciális beállítások](#advanced-settings)
    * [Azure-gyorsítótár a Redis tanácsadószámára](#azure-cache-for-redis-advisor)
    * [Méretezés](#scale)
    * [Fürt mérete](#cluster-size)
    * [Adatmegőrzés](#redis-data-persistence)
    * [Frissítések ütemezése](#schedule-updates)
    * [Georeplikáció](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Tűzfal](#firewall)
    * [Tulajdonságok](#properties)
    * [Zárak](#locks)
    * [Automation-szkript](#automation-script)
* Felügyelet
    * [Adatok importálása](#importexport)
    * [Adatok exportálása](#importexport)
    * [Újraindítás](#reboot)
* [Figyelés](#monitoring)
    * [Redis metrikák](#redis-metrics)
    * [Riasztási szabályok](#alert-rules)
    * [Diagnosztika](#diagnostics)
* Támogatás & hibaelhárítási beállítások
    * [Erőforrás állapota](#resource-health)
    * [Új támogatási kérelem](#new-support-request)


## <a name="overview"></a>Áttekintés

**Az áttekintés** alapvető információkat nyújt a gyorsítótárról, például a névről, a portokról, a tarifacsomagról és a kiválasztott gyorsítótár-mérőszámokkal kapcsolatban.

### <a name="activity-log"></a>Tevékenységnapló

Kattintson **a Tevékenységnapló** elemre a gyorsítótárban végrehajtott műveletek megtekintéséhez. A szűrés segítségével kibonthatja ezt a nézetet más erőforrásokkal is. A naplónaplók kal való együttműködésről további információt az [Erőforrás-kezelő naplózási műveletei című](../azure-resource-manager/management/view-activity-logs.md)témakörben talál. Az Azure Cache Redis-események figyelésével kapcsolatos további információkért lásd: [Műveletek és riasztások.](cache-how-to-monitor.md#operations-and-alerts)

### <a name="access-control-iam"></a>Hozzáférés-vezérlés (IAM)

A **hozzáférés-vezérlési (IAM)** szakasz támogatja a szerepköralapú hozzáférés-vezérlés (RBAC) az Azure Portalon. Ez a konfiguráció segít a szervezeteknek egyszerűen és pontosan megfelelni a hozzáférés-kezelési követelményeknek. További információ: [Szerepköralapú hozzáférés-vezérlés az Azure Portalon.](../role-based-access-control/role-assignments-portal.md)

### <a name="tags"></a>Címkék

A **Címkék** szakasz segít az erőforrások rendszerezéséhez. További információkért lásd: [Az Azure-erőforrások rendszerezése címkék használatával](../azure-resource-manager/management/tag-resources.md) című cikket.


### <a name="diagnose-and-solve-problems"></a>Problémák diagnosztizálása és megoldása

Kattintson **a Problémák diagnosztizálása és megoldása** gombra, amelyek a megoldásukhoz kapcsolódó gyakori problémákat és stratégiákat kapnak.



## <a name="settings"></a>Beállítások
A **Beállítások** szakasz lehetővé teszi a gyorsítótár következő beállításainak elérését és konfigurálását.

* [Elérési kulcs](#access-keys)
* [Speciális beállítások](#advanced-settings)
* [Azure-gyorsítótár a Redis tanácsadószámára](#azure-cache-for-redis-advisor)
* [Méretezés](#scale)
* [Fürt mérete](#cluster-size)
* [Adatmegőrzés](#redis-data-persistence)
* [Frissítések ütemezése](#schedule-updates)
* [Georeplikáció](#geo-replication)
* [Virtual Network](#virtual-network)
* [Tűzfal](#firewall)
* [Tulajdonságok](#properties)
* [Zárak](#locks)
* [Automation-szkript](#automation-script)



### <a name="access-keys"></a>Elérési kulcs
Kattintson **az Access billentyűk** elemre a gyorsítótár hozzáférési kulcsainak megtekintéséhez vagy újragenerálásához. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják.

![Azure-gyorsítótár a Redis hozzáférési kulcsokhoz](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Speciális beállítások
A speciális **beállítások** panelen a következő beállítások vannak konfigurálva.

* [Hozzáférési portok](#access-ports)
* [Memóriaházirendek](#memory-policies)
* [Keyspace értesítések (speciális beállítások)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Hozzáférési portok
Alapértelmezés szerint a nem TLS/SSL-hozzáférés le van tiltva az új gyorsítótárak esetében. A nem TLS-port engedélyezéséhez kattintson a **Nem** gombra a **Csak SSL-en keresztüli hozzáférés engedélyezése** elemre a Speciális **beállítások** panelen, majd kattintson a **Mentés gombra.**

> [!NOTE]
> A Redis-hez való TLS-hozzáférés támogatja a TLS 1.0, 1.1 és 1.2-es verzióját, de az 1.0-s és 1.1-es verziók hamarosan megszűnnek.  További részletekért olvassa el [a TLS 1.0 és 1.1 eltávolításával kapcsolatos oldalunkat.](cache-remove-tls-10-11.md)

![Azure-gyorsítótár a Redis Access portokhoz](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Memóriaházirendek
A **Speciális beállítások** **panelMaxmemory házirendje**, **maxmemory-reserved**és **maxfragmentationmemory-reserved** beállításai konfigurálják a gyorsítótár memóriaházirendjeit.

![Azure-gyorsítótár a Redis Maxmemory házirendhez](./media/cache-configure/redis-cache-maxmemory-policy.png)

**A Maxmemory házirend** konfigurálja a gyorsítótár kilakoltatási házirendjeit, és lehetővé teszi a következő kilakoltatási házirendek közül való választást:

* `volatile-lru`- Ez az alapértelmezett kilakoltatási szabály.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

A házirendekről `maxmemory` a [Kilakoltatási házirendekről](https://redis.io/topics/lru-cache#eviction-policies)további információt talál.

A **maxmemory-fenntartott** beállítás konfigurálja a nem gyorsítótárazási műveletekhez, például a feladatátvétel során történő replikációhoz lefoglalt memória mennyiségét MB-ban. Ennek az értéknek a beállításával egységesebb Redis-kiszolgálói élményt biztosít, ha a terhelés változik. Ezt az értéket magasabbra kell állítani a nehéz írási feladatokhoz. Ha a memória ilyen műveletekszámára van fenntartva, nem érhető el a gyorsítótárazott adatok tárolására.

A **maxfragmentationmemory-reserved** beállítás konfigurálja a memória töredezettségének elhelyezésére fenntartott mb memóriát. Ennek az értéknek a beállításával egységesebb Redis-kiszolgálói élményt biztosít, ha a gyorsítótár megtelt vagy közel van a teljeshez, és a töredezettségi arány magas. Ha a memória ilyen műveletekszámára van fenntartva, nem érhető el a gyorsítótárazott adatok tárolására.

Egy dolog, hogy fontolja meg, amikor kiválasztják az új memória foglalási érték (**maxmemory-reserved** vagy **maxfragmentationmemory-reserved**) az, hogy ez a változás hatással lehet a cache, amely már fut a nagy mennyiségű adatot benne. Ha például 53 GB-os gyorsítótárral rendelkezik 49 GB-os adatokkal, majd a foglalási értéket 8 GB-ra módosítja, ez a módosítás a rendszer számára rendelkezésre álló maximális memóriát 45 GB-ra csökkenti. Ha az `used_memory` aktuális `used_memory_rss` vagy az értékek magasabbak, mint az új 45 GB-os korlát, akkor a rendszernek ki kell zárnia az adatokat, amíg mindkettő `used_memory` nem `used_memory_rss` lesz 45 GB alatt. A kilakoltatás növelheti a kiszolgáló terhelését és a memória töredezettségét. A gyorsítótár-mérőszámokkal kapcsolatos `used_memory` `used_memory_rss`további információkért, például a ( [Elérhető mutatók és jelentési időszakok](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)) című témakörben talál további információt.

> [!IMPORTANT]
> A **maxmemory-reserved** és **maxfragmentationmemory-reserved** beállítások csak standard és prémium szintű gyorsítótárak esetén érhetők el.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace értesítések (speciális beállítások)
A Redis keyspace értesítései a **Speciális beállítások** panelen vannak konfigurálva. A kulcstéri értesítések lehetővé teszik az ügyfelek számára, hogy bizonyos események bekövetkeztekor értesítéseket kapjanak.

![Azure-gyorsítótár a Redis speciális beállításaihoz](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> A Keyspace-értesítések és a **notify-keyspace-events** beállítás csak standard és prémium szintű gyorsítótárak esetén érhető el.
>
>

További információ: [Redis Keyspace Notifications](https://redis.io/topics/notifications). A mintakódot tekintse meg a [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fájlt a [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure-gyorsítótár a Redis tanácsadószámára
Az **Azure Cache for Redis Advisor** panel javaslatokat jelenít meg a gyorsítótár. A normál működés során nem jelennek meg javaslatok.

![Javaslatok](./media/cache-configure/redis-cache-no-recommendations.png)

Ha bármilyen feltétel lép fel a gyorsítótár műveletei során, például a nagy memóriahasználat, a hálózati sávszélesség vagy a kiszolgáló terhelése, riasztás jelenik meg az **Azure Cache for Redis** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations-alert.png)

További információ a **Javaslatok** panelen található.

![Javaslatok](./media/cache-configure/redis-cache-recommendations.png)

Ezeket a metrikákat a [figyelési diagramok](cache-how-to-monitor.md#monitoring-charts) és [a használat diagramok](cache-how-to-monitor.md#usage-charts) az **Azure Cache for Redis** panel figyelése.

Minden tarifacsomag különböző korlátozásokat biztosít az ügyfélkapcsolatokra, a memóriára és a sávszélességre vonatkozóan. Ha a gyorsítótár megközelíti a metrikák maximális kapacitását egy hosszabb ideig, egy javaslat jön létre. A **Javaslatok** eszköz által áttekintett mutatókról és korlátokról az alábbi táblázatban talál további információt:

| Azure Cache for Redis metrika | További információ |
| --- | --- |
| Hálózatisávszélesség-felhasználás |[Gyorsítótár-teljesítmény – rendelkezésre álló sávszélesség](cache-faq.md#cache-performance) |
| Csatlakoztatott ügyfelek |[Alapértelmezett Redis-kiszolgálókonfiguráció - maxclients](#maxclients) |
| Kiszolgáló betöltése |[Használati diagramok - Redis Server Load](cache-how-to-monitor.md#usage-charts) |
| Memóriahasználat |[Gyorsítótár teljesítménye - méret](cache-faq.md#cache-performance) |

A gyorsítótár frissítéséhez kattintson a **Frissítés most** gombra a tarifacsomag módosításához és a gyorsítótár [méretezéséhez.](#scale) A tarifacsomag kiválasztásáról a [Mi az Azure-gyorsítótár a Redis-ajánlathoz és a mérethez című témakörben talál további információt?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Méretezés
Kattintson a **Méretezés** gombra a gyorsítótár tarifacsomagjának megtekintéséhez vagy módosításához. A méretezésről további információt az [Azure-gyorsítótár redis-hez méretezése című témakörben talál.](cache-how-to-scale.md)

![Azure cache a Redis-hez díjszabási szint](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis fürt mérete
Kattintson a **Fürtméret gombra** a fürtözést engedélyezve lévő, prémium szintű gyorsítótár fürtméretének módosításához.

![Fürt mérete](./media/cache-configure/redis-cache-redis-cluster-size.png)

A fürt méretének módosításához használja a csúszkát, vagy írjon be egy 1 és 10 közötti számot a **Szegmensszám** mezőbe, és kattintson az **OK** gombra a mentéshez.

> [!IMPORTANT]
> A Redis-fürtözés csak prémium szintű gyorsítótárak esetén érhető el. További információ: [A fürtözés konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis hez című témakörben.](cache-how-to-premium-clustering.md)
>
>


### <a name="redis-data-persistence"></a>Redis-adatmegőrzés
Kattintson **az adatmegőrzés elemre** a prémium szintű gyorsítótár adatmegőrzésengedélyezéséhez, letiltásához vagy konfigurálásához. Az Azure Cache for Redis a Redis adatmegőrzést [rdb-tartalommegőrzéssel](cache-how-to-premium-persistence.md#configure-rdb-persistence) vagy [AOF adatmegőrzéssel](cache-how-to-premium-persistence.md#configure-aof-persistence)kínálja.

További információ: [A Redis prémium szintű Azure-gyorsítótárának adatmegőrzési beállítása](cache-how-to-premium-persistence.md)című témakörben talál.


> [!IMPORTANT]
> A Redis adatmegőrzéscsak prémium szintű gyorsítótárak esetén érhető el.
>
>

### <a name="schedule-updates"></a>Frissítések ütemezése
A **Frissítés ütemezése** panel lehetővé teszi, hogy kijelölje a karbantartási időszak redis szerver frissítések a gyorsítótárba.

> [!IMPORTANT]
> A karbantartási időszak csak a Redis-kiszolgáló frissítéseire vonatkozik, a gyorsítótárat üzemeltető virtuális gépek operációs rendszerének egyetlen Azure-frissítésére és frissítéseire nem.
>
>

![Frissítések ütemezése](./media/cache-configure/redis-schedule-updates.png)

Karbantartási időszak megadásához ellenőrizze a kívánt napokat, és adja meg a karbantartási időszak kezdési óráját az egyes napokhoz, majd kattintson az **OK**gombra. A karbantartási időszak ideje UTC-ben van.

> [!IMPORTANT]
> A **Frissítésütemezés** funkció csak prémium szintű gyorsítótárak esetén érhető el. További információt és utasításokat az [Azure Cache for Redis felügyeleti – Frissítések ütemezése című](cache-administration.md#schedule-updates)témakörben talál.
>
>

### <a name="geo-replication"></a>Georeplikáció

A **georeplikációs** panel két prémium szintű Azure-gyorsítótár redis-példányok összekapcsolására szolgáló mechanizmust biztosít. Az egyik gyorsítótár elsődleges csatolt gyorsítótárként, a másik pedig másodlagos csatolt gyorsítótárként van megjelölve. A másodlagos csatolt gyorsítótár írásvédetté válik, és az elsődleges gyorsítótárba írt adatok replikálódnak a másodlagos csatolt gyorsítótárba. Ez a funkció a gyorsítótár Replikálására használható az Azure-régiók között.

> [!IMPORTANT]
> **A georeplikáció** csak a prémium szintű gyorsítótárak esetében érhető el. További információt és utasításokat a [Georeplikáció konfigurálása az Azure Cache for Redis című témakörben talál.](cache-how-to-geo-replication.md)
>
>

### <a name="virtual-network"></a>Virtual Network
A **Virtuális hálózat** szakasz lehetővé teszi a gyorsítótár virtuális hálózati beállításainak konfigurálását. A virtuális hálózat támogatásával rendelkező prémium szintű gyorsítótár létrehozásáról és a beállítások frissítéséről a [Virtuális hálózati támogatás konfigurálása prémium szintű Azure-gyorsítótárhoz a Redis hez](cache-how-to-premium-vnet.md)című témakörben olvashat.

> [!IMPORTANT]
> A virtuális hálózati beállítások csak a virtuális hálózat létrehozása során vnet-támogatással konfigurált prémium szintű gyorsítótárak esetében érhetők el.
>
>

### <a name="firewall"></a>Tűzfal

A tűzfalszabályok konfigurációja az összes Azure-gyorsítótárredis-szinthez elérhető.

Kattintson **a Tűzfal** gombra a gyorsítótár tűzfalszabályainak megtekintéséhez és konfigurálásához.

![Tűzfal](./media/cache-configure/redis-firewall-rules.png)

A kezdő és záró IP-címtartományú tűzfalszabályokat is megadhatja. Ha a tűzfalszabályok konfigurálva vannak, csak a megadott IP-címtartományokból származó ügyfélkapcsolatok kapcsolódhatnak a gyorsítótárhoz. Tűzfalszabály mentésekor a szabály hatékonysága rövid idő alatt lép hatályba. Ez a késleltetés általában kevesebb, mint egy perc.

> [!IMPORTANT]
> Az Azure Cache-ből a Redis figyelési rendszerek kapcsolatok mindig engedélyezettek, még akkor is, ha tűzfalszabályok vannak konfigurálva.
>
>

### <a name="properties"></a>Tulajdonságok
Kattintson **a Tulajdonságok** gombra a gyorsítótáradatainak megtekintéséhez, beleértve a gyorsítótár végpontját és a portokat.

![Azure-gyorsítótár a Redis tulajdonságaihoz](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Zárolások
A **Zárolások** szakasz lehetővé teszi egy előfizetés, erőforráscsoport vagy erőforrás zárolását, hogy megakadályozza a szervezet más felhasználóinak a kritikus erőforrások véletlen törlését vagy módosítását. További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automation-szkript

Kattintson **az Automation-parancsfájl** ra a telepített erőforrások sablonjának létrehozásához és exportálásához a jövőbeli központi telepítésekhez. A sablonok használatával kapcsolatos további tudnivalókért olvassa el az [Erőforrások üzembe helyezése az Azure Resource Manager-sablonokkal című témakört.](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="administration-settings"></a>Felügyeleti beállítások
A **Felügyelet** szakasz beállításai lehetővé teszik a gyorsítótár következő felügyeleti feladatainak elvégzését.

![Felügyelet](./media/cache-configure/redis-cache-administration.png)

* [Adatok importálása](#importexport)
* [Adatok exportálása](#importexport)
* [Újraindítás](#reboot)


### <a name="importexport"></a>Import/Export
Az importálás/exportálás egy Azure-gyorsítótár a Redis adatkezelési művelethez, amely lehetővé teszi az adatok importálását és exportálását a gyorsítótárban egy Azure-gyorsítótár redis-adatbázishoz (RDB) pillanatkép importálásával és exportálásával egy prémium szintű gyorsítótárból egy Azure Storage-fiókban lévő lapblobba. Az importálás/exportálás lehetővé teszi a redis-példányok különböző Azure-gyorsítótárai közötti áttelepítést, vagy a gyorsítótár feltöltése adatokkal használat előtt.

Az importálás segítségével redis kompatibilis RDB fájlokat hozhat bármely Redis szerverről, amely bármilyen felhőben vagy környezetben fut, beleértve a Linuxon, Windowsrendszeren vagy bármely felhőszolgáltatón futó Redis-t, például az Amazon Web Services-t és másokat. Az adatok importálásával egyszerűen hozhat létre gyorsítótárat előre kitöltött adatokkal. Az importálási folyamat során az Azure Cache for Redis betölti az RDB-fájlokat az Azure storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi, hogy exportálja az Azure Cache for Redis a Redis kompatibilis RDB-fájlokat tárolt adatokat. Ezzel a funkcióval áthelyezheti az adatokat az egyik Azure-gyorsítótárból a Redis-példányegy másik vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gép, amely az Azure Cache for Redis kiszolgálópéldány, és a fájl feltöltése a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

> [!IMPORTANT]
> Az importálás/exportálás csak a prémium szintű gyorsítótárak esetében érhető el. További információt és utasításokat az [Adatok importálása és exportálása az Azure Cache for Redis alkalmazásban](cache-how-to-import-export-data.md)című témakörben talál.
>
>

### <a name="reboot"></a>Újraindítás
Az **Újraindítás** panel lehetővé teszi a gyorsítótár csomópontjainak újraindítását. Ez az újraindítási képesség lehetővé teszi, hogy tesztelje az alkalmazást a rugalmasság, ha egy gyorsítótár-csomópont meghibásodása van.

![Újraindítás](./media/cache-configure/redis-cache-reboot.png)

Ha a fürtözés engedélyezve van a prémium szintű gyorsítótár, kiválaszthatja, hogy a gyorsítótár mely szegmenseit szeretné újraindítani.

![Újraindítás](./media/cache-configure/redis-cache-reboot-cluster.png)

A gyorsítótár egy vagy több csomók újraindításához jelölje ki a kívánt csomópontokat, és kattintson az **Újraindítás gombra.** Ha a fürtözés engedélyezve van a prémium szintű gyorsítótárral, válassza ki az újraindítani kívánt szegmens(eke)t, majd kattintson az **Újraindítás**gombra. Néhány perc múlva a kijelölt csomópont(ok) újraindulnak, és néhány perccel később újra online állapotba kerülnek.

> [!IMPORTANT]
> Az újraindítás már elérhető az összes tarifacsomaghoz. További információt és utasításokat az [Azure Cache for Redis felügyeleti – Újraindítás](cache-administration.md#reboot)című témakörben talál.
>
>


## <a name="monitoring"></a>Figyelés

A **figyelési** szakasz lehetővé teszi, hogy konfigurálja a diagnosztikát és a figyelést az Azure Cache for Redis.
Az Azure Cache for Redis figyelése és diagnosztika című témakörben további információt a [Redis Azure-gyorsítótárfigyelésfigyelése című témakörben talál.](cache-how-to-monitor.md)

![Diagnosztika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis metrikák](#redis-metrics)
* [Riasztási szabályok](#alert-rules)
* [Diagnosztika](#diagnostics)

### <a name="redis-metrics"></a>Redis metrikák
Kattintson **a Redis mérőszámok** a gyorsítótár [metrikák megtekintéséhez.](cache-how-to-monitor.md#view-cache-metrics)

### <a name="alert-rules"></a>Riasztási szabályok

Kattintson **a riasztási szabályok** konfigurálásához riasztások alapján az Azure Cache redis metrikák. További információt a Riasztások című [témakörben talál.](cache-how-to-monitor.md#alerts)

### <a name="diagnostics"></a>Diagnosztika

Alapértelmezés szerint az Azure Monitor gyorsítótár-metrikák [30 napig tárolódnak,](../azure-monitor/platform/data-platform-metrics.md) majd törlődnek. A gyorsítótár-metrikák 30 napnál hosszabb ideig való megőrzéséhez kattintson a **Diagnosztika** elemre a gyorsítótár-diagnosztika tárolásához használt [tárfiók konfigurálásához.](cache-how-to-monitor.md#export-cache-metrics)

>[!NOTE]
>A gyorsítótár-metrikák tárolásba való archiválása mellett [streamelheti őket egy eseményközpontba is, vagy elküldheti őket az Azure Monitor naplóiba.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
>
>

## <a name="support--troubleshooting-settings"></a>Támogatás & hibaelhárítási beállítások
A Támogatás **+ hibaelhárítás** szakasz beállításai lehetőséget nyújtanak a gyorsítótárral kapcsolatos problémák megoldására.

![Támogatás + hibaelhárítás](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Erőforrás állapota](#resource-health)
* [Új támogatási kérelem](#new-support-request)

### <a name="resource-health"></a>Erőforrás állapota
**Az erőforrás állapota** figyeli az erőforrást, és jelzi, hogy a várt módon fut-e. Az Azure Resource állapotszolgáltatásáról az [Azure Resource állapotának áttekintése](../resource-health/resource-health-overview.md)című témakörben olvashat bővebben.

> [!NOTE]
> Az erőforrás állapota jelenleg nem tud jelentést tenni a virtuális hálózatban üzemeltetett Redis-példányok Azure-gyorsítótárállapotáról. További információ: [Az összes gyorsítótár-szolgáltatás működik, ha a virtuális hálózatban gyorsítótárat tárol?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
>
>

### <a name="new-support-request"></a>Új támogatási kérelem
Kattintson **az Új támogatási kérelem** elemre a gyorsítótár támogatási kérelmének megnyitásához.





## <a name="default-redis-server-configuration"></a>Alapértelmezett Redis-kiszolgálókonfiguráció
A Redis-példányok új Azure-gyorsítótára a következő alapértelmezett Redis konfigurációs értékekkel van konfigurálva:

> [!NOTE]
> Az ebben a szakaszban lévő `StackExchange.Redis.IServer.ConfigSet` beállítások nem módosíthatók a módszerrel. Ha ezt a módszert az ebben a szakaszban található parancsok egyikével hívják meg, a következő példához hasonló kivétel jelenik meg:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> A konfigurálható értékek, például a **max-memória-házirend,** konfigurálhatók az Azure Portalon vagy a parancssori felügyeleti eszközök, például az Azure CLI vagy a PowerShell segítségével.
>
>

| Beállítás | Alapértelmezett érték | Leírás |
| --- | --- | --- |
| `databases` |16 |Az adatbázisok alapértelmezett száma 16, de a tarifacsomag alapján más számot is konfigurálhat. <sup>1</sup> Az alapértelmezett adatbázis DB 0, kapcsolatonként választhat egy `connection.GetDatabase(dbid)` másikat a használatával, ahol `dbid` a szám van a és `0` `databases - 1`a között. |
| `maxclients` |A<sup>2.</sup> |Ez az érték az egy időben engedélyezett csatlakoztatott ügyfelek maximális száma. A korlát elérése után a Redis bezárja az összes új kapcsolatot, és "az elért ügyfelek maximális száma" hibát ad vissza. |
| `maxmemory-policy` |`volatile-lru` |A Maxmemory-házirend az a beállítás, amely `maxmemory` azt határozza meg, hogy a Redis hogyan válassza ki, hogy mit szeretne eltávolítani, ha (a gyorsítótár-gyorsítótár-ajánlat mérete, amelyet a gyorsítótár létrehozásakor választott ki) elérésekor. Az Azure Cache for Redis `volatile-lru`az alapértelmezett beállítás, amely eltávolítja a kulcsokat egy lejárati készlet egy LRU-algoritmus használatával. Ez a beállítás konfigurálható az Azure Portalon. További információt a [Memóriaházirendek](#memory-policies)című témakörben talál. |
| `maxmemory-samples` |3 |A memória megtakarítása érdekében az LRU és a minimális TTL algoritmusok a pontos algoritmusok helyett közelítő algoritmusok. Alapértelmezés szerint a Redis három kulcsot ellenőriz, és a kevésbé a közelmúltban használt kulcsot választja. |
| `lua-time-limit` |5000 |A Lua parancsfájl maximális végrehajtási ideje ezredmásodpercben. Ha elérte a maximális végrehajtási időt, a Redis naplózza, hogy a parancsfájl a maximálisan engedélyezett idő után is végrehajtás alatt áll, és hibaüzenetet kezd válaszolni a lekérdezésekre. |
| `lua-event-limit` |500 |Parancsfájl-eseményvárólista maximális mérete. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8MB 60 |Az ügyfél kimeneti pufferkorlátai segítségével kényszerítheti az olyan ügyfelek leválasztását, amelyek valamilyen okból nem olvassák elég gyorsan az adatokat a kiszolgálóról (gyakori oka, hogy a Pub/Sub ügyfél nem tudja olyan gyorsan használni az üzeneteket, mint a közzétevő). További információ: [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1 1</sup> A korlát `databases` az egyes Azure-gyorsítótár redis-i tarifacsomag, és beállítható a gyorsítótár létrehozása. Ha `databases` a gyorsítótár létrehozása során nincs megadva beállítás, az alapértelmezett érték 16.

* Alapszintű és szabványos gyorsítótárak
  * C0 (250 MB) gyorsítótár – akár 16 adatbázis
  * C1 (1 GB) gyorsítótár – akár 16 adatbázis
  * C2 (2,5 GB) gyorsítótár – akár 16 adatbázis
  * C3 (6 GB) gyorsítótár – akár 16 adatbázis
  * C4 (13 GB) gyorsítótár – akár 32 adatbázis
  * C5 (26 GB) gyorsítótár – akár 48 adatbázis
  * C6 (53 GB) gyorsítótár – akár 64 adatbázis
* Prémium szintű gyorsítótárak
  * P1 (6 GB - 60 GB) – akár 16 adatbázis
  * P2 (13 GB - 130 GB) – akár 32 adatbázis
  * P3 (26 GB - 260 GB) – akár 48 adatbázis
  * P4 (53 GB - 530 GB) – akár 64 adatbázis
  * Minden prémium szintű gyorsítótár, amelyen a Redis cluster engedélyezve `databases` van – a Redis-fürt csak a 0-s adatbázis használatát támogatja, így a Redis fürt engedélyezése esetén a prémium szintű gyorsítótárak rakoncátlehe gyakorlatilag 1, és a [Select](https://redis.io/commands/select) parancs nem engedélyezett. További információ: [Az ügyfélalkalmazáson módosításokat kell végeznem a fürtözés használatához?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Az adatbázisokról további információt a [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` beállítás csak a gyorsítótár létrehozása során konfigurálható, és csak a PowerShell, a CLI vagy más felügyeleti ügyfelek használatával. A Gyorsítótár-létrehozás `databases` során a PowerShell használatával történő konfigurálásról a [New-AzRedisCache című témakörben található.](cache-how-to-manage-redis-cache-powershell.md#databases)
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` a Redis-díjcsomag hoz tartozó minden Egyes Azure-gyorsítótáresetében eltérő.

* Alapszintű és szabványos gyorsítótárak
  * C0 (250 MB) gyorsítótár - akár 256 kapcsolat
  * C1 (1 GB) gyorsítótár – akár 1000 kapcsolat
  * C2 (2,5 GB) gyorsítótár - akár 2000 kapcsolat
  * C3 (6 GB) gyorsítótár – akár 5000 kapcsolat
  * C4 (13 GB) gyorsítótár – akár 10 000 kapcsolat
  * C5 (26 GB) gyorsítótár – akár 15 000 kapcsolat
  * C6 (53 GB) gyorsítótár – akár 20 000 kapcsolat
* Prémium szintű gyorsítótárak
  * P1 (6 GB - 60 GB) - akár 7500 csatlakozás
  * P2 (13 GB - 130 GB) - akár 15 000 kapcsolat
  * P3 (26 GB - 260 GB) - akár 30 000 kapcsolat
  * P4 (53 GB - 530 GB) - akár 40 000 kapcsolat

> [!NOTE]
> Bár a gyorsítótár minden egyes mérete *lehetővé teszi, hogy egy* bizonyos számú kapcsolat, minden kapcsolat Redis van többletterhelést társítva. Ilyen többletterhelés például a CPU és a memória használata a TLS/SSL titkosítás eredményeként. Egy adott gyorsítótár méretének maximális kapcsolati korlátja enyhén betöltött gyorsítótárat feltételez. Ha az ügyfélműveletekből származó terhelés *és* az ügyfélműveletekből származó terhelés meghaladja a rendszer kapacitását, a gyorsítótár kapacitásproblémákat tapasztalhat, még akkor is, ha nem lépte túl az aktuális gyorsítótár méretére vonatkozó kapcsolati korlátot.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>A Redis-parancsokat nem támogatja a Redis Azure Cache for Redis
> [!IMPORTANT]
> Mivel a Redis-példányok Azure Cache konfigurációját és felügyeletét a Microsoft kezeli, a következő parancsok le vannak tiltva. Ha megpróbálja meghívni őket, a következőhöz `"(error) ERR unknown command"`hasonló hibaüzenet jelenik meg: .
>
> * BGREWRITEAOF
> * BGSAVE
> * Config
> * HIBAKERESÉS
> * Áttelepítése
> * MENTÉS
> * Shutdown
> * SLAVEOF KÖZÖTT
> * CLUSTER - A fürt írási parancsai le vannak tiltva, de csak olvasható fürtparancsok engedélyezettek.
>
>

A Redis parancsokról további [https://redis.io/commands](https://redis.io/commands)információt a .

## <a name="redis-console"></a>Redis konzol
Biztonságosan kiadhat parancsokat az Azure Cache for Redis-példányok a **Redis console**használatával, amely elérhető az Azure Portalon az összes gyorsítótár-szint.

> [!IMPORTANT]
> - A Redis konzol nem működik a [Virtuális hálózattal.](cache-how-to-premium-vnet.md) Ha a gyorsítótár egy virtuális hálózat része, csak a virtuális hálózatban lévő ügyfelek férhetnek hozzá a gyorsítótárhoz. Mivel a Redis-konzol a helyi böngészőben fut, amely kívül esik a virtuális hálózaton, nem tud csatlakozni a gyorsítótárhoz.
> - Nem minden Redis-parancs támogatott az Azure Cache for Redis. Az Azure Cache for Redis letiltott Redis-parancsok listáját az Azure Cache for Redis című szakaszban nem támogatott korábbi [Redis-parancsok](#redis-commands-not-supported-in-azure-cache-for-redis) című témakörben található. A Redis parancsokról további [https://redis.io/commands](https://redis.io/commands)információt a .
>
>

A Redis konzol eléréséhez kattintson a **Konzol** az **Azure Cache for Redis** panel.

![Redis konzol](./media/cache-configure/redis-console-menu.png)

Ha parancsokat szeretne kiadni a gyorsítótárpéldányellen, írja be a kívánt parancsot a konzolba.

![Redis konzol](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>A Redis konzol használata prémium szintű fürtözött gyorsítótárral

Ha a Redis-konzolt prémium szintű fürtözött gyorsítótárral használja, parancsokat adhat ki a gyorsítótár egyetlen szegmensének. Ha parancsot szeretne kiadni egy adott szegmenshez, először csatlakozzon a kívánt szegmenshez a shard-választóra kattintva.

![Redis konzol](./media/cache-configure/redis-console-premium-cluster.png)

Ha a csatlakoztatott szegmenstől eltérő szegmensben tárolt kulcshoz próbál hozzáférni, a következőhöz hasonló hibaüzenet jelenik meg:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

Az előző példában az 1-es szegmens `myKey` a kijelölt szegmens, de a `(shard 0)` 0-s szegmensben található, ahogy azt a hibaüzenet része jelzi. Ebben a példában `myKey`a hozzáféréshez válassza ki a 0-as szegmenst a szegmensválasztó használatával, majd adja ki a kívánt parancsot.


## <a name="move-your-cache-to-a-new-subscription"></a>Gyorsítótár áthelyezése új előfizetésbe
A gyorsítótárat áthelyezheti egy új előfizetésbe az **Áthelyezés**gombra kattintva.

![Az Azure-gyorsítótár áthelyezése a Redis-hez](./media/cache-configure/redis-cache-move.png)

Az erőforrások egyik erőforráscsoportból a másikba, illetve az egyik előfizetésből a másikba történő áthelyezéséről az [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md)című témakörben talál további információt.

## <a name="next-steps"></a>További lépések
* A Redis-parancsok kezeléséről a [Hogyan futtathatom a Redis parancsokat?](cache-faq.md#how-can-i-run-redis-commands)
