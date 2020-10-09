---
title: Azure CLI-parancsfájlok az adatátviteli (RU/s) műveletekhez Azure Cosmos DB Core (SQL) API-erőforrásokhoz
description: Azure CLI-parancsfájlok az adatátviteli (RU/s) műveletekhez Azure Cosmos DB Core (SQL) API-erőforrásokhoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 5ad1e40f12609e708a2bb818175146967b7dccf2
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839469"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-container-for-azure-cosmos-db-core-sql-api"></a>Átviteli sebesség (RU/s) műveletek az Azure CLI-vel az Azure Cosmos DB Core (SQL) API-hoz készült adatbázishoz vagy tárolóhoz

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a témakörhöz az Azure CLI 2.12.1 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy core (SQL) API-adatbázist a megosztott átviteli sebességgel, valamint egy core (SQL) API-tárolót dedikált átviteli sebességgel, majd frissíti az adatátviteli sebességet az adatbázis és a tároló számára is. A szkript ezután áttelepíti a standard-ról az autoskálázási átviteli sebességre, majd beolvassa az átviteli sebesség értékét az áttelepítés után.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/throughput.sh "Throughput operations for a SQL database and container.")]

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
| [az cosmosdb SQL Database Create](/cli/azure/cosmosdb/sql/database#az-cosmosdb-sql-database-create) | Létrehoz egy Azure Cosmos Core-(SQL-) adatbázist. |
| [az cosmosdb SQL Container Create](/cli/azure/cosmosdb/sql/container#az-cosmosdb-sql-container-create) | Létrehoz egy Azure Cosmos Core (SQL) tárolót. |
| [az cosmosdb SQL Database átviteli sebességének frissítése](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-update) | Egy Azure Cosmos Core-(SQL-) adatbázis teljesítményének frissítése. |
| [az cosmosdb SQL Container átviteli sebességének frissítése](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-update) | Egy Azure Cosmos Core-(SQL-) tároló frissítési sebességének frissítése. |
| [az cosmosdb SQL Database átviteli sebesség migrálása](/cli/azure/cosmosdb/sql/database/throughput#az-cosmosdb-sql-database-throughput-migrate) | Áttelepítési sebesség egy Azure Cosmos Core (SQL) adatbázishoz. |
| [az cosmosdb SQL Container átviteli sebesség migrálása](/cli/azure/cosmosdb/sql/container/throughput#az-cosmosdb-sql-container-throughput-migrate) | Áttelepítési sebesség egy Azure Cosmos Core (SQL) tárolóhoz. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>Következő lépések

A Azure Cosmos DB CLI-vel kapcsolatos további információkért lásd: [Azure Cosmos db parancssori felület dokumentációja](/cli/azure/cosmosdb).

A CLI-szkriptek összes Azure Cosmos DB a [Azure Cosmos db CLI GitHub-tárházban](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)található.
