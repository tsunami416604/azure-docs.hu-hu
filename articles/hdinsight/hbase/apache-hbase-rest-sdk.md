---
title: A HBase .NET SDK - használata az Azure HDInsight |} Microsoft Docs
description: A HBase .NET SDK használatával létrehozásához, és törli a táblákat, és olvasási és írási adatok.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/13/2017
ms.author: ashishth
ms.openlocfilehash: a1b9cd3fd9dbad5d8f27027a97c284b1bc3ba783
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="use-the-hbase-net-sdk"></a>A HBase .NET SDK használata

[A HBase](apache-hbase-overview.md) az adatok két elsődleges lehetőségeket: [Hive a lekérdezéseket, és a HBase a RESTful API](apache-hbase-tutorial-get-started-linux.md). Dolgozhat közvetlenül a REST API használatával a `curl` parancs vagy egy hasonló segédprogramot.

C# és .NET-alkalmazások a [Microsoft HBase REST ügyféloldali kódtára a .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) fölött a HBase REST API-t egy ügyfélkönyvtárat biztosít.

## <a name="install-the-sdk"></a>Az SDK telepítése

A HBase .NET SDK NuGet csomag, amely a Visual Studio telepíthető biztosított **NuGet Package Manager Console** a következő paranccsal:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Egy új HBaseClient objektumpéldányt

Az SDK használatához hozható létre egy új `HBaseClient` objektum benyújtása `ClusterCredentials` álló a `Uri` a fürtöt, és a Hadoop-felhasználónevet és a jelszavát.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Cserélje le a HDInsight HBase fürt nevét, és a FELHASZNÁLÓNÉVVEL és JELSZÓVAL CLUSTERNAME a fürt létrehozásakor megadott Hadoop hitelesítő adatokat. Az alapértelmezett Hadoop felhasználónév **admin**.

## <a name="create-a-new-table"></a>Új tábla létrehozása

A HBase táblákban tárolja az adatokat. A tábla tartalmaz egy *Rowkey*, az elsődleges kulcs, és az oszlopok egy vagy több csoportjára nevű *oszlopcsaláddal*. Minden tábla vízszintesen van-e terjesztve a Rowkey széles *régiók*. Minden egyes régió egy kezdő és záró kulccsal rendelkezik. Egy tábla egy vagy több régió lehet. A tábla adatainak növekedésével HBase felosztja a nagy régiók kisebb régiók. Régiók tárolódnak *régió kiszolgálók*, ahol egy régió tárolhat több régióba.

Az adatokat fizikailag tárolja *HFiles*. Egyetlen HFile egy tábla, egy régióhoz és egy oszlop termékcsalád adatokat tartalmaz. HFile sorokat rendezi a Rowkey tárolja. Minden egyes HFile rendelkezik egy *B + fa* gyors beolvasása a sor indexe.

Új tábla létrehozása, adjon meg egy `TableSchema` és oszlopokat. Az alábbi kód ellenőrzi, hogy a tábla már létezik "RestSDKTable" – Ha nem, a tábla jöjjön létre.

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

Az új tábla két oszlopcsaláddal, a t1 és t2 rendelkezik. Mivel oszlopcsaláddal külön-külön különböző HFiles tárolódnak, érdemes egy külön oszlop termékcsalád, gyakran lekérdezett adatok rendelkezik. Az alábbi [adatok beszúrása](#insert-data) példában oszlopa lett felvéve a t1 oszlop termékcsalád az.

## <a name="delete-a-table"></a>Tábla törlése

Tábla törlése:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Adat beszúrása

Adatok beszúrása, meg kell adnia egy egyedi sorkulcsa a sorazonosítóként. Minden adat egy `byte[]` tömb. Az alábbi kód határozza meg, és hozzáadja a `title`, `director`, és `release_date` ezeket az oszlopokat, a t1 oszlop termékcsalád oszlopok a leggyakrabban használt. A `description` és `tagline` oszlopa lett felvéve a t2 oszlop termékcsalád az. Az adatok a oszlopcsaláddal igény szerint is partícióazonosító.

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

A HBase BigTable, valósítja meg, így az adatok formátum a következőképpen néznek:

![Fürt felhasználói szerepkörrel rendelkező felhasználó](./media/apache-hbase-rest-sdk/table.png)

## <a name="select-data"></a>Adatok kiválasztása

Adatokat olvasni egy HBase tábla, adja át a tábla nevét és a sor kulcsot a `GetCellsAsync` metódus vissza a `CellSet`.

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

Ebben az esetben a kódot adja vissza csak az első egyező sor, mivel csak akkor kell egy egyedi kulcsot egy sort. A visszaadott érték megváltozik a `string` a formázza a `byte[]` tömb. Az érték is átalakítása más típusú, például egy egész számot a movie kiadás dátuma:

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

## <a name="scan-over-rows"></a>Sorok beolvasásához

A HBase használ `scan` egy vagy több sort beolvasni. Ebben a példában több sort 10 kötegekben kéri, és olvassa ki az adatokat, amelyek legfontosabb értékei 25 és 35. Beolvasása összes sorát, után törölje a képolvasó erőforrások karbantartása.

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

* [Ismerkedés az Apache HBase hdinsightban példa](apache-hbase-tutorial-get-started-linux.md)
* A végpont alkalmazás létrehozásához [elemzése a hbase eszközzel valós idejű Twitter sentiment](../hdinsight-hbase-analyze-twitter-sentiment.md)
