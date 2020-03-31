---
title: Az Azure Data Explorer teljesítményének, állapotának & használatának monitorozása mérőszámokkal
description: Ismerje meg, hogyan használhatja az Azure Data Explorer metrikák a fürt teljesítményének, állapotának és használatának figyeléséhez.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/19/2020
ms.openlocfilehash: 1319b8cd6ac8a0eb83381c24bcde9996458e47a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560304"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Az Azure Data Explorer teljesítményének, állapotának és használatának figyelése mérőszámokkal

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához először egy fürtöt hozunk létre, majd egy vagy több adatbázist a fürtben. Ezután adatokat töltünk be az adatbázisba, hogy lekérdezéseket futtathassunk rajta. Az Azure Data Explorer metrikák a fürterőforrások állapotát és teljesítményét mutató kulcsfontosságú mutatókat tartalmaznak. A metrikák, amelyek ebben a cikkben részletesen az Azure Data Explorer fürt állapotát és teljesítményét az adott forgatókönyvben önálló metrikákként. Az [azure-irányítópultok](/azure/azure-portal/azure-portal-dashboards) és az [Azure-riasztások](/azure/azure-monitor/platform/alerts-metric-overview)alapjául metrikákat is használhat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik ilyen, létrehozhat egy [ingyenes Azure-fiókot.](https://azure.microsoft.com/free/)
* Fürt [és adatbázis](create-cluster-database-portal.md).

## <a name="using-metrics"></a>Metrikák használata

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. Az Azure Data Explorer-fürtben válassza **a Metrikák** lehetőséget a metrikák ablaktáblájának megnyitásához és a fürt elemzésének megkezdéséhez.
    ![Válassza a](media/using-metrics/select-metrics.png)Metrikák lehetőséget.
1. A Metrikák ![ablaktáblában: Metrikák ablaktábla](media/using-metrics/metrics-pane.png)
    1. Metrikadiagram létrehozásához válassza a **Metrika** nevét és a megfelelő **aggregációt** metrikaszerint. Az **erőforrás-** és **metrikanévtér-választók** előre ki vannak választva az Azure Data Explorer-fürthöz. A különböző metrikákról további információt az [Azure Data Explorer támogatott metrikái című témakörben talál.](#supported-azure-data-explorer-metrics)
    1. Válassza **a Metrika hozzáadása** lehetőséget, ha több mérőszámot szeretne ábrázolni ugyanabban a diagramban.
    1. Kijelölés **+ Új diagram,** ha több diagramot szeretne látni egy nézetben.
    1. Az időválasztó val módosíthatja az időtartományt (alapértelmezett: elmúlt 24 óra).
    1. Használja [ **a Szűrő hozzáadása** és felosztás **alkalmazása** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dimenziókkal rendelkező metrikákhoz.
    1. Válassza **a Rögzítés az irányítópultra** lehetőséget, ha hozzá szeretné adni a diagram konfigurációját az irányítópultokhoz, hogy újra megtekinthesse.
    1. Állítsa be **az Új riasztási szabályt** a metrikák megjelenítéséhez a megadott feltételek használatával. Az új riasztási szabály tartalmazza a célerőforrást, a metrikát, a felosztást és a szűrődimenziókat a diagramról. Módosítsa ezeket a beállításokat a [riasztási szabály létrehozásának ablaktábláján.](/azure/azure-monitor/platform/metrics-charts#create-alert-rules)

További információ a [Metrikakezelő](/azure/azure-monitor/platform/metrics-getting-started)használatáról.

## <a name="supported-azure-data-explorer-metrics"></a>Támogatott Azure Data Explorer-mérőszámok

A támogatott Azure Data Explorer metrikák vannak elosztva a használat különböző kategóriákba. 

### <a name="cluster-health-metrics"></a>Fürt állapotmutatói

A fürt állapotmetrikák nyomon követi a fürt általános állapotát. Ez magában foglalja az erőforrás- és a betöltési kihasználtságot és a válaszidőt.

**Metrika** | **Egység** | **Összesítés** | **Metrikus leírás** | **Méretek** |
|---|---|---|---|---|
| Gyorsítótár kihasználtsága | Százalék | Átlagos, Max, Min | A fürt által jelenleg használt lefoglalt gyorsítótár-erőforrások százalékos aránya. A gyorsítótár a felhasználói tevékenységhez a megadott gyorsítótár-házirendnek megfelelően lefoglalt SSD mérete. A gyorsítótár átlagos kihasználtsága 80%, vagy kevesebb, egy fürt fenntartható állapota. Ha az átlagos gyorsítótár-kihasználtság meghaladja a 80%-ot, a fürtöt egy tárolóra optimalizált tarifaszintre kell [skálázni,](manage-cluster-vertical-scaling.md) vagy több példányra [kell skálázni.](manage-cluster-horizontal-scaling.md) Másik lehetőségként igazítsa a gyorsítótár-házirendet (kevesebb nap a gyorsítótárban). Ha a gyorsítótár kihasználtsága több mint 100%, a gyorsítótárazási házirend nek megfelelően gyorsítótárazandó adatok mérete nagyobb, mint a fürt gyorsítótárának teljes mérete. | None |
| CPU | Százalék | Átlagos, Max, Min | A fürtben lévő gépek által jelenleg használt lefoglalt számítási erőforrások százalékos aránya. Egy átlagos, leg80%-os vagy annál kisebb processzor fenntartható egy fürt számára. A processzor maximális értéke 100%, ami azt jelenti, hogy nincsenek további számítási erőforrások az adatok feldolgozásához. Ha egy fürt nem teljesít jól, ellenőrizze a processzor maximális értékét, és állapítsa meg, hogy vannak-e blokkolva bizonyos processzorok. | None |
| Lenyelés kihasználtsága | Százalék | Átlagos, Max, Min | A tényleges erőforrások százalékos aránya a kapacitásházirendben a töltés hez lefoglalt összes erőforrás adatainak betöltéséhez. Az alapértelmezett kapacitásházirend legfeljebb 512 egyidejű betöltési művelet, vagy a betöltésbe fektetett fürterőforrások 75%-a. A 80%-os vagy annál kisebb átlagos lenyelési kihasználtság a fürt fenntartható állapota. A lenyelés maximális értéke 100%, ami azt jelenti, hogy az összes fürtbetöltési képesség használatos, és egy betöltési várólista eredményezhet. | None |
| Életben maradni | Darabszám | Átlag | Nyomon követi a fürt válaszképességét. A teljesen reszponzív fürt értéke 1, a blokkolt vagy leválasztott fürt pedig 0 értéket ad vissza. |
| A szabályozott parancsok száma összesen | Darabszám | Átlag, Max, Min, Összeg | A fürtben lévő szabályozott (elutasított) parancsok száma, mivel elérte az egyidejű (párhuzamos) parancsok maximális antropogását. | None |
| A terjedések száma összesen | Darabszám | Átlag, Max, Min, Összeg | A fürtadat-terjedelmeinek teljes száma. Ebben a metrikában végrehajtott változások azt eredményezheti, hogy az adatszerkezet masszív változásai és a fürt nagy terhelése, mivel az adatok egyesítése egy CPU-nehéz tevékenység. | None |
| | | | |

### <a name="export-health-and-performance-metrics"></a>Állapot- és teljesítménymutatók exportálása

Az exportálási állapot- és teljesítménymutatók nyomon követik az olyan exportműveletek általános állapotát és teljesítményét, mint a késés, az eredmények, a rekordok száma és a kihasználtság.

**Metrika** | **Egység** | **Összesítés** | **Metrikus leírás** | **Méretek** |
|---|---|---|---|---|
Az exportált rekordok folyamatos exportálási száma    | Darabszám | Összeg | Az exportált rekordok száma az összes folyamatos exportálási feladatban. | None |
Folyamatos kiviteli késés |    Darabszám   | Max   | A fürt folyamatos exportálási feladatai által jelentett késés (percben). | None |
Folyamatos exportálás függőben lévő száma | Darabszám | Max   | A függőben lévő folyamatos exportálási feladatok száma. Ezek a feladatok készen állnak a futtatásra, de várólistán várakoznak, valószínűleg az elégtelen kapacitás miatt). 
Folyamatos exportáláseredménye    | Darabszám |   Darabszám   | Az egyes folyamatos exportálási futtatások sikertelen/sikeres eredménye. | ContinuousExportName |
Exportálás kihasználtsága |    Százalék | Max   | A felhasznált exportkapacitás a fürt teljes exportkapacitásából (0 és 100 között). | None |
| | | | |

### <a name="ingestion-health-and-performance-metrics"></a>Betöltési állapot és teljesítménymutatók

A betöltési állapot és a teljesítménymérők nyomon követik a betöltési műveletek, például a késés, az eredmények és a kötet általános állapotát és teljesítményét.

**Metrika** | **Egység** | **Összesítés** | **Metrikus leírás** | **Méretek** |
|---|---|---|---|---|
| Feldolgozott események (esemény-/IoT-központok esetén) | Darabszám | Max, Min, Összeg | Az eseményközpontokból beolvasott és a fürt által feldolgozott események teljes száma. Az események vannak osztva elutasított események és a fürtmotor által elfogadott események. | EventStatus |
| Betöltési késés | Másodperc | Átlagos, Max, Min | A bevitt adatok késése afürtben az adatok fogadásának időpontjától addig, amíg készen nem áll a lekérdezésre. A betöltési késés időszak a ttól függ, hogy a betöltési forgatókönyv. | None |
| Lenyelési eredmény | Darabszám | Darabszám | A sikertelen és sikeres betöltési műveletek teljes száma. A **felosztás alkalmazásával** sikeres és sikertelen eredményeket hozhat létre, és elemezheti a dimenziókat (**Érték** > **állapota**).| Betöltéseredményrészletei |
| Betöltési kötet (MB-ban) | Darabszám | Max, Összeg | A tömörítés előtt a fürtbe (MB-ban) bevitt adatok teljes mérete. | Adatbázis |
| | | | |  

### <a name="query-performance"></a>Lekérdezési teljesítmény

A lekérdezési teljesítménymérők nyomon követik a lekérdezés időtartamát és az egyidejű vagy szabályozott lekérdezések teljes számát.

**Metrika** | **Egység** | **Összesítés** | **Metrikus leírás** | **Méretek** |
|---|---|---|---|---|
| Lekérdezés időtartama | Ezredmásodperc | Átlag, Min, Max, Összeg | A lekérdezés eredményének fogadásáig töltött teljes idő (nem tartalmazza a hálózati késést). | Lekérdezésállapota |
| Az egyidejű lekérdezések teljes száma | Darabszám | Átlag, Max, Min, Összeg | A fürtben párhuzamosan futó lekérdezések száma. Ez a metrika jó módszer a fürt terhelésének becslésére. | None |
| A szabályozott lekérdezések teljes száma | Darabszám | Átlag, Max, Min, Összeg | A fürtben lévő szabályozott (elutasított) lekérdezések száma. Az egyidejű lekérdezési házirendben az engedélyezett egyidejű (párhuzamos) lekérdezések maximális száma van definiálva. | None |
| | | | |

### <a name="streaming-ingest-metrics"></a>A betöltési mérőszámok streamelése

A streamelési metrikák nyomon követik a streamelési betöltési adatokat és a kérelmek sebességét, időtartamát és eredményeit.

**Metrika** | **Egység** | **Összesítés** | **Metrikus leírás** | **Méretek** |
|---|---|---|---|---|
A betöltési adatok átviteli sebessége |    Darabszám   | RaterequestsPerSecond (RaterequestsPerSzekszekszek | A fürtbe bevitt adatok teljes mennyisége. | None |
Streamelési idő   | Ezredmásodperc  | Átlagos, Max, Min | Az összes streamelési betöltési kérelem teljes időtartama. | None |
Betöltési kérelem sebességének streamelése   | Darabszám | Számolás, Átlag, Max, Min, Összeg | A streamelési kérelmek teljes száma. | None |
A betöltés i. | Darabszám | Átlag   | A streamelési kérelmek teljes száma eredménytípus szerint. | Eredmény |
| | | | |

További információ a [támogatott Azure Data Explorer fürtmetrikákról.](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)


## <a name="next-steps"></a>További lépések

* [Oktatóanyag: Adatok betöltése és lekérdezésfigyelési adatok betöltése az Azure Data Explorerben](/azure/data-explorer/ingest-data-no-code)
* [Az Azure Data Explorer betöltési műveleteinek figyelése diagnosztikai naplók használatával](/azure/data-explorer/using-diagnostic-logs)
* [Rövid útmutató: Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
