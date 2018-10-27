---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: e8c5bf8e3c4cd63b7eec278c480527e95455140d
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165553"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Táblaentitások kezelése

Most, hogy rendelkezik egy táblával, nézzük meg entitások, vagy a tábla sorainak kezelése. 

Egy entitás legfeljebb 255 tulajdonságokat, beleértve a 3 rendszertulajdonsággal rendelkezhet: **PartitionKey**, **rowkey tulajdonságok esetén**, és **időbélyeg**. Beszúráskor és frissítéskor értékeit felelős **PartitionKey** és **RowKey**. A kiszolgáló kezeli értékét **időbélyeg**, amely nem módosítható. Együtt a **PartitionKey** és **RowKey** egy táblán belül minden entitás egyedi azonosításához.

* **PartitionKey**: meghatározza a partíció, amely az entitás van tárolva.
* **RowKey**: egyedileg azonosítja az entitást a partíción belül.

Egy entitás legfeljebb 252 egyéni tulajdonságokat adhat meg. 

### <a name="add-table-entities"></a>Tábla entitások hozzáadása

Entitások hozzáadása egy táblát a **Add-StorageTableRow**. Ezek a példák a partíciókulcsok értékek "partition1" és "partition2", és a sorkulcsok egyenlő állam rövidítése. Minden entitás tulajdonságai a felhasználónév és a felhasználói azonosítóját. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-StorageTableRow `
    -table $storageTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>A tábla entitások lekérdezése

Többféleképpen is eltérő az entitásokat egy tábla lekérdezésére.

#### <a name="retrieve-all-entities"></a>Összes entitásának lekérése

Minden entitás kérheti **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Ez a parancs az alábbi táblázat hasonló eredményeket eredményez:

| felhasználói azonosító | felhasználónév | partíció | rowkey tulajdonságok esetén |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Kérje le az entitásokat egy adott partícióra vonatkozóan

Egy adott partíció összes entitását kérheti **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
Az alábbi táblázat az eredmények kinéznie:

| felhasználói azonosító | felhasználónév | partíció | rowkey tulajdonságok esetén |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Kérje le az entitásokat egy megadott értéket egy adott oszlopban

Entitások, ahol egy adott oszlopban szereplő érték egyenlő-e egy adott érték lekéréséhez használja **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Ez a lekérdezés lekéri egy rekord.

|A mező|érték|
|----|----|
| felhasználói azonosító | 1 |
| felhasználónév | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Kérje le az entitásokat, egyéni szűrő használatával 

Egyéni szűrő használatával lekérdezheti, **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Ez a lekérdezés lekéri egy rekord.

|A mező|érték|
|----|----|
| felhasználói azonosító | 1 |
| felhasználónév | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Entitások frissítése 

Az entitások frissítése három lépésből áll. Először kérje le az entitást, módosíthatja. Ezután ellenőrizze a módosítást. Harmadik, véglegesíti a módosítás használatával **Update-AzureStorageTableRow**.

Az entitás módosítására a username = "Jessie" szeretné, hogy a felhasználónév = "Jessie2". Ebben a példában is létrehozható egy egyéni szűrő használata a .NET-típusok jeleníti meg. 

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2" 

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzureStorageTableRow -table $storageTable 

# To see the new record, query the table.
Get-AzureStorageTableRowByCustomFilter -table $storageTable `
    -customFilter "(username eq 'Jessie2')"
```

Az eredmények megjelenítése a Jessie2 rekord.

|A mező|érték|
|----|----|
| felhasználói azonosító | 2 |
| felhasználónév | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Tábla entitások törlése

Több entitást vagy a táblázatban lévő összes entitáshoz törölheti.

#### <a name="deleting-one-entity"></a>Egy entitás törlése

Egyetlen entitás törléséhez lekérdezni egy adott entitás hivatkozását, és átadhatja azt be **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter $filter
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>A tábla összes entitások törlése 

A tábla összes entitás törléséhez kérheti le azokat, és az eredmények kanálu a remove-parancsmagba. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```
