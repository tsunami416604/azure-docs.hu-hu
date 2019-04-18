---
title: Az Azure szolgáltatások teljesítményének figyelése |} A Microsoft Docs
description: Az alkalmazásteljesítmény-figyelés az Azure app services. Diagram betöltése és a válaszidő, a függőségi adatokat, és riasztásokat állíthat be a teljesítményre.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 25f620cb36c2bfb548ecf08c33dc04b37118a256
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489622"
---
# <a name="monitor-azure-app-service-performance"></a>Az Azure App Service teljesítményének monitorozása

Engedélyezni a figyelést a .NET és .NET Core-alapú futó webalkalmazásoknál [Azure App Services](https://docs.microsoft.com/azure/app-service/) , minden eddiginél egyszerűbbé teszi. Mivel korábban szükséges webhelybővítmény manuális telepítéséhez, a legújabb bővítési ügynök most már be van építve az app service lemezkép alapértelmezés szerint. Ez a cikk fog végigvezetik Application insights általi figyelés engedélyezése, valamint a nagyobb méretű környezetek esetében a folyamat automatizálásának előzetes útmutatást nyújtanak.

> [!NOTE]
> Manuálisan hozzáadni egy Application Insights-webhelybővítményt keresztül **Fejlesztőeszközök** > **bővítmények** elavult. Ezzel a módszerrel telepíteni a bővítményt minden egyes új verzió frissítései manuális függött. A bővítmény legújabb stabil kiadása már [előtelepített](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) az App Service-kép részeként. A fájlok találhatók `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` és automatikusan frissülnek minden stabil kiadással. Ha az ügynök utasítások figyelés engedélyezése az alábbiakban, automatikusan eltávolítja az elavult bővítmény az Ön számára.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Alkalmazások figyelése az Azure App Servicesben üzemeltetett alkalmazások engedélyezéséhez két módja van:

* **Az ügynök-alapú alkalmazásfigyelés** (ApplicationInsightsAgent).  
    * Ez a módszer engedélyezéséhez a legegyszerűbb, és semmilyen speciális konfigurációra szükség. Ezt gyakran nevezik "runtime" figyelése. Az Azure App Services azt javasoljuk ezt a szintű figyelés engedélyezése legalább, és majd kiértékelheti, hogy fejlett figyelési manuális instrumentation keresztül van szükség az adott forgatókönyv alapján.

* **Kézileg állítja be az alkalmazást kód** az Application Insights SDK telepítésével.

    * Ez a módszer sokkal testre szabható, de szükséges [függőség hozzáadása az Application Insights SDK NuGet-csomagok](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Ez a módszer azt is jelenti, kezelheti a frissítéseket a legújabb verzióra a csomagok saját magának kell.

    * Ha kell, hogy egyéni API-hívások nyomon követheti az eseményeket/függőségek nem rögzíti az ügynök-alapú figyelés alapértelmezés szerint, kell ezt a módszert használja. Tekintse meg a [API-val egyéni eseményeket és mérőszámokat cikk](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) további.

> [!NOTE]
> Az ügynök-alapú figyelési és a manuális SDK-alapú instrumentation észlel csak a manuális instrumentation beállításait fogja lesz érvényes. Ez az, hogy a duplikált adatok, küldeni. További információ a tekintse meg a [hibaelhárítás szakaszhoz](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) alatt.

## <a name="enable-agent-based-monitoring-net"></a>Az ügynök-alapú figyelés .NET engedélyezése

1. **Válassza az Application Insights** az app service az Azure Vezérlőpultján.

    ![A beállítások területen válassza az Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Válassza ki, hozzon létre egy új erőforrást, kivéve, ha korábban már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor rákattint **OK** meg fogja kérni az új erőforrás létrehozása **figyelési beállítások alkalmazása**. Kiválasztásával **Folytatás** társítani fogja az új Application Insights-erőforrást az app service-ben is ezzel **újra kell indítani az app service-trigger**. 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Adjon meg, melyik erőforrást kell használnia, kiválaszthatja, hogyan szeretné az application insights a platformonként az alkalmazás adatainak gyűjtéséről. ASP.NET alkalmazás figyelésére szolgáló az alapértelmezés szerint a gyűjtemény két különböző szintű.

    ![Válassza a beállítások platformonként](./media/azure-web-apps/choose-options-new.png)

   * .NET **alapszintű gyűjtemény** szint alapvető egypéldányos APM funkciókat kínál.

   * .NET **gyűjtemény ajánlott** szintje:
       * Hozzáadja a Processzor, memória és i/o-használati trendeket.
       * Mikroszolgáltatások hátterében a kérelem/függőségi határokon.
       * Gyűjti a használati trendeket, és lehetővé teszi, hogy a tranzakciók rendelkezésre állási eredmények korrelációs.
       * A gazdagép által nem kezelt kivételek gyűjti.
       * Mintavétel használatakor, javítja a APM metrikák pontosságot terhelés alatt.

3. Például mintavételezése esetén, amely korábban vezérelheti az applicationinsights.config fájlban keresztül-beállítások konfigurálása most már használhatja azokat a megfelelő előtaggal Alkalmazásbeállítások keresztül ugyanazokat a beállításokat. 

    * Például ha módosítani szeretné a kezdeti mintavételi arányt, hozhat létre az alkalmazás-beállítás: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` és a egy értéke `100`.

    * Támogatott adaptív mintavételezés telemetriai processzor beállítások listáját, olvassa az [kód](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) és [kapcsolódó dokumentáció](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Az ügynök-alapú figyelés .NET Core engedélyezése

Az alábbi .NET Core-verziók támogatottak: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

A .NET Core, önálló telepítés és az ASP.NET Core 3.0 a teljes keretrendszer célzó jelenleg **nem támogatott** figyelési ügynök/bővítmény alapján. ([Manuális instrumentation](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) keresztül kódot fog működni az összes korábbi forgatókönyv esetében.)

1. **Válassza az Application Insights** az app service az Azure Vezérlőpultján.

    ![A beállítások területen válassza az Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Válassza ki, hozzon létre egy új erőforrást, kivéve, ha korábban már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor rákattint **OK** meg fogja kérni az új erőforrás létrehozása **figyelési beállítások alkalmazása**. Kiválasztásával **Folytatás** társítani fogja az új Application Insights-erőforrást az app service-ben is ezzel **újra kell indítani az app service-trigger**. 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Adjon meg, melyik erőforrást kell használnia, kiválaszthatja, hogyan történjen az Application Insights a platformonként az alkalmazás adatainak gyűjtéséről. .NET core kínál **gyűjtemény ajánlott** vagy **letiltott** a .NET Core 2.0, a 2.1-es és a 2.2-es.

    ![Válassza a beállítások platformonként](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Ügyféloldali figyelés .NET engedélyezése

Feliratkozás az ASP.NET az ügyféloldali figyelés. Az ügyféloldali figyelés engedélyezése:

* Válassza ki **beállítások** > ** ** alkalmazás beállítások x
   * Alkalmazás beállításaiban, vegyen fel egy új **alkalmazásbeállítás neve** és **érték**:

     név: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `true`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

![Képernyőkép az alkalmazás felhasználói felület beállításai](./media/azure-web-apps/appinsights-javascript-enabled.png)

Tiltsa le az ügyféloldali figyelés vagy távolítsa el a társított kulcs-érték párt a az alkalmazás beállításait, vagy állítsa az értékét HAMIS értékre.

## <a name="enable-client-side-monitoring-net-core"></a>Ügyféloldali figyelés .NET Core engedélyezése

Az ügyféloldali figyelés **alapértelmezés szerint engedélyezett** for .NET Core-alkalmazásokat **gyűjtemény ajánlott**, függetlenül attól, hogy jelen-e az alkalmazás 'APPINSIGHTS_JAVASCRIPT_ENABLED' állítja.

Ha valamilyen okból, tiltsa le az ügyféloldali figyelést szeretné:

* Válassza ki **beállítások** > **nastavení Aplikace**
   * Alkalmazás beállításaiban, vegyen fel egy új **alkalmazásbeállítás neve** és **érték**:

     név: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `false`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

![Képernyőkép az alkalmazás felhasználói felület beállításai](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizálhatja a figyelése

Annak érdekében, hogy az Application Insights telemetriai adatok gyűjtésének engedélyezéséhez, csak az alkalmazás beállításait meg kell adnia:

   ![App Service-Alkalmazásbeállítások elérhető Application Insights-beállításokkal](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Alkalmazásdefiníciók beállítások

|Alkalmazásbeállítás neve |  Meghatározás | Érték |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Fő bővítmény, amely azt vezérli, hogy a futásidejű ellenőrzés. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Az alapértelmezett mód csak, alapvető szolgáltatások engedélyezve vannak annak érdekében, hogy az optimális teljesítmény biztosítása érdekében. | `default` vagy `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Azt szabályozza, ha a bináris-újraírási motor `InstrumentationEngine` be lesz kapcsolva. Ez a beállítás a teljesítményre gyakorolt hatása van, és hatással van a ritkán használt start/indítási idejének. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | A függőségi hívások együtt szabályozza, hogy az SQL és az Azure table szöveg fogja rögzíteni. Teljesítménnyel kapcsolatos figyelmeztetés: Ez a beállítás megköveteli az `InstrumentationEngine`. | `~1` |

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

Példa egy Azure Resource Manager-sablon az Alkalmazásbeállítások konfigurálása az Application Insights ez [sablon](https://github.com/Andrew-MSFT/BasicImageGallery) hasznos lehet, kifejezetten a szakasz a kezdődően [238 sor](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Egy Application Insights-erőforrás és a kapcsolat az újonnan létrehozott App Service létrehozásának automatizálása.

Azure Resource Manager-sablon létrehozása minden az alapértelmezett beállításokkal Application Insights konfigurálása, a folyamat megkezdéséhez, ha voltak hozzon létre egy új webalkalmazást az Application Insights engedélyezve van.

Válassza ki **automatizálási beállítások**

   ![Az App Service web app létrehozása menü](./media/azure-web-apps/create-web-app.png)

Ez a beállítás az összes szükséges beállítást állít elő, a legújabb Azure Resource Manager-sablon.

  ![Az App Service webalkalmazás-sablon](./media/azure-web-apps/arm-template.png)

Alul látható egy minta lecserélendő `AppMonitoredSite` a webhely neve:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> A sablon létrehoz Alkalmazásbeállítások "alapértelmezett" módban. Ebben a módban a teljesítményre optimalizált, azonban módosíthatja a sablon igény szerint bármelyik funkciók aktiválásához.

### <a name="enabling-through-powershell"></a>PowerShell-lel engedélyezése

Ahhoz, hogy az alkalmazás figyelése a PowerShell-lel, csak az alapul szolgáló alkalmazásbeállításokat módosítani kell. Alább egy minta, amely lehetővé teszi az alkalmazásfigyelés "AppMonitoredSite" nevű erőforráscsoportban "AppMonitoredRG" webhelyekhez, és konfigurálja a "012345678-abcd-ef01-2345-6789abcd" kialakítási kulcs küldendő adatok.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Figyelési bővítmény-ügynökök frissítése

### <a name="upgrading-from-versions-289-and-up"></a>2.8.9 verziókról és mentése frissítése

Történő frissítéskor 2.8.9 automatikusan történik, minden további műveletek nélkül. Az új figyelési bits érkezzenek a célként megadott app Service a háttérben, és az alkalmazás-újraindítás csak azt követően dolgozza.

A bővítmény verziójának ellenőrzéséhez keresse fel futtatja `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Képernyőkép az URL-cím http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Frissítés a 2.6.5 1.0.0 - verziók

2.8.9 verziótól kezdve az előre telepített webhelybővítményt szolgál. Ha egy korábbi, keresztül két módszer egyikével frissítheti:

* [A portálon keresztül engedélyezésével frissítési](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Akkor is, ha az Azure App Service telepítve van az Application Insights bővítményt, a felhasználói felület megjelenítése csak **engedélyezése** gombra. A színfalak mögött a régi privát webhelybővítmény távolítja el.)

* [Frissítés a PowerShell-lel](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Az előre telepített webhelybővítményt ApplicationInsightsAgent engedélyezése az alkalmazás beállításainak megadása Lásd: [engedélyezése a powershell-lel](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Távolítsa el manuálisan az Application Insights bővítményt nevű az Azure App Service privát webhelybővítményt.

Ha a frissítés előtt 2.5.1-es verziójában történik, ellenőrizze, hogy a ApplicationInsigths dll-fájlok törlődnek az alkalmazás bin mappájában [talál a hibaelhárítási lépéseket](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbi, a részletes hibaelhárítási útmutató a bővítmény/ügynök-alapú figyelés .NET-hez készült, és a .NET Core-alapú alkalmazások futtatása az Azure App Servicesben.

> [!NOTE]
> A Java és Node.js alkalmazások csak manuális alapú SDK instrumentation keresztül támogatott az Azure App Services szolgáltatásban, és ezért az alábbi lépések nem vonatkoznak ezek a forgatókönyvek.

1. Ellenőrizze, hogy az alkalmazás-n keresztül figyelt `ApplicationInsightsAgent`.
    * Ellenőrizze, hogy `ApplicationInsightsAgent_EXTENSION_VERSION` Alkalmazásbeállítás "~ 2" értékre van állítva.
2. Győződjön meg arról, hogy az alkalmazás megfelel-e figyelni a követelményeknek.
    * Keresse meg a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Képernyőkép a https://yoursitename.scm.azurewebsites/applicationinsights eredmények lap](./media/azure-web-apps/app-insights-sdk-status.png)

    * Ellenőrizze, hogy a `Application Insights Extension Status` van `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Ha nem fut, kövesse a [utasításokat figyelése az Application Insights engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Győződjön meg arról, hogy az állapot forrás létezik, és néz ki: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Ha egy hasonló értéke nem található, az azt jelenti, az alkalmazás jelenleg nem fut, vagy nem támogatott. Győződjön meg arról, hogy az alkalmazás fut, próbálja meg manuálisan felkeresése a URL-cím vagy alkalmazás végpontjainak, amely lehetővé teszi, hogy elérhető legyen, a futásidejű információkat.

    * Ellenőrizze, hogy `IKeyExists` van `true`
        * Ha FALSE (hamis), adja hozzá a "állítani az APPINSIGHTS_INSTRUMENTATIONKEY való az alkalmazás beállításait a rendszerállapotkulcsot GUID azonosítót.

    * Győződjön meg arról, hogy nincsenek-e a bejegyzések `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, és `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Ha ezek a bejegyzések bármelyikét létezik, a következő csomagok eltávolítása az alkalmazásból: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, és `Microsoft.AspNet.TelemetryCorrelation`.

Az alábbi táblázat ezek az értékek jelentése több részletes leírását tartalmazza, az alapul szolgáló okozza, és javasolt javítások:

|A probléma érték|Magyarázat|Javítás
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ez azt jelzi, hogy a bővítmény észlelte, hogy az SDK bizonyos elemeit már szerepel az alkalmazást, és fog visszatartás. Hivatkozás miatt lehet `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, vagy `Microsoft.ApplicationInsights`  | Távolítsa el a hivatkozást. Néhány mutató hivatkozásokat hozzáadja az alapértelmezés szerint egyes Visual Studio-sablonok alapján, és a Visual Studio régebbi verzióiban előfordulhat, hogy adja hozzá a hivatkozásokat `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Ha a .NET Core 2.1-es vagy a 2.2-es van-e állítva az alkalmazást, és hivatkozik [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-csomagot, majd lehetővé teszi az Application insights szolgáltatásban, és a bővítmény fog visszatartási. | A .NET Core 2.1,2.2 ügyfeleinknek [ajánlott](https://github.com/aspnet/Announcements/issues/287) helyette használhatja Microsoft.AspNetCore.App meta-csomagot.|
|`AppAlreadyInstrumented:true` | Ez az érték a fenti DLL-fájlja egy korábbi központi telepítés az alkalmazás mappájában jelenlétének is okozhatja. | Tisztítsa meg az alkalmazás mappája, győződjön meg arról, hogy ezek a DLL fájlok törlődnek.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ez az érték azt jelzi, hogy a bővítmény mutató hivatkozásokat észlelt `Microsoft.AspNet.TelemetryCorrelation` az alkalmazásban, és a rendszer visszatartás. | Távolítsa el a hivatkozást.
|`AppContainsDiagnosticSourceAssembly**:true`|Ez az érték azt jelzi, hogy a bővítmény mutató hivatkozásokat észlelt `System.Diagnostics.DiagnosticSource` az alkalmazásban, és a rendszer visszatartás.| Távolítsa el a hivatkozást.
|`IKeyExists:false`|Ez az érték azt jelzi, hogy a kialakítási kulcsot nem szerepel az Alkalmazásbeállítás `APPINSIGHTS_INSTRUMENTATIONKEY`. Lehetséges okok: Az értékek véletlenül eltávolításra kerültek, elfelejtette állítsa az értékeket az automation-szkript stb. | Ellenőrizze, hogy a beállítás szerepel az App Service-alkalmazás beállításait.

Az Application Insights-ügynök/bővítményt a legfrissebb információkért tekintse meg a [kibocsátási megjegyzések](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>További lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](../app/profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../platform/data-platform.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../platform/alerts-overview.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.
