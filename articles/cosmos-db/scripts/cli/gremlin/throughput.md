---
title: Ru/s frissítése Egy Gremlin-adatbázishoz és grafikonhoz az Azure Cosmos DB-hez
description: Ru/s frissítése Egy Gremlin-adatbázishoz és grafikonhoz az Azure Cosmos DB-hez
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 9/25/2019
ms.openlocfilehash: 0ef01a50e4827ec2ba96ce5d370bc2386e0dbfd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "71275520"
---
# <a name="update-rus-for-a-gremlin-database-and-graph-for-azure-cosmos-db-using-azure-cli"></a>Ru/s frissítése egy Gremlin-adatbázishoz és grafikonhoz az Azure Cosmos DB-hoz az Azure CLI használatával

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Ha úgy dönt, hogy helyileg telepíti és használja a CLI-t, ez a témakör megköveteli, hogy az Azure CLI 2.0.73-as vagy újabb verzióját futassza. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl létrehoz egy Gremlin-adatbázis megosztott átviteli és egy Gremlin grafikon dedikált átviteli, majd frissíti az átviteli szintet mind az adatbázis és a grafikon.

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
| [az cosmosdb gremlin adatbázis létrehozása](/cli/azure/cosmosdb/gremlin/database#az-cosmosdb-gremlin-database-create) | Létrehoz egy Azure Cosmos Gremlin adatbázist. |
| [az cosmosdb gremlin grafikon létrehozása](/cli/azure/cosmosdb/gremlin/graph#az-cosmosdb-gremlin-graph-create) | Létrehoz egy Azure Cosmos Gremlin grafikont. |
| [az cosmosdb gremlin adatbázis átviteli frissítés](/cli/azure/cosmosdb/gremlin/database/throughput#az-cosmosdb-gremlin-database-throughput-update) | Frissítse a RU/s egy Azure Cosmos Gremlin adatbázis. |
| [az cosmosdb gremlin graph átviteli frissítés](/cli/azure/cosmosdb/gremlin/graph/throughput#az-cosmosdb-gremlin-graph-throughput-update) | Frissítse a RU/s egy Azure Cosmos Gremlin grafikon. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB CLI-ről az [Azure Cosmos DB CLI dokumentációjában](/cli/azure/cosmosdb)olvashat bővebben.

Az Azure Cosmos DB CLI parancsfájlminták megtalálhatók az [Azure Cosmos DB CLI GitHub-tárházban.](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb)
