---
title: PowerShell-parancsfájl az Azure Cosmos DB MongoDB-hoz való API-jában való listázáshoz és beési műveletekhez
description: Azure PowerShell-parancsfájl – Az Azure Cosmos DB-lista és a MongoDB API-hoz való műveletek beszerezése
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 686b140915e90adefa15084c2e8317404b91ed09
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365902"
---
# <a name="list-and-get-databases-and-graphs-for-azure-cosmos-db---mongodb-api"></a>Adatbázisok és grafikonok listázása és lehívása az Azure Cosmos DB-hoz – MongoDB API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-list-get.ps1 "List or get databases or collections for MongoDB API")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Listázza a Cosmos DB-fiókokat, vagy lead egy megadott Cosmos DB-fiókot. |
| [Get-AzCosmosDBMongoDBAdatbázis](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabase) | Listázza a Cosmos DB MongoDB API-adatbázisokat egy fiókban, vagy lead egy adott Cosmos DB MogoDB API-adatbázist egy fiókban. |
| [Get-AzCosmosDBMongoDBGyűjtemény](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollection) | Listázza a Cosmos DB MongoDB API-gyűjteményeket, vagy lekap egy megadott Cosmos DB MongoDB API-gyűjteményt egy adatbázisban |
|**Azure erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.