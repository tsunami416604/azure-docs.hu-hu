---
title: Ru/s frissítése EGY SQL (Core) API-adatbázishoz és -tárolóhoz az Azure Cosmos DB-hez
description: Ru/s frissítése EGY SQL (Core) API-adatbázishoz és -tárolóhoz az Azure Cosmos DB-hez
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: cde0615a5312372992d2604f32809a983c248a2b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275394"
---
# <a name="update-rus-for-a-sql-core-api-database-and-container-for-azure-cosmos-db-using-azure-cli"></a>Ru/s frissítése egy SQL (Core) API-adatbázishoz és az Azure Cosmos DB tárolójához az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl létrehoz egy SQL (Core) API-adatbázis megosztott átviteli és egy SQL (Core) API-tároló dedikált átviteli, majd frissíti az átviteli szintet mind az adatbázis és a tároló.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Update RU/s for a SQL database and container.")]

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
| [az cosmosdb sql adatbázis létrehozása](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Létrehoz egy Azure Cosmos SQL (Core) adatbázist. |
| [az cosmosdb sql tároló létrehozása](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Létrehoz egy Azure Cosmos SQL (Core) tárolót. |
| [az cosmosdb sql adatbázis átviteli sorának frissítése](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Az Azure Cosmos SQL (Core) adatbázis ru/s frissítése. |
| [az cosmosdb sql tároló átviteli frissítés](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Az Azure Cosmos SQL (Core) tárolójának RU/s frissítése. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
