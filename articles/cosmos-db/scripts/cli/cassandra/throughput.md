---
title: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Cassandra API erőforrásokhoz
description: Azure CLI-parancsfájlok átviteli sebesség (RU/s) műveletekhez Azure Cosmos DB Cassandra API erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 09ea092afcfee6223330b1ad887d73ea2f78db59
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843022"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-vel a Azure Cosmos DB-Cassandra API

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.12.1 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript egy Cassandra-területet hoz létre a közös átviteli sebességgel és egy Cassandra-táblázatot dedikált átviteli sebességgel, majd frissíti a térköz és a tábla átviteli sebességét. A szkript ezután áttelepíti a standard-ról az autoskálázási átviteli sebességre, majd beolvassa az átviteli sebesség értékét az áttelepítés után.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Throughput operations for Cassandra keyspace and table.")]

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
| [az cosmosdb Cassandra-re Space átviteli sebességének frissítése](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az-cosmosdb-cassandra-keyspace-throughput-update) | Az RU/s frissítése az Azure Cosmos Cassandra Space-re. |
| [az cosmosdb Cassandra Table átviteli sebességének frissítése](/cli/azure/cosmosdb/cassandra/table/throughput#az-cosmosdb-cassandra-table-throughput-update) | Az RU/s frissítése Azure Cosmos Cassandra-táblához. |
| [az cosmosdb Cassandra](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | Áttelepítheti az átviteli sebességet egy Azure Cosmos Cassandra-hely számára. |
| [az cosmosdb Cassandra Table átviteli sebesség migrálása](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | Az átviteli sebesség áttelepíthető egy Azure Cosmos Cassandra-táblára. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
