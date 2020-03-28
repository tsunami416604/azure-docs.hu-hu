---
title: Ru/s frissítése az Azure Cosmos DB MongoDB API-hoz való adatbázishoz és gyűjteményhez
description: Ru/s frissítése az Azure Cosmos DB MongoDB API-hoz való adatbázishoz és gyűjteményhez
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 20516a66fe664e415b97e40beacd77c34c7ccaea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275506"
---
# <a name="update-rus-for-a-database-and-collection-for-mongodb-api-for-azure-cosmos-db-using-azure-cli"></a>Ru/s frissítése az Azure Cosmos DB MongoDB API-jának adatbázisához és gyűjteményéhez az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl létrehoz egy adatbázist megosztott átviteli és egy gyűjtemény dedikált átviteli azure Cosmos DB A MongoDB API-t, majd frissíti az átviteli szintet mind az adatbázis és a gyűjtemény.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Update RU/s for an Azure Cosmos DB MongoDB API database and collection.")]

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
| [az cosmosdb mongodb adatbázis átviteli frissítés](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Az Azure Cosmos MongoDB API-adatbázis több es szementusa. |
| [az cosmosdb mongodb gyűjtemény átviteli frissítés](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Az Azure Cosmos MongoDB API-gyűjtemény hez futó virtuális államok frissítése. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
