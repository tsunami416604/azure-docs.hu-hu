---
title: "Azure-webapp teljesítményének figyelése | Microsoft Docs"
description: "Alkalmazásteljesítmény figyelése Azure-webappok esetében. Diagrambetöltési és -válaszidők függőségi információk és beállított, teljesítménnyel kapcsolatos riasztások."
services: application-insights
documentationcenter: .net
author: CFreemanwa
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: azure-portal
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/05/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: b25f3dd41c8dc381258bb42d8802ba4b56e05903
ms.contentlocale: hu-hu
ms.lasthandoff: 08/02/2017

---
# <a name="monitor-azure-web-app-performance"></a>Azure-webapp teljesítményének figyelése
Az [Azure Portalon](https://portal.azure.com) beállíthat alkalmazásteljesítmény-figyelést [Azure-webappjaihoz](../app-service-web/app-service-web-overview.md). Az [Azure Application Insights](app-insights-overview.md) úgy alakítja ki az alkalmazást, hogy telemetriát küldjön tevékenységeiről az Application Insights szolgáltatásnak, amely tárolja és elemzi azokat. Itt metrikus diagramok és keresőeszközök segítségével diagnosztizálhat problémákat, javíthatja a teljesítményt, és felmérheti a használatot.

## <a name="run-time-or-build-time"></a>Futási idő vagy felépítési idő
A figyelés konfigurálásához kétféle módon alakíthatja ki az alkalmazást:

* **Futási idő** – Kiválaszthat egy teljesítményfigyelési bővítményt, amikor a webapp már működik. Nem szükséges újraépíteni vagy újratelepíteni az alkalmazást. Olyan standard csomagkészletet kap, amely figyeli a válaszidőt, a sikerességi arányokat, a kivételeket, a függőségeket stb. 
* **Felépítési idő** – Telepíthet egy csomagot fejlesztés alatt álló alkalmazásába. Ez a lehetőség jóval sokoldalúbb. A standard csomagkészlet mellett kódolással testreszabhatja a telemetriát, vagy elküldheti saját telemetriáját. Naplózhat konkrét tevékenységeket vagy rögzíthet eseményeket alkalmazástartománya szemantikájának megfelelően. 

## <a name="run-time-instrumentation-with-application-insights"></a>Futási idő kialakítása az Application Insights segítségével
Ha már futtat egy webappot az Azure-ban, a kéréseket és a hibák gyakoriságát a rendszer már figyeli. Az Application Insights hozzáadásával további figyelési funkciók érhetők el, például a válaszidők figyelése, a függőségek hívásainak figyelése, az intelligens észlelés és a hatékony Log Analytics lekérdezési nyelv. 

1. Webappja Azure-kezelőpaneljén válassza az **Application Insights** elemet.
   
    ![A Figyelés területen válassza az Application Insights lehetőséget](./media/app-insights-azure-web-apps/05-extend.png)
   
   * Válassza új erőforrás létrehozását, kivéve, ha korábban már beállított Application Insights-erőforrást ehhez az alkalmazáshoz egy másik útvonalon.
2. **Alakítsa ki webappját** az Application Insights telepítése után. 
   
    ![Webapp kialakítása](./media/app-insights-azure-web-apps/restart-web-app-for-insights.png)

   **Engedélyezze az ügyféloldali figyelést** a lapmegtekintések és a felhasználók telemetriai adatainak gyűjtéséhez.

   * Válassza a Beállítások > Alkalmazásbeállítások lehetőséget.
   * Az alkalmazásbeállításoknál adjon meg egy új kulcs-érték párt: 
   
    Kulcs: `APPINSIGHTS_JAVASCRIPT_ENABLED` 
    
    Érték: `true`
   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.
3. **Figyelje alkalmazását**.  [Vizsgálja meg az adatokat](#explore-the-data).

Később igény szerint felépítheti az alkalmazást az Application Insights segítségével.

*Hogyan távolíthatom el az Application Insightst vagy válthatok másik erőforrás küldésére?*

* Nyissa meg a webapp kezelőpaneljét az Azure-ban, és a Fejlesztőeszközök területen nyissa meg a **Bővítmények** panelt. Törölje az Application Insights bővítményt. Ezután a Figyelés területen válassza az Application Insights elemet, és hozza létre vagy válassza ki a kívánt erőforrást.

## <a name="build-the-app-with-application-insights"></a>Az alkalmazás építése az Application Insights segítségével
Ha telepít egy SDK-t alkalmazásába, az Application Insights részletesebb telemetriát biztosít. Például gyűjthet nyomkövetési naplókat, [egyéni telemetriát írhat](app-insights-api-custom-events-metrics.md), és részletesebb, kivételjelentéseket kaphat.

1. A **Visual Studióban** (2013 2. frissítés vagy újabb) konfigurálja az Application Insightst a projektjéhez.

    Kattintson a jobb gombbal a webes projektre, és válassza a **Hozzáadás -> Application Insights** vagy az **Application Insights konfigurálása** lehetőséget.
   
    ![Kattintson a jobb gombbal a webes projektre, és válassza a Hozzáadás vagy az Application Insights konfigurálása lehetőséget](./media/app-insights-azure-web-apps/03-add.png)
   
    Ha a rendszer arra kéri, hogy jelentkezzen be, használja az Azure-fiókjához tartozó hitelesítő adatokat.
   
    A műveletnek két hatása van:
   
   1. Létrehoz az Azure-ban egy Application Insights-erőforrást, ahol tárolja, elemzi és megjeleníti a telemetriát.
   2. Hozzáadja a kódhoz (ha korábban még nem szerepelt benne) az Application Insights NuGet-csomagot, és konfigurálja, hogy az telemetriát küldjön az Azure-erőforrásnak.
2. **Tesztelje a telemetriát** az alkalmazás a fejlesztői gépén való futtatásával (F5).
3. **Tegye közzé az alkalmazást** az Azure-ban a megszokott módon. 

*Hogyan válthatok más Application Insights-erőforrásnak való küldésre?*

* Kattintson a jobb gombbal a projektre a Visual Studióban, válassza az **Application Insights konfigurálása** lehetőséget, majd válassza ki a kívánt erőforrást. Megjelenik az új erőforrás létrehozásának lehetősége. Építse újra, és ismételten helyezze üzembe.

## <a name="explore-the-data"></a>Az adatok vizsgálata
1. Webappja kezelőpanelének Application Insights paneljén látható az Élő mérőszámok, amely egy-két másodperccel az előfordulásukat követően mutatja a kérelmeket és a hibákat. Hasznos funkció, ha ismételten teszi közzé alkalmazását, mert azonnal látható minden probléma.
2. Kattintással lépjen a teljes Application Insights-erőforráshoz.

    ![Végiglépkedés kattintással](./media/app-insights-azure-web-apps/view-in-application-insights.png)

    Az Azure-erőforrásnavigációból közvetlenül is odaléphet.

1. A részletekért kattintson végig bármelyik diagramon:
   
    ![Kattintson egy diagramra az Application Insights áttekintési paneljén](./media/app-insights-azure-web-apps/07-dependency.png)
   
    [Testreszabhatja a mérőszámpaneleket](app-insights-metrics-explorer.md).
2. Kattintson tovább az egyes események és tulajdonságaik megtekintéséhez:
   
    ![Kattintson egy eseménytípusra az adott típusra szűrő keresés megnyitásához](./media/app-insights-azure-web-apps/08-requests.png)
   
    A „...” hivatkozásra kattintva megnyithatja az összes tulajdonságot.
   
    [Testreszabhatja a kereséseket](app-insights-diagnostic-search.md).

Telemetriája még hatékonyabb kereséséhez használja a [Log Analytics lekérdezési nyelvet](app-insights-analytics-tour.md).

## <a name="more-telemetry"></a>További telemetria

* [Weblapbetöltési adatok](app-insights-javascript.md)
* [Egyéni telemetria](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>Következő lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](app-insights-profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](app-insights-azure-diagnostics.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../monitoring-and-diagnostics/insights-receive-alert-notifications.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](app-insights-javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](app-insights-monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.


