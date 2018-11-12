---
title: Azure CLI-vel az Azure Cosmos DB-erőforrások kezelése |} A Microsoft Docs
description: Az Azure Cosmos DB fiókot, adatbázist és tárolók kezelése az Azure CLI használatával.
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: mjbrown
ms.openlocfilehash: 3446f4f71349d0b7290a2514edf46efb37203324
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019149"
---
# <a name="manage-azure-cosmos-db-resources-using-azure-cli"></a>Azure CLI-vel az Azure Cosmos DB-erőforrások kezelése

Ez az útmutató azt ismerteti, automatizált felügyelete az Azure Cosmos DB-fiókok, adatbázisok és tárolók az Azure CLI-vel a parancsokat. A tároló átviteli teljesítményének skálázása parancsokat is tartalmaz. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](https://docs.microsoft.com/cli/azure/cosmosdb) között érheti el. A további példákat is talál [Azure CLI-minták az Azure Cosmos DB](cli-samples.md), beleértve, hogyan hozhat létre, és a MongoDB, a Gremlin, a Cassandra és a Table API Cosmos DB-fiókok, adatbázisok és tárolók kezelése.

A CLI-példaszkript létrehoz egy Azure Cosmos DB SQL API-fiókot, adatbázist és tároló.  

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Azure Cosmos DB-fiók létrehozása az SQL API-munkamenet-konzisztencia, az USA keleti RÉGIÓJA és USA nyugati RÉGIÓJA, több főkiszolgálós nyissa meg az Azure CLI vagy a cloud shell, és futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --kind GlobalDocumentDB \
   --default-consistency-level "Session" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy Cosmos DB-adatbázisban, nyissa meg az Azure CLI vagy a cloud shell, és futtassa a következő parancsot:

```azurecli-interactive
az cosmosdb database create \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup"
```

## <a name="create-a-container"></a>Tároló létrehozása

Hozzon létre egy Cosmos DB-tárolón az 1000 RU/s és a egy partíciókulcsot, nyissa meg az Azure CLI vagy a cloud shell, és futtassa a következő parancsot:

```azurecli-interactive
# Create a container
az cosmosdb collection create \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --partition-key-path = "/myPartitionKey" \
   --throughput 1000
```

## <a name="change-the-throughput-of-a-container"></a>Az átviteli sebességet egy adott tároló módosítása

Egy Cosmos DB-tároló átviteli kapacitást 400 RU/s módosításához nyissa meg az Azure CLI vagy a cloud shellben, és futtassa a következő parancsot:

```azurecli-interactive
# Update container throughput
az cosmosdb collection update \
   --collection-name "myContainer" \
   --name "myCosmosDbAccount" \
   --db-name "myDatabase" \
   --resource-group "myResourceGroup" \
   --throughput 400
```

## <a name="list-account-keys"></a>Fiók kulcsainak listázása

Az Azure Cosmos DB-fiók létrehozásakor a szolgáltatás két fő kulcsot, amely az Azure Cosmos DB-fiók elérésekor hitelesítéshez használt állít elő. Azáltal, hogy a két hozzáférési kulcsot, az Azure Cosmos DB lehetővé teszi a kulcsok az Azure Cosmos DB-fiók megszakítás nélkül. Írásvédett kulcsok hitelesítéséhez a csak olvasható műveletekhez is elérhetők. Nincsenek két írható és olvasható kulcsok (elsődleges és másodlagos) és a két csak olvasható kulcsok (elsődleges és másodlagos). A fiók a kulcsok beszerezheti a következő parancs futtatásával:

```azurecli-interactive
# List account keys
az cosmosdb list-keys \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="list-connection-strings"></a>Kapcsolati karakterláncok listája

Csatlakozás a Cosmos DB-fiókot az alkalmazás a kapcsolati karakterláncot a következő paranccsal lehet beolvasni.

```azurecli-interactive
# List connection strings
az cosmosdb list-connection-strings \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup"
```

## <a name="regenerate-account-key"></a>Fiókkulcs újragenerálása

Az Azure Cosmos DB-fiókot, és rendszeres időközönként biztonságosabb fenntarthatja a kapcsolatokat, módosítania kell a hozzáférési kulcsokat. Lehetővé teszi az Azure Cosmos DB-fiók az egyik kulcs, míg más hozzáférési kulcs kapcsolatok fenntartásához két kulcsot vannak hozzárendelve.

```azurecli-interactive
# Regenerate account key
az cosmosdb regenerate-key \
   --name "myCosmosDbAccount"\
   --resource-group "myResourceGroup" \
   --key-kind primary
```

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatban lásd:

- [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
- [Azure parancssori felület referenciája](https://docs.microsoft.com/cli/azure/cosmosdb)
- [További Azure CLI-minták az Azure Cosmos DB-hez](cli-samples.md)