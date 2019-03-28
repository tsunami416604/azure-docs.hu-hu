---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: cf971fe82e9ee70db9209bb196ddf36614d39d10
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58541383"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Táblaentitások kezelése

Most, hogy rendelkezik egy táblával, nézzük meg entitások, vagy a tábla sorainak kezelése. 

Entitások legfeljebb 255 tulajdonságokat, beleértve a három rendszertulajdonsággal veheti fel: **PartitionKey**, **rowkey tulajdonságok esetén**, és **időbélyeg**. Beszúráskor és frissítéskor értékeit felelős Ön **PartitionKey** és **RowKey**. A kiszolgáló kezeli értékét **időbélyeg**, amely nem módosítható. Együtt a **PartitionKey** és **RowKey** egy táblán belül minden entitás egyedi azonosításához.

* **PartitionKey**: Meghatározza a partíció, amely az entitás van tárolva.
* **RowKey**: Egyedi módon azonosítja az entitást a partíción belül.

Egy entitás legfeljebb 252 egyéni tulajdonságokat adhat meg. 

### <a name="add-table-entities"></a>Tábla entitások hozzáadása

Entitások hozzáadása egy táblát a **Add-AzTableRow**. Ezek a példák a partíciókulcsok értékekkel `partition1` és `partition2`, és a sorkulcsok egyenlő állam rövidítése. Minden entitás tulajdonságai `username` és `userid`. 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>A tábla entitások lekérdezése

Egy tábla az entitások használatával lekérdezheti a **Get-AzTableRow** parancsot.

> [!NOTE]
> A parancsmagok **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName**, és  **Get-AzureStorageTableRowByCustomFilter** elavultak, és a egy frissítés a jövőbeli verziójában meg fog szűnni.

#### <a name="retrieve-all-entities"></a>Összes entitásának lekérése

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Ez a parancs az alábbi táblázat hasonló eredményeket eredményez:

| felhasználói azonosító | felhasználónév | partíció | rowkey tulajdonságok esetén |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Kérje le az entitásokat egy adott partícióra vonatkozóan

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Az alábbi táblázat az eredmények kinéznie:

| felhasználói azonosító | felhasználónév | partíció | rowkey tulajdonságok esetén |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Kérje le az entitásokat egy megadott értéket egy adott oszlopban

```powershell
Get-AzTableRow -table $cloudTable `
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

```powershell
Get-AzTableRow `
    -table $cloudTable `
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

Az entitások frissítése három lépésből áll. Először kérje le az entitást, módosíthatja. Ezután ellenőrizze a módosítást. Harmadik, véglegesíti a módosítás használatával **Update-AzTableRow**.

Az entitás módosítására a username = "Jessie" szeretné, hogy a felhasználónév = "Jessie2". Ebben a példában is létrehozható egy egyéni szűrő használata a .NET-típusok jeleníti meg.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
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

Egyetlen entitás törléséhez lekérdezni egy adott entitás hivatkozását, és átadhatja azt be **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>A tábla összes entitások törlése

A tábla összes entitás törléséhez kérheti le azokat, és az eredmények kanálu a remove-parancsmagba. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
