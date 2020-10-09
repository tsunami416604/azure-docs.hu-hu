---
title: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Gremlin API-erőforrásokhoz
description: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Gremlin API-erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 3e4b912d086065f28c56fd4af309d373b811a8ec
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91838628"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-vel egy adatbázishoz vagy gráfhoz Azure Cosmos DB-Gremlin API-hoz

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.12.1 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Gremlin-adatbázist közös átviteli sebességgel, valamint egy dedikált átviteli sebességgel rendelkező Gremlin gráfot, majd frissíti az adatátvitelt az adatbázis és a gráf esetében is. A szkript ezután áttelepíti a standard-ról az autoskálázási átviteli sebességre, majd beolvassa az átviteli sebesség értékét az áttelepítés után.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Throughput operations for a Gremlin database and graph.")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Létrehoz egy Azure Cosmos DB-fiókot. |
| [az cosmosdb Gremlin Database Create](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Létrehoz egy Azure Cosmos Gremlin-adatbázist. |
| [az cosmosdb Gremlin Graph Create](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Létrehoz egy Azure Cosmos Gremlin gráfot. |
| [az cosmosdb Gremlin Database átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Az RU/s frissítése egy Azure Cosmos Gremlin-adatbázishoz. |
| [az cosmosdb Gremlin Graph átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Az RU/s frissítése egy Azure Cosmos Gremlin gráfhoz. |
| [az cosmosdb Gremlin Database átviteli sebesség migrálása](/cli/azure/cosmosdb/gremlin/database/throughput#az_cosmosdb_gremlin_database_throughput_migrate) | Átviteli sebesség áttelepíteni egy Azure Cosmos Gremlin-adatbázisra. |
| [az cosmosdb Gremlin Graph átviteli sebesség migrálása](/cli/azure/cosmosdb/gremlin/graph/throughput#az_cosmosdb_gremlin_graph_throughput_migrate) | Átviteli sebesség áttelepíteni egy Azure Cosmos Gremlin gráfhoz. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
