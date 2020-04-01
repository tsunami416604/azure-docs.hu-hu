---
title: Adatbázis és gyűjtemény létrehozása az Azure Cosmos DB MongoDB API-jához
description: Adatbázis és gyűjtemény létrehozása az Azure Cosmos DB MongoDB API-jához
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: ee9b2a40a5913af131db47ec1f4c2c065b27468a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275499"
---
# <a name="create-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Adatbázis és gyűjtemény létrehozása az Azure Cosmos DB MongoDB API-jához az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/create.sh "Create an Azure Cosmos DB MongoDB API account, database, and collection.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb mongodb adatbázis létrehozása](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Létrehoz egy Azure Cosmos MongoDB API-adatbázist. |
| [az cosmosdb mongodb gyűjtemény létrehozása](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Létrehoz egy Azure Cosmos MongoDB API-gyűjteményt. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
