---
title: Az Azure Application insights szolgáltatással a PowerShell használatával automatizálhatja |} Microsoft Docs
description: A PowerShell használata Azure Resource Manager-sablon létrehozása erőforrás, a riasztás és a rendelkezésre állási tesztek automatizálásához.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: d6bc4f69386cc8a9119aa852693456f6465f59ce
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2018
---
#  <a name="create-application-insights-resources-using-powershell"></a>Hozzon létre egy Application Insights-erőforrást PowerShell használatával
Ez a cikk bemutatja, hogyan létrehozása és frissítése [Application Insights](app-insights-overview.md) erőforrások automatikusan az Azure Resource Manager használatával. Akkor lehet hasznos, például így a felépítési folyamat részeként. Alapszintű Application Insights-erőforrások, valamint létrehozhat [webteszt rendelkezésre állási](app-insights-monitor-web-app-availability.md), állítsa be a következőt [riasztások](app-insights-alerts.md), beállíthatja a [séma árképzési](app-insights-pricing.md), és más Azure-erőforrások létrehozása .

A kulcs létrehozása ezeket az erőforrásokat JSON-sablonokat [Azure Resource Manager](../azure-resource-manager/powershell-azure-resource-manager.md). A ez már a vég, a művelet be nem: Töltse le a JSON-definíciókat a meglévő erőforrások; egyes értékek nevét; például parametrizálja és futtassa újra a sablont, bármikor létrehozhat egy újat. Egyszerre több erőforrás csomagot, azokat összes egy hozhatja létre – például egy alkalmazást a figyelőt rendelkezésreállás figyelésére szolgáló tesztek, a riasztások és a folyamatos exportálás-tároló. Nincsenek néhány subtleties egyes a parameterizations, amely azt ismertetjük, itt.

## <a name="one-time-setup"></a>Egyszeri beállítása
Ha még nem használta PowerShell Azure-előfizetéséhez előtt:

Az Azure Powershell modul telepítése a számítógépen, ahová a parancsfájlok futtatásához:

1. Telepítés [Microsoft Webplatform-telepítővel (v5 vagy magasabb)](http://www.microsoft.com/web/downloads/platform.aspx).
2. Segítségével a Microsoft Azure Powershell telepítése.

## <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása
Hozzon létre egy új .JSON kiterjesztésű fájlt – tegyük neki `template1.json` ebben a példában. A tartalom másolása be:

```JSON
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the application name."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the application type."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "East US",
                "allowedValues": [
                    "South Central US",
                    "West Europe",
                    "East US",
                    "North Europe"
                ],
                "metadata": {
                    "description": "Enter the application location."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "1 = Basic, 2 = Enterprise"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```



## <a name="create-application-insights-resources"></a>Application Insights-erőforrások létrehozása
1. A PowerShellben jelentkezzen be az Azure-bA:
   
    `Connect-AzureRmAccount`
2. Ehhez hasonló parancs futtatása:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` a csoport van, hol szeretné létrehozni az új erőforrásokat.
   * `-TemplateFile` az egyéni paraméterek előfordulása esetén.
   * `-appName` A létrehozni kívánt erőforrás neve.

Más paramétereket adhat hozzá, mert a azok leírásait tartalmazza a sablon a Paraméterek szakaszban találhat.

## <a name="to-get-the-instrumentation-key"></a>A rendszerállapot-kulcs beszerzése
Miután létrehozta az alkalmazás-erőforrást, érdemes a instrumentation kulcs: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Állítsa be az árlista terv

Beállíthatja a [ár terv](app-insights-pricing.md).

Az alkalmazás erőforrás létrehozása a vállalati ár csomagot, a fenti sablon használatával:

```PS
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|csomag|
|---|---|
|1|Alapszintű|
|2|Enterprise|

* Ha szeretné az alapértelmezett alapár csomag használata, kihagyhatja a CurrentBillingFeatures erőforrás a sablonból.
* Ha szeretné módosítani a ár terv, az összetevő-erőforrás létrehozása után, használhatja a sablont, amely kihagyja a "microsoft.insights/components" erőforrás. Emellett nincs megadva a `dependsOn` a számlázási erőforrás csomópontot. 

A frissített ár terv ellenőrzéséhez tekintse meg a **használati és a becsült költség lap** panel a böngészőben. **Frissítse a böngészőt nézetet** való ellenőrizze, hogy az aktuális állapotát.



## <a name="add-a-metric-alert"></a>A metrika-riasztások hozzáadása

A metrika-riasztások beállítása az alkalmazás-erőforrást, egy időben, a sablonfájl ilyen kód egyesítése:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

A sablon indításakor, ez a paraméter opcionálisan hozzáadhat:

    `-responseTime 2`

Természetesen a más mezők lehet paraméterezni. 

Tudja meg a típus neve és más riasztási szabályok konfigurációs adatai, manuálisan hozzon létre egy szabályt, és majd vizsgálja meg azt a [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Elérhetőségi teszt hozzáadása

A példa egy ping vizsgálat (egyoldalas tesztelése).  

**Két részből áll** az elérhetőségi teszt: a vizsgálat magát, és riasztást, értesítést küld a hibák.

Az alábbi kód egyesítése a sablonfájl, amely létrehozza az alkalmazást.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExectuionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Más teszt helyeket kódok felderítése, illetve összetettebb webteszt automatizálhatja, hozzon létre manuálisan egy példa, és majd parametrizálja a kód [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>További erőforrások hozzáadása

Automatikus bármilyen más erőforrás, hozzon létre egy példa manuálisan, majd másolja ki és a kód parametrizálja [Azure Resource Manager](https://resources.azure.com/). 

1. Nyissa meg [az Azure Resource Manager](https://resources.azure.com/). Lefelé járja végig `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, hogy az alkalmazás erőforrás. 
   
    ![Az Azure erőforrás-kezelőben navigációs](./media/app-insights-powershell/01.png)
   
    *Összetevők* az alkalmazások alapvető Application Insights-erőforrás. Nincsenek a társított riasztási szabályok és a rendelkezésre állási webteszt külön erőforrásait.
2. Az összetevő JSON másolja a megfelelő helyet `template1.json`.
3. Törli ezeket a tulajdonságokat:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Nyissa meg a webtests és alertrules szakaszban, és a JSON az egyes elemek másolása a sablonba. (A webtests vagy alertrules csomópontok nem másolja: lépjen be azokat az elemeket.)
   
    Minden webalkalmazás-teszt tartozó riasztási szabály, rendelkezik, így mindkettő másolni kell.
   
    Riasztások a metrikák is használható. [Metrika neve](app-insights-powershell-alerts.md#metric-names).
5. A sor beszúrása az egyes erőforrások:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>A sablon parametrizálja
Most már rendelkezik az adott nevek cseréje a paraméterek. A [sablon parametrizálja](../azure-resource-manager/resource-group-authoring-templates.md), kifejezések használatával írhat egy [segítő funkciók](../azure-resource-manager/resource-group-template-functions.md). 

Nem lehet paraméterezni csak részét, így `concat()` karakterláncok létrehozásához.

Itt példák kell, hogy a helyettesítést. Nincsenek minden helyettesítés többszöri felbukkanását. Szükség lehet mások a sablonban. Ezekben a példákban a paraméterek és változók meghatározott sablon tetején.

| Keresés | cserélje le |
| --- | --- |
| `"hidden-link:/subscriptions/.../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (nagybetűs) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Törölje a GUID azonosítót és azonosítóját. |

### <a name="set-dependencies-between-the-resources"></a>Az erőforrások közti függőségeket beállítása
Azure az erőforrások szigorú sorrendben kell beállítani. Győződjön meg arról, hogy a telepítő befejezte a következő megkezdése előtt, adja hozzá a függőség sorok:

* A rendelkezésre állási erőforrás teszteléséhez:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Az elérhetőségi teszt riasztási erőforrás:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>További lépések
Más automatizálási cikkek:

* [Az Application Insights-erőforrás létrehozása](app-insights-powershell-script-create-resource.md) -nélkül sablon használatával gyorsan elvégezhető.
* [Riasztások beállítása](app-insights-powershell-alerts.md)
* [Webteszt létrehozása](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Az Azure Diagnostics küldése az Application Insights-ba](app-insights-powershell-azure-diagnostics.md)
* [Telepítse az Azure a Githubról](http://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Kiadási jegyzetek írására](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

