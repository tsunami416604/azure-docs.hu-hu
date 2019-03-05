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
ms.topic: conceptual
ms.date: 11/19/2016
ms.author: mbullwin
ms.openlocfilehash: 91790f372dce4322d316b42c4bfa7ad36625c91d
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57315571"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-parancsfájl egy Application Insights-erőforrás létrehozásához

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha szeretne egy új alkalmazás - vagy egy alkalmazás új verziójának – figyelheti a [Azure Application Insights](https://azure.microsoft.com/services/application-insights/), beállíthat egy új erőforrást a Microsoft Azure-ban. Ehhez az erőforráshoz, ahol az alkalmazásából származó telemetriai adatokat elemzi és jelenik meg. 

Új erőforrás létrehozása a PowerShell használatával automatizálható.

Például ha egy mobileszköz-alkalmazást fejleszt, akkor valószínű, hogy bármikor lesz az alkalmazás az ügyfelek által használt számos közzétett verziója. Nem kívánja a telemetriai adatok eredményt lenne szerencsés különböző verzióit. Így kaphat a build-folyamatoknak minden build új erőforrás létrehozása.

> [!NOTE]
> Ha azt szeretné, létrehozhat egy csoportot az erőforrások ugyanabban az időben, érdemes lehet [létrehozása az Azure-sablon használatával erőforrások](powershell.md).
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Parancsfájl egy Application Insights-erőforrás létrehozása
Tekintse meg a megfelelő parancsmag adatait tartalmazza:

* [New-AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell-parancsfájl*  

```PowerShell


###########################################
# Set Values
###########################################

# If running manually, uncomment before the first 
# execution to login to the Azure Portal:

# Connect-AzAccount / Connect-AzAccount

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


$resource = New-AzResource `
  -ResourceName $appInsightsName `
  -ResourceGroupName $resourceGroupName `
  -Tag @{ applicationType = "web"; applicationName = $applicationTagName} `
  -ResourceType "Microsoft.Insights/components" `
  -Location "East US" `  # or North Europe, West Europe, South Central US
  -PropertyObject @{"Application_Type"="web"} `
  -Force

# Give owner access to the team

New-AzRoleAssignment `
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

* A [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*rendszerállapotkulcsot*`</instrumentationkey>`
* Vagy a [inicializálási kódot](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*Rendszerállapotkulcsot*`";`

## <a name="see-also"></a>Lásd még
* [Az Application Insights és a webes teszt erőforrások létrehozása sablonból](powershell.md)
* [A PowerShell-lel az Azure diagnostics-figyelés beállítása](powershell-azure-diagnostics.md) 
* [Riasztások beállítása a PowerShell használatával](powershell-alerts.md)

