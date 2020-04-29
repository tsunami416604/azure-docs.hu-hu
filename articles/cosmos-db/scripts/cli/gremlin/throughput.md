---
title: Az RU/s frissítése Gremlin-adatbázishoz és gráfhoz Azure Cosmos DB
description: Az RU/s frissítése Gremlin-adatbázishoz és gráfhoz Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 0ef01a50e4827ec2ba96ce5d370bc2386e0dbfd6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "71275520"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Gremlin-adatbázis és az Azure CLI-vel való Azure Cosmos DB gráf frissítése az RU/s használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.0.73 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Gremlin-adatbázist közös átviteli sebességgel, valamint egy dedikált átviteli sebességgel rendelkező Gremlin gráfot, majd frissíti az adatátvitelt az adatbázis és a gráf esetében is.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/gremlin/throughput.sh "Update RU/s for a Gremlin database and graph.")]

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
| [az cosmosdb Gremlin Database Create](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Létrehoz egy Azure Cosmos Gremlin-adatbázist. |
| [az cosmosdb Gremlin Graph Create](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Létrehoz egy Azure Cosmos Gremlin gráfot. |
| [az cosmosdb Gremlin Database átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Az RU/s frissítése egy Azure Cosmos Gremlin-adatbázishoz. |
| [az cosmosdb Gremlin Graph átviteli sebességének frissítése](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Az RU/s frissítése egy Azure Cosmos Gremlin gráfhoz. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
