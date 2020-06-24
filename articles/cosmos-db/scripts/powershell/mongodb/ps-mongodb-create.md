---
title: PowerShell-szkript az Azure Cosmos MongoDB API-adatbázis és-gyűjtemény létrehozásához
description: Azure PowerShell szkript – Azure Cosmos MongoDB API-adatbázis és-gyűjtemény létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 1c3e4816e5bf2d104557fa3ed5ef2923d075e237
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85125566"
---
# <a name="create-a-database-and-collection-for-azure-cosmos-db---mongodb-api"></a>Adatbázis és gyűjtemény létrehozása a Azure Cosmos DB-MongoDB API-hoz

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

| Parancs | Jegyzetek |
|---|---|
|**Azure Cosmos DB**| |
| [Új – AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Létrehoz egy Cosmos DB fiókot. |
| [Új – AzCosmosDBMongoDBDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbdatabase) | Létrehoz egy MongoDB API-adatbázist. |
| [Új – AzCosmosDBMongoDBIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Létrehoz egy MongoDB API-indexet. |
| [Új – AzCosmosDBMongoDBCollection](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbcollection) | Létrehoz egy MongoDB API-gyűjteményt. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.