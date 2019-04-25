---
title: Azure PowerShell-Példaszkript – többrégiós replikáció az Azure Cosmos DB-hez
description: Azure PowerShell-példaszkript – Többrégiós replikáció az Azure Cosmos DB-ben
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
author: rockboyfor
ms.author: v-yeche
ms.devlang: PowerShell
ms.topic: sample
origin.date: 05/10/2017
ms.date: 04/15/2019
ms.reviewer: sngun
ms.openlocfilehash: 02628401bed6e65784bf7ddc4a7082f617640cf9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60448469"
---
# <a name="replicate-an-azure-cosmos-db-database-account-in-multiple-regions-and-configure-failover-priorities-using-powershell"></a>Egy Azure Cosmos DB-adatbázisfiók replikálása több régióban és a feladatátvételi prioritások konfigurálása a PowerShell használatával

Ez a példa replikál bármilyen Azure Cosmos DB-adatbázisfiókot több régióban, és konfigurálja a feladatátvételi prioritásokat a PowerShell használatával. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

<!--First make chinaeast2 as wirte region-->
<!--Second make chinanorth as write region-->
<!--Last add chinanorth2 new region-->

```powershell
# Set the Azure resource group name and location
$resourceGroupName = "myResourceGroup"
$resourceGroupLocation = "chinaeast2"

# Database name
$DBName = "testdb"
# Distribution locations
$locations = @(@{"locationName"="chinaeast"; 
                 "failoverPriority"=2},
               @{"locationName"="chinanorth"; 
                 "failoverPriority"=1},
               @{"locationName"="chinaeast2"; 
                 "failoverPriority"=0})


# Create the resource group
New-AzResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation

# Consistency policy
$consistencyPolicy = @{"maxIntervalInSeconds"="10"; 
                       "maxStalenessPrefix"="200"}

# DB properties
$DBProperties = @{"databaseAccountOfferType"="Standard";
                  "Kind"="GlobalDocumentDB"; 
                  "locations"=$locations; 
                  "consistencyPolicy"=$consistencyPolicy;}

# Create the database
New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
                    -ApiVersion "2015-04-08" `
                    -ResourceGroupName $resourceGroupName `
                    -Location $resourceGroupLocation `
                    -Name $DBName `
                    -PropertyObject $DBProperties

# Update failoverpolicy to make China North as a write region
$NewfailoverPolicies = @(@{"locationName"="chinanorth"; "failoverPriority"=0}, @{"locationName"="chinaeast2"; "failoverPriority"=1}, @{"locationName"="chinaeast"; "failoverPriority"=2} )

Invoke-AzResourceAction `
    -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName `
    -Name $DBName `
    -Parameters @{"failoverPolicies"=$NewfailoverPolicies}

# Add a new locations with priorities
$newLocations = @(@{"locationName"="chinanorth"; 
                 "failoverPriority"=0},
               @{"locationName"="chinaeast"; 
                 "failoverPriority"=1},
               @{"locationName"="chinanorth2"; 
                 "failoverPriority"=2},
               @{"locationName"="chinaeast2";
                 "failoverPriority"=3})

# Updated properties
$updateDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$newLocations;}

# Update the database with the properties
Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName `
    -Name $DBName `
    -PropertyObject $UpdateDBProperties

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
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Módosítja az adatbázisfiókot. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../powershell-samples.md) találhat.

<!-- Update_Description: update meta properties -->