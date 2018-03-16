<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Táblaentitásokat kezelése

Most, hogy a tábla, hogyan kezelheti az entitások vagy a sorokat a tábla vizsgáljuk meg. 

Egy entitás legfeljebb 255 tulajdonságokat, beleértve a 3 rendszertulajdonsággal állhat: **PartitionKey**, **RowKey**, és **időbélyeg**. Ön felelőssége lesz beszúrva, és az értékek frissítése **PartitionKey** és **RowKey**. A kiszolgáló kezeli értékének **időbélyeg**, amely nem módosítható. Együtt a **PartitionKey** és **RowKey** egyedi módon azonosítja az egy táblázaton belüli összes entitás.

* **PartitionKey**: meghatározza a partíció az entitás tárolt.
* **RowKey**: egyedileg azonosítja az entitást a partíción belül.

Egy entitás legfeljebb 252 egyéni tulajdonságok adhatók meg. 

### <a name="add-table-entities"></a>Adja hozzá a táblaentitásokat

Entitás hozzáadása egy tábla használatával **Add-StorageTableRow**. Ezekben a példákban partíciókulcsok értékekkel "partition1" és a "partition2" és a sor kulcsok állapot rövidítések egyenlő. Az entitásban tulajdonságainak a felhasználónév és a felhasználói azonosítóját. 

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

### <a name="query-the-table-entities"></a>A táblaentitásokat lekérdezése

Egy tábla entitást lekérdezni számos különböző módja van.

#### <a name="retrieve-all-entities"></a>Összes entitásának lekérése

Minden entitás lekéréséhez használja **Get-AzureStorageTableRowAll**.

```powershell
Get-AzureStorageTableRowAll -table $storageTable | ft
```

Ez a parancs a következő táblázat hasonló eredményeket eredményez:

| felhasználói azonosítóját | felhasználónév | partíció | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Egy adott partícióra entitások beolvasása

Egy adott partíció összes entitásának lekéréséhez használja **Get-AzureStorageTableRowByPartitionKey**.

```powershell
Get-AzureStorageTableRowByPartitionKey -table $storageTable -partitionKey $partitionKey1 | ft
```
A következő táblázat az eredmények hasonlítania:

| felhasználói azonosítóját | felhasználónév | partíció | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Kérje le az entitásokat egy megadott értéket egy adott oszlopban

Ha egy adott oszlopban szereplő érték egyenlő-e egy adott értéket entitások lekéréséhez használja **Get-AzureStorageTableRowByColumnName**.

```powershell
Get-AzureStorageTableRowByColumnName -table $storageTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

Ez a lekérdezés egy rekordot be.

|A mező|érték|
|----|----|
| felhasználói azonosítóját | 1 |
| felhasználónév | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Kérje le az entitásokat, egy egyéni szűrő használatával 

Egy egyéni szűrő segítségével entitások lekéréséhez használja **Get-AzureStorageTableRowByCustomFilter**.

```powershell
Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter "(userid eq 1)"
```

Ez a lekérdezés egy rekordot be.

|A mező|érték|
|----|----|
| felhasználói azonosítóját | 1 |
| felhasználónév | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Entitások frissítése 

Entitások frissítésére három lépésben történik. Az entitás módosításához először beolvasása. Második a módosítás. Harmadik, véglegesítse a módosítás használatával **frissítés-AzureStorageTableRow**.

Az entitás módosítására a username = "Jessie", hogy a felhasználónév = "Jessie2". Ebben a példában is látható másik módja, hogy hozzon létre egy egyéni szűrő .NET-típus használatával. 

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
| felhasználói azonosítóját | 2 |
| felhasználónév | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Tábla entitások törlése

Egy entitás vagy a tábla összes entitás törlése.

#### <a name="deleting-one-entity"></a>Egy entitás törlése

Egyetlen entitás törléséhez érintett entitásként való hivatkozás, és átadhatja azt a **Remove-AzureStorageTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.WindowsAzure.Storage.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.WindowsAzure.Storage.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzureStorageTableRowByCustomFilter `
    -table $storageTable `
    -customFilter 
$userToDelete | Remove-AzureStorageTableRow -table $storageTable 

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzureStorageTableRowAll -table $storageTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>A tábla összes entitás törlése 

A tábla összes entitás törlése, kérheti le azokat, és az eredmények pipe azokat a remove-parancsmag. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzureStorageTableRowAll `
    -table $storageTable | Remove-AzureStorageTableRow -table $storageTable 

# List entities in the table (there won't be any).
Get-AzureStorageTableRowAll -table $storageTable | ft
```