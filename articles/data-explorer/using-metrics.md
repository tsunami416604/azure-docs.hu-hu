---
title: Azure Data Explorer Teljesítményfigyelő, egészségügyi és használati metrikákkal
description: Ismerje meg, hogy a fürt teljesítmény, állapotának és használatának monitorozása az Azure Data Explorer metrikák használatával.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2019
ms.openlocfilehash: a9c9f4d827d21c374bebba9d39e33b0bcad8a83e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050614"
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
    | Gyorsítótár-kihasználtság | Százalék | Avg, Max, Min | A fürt által használt lefoglalt gyorsítótár erőforrások százaléka. Gyorsítótár felhasználói tevékenységet a megadott gyorsítótár-szabályzatnak megfelelően számára lefoglalt SSD méretét jelenti. Egy gyorsítótár átlagos kihasználtsága 80 %-os vagy még kevesebb csak egy fürt fenntartható állapot. Ha a gyorsítótár átlagos kihasználtság meghaladja a 80 %-os, a fürt kell [vertikálisan](manage-cluster-scale-up.md) egy Storage-tarifacsomag optimalizált vagy [horizontálisan felskálázott](manage-cluster-scale-out.md) több példányra. Másik lehetőségként alkalmazkodik a gyorsítótár-szabályzat (kevesebb napig a gyorsítótárban). Ha több mint 100 %-os gyorsítótár-kihasználtság, az adatok gyorsítótárazását, a gyorsítótárazási házirend szerint mérete nagyobb, amelyek a fürtön a gyorsítótár teljes mérete. |
    | CPU | Százalék | Avg, Max, Min | Jelenleg a gépek a fürt által használt lefoglalt számítási erőforrások százaléka. Az átlagos Processzorhasználat 80 %-os vagy még kevesebb fenntartható egy fürthöz. CPU maximális értéke 100 %, ami azt jelenti, hogy nincsenek további számítási erőforrások fel adatokat. Amikor a fürt nem teljesít jól, ellenőrizze a CPU-határozza meg, hogy vannak-e le vannak tiltva adott processzorok maximális értéke. |
    | (Az Event Hubs) feldolgozott események | Darabszám | Sum, Min, max | Események száma összesen event hubs szolgáltatásból, és a fürt által feldolgozott. Az események elutasított és fogadja el a fürt motor események vannak felosztva. |
    | Adatbetöltési késés | másodperc | Avg, Max, Min | Betöltött, kezdve az adatok mindaddig, amíg készen áll a lekérdezés érkezett a fürtben lévő adatok késését. Az Adatbetöltési késés időszak az adatfeldolgozási forgatókönyvtől függ. |
    | Adatbetöltési eredménye | Darabszám | Darabszám | Sikertelen volt, és sikerült Adatbetöltési műveletek teljes száma. Használat **alkalmazni a felosztás** és hozhat létre gyűjtők a sikeres és sikertelen eredményt a dimenziók elemzése (**érték** > **állapota**).|
    | Adatbetöltési kihasználtsága | Százalék | Avg, Max, Min | Gyűjthet adatokat a lefoglalt erőforrások teljes mennyisége, a kapacitás házirendben Adatbetöltési végrehajtásához használt tényleges erőforrások százaléka. Az alapértelmezett kapacitás házirend nem több, mint 512 egyidejű Adatbetöltési műveletek vagy 75 %-a fürt feldolgozó erőforrásaikat. Átlagos betöltési kihasználtsága 80 %-os vagy még kevesebb csak egy fürt fenntartható állapot. Maximális betöltési kihasználtsági értéke 100 %-os, ami azt jelenti, hogy minden fürt feldolgozási képessége szolgál, és a egy Adatbetöltési üzenetsor vonhat maga után. |
    | Adatbetöltési kötet (megabájtban) | Darabszám | Sum, Min, max | A tömörítés előtti (megabájtban) a fürthöz betöltött adatok teljes mérete. |
    | Életben tartási | Darabszám | Átlag | A fürt rendelkezésre állását követi nyomon. Egy teljes mértékben rugalmas fürt 1 értéket ad vissza, és a letiltott vagy leválasztott fürt 0 értéket adja vissza. |
    | Lekérdezés időtartama | másodperc | Száma, Avg, Min., max. és összeg | Teljes idő, amíg a lekérdezés eredményeit a rendszer küldjön (nem tartalmazza a hálózati késés). |
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
