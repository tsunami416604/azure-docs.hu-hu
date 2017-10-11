---
title: "Azure Search kezelése a Powershell-parancsfájlok |} Microsoft Docs"
description: "Az Azure Search szolgáltatás PowerShell-parancsfájlokkal kezelheti. Hozzon létre vagy frissíteni az Azure Search szolgáltatást, és Azure Search adminisztrációs kulcsok kezelése"
services: search
documentationcenter: 
author: seansaleh
manager: mblythe
editor: 
tags: azure-resource-manager
ms.assetid: 9b3dc1f2-3619-4235-ba1f-d2d6f5c45dd5
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: powershell
ms.date: 08/15/2016
ms.author: seasa
ms.openlocfilehash: aa51c846efef12461ec382274199bc049c42aaa3
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="manage-your-azure-search-service-with-powershell"></a>A PowerShell segítségével az Azure Search szolgáltatás kezelése
> [!div class="op_single_selector"]
> * [Portal](search-manage.md)
> * [PowerShell](search-manage-powershell.md)
> 
> 

Ez a témakör ismerteti a PowerShell-parancsokat az Azure Search szolgáltatások felügyeleti feladatok elvégzéséhez. Végigvezetjük a search szolgáltatás létrehozása, skálázás, és annak API-kulcsok kezelése.
Ezek a parancsok párhuzamosan használható felügyeleti lehetőségekről a [Azure Search felügyeleti REST API](http://msdn.microsoft.com/library/dn832684.aspx).

## <a name="prerequisites"></a>Előfeltételek
* Az Azure PowerShell 1.0-ás vagy újabb verzióját kell rendelkeznie. Útmutatásért lásd: [telepítse és konfigurálja az Azure Powershellt](/powershell/azure/overview).
* Az Azure-előfizetéshez az alább ismertetett PowerShell kell bejelentkeznie.

Először meg kell ezzel a paranccsal az Azure bejelentkezési azonosító:

    Login-AzureRmAccount

A Microsoft Azure bejelentkezési párbeszédpanelen adja meg az e-mail cím, az Azure-fiókjával, és a hozzá tartozó jelszó.

Alternatív megoldásként [egy egyszerű szolgáltatást a nem interaktív bejelentkezési](../azure-resource-manager/resource-group-authenticate-service-principal.md).

Ha több Azure-előfizetéssel rendelkezik, az Azure-előfizetéshez be kell. Az aktuális előfizetések listájának megtekintéséhez futtassa ezt a parancsot.

    Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Adja meg az előfizetést, futtassa a következő parancsot. A következő példában az előfizetés neve: `ContosoSubscription`.

    Select-AzureRmSubscription -SubscriptionName ContosoSubscription

## <a name="commands-to-help-you-get-started"></a>Parancsok segítségével első lépései
    $serviceName = "your-service-name-lowercase-with-dashes"
    $sku = "free" # or "basic" or "standard" for paid services
    $location = "West US"
    # You can get a list of potential locations with
    # (Get-AzureRmResourceProvider -ListAvailable | Where-Object {$_.ProviderNamespace -eq 'Microsoft.Search'}).Locations
    $resourceGroupName = "YourResourceGroup" 
    # If you don't already have this resource group, you can create it with 
    # New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Register the ARM provider idempotently. This must be done once per subscription
    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.Search"

    # Create a new search service
    # This command will return once the service is fully created
    New-AzureRmResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateUri "https://gallery.azure.com/artifact/20151001/Microsoft.Search.1.0.9/DeploymentTemplates/searchServiceDefaultTemplate.json" `
        -NameFromTemplate $serviceName `
        -Sku $sku `
        -Location $location `
        -PartitionCount 1 `
        -ReplicaCount 1

    # Get information about your new service and store it in $resource
    $resource = Get-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # View your resource
    $resource

    # Get the primary admin API key
    $primaryKey = (Invoke-AzureRmResourceAction `
        -Action listAdminKeys `
        -ResourceId $resource.ResourceId `
        -ApiVersion 2015-08-19).PrimaryKey

    # Regenerate the secondary admin API Key
    $secondaryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/regenerateAdminKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action secondary).SecondaryKey

    # Create a query key for read only access to your indexes
    $queryKeyDescription = "query-key-created-from-powershell"
    $queryKey = (Invoke-AzureRmResourceAction `
        -ResourceType "Microsoft.Search/searchServices/createQueryKey" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19 `
        -Action $queryKeyDescription).Key

    # View your query key
    $queryKey

    # Delete query key
    Remove-AzureRmResource `
        -ResourceType "Microsoft.Search/searchServices/deleteQueryKey/$($queryKey)" `
        -ResourceGroupName $resourceGroupName `
        -ResourceName $serviceName `
        -ApiVersion 2015-08-19

    # Scale your service up
    # Note that this will only work if you made a non "free" service
    # This command will not return until the operation is finished
    # It can take 15 minutes or more to provision the additional resources
    $resource.Properties.ReplicaCount = 2
    $resource | Set-AzureRmResource

    # Delete your service
    # Deleting your service will delete all indexes and data in the service
    $resource | Remove-AzureRmResource

## <a name="next-steps"></a>Következő lépések
Most, hogy a szolgáltatás létrehozása a következő lépésekre is: build egy [index](search-what-is-an-index.md), [lekérdezheti az indexét](search-query-overview.md), és végül létrehozása és kezelése a saját keresési alkalmazás az Azure Search használ.

* [Az Azure Search-index létrehozása az Azure portálon](search-create-index-portal.md)
* [Keresési ablak használata az Azure-portálon az Azure Search-index lekérdezése](search-explorer.md)
* [Adatok betöltése a többitől indexelő beállítása](search-indexer-overview.md)
* [Azure Search .NET használata](search-howto-dotnet-sdk.md)
* [Az Azure Search forgalom elemzése](search-traffic-analytics.md)

