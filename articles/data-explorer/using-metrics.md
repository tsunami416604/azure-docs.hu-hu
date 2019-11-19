---
title: Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal
description: Ismerje meg, hogyan használhatja az Azure Adatkezelő mérőszámait a fürt teljesítményének, állapotának és használatának figyelésére.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: f5b47a5ae9d13711233d0e4852ec487af7344622
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173789"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Az Azure Adatkezelő mérőszámai a fürterőforrások állapotára és teljesítményére vonatkozó kulcsfontosságú mutatókat biztosítanak. Az ebben a cikkben részletezett metrikák használatával az Azure Adatkezelő a fürt állapotát és teljesítményét az önálló mérőszámok alapján figyelheti meg az adott forgatókönyvben. A mérőszámokat az operatív [Azure-irányítópultok](/azure/azure-portal/azure-portal-dashboards) és az [Azure-riasztások](/azure/azure-monitor/platform/alerts-metric-overview)alapjául is használhatja.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* Hozzon létre egy [fürtöt és egy adatbázist](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Bejelentkezés az [Azure Portalra](https://portal.azure.com/).

## <a name="using-metrics"></a>Metrikák használata

Az Azure Adatkezelő-fürtben válassza a **metrikák** lehetőséget a metrikák ablaktábla megnyitásához és a fürt elemzésének megkezdéséhez.

![Metrikák kiválasztása](media/using-metrics/select-metrics.png)

A metrikák ablaktáblán:

![Metrikák ablaktábla](media/using-metrics/metrics-pane.png)

1. Metrikai diagram létrehozásához válassza a **metrika** neve és a megfelelő **Összesítés** /metrika lehetőséget az alább részletezett módon. Az **erőforrás** -és **metrikus névterek** választói előre ki vannak választva az Azure adatkezelő-fürtön.

    **Metrika** | **Egység** | **Összesítés** | **Metrika leírása**
    |---|---|---|---|
    | Gyorsítótár kihasználtsága | Százalék | Avg, Max, Min | A fürt által jelenleg használt lefoglalt gyorsítótár-erőforrások százalékos aránya. A gyorsítótár a megadott gyorsítótár-házirend alapján a felhasználói tevékenység számára lefoglalt SSD méretére utal. A gyorsítótár átlagos kihasználtsága 80%-os vagy annál kevesebb a fürt fenntartható állapota. Ha az átlagos gyorsítótár kihasználtsága 80% fölé esik, a fürtöt egy tárterületre optimalizált díjszabási [szinten kell méretezni](manage-cluster-vertical-scaling.md) , vagy több példányra kell [méretezni](manage-cluster-horizontal-scaling.md) . Azt is megteheti, hogy a gyorsítótár-szabályzatot (kevesebb nap a gyorsítótárban) módosítja. Ha a gyorsítótár kihasználtsága meghaladja a 100%-ot, a gyorsítótárazási házirendnek megfelelően a gyorsítótárazott adat mérete nagyobb, mint a fürt gyorsítótárának teljes mérete. |
    | CPU | Százalék | Avg, Max, Min | A fürtben lévő gépek által jelenleg használt lefoglalt számítási erőforrások százalékos aránya. Egy fürt számára a 80%-os vagy annál kisebb átlagos CPU-érték fenntartható. A CPU maximális értéke 100%, ami azt jelenti, hogy nincs további számítási erőforrás az adatok feldolgozásához. Ha egy fürt nem jól teljesítő, ellenőrizze a CPU maximális értékét annak megállapításához, hogy vannak-e letiltott processzorok. |
    | Feldolgozott események (Event Hubs) | Darabszám | Max., min. összeg | Az Event hubokból beolvasott és a fürt által feldolgozott események teljes száma. Az események az elutasított eseményekre oszlanak, és a fürt motorja által elfogadott eseményeket. |
    | Betöltési késés | másodperc | Avg, Max, Min | A betöltött adatok késése, az adatoknak a fürtön való kézhezvételének időpontjáig, amíg a lekérdezésre készen áll. A betöltési késés időtartama a betöltési forgatókönyvtől függ. |
    | Betöltés eredménye | Darabszám | Darabszám | A meghiúsult és sikeres betöltési műveletek teljes száma. A **felosztás alkalmazása** paranccsal hozhat létre sikereket, és nem végezheti el az eredményeket, és elemezheti a dimenziókat (**érték** > **állapot**).|
    | Betöltés kihasználtsága | Százalék | Avg, Max, Min | A rendelkezésre álló erőforrások százalékos aránya a kapacitási szabályzatban a betöltés végrehajtásához felhasznált összes erőforrásból. Az alapértelmezett kapacitási szabályzat nem több mint 512 egyidejű betöltési művelet, vagy a betöltésbe fektetett fürterőforrások 75%-a. A betöltés átlagos kihasználtsága 80% vagy kevesebb a fürt fenntartható állapota. A betöltés kihasználtságának maximális értéke 100%, ami azt jelenti, hogy az összes fürtön lévő betöltési képesség használatos, és a betöltési várólista is eredményezhet. |
    | Betöltési mennyiség (MB) | Darabszám | Max., min. összeg | A fürtbe betöltött adatmennyiség teljes mérete (MB) a tömörítés előtt. |
    | Életben tartása | Darabszám | AVG | Nyomon követi a fürt érzékenységét. A teljes mértékben válaszoló fürt 1 értéket ad vissza, a letiltott vagy leválasztott fürt pedig 0 értéket ad vissza. |
    | Lekérdezés időtartama | másodperc | Darabszám, átlag, min, Max, Sum | A lekérdezési eredmények fogadásának teljes ideje (nem tartalmazza a hálózati késést). |
    | | | |

    További információ a [támogatott Azure adatkezelő-fürt metrikákkal](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters) kapcsolatban

2. A **metrika hozzáadása** gomb kiválasztásával több, ugyanabban a diagramban ábrázolt metrika látható.
3. Kattintson az **+ új diagram** gombra, ha több diagramot szeretne látni egy nézetben.
4. A Time Picker használatával módosíthatja az időtartományt (alapértelmezés: az elmúlt 24 óra).
5. Használja a [ **szűrő hozzáadása** és a **felosztás alkalmazása** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) a dimenziókkal rendelkező mérőszámokhoz.
6. Válassza a **rögzítés az irányítópulton** lehetőséget, hogy hozzáadja a diagram konfigurációját az irányítópultokhoz, hogy megtekintse azt újra.
7. Az **új riasztási szabály** beállításával jelenítheti meg a mérőszámokat a set feltételek használatával. Az új riasztási szabály a diagramon a cél erőforrás, metrika, felosztás és szűrés dimenziókat fogja tartalmazni. Módosítsa ezeket a beállításokat a [riasztási szabály létrehozása ablaktáblán](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

További információ a [Metrikaböngésző](/azure/azure-monitor/platform/metrics-getting-started)használatáról.


## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: figyelési adatfeldolgozás és-lekérdezés az Azure Adatkezelő](/azure/data-explorer/ingest-data-no-code)
* [Azure Adatkezelő-betöltési műveletek figyelése diagnosztikai naplók használatával](/azure/data-explorer/using-diagnostic-logs)
* [Rövid útmutató: Adatok lekérdezése az Azure Data Explorerben](web-query-data.md)
