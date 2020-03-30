---
title: Phoenix Query Server REST SDK – Azure HDInsight
description: Telepítse és használja a REST SDK-t az Azure HDInsight Phoenix Query Server szolgáltatásához.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612257"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix lekérdezési kiszolgáló REST SDK

[Apache Phoenix](https://phoenix.apache.org/) egy nyílt forráskódú, masszívan párhuzamos relációs adatbázis réteg tetején [Apache HBase](apache-hbase-overview.md). A Phoenix lehetővé teszi, hogy SQL-szerű lekérdezéseket használjon a HBase-lel az SSH-eszközökön, például [az SQLLine-on](apache-hbase-query-with-phoenix.md)keresztül. A Phoenix egy Phoenix Query Server (PQS) nevű HTTP-kiszolgálót is biztosít, amely egy vékony ügyfél, amely két átviteli mechanizmust támogat az ügyfélkommunikációhoz: a JSON- és a Protokollpuffereket. A protokollpufferek az alapértelmezett mechanizmus, és hatékonyabb kommunikációt kínálnak, mint a JSON.

Ez a cikk azt ismerteti, hogy a PQS REST SDK használatával hogyan hozhat létre táblázatokat, upsert sorokat külön-külön és tömegesen, és hogyan választhat adatokat SQL utasítások használatával. A példák az [Apache Phoenix Query Server Microsoft .NET illesztőprogramját](https://www.nuget.org/packages/Microsoft.Phoenix.Client)használják. Ez az SDK az [Apache Calcite Avatica](https://calcite.apache.org/avatica/) API-jaira épül, amelyek kizárólag protokollpuffereket használnak a szerializálási formátumhoz.

További információ: [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Az SDK telepítése

Az Apache Phoenix Query Server Microsoft .NET illesztőprogramja NuGet csomagként érhető el, amely a Visual Studio **NuGet Package Manager konzolról** telepíthető a következő paranccsal:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Új PhoenixClient objektum létrehozása

A könyvtár használatának megkezdéséhez példányosítson `ClusterCredentials` meg egy `Uri` új `PhoenixClient` objektumot, amely tartalmazza a fürtés a fürt Apache Hadoop felhasználónevét és jelszavát.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Cserélje le a CLUSTERNAME-t a HDInsight HBase fürtnevére, a USERNAME és PASSWORD nevet a fürt létrehozásákor megadott Hadoop hitelesítő adatokra. Az alapértelmezett Hadoop felhasználónév **admin**.

## <a name="generate-unique-connection-identifier"></a>Egyedi kapcsolatazonosító létrehozása

Ha egy vagy több kérelmet szeretne küldeni a PQS-nek, meg kell adnia egy egyedi kapcsolatazonosítót a kérelem(ek) és a kapcsolat társításához.

```csharp
string connId = Guid.NewGuid().ToString();
```

Minden példa először hívást `OpenConnectionRequestAsync` kezdeményez a metódushoz, és átadja az egyedi kapcsolatazonosítót. Ezután `ConnectionProperties` definiálja és `RequestOptions`adja át ezeket az `ConnectionSyncRequestAsync` objektumokat és a létrehozott kapcsolatazonosítót a metódusnak. A PQS `ConnectionSyncRequest` objektuma segít biztosítani, hogy mind az ügyfél, mind a kiszolgáló egységes nézetben tekintse meg az adatbázis tulajdonságait.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest és kapcsolattulajdonságai

A `ConnectionSyncRequestAsync`híváshoz adja `ConnectionProperties` át az objektumot.

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

Íme néhány érdekes tulajdonság:

| Tulajdonság | Leírás |
| -- | -- |
| Automatikus véglegesítés | Logikai érték, amely `autoCommit` azt jelzi, hogy engedélyezve van-e a Phoenix-tranzakciókhoz. |
| ReadOnly | Logikai érték, amely azt jelzi, hogy a kapcsolat írásvédett-e. |
| Tranzakcióelkülönítése | A JDBC specifikációszerint a tranzakcióelkülönítés szintjét jelző egész szám – lásd az alábbi táblázatot.|
| Katalógus | A kapcsolat tulajdonságainak beolvasásakor használandó katalógus neve. |
| Séma | A kapcsolat tulajdonságainak beolvasásakor használandó séma neve. |
| IsDirty (Piszkos) | Logikai érték, amely azt jelzi, hogy a tulajdonságok módosultak-e. |

Itt vannak `TransactionIsolation` az értékek:

| Elkülönítési érték | Leírás |
| -- | -- |
| 0 | A tranzakciók nem támogatottak. |
| 1 | Piszkos olvasások, nem ismételhető olvasások és fantomolvasások fordulhatnak elő. |
| 2 | A piszkos olvasások nem használhatók, de nem ismételhető olvasások és fantomolvasások fordulhatnak elő. |
| 4 | A piszkos olvasások és a nem ismételhető olvasások megelőzhetők, de fantomolvasások is előfordulhatnak. |
| 8 | A piszkos olvasások, a nem ismételhető olvasások és a fantomolvasások mind megakadályozva vannak. |

## <a name="create-a-new-table"></a>Új tábla létrehozása

A HBase, mint bármely más RDBMS, táblákban tárolja az adatokat. A Phoenix szabványos SQL-lekérdezéseket használ új táblák létrehozásához, miközben meghatározza az elsődleges kulcs- és oszloptípusokat.

Ez a példa és az összes későbbi `PhoenixClient` példa az [új PhoenixClient objektum példányosított](#instantiate-new-phoenixclient-object)objektumában meghatározott példányosított objektumot használja.

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

Az előző példa egy `Customers` új `IF NOT EXISTS` táblát hoz létre, amelynek neve a beállítás használatával. A `CreateStatementRequestAsync` hívás új utasítást hoz létre az Avitica (PQS) kiszolgálón. A `finally` blokk bezárja `CreateStatementResponse` a `OpenConnectionResponse` visszaküldött és az objektumokat.

## <a name="insert-data-individually"></a>Adatok beszúrása egyenként

Ez a példa egy egyedi adatbeszúrást mutat be, amely amerikai állam- és területrövidítések `List<string>` gyűjteményére hivatkozik:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

A tábla `StateProvince` oszlopértéke egy későbbi kijelölési műveletben lesz használva.

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

A beszúrási utasítás végrehajtásának szerkezete hasonló az új tábla létrehozásához. A blokk végén `try` a tranzakció explicit módon véglegesítve van. Ez a példa 300-szor megismétli a beszúrási tranzakciót. A következő példa egy hatékonyabb kötegbeillesztési folyamatot mutat be.

## <a name="batch-insert-data"></a>Kötegadatok beszúrása

A következő kód majdnem megegyezik az adatok egyenkénttörténő beszúrásának kódjával. Ez a `UpdateBatch` példa az objektumot használja a hívásban, ahelyett, hogy `ExecuteBatchRequestAsync`ismételten egy előkészített utasítással hívna. `ExecuteRequestAsync`

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

Egy tesztkörnyezetben 300 új rekord egyedi beillesztése majdnem 2 percet vett igénybe. Ezzel szemben 300 rekord kötegként történő beszúrása mindössze 6 másodpercet igényelt.

## <a name="select-data"></a>Adatok kiválasztása

Ez a példa bemutatja, hogyan lehet egy kapcsolatot újra felhasználni több lekérdezés végrehajtásához:

1. Jelölje ki az összes rekordot, majd az alapértelmezett legfeljebb 100 rekord visszaadása után olvassa be a fennmaradó rekordokat.
2. Az egyetlen skaláris eredmény lekéréséhez használjon összes sorszám select utasítást.
3. Olyan select utasítás végrehajtása, amely az ügyfelek államonként vagy területenkénti teljes számát adja vissza.

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

A `select` kimutatások kimenetének a következő eredménynek kell lennie:

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

## <a name="next-steps"></a>További lépések

* [Apache Phoenix a HDInsightban](../hdinsight-phoenix-in-hdinsight.md)
* [Az Apache HBase REST SDK használata](apache-hbase-rest-sdk.md)
