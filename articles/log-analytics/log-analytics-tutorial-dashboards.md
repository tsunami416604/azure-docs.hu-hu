---
title: Irányítópultok létrehozása és megosztása Azure Log Analytics-adatokból | Microsoft Docs
description: Ez az oktatóanyag segít megérteni, hogyan jelenítheti meg a Log Analytics irányítópultok segítségével az összes mentett naplókeresését, hogy az egész környezetet egyetlen helyen tekinthesse át.
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
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.component: ''
ms.openlocfilehash: f4d46b7b8ba245c4a7e3eddbe0615c47a1e99096
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870436"
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Irányítópultok létrehozása és megosztása Log Analytics-adatokból

A Log Analytics-irányítópultok képesek megjeleníteni az összes mentett naplókeresését, így informatikai működési adatokat kereshet, hasonlíthat össze és oszthat meg cégen belül.  Ez az oktatóanyag ismerteti, hogyan kell létrehozni egy naplókeresést egy olyan megosztott irányítópult alapjául, amelyhez az informatikai működéstámogatási csoport fog hozzáférni.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Megosztott irányítópult létrehozása az Azure Portalon
> * A teljesítménynapló-keresés ábrázolása 
> * Naplókeresés hozzáadása megosztott irányítópulthoz 
> * Csempe testreszabása megosztott irányítópulton belül

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre, amely [a Log Analytics-munkaterülethez csatlakozik](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) címen. 

## <a name="create-a-shared-dashboard"></a>Megosztott irányítópult létrehozása

Miután bejelentkezett a Microsoft Azure Portalra, elsőként egy [irányítópultot](../azure-portal/azure-portal-dashboards.md) fog látni.<br> ![Azure Portal irányítópult](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Itt összeállíthatók az informatikai szempontból legfontosabb működési adatok az összes Azure-erőforrásra vonatkozóan, beleértve az Azure Log Analyticsből származó telemetriákat.  Mielőtt nekifognánk egy naplókeresés ábrázolásának, hozzunk létre és osszunk meg egy irányítópultot.  Így ezt a lépést már azelőtt elvégezzük, hogy rátérnénk a teljesítménynapló-keresési példánkra, amelyet vonaldiagramként fogunk megjeleníteni, majd hozzáadjuk az irányítópulthoz.  

Egy irányítópult létrehozásához kattintson az **Új irányítópult** gombra a jelenlegi irányítópult neve mellett.<br> ![Új irányítópult létrehozása az Azure Portalon](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Ez a művelet létrehoz egy új, üres, privát irányítópultot, amely rögtön testreszabási módba lép, ahol elnevezheti az irányítópultot, valamint hozzáadhat csempéket vagy átrendezheti azokat. A jelen példa kedvéért adja a *Minta-irányítópult* nevet az irányítópultnak, majd válassza a **Testreszabás kész** gombot.<br><br> ![Testreszabott Azure-irányítópult mentése](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Amikor létrehoz egy irányítópultot, az alapértelmezetten privát lesz, vagyis csak Ön láthatja. Ahhoz, hogy mások számára is láthatóvá tegye, használja az egyéb irányítópult-parancsok mellett megjelenő **Megosztás** gombot.<br> ![Új irányítópult megosztása az Azure Portalon](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

A rendszer felkéri, hogy válasszon ki egy előfizetést és egy erőforráscsoportot, ahová az irányítópultot közzé kívánja tenni. A kényelmesebb használat érdekében a portál közzétételi folyamata egy olyan mintához vezet, amellyel az irányítópultokat egy **irányítópultok** elnevezésű erőforráscsoportba helyezi.  Ellenőrizze a kiválasztott előfizetést, majd kattintson a **Közzététel** elemre.  A rendszer az irányítópulton megjelenő információhoz való hozzáférést az [Azure erőforrás-alapú hozzáférés-vezérléssel](../role-based-access-control/role-assignments-portal.md) ellenőrzi.   

## <a name="visualize-a-log-search"></a>Naplókeresés ábrázolása

Az Azure Portal naplókeresési portálján hozhat létre alapszintű lekérdezéseket egyetlen sorra vonatkozóan. A naplókeresési portál egy külső portál megnyitása nélkül használható, és a segítségével számos feladatot végrehajthat naplókeresésekkel, például a riasztási szabályok létrehozását, számítógépcsoportok létrehozását és a lekérdezéseredmények exportálását. 

A [Log Analytics portál](../azure-monitor/log-query/get-started-portal.md) egy dedikált portál, amely olyan fejlett funkciókkal rendelkezik, amelyek nem érhetők el a naplókeresési portálon. Szolgáltatásai közé tartozik a lekérdezés több sorban való szerkesztése, a szelektív kódvégrehajtás, a kontextusfüggő Intellisense és az intelligens elemzés. A Bővített analitika portálon létre fog hozni egy grafikus teljesítménynézetet, menti egy későbbi kereséshez, majd rögzíti az imént létrehozott megosztott irányítópulton.   

A Bővített analitika portált a naplókeresési portálon található hivatkozással indíthatja el.<br> ![A Bővített analitika portál elindítása](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Az Analitika portálon írja be a következő lekérdezést, amely Windows és Linux rendszerű számítógépekhez tartozó, csak processzorhasználati rekordokat ad vissza, Computer (Számítógép) és TimeGenerated (Létrehozási idő) szerint csoportosítva, és egy vizuális diagramban megjelenítve:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

A lekérdezés mentéséhez válassza a jobb felső sarokban a **Lekérdezés mentése** gombot.<br> ![Lekérdezés mentése a Bővített analitika portálról](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> A **Lekérdezés mentése** kezelőpanelen adjon meg egy nevet, pl. *Azure-beli virtuális gépek processzorhasználata*, majd kattintson a **Mentés** gombra.  Ezzel egy kódtárat hozhat létre a keresésekben használt gyakori lekérdezések számára, vagy módosíthatja a kódtárat anélkül, hogy teljesen át kellene azt írnia.  Végül rögzítse a kódtárat a már korábban létrehozott megosztott irányítópulton. Ezt a **Pin chart to your Azure dashboard** (Diagram rögzítése az Azure irányítópulthoz) gombbal teheti meg, amely a lap középső sávján, a jobb sarokban található.  

Most, hogy rögzítettünk egy lekérdezést az irányítópulton, láthatja, hogy az irányítópult kapott egy általános címet és egy cím alatti megjegyzést.<br> ![Azure-irányítópult – példa](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Adjunk neki egy pontosabb nevet, hogy más felhasználók számára is érthető legyen.  Kattintson a jobb gombbal a csempére, majd a **Csempe szerkesztése** elemre.  Amikor elkészült a csempe címének és alcímének testreszabásával, kattintson a **Frissítés** elemre.  Egy szalag jelenik meg, ahol választania kell, hogy közzéteszi vagy elveti a módosításokat.  Kattintson a **Módosítások közzététele** gombra, majd zárja be a **Csempe szerkesztése** vezérlőpanelt.  

![A minta-irányítópult kész konfigurációja](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan hozhat létre egy irányítópultot az Azure Portalon, és hogyan rendelhet hozzá naplókereséseket.  Folytassa a következő oktatóanyaggal, amely ismerteti, hogy milyen válaszokat valósíthat meg naplókeresések alapján.  

> [!div class="nextstepaction"]
> [Eseményekre való válaszadás Log Analytics-riasztásokkal](log-analytics-tutorial-response.md)
