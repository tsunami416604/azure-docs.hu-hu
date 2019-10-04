---
title: Irányítópultok létrehozása és megosztása Azure Log Analytics-adatokból | Microsoft Docs
description: Ez az oktatóanyag segít megérteni, hogy Log Analytics irányítópultok hogyan láthatják el az összes mentett napló lekérdezését, így egyetlen lencsével megtekintheti a környezetét.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c859fb9b8b3509e8369559a3a9a4d45cb4e34125
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414174"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Irányítópultok létrehozása és megosztása Log Analytics-adatokból

Log Analytics irányítópultok képesek megjeleníteni az összes mentett napló lekérdezését, így a szervezeten belül megkeresheti, korrelálhatja és megoszthatja informatikai működési adatait.  Ez az oktatóanyag egy olyan log-lekérdezés létrehozását ismerteti, amelyet az informatikai operatív támogatási csapat által elérhető megosztott irányítópult támogatásához fog használni.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Megosztott irányítópult létrehozása az Azure Portalon
> * Teljesítménynapló-lekérdezés megjelenítése 
> * Log-lekérdezés hozzáadása egy megosztott irányítópulthoz 
> * Csempe testreszabása megosztott irányítópulton belül

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-shared-dashboard"></a>Megosztott irányítópult létrehozása
Válassza az **irányítópult** lehetőséget az alapértelmezett [irányítópult](../../azure-portal/azure-portal-dashboards.md)megnyitásához. Az irányítópult az alábbi példától eltérően fog megjelenni.

![Az Azure portal irányítópultján](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Itt összeállíthatók az informatikai szempontból legfontosabb működési adatok az összes Azure-erőforrásra vonatkozóan, beleértve az Azure Log Analyticsből származó telemetriákat.  Mielőtt megkezdené a naplózási lekérdezés megjelenítését, először hozzon létre egy irányítópultot, és ossza meg azt.  Ezután a példában szereplő Teljesítménynapló-lekérdezésre koncentrálunk, amely diagramként fog megjelenni, és hozzáadja az irányítópulthoz.  

Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.

![Új irányítópult létrehozása Azure Portal](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat. Szerkessze az irányítópult nevét, és adja meg a *minta irányítópultot* ehhez az oktatóanyaghoz, majd válassza a **Testreszabás kész**lehetőséget.<br><br> ![Testreszabott Azure-irányítópult mentése](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja. Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.

![Új irányítópult megosztása Azure Portal](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  Ellenőrizze a kiválasztott előfizetést, majd kattintson a **Közzététel** elemre.  A rendszer az irányítópulton megjelenő információhoz való hozzáférést az [Azure erőforrás-alapú hozzáférés-vezérléssel](../../role-based-access-control/role-assignments-portal.md) ellenőrzi.   

## <a name="visualize-a-log-query"></a>Napló lekérdezésének megjelenítése
A [log Analytics](../log-query/get-started-portal.md) egy dedikált portál, amely a naplózási lekérdezésekkel és azok eredményeivel való együttműködésre szolgál. Szolgáltatásai közé tartozik a lekérdezés több sorban való szerkesztése, a szelektív kódvégrehajtás, a kontextusfüggő Intellisense és az intelligens elemzés. Ebben az oktatóanyagban a Log Analytics használatával grafikus formában hozza létre a teljesítmény nézetet, mentse egy későbbi lekérdezéshez, majd rögzítse azt a korábban létrehozott megosztott irányítópultra.

Nyissa meg Log Analytics a Azure Monitor menüben a **naplók** lehetőség kiválasztásával. Egy új üres lekérdezéssel kezdődik.

![Kezdőlap](media/tutorial-logs-dashboards/homepage.png)

Adja meg a következő lekérdezést a processzor-kihasználtsági rekordok Windows és Linux rendszerű számítógépekre történő visszaadásához, a számítógép-és TimeGenerated szerint csoportosítva, valamint egy vizualizációs diagramon. Kattintson a **Futtatás** gombra a lekérdezés futtatásához és az eredményül kapott diagram megtekintéséhez.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

Mentse a lekérdezést a lap tetején található **Save (Mentés** ) gombra kattintva.

![Lekérdezés mentése](media/tutorial-logs-dashboards/save-query.png)

A **lekérdezés mentése** panelen adjon meg egy olyan nevet, mint például *Az Azure virtuális gépek – processzor kihasználtsága* és egy kategória, például *irányítópultok* , majd kattintson a **Mentés**gombra.  Így létrehozhatja a használható és módosítható gyakori lekérdezések tárait.  Végül a lap jobb felső sarkában lévő **rögzítés** az irányítópultra gombra kattintva rögzítse a korábban létrehozott megosztott irányítópultra, majd válassza ki az irányítópult nevét.

Most, hogy rögzítettünk egy lekérdezést az irányítópulton, láthatja, hogy az irányítópult kapott egy általános címet és egy cím alatti megjegyzést.

![Azure-irányítópult minta](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Adjunk neki egy pontosabb nevet, hogy más felhasználók számára is érthető legyen.  Kattintson a Szerkesztés gombra a csempe címének és alcímének testreszabásához, majd kattintson a **frissítés**gombra.  Egy szalag jelenik meg, ahol választania kell, hogy közzéteszi vagy elveti a módosításokat.  Kattintson **a másolat mentése**gombra.  

![A minta-irányítópult kész konfigurációja](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtanulta, hogyan hozhat létre irányítópultot a Azure Portalban, és hogyan adhat hozzá egy napló-lekérdezést.  Folytassa a következő oktatóanyaggal, amely a napló lekérdezési eredményei alapján megvalósítható különböző válaszokat ismerteti.  

> [!div class="nextstepaction"]
> [Eseményekre való válaszadás Log Analytics-riasztásokkal](tutorial-response.md)
