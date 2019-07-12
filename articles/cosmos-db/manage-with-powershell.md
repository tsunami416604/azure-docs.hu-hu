---
title: Létrehozása és kezelése az Azure Cosmos DB PowerShell-lel
description: Az Azure Powershell használata kezelheti az Azure Cosmos DB fiókok, adatbázisok, tárolók és átviteli sebességet.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/09/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: b61c7bbc06d8d265e5dd5dddd31aceadce1f623b
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797055"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>PowerShell-lel az Azure Cosmos DB SQL API-erőforrások kezelése

Ez az útmutató ismerteti parancsfájlt a PowerShell használatával, és az Azure Cosmos DB-erőforrásokat, beleértve a fiók, adatbázis, tároló és átviteli sebesség a kezelés automatizálása. Az Azure Cosmos DB kezelése az AzResource parancsmagot közvetlenül az Azure Cosmos DB erőforrás-szolgáltató kezeli. A tulajdonságokat, amelyeket az Azure Cosmos DB erőforrás-szolgáltató a PowerShell-lel kezelhető összes megtekintése: [Azure Cosmos DB erőforrás-szolgáltató sémáján](/azure/templates/microsoft.documentdb/allversions)

A platformok közötti kezelése az Azure Cosmos DB, használhatja [Azure CLI-vel](manage-with-cli.md), a [REST API-val][rp-rest-api], vagy a [az Azure portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Első lépések

Kövesse a [telepítése és konfigurálása az Azure PowerShell-lel][powershell-install-configure] telepítéséhez, és jelentkezzen be az Azure Powershell-fiókjával.

* Ha szeretné, hajtsa végre a következő parancsokat a felhasználó jóváhagyásának kérése nélkül, fűzze hozzá a `-Force` jelző a következő paranccsal.
* Az alábbi parancsokat a rendszer szinkron.

## <a name="azure-cosmos-accounts"></a>Az Azure Cosmos-fiókok

A következő szakaszok bemutatják, hogyan lehet az Azure Cosmos-fiók kezeléséhez, többek között:

* [Egy Azure Cosmos-fiók létrehozása](#create-account)
* [Egy Azure Cosmos-fiók frissítése](#update-account)
* [Egy adott előfizetés összes Azure Cosmos-fiókok listázása](#list-accounts)
* [Egy Azure Cosmos-fiók létrehozása](#get-account)
* [Egy Azure Cosmos-fiók törlése](#delete-account)
* [Frissítheti a címkéket egy Azure Cosmos-fiók](#update-tags)
* [Egy Azure Cosmos-fiók kulcsainak listázása](#list-keys)
* [Egy Azure Cosmos-fiók kulcsainak újragenerálása](#regenerate-keys)
* [Lista kapcsolati karakterláncok az Azure Cosmos-fiók](#list-connection-strings)
* [Feladatátvételi prioritását egy Azure Cosmos-fiók módosítása](#modify-failover-priority)

### <a id="create-account"></a> Egy Azure Cosmos-fiók létrehozása

Ez a parancs létrehoz egy Azure Cosmos DB-adatbázisfiók a [több régiót][distribute-data-globally], kötött elavulás [konzisztencia-szabályzat](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lower case.

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` Az Azure Cosmos-fiók neve. Csak kisbetűket, alfanumerikus fogad, és a "-" karakter, és 3. és 31 karakter közötti.
* `$location` Az Azure Cosmos-fiók erőforrás helye.
* `$locations` A replika régiók, az adatbázis fiókjához tartozó. 0, a feladatátvétel prioritási értékkel rendelkező adatbázis-fiókonként egy írási régiót kell lennie.
* `$consistencyPolicy` Az Azure Cosmos-fiók alapértelmezett konzisztencia szintjét. További információkért lásd: [Azure Cosmos DB-ben Konzisztenciaszintek](consistency-levels.md).
* `$CosmosDBProperties` A Cosmos DB az Azure erőforrás-kezelő szolgáltató üzembe helyezi a fiók a tulajdonságértékek kerülnek.

Fiókok konfigurálhatók az IP-tűzfalon, valamint a virtuális hálózat az Azure Cosmos-végpontok szolgáltatás. Az IP-tűzfal konfigurálása az Azure Cosmos DB információkért lásd: [IP-tűzfal konfigurálása](how-to-configure-firewall.md).  További információ a Szolgáltatásvégpontok engedélyezése az Azure Cosmos DB: [hozzáférés a virtuális hálózatok konfigurálása](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a> Egy adott előfizetés összes Azure Cosmos-fiókok listázása

Ez a parancs lehetővé teszi, hogy egy adott előfizetés összes az Azure Cosmos-fiók megjelenítése.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> Egy Azure Cosmos-fiók tulajdonságainak beolvasása

Ez a parancs lehetővé teszi, hogy egy meglévő Azure-Cosmos-fiók tulajdonságainak beolvasása.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Egy Azure Cosmos-fiók frissítése

Ez a parancs lehetővé teszi az Azure Cosmos DB-adatbázis fiók tulajdonságainak frissítése. Frissíthető tulajdonságok a következők:

* Hozzáadásával vagy eltávolításával régiók
* Alapértelmezett konzisztencia-szabályzat módosítása
* Feladatátvételi szabályzat módosítása
* IP-címtartományok szűrője módosítása
* Virtuális hálózati konfiguráció módosítása
* Több főkiszolgálós engedélyezése

> [!NOTE]
> Ez a parancs lehetővé teszi, hogy hozzá és távolíthat el a régióban, de nem engedélyezi, hogy módosítsa a feladatátvételi prioritások. Feladatátvételi prioritás módosítása esetén lásd: [módosítása az Azure Cosmos-fiók feladatátvételi prioritását](#modify-failover-priority).

```azurepowershell-interactive
# Update an Azure Cosmos Account and set Consistency level to Session

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$consistencyPolicy = @{ "defaultConsistencyLevel"="Session" }

$account.Properties.consistencyPolicy = $consistencyPolicy
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Egy Azure Cosmos-fiók törlése

Ez a parancs lehetővé teszi egy meglévő Azure-Cosmos-fiók törlése.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Frissítheti a címkéket egy Azure Cosmos-fiók

Az alábbi példa azt ismerteti, hogyan állíthatja be [Azure-erőforráscímkék][azure-resource-tags] egy Azure Cosmos-fiók.

> [!NOTE]
> Ez a parancs kombinálva, a létrehozás vagy frissítés parancsok hozzáfűzésével a `-Tags` jelzőt mellékel a megfelelő paraméter.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Fiók kulcsainak listázása

Az Azure Cosmos DB-fiók létrehozásakor a szolgáltatás két fő kulcsot, amely az Azure Cosmos DB-fiók elérésekor hitelesítéshez használt állít elő. Azáltal, hogy a két hozzáférési kulcsot, az Azure Cosmos DB lehetővé teszi a kulcsok az Azure Cosmos DB-fiók megszakítás nélkül. Írásvédett kulcsok hitelesítéséhez a csak olvasható műveletekhez is elérhetők. Nincsenek két írható és olvasható kulcsok (elsődleges és másodlagos) és a két csak olvasható kulcsok (elsődleges és másodlagos).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Kapcsolati karakterláncok listája

MongoDB-fiókok esetében a kapcsolati karakterláncot a MongoDB-alkalmazás csatlakozni az adatbázis-fiókot az alábbi parancs használatával lekérhetők.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Fiókkulcsok ismételt létrehozása

Egy Azure Cosmos-fiók hozzáférési kulcsokat rendszeres időközönként újragenerálása kell a kapcsolatok több biztonsága érdekében. A fiók elsődleges és másodlagos hívóbetűket vannak rendelve. Ez lehetővé teszi a hozzáférés fenntartása, míg a másik újra létrehozzák azok az ügyfelek. Négyféle (elsődleges, másodlagos, PrimaryReadonly és SecondaryReadonly) egy Azure Cosmos-fiók kulcsok

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Feladatátvételi prioritás módosítása

A többrégiós adatbázisfiókhoz módosíthatja a sorrendet, amelyben egy Cosmos-fiók lépteti másodlagos olvasási-replikák egy regionális feladatátvétel replikán kell az elsődleges írási. Ha a régió `failoverPriority=0` van módosítani, ez a parancs is használható kezdeményezni a vészhelyreállítási tervek tesztelésére egy vészhelyreállítási próbát.

Az alábbi példában azt feltételezik, hogy a fiók rendelkezik-e egy westus aktuális feladatátvételi prioritását = 0 és az USA keleti régiója = 1, és a régiók tükrözés.

> [!CAUTION]
> Módosítása `locationName` a `failoverPriority=0` manuális feladatátvételt az Azure Cosmos-fiók vált. Prioritás más módosítást nem vált a feladatátvételt.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverPolicies = @(
    @{ "locationName"="East US"; "failoverPriority"=0 },
    @{ "locationName"="West US"; "failoverPriority"=1 }
)

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Az Azure Cosmos-adatbázis

A következő szakaszok bemutatják, hogyan kezelheti az Azure Cosmos-adatbázis, többek között:

* [Hozzon létre egy Azure Cosmos database.](#create-db)
* [Megosztott adattovábbítási kapacitással rendelkező Azure Cosmos-adatbázis létrehozása](#create-db-ru)
* [Egy Azure Cosmos Database az átviteli sebesség lekérdezése](#get-db-ru)
* [-Fiókban lévő összes az Azure Cosmos-adatbázis listázása](#list-db)
* [Egy Azure Cosmos-adatbázis beolvasása](#get-db)
* [Egy Azure Cosmos-adatbázis törlése](#delete-db)

### <a id="create-db"></a>Hozzon létre egy Azure Cosmos database.

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Megosztott adattovábbítási kapacitással rendelkező Azure Cosmos-adatbázis létrehozása

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Egy Azure Cosmos Database az átviteli sebesség lekérdezése

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Egy fiók minden Azure Cosmos-adatbázis beolvasása

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Egy Azure Cosmos-adatbázis beolvasása

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Egy Azure Cosmos-adatbázis törlése

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos Container

A következő szakaszok bemutatják, hogyan kezelheti az Azure Cosmos-tárolót, többek között:

* [Egy Azure Cosmos-tároló létrehozása](#create-container)
* [Hozzon létre egy Azure Cosmos-tárolóhoz nagy partíciókulccsal rendelkező](#create-container-big-pk)
* [Az Azure Cosmos-tároló átviteli sebesség lekérdezése](#get-container-ru)
* [Hozzon létre egy Azure Cosmos-tárolóhoz megosztott átviteli sebesség](#create-container-ru)
* [Egyéni indexelési hozzon létre egy Azure Cosmos-tárolóhoz](#create-container-custom-index)
* [Hozzon létre egy Azure Cosmos-tárolóhoz indexelése ki van kapcsolva](#create-container-no-index)
* [Hozzon létre egy Azure Cosmos-tárolóhoz egyedi kulcsot és a TTL](#create-container-unique-key-ttl)
* [Hozzon létre egy Azure Cosmos-tárolóhoz ütközésének feloldása](#create-container-lww)
* [Az adatbázis összes az Azure Cosmos-tárolót](#list-containers)
* [Egy adatbázis egy egyetlen Azure Cosmos-tároló beolvasása](#get-container)
* [Egy Azure Cosmos-tároló törlése](#delete-container)

### <a id="create-container"></a>Egy Azure Cosmos-tároló létrehozása

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Hozzon létre egy Azure Cosmos-tárolóhoz egy nagy partíció kulcsának mérete

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Az Azure Cosmos-tároló átviteli sebesség lekérdezése

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Hozzon létre egy Azure Cosmos-tárolóhoz megosztott átviteli sebesség

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Hozzon létre egy Azure Cosmos-tárolóhoz egyéni index házirend

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Hozzon létre egy Azure Cosmos-tárolóhoz indexelése ki van kapcsolva

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Egy Azure Cosmos-tároló létrehozása az egyedi és a TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Hozzon létre egy Azure Cosmos-tárolóhoz ütközésének feloldása

Hozzon létre egy tárolt eljárás használatával ütközésfeloldási házirend, állítsa `"mode"="custom"` és a megoldási elérési útjának beállítása a tárolt eljárás neveként `"conflictResolutionPath"="myResolverStoredProcedure"`. Állítsa a ConflictsFeed írni az összes ütközést, és külön-külön kezelni, `"mode"="custom"` és `"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Az adatbázis összes az Azure Cosmos-tárolót

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Egy adatbázis egy egyetlen Azure Cosmos-tároló beolvasása

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Egy Azure Cosmos-tároló törlése

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>További lépések

* [Az összes PowerShell-minták](powershell-samples.md)
* [Az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Egy Azure Cosmos-tároló létrehozása](how-to-create-container.md)
* [Time-to-live konfigurálása az Azure Cosmos DB-ben](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
