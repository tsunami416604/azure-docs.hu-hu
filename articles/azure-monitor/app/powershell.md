---
title: Automatizálása a PowerShell-lel az Azure Application Insights |} A Microsoft Docs
description: Automatizálhatja az erőforrást, a riasztás és a rendelkezésre állási tesztek létrehozása a PowerShell egy Azure Resource Manager-sablon használatával.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 9f73b87f-be63-4847-88c8-368543acad8b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/02/2017
ms.author: mbullwin
ms.openlocfilehash: 74da56b5e90512f8b903d5a62f7dde4e903560b8
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817864"
---
#  <a name="create-application-insights-resources-using-powershell"></a>Hozzon létre egy Application Insights-erőforrást PowerShell használatával
Ez a cikk bemutatja, hogyan automatizálhatja a létrehozása és frissítése [Application Insights](../../azure-monitor/app/app-insights-overview.md) erőforrások automatikusan az Azure Resource Management használatával. Előfordulhat például, ekkor a buildelési folyamat részeként. Alapszintű Application Insights-erőforrás, valamint létrehozhat [rendelkezésre állási webes tesztek](../../azure-monitor/app/monitor-web-app-availability.md), állítsa be [riasztások](../../azure-monitor/app/alerts.md)állítsa be a [díjszabási séma](pricing.md), és más Azure-erőforrások létrehozása .

A kulcs létrehozásához ezeket az erőforrásokat: JSON-sablonokat [Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md). Legnépszerűbb, a művelet be nem: a meglévő erőforrások; JSON-definíciók letöltése egyes értékek nevét; például paraméterezése és futtassa a sablont, amikor szeretne létrehozni egy új erőforrást. Több erőforrás együtt is csomag, hozza létre őket mindezt egy lépjen – például egy alkalmazás-figyelő rendelkezésre állási tesztek, a riasztások és a tárhelyet a folyamatos exportálás. Nincsenek egyes parameterizations, amely itt elmagyarázzuk, néhány apró.

## <a name="one-time-setup"></a>Egyszeri beállítás
Ha még nem használta a Powershellt az Azure-előfizetésében előtt:

Azure Powershell-modul telepítéséhez a számítógépen, ahol szeretné a szkriptek futtatására:

1. Telepítés [Microsoft Webplatform-telepítővel (5-ös verzióját vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Ezzel a Microsoft Azure Powershell telepítéséhez.

## <a name="create-an-azure-resource-manager-template"></a>Az Azure Resource Manager-sablon létrehozása
Hozzon létre egy új .JSON kiterjesztésű fájlt – neki `template1.json` ebben a példában. Másolja ezt a tartalmat a fájlba:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
2. Futtassa egy ehhez hasonló parancsot:
   
    ```PS
   
        New-AzureRmResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` a csoport van, ahol szeretné az új erőforrásokat hozhat létre.
   * `-TemplateFile` az egyéni paraméter előzheti meg kell.
   * `-appName` A létrehozandó erőforrás neve.

Más paramétereket adhat hozzá – a sablon a Paraméterek szakaszban ezek leírását találja.

## <a name="to-get-the-instrumentation-key"></a>A kialakítási kulcs beszerzése
Miután létrehozott egy alkalmazás-erőforrást, érdemes a kialakítási kulcsot: 

```PS
    $resource = Find-AzureRmResource -ResourceNameEquals "<YOUR APP NAME>" -ResourceType "Microsoft.Insights/components"
    $details = Get-AzureRmResource -ResourceId $resource.ResourceId
    $ikey = $details.Properties.InstrumentationKey
```


<a id="price"></a>
## <a name="set-the-price-plan"></a>Állítsa be a tarifacsomag

Beállíthatja a [árképzési csomag](pricing.md).

Az alkalmazás-erőforrás létrehozásához a vállalati ár csomaggal, a fenti sablon használatával:

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

* Ha csak szeretné használni az alapértelmezett alapszintű árképzési csomag, kihagyhatja a CurrentBillingFeatures erőforrás a sablonból.
* Ha azt szeretné, a tarifacsomag módosítása az összetevő-erőforrás létrehozása után, használhatja a sablont, amely a "microsoft.insights/components" erőforrás az áttekinthetőség kedvéért kihagyja. Emellett nincs megadva a `dependsOn` csomópont számlázási erőforrásból. 

A frissített ár terv ellenőrzéséhez tekintse meg a **használat és becsült költségek lapon** panel a böngészőben. **Frissítse a böngészőt nézetet** , ellenőrizze, hogy a legutóbbi állapotára.



## <a name="add-a-metric-alert"></a>Metrikariasztás hozzáadása

Metrikariasztás beállítása az alkalmazás-erőforrást, egy időben, egyesítse a sablonfájlt ehhez hasonló kódok:

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

A sablon hívhat meg, amikor azt is megteheti ezt a paramétert:

    `-responseTime 2`

Ön természetesen parametrizálja, hogy a többi mező. 

Ismerje meg az alkalmazástípus-nevek és más riasztási szabályok konfigurációs adatait, manuálisan hozzon létre egy szabályt, és ezután pedig megvizsgálhatja a [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Rendelkezésre állási teszt hozzáadása

Ebben a példában a ping a vizsgálat (egy egyoldalas tesztelése).  

**Két részből áll** a rendelkezésre állási teszt: a teszt magát, és a riasztást, amely értesíti, a hibák.

Egyesítse a következő kódot a sablon fájlba, amely létrehozza az alkalmazást.

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

Fedezze fel a kódok, egyéb tesztelési helyeket, vagy összetettebb webes tesztek automatizálhatja, hozza létre manuálisan egy példa, és majd paraméterezni a kódot az [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>További erőforrások hozzáadása

Bármely más bármiféle-erőforrás létrehozását automatizálhatja, hozzon létre például manuálisan, majd másolja és paraméterezni a kódot [Azure Resource Manager](https://resources.azure.com/). 

1. Nyissa meg [az Azure Resource Manager](https://resources.azure.com/). Lefelé végignavigál `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`, az alkalmazás-erőforrást. 
   
    ![Navigálás az Azure erőforrás-kezelő](./media/powershell/01.png)
   
    *Összetevők* alkalmazások megjelenítése az alapszintű Application Insights-erőforrások vannak. Nincsenek külön erőforrásokat a kapcsolódó riasztási szabályokat és a rendelkezésre állási webes tesztekről.
2. Másolja a JSON az összetevő-t a megfelelő helyre `template1.json`.
3. Törli ezeket a tulajdonságokat:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Nyissa meg a webteszt és alertrules szakaszokat, és a JSON-t az egyes elemek másolása a sablonhoz. (Nem másolja a webtesztet illetve alertrules csomópontok: Nyissa meg a távoli alatt azokat az elemeket.)
   
    Minden egyes webes teszt tartozó riasztási szabály, rendelkezik, így a beilleszteni, mindkettő.
   
    Riasztások a metrikák is használható. [Metrikák nevei](powershell-alerts.md#metric-names).
5. Ez a sor beszúrása az egyes erőforrások:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>A sablon paraméterezése
Most már rendelkezik paraméterekkel az egyedi nevek helyett. A [sablon paraméterezni](../../azure-resource-manager/resource-group-authoring-templates.md), kifejezések használatával ír egy [segítő funkciók](../../azure-resource-manager/resource-group-template-functions.md). 

Nem lehet paraméterezni a karakterlánc csak része, ezért a `concat()` karakterláncok hozhat létre.

Az alábbiakban a helyettesítések továbbá győződjön meg arról, hogy érdemes példái. Nincsenek minden behelyettesítési többszöri felbukkanását. Szükség lehet másokkal a sablonban. Ezek a példák a paramétereket és változókat meghatározott felső részén a sablont.

| find | cserélje le |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>` resourceId('microsoft.insights/components',` <br/> ` parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (kisbetű) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>` Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`<br/>Törölje a GUID azonosítót és -azonosítót. |

### <a name="set-dependencies-between-the-resources"></a>Az erőforrások közötti függőségek beállítása
Az Azure az erőforrásokat, szigorú sorrendben kell beállítania. Ahhoz, hogy egy telepítés befejezése előtt a következő kezdődik, adja hozzá a függőség sorokat:

* A rendelkezésre állási teszt erőforrás:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* Az egy rendelkezésre állási teszthez tartozó riasztási erőforrás:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>További lépések
Más automatizálási cikkek:

* [Hozzon létre egy Application Insights-erőforrást](powershell-script-create-resource.md) -sablon használata nélkül, gyorsan elvégezhető.
* [Riasztások beállítása](powershell-alerts.md)
* [Létrehozhat webes teszteket](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Az Azure Diagnostics küldése az Application Insights-ba](powershell-azure-diagnostics.md)
* [Az Azure-bA a Githubról történő üzembe helyezése](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Kiadási jegyzetek létrehozása](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)

