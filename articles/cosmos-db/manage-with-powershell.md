---
title: Az Azure Cosmos DB létrehozása és kezelése a PowerShell használatával
description: Azure Powershell kezelheti az Azure Cosmos-fiókokat, adatbázisokat, tárolókat és átviteli teljesítményt.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: c8e833a4ba18520d8e354398cfd0d00525594d15
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365761"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Az Azure Cosmos DB SQL API-erőforrások kezelése a PowerShell használatával

Az alábbi útmutatóban megismerheti, hogyan szkriptelheti és automatizálhatja az Azure Cosmos DB-erőforrások (például a fiókok, az adatbázisok, a tárolók és az átviteli sebesség) felügyeletét a PowerShell használatával.

> [!NOTE]
> Ebben a cikkben a használat `Get-AzResource` és `Set-AzResource` a Powershell-parancsmagok az Azure-erőforrás-műveletek, valamint az [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) felügyeleti parancsmagok. `Az.CosmosDB`a parancsmagok továbbra is előzetes verzióban jelennek meg, és változhatnak, mielőtt általánosan elérhetővé váltanak. Tekintse meg az [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) API referencialapon a parancsok frissítéseit.

A PowerShell-parancsmagok használatával `Get-Resource` / `Set-AzResource` kezelhető összes tulajdonság megtekintéséhez tekintse meg az [Azure Cosmos DB erőforrás-szolgáltató sémáját](/azure/templates/microsoft.documentdb/allversions)

Az Azure Cosmos DB platformfüggetlen felügyeletéhez `Az` használhatja a `Az.CosmosDB` [platformfüggetlen Powershell,](https://docs.microsoft.com/powershell/scripting/install/installing-powershell)valamint az [Azure CLI,](manage-with-cli.md)a REST [API][rp-rest-api]vagy az Azure [Portal](create-sql-api-dotnet.md#create-account)használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Első lépések

Kövesse az [Azure PowerShell telepítése és konfigurálása][powershell-install-configure] az Azure-fiók powershellben való telepítéséhez és bejelentkezéséhez című témakör utasításait.

* `Set-AzureResource`az alábbiakban használjuk. Kérni fogja a felhasználó megerősítését.  Ha felhasználói megerősítés nélkül szeretné végrehajtani a `-Force` végrehajtást, fűzz hozzá a jelzőt a parancshoz.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-fiókok

Az alábbi szakaszok bemutatják, hogyan kezelheti az Azure Cosmos-fiókot, többek között:

* [Azure Cosmos-fiók létrehozása](#create-account)
* [Azure Cosmos-fiók frissítése](#update-account)
* [Az összes Azure Cosmos-fiók listázása előfizetésben](#list-accounts)
* [Azure Cosmos-fiók beszereznie](#get-account)
* [Azure Cosmos-fiók törlése](#delete-account)
* [Címkék frissítése egy Azure Cosmos-fiókhoz](#update-tags)
* [Azure Cosmos-fiók kulcsainak listázása](#list-keys)
* [Kulcsok újragenerálása egy Azure Cosmos-fiókhoz](#regenerate-keys)
* [Kapcsolati karakterláncok listázása egy Azure Cosmos-fiókhoz](#list-connection-strings)
* [Feladatátvételi prioritás módosítása egy Azure Cosmos-fiókhoz](#modify-failover-priority)
* [Manuális feladatátvétel indítása egy Azure Cosmos-fiókhoz](#trigger-manual-failover)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a>Azure Cosmos-fiók létrehozása

Ez a parancs létrehoz egy Azure Cosmos DB adatbázis-fiókot [több régióval,][distribute-data-globally] [automatikus feladatátvételi](how-to-manage-database-account.md#automatic-failover) és a határolt elavultság [konzisztencia-házirenddel.](consistency-levels.md)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000

New-AzCosmosDBAccount -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName`Az Azure-erőforráscsoport, amelybe a Cosmos-fiók üzembe helyezéséhez. Már léteznie kell.
* `$locations`Az adatbázis-fiók régiói, kezdve az írási régióval, és feladatátvételi prioritás sal rendezve.
* `$accountName`Az Azure Cosmos-fiók neve. Egyedinek, kisbetűsnek kell lennie, csak alfanumerikus és '-' karaktereket tartalmazhat, és 3 és 31 karakter közötti hosszúságúnak kell lennie.
* `$apiKind`A létrehozandó Cosmos-fiók típusa. További információ: [API-k a Cosmos DB-ben.](introduction.md#develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis)
* `$consistencyPolicy`, `$maxStalenessInterval`és `$maxStalenessPrefix` az Azure Cosmos-fiók alapértelmezett konzisztenciaszintje és beállításai. További információ: [Konzisztenciaszintek az Azure Cosmos DB-ben.](consistency-levels.md)

Az Azure Cosmos-fiókok konfigurálhatók IP-tűzfallal, virtuális hálózati szolgáltatás-végpontokkal és privát végpontokkal. Az IP-tűzfal Azure Cosmos DB-hez való konfigurálásáról az [IP-tűzfal konfigurálása című](how-to-configure-firewall.md)témakörben talál további információt. Az Azure Cosmos DB szolgáltatásvégpontjainak engedélyezéséről a [Hozzáférés konfigurálása a virtuális hálózatokról](how-to-configure-vnet-service-endpoint.md)című témakörben talál. Az Azure Cosmos DB privát végpontjainak engedélyezéséről a [Hozzáférés konfigurálása magánvégpontokból című témakörben](how-to-configure-private-endpoints.md)talál.

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a>Az összes Azure Cosmos-fiók listázása erőforráscsoportban

Ez a parancs egy erőforráscsoportban lévő összes Azure Cosmos-fiókot listázza.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a>Egy Azure Cosmos-fiók tulajdonságainak beszereznie

Ez a parancs lehetővé teszi egy meglévő Azure Cosmos-fiók tulajdonságainak leése.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a>Azure Cosmos-fiók frissítése

Ez a parancs lehetővé teszi az Azure Cosmos DB adatbázis-fiók tulajdonságainak frissítését. A frissíthető tulajdonságok a következők:

* Régiók hozzáadása és eltávolítása
* Alapértelmezett konzisztenciaházirend módosítása
* IP-tartományszűrő módosítása
* Virtuális hálózati konfigurációk módosítása
* Többfőkiszolgáló engedélyezése

> [!NOTE]
> Egy Azure Cosmos-fiók `locations` egyéb tulajdonságainak egyidejű hozzáadása és eltávolítása nem adható hozzá és távolíthat el, és módosíthatja az egyéb tulajdonságokat. A módosító régiókat a fiók bármely más módosításától eltérő műveletként kell végrehajtani.
> [!NOTE]
> Ez a parancs lehetővé teszi a régiók hozzáadását és eltávolítását, de nem teszi lehetővé a feladatátvételi prioritások módosítását vagy manuális feladatátvétel elindítását. Lásd: [Feladatátvételi prioritás módosítása](#modify-failover-priority) és [Manuális feladatátvétel aktiválása.](#trigger-manual-failover)

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$locations = @("West US 2", "East US 2")
$accountName = "mycosmosaccount"
$apiKind = "GlobalDocumentDB"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Location $locations -Name $accountName `
    -ApiKind $apiKind -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Region operations
$resourceType = "Microsoft.DocumentDb/databaseAccounts"
$apiVersion = "2020-03-01"

# Add a region to the account
$locations2 = @("West US 2", "East US 2", "South Central US")
$locationObjects2 = @()
$i = 0
ForEach ($location in $locations2) { $locationObjects2 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects2;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove a region from the account
$locations3 = @("West US 2", "South Central US")
$locationObjects3 = @()
$i = 0
ForEach ($location in $locations3) { $locationObjects3 += @{ locationName = "$location"; failoverPriority = $i++ } }
$accountProperties = @{
    databaseAccountOfferType = "Standard";
    locations = $locationObjects3;
    enableAutomaticFailover = $enableAutomaticFailover;
}

Set-AzResource -ResourceType $resourceType `
    -ResourceGroupName $resourceGroupName `
    -ApiVersion $apiVersion -Name $accountName `
    -PropertyObject $accountProperties

Write-Host "Set-AzResource returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```
### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-master"></a>Több írási régió engedélyezése egy Azure Cosmos-fiókhoz

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-master
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a>Azure Cosmos-fiók törlése

Ez a parancs egy meglévő Azure Cosmos-fiókot töröl.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -PassThru
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a>Azure Cosmos-fiók címkéinek frissítése

Ez a parancs beállítja az [Azure-erőforrás-címkéket][azure-resource-tags] egy Azure Cosmos-fiókhoz. A címkék beállíthatók mind `New-AzCosmosDBAccount` a fiók létrehozásakor, mind a fiókfrissítéshez a használatával. `Update-AzCosmosDBAccount`

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a>Fiókkulcsok listázása

Amikor létrehoz egy Azure Cosmos-fiókot, a szolgáltatás két fő hozzáférési kulcsot hoz létre, amelyek az Azure Cosmos-fiók elérésekor hitelesítéshez használhatók. Csak olvasható kulcsok at hitelesítő műveletek is jönnek létre.
Két hozzáférési kulcs biztosításával az Azure Cosmos DB lehetővé teszi, hogy az Azure Cosmos-fiók megszakítása nélkül újrageneráljon és elforgasson egy kulcsot.
A Cosmos DB-fiókok két írási-olvasási kulcsgal (elsődleges és másodlagos) és két csak olvasható(elsődleges és másodlagos) rendelkezik.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a>Kapcsolati karakterláncok listázása

A következő parancs lekéri a kapcsolati karakterláncokat, hogy alkalmazásokat csatlakoztasson a Cosmos DB-fiókhoz.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a>Fiókkulcsok újragenerálása

Az Azure Cosmos-fiók hozzáférési kulcsait rendszeres időközönként újra kell generálni a kapcsolatok biztonságának biztosítása érdekében. A fiókhoz elsődleges és másodlagos hozzáférési kulcsok vannak hozzárendelve. Ez lehetővé teszi az ügyfelek számára a hozzáférés fenntartását, miközben egyszerre csak egy kulcs van újragenerálva.
Az Azure Cosmos-fiók négy típusú kulcsa van (elsődleges, másodlagos, primaryreadonly és secondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a>Automatikus feladatátvétel engedélyezése

A következő parancs beállítja a Cosmos DB-fiókot, hogy automatikusan feladatátvételt a másodlagos régióban, ha az elsődleges régió elérhetetlenné válik.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-master - cannot have both automatic
# failover and multi-master on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a>Feladatátvételi prioritás módosítása

Az automatikus feladatátvételsel konfigurált fiókok esetében módosíthatja azt a sorrendet, amelyben a Cosmos előlépteti a másodlagos replikákat elsődlegesre, ha az elsődleges elérhetetlenné válik.

Az alábbi példában tegyük fel, `West US 2 = 0` `East US 2 = 1`hogy `South Central US = 2`az aktuális feladatátvételi prioritás a , . A parancs ezt `West US 2 = 0`a `South Central US = 1` `East US 2 = 2`, , .

> [!CAUTION]
> A hely `failoverPriority=0` módosítása egy Azure Cosmos-fiók manuális feladatátvételt indít el. Minden más prioritási módosítás nem indít el feladatátvételt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a>Kézi feladatátvétel aktiválása

A manuális feladatátvételsel konfigurált fiókok esetén a feladatátvételt `failoverPriority=0`átveheti, és előléptetheti a másodlagos replika elsődleges replikáját a. Ez a művelet vész-helyreállítási gyakorlat kezdeményezésére használható a vész-helyreállítási tervezés teszteléséhez.

Az alábbi példában tegyük fel, hogy `West US 2 = 0` a `East US 2 = 1` fiók aktuális feladatátvételi prioritással rendelkezik, és a régiók tükrözése.

> [!CAUTION]
> A `locationName` `failoverPriority=0` módosítás manuális feladatátvételt indít el egy Azure Cosmos-fiókhoz. Bármely más prioritási módosítás nem indít el feladatátvételt.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB adatbázis

Az alábbi szakaszok bemutatják, hogyan kezelheti az Azure Cosmos DB adatbázist, többek között:

* [Azure Cosmos DB-adatbázis létrehozása](#create-db)
* [Azure Cosmos DB-adatbázis létrehozása megosztott átviteli hatással](#create-db-ru)
* [Az Azure Cosmos DB-adatbázis átviteli állásának beszereznie](#get-db-ru)
* [Az összes Azure Cosmos DB-adatbázis listázása egy fiókban](#list-db)
* [Egyetlen Azure Cosmos DB-adatbázis beszereznie](#get-db)
* [Azure Cosmos DB-adatbázis törlése](#delete-db)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Azure Cosmos DB-adatbázis létrehozása

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Azure Cosmos DB-adatbázis létrehozása megosztott átviteli hatással

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

Set-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Az Azure Cosmos DB-adatbázis átviteli állásának beszereznie

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Az összes Azure Cosmos DB-adatbázis beszereznie egy fiókban

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Egyetlen Azure Cosmos DB-adatbázis beszereznie

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Azure Cosmos DB-adatbázis törlése

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB tároló

Az alábbi szakaszok bemutatják, hogyan kezelheti az Azure Cosmos DB-tárolót, többek között:

* [Hozzon létre egy Azure Cosmos DB-tárolót](#create-container)
* [Hozzon létre egy Azure Cosmos DB-tárolót egy nagy partíciós kulccsal](#create-container-big-pk)
* [Az Azure Cosmos DB-tároló átviteli fazeka](#get-container-ru)
* [Hozzon létre egy Azure Cosmos DB-tárolót egyéni indexeléssel](#create-container-custom-index)
* [Azure Cosmos DB-tároló létrehozása kikapcsolt indexeléssel](#create-container-no-index)
* [Hozzon létre egy Azure Cosmos DB-tárolót egyedi kulccsal és TTL-lel](#create-container-unique-key-ttl)
* [Azure Cosmos DB-tároló létrehozása ütközésfeloldással](#create-container-lww)
* [Az összes Azure Cosmos DB-tároló felsorolása egy adatbázisban](#list-containers)
* [Egyetlen Azure Cosmos DB-tároló beszereznie egy adatbázisban](#get-container)
* [Azure Cosmos DB-tároló törlése](#delete-container)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Hozzon létre egy Azure Cosmos DB-tárolót

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Hozzon létre egy Azure Cosmos DB-tárolót nagy partíciókulcs-mérettel

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Az Azure Cosmos DB-tároló átviteli fazeka

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Hozzon létre egy Azure Cosmos DB-tárolót egyéni indexszabályzattal

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Azure Cosmos DB-tároló létrehozása kikapcsolt indexeléssel

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Hozzon létre egy Azure Cosmos DB-tárolót egyedi kulcsházirenddel és TTL-lel

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Azure Cosmos DB-tároló létrehozása ütközésfeloldással

Ha a tárolt eljárás használatához ütközésfeloldási `"mode"="custom"` házirendet szeretne létrehozni, állítsa be és `"conflictResolutionPath"="myResolverStoredProcedure"`állítsa be a feloldási útvonalat a tárolt eljárás neveként. Ha az összes ütközést az ConflictsFeed-be `"mode"="custom"` szeretné írni, és külön szeretné kezelni, állítsa be és`"conflictResolutionPath"=""`

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

Set-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Az összes Azure Cosmos DB-tároló felsorolása egy adatbázisban

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Egyetlen Azure Cosmos DB-tároló beszereznie egy adatbázisban

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Azure Cosmos DB-tároló törlése

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

## <a name="next-steps"></a>További lépések

* [Minden PowerShell-minta](powershell-samples.md)
* [Az Azure Cosmos-fiók kezelése](how-to-manage-database-account.md)
* [Hozzon létre egy Azure Cosmos DB-tárolót](how-to-create-container.md)
* [Az azure Cosmos DB-ben az idő-hátra lévő idő konfigurálása](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
