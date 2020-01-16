---
title: Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal
description: Ismerje meg, hogyan használhatja az Azure Adatkezelő mérőszámait a fürt teljesítményének, állapotának és használatának figyelésére.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: 7ff504a466224594c0098bc9d80557d45e4197a6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76027888"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Az Azure Adatkezelő teljesítményének, állapotának és használatának monitorozása metrikákkal

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet alkalmazások, webhelyek, IoT-eszközök és egyebek nagy mennyiségű adatfolyamain. Az Azure Adatkezelő használatához először létre kell hoznia egy fürtöt, és létre kell hoznia egy vagy több adatbázist a fürtben. Ezután betöltheti az adatterhelést egy adatbázisba, így lekérdezéseket futtathat. Az Azure Adatkezelő mérőszámai a fürterőforrások állapotára és teljesítményére vonatkozó kulcsfontosságú mutatókat biztosítanak. Az ebben a cikkben részletezett metrikák használatával az Azure Adatkezelő a fürt állapotát és teljesítményét az önálló mérőszámok alapján figyelheti meg az adott forgatókönyvben. A mérőszámokat az operatív [Azure-irányítópultok](/azure/azure-portal/azure-portal-dashboards) és az [Azure-riasztások](/azure/azure-monitor/platform/alerts-metric-overview)alapjául is használhatja.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/).

* Hozzon létre egy [fürtöt és egy adatbázist](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="using-metrics"></a>Metrikák használata

Az Azure Adatkezelő-fürtben válassza a **metrikák** lehetőséget a metrikák ablaktábla megnyitásához és a fürt elemzésének megkezdéséhez.

![Metrikák kiválasztása](media/using-metrics/select-metrics.png)

A metrikák ablaktáblán:

![Metrikák ablaktábla](media/using-metrics/metrics-pane.png)

1. Metrikai diagram létrehozásához válassza a **metrika** neve és a megfelelő **Összesítés** /metrika lehetőséget az alább részletezett módon. Az **erőforrás** -és **metrikus névterek** választói előre ki vannak választva az Azure adatkezelő-fürtön.

    **Metrika** | **Egység** | **Összesítés** | **Metrika leírása**
    |---|---|---|---|
    | Gyorsítótár kihasználtsága | Százalék | AVG, Max, min | A fürt által jelenleg használt lefoglalt gyorsítótár-erőforrások százalékos aránya. A gyorsítótár a felhasználói tevékenység számára lefoglalt SSD mérete a megadott gyorsítótár-házirend szerint. A gyorsítótár átlagos kihasználtsága 80%-os vagy annál kevesebb a fürt fenntartható állapota. Ha az átlagos gyorsítótár kihasználtsága 80% fölé esik, a fürtöt egy tárterületre optimalizált díjszabási [szinten kell méretezni](manage-cluster-vertical-scaling.md) , vagy több példányra kell [méretezni](manage-cluster-horizontal-scaling.md) . Azt is megteheti, hogy a gyorsítótár-szabályzatot (kevesebb nap a gyorsítótárban) módosítja. Ha a gyorsítótár kihasználtsága meghaladja a 100%-ot, a gyorsítótárazási házirendnek megfelelően a gyorsítótárazott adat mérete nagyobb, mint a fürt gyorsítótárának teljes mérete. |
    | CPU | Százalék | AVG, Max, min | A fürtben lévő gépek által jelenleg használt lefoglalt számítási erőforrások százalékos aránya. Egy fürt számára a 80%-os vagy annál kisebb átlagos CPU-érték fenntartható. A CPU maximális értéke 100%, ami azt jelenti, hogy nincs további számítási erőforrás az adatok feldolgozásához. Ha egy fürt nem jól teljesítő, ellenőrizze a CPU maximális értékét annak megállapításához, hogy vannak-e letiltott processzorok. |
    | Feldolgozott események (Event Hubs) | Mennyiség | Max., min. összeg | Az Event hubokból beolvasott és a fürt által feldolgozott események teljes száma. Az események az elutasított eseményekre oszlanak, és a fürt motorja által elfogadott eseményeket. |
    | Betöltési késés | másodperc | AVG, Max, min | A betöltött adatok késése, az adatoknak a fürtön való kézhezvételének időpontjáig, amíg a lekérdezésre készen áll. A betöltési késés időtartama a betöltési forgatókönyvtől függ. |
    | Betöltés eredménye | Mennyiség | Mennyiség | A meghiúsult és sikeres betöltési műveletek teljes száma. A **felosztás alkalmazása** paranccsal hozhat létre sikereket, és nem végezheti el az eredményeket, és elemezheti a dimenziókat (**érték** > **állapot**).|
    | Betöltés kihasználtsága | Százalék | AVG, Max, min | A rendelkezésre álló erőforrások százalékos aránya a kapacitási szabályzatban a betöltés végrehajtásához felhasznált összes erőforrásból. Az alapértelmezett kapacitási szabályzat nem több mint 512 egyidejű betöltési művelet, vagy a betöltésbe fektetett fürterőforrások 75%-a. A betöltés átlagos kihasználtsága 80% vagy kevesebb a fürt fenntartható állapota. A betöltés kihasználtságának maximális értéke 100%, ami azt jelenti, hogy az összes fürtön lévő betöltési képesség használatos, és a betöltési várólista is eredményezhet. |
    | Betöltési mennyiség (MB) | Mennyiség | Max., min. összeg | A fürtbe betöltött adatmennyiség teljes mérete (MB) a tömörítés előtt. |
    | Életben tartása | Mennyiség | Átlag | Nyomon követi a fürt érzékenységét. A teljes mértékben válaszoló fürt 1 értéket ad vissza, a letiltott vagy leválasztott fürt pedig 0 értéket ad vissza. |
    | Lekérdezés időtartama | másodperc | Darabszám, átlag, min, Max, Sum | A lekérdezési eredmények fogadásának teljes ideje (nem tartalmazza a hálózati késést). |
    | Az egyidejű lekérdezések teljes száma | Mennyiség | AVG, Max, min, Sum | A fürtben párhuzamosan futtatott lekérdezések száma. Ez a mérőszám jó módszer a fürt terhelésének becslésére. |
    | A szabályozott lekérdezések teljes száma | Mennyiség | AVG, Max, min, Sum | A fürtben a szabályozott (visszautasított) lekérdezések száma. Az egyidejű (párhuzamos) lekérdezések megengedett maximális száma az egyidejű lekérdezési házirendben van meghatározva. |
    | A szabályozott parancsok teljes száma | Mennyiség | AVG, Max, min, Sum | A fürtben lévő szabályozott (visszautasított) parancsok száma, mivel elérte az egyidejű (párhuzamos) parancsok maximálisan megengedett számát. |
    | Egységek teljes száma | Mennyiség | AVG, Max, min, Sum | Az adategységek teljes száma a fürtben. Az ebben a metrikaban bekövetkező változások nagy adatszerkezetbeli változásokat és nagy terhelést okozhatnak a fürtben, mivel az adategységek összevonása egy CPU-nehéz tevékenység. |
    | | | | |

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
