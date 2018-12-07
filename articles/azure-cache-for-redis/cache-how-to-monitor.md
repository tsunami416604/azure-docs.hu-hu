---
title: Az Azure Cache redis figyelése |} A Microsoft Docs
description: Ismerje meg, hogyan állapotának és teljesítményének figyelése az Azure Cache a Redis-példány
services: azure-cache-for-redis
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: azure-cache-for-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 19eef82aeeddef166e9843f00d14282316a0fe34
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2018
ms.locfileid: "53019744"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>A Redis Azure Cache figyelése
Az Azure Cache Redis felhasználásra [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) biztosít a cache-példány figyelése számos lehetőség közül választhat. Meg is metrikákat tekinthet meg, rögzítheti a kezdőpulton mérőszámdiagramok, testre szabhatja a dátum- és időtartományt figyelési diagramok, hozzáadása és metrikák eltávolítása a diagramok és riasztásokat állíthat be adott feltételek teljesülése esetén. Ezek az eszközök lehetővé teszik az Azure Cache Redis-példány és a gyorsítótárazás alkalmazások kezeléséhez segítségért állapotának monitorozásához.

A Redis-példány az Azure Cache metrikákat gyűjt a Redis használatával [INFO](http://redis.io/commands/info) parancs körülbelül kétszer / perces, és automatikusan tárolt 30 napig (lásd: [gyorsítótár-metrikák exportálása](#export-cache-metrics) konfigurálása egy a különböző megőrzési házirend) ahhoz, hogy a mérőszámdiagramok jelenik meg, illetve riasztási szabályok szerint értékeli ki. Minden egyes gyorsítótár metrika használt különböző INFO értékek kapcsolatos további információkért lásd: [rendelkezésre álló metrikák és a jelentéskészítés időközök](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Gyorsítótár-metrikák megtekintése [Tallózás](cache-configure.md#configure-azure-cache-for-redis-settings) a cache-példányhoz a a [az Azure portal](https://portal.azure.com).  Az Azure Cache redis Ez a témakör néhány beépített diagramon a **áttekintése** panel és a **Redis-metrikák** panelen. Minden egyes diagram testre szabható hozzáadásával vagy eltávolításával a metrikák és a jelentési időköz módosítása.

![Redis-metrikák](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Előre konfigurált mérőszámok diagramok megtekintése

A **áttekintése** panelen találhatók a következő előre konfigurált figyelési diagramokat.

* [Figyelési diagramok](#monitoring-charts)
* [Használati diagramok](#usage-charts)

### <a name="monitoring-charts"></a>Figyelési diagramok
A **figyelés** című rész a **áttekintése** panel **találatok és tévesztések**, **lekérdezi és beállítja**, **kapcsolatok**, és **parancsok összesen** diagramok.

![Figyelési diagramok](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Használati diagramok
A **használati** című rész a **áttekintése** panel **Redis-kiszolgáló terhelése**, **memóriahasználat**, **hálózatisávszélesség**, és **CPU-használat** diagramok, és megjeleníti a **tarifacsomag** a cache-példányhoz.

![Használati diagramok](./media/cache-how-to-monitor/redis-cache-usage-part.png)

A **tarifacsomag** jeleníti meg a cache szolgáltatás díjszabási szint, és az alábbiakra használhatók [méretezési](cache-how-to-scale.md) a gyorsítótár egy másik tarifacsomagra.

## <a name="view-metrics-with-azure-monitor"></a>Az Azure monitor-metrikák megtekintése
Redis-metrikák megtekintése, és az Azure Monitor használatával egyéni diagramok létrehozásához kattintson a **metrikák** a a **erőforrás menüben**, és a kívánt, jelentési időköz, típusú és egyéb mérőszámok segítségével a diagram testreszabásához.

![Redis-metrikák](./media/cache-how-to-monitor/redis-cache-monitor.png)

Az Azure Monitor használatával metrikák további információkért lásd: [Microsoft Azure-ban mérőszámok áttekintése](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Gyorsítótár-metrikák exportálása
Alapértelmezés szerint a gyorsítótár-metrikák az Azure monitorban vannak [30 napig](../azure-monitor/platform/data-collection.md#metrics) majd törli. A gyorsítótár-metrikák 30 napnál hosszabb ideig megőrizni, akkor is [jelöl ki a storage-fiók](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) , és adja meg egy **megőrzése (nap)** házirendet a gyorsítótár-metrikák. 

A storage-fiókját a gyorsítótár-metrikák konfigurálása:

1. Kattintson a **diagnosztikai** származó a **erőforrás menüben** a a **Azure Cache redis** panelen.
2. Kattintson a **a**.
3. Ellenőrizze **archiválás tárfiókba**.
4. Válassza ki a tárfiókot, amely a gyorsítótár-metrikák tárolja.
5. Ellenőrizze a **1 perces** jelölőnégyzetet, és adja meg egy **megőrzés (nap)** házirend. Ha nem szeretne adatmegőrzési szabályt alkalmazni, és örökre megőrizni az adatokat, és állítsa **megőrzés (nap)** való **0**.
6. Kattintson a **Save** (Mentés) gombra.

![A redis-diagnosztika](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>A gyorsítótár-metrikák tárolóba archiválás, mellett is [azok streamelésére az Eseményközpontok felé, vagy küldje el azokat a Log Analytics](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md#retrieve-metric-values).
>
>

A metrikák eléréséhez, megtekintheti őket a korábban ismertetett Ez a cikk az Azure Portalon, és is elérheti azokat használja a [Azure Monitor-metrikák REST API](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md).

> [!NOTE]
> Ha módosítjuk a storage-fiókok, a korábban beállított storage-fiókban lévő adatok marad letölthető, de ez nem jelenik meg az Azure Portalon.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Rendelkezésre álló metrikák és a jelentéskészítés időközök
Gyorsítótár-metrikák használatával több jelentéskészítési időközök, beleértve a jelentett **elmúlt óra**, **Ma**, **elmúlt hét**, és **egyéni**. A **metrika** egyes metrikadiagram paneljén a diagram minden metrika az átlagos, minimális és maximális értékeket jeleníti meg, illetve bizonyos metrikák összesen megjelenítése a jelentéskészítési időtartam alatt. 

Mindegyik metrikát két verzióját tartalmazza. Egy metrika méri a teljesítményt, a teljes gyorsítótárat, és a gyorsítótárak esetében, amelyek használata [Fürtszolgáltatás](cache-how-to-premium-clustering.md), egy második verziója, amely tartalmazza a metrika `(Shard 0-9)` a neve a mértékek működést a egy szegmenst a gyorsítótárban. Például ha egy gyorsítótárában 4 szegmensek `Cache Hits` van a teljes gyorsítótárat, a találatok teljes mennyisége és `Cache Hits (Shard 3)` csak az adott szegmenst a gyorsítótár találatai van.

> [!NOTE]
> Még ha a gyorsítótár üresjáratban aktív ügyfél csatlakoztatott alkalmazások nélkül, néhány gyorsítótár tevékenységgel, például egy csatlakoztatott ügyfelek, a memóriahasználat és a végrehajtott műveletek jelenhet meg. Az Azure Cache Redis-példány működése során ezt a tevékenységet, normál.
> 
> 

| Metrika | Leírás |
| --- | --- |
| Gyorsítótárbeli találatok |A megadott jelentési időszak során sikeres kulcs keresések száma. Ez leképezhető `keyspace_hits` a redis [INFO](http://redis.io/commands/info) parancsot. |
| Gyorsítótár késés (előzetes verzió) | A késés, a gyorsítótár ki a csomópontok késés, a gyorsítótár alapján számítja ki. Ez a metrika mikroszekundumban mérik, és három dimenziója van: "Átlagos", "Minimum" és "Max", amelyek a gyorsítótár átlagos, minimális és maximális késés rendre tartalmazzák a megadott jelentési időszak során. |
| Gyorsítótárbeli tévesztések |A megadott jelentési időszak során sikertelen kulcs keresések száma. Ez leképezhető `keyspace_misses` a Redis INFO parancsát. Gyorsítótárbeli nem jelenti a gyorsítótár probléma van. Például a programozási gyorsítótár-feltöltési minta használatával, egy alkalmazás jelenik meg először egy elemet a gyorsítótárban. Ha az elem nem létezik (gyorsítótár-tévesztés), a cikk az adatbázisból lekért és a következő alkalomra hozzáadja a gyorsítótárhoz. Gyorsítótárbeli a programozási gyorsítótár-feltöltési minta normál viselkedése. Ha a gyorsítótár-tévesztések száma a vártnál magasabb, vizsgálja meg az alkalmazás logikáját, és beolvassa a gyorsítótárból tölti fel. Ha elemek ürülnek folyamatban van a gyorsítótár memóriaigény miatti kiürítése, majd előfordulhat, hogy néhány gyorsítótárbeli, de figyeli, hogy a rendelkezésre álló memória mennyisége jobb metrikát lenne `Used Memory` vagy `Evicted Keys`. |
| Gyorsítótár-olvasás |Adatok mennyisége (MB) a gyorsítótár / másodperc (MB/s) a megadott jelentési időszak során olvasni. Ez az érték a hálózati kártyák, amelyek támogatják a virtuális gép, amely üzemelteti a gyorsítótárban, és nem adott Redis származik. **Ez az érték megfelel a gyorsítótár által használt hálózati sávszélesség. Ha szeretné a kiszolgálóoldali hálózati sávszélesség korlátja riasztásokat állíthat be, majd hozza létre ezt a `Cache Read` számlálót. Lásd: [Ez a táblázat](cache-faq.md#cache-performance) tarifacsomagok és méretek különböző gyorsítótár megfigyelt sávszélesség korlátait.** |
| Gyorsítótár-írás |A gyorsítótár (MB) a megadott (MB/s) másodpercenként írt adatok mennyisége jelentési időköz. Ez az érték a hálózati kártyák, amelyek támogatják a virtuális gép, amely üzemelteti a gyorsítótárban, és nem adott Redis származik. Ez az érték megfelel a hálózati sávszélesség a gyorsítótárhoz az ügyfélről küldött adatok. |
| Csatlakoztatott ügyfélrendszerek |A megadott jelentési időszak során a gyorsítótárhoz ügyfélkapcsolatok száma. Ez leképezhető `connected_clients` a Redis INFO parancsát. Miután a [kapcsolathoz megadott korlátot](cache-configure.md#default-redis-server-configuration) van, a gyorsítótár lesz elérte későbbi csatlakozási kísérletekhez sikertelen lesz. Vegye figyelembe, hogy akkor sem, ha nincs aktív ügyfélalkalmazások, lehet, hogy van még néhány példányait kapcsolódó ügyfelek a belső folyamatok és kapcsolatok miatt lehet. |
| CPU |A CPU-kihasználtság az Azure Cache a Redis-kiszolgáló százalékos jelentés a megadott intervallumban. Ezt az értéket rendeli az operációs rendszer `\Processor(_Total)\% Processor Time` teljesítményadatait mutatja. |
| Hibák | Adott hibáiról és teljesítményproblémáiról, amely a gyorsítótár sikerült esetlegesen tapasztalt megadott jelentési időtartam alatt. Ez a metrika nyolc dimenzió különböző alkalmazáshiba-típusok jelölő rendelkezik, de sikerült több hozzáadta a jövőben. Most már jelölt alkalmazáshiba-típusok a következők: <br/><ul><li>**Feladatátvétel** – Ha a gyorsítótár átadja a feladatokat (fő elősegíti az alárendelt)</li><li>**Az összeomlási** – Ha a csomópontok egyikét a váratlanul összeomlik, a gyorsítótár</li><li>**Dataloss** – Ha a gyorsítótár dataloss</li><li>**UnresponsiveClients** – Ha az ügyfelek vannak nem adatok beolvasása a kiszolgálóról elég gyorsan</li><li>**AOF** – amikor az adatmegőrzés AOF kapcsolatos probléma</li><li>**RDB-fájlba való** – Ha RDB-fájlba való megőrzését kapcsolatos probléma</li><li>**Importálás** – amikor az importálás RDB kapcsolatos probléma</li><li>**Exportálás** – Ha RDB-fájlba való exportálása kapcsolatos probléma</li></ul> |
| Kizárt kulcsok |A gyorsítótárból oka az, hogy a megadott jelentési időszak során kizárt elemek száma a `maxmemory` korlátot. Ez leképezhető `evicted_keys` a Redis INFO parancsát. |
| Lejárt kulcsok |Azon elemek száma a megadott jelentési időszak során lejárt a gyorsítótárból. Ezt az értéket képez `expired_keys` a Redis INFO parancsát.|
| Lekérdezések |A megadott jelentési időszak során a gyorsítótárból a get műveletek száma. Az értéket nem egyezik meg a következő értékeket a Redis ADATAIT minden parancs: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, és `cmdstat_getrange`, és egyenértékű a gyorsítótár-találatok és tévesztések összege a jelentéskészítési intervallumban. |
| Műveletek száma másodpercenként | A megadott jelentési időszak során a gyorsítótár-kiszolgáló által másodpercenként feldolgozott parancsok teljes száma.  Ez az érték "instantaneous_ops_per_sec" parancsot a Redis INFO rendeli hozzá. |
| Redis-kiszolgáló terhelése |A százalékos aránya, amelyben a Redis-kiszolgáló elfoglalva, feldolgozási és nem vár a tétlen üzenetek ciklusokat. Ha ez a számláló eléri az azt jelenti, hogy a Redis-kiszolgáló elérte a teljesítményének felső határa, és a Processzor nem tudja feldolgozni a 100 bármely gyorsabban működnek. Ha nagy a Redis kiszolgáló terhelése, fog megjelenni az ügyfél időtúllépési kivételek jelennek meg. Ebben az esetben érdemes vertikális felskálázásával, vagy az adatok particionálása több gyorsítótárak be. |
| Beállítások |A megadott jelentési időszak során a gyorsítótárhoz set-műveletek száma. Az értéket nem egyezik meg a következő értékeket a Redis ADATAIT minden parancs: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` , és `cmdstat_setnx`. |
| Kulcsok összesen  | A korábbi jelentéskészítési időszakban a gyorsítótárban lévő kulcsok maximális száma. Ez leképezhető `keyspace` a Redis INFO parancsát. A fürtözés engedélyezve van, a gyorsítótárak esetében az alapul szolgáló metrikák rendszer kapcsolatos korlátozás miatt kulcsok összesen kulcsok, akinek a kulcsok maximális számát a jelentési időköze során a szegmensek maximális számát adja vissza.  |
| Műveletek összesen |A megadott jelentési időszak során a gyorsítótár-kiszolgáló által feldolgozott parancsok teljes száma. Ezt az értéket képez `total_commands_processed` a Redis INFO parancsát. Ne felejtse, hogy ha Azure Cache redis tisztán pub/sub történik hiba nincs metrikáit `Cache Hits`, `Cache Misses`, `Gets`, vagy `Sets`, viszont `Total Operations` , amely a gyorsítótár-pub/sub-műveletek használati metrikákat. |
| Felhasznált memória |A kulcs-érték párokat a gyorsítótárban (MB) a megadott jelentési időszak során használt gyorsítótár-memória mennyisége. Ezt az értéket képez `used_memory` a Redis INFO parancsát. Ez nem tartalmaz, metaadatait vagy töredezettsége. |
| Használt memória százalékos aránya | A megadott jelentési időszak során használt memória összesen %.  Ez a "used_memory" érték kiszámításához Redis INFO parancsát hivatkozik. |
| Használt memória RSS |A gyorsítótár felhasznált memóriamennyiség (MB) a megadott intervallumban jelentéskészítési, többek között a töredezettséget és azok metaadatait. Ezt az értéket képez `used_memory_rss` a Redis INFO parancsát. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Riasztások
A mérőszámok és tevékenységnaplók alapján beállíthatja, hogy milyen riasztásokat szeretne kapni. Az Azure Monitor segítségével konfigurálhat riasztásokat, amelyek aktiválása esetén a követező műveletek végrehajtására kerülhet sor:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

A gyorsítótár a riasztási szabályok konfigurálásához kattintson **riasztási szabályok** származó a **erőforrás menüben**.

![Figyelés](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Konfigurálásával és riasztások használatával kapcsolatos további információkért lásd: [áttekintése a riasztások](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Tevékenységnaplók
A Tevékenységnaplók betekintést az Azure Cache Redis-példány a tartományvezérlőn végrehajtott műveletek. Ez volt korábbi nevén "naplófájlok" vagy "operational-logs". Tevékenységnaplók segítségével megadhatja, hogy a "mit, ki, és mikor" írási műveletek (PUT, POST, DELETE) az Azure Cache Redis-példány számára készült. 

> [!NOTE]
> Tevékenységnaplók az olvasási (GET) műveleteket nem tartalmazzák.
>
>

A gyorsítótár tevékenységeket tartalmazó naplók megtekintéséhez kattintson **tevékenységeket tartalmazó naplók** származó a **erőforrás menüben**.

A Tevékenységnaplók kapcsolatos további információkért lásd: [áttekintése az Azure-tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











