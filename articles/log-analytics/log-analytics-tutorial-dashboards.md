---
title: "Hozzon létre és osszon meg az Azure Log Analytics-adatok irányítópultok |} Microsoft Docs"
description: "Ez az oktatóanyag segít megérteni, hogyan Naplóelemzési irányítópultok jelenítheti meg az összes a mentett napló keresések felkínálva egy egyetlen helyre gyűjti a környezet megtekintéséhez."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: abb07f6c-b356-4f15-85f5-60e4415d0ba2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 272945134b534a5ded794379ce5e96b0902a4227
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2017
---
# <a name="create-and-share-dashboards-of-log-analytics-data"></a>Hozzon létre és osszon irányítópultok Log Analytics-adatok

Log Analyticshez irányítópultok jelenítheti meg a napló mentett keresések mindegyikének megkeresése, felkínálva összefüggéseket, és megoszthatják az informatikai működési adatokat a szervezetében.  Ez az oktatóanyag ismerteti, amely támogatja az informatikai műveletek támogatási csapat által használt megosztott irányítópult használandó napló keresés létrehozása.  Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Megosztott irányítópult létrehozása az Azure-portálon
> * A teljesítmény napló keresési megjelenítése 
> * A naplófájl-keresési hozzáadása egy megosztott irányítópult 
> * A megosztott irányítópult mozaikra testreszabása

A példa az oktatóanyag elvégzéséhez rendelkeznie kell egy meglévő virtuális gép [csatlakozik a Naplóelemzési munkaterület](log-analytics-quick-collect-azurevm.md).  
 
## <a name="log-in-to-azure-portal"></a>Jelentkezzen be Azure-portálon
Jelentkezzen be az Azure portálon, a [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-shared-dashboard"></a>Hozzon létre egy megosztott irányítópult

A legfontosabb, a Microsoft Azure-portálon való bejelentkezés után megjelenik egy [irányítópult](../azure-portal/azure-portal-dashboards.md).<br> ![Azure-portál irányítópultjának](media/log-analytics-tutorial-dashboards/log-analytics-portal-dashboard.png)

Itt helyezheti együtt legfontosabb működésiadat informatikai minden az Azure-erőforrások, például az Azure Naplóelemzés telemetriai között.  Azt lépésenként naplófájl-keresési megjelenítése, mielőtt most először hozzon létre egy irányítópultot és ossza meg.  Ez lehetővé teszi, hogy töltse le innen a bevezetés előtt vesszük fel példa teljesítmény napló keresést, amely egy vonaldiagramot megjelenítve, és adja hozzá az irányítópulton.  

Hozzon létre egy irányítópultot, válassza ki a **új irányítópult** az aktuális irányítópult neve melletti gombra.<br> ![Új irányítópult létrehozása az Azure-portálon](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-01.png)

Ez a művelet létrehoz egy új, üres, személyes irányítópultot, és elhelyezi, amelyen az irányítópult neve, és adja hozzá vagy átrendezheti a csempéket testreszabási módban. Az irányítópult nevének módosítása, és adja meg *minta-irányítópult* Ez az oktatóanyag, majd válassza ki a **végzett Testreszabás**.<br><br> ![Testre szabott Azure irányítópult mentése](media/log-analytics-tutorial-dashboards/log-analytics-create-dashboard-02.png)

Amikor létrehoz egy irányítópultot, titkos alapértelmezés szerint azt jelenti, hogy más nem is áll. Ahhoz, hogy mások számára látható, használja a **megosztás** az irányítópult parancsok mellett megjelenő gombra.<br> ![Az Azure portál egy új irányítópult megosztása](media/log-analytics-tutorial-dashboards/log-analytics-share-dashboard.png) 

A rendszer felkéri válasszon ki egy előfizetés és az erőforráscsoport irányítópulthoz való közzétételhez. Kényelmi célokat szolgál, a portál egy erőforráscsoportban irányítópultok helyétől minta felé hívott élmény útmutatók tartozó közzétételi **irányítópultok**.  Ellenőrizze a kijelölt előfizetésben, majd kattintson **közzététel**.  Az irányítópulton látható információk hozzáférésének vezérli [Azure erőforrás-alapú hozzáférés-vezérlés](../active-directory/role-based-access-control-configure.md).   

## <a name="visualize-a-log-search"></a>A naplófájl-keresési megjelenítése

Alapszintű lekérdezést is létrehozhat egy sorba a naplózási keresési portálról az Azure portálon. A naplófájl-keresési portál egy külső portál megnyitása nélkül használható, és számos feladatot végrehajtani a napló keresések többek között a riasztási szabályok létrehozásához, számítógép-csoportok létrehozásáról és a lekérdezés eredményeit az használhatja. 

A [Advanced Analytics portál](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-the-Analytics-portal) egy dedikált portál, amely nem érhető el, a keresési napló portálon speciális funkciókat biztosítja. Szolgáltatások közé tartozik a lekérdezés több sorban szerkesztése, szelektív módon hajtható végre a kódot, környezetfüggő Intellisense és intelligens Analytics képes. Az Advanced Analytics-portálon létrehoz olyan teljesítménynézet grafikus formában, mentheti későbbi keresés, és a korábban létrehozott megosztott irányítópulton való rögzítéséhez.   

A naplófájl-keresési portál tartalmaz egy hivatkozást a Advanced Analytics portálon indíthatja el.<br> ![Indítsa el a Advanced Analytics portál](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-01.png)

Adja meg a következő lekérdezés csak a processzor kihasználtsága rögzíti a Windows és Linux számítógépek, számítógép és a TimeGenerated szerint csoportosítva, és egy visual diagram vissza az Analytics-portálon:

```
Perf | where CounterName == "% Processor Time" and ObjectName == "Processor" and InstanceName == "_Total" | summarize AggregatedValue = avg(CounterValue) by bin(TimeGenerated, 1m), Computer | render timechart
```

A lekérdezés mentésének kiválasztásával a **mentés lekérdezés** a jobb felső sarokban a gomb.<br> ![Lekérdezés mentése Advanced Analytics-portálról](media/log-analytics-tutorial-dashboards/log-analytics-advancedportal-02.png)<br><br> Az a **lekérdezés mentése** Vezérlőpultot, például adjon meg egy nevet *Azure virtuális gépek - processzorhasználat* majd **mentése**.  Így közös lekérdezések keresést, vagy módosítsa ezt a teljes egészében újra írása nélkül könyvtárát is létrehozhat.  Végül, ez a korábban létrehozott kiválasztásával megosztott irányítópult rögzíteni **PIN-kód diagram az Azure Irányítópultjára** gombra az oldal középső jobb felső sarkából.  

Most, hogy a lekérdezés rögzítve az irányítópulton, megfigyelheti, egy általános cím és az alatta megjegyzés.<br> ![Az Azure irányítópult minta](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-01.png)<br><br>  Valami értelmesebbre, amelyek könnyen érthető sikerült látják el azt kell nevezni.  Kattintson a jobb gombbal a csempe, és válasszon **Szerkesztés csempe**.  Amikor elkészült, a cím és a csempe alcíme testreszabása, kattintson a **frissítés**.  A fejléc megjelenik az kéri, hogy a változtatásokat, vagy vesse el.  Kattintson a **változtatásokat** , majd zárja be a **szerkesztése csempe** vezérlő ablaktáblán.  

![A minta-irányítópult elkészült konfigurációban](media/log-analytics-tutorial-dashboards/log-analytics-modify-dashboard-02.png)

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóprogramban megismerte irányítópult létrehozása az Azure portálon, és a naplófájl-keresési felvétele.  Megtudhatja, a különböző válaszok valósíthatja meg a következő oktatóanyagot előzetes napló keresési eredmények alapján.  

> [!div class="nextstepaction"]
> [A napló Analytics riasztásokkal kapcsolatos események válaszolni](log-analytics-tutorial-response.md)