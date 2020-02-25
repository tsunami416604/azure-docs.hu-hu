---
title: Az Azure Adatkezelő teljesítményének, az állapot & a metrikák használatának monitorozása
description: Ismerje meg, hogyan használhatja az Azure Adatkezelő mérőszámait a fürt teljesítményének, állapotának és használatának figyelésére.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2020
ms.locfileid: "77560304"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Az Azure Adatkezelő mérőszámai a fürterőforrások állapotára és teljesítményére vonatkozó kulcsfontosságú mutatókat biztosítanak. Az ebben a cikkben részletezett metrikák használatával az Azure Adatkezelő a fürt állapotát és teljesítményét az önálló mérőszámok alapján figyelheti meg az adott forgatókönyvben. A mérőszámokat az operatív [Azure-irányítópultok](/azure/azure-portal/azure-portal-dashboards) és az [Azure-riasztások](/azure/azure-monitor/platform/alerts-metric-overview)alapjául is használhatja.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/)is.
* Egy [fürt és egy adatbázis](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Metrikák használata

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).
1. Az Azure Adatkezelő-fürtben válassza a **metrikák** lehetőséget a metrikák ablaktábla megnyitásához és a fürt elemzésének megkezdéséhez.
    ![válassza a metrikák](media/using-metrics/select-metrics.png)lehetőséget.
1. A metrikák ablaktáblán: ![metrikák ablaktáblán](media/using-metrics/metrics-pane.png)
    1. Metrika diagram létrehozásához válassza a **metrika** neve és a megfelelő **Összesítés** /metrika elemet. Az **erőforrás** -és **metrikus névtér** -választókat előre ki kell választani az Azure adatkezelő-fürthöz. További információ a különböző metrikákkal kapcsolatban: [támogatott Azure adatkezelő mérőszámok](#supported-azure-data-explorer-metrics).
    1. Válassza a **metrika hozzáadása** lehetőséget, ha több mérőszámot szeretne megjeleníteni ugyanabban a diagramban.
    1. Válassza az **+ új diagram** lehetőséget, ha több diagramot szeretne látni egy nézetben.
    1. A Time Picker használatával módosíthatja az időtartományt (alapértelmezés: az elmúlt 24 óra).
    1. Használja a [ **szűrő hozzáadása** és a **felosztás alkalmazása** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) a dimenziókkal rendelkező mérőszámokhoz.
    1. Válassza a **rögzítés az irányítópulton** lehetőséget, hogy hozzáadja a diagram konfigurációját az irányítópultokhoz, hogy megtekintse azt újra.
    1. Az **új riasztási szabály** beállításával jelenítheti meg a mérőszámokat a set feltételek használatával. Az új riasztási szabály a diagramon a cél erőforrás, metrika, felosztás és szűrés dimenziókat fogja tartalmazni. Módosítsa ezeket a beállításokat a [riasztási szabály létrehozása ablaktáblán](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

További információ a [Metrikaböngésző](/azure/azure-monitor/platform/metrics-getting-started)használatáról.

## <a name="supported-azure-data-explorer-metrics"></a>Támogatott Azure Adatkezelő mérőszámok

A támogatott Azure Adatkezelő mérőszámok a használat alapján különböző kategóriákba vannak elkülönítve. 

### <a name="cluster-health-metrics"></a>Fürt állapotának mérőszámai

A fürt állapotának mérőszámai követik a fürt általános állapotát. Ez magában foglalja az erőforrás-és a betöltés kihasználtságát és a válaszadást.

**Metrika** | **Egység** | **Összesítés** | **Metrika leírása** | **Méretek** |
|---|---|---|---|---|
| Gyorsítótár kihasználtsága | Százalék | Avg, Max, Min | A fürt által jelenleg használt lefoglalt gyorsítótár-erőforrások százalékos aránya. A gyorsítótár a felhasználói tevékenység számára lefoglalt SSD mérete a megadott gyorsítótár-házirend szerint. A gyorsítótár átlagos kihasználtsága 80%-os vagy annál kevesebb a fürt fenntartható állapota. Ha az átlagos gyorsítótár kihasználtsága 80% fölé esik, a fürtöt egy tárterületre optimalizált díjszabási [szinten kell méretezni](manage-cluster-vertical-scaling.md) , vagy több példányra kell [méretezni](manage-cluster-horizontal-scaling.md) . Azt is megteheti, hogy a gyorsítótár-szabályzatot (kevesebb nap a gyorsítótárban) módosítja. Ha a gyorsítótár kihasználtsága meghaladja a 100%-ot, a gyorsítótárazási házirendnek megfelelően a gyorsítótárazott adat mérete nagyobb, mint a fürt gyorsítótárának teljes mérete. | Nincs |
| CPU | Százalék | Avg, Max, Min | A fürtben lévő gépek által jelenleg használt lefoglalt számítási erőforrások százalékos aránya. Egy fürt számára a 80%-os vagy annál kisebb átlagos CPU-érték fenntartható. A CPU maximális értéke 100%, ami azt jelenti, hogy nincs további számítási erőforrás az adatok feldolgozásához. Ha egy fürt nem jól teljesítő, ellenőrizze a CPU maximális értékét annak megállapításához, hogy vannak-e letiltott processzorok. | Nincs |
| Betöltés kihasználtsága | Százalék | Avg, Max, Min | A rendelkezésre álló erőforrások százalékos aránya a kapacitási szabályzatban a betöltés végrehajtásához felhasznált összes erőforrásból. Az alapértelmezett kapacitási szabályzat nem több mint 512 egyidejű betöltési művelet, vagy a betöltésbe fektetett fürterőforrások 75%-a. A betöltés átlagos kihasználtsága 80% vagy kevesebb a fürt fenntartható állapota. A betöltés kihasználtságának maximális értéke 100%, ami azt jelenti, hogy az összes fürtön lévő betöltési képesség használatos, és a betöltési várólista is eredményezhet. | Nincs |
| Életben tartása | Száma | AVG | Nyomon követi a fürt érzékenységét. A teljes mértékben válaszoló fürt 1 értéket ad vissza, a letiltott vagy leválasztott fürt pedig 0 értéket ad vissza. |
| A szabályozott parancsok teljes száma | Száma | AVG, Max, min, Sum | A fürtben lévő szabályozott (visszautasított) parancsok száma, mivel elérte az egyidejű (párhuzamos) parancsok maximálisan megengedett számát. | Nincs |
| Egységek teljes száma | Száma | AVG, Max, min, Sum | Az adategységek teljes száma a fürtben. Az ebben a metrikaban bekövetkező változások nagy adatszerkezetbeli változásokat és nagy terhelést okozhatnak a fürtben, mivel az adategységek összevonása egy CPU-nehéz tevékenység. | Nincs |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Állapot-és teljesítmény-mérőszámok exportálása

Az állapot-és teljesítmény-mérőszámok exportálásával nyomon követheti az exportálási műveletek általános állapotát és teljesítményét, például a késői állapotot, az eredményeket, a rekordok számát és a kihasználtságot.

**Metrika** | **Egység** | **Összesítés** | **Metrika leírása** | **Méretek** |
|---|---|---|---|---|
Exportált rekordok folyamatos exportálásának száma    | Száma | Összeg | Az exportált rekordok száma az összes folyamatos exportálási feladatban. | Nincs |
Folyamatos exportálás maximális késői |    Száma   | Max   | A fürt folyamatos exportálási feladatai által jelentett késői idő (percben). | Nincs |
Folyamatos exportálás függőben lévő darabszáma | Száma | Max   | A függőben lévő folyamatos exportálási feladatok száma. Ezek a feladatok készen állnak a futtatásra, de várólistára várnak, valószínűleg nem elegendő kapacitás miatt. 
Folyamatos exportálás eredménye    | Száma |   Száma   | Az egyes folyamatos exportálási futtatások hibája/sikerességi eredménye. | ContinuousExportName |
Exportálás kihasználtsága |    Százalék | Max   | Exportálási kapacitás, a fürt teljes exportálási kapacitásán kívül (0 és 100 között). | Nincs |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Betöltési állapot és teljesítmény mérőszámai

A betöltési állapot és a teljesítmény mérőszámai nyomon követik a betöltési műveletek általános állapotát és teljesítményét, például a késést, az eredményeket és a kötetet.

**Metrika** | **Egység** | **Összesítés** | **Metrika leírása** | **Méretek** |
|---|---|---|---|---|
| Feldolgozott események (Event/IoT hubok esetében) | Száma | Max., min. összeg | Az Event hubokból beolvasott és a fürt által feldolgozott események teljes száma. Az események az elutasított eseményekre oszlanak, és a fürt motorja által elfogadott eseményeket. | EventStatus |
| Betöltési késés | Másodperc | Avg, Max, Min | A betöltött adatok késése, az adatoknak a fürtön való kézhezvételének időpontjáig, amíg a lekérdezésre készen áll. A betöltési késés időtartama a betöltési forgatókönyvtől függ. | Nincs |
| Betöltés eredménye | Száma | Száma | A meghiúsult és sikeres betöltési műveletek teljes száma. A **felosztás alkalmazása** paranccsal hozhat létre sikereket, és nem végezheti el az eredményeket, és elemezheti a dimenziókat (**érték** > **állapot**).| IngestionResultDetails |
| Betöltési mennyiség (MB) | Száma | Max. összeg | A fürtbe betöltött adatmennyiség teljes mérete (MB) a tömörítés előtt. | Adatbázis |
| | | | |  

### <a name="query-performance"></a>Lekérdezési teljesítmény

A lekérdezési teljesítmény mérőszámai nyomon követhetik a lekérdezés időtartamát és az egyidejű vagy a szabályozott lekérdezések teljes számát.

**Metrika** | **Egység** | **Összesítés** | **Metrika leírása** | **Méretek** |
|---|---|---|---|---|
| Lekérdezés időtartama | Ezredmásodperc | AVG, min, Max, Sum | A lekérdezési eredmények fogadásának teljes ideje (nem tartalmazza a hálózati késést). | QueryStatus |
| Az egyidejű lekérdezések teljes száma | Száma | AVG, Max, min, Sum | A fürtben párhuzamosan futtatott lekérdezések száma. Ez a mérőszám jó módszer a fürt terhelésének becslésére. | Nincs |
| A szabályozott lekérdezések teljes száma | Száma | AVG, Max, min, Sum | A fürtben a szabályozott (visszautasított) lekérdezések száma. Az egyidejű (párhuzamos) lekérdezések megengedett maximális száma az egyidejű lekérdezési házirendben van meghatározva. | Nincs |
| | | | |

### <a name="streaming-ingest-metrics"></a>Adatfolyamok betöltésének mérőszámai

A streaming betöltési mérőszámok nyomon követik az adatfolyam-betöltési adatokat és a kérelmek sebességét, időtartamát és eredményeit.

**Metrika** | **Egység** | **Összesítés** | **Metrika leírása** | **Méretek** |
|---|---|---|---|---|
Adatforgalom adatátviteli sebessége |    Száma   | RateRequestsPerSecond | A fürtbe bevitt összes adatmennyiség. | Nincs |
Folyamatos átvitel időtartama   | Ezredmásodperc  | Avg, Max, Min | Az összes adatfolyam-betöltési kérelem teljes időtartama. | Nincs |
Folyamatos átviteli kérelmek gyakorisága   | Száma | Darabszám, átlag, Max, min, Sum | A folyamatos átviteli kérelmek teljes száma. | Nincs |
Folyamatos átvitel eredménye | Száma | AVG   | Az adatfolyam-betöltési kérelmek teljes száma eredményhalmaz szerint. | Eredmény |
| | | | |

További információ a [támogatott Azure adatkezelő-fürtök metrikáinak](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)használatáról.


## <a name="next-steps"></a>További lépések

* [Oktatóanyag: figyelési adatfeldolgozás és-lekérdezés az Azure Adatkezelő](/azure/data-explorer/ingest-data-no-code)
* [Azure Adatkezelő-betöltési műveletek figyelése diagnosztikai naplók használatával](/azure/data-explorer/using-diagnostic-logs)
* [Rövid útmutató: Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
