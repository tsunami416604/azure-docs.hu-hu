---
title: Irányítópultok létrehozása és megosztása Azure Log Analytics-adatokból | Microsoft Docs
description: Ez az oktatóanyag segít megérteni, hogyan Log Analytics-irányítópultok képesek megjeleníteni az összes, a mentett naplóbeli lekérdezések, így egy egyetlen fókuszban a környezet megtekintése.
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
ms.openlocfilehash: 93cda8680bc665055d449e86c24d6565f6fc525f
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296406"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Irányítópultok létrehozása és megosztása Log Analytics-adatokból

A log Analytics irányítópultok képesek megjeleníteni az összes, a mentett naplóbeli lekérdezések kereshet, így összekapcsolását, és megoszthatja a szervezet informatikai működési adatokat.  Ez az oktatóanyag bemutatja egy olyan megosztott irányítópult, amely fogják elérni az informatikai működéstámogatási csoport által használt log-lekérdezés létrehozása.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Megosztott irányítópult létrehozása az Azure Portalon
> * A teljesítmény naplólekérdezés megjelenítése 
> * A napló lekérdezés hozzáadása megosztott irányítópulthoz 
> * Csempe testreszabása megosztott irányítópulton belül

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](quick-collect-azurevm.md).  
 
## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure portálra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="create-a-shared-dashboard"></a>Megosztott irányítópult létrehozása
Válassza ki **irányítópult** , nyissa meg az alapértelmezett [irányítópult](../../azure-portal/azure-portal-dashboards.md). Az irányítópulton fog kinézni, mint az alábbi példát.

![Az Azure portal irányítópultján](media/tutorial-logs-dashboards/log-analytics-portal-dashboard.png)

Itt összeállíthatók az informatikai szempontból legfontosabb működési adatok az összes Azure-erőforrásra vonatkozóan, beleértve az Azure Log Analyticsből származó telemetriákat.  Mielőtt nekifognánk egy naplólekérdezés, nézzük először hozzon létre egy irányítópultot, és ossza meg.  Ezután összpontosíthatunk a példa teljesítmény log lekérdezést, amely egy vonaldiagramon jelennek meg, és adja hozzá az irányítópulthoz.  

Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.

![Új irányítópult létrehozása az Azure Portalon](media/tutorial-logs-dashboards/log-analytics-create-dashboard-01.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat. Szerkessze az irányítópult nevét, és adja meg *minta-irányítópult* ebben az oktatóanyagban, és válassza ki a **testreszabás kész**.<br><br> ![Testreszabott Azure-irányítópult mentése](media/tutorial-logs-dashboards/log-analytics-create-dashboard-02.png)

Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja. Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.

![Az Azure Portalon új irányítópult megosztása](media/tutorial-logs-dashboards/log-analytics-share-dashboard.png) 

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  Ellenőrizze a kiválasztott előfizetést, majd kattintson a **Közzététel** elemre.  A rendszer az irányítópulton megjelenő információhoz való hozzáférést az [Azure erőforrás-alapú hozzáférés-vezérléssel](../../role-based-access-control/role-assignments-portal.md) ellenőrzi.   

## <a name="visualize-a-log-query"></a>A naplólekérdezés megjelenítése
[Log Analytics](../log-query/get-started-portal.md) egy dedikált portál, a naplóbeli lekérdezések és az eredményeket használja. Szolgáltatásai közé tartozik a lekérdezés több sorban való szerkesztése, a szelektív kódvégrehajtás, a kontextusfüggő Intellisense és az intelligens elemzés. Ebben az oktatóanyagban a Log Analytics használandó grafikus formában mentse azt egy jövőbeli lekérdezés teljesítmény nézetet létrehoznia, és rögzítheti is a korábban létrehozott megosztott irányítópulton.

Nyissa meg a Log Analytics kiválasztásával **naplók** az Azure figyelő menü. Egy új üres lekérdezés kezdődik.

![Kezdőlap](media/tutorial-logs-dashboards/homepage.png)

Adja meg a következő lekérdezést, a Windows- és Linux számítógépek, számítógép és a TimeGenerated szerint csoportosítva, és egy vizuális diagramban megjelenítve processzorhasználati rekordjait adja vissza a processzor. Kattintson a **futtatása** futtatni a lekérdezést, és megtekintheti a létrehozott diagramra.

```Kusto
Perf 
| where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" 
| summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1hr), Computer 
| render timechart
```

A lekérdezés mentéséhez válassza a **mentése** gombra a lap tetején.

![Lekérdezés mentése](media/tutorial-logs-dashboards/save-query.png)

Az a **lekérdezés mentése** Vezérlőpultot, például adjon meg egy nevet *Azure virtuális gépek – processzorhasználat* és a egy kategóriát, például *irányítópultok* majd **mentése** .  Ezzel a módszerrel, amely egy könyvtár használt gyakori lekérdezések hozhat létre használhat és módosítása.  Végül rögzítse a korábban létrehozott kiválasztásával a megosztott irányítópultra mutató a **PIN-kód** gombra a jobb felső részén a lapot, és válassza az irányítópult nevét.

Most, hogy rögzítettünk egy lekérdezést az irányítópulton, láthatja, hogy az irányítópult kapott egy általános címet és egy cím alatti megjegyzést.

![Az Azure-irányítópult – példa](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-01.png)

 Adjunk neki egy pontosabb nevet, hogy más felhasználók számára is érthető legyen.  Kattintson a cím és alcím csempe testreszabása, és kattintson a Szerkesztés gombra **frissítés**.  Egy szalag jelenik meg, ahol választania kell, hogy közzéteszi vagy elveti a módosításokat.  Kattintson a **mentsen egy másolatot**.  

![A minta-irányítópult kész konfigurációja](media/tutorial-logs-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban megtudhatta, hogyan irányítópult létrehozása az Azure Portalon, és a egy naplólekérdezés hozzá.  Milyen válaszokat valósíthat meg a következő oktatóanyaggal, amelyben tudatjuk a felhasználókkal a lekérdezés eredményeihez alapján.  

> [!div class="nextstepaction"]
> [Eseményekre való válaszadás Log Analytics-riasztásokkal](tutorial-response.md)
