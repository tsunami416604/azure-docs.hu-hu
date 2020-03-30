---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67178794"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Táblaentitások kezelése

Most, hogy rendelkezik egy táblával, nézzük meg, hogyan kezelheti az entitásokat vagy sorokat a táblában. 

Az entitások legfeljebb 255 tulajdonsággal rendelkezhetnek, köztük három rendszertulajdonsággal: **PartitionKey**, **RowKey**és **Timestamp**. Ön a felelős a PartitionKey és a **RowKey** értékek beszúrásáért és **frissítéséért.** A kiszolgáló kezeli az **időbélyeg**értékét, amely nem módosítható. A **PartitionKey** és a **RowKey** együtt egyedileg azonosít minden entitást egy táblában.

* **PartitionKey**: Azt a partíciót határozza meg, amelyben az entitás tárolódik.
* **RowKey**: Egyedileg azonosítja az entitást a partíción belül.

Egy entitáshoz legfeljebb 252 egyéni tulajdonságdefiniálható. 

### <a name="add-table-entities"></a>Táblaentitások hozzáadása

Entitások hozzáadása táblához az **Add-AzTableRow**segítségével. Ezek a példák `partition1` olyan `partition2`partíciókulcsokat használnak, amelyek értékei és a , és az állapotrövidítésekkel egyenlő sorkulcsok. Az egyes entitások `username` `userid`tulajdonságai és a. 

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

### <a name="query-the-table-entities"></a>Táblaentitások lekérdezése

A **Get-AzTableRow** paranccsal lekérdezheti a táblában lévő entitásokat.

> [!NOTE]
> A **Get-AzureStorageTableRowAll**, **get-azurestoragetablebypartitionkey**, **get-azurestoragetablerowoszlop-név**és **get-azurestoragetablerow-customfilter** parancsmagok elavultak, és egy későbbi verziófrissítésben eltávolításra kerülnek.

#### <a name="retrieve-all-entities"></a>Az összes entitás beolvasása

```powershell
Get-AzTableRow -table $cloudTable | ft
```

Ez a parancs az alábbitáblázathoz hasonló eredményt ad:

| Userid | felhasználónév | Partíció | sorkulcs |
|----|---------|---------------|----|
| 1 | Chris | partíció1 | CA |
| 3 | Christine | partíció1 | WA |
| 2 | Jessie | partíció2 | NM |
| 4 | Steven | partíció2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Adott partíció entitásainak lekérése

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Az eredmények az alábbi táblázathoz hasonlóan néznek ki:

| Userid | felhasználónév | Partíció | sorkulcs |
|----|---------|---------------|----|
| 1 | Chris | partíció1 | CA |
| 3 | Christine | partíció1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Adott oszlop adott értékének entitásai lekérése

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Ez a lekérdezés egy rekordot olvas be.

|mező|érték|
|----|----|
| Userid | 1 |
| felhasználónév | Chris |
| PartitionKey | partíció1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Entitások beolvasása egyéni szűrővel 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

Ez a lekérdezés egy rekordot olvas be.

|mező|érték|
|----|----|
| Userid | 1 |
| felhasználónév | Chris |
| PartitionKey | partíció1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Entitások frissítése 

Az entitások frissítéséhez három lépés ből áll. Először olvassa be a módosításhoz tartozó entitást. Másodszor, változtassa meg. Harmadszor, véglegesítse a módosítást az **Update-AzTableRow**segítségével.

Frissítse az entitást a felhasználónév = "Jessie" felhasználónévvel = "Jessie2". Ez a példa azt is bemutatja, hogy egy másik módja annak, hogy hozzon létre egy egyéni szűrőt a .NET típusok használatával.

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

Az eredmények a Jessie2 rekordot mutatják.

|mező|érték|
|----|----|
| Userid | 2 |
| felhasználónév | Jessie2 (1988) |
| PartitionKey | partíció2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Táblaentitások törlése

Törölhet egy entitást vagy a tábla összes entitását.

#### <a name="deleting-one-entity"></a>Egy entitás törlése

Egyetlen entitás törléséhez kapjon hivatkozást az adott entitásra, és az **Remove-AzTableRow**sorba.

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

#### <a name="delete-all-entities-in-the-table"></a>A tábla összes entitásának törlése

A tábla összes entitásának törléséhez kérje be őket, és az eredményeket az eltávolítási parancsmagba adja. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
