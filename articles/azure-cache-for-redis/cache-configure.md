---
title: Azure Cache Redis konfigurálása |} A Microsoft Docs
description: A Redis az Azure Cache Redis alapértelmezett konfigurációja ismertetése, és ismerje meg, hogyan konfigurálhatja az Azure Cache a Redis-példány
services: cache
documentationcenter: na
author: yegu-ms
manager: jhubbard
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache
ms.workload: tbd
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 67a093068df30460a1b2cf17c04164bf8b1dc6a0
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56234900"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Azure Cache Redis konfigurálása
Ez a témakör ismerteti az Azure Cache a Redis-példány érhető el a konfigurációkat. Ez a témakör ismerteti a Redis server az alapértelmezett konfigurációjának for Azure Cache Redis-példány is.

> [!NOTE]
> Konfigurálása és használata a prémiumszintű gyorsítótár funkcióival további információkért lásd: [adatmegőrzés konfigurálása](cache-how-to-premium-persistence.md), [fürtözés konfigurálása](cache-how-to-premium-clustering.md), és [Virtuálishálózat-támogatást konfigurálása ](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-azure-cache-for-redis-settings"></a>Azure Cache Redis beállításainak konfigurálása
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Az Azure Cache Redis beállítások tekinthetők meg, és konfigurálni a **Azure Cache redis** panel használatával a **erőforrás menüben**.

![Az Azure Cache Redis-beállítások](./media/cache-configure/redis-cache-settings.png)

Megtekintheti és használatával a következő beállításokat konfigurálhatja a **erőforrás menüben**.

* [Áttekintés](#overview)
* [Tevékenységnapló](#activity-log)
* [Hozzáférés-vezérlés (IAM)](#access-control-iam)
* [Címkék](#tags)
* [Problémák diagnosztizálása és megoldása](#diagnose-and-solve-problems)
* [Beállítások](#settings)
    * [Hozzáférési kulcsok](#access-keys)
    * [Speciális beállítások](#advanced-settings)
    * [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
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
* Adminisztráció
    * [Adatok importálása](#importexport)
    * [Adatok exportálása](#importexport)
    * [Újraindítás](#reboot)
* [Monitorozás](#monitoring)
    * [Redis-metrikák](#redis-metrics)
    * [Riasztási szabályok](#alert-rules)
    * [Diagnosztika](#diagnostics)
* Támogatás és hibaelhárítás beállításai
    * [A Resource health](#resource-health)
    * [Új támogatási kérelem](#new-support-request)


## <a name="overview"></a>Áttekintés

**Áttekintés** biztosít, és alapvető információkat szeretne a gyorsítótár, például nevét, a portok, a tarifacsomag, és kijelölt gyorsítótár-metrikák.

### <a name="activity-log"></a>Tevékenységnapló

Kattintson a **tevékenységnapló** művelet elvégezhető a gyorsítótár megtekintéséhez. Is használhatja szűrés bontsa ki a más erőforrások, ezt a nézetet. A vizsgálati naplók használatáról további információkért lásd: [auditálási műveletek a Resource Manager](../azure-resource-manager/resource-group-audit.md). Azure Cache Redis-események figyeléséről további információkért lásd: [műveletek és a riasztások](cache-how-to-monitor.md#operations-and-alerts).

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
* [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
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

![Az Azure Cache Redis hozzáférési kulcsok](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Speciális beállítások
A következő beállításokat a **speciális beállítások** panelen.

* [Hozzáférési portok](#access-ports)
* [Memória-szabályzatok](#memory-policies)
* [Kulcstérértesítések (Speciális beállítások)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Hozzáférési portok
A nem SSL hozzáférés alapértelmezés szerint le van tiltva az új gyorsítótárakhoz. A nem SSL port engedélyezéséhez kattintson **nem** a **engedélyezi a hozzáférést csak SSL használatával** a a **speciális beállítások** panelen, majd kattintson **mentése**.

> [!NOTE]
> SSL-hozzáférés az Azure Cache a Redis támogatja a TLS 1.0 alapértelmezés szerint. A minimális támogatott TLS-verzió a TLS 1.2 legfeljebb lehet megemelni segítségével szükség esetén a **TLS minimális verziója** legördülő listából a **speciális beállítások** panelen, majd kattintson **mentése**.

![Az Azure Cache Redis hozzáférési portok](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Memória-szabályzatok
A **Maxmemory házirend**, **maxmemory fenntartott**, és **maxfragmentationmemory fenntartott** beállításait a **speciális beállítások** panelen konfigurálja a memória a gyorsítótárhoz.

![Az Azure Cache Redis Maxmemory házirend](./media/cache-configure/redis-cache-maxmemory-policy.png)

**A Maxmemory házirend** konfigurálja a gyorsítótár kiürítési szabályzatát, és lehetővé teszi, hogy a következő kiürítési szabályzat közül választhat:

* `volatile-lru` – Ez az alapértelmezett kiürítési szabályzatát.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

További információ `maxmemory` házirendek, lásd: [kiürítési szabályzat](https://redis.io/topics/lru-cache#eviction-policies).

A **maxmemory fenntartott** beállítással memória mennyisége (MB), nem gyorsítótárazási műveletek, például a replikáció, feladatátvétel során tart fenn. Ha az érték lehetővé teszi, hogy a Redis kiszolgáló egységesebb, ha változik a terhelés. Ez az érték nagyobb számítási feladatokhoz, hogy van írási (nagy erőforrásigényű) kell állítani. Memória ilyen műveletek számára van fenntartva, ha nem érhető el a gyorsítótárazott adatok tárolására.

A **maxfragmentationmemory fenntartott** -beállítással konfigurálható a memória mennyisége (MB), amely a memória töredezettségét terhelésének megfelelően van fenntartva. Az érték lehetővé teszi, hogy a Redis kiszolgáló egységesebb, amikor a gyorsítótár teljes vagy közel teljes és a töredezettség aránya túl magas. Memória ilyen műveletek számára van fenntartva, ha nem érhető el a gyorsítótárazott adatok tárolására.

Egy új memória foglalás értéket kiválasztásakor érdemes (**maxmemory fenntartott** vagy **maxfragmentationmemory fenntartott**) milyen ezt a módosítást, amely már fut a gyorsítótár hatással lehet a nagy mennyiségű adattal. Ha rendelkezik egy 53 GB-os gyorsítótár 49 GB adatot tartalmazó, majd módosítsa a foglalási 8 GB, például a módosítás csökken, a maximális rendelkezésre álló memória 45 GB le a rendszer. Ha az aktuális `used_memory` vagy a `used_memory_rss` értékek magasabb, mint az új határértéket 45 GB, akkor a rendszer lesz, amíg mindkettő adatainak fürtből `used_memory` és `used_memory_rss` 45 GB alatt. A kizárási növelheti a kiszolgáló terhelését és a memória töredezettség. További információ a gyorsítótár-metrikák például `used_memory` és `used_memory_rss`, lásd: [rendelkezésre álló metrikák és a jelentéskészítés időközök](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> A **maxmemory fenntartott** és **maxfragmentationmemory fenntartott** beállítások csak érhetők el a Standard és prémium szintű gyorsítótárazza.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Kulcstérértesítések (Speciális beállítások)
Kulcstér értesítések be vannak állítva, a redis a **speciális beállítások** panelen. Kulcstérértesítések engedélyezése az ügyfelek értesítéseket kaphat, amikor bizonyos események történnek.

![Azure Cache for Redis Advanced Settings](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Kulcstérértesítések, és a **értesítése kulcstér-események** beállítás csak Standard és prémium gyorsítótárak esetében érhető el.
> 
> 

További információkért lásd: [Kulcstérértesítések Redis](https://redis.io/topics/notifications). A mintakódot, tekintse meg a [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) fájlt a [Helló, világ](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) minta.


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Az Azure Cache Redis tanácsadó
A **Azure Cache redis Cache Advisor** panel jeleníti meg a javaslatok a gyorsítótár számára. A normál működés során nincsenek javaslatok jelennek meg. 

![Javaslatok](./media/cache-configure/redis-cache-no-recommendations.png)

Azokat a feltételeket például magas memóriahasználat, a hálózati sávszélesség vagy a kiszolgáló terhelését a gyorsítótár a műveletek során fordul elő, ha egy riasztás jelenik meg a **Azure Cache redis** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations-alert.png)

További információk találhatók a **javaslatok** panelen.

![Javaslatok](./media/cache-configure/redis-cache-recommendations.png)

Ezeket a metrikákat a figyelheti a [figyelési diagramokat](cache-how-to-monitor.md#monitoring-charts) és [használati diagramok](cache-how-to-monitor.md#usage-charts) szakaszait a **Azure Cache redis** panelen.

Minden egyes tarifacsomagja az ügyfélkapcsolatokat, a memória és a sávszélesség eltérő korlátokkal rendelkeznek. A gyorsítótár maximális kapacitás, a következő metrikákhoz megközelíti a huzamosabb ideig keresztül, ha egy javaslatra jön létre. További információ a metrikák és a véleményező korlátok a **javaslatok** eszközt, az alábbi táblázatban foglaltuk össze:

| Az Azure Cache Redis-metrika | További információ |
| --- | --- |
| Hálózati sávszélesség-használat |[Gyorsítótár teljesítmény - rendelkezésre álló sávszélesség](cache-faq.md#cache-performance) |
| Csatlakozott ügyfelek |[A Redis-kiszolgáló konfiguráció - maxclients alapértelmezett](#maxclients) |
| Kiszolgáló terhelése |[Használati diagramok - Redis-kiszolgáló terhelése](cache-how-to-monitor.md#usage-charts) |
| Memóriahasználat |[Gyorsítótár teljesítmény - mérete](cache-faq.md#cache-performance) |

Frissítse a gyorsítótárat, kattintson a **Frissítsen most** a tarifacsomag módosítása és [méretezési](#scale) a gyorsítótárat. A tarifacsomag kiválasztásáról további információkért lásd: [milyen az Azure Cache a Redis-ajánlatot és -méretet használjam?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)


### <a name="scale"></a>Méretezés
Kattintson a **méretezési** megtekintéséhez, vagy módosítsa a tarifacsomagot a gyorsítótárhoz. Méretezéssel kapcsolatos további információkért lásd: [How to Scale Azure Cache redis](cache-how-to-scale.md).

![Az Azure Cache redis-tarifacsomag](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Redis-fürt mérete
Kattintson a **(előzetes verzió) Redis-fürt mérete** módosítása egy futó szektorcsoportmérete prémium szintű gyorsítótár-fürtözés engedélyezve van.

> [!NOTE]
> Vegye figyelembe, hogy közben az Azure Cache redis Cache prémium szint most már általánosan elérhető általános rendelkezésre állás, a Redis-fürt mérete a szolgáltatás jelenleg előzetes verzióban érhető el.
> 
> 

![Redis-fürt mérete](./media/cache-configure/redis-cache-redis-cluster-size.png)

A fürt méretének módosításához a csúszka, vagy adjon meg egy számot 1 és 10 között a **szegmensszám** szövegmezőbe, majd kattintson **OK** mentéséhez.

> [!IMPORTANT]
> A redis-fürtözés lehetőség csak a prémium gyorsítótárak. További információkért lásd: [egy prémium szintű Azure Cache redis fürtözés konfigurálása](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Redis-adatmegőrzés
Kattintson a **Redis-adatmegőrzés** engedélyezéséhez letiltja, vagy konfigurálja a prémium szintű gyorsítótár adatainak megőrzését. Az Azure Cache redis kínál a Redis megőrzési funkciója segítségével [RDB-fájlba való megőrzését](cache-how-to-premium-persistence.md#configure-rdb-persistence) vagy [AOF adatmegőrzés](cache-how-to-premium-persistence.md#configure-aof-persistence).

További információkért lásd: [egy prémium szintű Azure Cache megőrzése a Redis konfigurálása](cache-how-to-premium-persistence.md).


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

Adja meg a karbantartási időszak, ellenőrizze a kívánt napok és adja meg a karbantartási időszak kezdő órája minden nap, és kattintson **OK**. A karbantartási ablak ideje (UTC).

> [!IMPORTANT]
> A **frissítések ütemezése** funkció csak prémium szinten gyorsítótárak rendelkezésre áll. További információk és utasítások: [Azure Cache Redis-felügyelet – frissítések ütemezése](cache-administration.md#schedule-updates).
> 
> 

### <a name="geo-replication"></a>Georeplikáció

A **georeplikációs** panelen két prémium szintű Azure Cache Redis-példány kapcsolásának mechanizmust biztosít. Egy gyorsítótár az elsődleges társított gyorsítótár, a másik pedig a csatolt másodlagos gyorsítótáraként van kijelölve. A másodlagos összekapcsolt gyorsítótár csak olvashatóvá válik, és az elsődleges gyorsítótár írt adatok replikálódnak a másodlagos összekapcsolt gyorsítótár. Ez a funkció segítségével gyorsítótár replikálása az Azure-régiók között.

> [!IMPORTANT]
> **Georeplikáció** csak prémium szinten gyorsítótárak esetében érhető el. További információk és utasítások: [georeplikáció konfigurálása az Azure Cache Redis](cache-how-to-geo-replication.md).
> 
> 

### <a name="virtual-network"></a>Virtual Network
A **virtuális hálózat** szakasz lehetővé teszi, hogy konfigurálja a virtuális hálózati beállításait a gyorsítótárhoz. Támogatja a virtuális hálózattal egy prémium szintű gyorsítótár létrehozásával kapcsolatos információkat és a beállítások frissítése, lásd: [konfigurálása virtuális hálózat támogatja a prémium szintű Azure Cache Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> A virtuális hálózati beállítások csak érhetők el a prémium gyorsítótárak konfigurált a VNET-támogatás gyorsítótár létrehozása során. 
> 
> 

### <a name="firewall"></a>Tűzfal

Tűzfal-szabályok konfiguráció az összes Azure Cache Redis szinten érhető el.

Kattintson a **tűzfal** megtekintéséhez és a gyorsítótár tűzfalszabályok konfigurálása.

![Tűzfal](./media/cache-configure/redis-firewall-rules.png)

A tűzfalszabályok segítségével adható meg egy kezdő és záró IP-címtartomány. Ha tűzfalszabályok vannak beállítva, csak a megadott IP-címtartományok érkező ügyfélkapcsolatokat is csatlakozás a gyorsítótárhoz. Ha olyan tűzfalszabályt, van egy kis ideig várakozni ahhoz a szabály érvényben. Ez a késleltetés van általában kevesebb egy percnél.

> [!IMPORTANT]
> Azure Cache redis megfigyelő rendszerek kapcsolódását mindig engedélyezettek, még akkor is, ha tűzfalszabályok vannak konfigurálva.
> 
> 

### <a name="properties"></a>Tulajdonságok
Kattintson a **tulajdonságok** a gyorsítótár, beleértve a gyorsítótár végpontját és a portok kapcsolatos információk megtekintéséhez.

![Az Azure Cache a Redis-tulajdonságok](./media/cache-configure/redis-cache-properties.png)

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
Importálás/exportálás szolgáltatás egy Azure Cache lehetővé teszi, hogy importálása és exportálása a gyorsítótárban lévő adatok importálása és a egy prémium szintű gyorsítótár az Azure Cache Redis adatbázis (RDB) pillanatkép exportálása egy lapblob egy Azure Storage-fiók Redis adatok felügyeleti művelethez. Importálási/exportálási lehetővé teszi a különböző Azure Cache a Redis-példány közötti áttelepítéséhez, vagy töltse fel a gyorsítótárat az adatokat használat előtt.

Importálás a Redis-kompatibilis RDB-fájlok viszi, minden bármilyen felhőben vagy a környezetben, beleértve a Linux, Windows vagy bármely más szolgáltatónál, például az Amazon Web Services és a többi futó Redis futó Redis-kiszolgálóról is használható. A rendszer egyszerűen hozzon létre egy gyorsítótárat az adatokkal előre feltöltött adatok importálása. Az importálási folyamat során az Azure Cache redis RDB-fájlba való fájlokat az Azure storage-ból betölti a memóriába, és majd szúrja be a kulcsokat a gyorsítótárba.

Exportálás lehetővé teszi, hogy a Redis a Redis-kompatibilis RDB-fájlba való fájlok Azure Cache-ben tárolt adatok exportálása. Ez a funkció használatával adatok áthelyezése egy másik vagy egy másik Redis-kiszolgáló egy Azure Cache a Redis-példányt. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gépen, amelyen az Azure Cache Redis-server-példányhoz, és a fájlt a kijelölt tárfiók töltenek fel. Az exportálási művelet befejeződik, vagy sikeres állapotú vagy sikertelen, amikor az ideiglenes fájl törlődik.

> [!IMPORTANT]
> Importálási/exportálási csak prémium szinten gyorsítótárak érhető el. További információk és utasítások: [adatok importálása és exportálása az Azure Cache redis](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Újraindítás
A **újraindítás** panel lehetővé teszi, hogy indítsa újra a csomópontokat, a gyorsítótár. Az újraindítás funkció lehetővé teszi, hogy tesztelje az alkalmazást a rugalmassághoz, ha sikertelen. a gyorsítótár-csomópont.

![Újraindítás](./media/cache-configure/redis-cache-reboot.png)

Ha egy prémium szintű gyorsítótár-fürtözés engedélyezve van, kiválaszthatja, melyik szegmensben indítani a gyorsítótár.

![Újraindítás](./media/cache-configure/redis-cache-reboot-cluster.png)

Indítsa újra a gyorsítótár egy vagy több csomópontot, válassza ki a kívánt csomópontokat, és kattintson a **újraindítás**. Ha egy prémium szintű gyorsítótár-fürtözés engedélyezve van, válassza ki az újraindítás, majd kattintson a szegmensről **újraindítás**. Néhány perc múlva a kijelölt csomópont újraindítása, és a rendszer ismét elérhető néhány perc múlva.

> [!IMPORTANT]
> Újraindítás most már az összes tarifacsomag érhető el. További információk és utasítások: [Azure Cache Redis-felügyelet – újraindítás](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Figyelés

A **figyelés** szakasz lehetővé teszi, hogy a diagnosztika és figyelés az Azure Cache redis konfigurálása. A Redis monitorozása és diagnosztizálása az Azure Cache további információkért lásd: [figyelése az Azure Cache redis](cache-how-to-monitor.md).

![Diagnosztika](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-metrikák](#redis-metrics)
* [Riasztási szabályok](#alert-rules)
* [Diagnosztika](#diagnostics)

### <a name="redis-metrics"></a>Redis-metrikák
Kattintson a **Redis-metrikák** való [megtekintheti a metrikákat](cache-how-to-monitor.md#view-cache-metrics) a gyorsítótárhoz.

### <a name="alert-rules"></a>Riasztási szabályok

Kattintson a **riasztási szabályok** Azure Cache Redis-metrikák alapján riasztások konfigurálása. További információkért lásd: [riasztások](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnosztika

Alapértelmezés szerint a gyorsítótár-metrikák az Azure monitorban vannak [30 napig](../azure-monitor/platform/data-collection.md#metrics) majd törli. A gyorsítótár-metrikák 30 napnál hosszabb ideig megőrizni, kattintson a **diagnosztikai** való [konfigurálása a tárfiók](cache-how-to-monitor.md#export-cache-metrics) fogja tárolni a gyorsítótár-diagnosztikát.

>[!NOTE]
>A gyorsítótár-metrikák tárolóba archiválás, mellett is [azok streamelésére az Eseményközpontok felé, vagy küldje el azokat a Log Analytics](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
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
> Erőforrás állapota jelenleg nem lehet a jelentés az Azure Cache állapotát a virtuális hálózatban lévő üzemeltetett Redis-példány. További információkért lásd: [minden gyorsítótár-szolgáltatás a virtuális hálózat gyorsítótár üzemeltetése esetén működnek?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Új támogatási kérelem
Kattintson a **új támogatási kérelem** , nyisson egy támogatási kérelmet a gyorsítótár számára.





## <a name="default-redis-server-configuration"></a>Az alapértelmezett érték a Redis kiszolgáló konfigurációja
Új Azure Cache a Redis-példány a következő alapértelmezett Redis konfigurációs értékeket vannak konfigurálva:

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
| `maxmemory-policy` |`volatile-lru` |A Maxmemory házirend beállítás a hogyan Redis választja ki, mit és mikor `maxmemory` (a mérete a gyorsítótárban a gyorsítótár létrehozásakor kiválasztott ajánlat) elérésekor. Az alapértelmezett beállítás: az Azure redis Cache- `volatile-lru`, amely a kulcsok távolítja el, az elévülés LRU algoritmus használatával. Ez a beállítás konfigurálható az Azure Portalon. További információkért lásd: [memória házirendek](#memory-policies). |
| `maxmemory-samples` |3 |A memóriahasználat a LRU és a minimális TTL algoritmusok helyett pontos algoritmusok közelítő algoritmusok is. Alapértelmezés szerint a Redis-ellenőrzések három kulcsot, és kiválasztja azt, amelyik utoljára kevésbé használt. |
| `lua-time-limit` |5000 |Maximális végrehajtási ideje (MS) egy Lua-parancsprogram. Ha eléri a maximális végrehajtási időt, a Redis naplózza, hogy egy parancsfájl továbbra is a végrehajtási, a maximális engedélyezett idő után, és elindítja egy hiba miatt lekérdezések megválaszolására. |
| `lua-event-limit` |500 |Parancsfájl esemény üzenetsor maximális méretét. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 032mb, 8 MB méretű 60 0 |Az ügyfél kimeneti puffer korlátok használható leválasztásának az ügyfelek, amelyek a rendszer nem történő adatkiolvasás elég gyors a kiszolgáló valamilyen okból (gyakori oka az, hogy egy Pub/Sub-ügyfél nem tudja fogadni üzenetek a lehető leghamarabb a közzétevő hozhatnak, azokat) kényszerítése. Tovább információ: [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>maximális `databases` minden egyes Azure Cache redis tarifacsomag esetében eltérő, és a gyorsítótár létrehozásakor állítható. Ha nincs `databases` beállítás meg van adva gyorsítótár létrehozása során, az alapértelmezett érték 16.

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
  * Redis-fürttel kompatibilis – minden prémium gyorsítótárak a Redis-fürttel csak támogatja 0-adatbázis használatát tehát a `databases` korlátozása minden prémium szintű gyorsítótár engedélyezve van a Redis-fürttel hatékonyan értéke 1 és a [kiválasztása](https://redis.io/commands/select) parancs nem engedélyezett. További információkért lásd: [van a fürtszolgáltatás használandó ügyfélalkalmazásomnak semmilyen módosítást?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Adatbázisokkal kapcsolatos további információkért lásd: [Mik azok a Redis-adatbázisok?](cache-faq.md#what-are-redis-databases)

> [!NOTE]
> A `databases` beállítás lehet, csak a cache létrehozásakor konfigurált, és csak a PowerShell, a parancssori felület vagy a más felügyeleti ügyfeleket. Példa konfigurálása `databases` gyorsítótár létrehozása során PowerShell-lel, tekintse meg a [New-AzRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup> `maxclients` eltér az egyes Azure Cache redis tarifacsomag.

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



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Redis Cache nem támogatott az Azure Cache redis parancsok
> [!IMPORTANT]
> Konfigurálása és felügyelete Azure Cache Redis-példány a Microsoft által felügyelt, mert a következő parancsok le vannak tiltva. Ha megpróbálja meghívni őket, egy ehhez hasonló hibaüzenetet kap `"(error) ERR unknown command"`.
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

A Redis-parancsokkal kapcsolatos további információkért lásd: [ https://redis.io/commands ](https://redis.io/commands).

## <a name="redis-console"></a>Redis konzol
Az Azure Cache a Redis-példány használatával biztonságosan kiadhat parancsokat a **Redis konzol**, gyorsítótár minden szint esetében az Azure Portalon elérhető.

> [!IMPORTANT]
> - A Redis-konzol nem működik a [VNET](cache-how-to-premium-vnet.md). Ha a gyorsítótár része virtuális Hálózatnak, csak a virtuális hálózaton lévő ügyfelek hozzáférhet a gyorsítótárban. Redis konzol fut a helyi böngészőben, amely a virtuális hálózaton kívülről, mert azt nem lehet csatlakozni a gyorsítótár.
> - Nem minden Redis parancsok a Redis Azure Cache-ben támogatottak. Redis parancsok, amelyek le vannak tiltva az Azure Cache redis listáját lásd az előző [redis Cache a redis Azure Cache-ben nem támogatott parancsok](#redis-commands-not-supported-in-azure-cache-for-redis) szakaszban. A Redis-parancsokkal kapcsolatos további információkért lásd: [ https://redis.io/commands ](https://redis.io/commands).
> 
> 

A Redis-konzol eléréséhez kattintson **konzol** származó a **Azure Cache redis** panelen.

![Redis konzol](./media/cache-configure/redis-console-menu.png)

Adja ki a gyorsítótárpéldány irányuló parancsot, írja be a konzolba a kívánt parancsot.

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

![Helyezze át az Azure Cache redis](./media/cache-configure/redis-cache-move.png)

Erőforrások áthelyezése az egyik erőforráscsoportból egy másikba, és a egy előfizetésből egy másikba kapcsolatos információkért lásd: [erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>További lépések
* Redis parancsok való használatáról további információkért lásd: [hogyan futtathatok Redis parancsok?](cache-faq.md#how-can-i-run-redis-commands)

