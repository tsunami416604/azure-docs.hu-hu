---
title: Ru/s frissítése cassandra kulcstérhez és az Azure Cosmos DB táblához
description: Ru/s frissítése Cassandra számára egy keyspace és tábla az Azure Cosmos DB-hez
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 3615157543d826050b8adf0e6ae59bf62ad9cb1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71827303"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Ru/s frissítése Cassandra kulcstérhez és táblához az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl létrehoz egy Cassandra kulcsteret megosztott átviteli fokkal és egy dedikált átviteli fokkal rendelkező Cassandra-táblát, majd frissíti az átviteli szintet mind a keyspace, mind a table számára.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

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
| [az cosmosdb cassandra keyspace létrehozása](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Létrehoz egy Azure Cosmos Cassandra kulcstér. |
| [az cosmosdb cassandra tábla létrehozása](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Létrehoz egy Azure Cosmos Cassandra-táblát. |
| [az cosmosdb cassandra keyspace átviteli frissítés](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest#az-cosmosdb-cassandra-keyspace-throughput-update) | Frissítse a RU/s egy Azure Cosmos Cassandra keyspace. |
| [az cosmosdb cassandra tábla átviteli frissítés](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Frissítse a RU/s egy Azure Cosmos Cassandra-tábla. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
