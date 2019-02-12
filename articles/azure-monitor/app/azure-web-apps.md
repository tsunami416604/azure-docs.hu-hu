---
title: Az Azure szolgáltatások teljesítményének figyelése |} A Microsoft Docs
description: Az alkalmazásteljesítmény-figyelés az Azure app services. Diagrambetöltési és -válaszidők függőségi információk és beállított, teljesítménnyel kapcsolatos riasztások.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: mbullwin
ms.openlocfilehash: 4ec6e1288b34f04350a88697ca13ed044922ee50
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56002242"
---
# <a name="monitor-azure-app-service-performance"></a>Az Azure App Service teljesítményének monitorozása
Az a [az Azure portal](https://portal.azure.com) beállíthat alkalmazásteljesítmény-figyelés a web apps, mobile háttérrendszerek és API-alkalmazások a [Azure App Service](../../app-service/overview.md). Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) úgy alakítja ki az alkalmazást, hogy telemetriát küldjön tevékenységeiről az Application Insights szolgáltatásnak, amely tárolja és elemzi azokat. Itt metrikus diagramok és keresőeszközök segítségével diagnosztizálhat problémákat, javíthatja a teljesítményt, és felmérheti a használatot.

## <a name="run-time-or-build-time"></a>Futási idő vagy felépítési idő
A figyelés konfigurálásához kétféle módon alakíthatja ki az alkalmazást:

* **Futási idő** -kiválaszthat egy teljesítményfigyelési bővítményt, ha már élő az app service. Nem szükséges újraépíteni vagy telepítse újra az alkalmazást. Olyan standard csomagkészletet kap, amely figyeli a válaszidőt, a sikerességi arányokat, a kivételeket, a függőségeket stb. 
* **Felépítési idő** – Telepíthet egy csomagot fejlesztés alatt álló alkalmazásába. Ez a lehetőség jóval sokoldalúbb. A standard csomagkészlet mellett kódolással testreszabhatja a telemetriát, vagy elküldheti saját telemetriáját. Naplózhat konkrét tevékenységeket vagy rögzíthet eseményeket alkalmazástartománya szemantikájának megfelelően. 

## <a name="run-time-instrumentation-with-application-insights"></a>Futási idő kialakítása az Application Insights segítségével
Melyeket már használ egy app Service-ben az Azure-ban, ha már kap bizonyos figyelőket: kérés és a hibák gyakoriságát. Adja hozzá az Application Insights további, például a válaszidők, a függőségek hívásainak figyelése, intelligens észlelés és a hatékony adatkezelő lekérdezési nyelv. 

1. **Válassza az Application Insights** az app service az Azure Vezérlőpultján.

    ![A beállítások területen válassza az Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Válassza ki, hozzon létre egy új erőforrást, kivéve, ha korábban már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

    > [!NOTE]
    > Amikor rákattint **OK** meg fogja kérni az új erőforrás létrehozása **figyelési beállítások alkalmazása**. Kiválasztásával **Folytatás** társítani fogja az új Application Insights-erőforrást az app service-ben is ezzel **újra kell indítani az app service-trigger**. 

    ![Webapp kialakítása](./media/azure-web-apps/create-resource.png)

2. Adjon meg, melyik erőforrást kell használnia, kiválaszthatja, hogyan szeretné az application insights a platformonként az alkalmazás adatainak gyűjtéséről. (Az alapértelmezés szerinti ASP.NET-alkalmazások monitorozása a gyűjtemény két különböző szintű.)

    ![Válassza a beállítások platformonként](./media/azure-web-apps/choose-options-new.png)

    * .NET **alapszintű gyűjtemény** szint alapvető egypéldányos APM funkciókat kínál.
    
    * .NET **gyűjtemény ajánlott** szintje:
        * Hozzáadja a Processzor, memória és i/o-használati trendeket.
        * Mikroszolgáltatások hátterében a kérelem/függőségi határokon.
        * Gyűjti a használati trendeket, és lehetővé teszi, hogy a tranzakciók rendelkezésre állási eredmények korrelációs.
        * A gazdagép által nem kezelt kivételek gyűjti.
        * Mintavétel használatakor, javítja a APM metrikák pontosságot terhelés alatt.
    
    .NET core kínál **gyűjtemény ajánlott** vagy le van tiltva, a .NET Core 2.0 és a 2.1-es verzióját.

3. **Alakítsa ki az app service** Application Insights telepítése után.

   **Az ügyféloldali figyelés engedélyezése** az oldal és a felhasználók telemetriai adatokat.

    (A .NET Core-alkalmazások alapértelmezés szerint engedélyezve van ez **gyűjtemény ajánlott**, függetlenül attól, hogy jelen-e az alkalmazás 'APPINSIGHTS_JAVASCRIPT_ENABLED' állítja. A részletes Felhasználóifelület-alapú támogatás letiltása az ügyféloldali figyelés jelenleg nem áll rendelkezésre a .NET Core.)
    
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
Ha telepít egy SDK-t alkalmazásába, az Application Insights részletesebb telemetriát biztosít. Például gyűjthet nyomkövetési naplókat, [egyéni telemetriát írhat](../../azure-monitor/app/api-custom-events-metrics.md), és részletesebb, kivételjelentéseket kaphat.

1. A **Visual Studióban** (2013 2. frissítés vagy újabb) konfigurálja az Application Insightst a projektjéhez.

    Kattintson a jobb gombbal a webes projektre, és válassza ki **Hozzáadás > Application Insights** vagy **projekt** > **Application Insights**  >  **Az Application Insights beállítása**.

    ![Kattintson a jobb gombbal a webes projektre, és válassza a Hozzáadás vagy az Application Insights konfigurálása lehetőséget](./media/azure-web-apps/03-add.png)

    Ha a rendszer arra kéri, hogy jelentkezzen be, használja az Azure-fiókjához tartozó hitelesítő adatokat.

    A műveletnek két hatása van:

   1. Létrehoz egy Application Insights-erőforrást az Azure-ban, ahol a telemetriai tárolja, elemzi és jelenik meg.
   2. Hozzáadja a kódhoz (ha korábban még nem szerepelt benne) az Application Insights NuGet-csomagot, és konfigurálja, hogy az telemetriát küldjön az Azure-erőforrásnak.
2. **Tesztelje a telemetriát** az alkalmazás a fejlesztői gépén való futtatásával (F5).
3. **Tegye közzé az alkalmazást** az Azure-ban a megszokott módon. 

*Hogyan válthatok más Application Insights-erőforrásnak való küldésre?*

* A Visual Studióban kattintson a jobb gombbal a projektre, válassza a **Application Insights konfigurálása**, és válassza ki a kívánt erőforrást. Megjelenik az új erőforrás létrehozásának lehetősége. Építse újra, és ismételten helyezze üzembe.

## <a name="more-telemetry"></a>További telemetria

* [Weblapbetöltési adatok](../../azure-monitor/app/javascript.md)
* [Egyéni telemetria](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED okoz NET CORE-webalkalmazások hiányos HTML-válaszban.

App Services Javascript engedélyezése okozhat a html-válaszok csonkolva.

* 1. megkerülő megoldás: APPINSIGHTS_JAVASCRIPT_ENABLED Alkalmazásbeállítás "false" értékűre, vagy távolítsa el teljesen, és indítsa újra a
* 2. megkerülő megoldás: hozzáadhatja az sdk-kód-n keresztül, és távolítsa el a bővítményt (Ez a konfiguráció nem Profiler és a pillanatkép-hibakereső)

Mi a probléma nyomon követ [Itt](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)

A .NET Core az alábbi jelenleg **nem támogatott**:

* Önálló telepítés.
* Az alkalmazások .NET-keretrendszerre.
* .NET Core 2.2 applications.

> [!NOTE]
> .NET core 2.0 és a .NET Core 2.1-es verziója támogatott. .NET Core 2.2 támogatási hozzáadásakor, ez a cikk fog frissülni.

## <a name="next-steps"></a>További lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](../../azure-monitor/app/profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../../azure-monitor/platform/data-collection.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../../azure-monitor/platform/alerts-overview.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](../../azure-monitor/app/javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](../../azure-monitor/app/monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.

