---
title: Az Azure-gyorsítótár figyelése a Redis számára
description: Ismerje meg, hogyan figyelheti az Azure-gyorsítótár állapotát és teljesítményét a Redis-példányok számára
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547489"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Az Azure-gyorsítótár figyelése a Redis számára

A Redis Azure Cache for Redis az [Azure Monitor](../azure-monitor/index.yml) használatával több lehetőséget biztosít a gyorsítótár-példányok figyelésére. Megtekintheti a mérőszámokat, mérőszámokat rögzíthet a Starttáblára, testreszabhatja a figyelési diagramok dátum- és időtartományát, metrikákat adhat hozzá és távolíthat el a diagramokból, és riasztásokat állíthat be bizonyos feltételek teljesülése esetén. Ezek az eszközök lehetővé teszik az Azure-gyorsítótár redis-példányok állapotának figyelését, és segítenek a gyorsítótárazási alkalmazások kezelésében.

Metrikák az Azure Cache redis példányok gyűjtött a Redis [INFO](https://redis.io/commands/info) parancs használatával körülbelül percenként kétszer, és automatikusan tárolja 30 napig (lásd: [Exportálási gyorsítótár metrikák](#export-cache-metrics) konfigurálásához egy másik adatmegőrzési szabály), így azok megjelennek a metrikák diagramok és riasztási szabályok szerint kiértékelhető. Az egyes gyorsítótár-mérőkhöz használt különböző INFO-értékekről az [Elérhető mutatók és jelentési intervallumok](#available-metrics-and-reporting-intervals)című témakörben talál további információt.

<a name="view-cache-metrics"></a>

A gyorsítótár-metrikák megtekintéséhez [keresse](cache-configure.md#configure-azure-cache-for-redis-settings) meg a gyorsítótár-példányt az [Azure Portalon.](https://portal.azure.com)  A Redis Azure Cache néhány beépített diagramot biztosít az **Áttekintés** panelen és a **Redis metrikák** panelen. Az egyes diagramok testre szabhatók a mérőszámok hozzáadásával vagy eltávolításával, valamint a jelentési időköz módosításával.

![Redis metrikák](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Előre konfigurált mérőszámdiagramok megtekintése

Az **Áttekintő** panel a következő előre konfigurált figyelési diagramokat rendelkezik.

* [Figyelési diagramok](#monitoring-charts)
* [Használati diagramok](#usage-charts)

### <a name="monitoring-charts"></a>Figyelési diagramok

**Az Áttekintő** panel **Figyelés** szakasza **találatokkal és kisasszonyokkal,** **lekérésekkel és halmazokkal,** **kapcsolatokkal**és **összes parancsdiagramokkal** rendelkezik.

![Figyelési diagramok](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Használati diagramok

Az **Áttekintés** panel **Használat** szakasza **Redis Server Load**, **Memóriahasználat**, **Hálózati sávszélesség**és **CPU-használati** diagramokkal rendelkezik, és megjeleníti a gyorsítótár-példány **díjszabási szintjét** is.

![Használati diagramok](./media/cache-how-to-monitor/redis-cache-usage-part.png)

A **tarifacsomag** megjeleníti a gyorsítótár tarifacsomag, és használható a gyorsítótár [méretezése](cache-how-to-scale.md) egy másik tarifacsomagra.

## <a name="view-metrics-with-azure-monitor"></a>Metrikák megtekintése az Azure-figyelővel

A Redis-metrikák megtekintéséhez és egyéni diagramok létrehozásához az Azure Monitor használatával kattintson az **Erőforrás menü** **Metrikák parancsára,** és szabja testre a diagramot a kívánt mérőszámokkal, jelentéskészítési időközzel, diagramtípussal és egyebek használatával.

![Redis metrikák](./media/cache-how-to-monitor/redis-cache-monitor.png)

Az Azure Monitor metrikákkal való használatáról a [Microsoft Azure metrikáinak áttekintése című témakörben](../monitoring-and-diagnostics/monitoring-overview-metrics.md)olvashat bővebben.

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Gyorsítótár-mutatók exportálása

Alapértelmezés szerint az Azure Monitor gyorsítótár-metrikák [30 napig tárolódnak,](../azure-monitor/platform/data-platform-metrics.md) majd törlődnek. A gyorsítótár-metrikák 30 napnál hosszabb ideig való megőrzéséhez [kijelölhet egy tárfiókot,](../azure-monitor/platform/archive-diagnostic-logs.md) és megadhat egy **megőrzési (nap)** házirendet a gyorsítótár-metrikákhoz. 

Tárfiók konfigurálása a gyorsítótár-metrikákhoz:

1. Az **Azure Cache for Redis (A Redis-gyorsítótár)** lapon a **Figyelés** fejlécalatt válassza a **Diagnosztika**lehetőséget.
2. Válassza **a + Diagnosztikai beállítás hozzáadása lehetőséget**.
3. Nevezze el a beállításokat.
4. Ellenőrizze **az Archívumot egy tárfiókba.** Normál adatátviteli díjakat számítunk fel a tártárhelyre és a tranzakciókért, amikor diagnosztikát küld egy tárfiókba.
4. Válassza **a Konfigurálás** lehetőséget, ha ki szeretné választani azt a tárfiókot, amelyben a gyorsítótár-metrikákat tárolni szeretné.
5. A **táblacímsor-mérőszám**alatt jelölje be a tárolni kívánt sorok (például **AllMetrics) melletti jelölőnégyzetet.** Adjon meg **megőrzési (nap)** házirendet. A megadható maximális megőrzési nap **365 nap**. Ha azonban a metrikák adatait örökre meg szeretné őrizni, állítsa **az Adatmegőrzés (nap)** **beállítását 0-ra.**
6. Kattintson a **Mentés** gombra.


![Redis diagnosztika](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>A gyorsítótár-metrikák tárolásba való archiválása mellett [streamelheti őket egy eseményközpontba is, vagy elküldheti őket az Azure Monitor naplóiba.](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values)
>

A metrikák eléréséhez megtekintheti őket az Azure Portalon a korábban ismertetett cikkben leírtak szerint, és az [Azure Monitor Metrics REST API használatával](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)is elérheti őket.

> [!NOTE]
> Ha módosítja a tárfiókokat, a korábban konfigurált tárfiókban lévő adatok letölthetők maradnak, de nem jelennek meg az Azure Portalon.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Elérhető mutatók és jelentési intervallumok

A gyorsítótár-mérőszámok több jelentési időköz használatával jelennek meg, például **az Elmúlt órában,** **a Ma,** **az Elmúlt héten**és **az Egyéni**. Az egyes metrikadiagramok **Metrikus** panelje a diagram egyes mutatóinak átlagát, minimális és maximális értékeit jeleníti meg, és egyes mutatók a jelentési intervallum összesítését jelenítik meg. 

Minden metrika két verziót tartalmaz. Egy metrika méri a teljes gyorsítótár teljesítményét, és a [fürtözést](cache-how-to-premium-clustering.md)használó gyorsítótárak esetében a metrika egy második verziója, amely a névben szerepel, `(Shard 0-9)` a gyorsítótár egyetlen szegmensének teljesítményét méri. Például ha egy gyorsítótár négy `Cache Hits` szilánkok, a teljes gyorsítótár lekéréseinek teljes száma, és `Cache Hits (Shard 3)` csak a lekéréseket, hogy a szegmens a gyorsítótár.

> [!NOTE]
> Még akkor is, ha a gyorsítótár tétlen, és nincs csatlakoztatott aktív ügyfélalkalmazás, előfordulhat, hogy bizonyos gyorsítótár-tevékenység, például a csatlakoztatott ügyfelek, a memóriahasználat és a végrehajtott műveletek. Ez a tevékenység normális egy Azure-gyorsítótár a Redis-példány működése során.
> 
> 

| Metrika | Leírás |
| --- | --- |
| Gyorsítótár-találatok |A megadott jelentési időköz sikeres kulcskereséseinek száma. Ez a `keyspace_hits` szám a Redis [INFO](https://redis.io/commands/info) parancsból származik. |
| Gyorsítótár késése (előzetes verzió) | A gyorsítótár késése a gyorsítótár internode késése alapján számítva. Ezt a metrikát mikroszekundumban mérik, és három dimenzióval rendelkezik: `Avg`, `Min`és `Max`, amelyek a gyorsítótár átlagos, minimális és maximális késését jelölik a megadott jelentési időköz ben. |
| Gyorsítótár-tévesztések |A megadott jelentési időköz alatt a sikertelen kulcskeresések száma. Ez a `keyspace_misses` szám a Redis INFO parancsból származik. A gyorsítótár-tévesztések nem feltétlenül jelentik azt, hogy probléma van a gyorsítótárral. Például a gyorsítótár-félre programozási minta használatakor egy alkalmazás először egy elem gyorsítótárában jelenik meg. Ha az elem nincs ott (gyorsítótár-tévesztés), az elem beolvasása az adatbázisból, és a következő alkalommal hozzáadódik a gyorsítótárhoz. A gyorsítótár-tévesztések a gyorsítótár-a-félreprogramozási minta normál viselkedése. Ha a gyorsítótár-tévesztések száma a vártnál nagyobb, vizsgálja meg a gyorsítótárból felpezsdítést és olvasást felugró és beolvasást tartalmazó alkalmazáslogikát. Ha az elemeket memórianyomás miatt kilakoltatják a gyorsítótárból, akkor előfordulhat, hogy a gyorsítótár `Used Memory` `Evicted Keys`elhibázza, de a memórianyomás figyelésének jobb mérőszáma a vagy a . |
| Gyorsítótár olvasása |A gyorsítótárból megabájt/másodpercben (MB/s) beolvasott adatok mennyisége a megadott jelentési időköz alatt. Ez az érték a gyorsítótárat tartalmazó virtuális gépet támogató hálózati csatolókártyákból származik, és nem Redis-specifikus. **Ez az érték megfelel a gyorsítótár által használt hálózati sávszélességnek. Ha a kiszolgálóoldali hálózati sávszélesség-korlátokra vonatkozó riasztásokat szeretne `Cache Read` beállítani, akkor hozza létre ezzel a számlálóval. Tekintse meg ezt a [táblázatot](cache-faq.md#cache-performance) a megfigyelt sávszélesség-korlátok a különböző gyorsítótár-tarifacsomagok és -méretek.** |
| Gyorsítótár írása |A gyorsítótárba másodpercenkénti (MB/s) írt adatok mennyisége a megadott jelentési időköz alatt. Ez az érték a gyorsítótárat tartalmazó virtuális gépet támogató hálózati csatolókártyákból származik, és nem Redis-specifikus. Ez az érték az ügyféltől a gyorsítótárba küldött adatok hálózati sávszélességének felel meg. |
| Csatlakozott ügyfelek |A gyorsítótárhoz a megadott jelentési időköz alatt létesített ügyfélkapcsolatok száma. Ez a `connected_clients` szám a Redis INFO parancsból származik. A [kapcsolati korlát](cache-configure.md#default-redis-server-configuration) elérése után a gyorsítótárhoz való későbbi kapcsolódási kísérletek sikertelenek lesznek. Még akkor is, ha nincsenek aktív ügyfélalkalmazások, előfordulhat, hogy a belső folyamatok és kapcsolatok miatt továbbra is előfordulhat, hogy a csatlakoztatott ügyfelek néhány példánya. |
| CPU |Az Azure Cache for Redis-kiszolgáló processzorkihasználtsága százalékban a megadott jelentéskészítési időközben. Ez az érték az `\Processor(_Total)\% Processor Time` operációs rendszer teljesítményszámlálójára van leképezve. |
| Hibák | A gyorsítótár által egy adott jelentési időköz alatt tapasztalt speciális hibák és teljesítményproblémák. Ez a metrika nyolc dimenzióval rendelkezik, amelyek különböző hibatípusokat jelölnek, de a jövőben több hozzáadott. A most képviselt hibatípusok a következők: <br/><ul><li>**Feladatátvétel** – ha egy gyorsítótár átadja a feladatátvételt (az alárendelt előléptetések főkiszolgálóra)</li><li>**Dataloss** – ha adatvesztés van a gyorsítótárban</li><li>**Nem válaszoló ügyfelek** – ha az ügyfelek nem olvasnak elég gyorsan adatokat a kiszolgálóról</li><li>**AOF** – ha az AOF perzisztenciával kapcsolatos probléma merül fel</li><li>**RDB** – ha az RDB perzisztenciával kapcsolatos probléma merül fel</li><li>**Importálás** – ha probléma merül fel az RDB importálásával kapcsolatban</li><li>**Exportálás** – ha probléma merül fel az RDB exportálásával kapcsolatban</li></ul> |
| Kilakoltatott kulcsok |A megadott jelentési időköz alatt a megadott jelentési `maxmemory` időköz ben kizárt elemek száma. Ez a `evicted_keys` szám a Redis INFO parancsból származik. |
| Lejárt kulcsok |A megadott jelentési időköz alatt a gyorsítótárból lejárt elemek száma. Ez az `expired_keys` érték a Redis INFO parancsból leképezi.|
| Kap |A gyorsítótárból a megadott jelentési időköz alatt beolvasási műveletek száma. Ez az érték a Redis INFO all parancs `cmdstat_get`következő `cmdstat_hget` `cmdstat_hgetall`értékeinek `cmdstat_mget` `cmdstat_getbit`összege: `cmdstat_getrange`, , `cmdstat_hmget`, , , és , és megegyezik a gyorsítótár-találatok és a jelentési időköz során lemaradt adatok összegével. |
| Műveletek másodpercenként | A gyorsítótár-kiszolgáló által a megadott jelentési időköz alatt másodpercenként feldolgozott parancsok száma.  Ez az érték a Redis INFO parancsból "instantaneous_ops_per_sec" lesz leképezve. |
| Redis kiszolgáló betöltése |Azon ciklusok százalékos aránya, amelyekben a Redis-kiszolgáló el van foglalva az üzenetek feldolgozásával, és nem várakozik tétlenül. Ha ez a számláló eléri a 100-at, az azt jelenti, hogy a Redis szerver elérte a teljesítményplafont, és a CPU nem tudja gyorsabban feldolgozni a munkát. Ha a Redis Server Load magas terhelést tapasztal, akkor időtúlterhelési kivételeket fog látni az ügyfélben. Ebben az esetben érdemes az adatok több gyorsítótárba való felskálázása vagy particionálása. |
| Halmazok |A megadott jelentési időköz alatt a gyorsítótárba beállított műveletek száma. Ez az érték a Redis INFO all parancs `cmdstat_set`következő `cmdstat_hset` `cmdstat_hmset`értékeinek összege: `cmdstat_setex`, `cmdstat_setrange` `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, , , , , , , és `cmdstat_setnx`. |
| Összes kulcs  | A gyorsítótárban lévő kulcsok maximális száma az elmúlt jelentéskészítési időszakban. Ez a `keyspace` szám a Redis INFO parancsból származik. Az alapul szolgáló metrikarendszer korlátozása miatt a fürtözést engedélyező gyorsítótárak esetében a Total Keys a shard azon maximális kulcsainak maximális számát adja vissza, amelyek a jelentési időköz alatt a kulcsok maximális számával voltak el.  |
| Összes művelet |A gyorsítótár-kiszolgáló által a megadott jelentési időköz alatt feldolgozott parancsok teljes száma. Ez az `total_commands_processed` érték a Redis INFO parancsból leképezi. Ha az Azure Cache for Redis kizárólag pub/sub, nem `Cache Hits` `Cache Misses`lesz `Gets`metrikák , vagy `Sets`, de lesznek `Total Operations` metrikák, amelyek tükrözik a gyorsítótár használatát pub/sub műveletek. |
| Használt memória |A megadott jelentési időköz alatt a gyorsítótárban lévő kulcs-/értékpárok gyorsítótár-memóriájának mennyisége MB-ban. Ez az `used_memory` érték a Redis INFO parancsból leképezi. Ez az érték nem tartalmazza a metaadatokat vagy a töredezettséget. |
| Használt memória százaléka | A megadott jelentési időköz alatt használt teljes memória %-a.  Ez az érték `used_memory` a Redis INFO parancs ban szereplő értéket a százalékos érték kiszámításához. |
| Használt memória RSS |Az MB-ban a megadott jelentési időköz alatt használt gyorsítótármemória mennyisége, beleértve a töredezettséget és a metaadatokat is. Ez az `used_memory_rss` érték a Redis INFO parancsból leképezi. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Riasztások

A mérőszámok és tevékenységnaplók alapján beállíthatja, hogy milyen riasztásokat szeretne kapni. Az Azure Monitor segítségével konfigurálhat riasztásokat, amelyek aktiválása esetén a követező műveletek végrehajtására kerülhet sor:

* E-mail-értesítés küldése
* Webhook meghívása
* Egy Azure Logic App-alkalmazás meghívása

A gyorsítótár riasztási szabályainak beállításához kattintson az Erőforrás menü **Riasztási szabályok** **parancsára.**

![Figyelés](./media/cache-how-to-monitor/redis-cache-monitoring.png)

A riasztások konfigurálásáról és használatáról a [Riasztások áttekintése című témakörben olvashat bővebben.](../monitoring-and-diagnostics/insights-alerts-portal.md)

## <a name="activity-logs"></a>Tevékenységnaplók
A tevékenységnaplók betekintést nyújtanak az Azure-gyorsítótárban a Redis-példányokhoz végrehajtott műveletekbe. Korábban "naplónaplók" vagy "működési naplók" néven ismerték. A tevékenységnaplók használatával meghatározhatja a "mit, ki és mikor" az Azure-gyorsítótárban redis-példányok hoz létre. 

> [!NOTE]
> A tevékenységnaplók nem tartalmaznak olvasási (GET) műveleteket.
>

A gyorsítótár tevékenységnaplóinak megtekintéséhez kattintson az **Erőforrás menü** **Tevékenységnaplók parancsára.**

A tevékenységnaplókról az [Azure-tevékenységnapló áttekintése](../azure-monitor/platform/platform-logs-overview.md)című témakörben olvashat bővebben.
