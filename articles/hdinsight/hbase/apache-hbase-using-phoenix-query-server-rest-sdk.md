---
title: "Phoenix lekérdezés Server REST SDK - az Azure HDInsight |} Microsoft Docs"
description: 
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/04/2017
ms.author: ashishth
ms.openlocfilehash: f57260b2ee280aa0f49f42cd145477205926cb0c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2018
---
# <a name="phoenix-query-server-rest-sdk"></a>Phoenix Query Server REST SDK

[Apache Phoenix](http://phoenix.apache.org/) egy nyílt forráskódú, nagymértékben párhuzamos relációs Adatbázisréteg a [HBase](apache-hbase-overview.md). Phoenix használatát teszi SQL-szerű lekérdezéseket a hbase eszközzel SSH eszközökkel, mint [SQLLine](apache-hbase-phoenix-squirrel-linux.md). Phoenix is biztosít a Phoenix lekérdezés Server (PQS), a vékony ügyfelek, az ügyfél-kommunikációhoz két átviteli mechanizmusok támogató nevű HTTP-kiszolgáló: JSON és a protokoll pufferek. Protokoll pufferek az alapértelmezett mechanizmus, és JSON-nál több hatékony kommunikációt biztosít.

Ez a cikk ismerteti, hogyan használható a PQS REST SDK táblák, upsert sorok létrehozása egyesével és csoportosan, és jelölje ki az SQL-utasítások segítségével adatokat. A példában a [Apache Phoenix lekérdezés kiszolgáló a Microsoft .NET-illesztőprogram](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Ez az SDK épül [Apache kalcit Avatica](https://calcite.apache.org/avatica/) API-k, amely kizárólag a szerializálási formátum protokoll pufferek használata.

További információkért lásd: [Apache kalcit Avatica protokoll pufferek hivatkozás](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Az SDK telepítése

A Microsoft .NET-illesztőprogram Apache Phoenix lekérdezés kiszolgáló biztosított NuGet csomag, amely a Visual Studio telepíthető **NuGet Package Manager Console** a következő paranccsal:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Új PhoenixClient objektumpéldányt

A könyvtár használatának megkezdéséhez hozható létre egy új `PhoenixClient` objektum benyújtása `ClusterCredentials` tartalmazó a `Uri` az a fürt és a fürt Hadoop-felhasználónevet és jelszót.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Cserélje le a HDInsight HBase fürt nevét, és a FELHASZNÁLÓNÉVVEL és JELSZÓVAL CLUSTERNAME a fürt létrehozásakor megadott Hadoop hitelesítő adatokat. Az alapértelmezett Hadoop felhasználónév **admin**.

## <a name="generate-unique-connection-identifier"></a>Létre egyedi azonosítója

Egy vagy több kérelmet küldeni PQS, akkor is kell egy egyedi csatlakozás-azonosító a kérelem (kérelmek) társítása a kapcsolathoz.

```csharp
string connId = Guid.NewGuid().ToString();
```

A példák először hívást indít a `OpenConnectionRequestAsync` metódust, az egyedi csatlakozás azonosító továbbításához. Ezután meg kell határozni `ConnectionProperties` és `RequestOptions`, azokat az objektumokat és a létrehozott kapcsolat-azonosító a `ConnectionSyncRequestAsync` metódust. PQS tartozó `ConnectionSyncRequest` objektum segítségével győződjön meg arról, hogy az ügyfél és a kiszolgáló rendelkezik-e az adatbázis-tulajdonságok egységes megjelenítése.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest és annak ConnectionProperties

Hívása `ConnectionSyncRequestAsync`, adjon át egy `ConnectionProperties` objektum.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Az alábbiakban néhány fontos tulajdonságok:

| Tulajdonság | Leírás |
| -- | -- |
| AutoCommit | Egy logikai azt jelöli, hogy `autoCommit` engedélyezve van a Phoenix tranzakciók. |
| ReadOnly | Olyan logikai érték azt jelöli, hogy a kapcsolat csak olvasható. |
| TransactionIsolation | Azt jelöli, a szintet egész / Típusmegadás a JDBC - tranzakció elkülönítési lásd az alábbi táblázatot.|
| Katalógus | A katalógus kapcsolat tulajdonságainak beolvasása során használandó neve. |
| Séma | A kapcsolat tulajdonságainak beolvasása során használandó séma neve. |
| IsDirty | Olyan logikai érték azt jelöli, hogy módosították a tulajdonságai. |

Az alábbiakban a `TransactionIsolation` értékeket:

| Elkülönítési érték | Leírás |
| -- | -- |
| 0 | Tranzakciók nem támogatottak. |
| 1 | A nem véglegesített adatokat, nem ismételhető olvasási és fantom olvasások fordulhat elő. |
| 2 | A rendszer letiltja a nem véglegesített adatokat, de nem ismételhető Olvasás, mind az fantom olvasások fordulhat elő. |
| 4 | Inkonzisztencia Olvasás, mind az olvasás nem ismételhető megakadályozta, de fantom olvasások fordulhat elő. |
| 8 | A nem véglegesített adatokat, nem ismételhető olvasási és fantom olvasások összes-jének megadásával. |

## <a name="create-a-new-table"></a>Új tábla létrehozása

Más RDBMS, például a HBase táblákban tárolja az adatokat. Phoenix szabványos SQL-lekérdezések használatával hozzon létre új táblákat, miközben meghatározza az elsődleges kulcs és az oszlop típusa.

Ebben a példában és minden további példákat, használja a példányként létrehozott `PhoenixClient` az objektum [új PhoenixClient objektumpéldányt](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

Az előző példában táblázatot hoz létre új nevű `Customers` használatával a `IF NOT EXISTS` lehetőséget. A `CreateStatementRequestAsync` hívás létrehoz egy új a Avitica (PQS) kiszolgálón. A `finally` blokk bezárja a visszaadott `CreateStatementResponse` és a `OpenConnectionResponse` objektumok.

## <a name="insert-data-individually"></a>Adatok egyénileg beszúrása

Ez a példa bemutatja egy egyéni adatok beszúrása hivatkozik egy `List<string>` American állapotát és területén rövidítések gyűjteménye:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

A tábla `StateProvince` oszlop értéket fogja használni a későbbi kiválasztási műveletet.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

A struktúra egy insert utasítás végrehajtása az új tábla létrehozása hasonlít. Vegye figyelembe, hogy a folyamat végén a `try` blokkot, a tranzakció explicit módon véglegesítve. Ez a példa 300 alkalommal megismétel egy egy insert tranzakció. A következő példa bemutatja a hatékonyabb kötegelt Beszúrás folyamat.

## <a name="batch-insert-data"></a>Kötegelt Beszúrás adatok

A következő kódot majdnem megegyezik a kódot az adatok beszúrása külön-külön. Ez a példa a `UpdateBatch` hívásában objektum `ExecuteBatchRequestAsync`, ahelyett, hogy a többször hívja `ExecuteRequestAsync` rendelkező előkészített utasítás.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

Egy tesztkörnyezetben 300 új rekordok külön-külön beszúrása szinte 2 percet vett igénybe. Ezzel szemben 300 rekordok beszúrása kötegként szükséges csak 6 másodperc.

## <a name="select-data"></a>Adatok kiválasztása

Ez a példa bemutatja, hogyan újból több lekérdezés végrehajtása egy kapcsolatot:

1. Válassza ki az összes rekord, és után a rendszer visszairányítja az alapértelmezett legfeljebb 100 majd beolvasni a fennmaradó rekordokat.
2. A sorok teljes számának select utasítás segítségével az egyetlen skaláris eredmény.
3. Hajtsa végre a select utasítást, amely visszaadja az állapot vagy területén ügyfelek teljes száma.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

A kimenetét a `select` utasításokat kell lennie a következő eredményét:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

<!-- ## Next steps -->
<!-- * [Phoenix in HDInsight](hdinsight-phoenix-in-hdinsight.md)  -->
<!-- * [Using the HBase REST SDK](hdinsight-using-hbase-rest-sdk.md)  -->
