---
title: PowerShell-parancsfájl az Azure Cosmos DB MongoDB-hoz való API-jában való adatbázis és gyűjtemény létrehozásához
description: Azure PowerShell-parancsfájl – Az Azure Cosmos DB mongoDB API-adatbázist és -gyűjteményt hoz létre
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 59e30de7a0f069cfc69db715115f776034e94909
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365883"
---
# <a name="create-a-database-and-collection-for-azure-cosmos-db---mongodb-api"></a>Adatbázis és gyűjtemény létrehozása az Azure Cosmos DB-hoz – MongoDB API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-create.ps1 "Create a database and collection for MongoDB API")]

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
| [Új-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Új Cosmos DB-fiókot hoz létre. |
| [Set-AzCosmosDBMongoDBAdatbázis](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbmongodbdatabase) | Új at hoz létre, vagy frissíti a meglévő Cosmos DB MongoDB API-adatbázis. |
| [Új-AzCosmosDBMongoDBIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Új MongoDB API-indexet hoz létre. |
| [Set-AzCosmosDBMongoDBGyűjtemény](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbmongodbcollection) | Új at hoz létre, vagy frissíti a meglévő Cosmos DB SQL Collection. |
|**Azure erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.