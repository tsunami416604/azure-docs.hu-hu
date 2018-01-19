---
title: "Azure Redis Cache figyelése |} Microsoft Docs"
description: "Ismerje meg, állapotának és teljesítményének figyelése az Azure Redis Cache példányt"
services: redis-cache
documentationcenter: 
author: wesmc7777
manager: cfowler
editor: 
ms.assetid: 7e70b153-9c87-4290-85af-2228f31df118
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: wesmc
ms.openlocfilehash: 3a68435866e6fb5bf0210144e53918c35b416449
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="how-to-monitor-azure-redis-cache"></a>Az Azure Redis Cache figyelése
Használja az Azure Redis Cache [Azure figyelő](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) arra, hogy a gyorsítótár-példányok számos lehetőség közül választhat. Is megtekinthető metrikákat, rögzítheti a kezdőpulton metrikák diagramok, testre szabhatja a dátum és idő tartomány diagramok figyelési, hozzáadása és metrikák eltávolítása a diagramok, és állítson be riasztásokat, bizonyos feltételek teljesülése esetén. Ezek az eszközök lehetővé teszik az Azure Redis Cache példány állapotának figyelésére, és a gyorsítótárazási alkalmazásokat kezeléséhez nyújt segítséget.

Az Azure Redis Cache példány metrikákat a Redis gyűjtése történt [INFO](http://redis.io/commands/info) parancs körülbelül kétszer / perc és automatikusan tárolt 30 napig (lásd: [gyorsítótár metrikák exportálása](#export-cache-metrics) egy másik adatmegőrzési konfigurálása), hogy a metrikák diagramok jelenik meg, és a riasztási szabályok szerint értékeli ki. Minden egyes gyorsítótár metrika különböző INFO értékeket kapcsolatos további információkért lásd: [elérhető és a jelentéskészítés intervallumok](#available-metrics-and-reporting-intervals).

<a name="view-cache-metrics"></a>

Gyorsítótár metrikák megtekintéséhez [Tallózás](cache-configure.md#configure-redis-cache-settings) a gyorsítótár-példányhoz, illetve a [Azure-portálon](https://portal.azure.com).  Azure Redis Cache Ez a témakör néhány beépített diagramok a **áttekintése** panel és a **metrikák Redis** panelen. Minden egyes diagram hozzáadásával vagy eltávolításával metrikák és a jelentési időköz módosítása testreszabhatók.

![Redis-metrikák](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Előre konfigurált metrikák diagramok megtekintése

A **áttekintése** panel rendelkezik a következő előre konfigurált diagramok figyelését.

* [Figyelési diagramok](#monitoring-charts)
* [Használati diagramok](#usage-charts)

### <a name="monitoring-charts"></a>Figyelési diagramok
A **figyelés** szakasz a **áttekintése** panel **illetve sikertelen**, **lekérdezi és beállítja a**, **kapcsolatok**, és **teljes parancsok** diagramokat.

![Figyelési diagramok](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Használati diagramok
A **használati** szakasz a **áttekintése** panel **kiszolgálóterhelés Redis**, **memóriahasználat**, **hálózati sávszélesség**, és **CPU-használat** diagramokat, és megjeleníti a **tarifacsomag** gyorsítótár-példány.

![Használati diagramok](./media/cache-how-to-monitor/redis-cache-usage-part.png)

A **tarifacsomag** jeleníti meg a gyorsítótár árképzési szintjüket, és a következőkre használható [méretezési](cache-how-to-scale.md) a gyorsítótár egy másik tarifacsomagra.

## <a name="view-metrics-with-azure-monitor"></a>Az Azure-figyelő nézet metrikák
Redis metrikák megtekintése, és hozzon létre egyéni diagramok Azure-figyelővel, kattintson a **metrikák** a a **erőforrás menü**, és szabja testre a diagram a kívánt mérőszámainkat, jelentéskészítés időköz, a diagram típusát, valamint több.

![Redis-metrikák](./media/cache-how-to-monitor/redis-cache-monitor.png)

Azure-figyelővel metrikák használatával kapcsolatos további információkért lásd: [áttekintése a Microsoft Azure-ban mérőszámok](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Gyorsítótár metrikák exportálása
Gyorsítótár-metrikát a Azure figyelő alapesetben [30 napig tárolja a](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md#store-and-archive) , majd törölni. A 30 napnál hosszabb gyorsítótár metrikáját megőrizni, akkor [tárfiók kijelölése](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md) , és adja meg egy **megőrzési (nap)** a gyorsítótár metrikáihoz házirend. 

A tárfiók a gyorsítótár metrikáihoz konfigurálása:

1. Kattintson a **diagnosztika** a a **erőforrás menü** a a **Redis Cache** panelen.
2. Kattintson a **a**.
3. Ellenőrizze **tárfiókba archív**.
4. Válassza ki a tárfiókot, a gyorsítótár metrikák tárolására.
5. Ellenőrizze a **1 perc** jelölőnégyzetet, és adja meg egy **megőrzés (nap)** házirend. Ha nem szeretné, hogy a bármely megőrzési házirend alkalmazása és adatainak megőrzése tartja meg **megőrzés (nap)** való **0**.
6. Kattintson a **Save** (Mentés) gombra.

![Redis diagnosztika](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Mellett a gyorsítótár metrikákat Storage archiválás, is [adatfolyamként küldje el azokat az Eseményközpontok felé, vagy küldje el a Naplóelemzési](../monitoring-and-diagnostics/monitoring-overview-metrics.md#export-metrics).
>
>

A metrikák eléréséhez a jelen cikkben korábban ismertetett Azure-portálon megtekintheti, és is elérheti őket használatával a [Azure figyelő metrikák REST API](../monitoring-and-diagnostics/monitoring-overview-metrics.md#access-metrics-via-the-rest-api).

> [!NOTE]
> Storage-fiókok módosítása esetén a korábban konfigurált tárfiókban lévő adatokat továbbra is elérhető letöltésre, de azt nem az Azure-portálon jelenik meg.  
> 
> 

## <a name="available-metrics-and-reporting-intervals"></a>Elérhető és a jelentéskészítés időközök
Gyorsítótár metrikák használatával több jelentéskészítési időközök, beleértve a jelentett **óránként túlra**, **Ma**, **elmúlt hét**, és **egyéni**. A **metrika** minden metrikák diagram csempe megjeleníti minden metrika az átlagos, minimális és maximális értékek a diagramon, és néhány metrikák összesen megjeleníti a jelentési időtartam alatt. 

Mindegyik metrikát két verzióját tartalmazza. Még egy metrika méri a teljesítményt a teljes gyorsítótárat, valamint a gyorsítótárak használó [Fürtszolgáltatás](cache-how-to-premium-clustering.md), egy második verziója, amely tartalmazza a metrika `(Shard 0-9)` a neve mértékek a működést a gyorsítótárban egy egyetlen szilánkcímtárban. Például ha a gyorsítótár tartalmaz 4 szilánkok `Cache Hits` mekkora a teljes gyorsítótár, a találatok és `Cache Hits (Shard 3)` csak az, hogy a gyorsítótár shard találatok van.

> [!NOTE]
> Akkor is, ha a gyorsítótár üresjáratban a csatlakoztatott aktív ügyfél alkalmazások nélkül, néhány gyorsítótár tevékenységgel, például egy csatlakoztatott ügyfelek, a memóriafelhasználásról és a végrehajtott műveletek jelenhet meg. Az Azure Redis Cache példány művelet során ez a tevékenység nem jelent hibás működést.
> 
> 

| Metrika | Leírás |
| --- | --- |
| Gyorsítótárbeli találatok |A megadott jelentési időszak során sikeres kulcs keresések száma. Ez hozzárendeli `keyspace_hits` a Redis a [INFO](http://redis.io/commands/info) parancsot. |
| Gyorsítótárbeli tévesztések |A megadott jelentési időszak során nem sikerült kulcs keresések száma. Ez hozzárendeli `keyspace_misses` a Redis INFO utasításból. Gyorsítótárbeli nem feltétlenül jelenti azt, a gyorsítótár probléma van. Például, ha a gyorsítótár-tartalékoljon programozási mintát használ, egy alkalmazás megvizsgálja első elem gyorsítótárában. Ha az elem nem létezik (gyorsítótár-tévesztései), a cikk az adatbázisból beolvasott és gyorsítótárában helyezte el a következő alkalommal. Gyorsítótárbeli a gyorsítótár-tartalékoljon programozási minta ezek normál viselkedése. Ha gyorsítótárbeli száma nagyobb, mint várható, vizsgálja meg az alkalmazás logika, amely feltölti és beolvassa a gyorsítótárból. Ha elemek vannak eltávolítandó memóriaprobléma miatt a gyorsítótárból ezt követően előfordulhat, hogy néhány gyorsítótárbeli, de Memóriaterhelést figyeléséhez jobb metrika lenne `Used Memory` vagy `Evicted Keys`. |
| Csatlakoztatott ügyfélszámítógépek |A megadott jelentéskészítési időszakban a gyorsítótárba ügyfélkapcsolatok számát. Ez hozzárendeli `connected_clients` a Redis INFO utasításból. Egyszer a [kapcsolathoz megadott korlátot](cache-configure.md#default-redis-server-configuration) van a gyorsítótár fog a elérte ezt követő csatlakozási kísérletek sikertelenek. Előfordulhat, hogy akkor is, ha nincs aktív ügyfél alkalmazás van, nincs még néhány példányai kapcsolódó ügyfelek a belső folyamatok és kapcsolatok miatt. |
| Kizárt kulcsok |Az oka az, hogy a megadott jelentéskészítési időszakban a gyorsítótárból kizárt elemek száma a `maxmemory` korlátot. Ez hozzárendeli `evicted_keys` a Redis INFO utasításból. |
| Lejárt kulcsok |Az elemek száma a megadott jelentési időszak során lejárt a gyorsítótárból. Ez az érték van leképezve `expired_keys` a Redis INFO utasításból. |
| Kulcsok összesen  | A gyorsítótárban a korábbi jelentéskészítési időszakban kulcsok maximális száma. Ez hozzárendeli `keyspace` a Redis INFO utasításból. Engedélyezve van, fürtözési gyorsítótárak esetében az alapul szolgáló metrikák rendszert korlátozása miatt az összes kulcs a shard, amelyekről a kulcsok maximális számát a jelentési időszak során a kulcsok maximális számát adja vissza.  |
| Lekérdezések |A megadott jelentéskészítési időszakban a gyorsítótárból get műveletek száma. Az értéke, a sum, a következő értékek a Redis-információból minden parancs: `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit`, és `cmdstat_getrange`, és a jelentési időszak során a találatot eredményező gyorsítótárbeli kereséseinek és tévesztések száma megegyezik. |
| Redis-kiszolgáló terhelése |A ciklus, amelyben a Redis-kiszolgáló túlterhelt dolgoz fel, és nem vár a tétlen üzenetek százalékos aránya Ha ez a számláló azt jelenti, hogy a Redis-kiszolgáló elérte a teljesítményének felső határát, ezért nem dolgozható fel a CPU 100 eléri a gyorsabban működnek. Ha nagy a Redis kiszolgálóterhelés azért jelent meg, akkor jelenik meg az ügyfél időtúllépési kivétel. Ebben az esetben érdemes vertikális felskálázásával vagy az adatok particionálása be több gyorsítótárában. |
| Beállítások |A megadott jelentéskészítési időszakban a gyorsítótárba set műveletek száma. Az értéke, a sum, a következő értékek a Redis-információból minden parancs: `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange`, és `cmdstat_setnx`. |
| Műveletek összesen |A megadott jelentéskészítési időszakban a gyorsítótár-kiszolgáló által feldolgozott parancsok száma. Ez az érték van leképezve `total_commands_processed` a Redis INFO utasításból. Pub/sub kizárólag az Azure Redis Cache használata esetén nem lesz metrikák hiányában a `Cache Hits`, `Cache Misses`, `Gets`, vagy `Sets`, de nem lesznek `Total Operations` tükrözze pub/sub műveletekhez a gyorsítótár-használati metrikák. |
| Felhasznált memória |A kulcs/érték párok MB-ban a gyorsítótárban a megadott jelentési időszak során használt a gyorsítótár-memória mennyisége. Ez az érték van leképezve `used_memory` a Redis INFO utasításból. Metaadatok vagy töredezettséget nem tartalmaz. |
| Használt memória RSS |Beleértve a töredezettséget és a metaadatok megadott jelentéskészítési időszakban MB-ban használt a gyorsítótár-memória mennyisége. Ez az érték van leképezve `used_memory_rss` a Redis INFO utasításból. |
| CPU |A CPU-felhasználást az Azure Redis Cache kiszolgáló százalékos a megadott jelentési időszak során. Ezt az értéket rendeli az operációs rendszer `\Processor(_Total)\% Processor Time` teljesítményszámláló. |
| Gyorsítótár-olvasás |Adatok mennyisége gyorsítótárából olvasta be a megabájt / másodperc (MB/s) a megadott jelentési időszak során. Ezt az értéket a hálózati kártyák, amely támogatja a virtuális gépet, amely üzemelteti a gyorsítótárban, és nem adott Redis származik. **Ennek az értéknek felel meg a gyorsítótár által használt hálózati sávszélesség. Ha azt szeretné, a kiszolgáló oldalán hálózati sávszélesség korlátja riasztások beállítása, majd létre kell hoznia azt ez `Cache Read` számláló. Lásd: [ezt a táblázatot](cache-faq.md#cache-performance) különböző árképzési szinteket, és méretű gyorsítótár megfigyelt sávszélesség korlátait.** |
| Gyorsítótár-írás |A gyorsítótár mérete (MB) a megadott (MB/s) másodpercenként írt adatok mennyiségét jelentési időköz. Ezt az értéket a hálózati kártyák, amely támogatja a virtuális gépet, amely üzemelteti a gyorsítótárban, és nem adott Redis származik. Ezt az értéket a hálózati sávszélesség, a gyorsítótár az ügyfél küldött adatok felel meg. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Riasztások
A riasztások metrikák és tevékenység naplók alapján konfigurálhatja. Azure a figyelő riasztást tegye a következőket, amikor elindítja a konfigurálását teszi lehetővé:

* E-mail-értesítés küldése
* A webhook hívása
* Egy Azure logikai alkalmazás meghívása

A riasztási szabályok a gyorsítótárhoz konfigurálásához kattintson **riasztási szabályok** a a **erőforrás menü**.

![Figyelés](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Konfigurálásával és riasztások használatával kapcsolatos további információkért lásd: [áttekintése a riasztások](../monitoring-and-diagnostics/insights-alerts-portal.md).

## <a name="activity-logs"></a>Tevékenységnaplók
Tevékenységi naplóit adja meg az Azure Redis Cache példányt a végrehajtott műveletek betekintést. Azt korábban hívták "naplófájlok" vagy "működési logs". Tevékenység-naplók segítségével meghatározhatja a "mi, ki, és mikor" az összes írási műveleteket (PUT, POST, Törlés), az Azure Redis Cache példány végzett. 

> [!NOTE]
> Tevékenység naplói nem tartalmazzák (GET) olvasási műveletek.
>
>

A gyorsítótár tevékenységi naplóit megtekintéséhez kattintson **tevékenységi naplóit** a a **erőforrás menü**.

Tevékenységi naplóit kapcsolatos további információkért lásd: [áttekintése Azure tevékenységnapló](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).











