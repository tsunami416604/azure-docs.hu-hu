---
title: Azure Cosmos DB erőforrások kezelése az Azure CLI-vel
description: Az Azure CLI használatával kezelheti Azure Cosmos DB-fiókját, adatbázisát és tárolóit.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: mjbrown
ms.openlocfilehash: 325840f8961fac49e599f1aa567ad8d4137820b4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251881"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Azure Cosmos-erőforrások kezelése az Azure CLI-vel

Az alábbi útmutató az Azure Cosmos DB-fiókok, -adatbázisok és -tárolók felügyeletének Azure CLI-vel történő automatizálásához használható általános parancsokat ismerteti. Az Azure Cosmos DB CLI-parancsainak egyes referenciaoldalait az [Azure CLI referenciái](https://docs.microsoft.com/cli/azure/cosmosdb) között érheti el. Az [Azure CLI-mintákban](cli-samples.md)további példákat is találhat Azure Cosmos DBhoz, beleértve Cosmos db fiókok, adatbázisok és tárolók létrehozását és kezelését a MongoDB, a Gremlin, a Cassandra és a Table APIhoz.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakörben leírt lépésekhez az Azure parancssori felületének 2.0-s vagy annál újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos DB fiókot SQL API-val, munkamenet-konzisztencia az USA 2. nyugati régiójában és az USA 2. keleti régiójában:

> [!IMPORTANT]
> Az Azure Cosmos-fiók nevének kisbetűnek kell lennie, és 31 karakternél rövidebbnek kell lennie.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount' #needs to be lower case and less than 31 characters

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --default-consistency-level Session \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --locations regionName='East US 2' failoverPriority=1 isZoneRedundant=False
```

## <a name="add-or-remove-regions"></a>Régiók hozzáadása vagy eltávolítása

Hozzon létre egy Azure Cosmos-fiókot két régióval, adjon hozzá egy régiót, és távolítson el egy régiót.

> [!NOTE]
> Nem lehet egyszerre hozzáadni vagy eltávolítani a régiókat `locations` és módosítani az Azure Cosmos-fiók egyéb tulajdonságait. A régiók módosításait külön műveletként kell végrehajtani, mint a fiók erőforrásának egyéb módosításait.
> [!NOTE]
> Ezzel a paranccsal hozzáadhat és eltávolíthat régiókat, de nem teszi lehetővé a feladatátvételi prioritások módosítását, illetve manuális feladatátvételt is indíthat. Lásd: a [feladatátvételi prioritás beállítása](#set-failover-priority) és a [manuális feladatátvétel elindítása](#trigger-manual-failover).

```azurecli-interactive
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName= "South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName= "West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName= "East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Több írási régió engedélyezése

Több főkiszolgáló engedélyezése Cosmos-fiókhoz

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Feladatátvételi prioritás beállítása

Az automatikus feladatátvételhez konfigurált Azure Cosmos-fiók feladatátvételi prioritásának beállítása

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>Automatikus feladatátvétel engedélyezése

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>Manuális feladatátvétel indítása

> [!CAUTION]
> Ha a Priority = 0 prioritású régiót módosítja, manuális feladatátvételt indít egy Azure Cosmos-fiókhoz. A többi prioritási változás nem indít el feladatátvételt.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a id="list-account-keys"></a>Az összes fiók kulcsainak listázása

Egy Cosmos-fiókhoz tartozó összes kulcs beolvasása.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Csak olvasható fiókok kulcsainak listázása

Egy Cosmos-fiók írásvédett kulcsainak beolvasása.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>Kapcsolatok karakterláncok listázása

A Cosmos-fiókhoz tartozó kapcsolatok karakterláncának beolvasása.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>Fiók kulcsának újralétrehozása

Új kulcs újralétrehozása Cosmos-fiókhoz.

```azurecli-interactive
# Regenerate secondary account keys
# key-kind values: primary, primaryReadonly, secondary, secondaryReadonly
az cosmosdb keys regenerate \
    -n $accountName \
    -g $resourceGroupName \
    --key-kind secondary
```

## <a name="create-a-database"></a>Adatbázis létrehozása

Hozzon létre egy Cosmos-adatbázist.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName
```

## <a name="create-a-database-with-shared-throughput"></a>Megosztott átviteli sebességgel rendelkező adatbázis létrehozása

Hozzon létre egy Cosmos-adatbázist közös átviteli sebességgel.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
throughput=400

az cosmosdb sql database create \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $throughput
```

## <a name="change-the-throughput-of-a-database"></a>Adatbázis átviteli sebességének módosítása

Egy Cosmos-adatbázis átviteli sebességének növelése 1000 RU/s használatával.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql database throughput show \
    -g $resourceGroupName -a $accountName -n $databaseName \
    --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql database throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -n $databaseName \
    --throughput $newRU
```

## <a name="create-a-container"></a>Tároló létrehozása

Hozzon létre egy Cosmos-tárolót az alapértelmezett index-házirenddel, a partíciós kulccsal és a 400-es RU/s-val.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput
```

## <a name="create-a-container-with-ttl"></a>Hozzon létre egy tárolót az ÉLETTARTAMmal

Hozzon létre egy Cosmos-tárolót, amelyen engedélyezve van az élettartam.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName = 'myResourceGroup'
accountName = 'mycosmosaccount'
databaseName = 'database1'
containerName = 'container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl = 86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Egyéni index-házirenddel rendelkező tároló létrehozása

Hozzon létre egy Cosmos-tárolót egyéni index-házirenddel, térbeli indexszel, összetett indexszel, a 400-es partíciós kulccsal és RU/s-vel.

```azurecli-interactive
# Create a SQL API container
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
partitionKey='/myPartitionKey'
throughput=400

# Generate a unique 10 character alphanumeric string to ensure unique resource names
uniqueId=$(env LC_CTYPE=C tr -dc 'a-z0-9' < /dev/urandom | fold -w 10 | head -n 1)

# Define the index policy for the container, include spatial and composite indexes
idxpolicy=$(cat << EOF
{
    "indexingMode": "consistent",
    "includedPaths": [
        {"path": "/*"}
    ],
    "excludedPaths": [
        { "path": "/headquarters/employees/?"}
    ],
    "spatialIndexes": [
        {"path": "/*", "types": ["Point"]}
    ],
    "compositeIndexes":[
        [
            { "path":"/name", "order":"ascending" },
            { "path":"/age", "order":"descending" }
        ]
    ]
}
EOF
)
# Persist index policy to json file
echo "$idxpolicy" > "idxpolicy-$uniqueId.json"


az cosmosdb sql container create \
    -a $accountName -g $resourceGroupName \
    -d $databaseName -n $containerName \
    -p $partitionKey --throughput $throughput \
    --idx @idxpolicy-$uniqueId.json

# Clean up temporary index policy file
rm -f "idxpolicy-$uniqueId.json"
```

## <a name="change-the-throughput-of-a-container"></a>Tároló átviteli sebességének módosítása

Növelje egy Cosmos-tároló átviteli sebességét 1000 RU/s használatával.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'
newRU=1000

# Get minimum throughput to make sure newRU is not lower than minRU
minRU=$(az cosmosdb sql container throughput show \
    -g $resourceGroupName -a $accountName -d $databaseName \
    -n $containerName --query resource.minimumThroughput -o tsv)

if [ $minRU -gt $newRU ]; then
    newRU=$minRU
fi

az cosmosdb sql container throughput update \
    -a $accountName \
    -g $resourceGroupName \
    -d $databaseName \
    -n $containerName \
    --throughput $newRU
```

## <a name="next-steps"></a>További lépések

Az Azure CLI-vel kapcsolatos további információkért lásd:

- [Az Azure parancssori felület telepítése](/cli/azure/install-azure-cli)
- [Azure parancssori felület referenciája](https://docs.microsoft.com/cli/azure/cosmosdb)
- [További Azure CLI-minták a Azure Cosmos DB](cli-samples.md)
