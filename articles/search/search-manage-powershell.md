---
title: Powershell-parancsfájlok – Azure Search az Azure Search szolgáltatás kezelése
description: A PowerShell-parancsfájlok az Azure Search szolgáltatás kezelése. Hozzon létre vagy frissítése az Azure Search szolgáltatást, és az Azure Search az adminisztrációs kulcsok kezelése
author: HeidiSteen
manager: cgronlun
tags: azure-resource-manager
services: search
ms.service: search
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/15/2016
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 86f8eebb8e174b4a4d4dbdc9def516e23b79a131
ms.sourcegitcommit: a8948ddcbaaa22bccbb6f187b20720eba7a17edc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56591649"
---
# <a name="manage-your-azure-search-service-with-powershell"></a>A PowerShell-lel az Azure Search szolgáltatás kezelése
> [!div class="op_single_selector"]
> * [Portál](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Ez a témakör ismerteti a PowerShell-parancsok az Azure Search-szolgáltatás a felügyeleti feladatok végrehajtásához. Végigvezetjük a search szolgáltatás létrehozása, méretezését, és az API-kulcsok kezelése.
Ezek a parancsok párhuzamos az elérhető lehetőségek a [Azure Search felügyeleti REST API](https://docs.microsoft.com/rest/api/searchmanagement).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Azure PowerShell-lel kell rendelkeznie. A telepítési utasításokért lásd: [Azure PowerShell telepítése](/powershell/azure/overview).
* Az Azure-előfizetéshez a PowerShell az alább ismertetett kell bejelentkeznie.

Először be kell bejelentkezés az Azure a következő paranccsal:

    Connect-AzAccount

Adja meg az e-mail-címét az Azure-fiókjával, és a hozzá tartozó jelszót a Microsoft Azure bejelentkezési párbeszédpanel.

Másik megoldásként is [egy egyszerű szolgáltatást a nem interaktív bejelentkezési](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

Ha több Azure-előfizetéssel rendelkezik, állítsa be az Azure-előfizetéshez szeretne. Az aktuális előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

    Get-AzSubscription | sort SubscriptionName | Select SubscriptionName

Válassza ki az előfizetést, futtassa a következő parancsot. A következő példában, az előfizetés nevét a `ContosoSubscription`.

    Select-AzSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Parancsok segítségével első lépései
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzResource

## <a name="next-steps"></a>További lépések
Most, hogy a szolgáltatás létrejött, a következő lépésekkel lehet: hozhat létre egy [index](search-what-is-an-index.md), [-index lekérdezése](search-query-overview.md), és végül hozzon létre és kezelheti saját keresőalkalmazást, amely az Azure Search használja.

* [Az Azure Search-index létrehozása az Azure Portalon](search-create-index-portal.md)
* [A keresési ablakkal az Azure Portalon az Azure Search-index lekérdezése](search-explorer.md)
* [Adatok betöltése az egyéb szolgáltatások indexelő beállítása](search-indexer-overview.md)
* [Az Azure Search használata a .NET-ben](search-howto-dotnet-sdk.md)
* [Az Azure Search-forgalom elemzése](search-traffic-analytics.md)

