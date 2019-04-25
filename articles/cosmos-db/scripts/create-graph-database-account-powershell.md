---
title: Az Azure PowerShell-szkript – létrehozása egy Azure Cosmos DB Gremlin API-fiók
description: Azure PowerShell-példaszkript – Azure Cosmos DB Gremlin API-fiók létrehozása
ms.service: cosmos-db
author: rockboyfor
ms.author: v-yeche
ms.subservice: cosmosdb-graph
ms.devlang: PowerShell
ms.topic: sample
origin.date: 05/29/2018
ms.date: 04/15/2019
ms.reviewer: sngun
ms.openlocfilehash: 06f4379a0100b4a0d5335ed6e8c99bb16160715d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60446635"
---
<!--Verify sucessfully-->
# <a name="azure-cosmos-db-create-a-gremlin-api-account-using-powershell"></a>Az Azure Cosmos DB: PowerShell-lel a Gremlin API-fiók létrehozása

Ez a PowerShell-példaszkript egy Azure Cosmos DB Gremlin API-fiókot hoz létre. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

```powershell
# Set the Azure resource group name and location
$resourceGroupName = "myResourceGroupgraph1"
$resourceGroupLocation = "China East"

# Create the resource group
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation

# Database name
$DBName = "testdbgraph1"

# Write and read locations and priorities for the database
$locations = @(@{"locationName"="China East"; 
                 "failoverPriority"=0}, 
               @{"locationName"="China North"; 
                  "failoverPriority"=1})

# IP addresses that can access the database through the firewall
$iprangefilter = "10.0.0.1"

# Consistency policy
$consistencyPolicy = @{"defaultConsistencyLevel"="BoundedStaleness";
                       "maxIntervalInSeconds"="10"; 
                       "maxStalenessPrefix"="200"}

# Create a Gremlin API Cosmos DB account
$Capability= "EnableGremlin"

$capabilities= @(@{"name"=$Capability})

# DB properties
$DBProperties = @{"databaseAccountOfferType"="Standard"; 
                          "locations"=$locations; 
                          "consistencyPolicy"=$consistencyPolicy;
                          "capabilities"=$capabilities; 
                          "ipRangeFilter"=$iprangefilter}

# Create the database
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
                    -ApiVersion "2015-04-08" `
                    -ResourceGroupName $resourceGroupName `
                    -Location $resourceGroupLocation `
                    -Name $DBName `
                    -PropertyObject $DBProperties

```

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Olyan logikai kiszolgálót hoz létre, amely egy adatbázist vagy rugalmas készletet üzemeltet. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../powershell-samples.md) találhat.

<!--Verify sucessfully-->
<!--Update_Description: update link -->