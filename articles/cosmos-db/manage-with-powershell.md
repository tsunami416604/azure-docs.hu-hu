---
title: Azure Cosmos DB létrehozása és kezelése a PowerShell használatával
description: Az Azure PowerShell használatával kezelheti Azure Cosmos DB fiókjait, adatbázisait, tárolóit és átviteli sebességét.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 5b041fecfaa5a84ed5a04a3a8c53de10b9efd65b
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71155371"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Azure Cosmos DB SQL API-erőforrások kezelése a PowerShell használatával

Az alábbi útmutató ismerteti, hogyan használható a PowerShell a Azure Cosmos DB erőforrások, például a fiók, az adatbázis, a tároló és az átviteli sebesség kezelésére és automatizálására. A Azure Cosmos DB felügyeletét a AzResource-parancsmag közvetlenül a Azure Cosmos DB erőforrás-szolgáltatón keresztül kezeli. Az Azure Cosmos DB erőforrás-szolgáltató PowerShell használatával felügyelhető összes tulajdonság megtekintéséhez lásd: [Azure Cosmos db erőforrás-szolgáltató sémája](/azure/templates/microsoft.documentdb/allversions)

Azure Cosmos DB platformfüggetlen felügyeletéhez használhatja az [Azure CLI](manage-with-cli.md)-t, a [REST API][rp-rest-api]vagy a [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Első lépések

Kövesse a [Azure PowerShell telepítésének és konfigurálásának][powershell-install-configure] lépéseit a PowerShell Azure-fiókjába való telepítéséhez és bejelentkezéséhez.

* Ha szeretné, hajtsa végre a következő parancsokat a felhasználó jóváhagyásának kérése nélkül, fűzze hozzá a `-Force` jelző a következő paranccsal.
* Az alábbi parancsokat a rendszer szinkron.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-fiókok

Az alábbi szakaszban bemutatjuk, hogyan kezelheti az Azure Cosmos-fiókot, beleértve a következőket:

* [Azure Cosmos-fiók létrehozása](#create-account)
* [Azure Cosmos-fiók frissítése](#update-account)
* [Az előfizetésben lévő összes Azure Cosmos-fiók listázása](#list-accounts)
* [Azure Cosmos-fiók beszerzése](#get-account)
* [Azure Cosmos-fiók törlése](#delete-account)
* [Azure Cosmos-fiókhoz tartozó címkék frissítése](#update-tags)
* [Azure Cosmos-fiók kulcsainak listázása](#list-keys)
* [Kulcsok újragenerálása Azure Cosmos-fiókhoz](#regenerate-keys)
* [Azure Cosmos-fiókhoz tartozó kapcsolatok karakterláncok listázása](#list-connection-strings)
* [Azure Cosmos-fiók feladatátvételi prioritásának módosítása](#modify-failover-priority)
* [Manuális feladatátvétel indítása Azure Cosmos-fiókhoz](#trigger-manual-failover)

### <a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ez a parancs egy Azure Cosmos-adatbázis-fiókot hoz létre [több régióval][distribute-data-globally], a határértékek közti [konzisztencia-házirenddel](consistency-levels.md).

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

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

* `$accountName`Az Azure Cosmos-fiók neve. Kisbetűnek kell lennie, alfanumerikus karaktereket és a "-" karaktert, valamint 3 – 31 karakterből állhat.
* `$location`Az Azure Cosmos-fiók erőforrásának helye.
* `$locations`Az adatbázis-fiók replikáinak régiói. A 0 értékű feladatátvételi prioritási értékkel rendelkező adatbázis-fiókok esetében egy írási régiónak kell lennie.
* `$consistencyPolicy`Az Azure Cosmos-fiók alapértelmezett konzisztencia-szintje. További információkért lásd: [Azure Cosmos DB-ben Konzisztenciaszintek](consistency-levels.md).
* `$CosmosDBProperties`A Cosmos DB Azure Resource Manager szolgáltatónak átadott tulajdonságértékek a fiók kiépítéséhez.

Az Azure Cosmos-fiókok IP-tűzfallal és Virtual Network szolgáltatásbeli végpontokkal is konfigurálhatók. További információ a Azure Cosmos DB IP-tűzfalának konfigurálásáról: az [IP-tűzfal konfigurálása](how-to-configure-firewall.md).  További információ a Azure Cosmos DB szolgáltatási végpontjának engedélyezéséről: [a virtuális hálózatokról való hozzáférés konfigurálása](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a>Az előfizetésben lévő összes Azure Cosmos-fiók listázása

Ezzel a paranccsal listázhatja az előfizetésben lévő összes Azure Cosmos-fiókot.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a>Azure Cosmos-fiók tulajdonságainak beolvasása

Ez a parancs lehetővé teszi egy meglévő Azure Cosmos-fiók tulajdonságainak beszerzését.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a>Azure Cosmos-fiók frissítése

Ezzel a paranccsal frissítheti az Azure Cosmos-adatbázis fiókjának tulajdonságait. A frissíthető tulajdonságok közé tartoznak a következők:

* Régiók hozzáadása vagy eltávolítása
* Alapértelmezett konzisztencia-házirend módosítása
* IP-címtartomány módosítása
* Virtual Network konfigurációk módosítása
* Több főkiszolgáló engedélyezése

> [!NOTE]
> Nem lehet egyszerre hozzáadni vagy eltávolítani a `locations` régiókat, és módosítani az Azure Cosmos-fiók egyéb tulajdonságait. A régiók módosításait külön műveletként kell végrehajtani, mint a fiók erőforrásának egyéb módosításait.
> [!NOTE]
> Ezzel a paranccsal hozzáadhat és eltávolíthat régiókat, de nem teszi lehetővé a feladatátvételi prioritások módosítását, illetve manuális feladatátvételt is indíthat. Lásd: a [feladatátvételi prioritás módosítása](#modify-failover-priority) és a [manuális feladatátvétel elindítása](#trigger-manual-failover).

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a>Azure Cosmos-fiók törlése

Ez a parancs lehetővé teszi egy meglévő Azure Cosmos-fiók törlését.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a>Azure Cosmos-fiók címkék frissítése

Az alábbi példa azt ismerteti, hogyan állíthatja be az [Azure-erőforrás címkéit][azure-resource-tags] egy Azure Cosmos-fiókhoz.

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

### <a id="regenerate-keys"></a>Fiók kulcsainak újragenerálása

Az Azure Cosmos-fiókhoz való hozzáférési kulcsokat rendszeresen újra kell generálni, hogy a kapcsolatok biztonságosabbak maradjanak. Az elsődleges és másodlagos hozzáférési kulcsok hozzá vannak rendelve a fiókhoz. Ez lehetővé teszi az ügyfelek számára a hozzáférés fenntartását, amíg a másik újragenerálta. Egy Azure Cosmos-fiókhoz négy típusú kulcs létezik (elsődleges, másodlagos, PrimaryReadonly és SecondaryReadonly)

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

### <a id="modify-failover-priority"></a>Feladatátvétel prioritásának módosítása

Az automatikus Feladatátvételsel konfigurált fiókok esetében megváltoztathatja azt a sorrendet, amelyben a Cosmos a másodlagos replikákat az elsődlegesnek fogja előléptetni, és az elsődleges elérhetetlenné válik.

Az alábbi példában feltételezzük, hogy az aktuális feladatátvételi prioritás `West US 2 = 0` `East US 2 = 1` `South Central US = 2`:,,.

> [!CAUTION]
> A `locationName` szolgáltatásra való `failoverPriority=0` váltáskor a rendszer manuális feladatátvételt indít el egy Azure Cosmos-fiókhoz. A többi prioritási változás nem indít el feladatátvételt.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="South Central US"; "failoverPriority"=1 },
    @{ "locationName"="East US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

### <a id="trigger-manual-failover"></a>Manuális feladatátvétel indítása

A manuális Feladatátvételsel konfigurált fiókok esetében a módosításával `failoverPriority=0`feladatátvételt hajthat végre, és előléptetheti a másodlagos replikákat az elsődlegesre. Ezzel a művelettel vészhelyzeti helyreállítási gyakorlatot indíthat a vész-helyreállítási tervezés teszteléséhez.

Az alábbi példában feltételezzük, hogy a fiók aktuális feladatátvételi prioritásával `West US 2 = 0` `East US 2 = 1` rendelkezik, és megfordítja a régiókat.

> [!CAUTION]
> A `locationName` szolgáltatásra való `failoverPriority=0` váltáskor a rendszer manuális feladatátvételt indít el egy Azure Cosmos-fiókhoz. A többi prioritási változás nem indít el feladatátvételt.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0, "East US 2" = 1, "South Central US" = 2

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="South Central US"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="West US 2"; "failoverPriority"=2 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Azure Cosmos-adatbázis

Az alábbi szakaszban bemutatjuk, hogyan kezelheti az Azure Cosmos-adatbázist, beleértve a következőket:

* [Azure Cosmos-adatbázis létrehozása](#create-db)
* [Azure Cosmos-adatbázis létrehozása megosztott átviteli sebességgel](#create-db-ru)
* [Azure Cosmos-adatbázis átviteli sebességének beolvasása](#get-db-ru)
* [Egy fiókban található összes Azure Cosmos-adatbázis listázása](#list-db)
* [Egyetlen Azure Cosmos-adatbázis beszerzése](#get-db)
* [Azure Cosmos-adatbázis törlése](#delete-db)

### <a id="create-db"></a>Azure Cosmos-adatbázis létrehozása

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

### <a id="create-db-ru"></a>Azure Cosmos-adatbázis létrehozása megosztott átviteli sebességgel

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

### <a id="get-db-ru"></a>Azure Cosmos-adatbázis átviteli sebességének beolvasása

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

### <a id="list-db"></a>Összes Azure Cosmos-adatbázis beolvasása egy fiókban

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Egyetlen Azure Cosmos-adatbázis beszerzése

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

### <a id="delete-db"></a>Azure Cosmos-adatbázis törlése

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Azure Cosmos-tároló

Az alábbi szakaszban bemutatjuk, hogyan kezelheti az Azure Cosmos-tárolót, beleértve a következőket:

* [Azure Cosmos-tároló létrehozása](#create-container)
* [Azure Cosmos-tároló létrehozása nagyméretű partíciós kulccsal](#create-container-big-pk)
* [Azure Cosmos-tároló átviteli sebességének beolvasása](#get-container-ru)
* [Azure Cosmos-tároló létrehozása megosztott átviteli sebességgel](#create-container-ru)
* [Azure Cosmos-tároló létrehozása egyéni indexeléssel](#create-container-custom-index)
* [Kikapcsolt indexeléssel rendelkező Azure Cosmos-tároló létrehozása](#create-container-no-index)
* [Azure Cosmos-tároló létrehozása egyedi kulccsal és TTL-vel](#create-container-unique-key-ttl)
* [Ütközéses feloldást biztosító Azure Cosmos-tároló létrehozása](#create-container-lww)
* [Egy adatbázisban lévő összes Azure Cosmos-tároló listázása](#list-containers)
* [Egyetlen Azure Cosmos-tároló beszerzése egy adatbázisban](#get-container)
* [Azure Cosmos-tároló törlése](#delete-container)

### <a id="create-container"></a>Azure Cosmos-tároló létrehozása

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

### <a id="create-container-big-pk"></a>Azure Cosmos-tároló létrehozása nagyméretű partíciós kulcs méretével

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

### <a id="get-container-ru"></a>Azure Cosmos-tároló átviteli sebességének beolvasása

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

### <a id="create-container-ru"></a>Azure Cosmos-tároló létrehozása megosztott átviteli sebességgel

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

### <a id="create-container-custom-index"></a>Egyéni index-házirenddel rendelkező Azure Cosmos-tároló létrehozása

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

### <a id="create-container-no-index"></a>Kikapcsolt indexeléssel rendelkező Azure Cosmos-tároló létrehozása

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

### <a id="create-container-unique-key-ttl"></a>Azure Cosmos-tároló létrehozása egyedi kulcs-házirenddel és TTL-vel

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

### <a id="create-container-lww"></a>Ütközéses feloldást biztosító Azure Cosmos-tároló létrehozása

Ha egy tárolt eljárás használatára vonatkozó ütközés-feloldási szabályzatot szeretne `"mode"="custom"` létrehozni, állítsa be és állítsa be a megoldás elérési útját `"conflictResolutionPath"="myResolverStoredProcedure"`a tárolt eljárás neveként. Ha az összes ütközést a ConflictsFeed szeretné írni, és külön `"mode"="custom"` kezeli a leírót, állítsa be és`"conflictResolutionPath"=""`

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

### <a id="list-containers"></a>Egy adatbázisban lévő összes Azure Cosmos-tároló listázása

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

### <a id="get-container"></a>Egyetlen Azure Cosmos-tároló beszerzése egy adatbázisban

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

### <a id="delete-container"></a>Azure Cosmos-tároló törlése

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

* [Minden PowerShell-minta](powershell-samples.md)
* [Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Azure Cosmos-tároló létrehozása](how-to-create-container.md)
* [A Azure Cosmos DB élettartamának konfigurálása](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
