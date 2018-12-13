---
title: Azure-webapp teljesítményének figyelése | Microsoft Docs
description: Alkalmazásteljesítmény figyelése Azure-webappok esetében. Diagrambetöltési és -válaszidők függőségi információk és beállított, teljesítménnyel kapcsolatos riasztások.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: 764ca0203a418ce29c942d38d8fad5bbc00c071b
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321749"
---
# <a name="monitor-azure-web-app-performance"></a>Azure-webapp teljesítményének figyelése
Az [Azure Portalon](https://portal.azure.com) beállíthat alkalmazásteljesítmény-figyelést [Azure-webappjaihoz](../app-service/app-service-web-overview.md). Az [Azure Application Insights](app-insights-overview.md) úgy alakítja ki az alkalmazást, hogy telemetriát küldjön tevékenységeiről az Application Insights szolgáltatásnak, amely tárolja és elemzi azokat. Itt metrikus diagramok és keresőeszközök segítségével diagnosztizálhat problémákat, javíthatja a teljesítményt, és felmérheti a használatot.

## <a name="run-time-or-build-time"></a>Futási idő vagy felépítési idő
A figyelés konfigurálásához kétféle módon alakíthatja ki az alkalmazást:

* **Futási idő** – Kiválaszthat egy teljesítményfigyelési bővítményt, amikor a webapp már működik. Nem szükséges újraépíteni vagy újratelepíteni az alkalmazást. Olyan standard csomagkészletet kap, amely figyeli a válaszidőt, a sikerességi arányokat, a kivételeket, a függőségeket stb. 
* **Felépítési idő** – Telepíthet egy csomagot fejlesztés alatt álló alkalmazásába. Ez a lehetőség jóval sokoldalúbb. A standard csomagkészlet mellett kódolással testreszabhatja a telemetriát, vagy elküldheti saját telemetriáját. Naplózhat konkrét tevékenységeket vagy rögzíthet eseményeket alkalmazástartománya szemantikájának megfelelően. 

## <a name="run-time-instrumentation-with-application-insights"></a>Futási idő kialakítása az Application Insights segítségével
Ha már futtat egy webappot az Azure-ban, a kéréseket és a hibák gyakoriságát a rendszer már figyeli. Az Application Insights hozzáadásával további figyelési funkciók érhetők el, például a válaszidők figyelése, a függőségek hívásainak figyelése, az intelligens észlelés és a hatékony Log Analytics lekérdezési nyelv. 

1. Webappja Azure-kezelőpaneljén válassza az **Application Insights** elemet.

    ![A beállítások területen válassza az Application Insights](./media/app-insights-azure-web-apps/settings-app-insights.png)

   * Válassza ki, hozzon létre egy új erőforrást, kivéve, ha korábban már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

    > [!NOTE]
    > Amikor rákattint **OK** meg fogja kérni az új erőforrás létrehozása **figyelési beállítások alkalmazása**. Kiválasztásával **Folytatás** társítani fogja az új Application Insights-erőforrást a web app alkalmazásban is ezzel **kezdeményezzen újraindítást a webalkalmazás**. 

    ![Webapp kialakítása](./media/app-insights-azure-web-apps/create-resource.png)

2. Adjon meg, melyik erőforrást kell használnia, kiválaszthatja, hogyan szeretné az application insights a platformonként az alkalmazás adatainak gyűjtéséről.

    ![Válassza a beállítások platformonként](./media/app-insights-azure-web-apps/choose-options.png)

3. **Alakítsa ki webappját** az Application Insights telepítése után.

   **Engedélyezze az ügyféloldali figyelést** a lapmegtekintések és a felhasználók telemetriai adatainak gyűjtéséhez.

   * Válassza a Beállítások > Alkalmazásbeállítások lehetőséget.
   * Az alkalmazásbeállításoknál adjon meg egy új kulcs-érték párt:

    Kulcs: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    Érték: `true`
   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.
4. Az alkalmazás figyelési adatok megismerése kattintva **beállítások** > **Application Insights** > **megtekintése további az Application Insights**.

Később igény szerint felépítheti az alkalmazást az Application Insights segítségével.

*Hogyan távolíthatom el az Application Insightst vagy válthatok másik erőforrás küldésére?*

* Az Azure-ban nyissa meg a webalkalmazás panelére vezérlő, és nyissa meg a beállítások **Application Insights**. Kapcsolhatja ki az Application Insights gombra kattintva **letiltása** tetején, vagy válasszon ki egy új erőforrás a **módosítsa az erőforrás** szakaszban.

## <a name="build-the-app-with-application-insights"></a>Az alkalmazás építése az Application Insights segítségével
Ha telepít egy SDK-t alkalmazásába, az Application Insights részletesebb telemetriát biztosít. Például gyűjthet nyomkövetési naplókat, [egyéni telemetriát írhat](app-insights-api-custom-events-metrics.md), és részletesebb, kivételjelentéseket kaphat.

1. A **Visual Studióban** (2013 2. frissítés vagy újabb) konfigurálja az Application Insightst a projektjéhez.

    Kattintson a jobb gombbal a webes projektre, és válassza ki **Hozzáadás > Application Insights** vagy **projekt** > **Application Insights**  >  **Az Application Insights beállítása**.

    ![Kattintson a jobb gombbal a webes projektre, és válassza a Hozzáadás vagy az Application Insights konfigurálása lehetőséget](./media/app-insights-azure-web-apps/03-add.png)

    Ha a rendszer arra kéri, hogy jelentkezzen be, használja az Azure-fiókjához tartozó hitelesítő adatokat.

    A műveletnek két hatása van:

   1. Létrehoz az Azure-ban egy Application Insights-erőforrást, ahol tárolja, elemzi és megjeleníti a telemetriát.
   2. Hozzáadja a kódhoz (ha korábban még nem szerepelt benne) az Application Insights NuGet-csomagot, és konfigurálja, hogy az telemetriát küldjön az Azure-erőforrásnak.
2. **Tesztelje a telemetriát** az alkalmazás a fejlesztői gépén való futtatásával (F5).
3. **Tegye közzé az alkalmazást** az Azure-ban a megszokott módon. 

*Hogyan válthatok más Application Insights-erőforrásnak való küldésre?*

* Kattintson a jobb gombbal a projektre a Visual Studióban, válassza az **Application Insights konfigurálása** lehetőséget, majd válassza ki a kívánt erőforrást. Megjelenik az új erőforrás létrehozásának lehetősége. Építse újra, és ismételten helyezze üzembe.

## <a name="more-telemetry"></a>További telemetria

* [Weblapbetöltési adatok](app-insights-javascript.md)
* [Egyéni telemetria](app-insights-api-custom-events-metrics.md)

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>További lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](app-insights-profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](../azure-monitor/platform/diagnostics-extension-to-application-insights.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../azure-monitor/platform/data-collection.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../monitoring-and-diagnostics/monitoring-overview-alerts.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](app-insights-javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](app-insights-monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.

