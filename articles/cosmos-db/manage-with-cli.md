---
title: Az Azure Cosmos DB-erőforrások kezelése az Azure CLI használatával
description: Az Azure CLI használatával kezelheti az Azure Cosmos DB-fiókját, adatbázisát és tárolóit.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3f86468bcafe3d7ce78827aba761bb4e1bf920fa
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273630"
---
# <a name="manage-azure-cosmos-resources-using-azure-cli"></a>Az Azure Cosmos erőforrásainak kezelése az Azure CLI használatával

Az alábbi útmutató az Azure Cosmos DB-fiókok, -adatbázisok és -tárolók felügyeletének Azure CLI-vel történő automatizálásához használható általános parancsokat ismerteti. Az Azure Cosmos DB CLI összes parancsának referencialapjai az [Azure CLI-referenciadokumentumban](https://docs.microsoft.com/cli/azure/cosmosdb)érhetők el. További példákat is találhat az [Azure Cosmos DB Azure CLI-mintáiban,](cli-samples.md)többek között cosmos DB-fiókok, adatbázisok és a MongoDB, a Gremlin, a Cassandra és a Table API tárolóinak létrehozásához és kezeléséhez.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítése és használata mellett dönt, a témakör az Azure CLI 2.0-s vagy annál újabb verziójának futtatását követeli meg. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB-fiók létrehozása

Hozzon létre egy Azure Cosmos DB-fiókot AZ SQL API-val, a munkamenet-konzisztenciát az USA nyugati régiójában 2 és AZ USA keleti régiójában:

> [!IMPORTANT]
> Az Azure Cosmos-fiók nevének kisbetűsnek és 31 karakternél rövidebbnek kell lennie.

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
> Egy Azure Cosmos-fiók `locations` egyéb tulajdonságainak egyidejű hozzáadása és eltávolítása nem adható hozzá és távolíthat el, és módosíthatja az egyéb tulajdonságokat. A módosító régiókat külön műveletként kell végrehajtani, mint a fiókerőforrás bármely más módosítását.
> [!NOTE]
> Ez a parancs lehetővé teszi a régiók hozzáadását és eltávolítását, de nem teszi lehetővé a feladatátvételi prioritások módosítását vagy manuális feladatátvétel elindítását. Lásd: [Feladatátvételi prioritás beállítása](#set-failover-priority) és [Manuális feladatátvétel aktiválása.](#trigger-manual-failover)

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount' # must be lower case and <31 characters

# Create an account with 2 regions
az cosmosdb create --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False

# Add a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False \
    --locations regionName="South Central US" failoverPriority=2 isZoneRedundant=False

# Remove a region
az cosmosdb update --name $accountName --resource-group $resourceGroupName \
    --locations regionName="West US 2" failoverPriority=0 isZoneRedundant=False \
    --locations regionName="East US 2" failoverPriority=1 isZoneRedundant=False
```

## <a name="enable-multiple-write-regions"></a>Több írási terület engedélyezése

Többfőkiszolgáló engedélyezése Cosmos-fiókhoz

```azurecli-interactive
# Update an Azure Cosmos account from single to multi-master
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-multiple-write-locations true
```

## <a name="set-failover-priority"></a>Feladatátvételi prioritás beállítása

Az automatikus feladatátvételhez konfigurált Azure Cosmos-fiók feladatátvételi prioritásának beállítása

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Make South Central US the next region to fail over to instead of East US 2
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'West US 2'=0 'South Central US'=1 'East US 2'=2
```

## <a name="enable-automatic-failover"></a>Automatikus feladatátvétel engedélyezése

```azurecli-interactive
# Enable automatic failover on an existing account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

az cosmosdb update --ids $accountId --enable-automatic-failover true
```

## <a name="trigger-manual-failover"></a>Kézi feladatátvétel aktiválása

> [!CAUTION]
> A régió módosítása prioritással = 0 elindítja az Azure Cosmos-fiók manuális feladatátvételt. Bármely más prioritási módosítás nem indít el feladatátvételt.

```azurecli-interactive
# Assume region order is initially 'West US 2'=0 'East US 2'=1 'South Central US'=2 for account
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'

# Get the account resource id for an existing account
accountId=$(az cosmosdb show -g $resourceGroupName -n $accountName --query id -o tsv)

# Trigger a manual failover to promote East US 2 as new write region
az cosmosdb failover-priority-change --ids $accountId \
    --failover-policies 'East US 2'=0 'South Central US'=1 'West US 2'=2
```

## <a name="list-all-account-keys"></a><a id="list-account-keys"></a>Az összes fiókkulcs listázása

Az összes kulcsot a Cosmos fiók.

```azurecli-interactive
# List all account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
   -n $accountName \
   -g $resourceGroupName
```

## <a name="list-read-only-account-keys"></a>Írásvédett fiókkulcsok listázása

Csak olvasható kulcsok beolvasása cosmos-fiókhoz.

```azurecli-interactive
# List read-only account keys
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type read-only-keys
```

## <a name="list-connection-strings"></a>Kapcsolati karakterláncok listázása

A Cosmos-fiók kapcsolati karakterláncai beszerezni.

```azurecli-interactive
# List connection strings
resourceGroupName='MyResourceGroup'
accountName='mycosmosaccount'

az cosmosdb keys list \
    -n $accountName \
    -g $resourceGroupName \
    --type connection-strings
```

## <a name="regenerate-account-key"></a>Fiókkulcs újragenerálása

Új kulcs újragenerálása egy Cosmos-fiókhoz.

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

## <a name="create-a-database-with-shared-throughput"></a>Megosztott átviteli hatósadatbázis létrehozása

Hozzon létre egy Cosmos-adatbázist megosztott átviteli hancróval.

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

## <a name="change-the-throughput-of-a-database"></a>Adatbázis átviteli idejének módosítása

Növelje a Cosmos-adatbázis átviteli fazeka1000 RU/s-t.

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

Hozzon létre egy Cosmos-tárolóalapértelmezett indexházirendet, partíciókulcsot és 400-as RU/s-t.

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

## <a name="create-a-container-with-ttl"></a>Tároló létrehozása TTL-lel

Hozzon létre egy Cosmos-tárolót ttl engedélyezve.

```azurecli-interactive
# Create an Azure Cosmos container with TTL of one day
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
databaseName='database1'
containerName='container1'

az cosmosdb sql container update \
    -g $resourceGroupName \
    -a $accountName \
    -d $databaseName \
    -n $containerName \
    --ttl=86400
```

## <a name="create-a-container-with-a-custom-index-policy"></a>Tároló létrehozása egyéni indexházirenddel

Hozzon létre egy Cosmos-tárolót egyéni indexházirenddel, egy térbeli indexszel, összetett indexszel, egy partíciókulcssal és 400-as RU/s-szal.

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

## <a name="change-the-throughput-of-a-container"></a>Tároló átviteli idejének módosítása

Növelje a Cosmos-tároló átviteli fazeka1000 RU/s.Increase the throughput of a Cosmos container by 1000 RU/s.

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

Az Azure CLI-ről további információt a következő témakörben talál:

- [Telepítse az Azure CLI-t](/cli/azure/install-azure-cli)
- [Azure parancssori felület referenciája](https://docs.microsoft.com/cli/azure/cosmosdb)
- [További Azure CLI-minták az Azure Cosmos DB-hez](cli-samples.md)
