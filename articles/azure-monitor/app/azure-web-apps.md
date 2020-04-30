---
title: Az Azure app Services teljesítményének figyelése | Microsoft Docs
description: Az alkalmazások teljesítményének figyelése az Azure app Servicesben. A diagram betöltésének és a válaszidő, a függőségi adatok és a riasztások beállítása a teljesítményre.
ms.topic: conceptual
ms.date: 12/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: dd0d3be6ed7e5185183618cc2bdeff5ee8d749f3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81729794"
---
# <a name="monitor-azure-app-service-performance"></a>Az Azure App Service teljesítményének monitorozása

Az [Azure app Services](https://docs.microsoft.com/azure/app-service/) -on futó ASP.NET és ASP.net Core-alapú webalkalmazások figyelésének engedélyezése mostantól minden eddiginél egyszerűbb. Mivel korábban a hely kiterjesztésének manuális telepítésére volt szükség, alapértelmezés szerint a legújabb bővítmény/ügynök már be van építve az App Service-lemezképbe. Ebből a cikkből megtudhatja, hogyan engedélyezheti Application Insights monitorozását, valamint előzetes útmutatást nyújt a nagyméretű központi telepítések folyamatának automatizálásához.

> [!NOTE]
> Application Insights hely bővítményének manuális hozzáadása a **fejlesztői eszközök** > **bővítményein** keresztül elavult. Ez a bővítmény-telepítési módszer az egyes új verziók manuális frissítéseitől függ. A bővítmény legújabb stabil kiadása mostantól a App Service rendszerkép részeként van [előtelepítve](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) . A fájlok a ben `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` találhatók, és automatikusan frissülnek az egyes stabil kiadásokkal. Ha követi az ügynök-alapú utasításokat az alábbi figyelés engedélyezéséhez, az automatikusan eltávolítja az elavult bővítményt.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Az alkalmazások figyelését kétféleképpen engedélyezheti az Azure App Services üzemeltetett alkalmazásaiban:

* **Ügynök-alapú alkalmazás figyelése** (ApplicationInsightsAgent).  
    * Ez a módszer a legkönnyebben engedélyezhető, és nincs szükség speciális konfigurációra. Ezt gyakran "futtatókörnyezet"-figyelőnek nevezzük. Az Azure App Services legalább ezt a monitorozási szintet ajánlott engedélyezni, majd az adott forgatókönyv alapján kiértékelheti, hogy a manuális rendszerállapotra vonatkozó fejlettebb figyelésre van-e szükség.

* Az **alkalmazást a programkódon keresztül manuálisan** , a Application Insights SDK telepítésével végezheti el.

    * Ez a megközelítés sokkal testreszabható, de a [Application INSIGHTS SDK NuGet-csomagokhoz való függőség hozzáadását](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)igényli. Ez a módszer azt is jelenti, hogy a frissítéseket a csomagok legújabb verziójára kell kezelnie.

    * Ha egyéni API-hívásokat kell megadnia az ügynök-alapú figyeléssel alapértelmezés szerint nem rögzített események/függőségek nyomon követéséhez, ezt a metódust kell használnia. További információért tekintse meg az [Egyéni események és mérőszámok API](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) -ját ismertető cikket. Ez jelenleg csak a Linux-alapú számítási feladatok esetében támogatott.

> [!NOTE]
> Ha az ügynök-alapú figyelés és a manuális SDK-alapú kialakítás is észlelhető, a rendszer csak a manuális rendszerállapot-beállításokat fogja figyelembe venni. Ez megakadályozza az ismétlődő adatok küldését. Ha többet szeretne megtudni erről, tekintse meg az alábbi [hibaelhárítási szakaszt](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) .

## <a name="enable-agent-based-monitoring"></a>Ügynök alapú figyelés engedélyezése

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> A APPINSIGHTS_JAVASCRIPT_ENABLED és a urlCompression kombinációja nem támogatott. További információ: a [Hibaelhárítás szakasz](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)magyarázata.


1. Az App Service-hez tartozó Azure Vezérlőpulton **válassza a Application Insights lehetőséget** .

    ![A beállítások területen válassza a Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Válassza az új erőforrás létrehozása lehetőséget, ha már beállított egy Application Insights erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor az **OK** gombra kattint az új erőforrás létrehozásához, a rendszer kérni fogja a **figyelési beállítások alkalmazására**. A **Folytatás** elem kiválasztásával összekapcsolja az új Application Insights erőforrást az App Service-be, így az **app Service újraindítását is elindíthatja**. 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Miután meghatározta, hogy melyik erőforrást szeretné használni, kiválaszthatja, hogy az Application-elemzések hogyan gyűjthetnek adatokat egy platformon az alkalmazás számára. A ASP.NET-alkalmazás figyelése alapértelmezés szerint két különböző szintű gyűjteménysel van ellátva.

    ![Beállítások kiválasztása platformon](./media/azure-web-apps/choose-options-new.png)
 
 Az alábbiakban az egyes útvonalakon összegyűjtött adatok összegzése látható:
        
|  | .NET alapszintű gyűjtemény | .NET ajánlott gyűjtemény |
| --- | --- | --- |
| Processzor-, memória- és I/O-használati trendek hozzáadása |Igen |Igen |
| Használati trendek gyűjtése, a rendelkezésreállási eredmények és a tranzakciók összevetése | Igen |Igen |
| A gazdafolyamat által nem kezelt kivételek gyűjtése | Igen |Igen |
| Az APM-metrikák pontosságának növelése terhelés alatt, mintavételezés használatakor | Igen |Igen |
| Mikroszolgáltatások összevetése kérési és függőségi határokon keresztül | Nem (csak egypéldányos APM-képességek) |Igen |

3. Az olyan beállítások konfigurálásához, mint például a mintavételezés, amelyet korábban a applicationinsights. config fájlon keresztül kezelhet, mostantól a megfelelő előtaggal használhatja ugyanezeket a beállításokat az Alkalmazásbeállítások használatával. 

    * Például a kezdeti mintavételi százalék módosításához hozzon létre egy alkalmazás-beállítást: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` és egy értéket. `100`

    * A támogatott adaptív mintavételi telemetria processzor-beállításainak listájáért tekintse meg a [kódot](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) és a [kapcsolódó dokumentációt](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

A .NET Core következő verziói támogatottak: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2, ASP.NET Core 3,0

A .NET Core, az önálló üzemelő példányok és a Linux-alapú alkalmazások teljes keretrendszerének megcélzása jelenleg **nem támogatott** az ügynök/bővítmény alapú figyeléssel. (A programkódon keresztüli[manuális](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) kialakítás az összes korábbi forgatókönyvben működni fog.)

1. Az App Service-hez tartozó Azure Vezérlőpulton **válassza a Application Insights lehetőséget** .

    ![A beállítások területen válassza a Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Válassza az új erőforrás létrehozása lehetőséget, ha már beállított egy Application Insights erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor az **OK** gombra kattint az új erőforrás létrehozásához, a rendszer kérni fogja a **figyelési beállítások alkalmazására**. A **Folytatás** elem kiválasztásával összekapcsolja az új Application Insights erőforrást az App Service-be, így az **app Service újraindítását is elindíthatja**. 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Miután meghatározta, hogy melyik erőforrást szeretné használni, kiválaszthatja, hogy a Application Insights hogyan gyűjtsön adatokat egy platformon az alkalmazás számára. A .NET Core a .NET Core 2,0, 2,1, 2,2 és 3,0 **ajánlott gyűjteményét** vagy **letiltását** ajánlja.

    ![Beállítások kiválasztása platformon](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A app Service webalkalmazás **Beállítások** > területén**válassza a Application Insights** > **Engedélyezés**lehetőséget. A Node. js-ügynök alapú figyelés jelenleg előzetes verzióban érhető el.

# <a name="java"></a>[Java](#tab/java)

A Java App Service-alapú webalkalmazások jelenleg nem támogatják az automatikus ügynök/bővítmény alapú figyelést. A Java-alkalmazás figyelésének engedélyezéséhez manuálisan kell megadnia [az alkalmazást](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

A Python App Service-alapú webalkalmazások jelenleg nem támogatják az automatikus ügynök/bővítmény alapú figyelést. A Python-alkalmazás figyelésének engedélyezéséhez manuálisan kell megadnia [az alkalmazást](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Ügyféloldali figyelés engedélyezése

# <a name="net"></a>[.NET](#tab/net)

Az ügyféloldali figyelés ASP.NET. Az ügyféloldali figyelés engedélyezése:

* **Beállítások** kiválasztása > * * * * alkalmazás beállításai * * * *
   * Az Alkalmazásbeállítások területen adjon hozzá egy új **alkalmazás-beállítási nevet** és **értéket**:

     név:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `true`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

![Az Alkalmazásbeállítások felhasználói felületének képernyőképe](./media/azure-web-apps/appinsights-javascript-enabled.png)

Az ügyféloldali figyelés letiltásához távolítsa el a társított kulcs értéke párt az Alkalmazásbeállítások közül, vagy állítsa hamis értékre.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Az ügyféloldali figyelés **alapértelmezés szerint engedélyezve** van a .net Core-alkalmazások számára az **ajánlott gyűjteménysel**, függetlenül attól, hogy szerepel-e az alkalmazás "APPINSIGHTS_JAVASCRIPT_ENABLED" beállítása.

Ha valamilyen oknál fogva le szeretné tiltani az ügyféloldali figyelést:

* **Beállítások** > **alkalmazás beállításainak** kiválasztása
   * Az Alkalmazásbeállítások területen adjon hozzá egy új **alkalmazás-beállítási nevet** és **értéket**:

     név:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `false`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

![Az Alkalmazásbeállítások felhasználói felületének képernyőképe](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ha engedélyezni szeretné az ügyféloldali figyelést a Node. js-alkalmazáshoz, [manuálisan kell hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

A Java-alkalmazás ügyféloldali figyelésének engedélyezéséhez [manuálisan kell hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

A Python-alkalmazás ügyféloldali figyelésének engedélyezéséhez [manuálisan kell hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>A figyelés automatizálása

Ahhoz, hogy a telemetria-gyűjtést Application Insights használatával engedélyezze, csak az Alkalmazásbeállítások megadása szükséges:

   ![Alkalmazásbeállítások App Service elérhető Application Insights beállításokkal](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Alkalmazásbeállítások definíciói

|Alkalmazás-beállítás neve |  Meghatározás | Érték |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | A fő bővítmény, amely a futtatókörnyezet figyelését vezérli. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Az alapértelmezett módban csak az alapvető funkciók engedélyezettek az optimális teljesítmény biztosításához. | `default` vagy `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Azt szabályozza, hogy a bináris Újraírási motor `InstrumentationEngine` be legyen-e kapcsolva. Ez a beállítás teljesítménybeli következményekkel jár, és a hatás a hideg indítás/indítás ideje. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Azt szabályozza, hogy az SQL & Azure Table szövege a függőségi hívásokkal együtt rögzítve lesz-e. Teljesítményre vonatkozó figyelmeztetés: a rendszer az alkalmazás hideg indítási idejét fogja érinteni. Ehhez a beállításhoz `InstrumentationEngine`a szükséges. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Alkalmazás-beállítások App Service Azure Resource Manager

A App Services alkalmazás-beállításai kezelhetők és konfigurálhatók Azure Resource Manager- [sablonokkal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Ez a módszer akkor használható, ha új App Service erőforrásokat telepít Azure Resource Manager automatizálással, vagy a meglévő erőforrások beállításainak módosítására.

Az Alkalmazásbeállítások JSON alapszintű szerkezete az App Service-hez a következő:

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

Ha például egy Azure Resource Manager sablonra van konfigurálva Application Insightshoz, akkor ez a [sablon](https://github.com/Andrew-MSFT/BasicImageGallery) hasznos lehet, különösen az 238-es [sorból](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)kezdődő szakasz.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizálhatja egy Application Insights erőforrás létrehozását és az újonnan létrehozott App Servicera mutató hivatkozást.

Ha Azure Resource Manager sablont szeretne létrehozni az összes beállított alapértelmezett Application Insights-beállítással, kezdje el a folyamatot úgy, mintha új webalkalmazást fog létrehozni Application Insights engedélyezve.

**Automatizálási beállítások** kiválasztása

   ![Webalkalmazás-létrehozási menü App Service](./media/azure-web-apps/create-web-app.png)

Ez a beállítás a legújabb Azure Resource Manager sablont hozza létre a konfigurált összes szükséges beállítással.

  ![Webalkalmazás-sablon App Service](./media/azure-web-apps/arm-template.png)

Az alábbi példa az összes példányát `AppMonitoredSite` lecseréli a hely nevével:

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
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
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
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Engedélyezés a PowerShell-lel

Ha engedélyezni szeretné az alkalmazások figyelését a PowerShellen keresztül, csak a mögöttes Alkalmazásbeállítások módosítására van szükség. Az alábbiakban egy minta látható, amely lehetővé teszi az alkalmazások figyelését a "AppMonitoredRG" erőforráscsoport "AppMonitoredSite" nevű webhelyén, és a "012345678-ABCD-ef01-2345-6789abcd" kialakítási kulcsba küldendő adatértékek konfigurálását.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Frissítési figyelési bővítmény/ügynök

### <a name="upgrading-from-versions-289-and-up"></a>Frissítés a verzió 2.8.9 és újabb verziókról

A verzióról történő verziófrissítés automatikusan történik, további műveletek nélkül. 2.8.9. Az új figyelési biteket a rendszer a háttérben továbbítja a cél app Service-be, az alkalmazások újraindításakor pedig a rendszer felveszi őket.

A meglátogatott bővítmény verziójának megkeresése`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Az URL-cím elérési útjának képernyőképehttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Verziófrissítés a következő verziókról: 1.0.0-2.6.5

A verzió 2.8.9 kezdődően az előre telepített hely kiterjesztése van használatban. Ha egy korábbi verziót használ, kétféleképpen frissíthet:

* [Frissítsen a portálon keresztüli engedélyezéssel](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Akkor is, ha telepítve van a Azure App Service Application Insights bővítménye, a felhasználói felület csak az **Engedélyezés** gombot jeleníti meg. A háttérben a régi Private site bővítmény el lesz távolítva.)

* [Frissítés a PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)-lel:

    1. Az Alkalmazásbeállítások beállításával engedélyezheti az előre telepített hely kiterjesztése ApplicationInsightsAgent. Lásd: [Engedélyezés a PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)-lel.
    2. Távolítsa el manuálisan a Application Insights Extension nevű Private site bővítményt Azure App Service.

Ha a frissítés a 2.5.1-nél korábbi verzióról történik, ellenőrizze, hogy a ApplicationInsigths dll-fájljai el lettek-e távolítva az Application bin mappából, [lásd: hibaelhárítási lépések](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban részletes hibaelhárítási útmutatót talál az Azure App Services-on futó .NET-és .NET Core-alapú alkalmazások bővítmény-és ügynök-alapú figyeléséhez.

> [!NOTE]
> A Java-alkalmazások csak az Azure App Serviceson, manuális SDK-alapú rendszerállapoton keresztül támogatottak, ezért az alábbi lépések nem vonatkoznak ezekre a forgatókönyvekre.

1. Ellenőrizze, hogy az alkalmazást a használatával `ApplicationInsightsAgent`figyeli-e.
    * Győződjön meg `ApplicationInsightsAgent_EXTENSION_VERSION` arról, hogy az alkalmazás beállítása "~ 2" értékre van állítva.
2. Győződjön meg arról, hogy az alkalmazás megfelel a figyelni kívánt követelményeknek.
    * Tallózással keresse meg a`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Az https://yoursitename.scm.azurewebsites/applicationinsights eredmények oldal képernyőképe](./media/azure-web-apps/app-insights-sdk-status.png)

    * Győződjön meg arról `Application Insights Extension Status` , hogy a`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Ha nem fut, kövesse az [Application Insights figyelésének engedélyezése című témakört](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) .

    * Győződjön meg arról, hogy az állapot forrása létezik, és így néz ki:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Ha nem található hasonló érték, az azt jelenti, hogy az alkalmazás jelenleg nem fut vagy nem támogatott. Az alkalmazás futásának ellenőrzéséhez próbálja meg manuálisan meglátogatni az alkalmazás URL-címét/alkalmazás-végpontját, ami lehetővé teszi, hogy a futásidejű információk elérhetővé váljanak.

    * Erősítse meg `IKeyExists` , hogy a`true`
        * Ha igen `false`, adja hozzá `APPINSIGHTS_INSTRUMENTATIONKEY` a `APPLICATIONINSIGHTS_CONNECTION_STRING` és az rendszerállapotkulcsot GUID azonosítóját az alkalmazás beállításaihoz.

    * Győződjön meg arról, hogy nincsenek bejegyzések `AppAlreadyInstrumented`a `AppContainsDiagnosticSourceAssembly`, és `AppContainsAspNetTelemetryCorrelationAssembly`rendszerhez.
        * Ha bármelyik bejegyzés létezik, távolítsa el a következő csomagokat az alkalmazásból: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`és `Microsoft.AspNet.TelemetryCorrelation`.

Az alábbi táblázat részletesen ismerteti, hogy mit jelentenek ezek az értékek, a kiváltó okok és az ajánlott javítások:

|Probléma értéke|Magyarázat|Hibajavítás
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ez az érték azt jelzi, hogy a bővítmény azt észlelte, hogy az SDK bizonyos aspektusai már szerepelnek az alkalmazásban, és a szolgáltatás vissza fog térni. Ennek oka lehet a következőre való hivatkozás `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`vagy`Microsoft.ApplicationInsights`  | Távolítsa el a hivatkozásokat. A hivatkozások némelyike alapértelmezés szerint a Visual Studio-sablonokból adódik hozzá, és a Visual Studio régebbi verziói is hozzáadhatnak `Microsoft.ApplicationInsights`hivatkozásokat a alkalmazáshoz.
|`AppAlreadyInstrumented:true` | Ha az alkalmazás a .NET Core 2,1-es vagy a 2,2-es verzióra vonatkozik, és a [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-csomagra hivatkozik, akkor Application Insights, és a bővítmény vissza fog térni. | A .NET Core 2.1-es és 2.2-es ügyfeleinek [ajánlott](https://github.com/aspnet/Announcements/issues/287) a Microsoft. AspNetCore. app meta-Package használata.|
|`AppAlreadyInstrumented:true` | Ezt az értéket is okozhatja, ha a fenti DLL-eket egy korábbi telepítésből az alkalmazás mappájába helyezi. | Törölje az alkalmazás mappáját, és győződjön meg arról, hogy a DLL-fájlok el lesznek távolítva. Győződjön meg arról, hogy a helyi alkalmazás bin-könyvtára és a App Service wwwroot könyvtára is található. (A App Service webalkalmazás wwwroot könyvtárának megkereséséhez: speciális eszközök (kudu) > hibakeresési konzol > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ez az érték azt jelzi, hogy a `Microsoft.AspNet.TelemetryCorrelation` bővítmény az alkalmazásra hivatkozik, és vissza fog térni. | Távolítsa el a hivatkozást.
|`AppContainsDiagnosticSourceAssembly**:true`|Ez az érték azt jelzi, hogy a `System.Diagnostics.DiagnosticSource` bővítmény az alkalmazásra hivatkozik, és vissza fog térni.| Távolítsa el a hivatkozást.
|`IKeyExists:false`|Ez az érték azt jelzi, hogy a kialakítási kulcs nem szerepel a Alkalmazásbeállítás `APPINSIGHTS_INSTRUMENTATIONKEY`. Lehetséges okok: Előfordulhat, hogy a rendszer véletlenül eltávolította az értékeket, elfelejtette az értékek beállítását az Automation-parancsfájlban stb. | Győződjön meg arról, hogy a beállítás szerepel a App Service alkalmazás beállításai között.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>A APPINSIGHTS_JAVASCRIPT_ENABLED és a urlCompression nem támogatott

Ha APPINSIGHTS_JAVASCRIPT_ENABLED = True értéket használ a tartalom kódolása esetén, a következőhöz hasonló hibák jelenhetnek meg: 

- 500 URL-Újraírási hiba
- 500,53 URL-írási modul hibája a kimenő üzenetekre vonatkozó Újraírási szabályokkal nem alkalmazható, ha a HTTP-válasz tartalma kódolt ("gzip"). 

Ennek az az oka, hogy a APPINSIGHTS_JAVASCRIPT_ENABLED alkalmazás beállítása true (igaz) értékre van állítva, és a Content-Encoding egyszerre van jelen. Ez a forgatókönyv még nem támogatott. A megkerülő megoldás az alkalmazás beállításaiból való APPINSIGHTS_JAVASCRIPT_ENABLED eltávolítása. Sajnos ez azt jelenti, hogy ha az ügyfél/böngésző oldali JavaScript-rendszerállapotra továbbra is szükség van, a weboldalakhoz manuális SDK-referenciára van szükség. Kövesse a manuális rendszerállapotra vonatkozó [utasításokat](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) a JavaScript SDK-val.

A Application Insights ügynökkel/bővítménnyel kapcsolatos legfrissebb információkért tekintse meg a [kibocsátási megjegyzéseket](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>A PHP és a WordPress nem támogatott

A PHP-és a WordPress-webhelyek nem támogatottak. Jelenleg nincs hivatalosan támogatott SDK/ügynök a számítási feladatok kiszolgálóoldali figyeléséhez. A PHP-vagy WordPress-webhelyen lévő ügyféloldali tranzakciók manuális kiépítéséhez azonban az ügyféloldali JavaScriptet a [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript)használatával lehet elérni a weblapokon. 

## <a name="next-steps"></a>További lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](../app/profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../platform/data-platform.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../platform/alerts-overview.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.
