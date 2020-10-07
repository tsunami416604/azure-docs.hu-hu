---
title: Az RU/s frissítése a Cassandra Spaces és a Table for Azure Cosmos DB
description: Az RU/s frissítése Cassandra a szóköz és a Table for Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c0026202e8f110123a7360a5fda521947ed8e9ee
ms.sourcegitcommit: 23aa0cf152b8f04a294c3fca56f7ae3ba562d272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2020
ms.locfileid: "91803653"
---
# <a name="update-rus-for-a-cassandra-keyspace-and-table-using-azure-cli"></a>Az RU/s frissítése az Azure CLI-vel rendelkező Cassandra Space és Table használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.9.1 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript egy Cassandra-területet hoz létre a közös átviteli sebességgel és egy Cassandra-táblázatot dedikált átviteli sebességgel, majd frissíti a térköz és a tábla átviteli sebességét.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Update RU/s for Cassandra keyspace and table.")]

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
| [az cosmosdb Cassandra Space Create](/cli/azure/cosmosdb/cassandra/keyspace#az-cosmosdb-cassandra-keyspace-create) | Létrehoz egy Azure Cosmos Cassandra-területet. |
| [az cosmosdb Cassandra Table Create](/cli/azure/cosmosdb/cassandra/table#az-cosmosdb-cassandra-table-create) | Létrehoz egy Azure Cosmos Cassandra-táblázatot. |
| [az cosmosdb Cassandra-re Space átviteli sebességének frissítése](/cli/azure/cosmosdb/cassandra/keyspace/throughput?view=azure-cli-latest&preserve-view=true#az-cosmosdb-cassandra-keyspace-throughput-update) | Az RU/s frissítése az Azure Cosmos Cassandra Space-re. |
| [az cosmosdb Cassandra Table átviteli sebességének frissítése](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Az RU/s frissítése Azure Cosmos Cassandra-táblához. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
