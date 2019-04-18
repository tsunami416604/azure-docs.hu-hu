---
title: Azure CLI-vel az Azure Cosmos DB-erőforrások kezelése
description: Az Azure Cosmos DB fiókot, adatbázist és tárolók kezelése az Azure CLI használatával.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 4/8/2019
ms.author: mjbrown
ms.openlocfilehash: 1d19e58b2d1381725de490b68d9e4d00a2ca4cb6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495481"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure CLI-vel az Azure Cosmos-erőforrások kezelése

Ez az útmutató azt ismerteti, automatizált felügyelete az Azure Cosmos DB-fiókok, adatbázisok és tárolók az Azure CLI-vel a gyakori parancsok. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](https://docs.microsoft.com/cli/azure/cosmosdb) között érheti el. A további példákat is talál [Azure CLI-minták az Azure Cosmos DB](cli-samples.md), beleértve, hogyan hozhat létre, és a MongoDB, a Gremlin, a Cassandra és a Table API Cosmos DB-fiókok, adatbázisok és tárolók kezelése.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

SQL API és a munkamenet-konzisztencia az USA keleti RÉGIÓJA és USA nyugati régiójában, az Azure Cosmos DB-fiók létrehozásához futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations EastUS=0 WestUS=1 \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Az Azure Cosmos-fiók neve csak kisbetűket tartalmazhatnak.

## <a name="create-a-database"></a>Adatbázis létrehozása

Cosmos DB-adatbázis létrehozásához futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Tároló létrehozása

Hozzon létre egy Cosmos DB-tárolón az RU/s 400-as és a egy partíciókulcsot, futtassa a következő parancsot:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --partition-key-path /myPartitionKey \
   --throughput 400
```

## <a name="change-the-throughput-of-a-container"></a>Az átviteli sebességet egy adott tároló módosítása

Az átviteli sebességet egy Cosmos DB-tárolón, 1000 RU/s módosításához futtassa a következő parancsot:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name myContainer \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup \
   --throughput 1000
```

## <a name="list-account-keys"></a>Fiók kulcsainak listázása

Cosmos-fiókja a kulcsok lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Kapcsolati karakterláncok listája

A kapcsolati karakterláncok Cosmos-fiókja lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Fiókkulcs újragenerálása

Cosmos-fiókja egy új elsődleges kulcs újragenerálása, futtassa a következő parancsot:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatban lásd:

- [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
- [Azure parancssori felület referenciája](https://docs.microsoft.com/cli/azure/cosmosdb)
- [További Azure CLI-minták az Azure Cosmos DB-hez](cli-samples.md)
