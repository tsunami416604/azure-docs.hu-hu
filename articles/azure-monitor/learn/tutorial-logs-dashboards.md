---
title: Irányítópultok létrehozása és megosztása Azure Log Analytics-adatokból | Microsoft Docs
description: Ez az oktatóanyag segít megérteni, hogy a Log Analytics irányítópultjai hogyan jeleníthetik meg az összes mentett naplólekérdezést, így egyetlen objektívvel megtekintheti a környezetet.
ms.subservice: logs
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 76ba79561df4a75004369d24c4c6af82de9b1cfc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77661532"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Irányítópultok létrehozása és megosztása Log Analytics-adatokból

A Log Analytics irányítópultjai megjeleníthetik az összes mentett naplólekérdezést, így lehetővé teszik az informatikai működési adatok megkeresését, korrelációját és megosztását a szervezetben.  Ez az oktatóanyag egy naplólekérdezés létrehozását ismerteti, amely egy megosztott irányítópult támogatásához lesz használva, amelyet az informatikai műveletek támogatási csapata fog elérni.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Megosztott irányítópult létrehozása az Azure Portalon
> * Teljesítménynapló-lekérdezés megjelenítése 
> * Naplólekérdezés hozzáadása megosztott irányítópulthoz 
> * Csempe testreszabása megosztott irányítópulton belül

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Jelentkezzen be az Azure Portalon
Jelentkezzen be az Azure [https://portal.azure.com](https://portal.azure.com)Portalon a . 

## <a name="create-a-shared-dashboard"></a>Megosztott irányítópult létrehozása
Az alapértelmezett [irányítópult](../../azure-portal/azure-portal-dashboards.md)megnyitásához válassza az **Irányítópult** lehetőséget. Az irányítópult másképp fog kinézni, mint az alábbi példa.

![Azure Portal irányítópultja](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Itt összeállíthatók az informatikai szempontból legfontosabb működési adatok az összes Azure-erőforrásra vonatkozóan, beleértve az Azure Log Analyticsből származó telemetriákat.  Mielőtt belépnénk egy naplólekérdezés megjelenítésébe, hozzunk létre egy irányítópultot, és osszuk meg.  Ezután a példa teljesítménynapló-lekérdezésre összpontosíthatunk, amely vonaldiagramként jelenik meg, és hozzáadhatjuk az irányítópulthoz.  

Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.

![Új irányítópult létrehozása az Azure Portalon](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat. Szerkesztheti az irányítópult nevét, és megadhatja az oktatóanyag *mintairányítópultját,* majd válassza a **Kész testreszabás lehetőséget.**<br><br> ![Testreszabott Azure-irányítópult mentése](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja. Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.

![Új irányítópult megosztása az Azure Portalon](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  Ellenőrizze a kiválasztott előfizetést, majd kattintson a **Közzététel** elemre.  A rendszer az irányítópulton megjelenő információhoz való hozzáférést az [Azure erőforrás-alapú hozzáférés-vezérléssel](../../role-based-access-control/role-assignments-portal.md) ellenőrzi.   

## <a name="visualize-a-log-query"></a>Naplólekérdezés megjelenítése
[A Log Analytics](../log-query/get-started-portal.md) egy dedikált portál, amely a naplólekérdezésekkel és azok eredményeivel működik. Szolgáltatásai közé tartozik a lekérdezés több sorban való szerkesztése, a szelektív kódvégrehajtás, a kontextusfüggő Intellisense és az intelligens elemzés. Ebben az oktatóanyagban a Log Analytics segítségével hozzon létre egy teljesítménynézetet grafikus formában, mentse el egy későbbi lekérdezéshez, és rögzítse a korábban létrehozott megosztott irányítópulton.

A Log Analytics megnyitásához válassza a **Naplók** lehetőséget az Azure Monitor menüben. Egy új üres lekérdezéssel kezdődik.

![Kezdőlap](media/tutorial-logs-dashboards/homepage.png)

Írja be a következő lekérdezést a processzorkihasználtsági rekordok visszaadására windowsos és linuxos számítógépeken, számítógép és timegenerated szerint csoportosítva, és egy vizuális diagramon. A Lekérdezés futtatásához és az eredményül kapott diagram megtekintéséhez kattintson a **Futtatás** gombra.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Mentse a lekérdezést úgy, hogy a lap tetején a **Mentés** gombot választja.

![Lekérdezés mentése](media/tutorial-logs-dashboards/save-query.png)

A **Lekérdezés mentése** vezérlőpulton adjon meg egy nevet, például *az Azure virtuális gépek – processzorkihasználtság* és egy kategória, például az *irányítópultok,* majd kattintson a **Mentés gombra.**  Így létrehozhat egy általános lekérdezési könyvtárat, amelyet használhat és módosíthat.  Végül rögzítse ezt a korábban létrehozott megosztott irányítópultra úgy, hogy a lap jobb felső sarkában a Rögzítés az **irányítópultra** gombra kattint, majd kiválasztja az irányítópult nevét.

Most, hogy rögzítettünk egy lekérdezést az irányítópulton, láthatja, hogy az irányítópult kapott egy általános címet és egy cím alatti megjegyzést.

![Azure-irányítópult – példa](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Adjunk neki egy pontosabb nevet, hogy más felhasználók számára is érthető legyen.  A szerkesztés gombra kattintva testreszabhatja a csempe címét és feliratát, majd kattintson a **Frissítés gombra.**  Egy szalag jelenik meg, ahol választania kell, hogy közzéteszi vagy elveti a módosításokat.  Kattintson **a Másolat mentése gombra.**  

![A minta-irányítópult kész konfigurációja](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre irányítópultot az Azure Portalon, és adhat hozzá egy naplólekérdezést.  Továbbléphet a következő oktatóanyagba, és ismerje meg a naplólekérdezés eredményei alapján végrehajtható különböző válaszokat.  

> [!div class="nextstepaction"]
> [Eseményekre való válaszadás Log Analytics-riasztásokkal](tutorial-response.md)
