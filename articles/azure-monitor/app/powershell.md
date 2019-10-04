---
title: Azure-Application Insights automatizálása a PowerShell használatával | Microsoft Docs
description: Automatizálja erőforrás-, riasztási és rendelkezésre állási tesztek létrehozását a PowerShellben egy Azure Resource Manager sablon használatával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: mbullwin
ms.openlocfilehash: b4f3d2eba70be39b23e86ebde3c71dfc7c19a374
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936706"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Hozzon létre egy Application Insights-erőforrást PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan automatizálható a [Application Insights](../../azure-monitor/app/app-insights-overview.md) -erőforrások automatikus létrehozása és frissítése az Azure Resource Management használatával. Előfordulhat például, hogy egy összeállítási folyamat részeként ezt megteheti. Az alapszintű Application Insights erőforrás mellett létrehozhat [rendelkezésre állási webes teszteket](../../azure-monitor/app/monitor-web-app-availability.md), riasztásokat állíthat be, és beállíthatja az [árképzési sémát](pricing.md), és más Azure-erőforrásokat is létrehozhat. [](../../azure-monitor/app/alerts.md)

Az erőforrások létrehozásának kulcsa a [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md)JSON-sablonjai. Dióhéjban az eljárás a következőképpen érhető el: a meglévő erőforrások JSON-definícióinak letöltése; parametrizálja bizonyos értékeket, például neveket; Ezután futtassa a sablont, amikor új erőforrást szeretne létrehozni. Egyszerre több erőforrást is becsomagolhat, így egyetlen lépéssel létrehozhatja őket, például egy alkalmazás-figyelő rendelkezésre állási tesztekkel, riasztásokkal és tárolással a folyamatos exportáláshoz. A parameterizations néhány finomságot talál, amelyeket itt mutatjuk be.

## <a name="one-time-setup"></a>Egyszeri telepítés
Ha még nem használta a PowerShellt az Azure-előfizetéséhez, mielőtt:

Telepítse az Azure PowerShell-modult arra a gépre, amelyen futtatni szeretné a parancsfájlokat:

1. Telepítse a [Microsoft webplatform-telepítőt (V5 vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Használja a Microsoft Azure PowerShell telepítéséhez.

## <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása
Hozzon létre egy új. JSON fájlt – hívjuk meg `template1.json` értéket ebben a példában. Tartalom másolása ide:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
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
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
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
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
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
                    "retentionInDays": "[variables('retentionInDays')]",
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
1. Jelentkezzen be az Azure-ba a PowerShellben:
   
    `Connect-AzAccount`
2. Futtassa az alábbihoz hasonló parancsot:
   
    ```PS
   
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * @no__t – 0: az a csoport, amelyben létre szeretné hozni az új erőforrásokat.
   * a `-TemplateFile` értéknek az egyéni paraméterek előtt kell történnie.
   * @no__t – 0 a létrehozandó erőforrás neve.

További paramétereket is hozzáadhat – a sablon paraméterek szakaszában megtalálja a leírásokat.

## <a name="to-get-the-instrumentation-key"></a>A kialakítási kulcs beszerzése
Az alkalmazás-erőforrás létrehozása után a kialakítási kulcsot érdemes megtekinteni: 

```PS
    $resource = Find-AzResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>A díjcsomag beállítása

Megadhatja a [díjcsomag árát](pricing.md).

Ha a fenti sablonnal szeretne létrehozni egy alkalmazás-erőforrást a nagyvállalati díjszabással, használja a következőt:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -priceCode 2 `
               -appName myNewApp
```

|priceCode|csomag|
|---|---|
|1|Alapszintű|
|2|Vállalati|

* Ha csak az alapértelmezett alapszintű árat szeretné használni, kihagyhatja a CurrentBillingFeatures erőforrást a sablonból.
* Ha az összetevő-erőforrás létrehozása után módosítani szeretné az árat, használhat olyan sablont, amely kihagyja a "Microsoft. betekintő/összetevők" erőforrást. Továbbá hagyja ki a `dependsOn` csomópontot a számlázási erőforrásból. 

A frissített díjcsomag ellenőrzéséhez tekintse meg a böngésző **használati és becsült költségek lapját** . **Frissítse a böngésző nézetét** , és győződjön meg arról, hogy a legutóbbi állapot jelenik meg.



## <a name="add-a-metric-alert"></a>Metrikai riasztás hozzáadása

Ha mérőszám-riasztást szeretne beállítani az alkalmazás-erőforrással megegyező időben, egyesítse a következőhöz hasonló kódot a sablonfájlba:

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

A sablon meghívásakor a következő paramétert is hozzáadhatja:

    `-responseTime 2`

Természetesen parametrizálja más mezőket is. 

Az egyéb riasztási szabályok típus-és konfigurációs részleteinek megkereséséhez hozzon létre egy szabályt manuálisan, majd vizsgálja [](https://resources.azure.com/)meg Azure Resource Managerban. 


## <a name="add-an-availability-test"></a>Rendelkezésre állási teszt hozzáadása

Ez a példa egy pingelési tesztre vonatkozik (egyetlen oldal teszteléséhez).  

Egy rendelkezésre állási teszt **két részből** áll: maga a teszt, és a riasztás, amely értesíti a hibákat.

Egyesítse a következő kódot az alkalmazást létrehozó sablonfájl számára.

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
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
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

Ha más tesztelési helyekhez tartozó kódokat szeretne felderíteni, vagy összetettebb webes tesztek létrehozását szeretné automatizálni, hozzon létre egy példát manuálisan, majd parametrizálja a kódot a [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>További erőforrások hozzáadása

Bármilyen más erőforrás létrehozásának automatizálásához hozzon létre egy példát manuálisan, majd másolja és parametrizálja a kódját a [Azure Resource Managerból](https://resources.azure.com/). 

1. Nyissa meg [Azure Resource Manager](https://resources.azure.com/). Navigáljon @no__t – 0 értékre az alkalmazás-erőforráshoz. 
   
    ![Navigálás Azure Erőforrás-kezelő](./media/powershell/01.png)
   
    Az *összetevők* az alkalmazások megjelenítésének alapszintű Application Insights erőforrásai. A kapcsolódó riasztási szabályokhoz és a rendelkezésre állási webes tesztekhez külön erőforrások tartoznak.
2. Másolja az összetevő JSON-fájlját a `template1.json` megfelelő helyére.
3. Törölje a következő tulajdonságokat:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Nyissa meg a webteszteket és az alertrules szakaszt, és másolja a JSON-t az egyes elemekre a sablonba. (Ne másolja a webteszteket vagy a alertrules-csomópontokat: lépjen bele az elemek alá.)
   
    Mindegyik webes teszthez tartozik egy riasztási szabály, ezért mindkettőt másolni kell.
   
    A metrikák riasztásait is felveheti. [Metrikák nevei](powershell-alerts.md#metric-names)
5. Szúrja be ezt a sort az egyes erőforrásokban:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>A sablon parametrizálja
Most le kell cserélnie az adott neveket paraméterekkel. [Egy sablon parametrizálja](../../azure-resource-manager/resource-group-authoring-templates.md)a kifejezéseket [segítő függvények](../../azure-resource-manager/resource-group-template-functions.md)használatával írhatja be. 

A karakterláncok csak egy részét parametrizálja, ezért a karakterláncok létrehozásához használja a `concat()` értéket.

Íme néhány példa a használni kívánt helyettesítésekre. Az egyes cserék több előfordulása is van. Előfordulhat, hogy a sablonban másokra is szüksége van. Ezek a példák a sablon tetején definiált paramétereket és változókat használják.

| find | Csere erre |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| @no__t – 0 (kisbetűs) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>A GUID és az azonosító törlése. |

### <a name="set-dependencies-between-the-resources"></a>Az erőforrások közötti függőségek beállítása
Az Azure-nak szigorú sorrendben kell beállítania az erőforrásokat. A következő megkezdése előtt győződjön meg arról, hogy az egyik telepítés befejeződik, és adja hozzá a függőségi sorokat:

* A rendelkezésre állási teszt erőforrásban:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Egy rendelkezésre állási teszt esetén a riasztási erőforrásban:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>További lépések
Egyéb Automation-cikkek:

* [Hozzon létre egy Application Insights erőforrás](powershell-script-create-resource.md) -gyors metódust sablon használata nélkül.
* [Riasztások beállítása](powershell-alerts.md)
* [Webes tesztek létrehozása](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Az Azure Diagnostics küldése az Application Insights-ba](powershell-azure-diagnostics.md)
* [Üzembe helyezés az Azure-ban a GitHubról](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Kiadási jegyzetek létrehozása](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)