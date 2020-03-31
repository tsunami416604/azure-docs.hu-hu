---
title: A HBase .NET SDK – Azure HDInsight használata
description: A HBase .NET SDK segítségével táblákat hozhat létre és törölhet, valamint adatokat olvashat és írhat.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: eba7d7ad009b2ef0442a916983489489eb5cceb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806660"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>A .NET SDK használata apache HBase-hez

[Az Apache HBase](apache-hbase-overview.md) két elsődleges lehetőséget kínál az adatokkal való együttműködésre: [az Apache Hive-lekérdezéseket, valamint a HBase RESTful API-ját.](apache-hbase-tutorial-get-started-linux.md) A parancs vagy egy hasonló `curl` segédprogram segítségével közvetlenül dolgozhat a REST API-val.

C# és .NET alkalmazások esetén a [Microsoft HBase REST client library for .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) ügyfélkönyvtára a HBase REST API tetején biztosít ügyfélkönyvtárat.

## <a name="install-the-sdk"></a>Az SDK telepítése

A HBase .NET SDK nuget csomagként érhető el, amely a Visual Studio **NuGet Package Manager konzolról** telepíthető a következő paranccsal:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Új HBaseClient objektum létrehozása

Az SDK használatához példányosítson `HBaseClient` meg egy `ClusterCredentials` új `Uri` objektumot, amely a fürtből és a Hadoop felhasználónevéből és jelszavából áll.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Cserélje le a CLUSTERNAME-t a HDInsight HBase fürtnevére, a USERNAME és PASSWORD-t a fürt létrehozásakor megadott Apache Hadoop hitelesítő adatokra. Az alapértelmezett Hadoop felhasználónév **admin**.

## <a name="create-a-new-table"></a>Új tábla létrehozása

A HBase táblákban tárolja az adatokat. A táblázat egy *Sorkulcsból*, az elsődleges kulcsból és egy vagy több oszlopcsalád-csoportból áll. *column families* Az egyes táblázatokban lévő adatokat egy Rowkey tartomány vízszintesen osztja el *régiók*között. Minden régió rendelkezik egy kezdő és záró kulccsal. Egy tábla egy vagy több területből is rendelkezhet. A táblázatban lévő adatok növekedésével a HBase a nagy régiókat kisebb régiókra osztja. A régiók *régiókiszolgálókban*tárolódnak, ahol egy régiókiszolgáló több régiót is tárolhat.

Az adatok fizikailag tárolják *HFiles*. Egyetlen HFile egy tábla, egy régió és egy oszlopcsalád adatait tartalmazza. A HFile sorai rowkey-n vannak rendezve. Minden HFile rendelkezik egy *B + fa* index gyors lekérés a sorok.

Új tábla létrehozásához adjon `TableSchema` meg a és oszlopokat. A következő kód ellenőrzi, hogy a "RestSDKTable" tábla létezik-e már - ha nem, akkor létrejön a tábla.

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

Ez az új táblázat kétoszlopos családok, t1 és t2. Mivel az oszlopcsaládokat külön tárolják a különböző HFiles-fájlokban, érdemes külön oszlopcsaláddal rendelkezni a gyakran lekérdezett adatokhoz. A következő [Adatok beszúrása](#insert-data) példában oszlopok kerülnek a t1 oszlopcsaládba.

## <a name="delete-a-table"></a>Tábla törlése

Táblázat törlése:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Adat beszúrása

Adatok beszúrásához egyedi sorkulcsot kell megadni a sorazonosítóként. Az összes adat tömbben `byte[]` van tárolva. A következő kód határozza `title`meg `director`és `release_date` adja hozzá a , és oszlopokat a t1 oszlopcsaládhoz, mivel ezek az oszlopok a leggyakrabban használt. A `description` `tagline` és az oszlopok hozzáadódnak a t2 oszlopcsaládhoz. Az adatokat szükség szerint oszlopcsaládokra particionálhatja.

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

A HBase megvalósítja a [Cloud BigTable](https://cloud.google.com/bigtable/)programot, így az adatformátum a következő képre hasonlít:

![Apache HBase mintaadat-kimenet](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Adatok kiválasztása

Ha hbase táblából szeretne adatokat olvasni, adja `GetCellsAsync` át a `CellSet`tábla nevét és a sorkulcsot a metódusnak a visszatéréséhez.

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

Ebben az esetben a kód csak az első egyező sort adja vissza, mivel egy egyedi kulcsnak csak egy sornak kell lennie. A visszaadott érték `string` formátumra `byte[]` változik a tömbből. Az értéket más típusokra is konvertálhatja, például egész számra a film megjelenési dátumához:

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

## <a name="scan-over-rows"></a>Beszkavalaa a sorok között

A HBase egy vagy több sor beolvasására használja. `scan` Ez a példa több sort kér 10-es kötegekben, és lekéri azokat az adatokat, amelyek kulcsértéke i25 és 35 között van. Az összes sor beolvasása után törölje a lapolvasót az erőforrások törléséhez.

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

* [Bevezetés a HDInsight egy Apache HBase-példájába](apache-hbase-tutorial-get-started-linux.md)
* Hozzon létre egy végponttól végpontig rendelkező alkalmazást [a valós idejű Twitter-hangulat elemzése](../hdinsight-hbase-analyze-twitter-sentiment.md) segítségével az Apache HBase segítségével
