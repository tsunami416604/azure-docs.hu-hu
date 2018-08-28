---
title: A HBase .NET SDK - Azure HDInsight használata
description: A HBase .NET SDK használatával hozhat létre, és törli a táblákat, és adatok olvasását és írását.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: af3b87fbe79624143b6c2b7e0a3c50852e532524
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43042121"
---
# <a name="use-the-hbase-net-sdk"></a>A HBase .NET SDK használata

[A HBase](apache-hbase-overview.md) az adatokkal való munka két elsődleges lehetőségeket: [Hive-lekérdezések és a HBase REST-alapú API-hívások](apache-hbase-tutorial-get-started-linux.md). Használhatja a közvetlenül a REST API használatával a `curl` parancs vagy egy hasonló eszköz.

C# és a .NET-alkalmazások a [Microsoft HBase REST Client Library for .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) a HBase REST API egy ügyfélkönyvtárat biztosít.

## <a name="install-the-sdk"></a>Az SDK telepítése

A HBase .NET SDK NuGet-csomagként, amely a Visual studióból is telepíthető biztosított **NuGet Package Manager Console** a következő paranccsal:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Egy új HBaseClient objektumpéldányt

Az SDK használatához létre egy új `HBaseClient` objektum átadása az `ClusterCredentials` mikroszolgáltatásokból álló, a `Uri` , a fürthöz, és a Hadoop-felhasználónevet és jelszót.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Cserélje le a HDInsight HBase-fürt nevét, és a FELHASZNÁLÓNÉVVEL és JELSZÓVAL CLUSTERNAME a fürt létrehozásakor megadott Hadoop hitelesítő adatokat. Az alapértelmezett Hadoop felhasználónév **rendszergazdai**.

## <a name="create-a-new-table"></a>Új tábla létrehozása

A HBase táblák tárolja az adatokat. Egy táblázat áll egy *rowkey tulajdonságok esetén*, az elsődleges kulcsot, és a egy vagy több csoportot, az oszlopok nevű *oszlopcsaláddal*. Minden egyes táblában lévő adatokat vízszintesen elosztott be Rowkey által *régiók*. Minden egyes régió egy kezdő és záró kulccsal rendelkezik. Egy tábla egy vagy több régióban is rendelkezik. A tábla adatait növekedésével a HBase felosztja a nagy régiók kisebb régiók. Régiók tárolt *régióbeli kiszolgálók*, ahol egy régió tárolhat több régióban.

Az adatokat fizikailag tárolja *HFiles*. Egyetlen HFile egy olyan táblát, egy adott régióban, és a egy oszlopcsalád adatait tartalmazza. Sorok HFile tárolódnak, Rowkey alapján vannak rendezve. Minden egyes HFile rendelkezik egy *B + fa* indexben a sorok gyors lekérésére.

Hozzon létre egy új táblát, adjon meg egy `TableSchema` és oszlopokat. A következő kódot ellenőrzi, hogy a tábla már létezik "RestSDKTable" – Ha nem, a tábla jön létre.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Az új tábla két oszlopcsaláddal, a t1 és t2. Mivel oszlopcsaláddal különböző HFiles elkülönítve tárolódnak, logikus szeretné, hogy egy külön oszlopcsalád gyakran lekérdezett adatok. A következő [adatok beszúrása](#insert-data) például oszlopok a t1 oszlopcsalád kerülnek.

## <a name="delete-a-table"></a>Tábla törlése

Tábla törlése:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Adat beszúrása

Adatok beszúrása, meg kell adni egy egyedi sorkulcsot sort azonosítójaként. Az összes adata egy `byte[]` tömb. Az alábbi kód határozza meg, és hozzáadja a `title`, `director`, és `release_date` oszlopokat a t1 oszlopcsalád szerint ezeket az oszlopokat a leggyakrabban lehívott. A `description` és `tagline` oszlopokat ad hozzá, a t2 oszlopcsalád. Igény szerint oszlopcsaláddal be is particionálja az adatait.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

A HBase BigTable, valósít meg, így az adatok formátumát az alábbihoz hasonló:

![Fürt felhasználói szerepkörrel rendelkező felhasználók](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Adatok kiválasztása

Adatok beolvasása a kiszolgálóról egy HBase-tábla, át kell adnia a tábla nevét és a sor kulcsot a `GetCellsAsync` metódus való visszatéréshez a `CellSet`.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

Ebben az esetben a kódot adja vissza csak az első egyező sor, mivel csak akkor kell egy sort egy egyedi kulcsot. A visszaadott érték megváltozik `string` formázáshoz a `byte[]` tömb. Átválthat a értéke más típusú, például egy egész számot a film kiadási dátum:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Sorain vizsgálata

A HBase használ `scan` egy vagy több sor lekéréséhez. Ebben a példában több sort 10 kötegekben kéri, és kér le adatokat, amelynek fő között kell lennie 25-ös és 35. Után az összes sor beolvasása, törölje az olvasót az erőforrások törlése.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>További lépések

* [Ismerkedés a HDInsight az Apache HBase példa](apache-hbase-tutorial-get-started-linux.md)
* A végpontok közötti alkalmazás létrehozását [a hbase-ben a valós idejű Twitter-vélemények elemzése](../hdinsight-hbase-analyze-twitter-sentiment.md)
