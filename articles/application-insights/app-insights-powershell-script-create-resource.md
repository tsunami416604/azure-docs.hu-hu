---
title: Application Insights-erőforrás létrehozása a PowerShell-parancsfájl |} A Microsoft Docs
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
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 0f2bf1e318729ae8ccb0f4f521b2a795cf932a86
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091324"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-parancsfájl egy Application Insights-erőforrás létrehozásához


Ha szeretne egy új alkalmazás - vagy egy alkalmazás új verziójának – figyelheti a [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), beállíthat egy új erőforrást a Microsoft Azure-ban. Ehhez az erőforráshoz, ahol az alkalmazásából származó telemetriai adatokat elemzi és jelenik meg. 

Új erőforrás létrehozása a PowerShell használatával automatizálható.

Például ha egy mobileszköz-alkalmazást fejleszt, akkor valószínű, hogy bármikor lesz az alkalmazás az ügyfelek által használt számos közzétett verziója. Nem kívánja a telemetriai adatok eredményt lenne szerencsés különböző verzióit. Így kaphat a build-folyamatoknak minden build új erőforrás létrehozása.

> [!NOTE]
> Ha azt szeretné, létrehozhat egy csoportot az erőforrások ugyanabban az időben, érdemes lehet [létrehozása az Azure-sablon használatával erőforrások](app-insights-powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Parancsfájl egy Application Insights-erőforrás létrehozása
Tekintse meg a megfelelő parancsmag adatait tartalmazza:

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

## <a name="what-to-do-with-the-ikey"></a>Mi a teendő a Rendszerállapotkulcsot az
Az egyes erőforrások azonosítására a kialakítási kulcsot (Rendszerállapotkulcsot). A Rendszerállapotkulcsot az erőforrás-létrehozási parancsprogrammal kimeneteként. A buildszkript a Rendszerállapotkulcsot az Application Insights SDK-t az alkalmazásba ágyazott kell biztosítania.

A Rendszerállapotkulcsot az SDK-val elérhetővé két módja van:

* A [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*rendszerállapotkulcsot*`</instrumentationkey>`
* Vagy a [inicializálási kódot](app-insights-api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*Rendszerállapotkulcsot*`";`

## <a name="see-also"></a>Lásd még
* [Az Application Insights és a webes teszt erőforrások létrehozása sablonból](app-insights-powershell.md)
* [A PowerShell-lel az Azure diagnostics-figyelés beállítása](app-insights-powershell-azure-diagnostics.md) 
* [Riasztások beállítása a PowerShell használatával](app-insights-powershell-alerts.md)

