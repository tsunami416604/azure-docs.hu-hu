---
title: Az Application Insights-erőforrás létrehozása a PowerShell parancsfájl |} Microsoft Docs
description: Application Insights-erőforrások létrehozásának automatizálása.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: f0082c9b-43ad-4576-a417-4ea8e0daf3d9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: d06b44246b694c0d2a83503ecd1ae0cedfadd9ec
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-parancsfájl egy Application Insights-erőforrás létrehozásához


Ha figyelni kívánt új alkalmazás - vagy egy alkalmazás új verziójának – a [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), állít be egy új erőforrást a Microsoft Azure-ban. Ehhez az erőforráshoz, ahol az alkalmazásból a telemetriai adatok elemzése és jelenik meg. 

Egy új erőforrást a PowerShell segítségével automatizálható.

Például ha egy mobileszköz-alkalmazást fejleszt, valószínű, hogy tetszőleges időpontban lesz közzétett változatának ügyfelei az alábbiakra használhatják az alkalmazást. Nem szeretnénk vegyes különböző verzióiból a telemetriai adatok eredményt. Ezért az egyes buildekhez új erőforrás létrehozása a felépítési folyamat kap.

> [!NOTE]
> Ha szeretne létrehozni az erőforráscsoport összes egyszerre, fontolja meg a [létrehozása az Azure-sablon alapján erőforrások](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Hozzon létre egy Application Insights-erőforrást parancsprogram
Tekintse meg a megfelelő parancsmag specifikációk:

* [New-AzureRmResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell-parancsfájl*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzureRmAccount / Connect-AzureRmAccount

# Set the name of the Application Insights Resource

$appInsightsName = "TestApp"

# Set the application name used for the value of the Tag "AppInsightsApp" 

$applicationTagName = "MyApp"

# Set the name of the Resource Group to use.  
# Default is the application name.
$resourceGroupName = "MyAppResourceGroup"

###################################################
# Create the Resource and Output the name and iKey
###################################################

# Select the azure subscription
Select-AzureSubscription -SubscriptionName "MySubscription"

# Create the App Insights Resource


$resource = New-AzureRmResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzureRmRoleAssignment `
  -SignInName "myteam@fabrikam.com" `
  -RoleDefinitionName Owner `
  -Scope $resource.ResourceId 


# Display iKey
Write-Host "App Insights Name = " $resource.Name
Write-Host "IKey = " $resource.Properties.InstrumentationKey

```

## <a name="what-to-do-with-the-ikey"></a>Mi a teendő, ha a iKey
Az egyes erőforrások azonosítja a rendszerállapot-kulcsok (iKey). A iKey az erőforrás-létrehozási parancsfájl kimenete. A build script kell biztosítania az Application Insights SDK iKey az alkalmazásba ágyazott.

Két módon lehet elérhetővé tenni a iKey az SDK-val:

* A [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*ikey*`</instrumentationkey>`
* Vagy a [inicializálási kód](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*iKey*`";`

## <a name="see-also"></a>Lásd még
* [Az Application Insights és webes teszt erőforrások létrehozása sablonból](app-insights-powershell.md)
* [A PowerShell segítségével az Azure diagnosztikai figyelés beállítása](app-insights-powershell-azure-diagnostics.md) 
* [Értesítések beállítása a PowerShell használatával](app-insights-powershell-alerts.md)

