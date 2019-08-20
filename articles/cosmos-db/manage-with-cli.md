---
title: Azure Cosmos DB erőforrások kezelése az Azure CLI-vel
description: Az Azure CLI használatával kezelheti Azure Cosmos DB-fiókját, adatbázisát és tárolóit.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: f9d8bf9161343e4b36a3c16209873962b69d8af5
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615213"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure Cosmos-erőforrások kezelése az Azure CLI-vel

Az alábbi útmutató általános parancsokat tartalmaz a Azure Cosmos DB-fiókok,-adatbázisok és-tárolók Azure CLI-vel való felügyeletének automatizálásához. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](https://docs.microsoft.com/cli/azure/cosmosdb) között érheti el. Az [Azure CLI](cli-samples.md)-mintákban további példákat is találhat Azure Cosmos DBhoz, beleértve Cosmos db fiókok, adatbázisok és tárolók létrehozását és kezelését a MongoDB, a Gremlin, a Cassandra és a Table APIhoz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

SQL API-val rendelkező Azure Cosmos DB-fiók létrehozásához az USA keleti régiójában és az USA nyugati régiójában, a következő parancs futtatásával:

```azurecli-interactive
az cosmosdb create \
   --name mycosmosdbaccount # must be lowercase and < 31 characters \
   --resource-group myResourceGroup \
   --kind GlobalDocumentDB \
   --default-consistency-level Session \
   --locations regionName=EastUS failoverPriority=0 isZoneRedundant=False \
   --locations regionName=WestUS failoverPriority=1 isZoneRedundant=False \
   --enable-multiple-write-locations false
```

> [!IMPORTANT]
> Az Azure Cosmos-fiók nevének kisbetűnek kell lennie.

## <a name="create-a-database"></a>Adatbázis létrehozása

Cosmos-adatbázis létrehozásához futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb database create \
   --name mycosmosdbaccount \
   --db-name myDatabase \
   --resource-group myResourceGroup
```

## <a name="create-a-container"></a>Tároló létrehozása

A következő parancs futtatásával hozzon létre egy Cosmos-tárolót a 400-es RU/s-vel és egy partíciós kulccsal:

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

## <a name="change-the-throughput-of-a-container"></a>Tároló átviteli sebességének módosítása

Ha módosítani szeretné egy Cosmos-tároló átviteli sebességét 1000 RU/s értékre, futtassa a következő parancsot:

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

A Cosmos-fiók kulcsainak lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
# List account keys
az cosmosdb keys list \
   --name  mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="list-connection-strings"></a>Kapcsolatok karakterláncok listázása

A Cosmos-fiókhoz tartozó kapcsolódási karakterláncok beszerzéséhez futtassa a következő parancsot:

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup
```

## <a name="regenerate-account-key"></a>Fiók kulcsának újralétrehozása

A Cosmos-fiókhoz tartozó új elsődleges kulcs újralétrehozásához futtassa a következő parancsot:

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name mycosmosdbaccount \
   --resource-group myResourceGroup \
   --key-kind primary
```

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információkért lásd:

- [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
- [Azure parancssori felület referenciája](https://docs.microsoft.com/cli/azure/cosmosdb)
- [További Azure CLI-minták a Azure Cosmos DB](cli-samples.md)
