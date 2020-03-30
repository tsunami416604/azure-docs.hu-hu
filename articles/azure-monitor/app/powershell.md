---
title: Az Azure Application Insights automatizálása a PowerShell segítségével | Microsoft dokumentumok
description: Az Azure Resource Manager-sablon használatával automatizálhatja az erőforrások, riasztások és rendelkezésre állási tesztek létrehozását és kezelését a PowerShellben.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 9494b659b5b4357f3190c45d8cc72c4e130f0ecc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275879"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Az Application Insights-erőforrások kezelése a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ez a cikk bemutatja, hogyan automatizálhatja az [Application Insights-erőforrások](../../azure-monitor/app/app-insights-overview.md) létrehozását és frissítését automatikusan az Azure Resource Management használatával. Ezt például egy létrehozási folyamat részeként teheti meg. Az alapvető Application Insights-erőforrással együtt [rendelkezésre állási webes teszteket](../../azure-monitor/app/monitor-web-app-availability.md)hozhat létre, [riasztásokat](../../azure-monitor/app/alerts.md)állíthat be, beállíthatja az [árképzési sémát,](pricing.md)és más Azure-erőforrásokat hozhat létre.

Ezeknek az erőforrásoknak a létrehozásához a legfontosabb az Azure Resource Manager JSON-sablonjai. [Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md) Az alapvető eljárás a következő: töltse le a meglévő erőforrások JSON-definícióit; paraméterezbizonyos értékeket, például neveket; majd futtassa a sablont, amikor új erőforrást szeretne létrehozni. Több erőforrást is becsomagolhat, így egyszerre hozhatja létre őket – például egy alkalmazásfigyelőt rendelkezésre állási tesztekkel, riasztásokkal és tárhellyel a folyamatos exportáláshoz. Van néhány finomság a paraméterekhez, amit itt fogunk elmagyarázni.

## <a name="one-time-setup"></a>Egyszeri beállítás
Ha korábban még nem használta a PowerShellt az Azure-előfizetésével:

Telepítse az Azure Powershell-modult arra a számítógépre, ahol futtatni szeretné a parancsfájlokat:

1. Telepítse a [Microsoft Web Platform Installer (v5 vagy újabb)](https://www.microsoft.com/web/downloads/platform.aspx)programot.
2. A Microsoft Azure Powershell telepítéséhez használja.

A Resource Manager-sablonok használata mellett az [Application Insights PowerShell-parancsmagok](https://docs.microsoft.com/powershell/module/az.applicationinsights)gazdag készlete is rendelkezésre áll, amelyek megkönnyítik az Application Insights-erőforrások programozott konfigurálását. A parancsmagok által engedélyezett képességek a következők:

* Application Insights-erőforrások létrehozása és törlése
* Az Application Insights-erőforrások és tulajdonságaik listájának leése
* Folyamatos exportálás létrehozása és kezelése
* Alkalmazáskulcsok létrehozása és kezelése
* A napi korlát beállítása
* Az árképzési terv beállítása

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Az Application Insights-erőforrások létrehozása PowerShell-parancsmag használatával

Az alábbiakban bemutatja, hogyan hozhat létre új Application Insights-erőforrást az Azure East US adatközpontban a [New-AzApplicationInsights-parancsmag](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights) használatával:

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Alkalmazáselemzési erőforrások létrehozása Erőforrás-kezelő sablonnal

Az alábbiakban bemutatja, hogyan hozhat létre új Application Insights-erőforrást egy Erőforrás-kezelő sablon használatával.

### <a name="create-the-azure-resource-manager-template"></a>Az Azure Resource Manager sablon létrehozása

Hozzon létre egy új .json `template1.json` fájlt – nevezzük meg ebben a példában. Másolja be a tartalmat a következőbe:

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
                "defaultValue": 0,
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
                    "ApplicationId": "[parameters('appName')]",
                    "retentionInDays": "[parameters('retentionInDays')]"
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

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Új Application Insights-erőforrás létrehozása az Erőforrás-kezelő sablon használatával

1. A PowerShellben jelentkezzen be az Azure-ba a`$Connect-AzAccount`
2. A környezet beállítása előfizetésre`Set-AzContext "<subscription ID>"`
2. Új központi telepítés futtatásával hozzon létre egy új Application Insights-erőforrást:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName`Az a csoport, ahol létre szeretné hozni az új erőforrásokat.
   * `-TemplateFile`az egyéni paraméterek előtt kell bekövetkeznie.
   * `-appName`A létrehozandó erőforrás neve.

Hozzáadhat más paramétereket is - a leírásokat a sablon paraméterek szakaszában találja.

## <a name="get-the-instrumentation-key"></a>Szerezd meg a műszerkulcsot

Az alkalmazás-erőforrás létrehozása után a műszerezési kulcsot szeretné használni: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Az Application Insights-erőforrás számos más tulajdonságának listájának megtekintéséhez használja a következőket:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

További tulajdonságok érhetők el a parancsmagokon keresztül:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

A parancsmagok paramétereinek [részletes dokumentációját](https://docs.microsoft.com/powershell/module/az.applicationinsights) olvassa el.  

## <a name="set-the-data-retention"></a>Az adatmegőrzés beállítása 

Az Application Insights-erőforrás aktuális adatmegőrzésének lekérnie használhatja az OSS eszköz [ARMClient.](https://github.com/projectkudu/ARMClient)  (Tudjon meg többet ARMClient a cikkek [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) és [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).)  Íme egy példa `ARMClient`a használatával, hogy az aktuális megőrzés:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

A megőrzés beállításához a parancs hasonló PUT:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Ha az adatmegőrzést 365 napra szeretné állítani a fenti sablon használatával, futtassa a következőt:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

A következő parancsfájl is használható a megőrzési módosítása. Másolja a parancsfájlt `Set-ApplicationInsightsRetention.ps1`a mentéshez .

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Ez a szkript a következőképpen használható:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>A napi korlát beállítása

A napi korlát tulajdonságainak lekért, használja a [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) parancsmag: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

A napi korlát tulajdonságainak beállításához használja ugyanazt a parancsmamot. Ha például a felső határt 300 GB/nap-ra szeretné állítani,

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

Az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével is beszerezheti és beállíthatja a napi korlát paramétereit.  Az aktuális értékek leéséhez használja a következőket:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

## <a name="set-the-daily-cap-reset-time"></a>A napi korlát visszaállítási idejének beállítása

A napi korlát visszaállítási idejének beállításához használhatja az [ARMClient programot.](https://github.com/projectkudu/ARMClient) Íme egy példa `ARMClient`a használatával, hogy állítsa be a visszaállítási idő egy új óra (ebben a példában 12:00 UTC):

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview "{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'ResetTime':12}}"
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Az árképzési terv beállítása 

Az aktuális díjszabási terv lekért, használja a [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan) parancsmag:

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Az árképzési terv beállításához használja ugyanazt `-PricingPlan` a parancsmatot a megadott:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

A fenti Erőforrás-kezelő sablon használatával beállíthatja egy meglévő Application Insights-erőforrás díjszabási tervét is, kihagyva a "microsoft.insights/components" erőforrást és a `dependsOn` számlázási erőforrás csomópontját. Ha például a GB-onkénti tervre (korábbi nevén alaptervre) szeretné beállítani, futtassa a következőket:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

A `priceCode` definíció ja:

|árkód|Terv|
|---|---|
|1|GB-onként (korábbi nevén alapterv)|
|2|Csomópontonként (korábbi nevén Enterprise csomag)|

Végül az [ARMClient](https://github.com/projectkudu/ARMClient) segítségével lekaphatja és beállíthatja az árképzési terveket és a napi korlát paramétereit.  Az aktuális értékek leéséhez használja a következőket:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
```

És beállíthatja az összes ilyen paramétert a következő használatával:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName/CurrentBillingFeatures?api-version=2018-05-01-preview
"{'CurrentBillingFeatures':['Basic'],'DataVolumeCap':{'Cap':200,'ResetTime':12,'StopSendNotificationWhenHitCap':true,'WarningThreshold':90,'StopSendNotificationWhenHitThreshold':true}}"
```

Ez a napi korlátot 200 GB/nap-ra állítja, a napi korlát visszaállítási idejét 12:00 UTC-re állítja, e-maileket küld mind a felső korlát elérésekor, mind a figyelmeztetési szint elérésekor, és a figyelmeztetési küszöbértéket a felső korlát 90% -ára állítja.  

## <a name="add-a-metric-alert"></a>Metrikariasztás hozzáadása

A metrikariasztások létrehozásának automatizálásához tekintse meg a [metrikariasztások sabloncikket](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-a-simple-static-threshold-metric-alert)


## <a name="add-an-availability-test"></a>Rendelkezésre állási teszt hozzáadása

A rendelkezésre állási tesztek automatizálásához olvassa el a [metrikariasztások sabloncikket.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-create-templates#template-for-an-availability-test-along-with-a-metric-alert)

## <a name="add-more-resources"></a>További erőforrások hozzáadása

Bármely más erőforrás létrehozásának automatizálásához hozzon létre egy példát manuálisan, majd másolja és paraméterezze be a kódot az [Azure Resource Manager](https://resources.azure.com/)ből. 

1. Nyissa meg [az Azure Resource Manager](https://resources.azure.com/)t. Navigáljon `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components`lefelé a , az alkalmazás erőforrás. 
   
    ![Navigáció az Azure Resource Explorerben](./media/powershell/01.png)
   
    *Az összetevők* az alkalmazások megjelenítéséhez az Application Insights alapvető erőforrásai. A társított riasztási szabályokhoz és a rendelkezésre állási webes tesztekhez külön erőforrások tartoznak.
2. Másolja az alkatrész JSON-ját a `template1.json`megfelelő helyre a programban.
3. A következő tulajdonságok törlése:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Nyissa `webtests` meg `alertrules` a szakaszokat, és másolja a JSON-t az egyes elemekhez a sablonba. (Ne másolja a `webtests` vagy `alertrules` csomópontok: menj be az elemeket alattuk.)
   
    Minden webes teszthez tartozik egy riasztási szabály, ezért mindkettőt másolnia kell.
   
    Riasztásokat is megadhat a metrikákon. [Metrikanevek](powershell-alerts.md#metric-names).
5. Szúrja be ezt a sort az egyes erőforrásokba:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>A sablon paraméterezése
Most le kell cserélnie az adott neveket paraméterekre. Sablon [paraméterezéséhez](../../azure-resource-manager/templates/template-syntax.md)kifejezéseket kell írni a segítő [függvények segítségével.](../../azure-resource-manager/templates/template-functions.md) 

Nem lehet paraméterezni csak egy karakterlánc `concat()` egy részét, ezért karakterláncok létrehozásához használható.

Íme néhány példa a cserék érdemes tenni. Az egyes helyettesítések több előfordulása is előfordulnak. Előfordulhat, hogy másokra is szüksége lesz a sablonban. Ezek a példák a sablon tetején definiált paramétereket és változókat használják.

| find | csere |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"`(kisbetű) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Függőségek beállítása az erőforrások között
Az Azure-nak szigorú sorrendben kell beállítania az erőforrásokat. Ha meg szeretne győződni arról, hogy az egyik beállítás befejeződik a következő kezdete előtt, adja hozzá a függőségi sorokat:

* Az elérhetőségi teszterőforrásban:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* A rendelkezésre állási teszt riasztási erőforrásában:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>További lépések
Egyéb automatizálási cikkek:

* [Hozzon létre egy Application Insights-erőforrást](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource#creating-a-resource-automatically) – gyors metódus sablon használata nélkül.
* [Riasztások beállítása](powershell-alerts.md)
* [Hozzon létre webes teszteket](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Az Azure Diagnostics küldése az Application Insights-ba](powershell-azure-diagnostics.md)
* [Üzembe helyezés az Azure-ba a GitHubról](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Kiadási jegyzetek létrehozása](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)