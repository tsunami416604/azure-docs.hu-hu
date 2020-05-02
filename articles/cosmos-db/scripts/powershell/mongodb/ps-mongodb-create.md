---
title: PowerShell-szkript adatbázis és gyűjtemény létrehozásához Azure Cosmos DB API-MongoDB
description: Azure PowerShell script-Azure Cosmos DB MongoDB API-adatbázis és-gyűjtemény létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 06d386f80b6a9c1116e71a629a2c445da3082836
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653016"
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

| Parancs | Megjegyzések |
|---|---|
|**Azure Cosmos DB**| |
| [Új – AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Új Cosmos DB-fiók létrehozása. |
| [Set-AzCosmosDBMongoDBDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbmongodbdatabase) | Létrehoz egy új vagy frissített egy meglévő MongoDB API-adatbázist. |
| [Új – AzCosmosDBMongoDBIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbmongodbindex) | Létrehoz egy új MongoDB API-indexet. |
| [Set-AzCosmosDBMongoDBCollection](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbmongodbcollection) | Létrehoz egy újat, vagy frissíti a meglévő MongoDB API-gyűjteményt. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.