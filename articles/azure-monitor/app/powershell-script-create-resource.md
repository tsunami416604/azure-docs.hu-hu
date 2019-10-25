---
title: PowerShell-parancsfájl Application Insights erőforrás létrehozásához | Microsoft Docs
description: Application Insights erőforrások létrehozásának automatizálása.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 11/19/2016
ms.openlocfilehash: 11245d0f9d6e6b86a5d0249df65b33f851bee9d7
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72820686"
---
# <a name="powershell-script-to-create-an-application-insights-resource"></a>PowerShell-parancsfájl egy Application Insights-erőforrás létrehozásához

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha egy új alkalmazást vagy egy alkalmazás új verzióját kívánja figyelni az [Azure Application Insights](https://azure.microsoft.com/services/application-insights/)-vel, új erőforrást kell beállítania a Microsoft Azureban. Ez az erőforrás az alkalmazás telemetria-adatainak elemzése és megjelenítése. 

A PowerShell használatával automatizálható egy új erőforrás létrehozása.

Ha például mobileszköz-alkalmazást fejleszt, akkor valószínű, hogy az alkalmazás számos közzétett verzióját fogja használni az ügyfelek által használt alkalmazások számára. Nem szeretné, hogy a különböző verziókból származó telemetria eredmények összekeverve legyenek. Így a létrehozási folyamattal új erőforrást hozhat létre az egyes buildekhez.

> [!NOTE]
> Ha egyszerre több erőforrást szeretne létrehozni, érdemes lehet [az erőforrásokat egy Azure-sablon használatával](powershell.md)létrehozni.
> 
> 

## <a name="script-to-create-an-application-insights-resource"></a>Parancsfájl egy Application Insights erőforrás létrehozásához
Tekintse meg a vonatkozó parancsmag specifikációit:

* [Új – AzResource](https://msdn.microsoft.com/library/mt652510.aspx)
* [New-AzRoleAssignment](https://msdn.microsoft.com/library/mt678995.aspx)

*PowerShell-parancsfájl*  

```powershell


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

## <a name="what-to-do-with-the-ikey"></a>Mi a teendő a Rendszerállapotkulcsot
Az egyes erőforrásokat a kialakítási kulcs (Rendszerállapotkulcsot) azonosítja. A Rendszerállapotkulcsot az erőforrás-létrehozási parancsfájl kimenete. A felépítési parancsfájlnak meg kell adnia a Rendszerállapotkulcsot az alkalmazásba beágyazott Application Insights SDK-val.

A Rendszerállapotkulcsot kétféleképpen teheti elérhetővé az SDK számára:

* A [ApplicationInsights. config fájlban](../../azure-monitor/app/configuration-with-applicationinsights-config.md): 
  * `<instrumentationkey>`*rendszerállapotkulcsot*`</instrumentationkey>`
* Vagy az [inicializálási kódban](../../azure-monitor/app/api-custom-events-metrics.md): 
  * `Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey = "`*rendszerállapotkulcsot*`";`

## <a name="see-also"></a>Lásd még:
* [Application Insights-és webes tesztelési erőforrások létrehozása sablonokból](powershell.md)
* [Az Azure Diagnostics figyelésének beállítása a PowerShell-lel](powershell-azure-diagnostics.md) 
* [Riasztások beállítása a PowerShell használatával](powershell-alerts.md)

