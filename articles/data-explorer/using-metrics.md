---
title: Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal
description: Ismerje meg, hogy a fürt teljesítmény, állapotának és használatának monitorozása az Azure Data Explorer metrikák használatával.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: 5252ca8898439b63a8819f6abfd634de0786932b
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851803"
---
# <a name="monitor-azure-data-explorer-performance-health-and-usage-with-metrics"></a>Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal

Az Azure Data Explorer egy gyors, teljes mértékben felügyelt adatelemző szolgáltatás, amellyel valós idejű elemzést végezhet többek között alkalmazások, webhelyek és IoT-eszközök nagy mennyiségű adatfolyamain. Az Azure Data Explorer használatához, először hozzon létre egy fürtöt, és az adott fürt egy vagy több adatbázis létrehozása. Ezután (betöltés) adatokat az adatbázisba fogadására, így vonatkozóan, lekérdezéseket futtathat. Az Azure Data Explorer metrikák adja meg a legfontosabb mutatók állapotának és teljesítményének a fürterőforrások esetleges szabályozási hiányosságok elhárítását. A metrikák figyelése az Azure Data Explorer fürt állapotát és teljesítményét az adott önálló mérőszámokként ebben a cikkben részletes használja. Metrikák alapjaként is használhatja működési [Azure-irányítópultok](/azure/azure-portal/azure-portal-dashboards) és [Azure Alerts](/azure/azure-monitor/platform/alerts-metric-overview).

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes Azure-fiók](https://azure.microsoft.com/free/).

* Hozzon létre egy [fürt és adatbázis](create-cluster-database-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="using-metrics"></a>Mérőszámok segítségével

Válassza ki az Azure Data Explorer fürt **metrikák** a metrika panel megnyitásához, és a fürtön elemzésének indításához.

![Metrikák kiválasztása](media/using-metrics/select-metrics.png)

A metrikák panelen:

![Metrika panel](media/using-metrics/metrics-pane.png)

1. Metrika diagram létrehozásához válassza **metrika** nevét és a megfelelő **összesítési** metrikánként leírtaknak megfelelően az alábbi. A **erőforrás** és **metrika Namespace** színválasztó előre kiválasztott az Azure Data Explorer fürthöz.

    **Metrika** | **Egység** | **Összesítés** | **Metrika leírása**
    |---|---|---|---|
    | Gyorsítótár-kihasználtság | Százalék | Avg, Max, Min | Szükséges gyorsítótár méretének (üzletszabályzata előírja a meghatározott gyorsítótár) és a gyorsítótár teljes mérete (a felhasználói tevékenység megadott SSD teljes méretét) fürt aránya. Egy gyorsítótár átlagos kihasználtsága 80 %-os vagy még kevesebb csak egy fürt fenntartható állapot. Ha a gyorsítótár átlagos kihasználtság meghaladja a 80 %-os, a fürt kell [vertikálisan](manage-cluster-scale-up.md) egy Storage-tarifacsomag optimalizált vagy [horizontálisan felskálázott](manage-cluster-scale-out.md) több példányra. Másik lehetőségként alkalmazkodik a gyorsítótár-szabályzat (kevesebb napig a gyorsítótárban). Ha több mint 100 %-os gyorsítótár-kihasználtság, az adatok gyorsítótárazását, a gyorsítótárazási házirend szerint mérete nagyobb, amelyek a fürtön a gyorsítótár teljes mérete. |
    | CPU | Százalék | Avg, Max, Min | Teljes CPU-használatának és az egész fürtön elérhető CPU aránya. Az átlagos Processzorhasználat 80 %-os vagy még kevesebb fenntartható egy fürthöz. CPU maximális értéke 100 %, ami azt jelenti, hogy nincsenek további számítási erőforrások fel adatokat. Amikor a fürt nem teljesít jól, ellenőrizze a CPU-határozza meg, hogy vannak-e le vannak tiltva adott processzorok maximális értékét. |
    | (Az Event Hubs) feldolgozott események | Darabszám | Sum, Min, max | Események száma összesen az Event Hubs által küldött, és a fürt által fogadott. Az események elutasított és fogadja el a fürt motor események vannak felosztva. |
    | Adatbetöltési késés | másodperc | Avg, Max, Min | Az idő, az adatok mindaddig, amíg készen áll a lekérdezés érkezett a fürtben található adatok késését. Adatbetöltési késés idejének mérése másodpercben történik. Az Adatbetöltési késés időszak az adatfeldolgozási forgatókönyvtől függ. |
    | Adatbetöltési eredménye | Darabszám | Darabszám | Sikertelen volt, és sikerült Adatbetöltési műveletek teljes száma. Használat **alkalmazni a felosztás** siker gyűjtők létrehozásához, és sikertelen eredményt.|
    | Adatbetöltési kihasználtsága | Százalék | Avg, Max, Min | Az adatok feldolgozására használt tényleges erőforrásokat és a feldolgozó ehhez a kapacitás házirendben lefoglalt erőforrások teljes aránya. Az alapértelmezett kapacitás házirend nem több, mint 512 egyidejű Adatbetöltési műveletek vagy 75 %-a fürt feldolgozó erőforrásaikat. Átlagos betöltési kihasználtsága 80 %-os vagy még kevesebb csak egy fürt fenntartható állapot. Maximális betöltési kihasználtsági értéke 100 %-os, ami azt jelenti, hogy minden fürt feldolgozási képessége szolgál, és a egy Adatbetöltési üzenetsor vonhat maga után. |
    | Adatbetöltési kötet (megabájtban) | Darabszám | Sum, Min, max | Teljes méretét (megabájtban) a fürt betöltött adatokért. Az egységek olyan MB gondoskodik a feldolgozott adatok száma. |
    | Életben tartási | Darabszám | Átlag | A fürt rendelkezésre állását követi nyomon. Egy teljes mértékben rugalmas fürt 1 értéket ad vissza, és a letiltott vagy leválasztott fürt 0 értéket adja vissza. |
    | Lekérdezés időtartama | másodperc | Száma, Avg, Min., max. és összeg | Teljes idő, amíg a lekérdezés eredményeit a rendszer küldjön. |
    | | | |

    További információk kapcsolatos [támogatott az Azure Data Explorer fürtmetrikák](/azure/azure-monitor/platform/metrics-supported#microsoftkustoclusters)

2. Válassza ki a **metrika hozzáadása** gombra a diagram ugyanaz megjelennek több metrikák megtekintéséhez.
3. Válassza ki a **+ új diagram** gombra kattintva egyetlen nézetben több diagramok megjelenítéséhez.
4. Az időtartomány módosításához használja a időválasztó (alapértelmezett: elmúlt 24 óra).
5. Használja [ **szűrő hozzáadása** és **alkalmazni a felosztás** ](/azure/azure-monitor/platform/metrics-getting-started#apply-dimension-filters-and-splitting) dimenziók rendelkező metrikák.
6. Válassza ki **rögzítés az irányítópulton** irányítópultokra történő felvételéhez a diagram konfigurációját az, hogy újra megtekintéséhez.
7. Állítsa be **Új riasztási szabály** megjelenítése a metrikákat, a set-feltétel használatával. Az Új riasztási szabály a célként megadott erőforrás, metrika, felosztását és szűrő dimenziók a diagram tartalmazza. Ezek a beállítások módosításához a [riasztási szabály létrehozása panel](/azure/azure-monitor/platform/metrics-charts#create-alert-rules).

További tájékoztatást a [Metrikaböngésző](/azure/azure-monitor/platform/metrics-getting-started).


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Az Azure Data Explorer adatok lekérdezése](web-query-data.md)
