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
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 09084f71eda611c4b638a339d0d837e77e2639b9
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432311"
---
# <a name="monitor-azure-app-service-performance"></a>Az Azure App Service teljesítményének monitorozása
Az a [az Azure portal](https://portal.azure.com) beállíthat alkalmazásteljesítmény-figyelés a web apps, mobile háttérrendszerek és API-alkalmazások a [Azure App Service](../../app-service/overview.md). Az [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) úgy alakítja ki az alkalmazást, hogy telemetriát küldjön tevékenységeiről az Application Insights szolgáltatásnak, amely tárolja és elemzi azokat. Itt metrikus diagramok és keresőeszközök segítségével diagnosztizálhat problémákat, javíthatja a teljesítményt, és felmérheti a használatot.

## <a name="runtime-or-build-time"></a>Futásidejű vagy felépítési idő
A figyelés konfigurálásához kétféle módon alakíthatja ki az alkalmazást:

* **Futásidejű** -kiválaszthat egy teljesítményfigyelési bővítményt, ha már élő az app service. Nem szükséges újraépíteni vagy telepítse újra az alkalmazást. Olyan standard csomagkészletet kap, amely figyeli a válaszidőt, a sikerességi arányokat, a kivételeket, a függőségeket stb.

* **Felépítési idő** – Telepíthet egy csomagot fejlesztés alatt álló alkalmazásába. Ez a lehetőség jóval sokoldalúbb. A standard csomagkészlet mellett kódolással testreszabhatja a telemetriát, vagy elküldheti saját telemetriáját. Naplózhat konkrét tevékenységeket vagy rögzíthet eseményeket alkalmazástartománya szemantikájának megfelelően. Ez is biztosít az Application Insights SDK legújabb verziójának tesztelhetik, dönthet úgy, hogy kiértékelése béta SDK-k, mivel a legújabb stabil kiadásra a futásidejű ellenőrzés korlátozódik.

## <a name="runtime-instrumentation-with-application-insights"></a>Az Application Insights futásidejű rendszerállapot
Jelenleg az Azure-ban futtatja egy app Service-ben, ha már kap bizonyos figyelőket: alapértelmezés szerint a kérés és a hibák gyakoriságát. Adja hozzá az Application Insights további, például a válaszidők, a hívások figyelése, a függőségek, intelligens detektálás, valamint hozzáférés a Kusto a hatékony lekérdezési nyelvet. 

1. **Válassza az Application Insights** az app service az Azure Vezérlőpultján.

    ![A beállítások területen válassza az Application Insights](./media/azure-web-apps/settings-app-insights.png)

   * Válassza ki, hozzon létre egy új erőforrást, kivéve, ha korábban már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

    > [!NOTE]
    > Amikor rákattint **OK** meg fogja kérni az új erőforrás létrehozása **figyelési beállítások alkalmazása**. Kiválasztásával **Folytatás** társítani fogja az új Application Insights-erőforrást az app service-ben is ezzel **újra kell indítani az app service-trigger**. 

    ![Webapp kialakítása](./media/azure-web-apps/create-resource.png)

2. Adjon meg, melyik erőforrást kell használnia, kiválaszthatja, hogyan szeretné az application insights a platformonként az alkalmazás adatainak gyűjtéséről. ASP.NET alkalmazás figyelésére szolgáló az alapértelmezés szerint a gyűjtemény két különböző szintű.

    ![Válassza a beállítások platformonként](./media/azure-web-apps/choose-options-new.png)

    * .NET **alapszintű gyűjtemény** szint alapvető egypéldányos APM funkciókat kínál.
    
    * .NET **gyűjtemény ajánlott** szintje:
        * Hozzáadja a Processzor, memória és i/o-használati trendeket.
        * Mikroszolgáltatások hátterében a kérelem/függőségi határokon.
        * Gyűjti a használati trendeket, és lehetővé teszi, hogy a tranzakciók rendelkezésre állási eredmények korrelációs.
        * A gazdagép által nem kezelt kivételek gyűjti.
        * Mintavétel használatakor, javítja a APM metrikák pontosságot terhelés alatt.
    
    .NET core kínál **gyűjtemény ajánlott** vagy **letiltott** a .NET Core 2.0 és a 2.1-es verzióját.

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

## <a name="automate-monitoring"></a>Automatizálhatja a figyelése

Csak az alkalmazás beállításait, és engedélyezze a telemetriai adatok gyűjtése az Application insights segítségével kell állítani:

   ![App Service-Alkalmazásbeállítások elérhető Application Insights-beállításokkal](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Alkalmazásdefiníciók beállítások

|Alkalmazásbeállítás neve |  Meghatározás | Érték |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Fő bővítmény, amely azt vezérli, hogy a futásidejű ellenőrzés. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Az alapértelmezett mód csak, alapvető szolgáltatások engedélyezve vannak annak érdekében, hogy az optimális teljesítmény biztosítása érdekében. | `default` vagy `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Azt szabályozza, ha a bináris-újraírási motor `InstrumentationEngine` be lesz kapcsolva. Ez a beállítás a teljesítményre gyakorolt hatása van, és hatással van a ritkán használt start/indítási idejének. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | A függőségi hívások együtt szabályozza, hogy az SQL és az Azure table szöveg fogja rögzíteni. Teljesítménnyel kapcsolatos figyelmeztetés: Ez igényli a `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service-alkalmazás beállításai az Azure Resource Managerrel

Nastavení aplikace Pro App Services kezelhető és konfigurált [Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Ez a módszer használható, az Azure Resource Manager automation, illetve a meglévő erőforrások beállítások módosítása új App Service-erőforrások üzembe helyezésekor.

Egy app Service-Alkalmazásbeállítások JSON alapvető struktúráját alatt van:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

Példa egy Azure Resource Manager-sablon az alkalmazás beállításait konfigurálni az Application Insights ez [sablon](https://github.com/Andrew-MSFT/BasicImageGallery) hasznos lehet, kifejezetten a szakasz a kezdődően [238 sor](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Egy Application Insights-erőforrás és a kapcsolat az újonnan létrehozott App Service létrehozásának automatizálása.

Azure Resource Manager-sablon létrehozása minden az alapértelmezett beállításokkal Application Insights konfigurálása, a folyamat megkezdéséhez, ha voltak hozzon létre egy új webalkalmazást az Application Insights engedélyezve van.

Válassza ki **automatizálási beállítások**

   ![Az App Service web app létrehozása menü](./media/azure-web-apps/create-web-app.png)

Ez a legújabb Azure Resource Manager-sablon állít elő az összes szükséges beállítást.

  ![Az App Service webalkalmazás-sablon](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> A sablon létrehoz Alkalmazásbeállítások "alapértelmezett" módban. Ebben a módban a teljesítményre optimalizált, azonban módosíthatja a sablon igény szerint bármelyik funkciók aktiválásához.

## <a name="more-telemetry"></a>További telemetria

* [Weblapbetöltési adatok](../../azure-monitor/app/javascript.md)
* [Egyéni telemetria](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>Is meg kell bővítmények grás – Hozzáadás – új App Service-alkalmazások az Application Insights bővítményt?

Nem, nem kell többé manuálisan adja hozzá a bővítményt. Az Application Insights engedélyezése a beállítások panelen keresztül felveszi az összes a szükséges alkalmazás beállításait, hogy engedélyezze a monitorozást. Ez a lehetőség most, mivel a bővítmény által korábban hozzáadott fájlok mostantól [előtelepített](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) az App Service-kép részeként. A fájlok találhatók `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>Ha-futtatókörnyezet és a felépítési idő figyelése egyaránt engedélyezve van do I kialakított duplikált adatok?

Nem, alapértelmezés szerint felépítési idő figyelési észlelésekor futásidejű megfigyelése a bővítményével leállítja az adatok küldésének és csak a figyelés konfigurációja build alkalommal fog lesz érvényes. A meghatározását, hogy letiltja a futásidejű ellenőrzés-e ezek a fájlok észlelésének alapul:

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* System.Diagnostics.DiagnosticSource dll

Fontos, hogy sok verziója a Visual Studio néhány vagy összes fájl rendeljük hozzá alapértelmezés szerint az ASP.NET és az ASP.NET Core Visual Studio sablonfájlokat szem előtt tartani. Ha a projekt engedményt egyik sablont alapján készült, akkor is, ha explicit módon még nem engedélyezte az Application Insights, a fájl függőség jelenléte megakadályozná a futásidejű figyelés aktiválása.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED okoz NET CORE-webalkalmazások hiányos HTML-válaszban.

App Services Javascript engedélyezése okozhat a html-válaszok csonkolva.

* 1. megkerülő megoldás: APPINSIGHTS_JAVASCRIPT_ENABLED Alkalmazásbeállítás "false" értékűre, vagy távolítsa el teljesen, és indítsa újra a
* 2. megkerülő megoldás: hozzáadhatja az sdk-kód-n keresztül, és távolítsa el a bővítményt (Ez a konfiguráció a Profiler és a pillanatkép-hibakereső nem fog működni)

A probléma nyomon követéséhez nyissa meg [okozó hiányos HTML-válaszban az Azure-bővítmény](https://github.com/Microsoft/ApplicationInsights-Home/issues/277).

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