---
title: Az Azure-alkalmazások szolgáltatásainak teljesítményének figyelése | Microsoft dokumentumok
description: Alkalmazásteljesítmény-figyelés az Azure-alkalmazásszolgáltatásokhoz. A diagram terhelési és válaszideje, a függőségi információk és a teljesítményre vonatkozó riasztások beállítása.
ms.topic: conceptual
ms.date: 12/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: dd0d3be6ed7e5185183618cc2bdeff5ee8d749f3
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81729794"
---
# <a name="monitor-azure-app-service-performance"></a>Az Azure App Service teljesítményének monitorozása

Az [Azure App Servicesen](https://docs.microsoft.com/azure/app-service/) futó ASP.NET és ASP.NET Core alapú webalkalmazások figyelésének engedélyezése most minden eddiginél egyszerűbb. Mivel korábban manuálisan kellett telepítenie egy webhelybővítményt, a legújabb bővítmény/ügynök alapértelmezés szerint be van építve az alkalmazásszolgáltatás lemezképébe. Ez a cikk bemutatja az Application Insights figyelésének engedélyezését, valamint előzetes útmutatást nyújt a folyamat nagyméretű telepítések automatizálásához.

> [!NOTE]
> Az Application Insights-webhelybővítmény **fejlesztői eszközök** > **bővítményein** keresztüli manuális hozzáadása elavult. Ez a bővítménytelepítés ido-ja minden új verzió manuális frissítésétől függött. A bővítmény legújabb stabil kiadása most [már előre telepítve](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) van az App Service-lemezkép részeként. A fájlok találhatók, `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` és automatikusan frissül minden stabil kiadás. Ha követi az ügynök alapú utasításokat, hogy a figyelés az alábbiakban, akkor automatikusan eltávolítja az elavult kiterjesztés az Ön számára.

## <a name="enable-application-insights"></a>Az Application Insights engedélyezése

Kétféleképpen engedélyezheti az alkalmazások figyelését az Azure App Services által üzemeltetett alkalmazásokhoz:

* **Ügynökalapú alkalmazásfigyelés** (ApplicationInsightsAgent).  
    * Ez a módszer a legkönnyebben engedélyezve, és nincs szükség speciális konfigurációra. Gyakran nevezik "futásidejű" figyelés. Az Azure App Services azt javasoljuk, hogy legalább ezt a szintet a figyelés, majd az adott forgatókönyv alapján kiértékelheti, hogy a manuális instrumentation segítségével fejlettebb figyelés.

* **Az alkalmazás manuális instrumenting a kód on** az Application Insights SDK telepítésével.

    * Ez a megközelítés sokkal testreszabhatóbb, de az [Application Insights SDK NuGet-csomagoktól való függőség et](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)igényel. Ez a módszer azt is jelenti, hogy saját maga kell kezelnie a csomagok legújabb verziójának frissítéseit.

    * Ha egyéni API-hívásokat kell kezdeményeznie az alapértelmezetten nem rögzített események/függőségek nyomon követéséhez az ügynökalapú figyeléssel, akkor ezt a módszert kell használnia. További információért tekintse meg az [EGYÉNI események és metrikák API-ját.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) Ez jelenleg az egyetlen támogatott lehetőség a Linux alapú számítási feladatokhoz.

> [!NOTE]
> Ha mind az ügynök-alapú figyelés, mind a manuális SDK-alapú instrumentation észlelése, csak a manuális műszerezési beállítások at kell tiszteletben tartani. Ezzel megakadályozhatja az ismétlődő adatok küldését. Erről az alábbi [hibaelhárítási szakaszban olvashat bővebben.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)

## <a name="enable-agent-based-monitoring"></a>Ügynökalapú figyelés engedélyezése

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> A APPINSIGHTS_JAVASCRIPT_ENABLED és az urlCompression kombinációja nem támogatott. További információkért lásd a magyarázat a [hibaelhárítási szakaszban](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Válassza az Application Insights** az Azure vezérlőpulton az alkalmazásszolgáltatás.

    ![A Beállítások csoportban válassza az Application Insights (Alkalmazáselemzési adatok) lehetőséget.](./media/azure-web-apps/settings-app-insights-01.png)

   * Válasszon új erőforrást, kivéve, ha már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor az ÚJ erőforrás létrehozásához az **OK** gombra kattint, a program a **figyelési beállítások alkalmazása**gombra fogja kérni. A **Folytatás** lehetőséget választva az új Application Insights-erőforrást az alkalmazásszolgáltatáshoz kapcsolja, így **az alkalmazásszolgáltatás újraindítását is elindítja.** 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Miután megadta, hogy melyik erőforrást használja, megadhatja, hogy az alkalmazáselemzési adatok hogyan gyűjtsenek adatokat platformonként az alkalmazáshoz. ASP.NET alkalmazásfigyelés alapértelmezés szerint két különböző szintű gyűjteménysel érhető el.

    ![Platformonkénti beállítások megadása](./media/azure-web-apps/choose-options-new.png)
 
 Az alábbiakban összefoglaljuk az egyes útvonalakról gyűjtött adatokat:
        
|  | .NET alapgyűjtemény | .NET ajánlott gyűjtemény |
| --- | --- | --- |
| Processzor-, memória- és I/O-használati trendek hozzáadása |Igen |Igen |
| Használati trendek gyűjtése, a rendelkezésreállási eredmények és a tranzakciók összevetése | Igen |Igen |
| A gazdafolyamat által nem kezelt kivételek gyűjtése | Igen |Igen |
| Az APM-metrikák pontosságának növelése terhelés alatt, mintavételezés használatakor | Igen |Igen |
| Mikroszolgáltatások összevetése kérési és függőségi határokon keresztül | Nem (csak egypéldányos APM-képességek) |Igen |

3. Beállítások konfigurálásához, például a mintavétel, amely et korábban vezérelheti az applicationinsights.config fájlon keresztül, most már használhatja ugyanazokat a beállításokat alkalmazásbeállítások on a megfelelő előtaggal. 

    * Például a kezdeti mintavételi százalék módosításához létrehozhat egy `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` alkalmazásbeállítást: `100`és egy value of értéket.

    * A támogatott adaptív mintavételi telemetriai processzor beállításainak listáját a [kódban](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) és a [kapcsolódó dokumentációban](https://docs.microsoft.com/azure/azure-monitor/app/sampling)tekintheti meg.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

A .NET Core következő verziói támogatottak: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

A teljes keretrendszer célzása a .NET Core, önálló központi telepítés és Linux alapú alkalmazások jelenleg **nem támogatott** ügynök/bővítmény alapú figyelés. ([A kódon keresztüli manuális instrumentation](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) minden korábbi esetben működni fog.)

1. **Válassza az Application Insights** az Azure vezérlőpulton az alkalmazásszolgáltatás.

    ![A Beállítások csoportban válassza az Application Insights (Alkalmazáselemzési adatok) lehetőséget.](./media/azure-web-apps/settings-app-insights-01.png)

   * Válasszon új erőforrást, kivéve, ha már beállított egy Application Insights-erőforrást ehhez az alkalmazáshoz. 

     > [!NOTE]
     > Amikor az ÚJ erőforrás létrehozásához az **OK** gombra kattint, a program a **figyelési beállítások alkalmazása**gombra fogja kérni. A **Folytatás** lehetőséget választva az új Application Insights-erőforrást az alkalmazásszolgáltatáshoz kapcsolja, így **az alkalmazásszolgáltatás újraindítását is elindítja.** 

     ![Webapp kialakítása](./media/azure-web-apps/create-resource-01.png)

2. Miután megadta, hogy melyik erőforrást használja, kiválaszthatja, hogy az Application Insights hogyan gyűjtse össze az alkalmazás platformonkénti adatait. A .NET Core **ajánlott gyűjtést** vagy **letiltva** a .NET Core 2.0, 2.1, 2.2 és 3.0 esetében ajánlott gyűjtést kínál.

    ![Platformonkénti beállítások megadása](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Az **Settings** > App Service-webalkalmazásbeállításai közül válassza az Application Insights > **Engedélyezése lehetőséget.****select Application Insights** Node.js ügynök alapú figyelés jelenleg előzetes verzióban.

# <a name="java"></a>[Java](#tab/java)

A Java App Service alapú webalkalmazások jelenleg nem támogatják az automatikus ügynök/bővítmény alapú figyelést. A Java-alkalmazás figyelésének engedélyezéséhez manuálisan kell [eszközre állítania az alkalmazást.](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started)

# <a name="python"></a>[Python](#tab/python)

A Python App Service alapú webalkalmazások jelenleg nem támogatják az automatikus ügynök/bővítmény alapú figyelést. A Python-alkalmazás figyelésének engedélyezéséhez manuálisan kell [eszközolnia az alkalmazást.](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

---

## <a name="enable-client-side-monitoring"></a>Ügyféloldali figyelés engedélyezése

# <a name="net"></a>[.NET](#tab/net)

Az ügyféloldali figyelés ASP.NET. Az ügyféloldali figyelés engedélyezése:

* Válassza a **Beállítások** >** **Alkalmazásbeállítások****
   * Az Alkalmazásbeállítások csoportban adjon hozzá egy új **alkalmazásbeállítás nevét** és **értékét:**

     név:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `true`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

![Képernyőkép az alkalmazásbeállítások felhasználói felületéről](./media/azure-web-apps/appinsights-javascript-enabled.png)

Az ügyféloldali figyelés letiltásához távolítsa el a társított kulcsérték-párt az alkalmazás beállításaiközül, vagy állítsa az értéket hamisértékre.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Az ügyféloldali figyelés **alapértelmezés szerint engedélyezve** van az **Ajánlott gyűjteménylel**rendelkező .NET Core alkalmazások esetében, függetlenül attól, hogy a "APPINSIGHTS_JAVASCRIPT_ENABLED" alkalmazásbeállítás jelen van-e.

Ha valamilyen okból le szeretné tiltani az ügyféloldali figyelést:

* Válassza az Alkalmazás **beállításainak megadása** > **lehetőséget**
   * Az Alkalmazásbeállítások csoportban adjon hozzá egy új **alkalmazásbeállítás nevét** és **értékét:**

     név:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Érték: `false`

   * **Mentse** a beállításokat, és **indítsa újra** az alkalmazást.

![Képernyőkép az alkalmazásbeállítások felhasználói felületéről](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

A Node.js alkalmazás ügyféloldali figyelésének engedélyezéséhez manuálisan kell [hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="java"></a>[Java](#tab/java)

A Java-alkalmazás ügyféloldali figyelésének engedélyezéséhez manuálisan kell [hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

# <a name="python"></a>[Python](#tab/python)

Az ügyféloldali figyelés engedélyezéséhez manuálisan kell [hozzáadnia az ügyféloldali JavaScript SDK-t az alkalmazáshoz.](https://docs.microsoft.com/azure/azure-monitor/app/javascript)

---

## <a name="automate-monitoring"></a>A figyelés automatizálása

Annak érdekében, hogy telemetriai adatok gyűjteménye az Application Insights, csak az alkalmazás beállításait kell beállítani:

   ![Az App Service alkalmazásbeállításai az elérhető Application Insights-beállításokkal](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Alkalmazásbeállítások definíciói

|Alkalmazásbeállítás neve |  Meghatározás | Érték |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Fő bővítmény, amely a futásidejű figyelést vezérli. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Csak alapértelmezett módban engedélyezve vannak az alapvető funkciók az optimális teljesítmény biztosítása érdekében. | `default` vagy `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Azt szabályozza, hogy `InstrumentationEngine` a bináris átíró motor be lesz-e kapcsolva. Ez a beállítás teljesítménybeli következményekkel jár, és hatással van a hidegindítási/indítási időre. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Azt szabályozza, hogy az SQL & Azure-táblázat szövege a függőségi hívásokkal együtt rögzítésre kerül-e. Teljesítményfigyelmeztetés: az alkalmazás hidegindítási ideje érintett lesz. Ehhez a `InstrumentationEngine`beállításhoz a szükséges. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Az App Service-alkalmazás beállításai az Azure Resource Managerrel

Az App Services alkalmazásbeállításai az [Azure Resource Manager-sablonokkal kezelhetők](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)és konfigurálhatók. Ez a módszer akkor használható, ha új App Service-erőforrásokat telepít az Azure Resource Manager automatizálásával, vagy a meglévő erőforrások beállításainak módosításakor.

Az alkalmazásbeállítások JSON-jazmának alapvető szerkezete az alábbi:

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

Az Application Insightshoz konfigurált Alkalmazásbeállításokkal rendelkező Azure Resource Manager-sablon például ez a [sablon](https://github.com/Andrew-MSFT/BasicImageGallery) hasznos lehet, különösen a [238-as vonalon](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)kezdődő szakasz.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizálhatja egy Application Insights-erőforrás létrehozását, és csatolhatja az újonnan létrehozott App Service.Automat is a creation of an Application Insights resource and link to your newly created App Service.

Ha egy Azure Resource Manager-sablont szeretne létrehozni az összes alapértelmezett Application Insights-beállítással, úgy kezdje el a folyamatot, mintha egy új webalkalmazást hozna létre, amelyen engedélyezve van az Application Insights.

**Automatizálási beállítások** kiválasztása

   ![Az App Service webalkalmazás-létrehozási menüje](./media/azure-web-apps/create-web-app.png)

Ez a beállítás a legújabb Azure Resource Manager-sablont hozza létre az összes szükséges beállítással.

  ![App Service-webalkalmazás-sablon](./media/azure-web-apps/arm-template.png)

Az alábbiakban egy minta, `AppMonitoredSite` cserélje ki az összes példányt a webhely nevét:

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

### <a name="enabling-through-powershell"></a>Engedélyezés a PowerShellen keresztül

Annak érdekében, hogy az alkalmazás figyelése a PowerShellen keresztül, csak az alapul szolgáló alkalmazás beállításait kell módosítani. Az alábbiakban egy minta, amely lehetővé teszi az alkalmazás figyelése a webhely neve "AppMonitoredSite" az erőforráscsoport "AppMonitoredRG", és konfigurálja az adatokat kell küldeni a "012345678-abcd-ef01-2345-6789abcd" instrumentation kulcs.

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

## <a name="upgrade-monitoring-extensionagent"></a>Frissítésfigyelési bővítmény/ügynök

### <a name="upgrading-from-versions-289-and-up"></a>Frissítés a 2.8.9-es és újabb verziókról

A 2.8.9-es verzióról történő frissítés automatikusan, további műveletek nélkül történik. Az új figyelési bitek a háttérben kerülnek a célalkalmazás-szolgáltatásba, és az alkalmazás újraindításakor felveszik őket.

A bővítmény futtatásának melyik verziójának ellenőrzése`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Az URL elérési útjának képernyőképehttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Frissítés az 1.0.0-as verzióról és a 2.6.5-ös verziókról

A 2.8.9-es verziótól kezdve az előre telepített helybővítményt használja. Ha Ön korábbi verzió, kétféleképpen frissíthet:

* [Frissítsen a portálon keresztüli engedélyezéssel.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) (Még akkor is, ha az Application Insights-bővítmény telepítve van az Azure App Service-hez, a felhasználói felület csak **az Engedélyezés** gombot jeleníti meg. A színfalak mögött a régi privát webhelybővítmény eltávolításra kerül.)

* [Frissítés a PowerShellen keresztül:](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell)

    1. Állítsa be az alkalmazás beállításait az ApplicationInsightsAgent előre telepített helybővítmény engedélyezéséhez. Lásd: [Powershell átkapcsolása](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Manuálisan távolítsa el az Azure Insights-bővítmény nevű privát webhelybővítményt az Azure App Service-hez.

Ha a frissítés a 2.5.1-es verzió előtti verzióról történik, ellenőrizze, hogy az ApplicationInsigths dlls eltávolításra kerül-e az alkalmazástároló [mappából, lásd a hibaelhárítási lépéseket.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting)

## <a name="troubleshooting"></a>Hibaelhárítás

Az alábbiakban részletes hibaelhárítási útmutatót talál a .NET és a .NET Core alapú alkalmazások bővítmény-ügynök alapú figyeléséhez az Azure App Services szolgáltatásban futó.

> [!NOTE]
> Java-alkalmazások csak manuális SDK-alapú instrumentation, és ezért az alábbi lépések nem vonatkoznak ezekre a forgatókönyvekre.

1. Ellenőrizze, hogy az `ApplicationInsightsAgent`alkalmazás figyelése a segítségével.
    * Ellenőrizze, `ApplicationInsightsAgent_EXTENSION_VERSION` hogy az alkalmazásbeállítása "~2" értékre van-e állítva.
2. Győződjön meg arról, hogy az alkalmazás megfelel a felügyelendő követelményeknek.
    * Tallózás a`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Az https://yoursitename.scm.azurewebsites/applicationinsights eredmények lap képernyőképe](./media/azure-web-apps/app-insights-sdk-status.png)

    * Ellenőrizze, `Application Insights Extension Status` hogy a`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Ha nem fut, kövesse az [Application Insights figyelési utasításainak engedélyezését](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Ellenőrizze, hogy az állapotforrás létezik-e, és így néz ki:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Ha egy hasonló érték nincs jelen, az azt jelenti, hogy az alkalmazás jelenleg nem fut, vagy nem támogatott. Győződjön meg arról, hogy az alkalmazás fut, próbálja meg manuálisan felkeresni az alkalmazás URL/application végpontok, amely lehetővé teszi a futásidejű információk elérhetővé válnak.

    * Annak `IKeyExists` ellenőrzése, hogy`true`
        * Ha ez, `false` `APPINSIGHTS_INSTRUMENTATIONKEY` add `APPLICATIONINSIGHTS_CONNECTION_STRING` hozzá, és az ikey guid az alkalmazás beállításait.

    * Győződjön meg arról, `AppContainsDiagnosticSourceAssembly`hogy `AppContainsAspNetTelemetryCorrelationAssembly`nincsenek bejegyzések a és a `AppAlreadyInstrumented`hoz.
        * Ha a bejegyzések bármelyike létezik, távolítsa el `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource`a `Microsoft.AspNet.TelemetryCorrelation`következő csomagokat az alkalmazásból: , és .

Az alábbi táblázat részletesebb magyarázatot ad arra, hogy mit jelentenek ezek az értékek, milyen okok okait és ajánlott javításokat tartalmaz:

|Probléma értéke|Magyarázat|Hibajavítás
|---- |----|---|
| `AppAlreadyInstrumented:true` | Ez az érték azt jelzi, hogy a bővítmény észlelte, hogy az SDK bizonyos aspektusa már jelen van az alkalmazásban, és visszalép. Ez annak köszönhető, hogy `System.Diagnostics.DiagnosticSource` `Microsoft.AspNet.TelemetryCorrelation`a , vagy`Microsoft.ApplicationInsights`  | Távolítsuk el a hivatkozásokat. A hivatkozások némelyike alapértelmezés szerint bizonyos Visual Studio-sablonokból, a Visual Studio `Microsoft.ApplicationInsights`régebbi verziói pedig hivatkozásokat adhatnak hozzá a programhoz.
|`AppAlreadyInstrumented:true` | Ha az alkalmazás a .NET Core 2.1 vagy 2.2-es rendszert célozza meg, és a [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package-re hivatkozik, akkor az Application Insightsot hozza, és a bővítmény visszalép. | A .NET Core 2.1,2.2 felhasználóinak [ajánlott](https://github.com/aspnet/Announcements/issues/287) a Microsoft.AspNetCore.App meta-package használata.|
|`AppAlreadyInstrumented:true` | Ezt az értéket az is okozhatja, hogy a fenti dll-ek egy korábbi központi telepítésből származó alkalmazásmappában jelennek meg. | Tisztítsa meg az alkalmazásmappát, és győződjön meg arról, hogy ezek a dll-ek törlődnek. Ellenőrizze a helyi alkalmazás bin könyvtárát és az App Service wwwroot könyvtárát is. (Az App Service webalkalmazás wwwroot könyvtárának ellenőrzése: Advanced Tools (Kudu) > Debug konzol > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Ez az érték azt jelzi, `Microsoft.AspNet.TelemetryCorrelation` hogy a bővítmény az alkalmazásban hivatkozott hivatkozásokat észlelt, és visszalép. | Távolítsa el a hivatkozást.
|`AppContainsDiagnosticSourceAssembly**:true`|Ez az érték azt jelzi, `System.Diagnostics.DiagnosticSource` hogy a bővítmény az alkalmazásban hivatkozott hivatkozásokat észlelt, és visszalép.| Távolítsa el a hivatkozást.
|`IKeyExists:false`|Ez az érték azt jelzi, hogy a instrumentation `APPINSIGHTS_INSTRUMENTATIONKEY`kulcs nem található az AppSetting, . Lehetséges okok: Előfordulhat, hogy az értékeket véletlenül eltávolították, elfelejtették beállítani az értékeket az automatizálási parancsfájlban stb. | Győződjön meg arról, hogy a beállítás szerepel az App Service-alkalmazás beállításaiközött.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED és urlCompression nem támogatott

Ha APPINSIGHTS_JAVASCRIPT_ENABLED=true-t használ olyan esetekben, amikor a tartalom kódolt, az ilyen hibák jelenhetnek meg: 

- 500 URL-es újraírási hiba
- 500.53 URL-újraírási modulhiba üzenettel A Kimenő újraírási szabályok nem alkalmazhatók, ha a HTTP-válasz tartalma kódolt ("gzip"). 

Ennek az az oka, hogy a APPINSIGHTS_JAVASCRIPT_ENABLED alkalmazás beállítása igaz, és a tartalomkódolás egyszerre van jelen. Ez a forgatókönyv még nem támogatott. A megoldás az, hogy eltávolítja a APPINSIGHTS_JAVASCRIPT_ENABLED az alkalmazás beállításaiból. Sajnos ez azt jelenti, hogy ha az ügyfél/böngésző oldali JavaScript-instrumentation továbbra is szükséges, manuális SDK-hivatkozásokra van szükség a weboldalakhoz. Kérjük, kövesse a JavaScript SDK kézi műszerezésre [vonatkozó utasításait.](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)

Az Application Insights-ügynökkel/-bővítményről a [kiadási megjegyzésekben](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)talál legfrissebb információt.

### <a name="php-and-wordpress-are-not-supported"></a>A PHP és a WordPress nem támogatott

A PHP és a WordPress webhelyek nem támogatottak. Jelenleg nincs hivatalosan támogatott SDK/ügynök a kiszolgálóoldali figyelése ezeket a számítási feladatokat. Azonban manuálisan műszerezés kliens oldali tranzakciók a PHP vagy WordPress oldalon hozzáadásával az ügyféloldali javascript a weboldalakat lehet elérni a [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>További lépések
* [Futtassa a profilkészítőt a működő alkalmazásán.](../app/profiler.md)
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – az Azure Functions figyelése az Application Insights segítségével
* [Engedélyezze az Azure Diagnostics](../platform/diagnostics-extension-to-application-insights.md) Application Insightsba való küldését.
* [Figyelje a szolgáltatások állapotával kapcsolatos mérőszámokat](../platform/data-platform.md), így meggyőződhet róla, hogy szolgáltatása elérhető és válaszkész.
* [Riasztási értesítéseket kaphat](../platform/alerts-overview.md), ha működési események történnek vagy a mérőszámok átlépnek egy küszöbértéket.
* Az [Application Insights JavaScript-alkalmazásokhoz és weblapokhoz](javascript.md) való használatával ügyféltelemetriát kaphat azoktól a böngészőktől, amelyek ellátogatnak egy weblapra.
* [Állítson be rendelkezésre állási webes teszteket](monitor-web-app-availability.md), így riasztást kaphat, ha webhelye nem működik.
