---
title: Phoenix-lekérdezéskiszolgáló REST SDK – Azure HDInsight
description: Telepítse, és az Azure HDInsight Phoenix-lekérdezéskiszolgáló REST SDK-t használni.
services: hdinsight
ms.service: hdinsight
author: ashishthaps
ms.author: ashishth
editor: jasonwhowell
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/04/2017
ms.openlocfilehash: 93a08baddb12f427902f33171eba72f3dea628a6
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2018
ms.locfileid: "39599044"
---
# <a name="phoenix-query-server-rest-sdk"></a>Phoenix-lekérdezéskiszolgáló REST SDK

[Az Apache Phoenix](http://phoenix.apache.org/) egy nyílt forráskódú, nagy mértékben párhuzamosított alapuló relációs Adatbázisréteg a [HBase](apache-hbase-overview.md). A Phoenix lehetővé teszi, hogy az SQL-szerű lekérdezéseket a hbase-ben az SSH-eszközök segítségével például [az SQLLine](apache-hbase-phoenix-squirrel-linux.md). A Phoenix is biztosít a Phoenix lekérdezés kiszolgáló (PQS), a vékony ügyfelek, amelyek kétféle átviteli módszer támogatja az ügyfél-kommunikációhoz nevű HTTP-kiszolgáló: JSON-t és a Protokollpufferekhez. Protokollpuffereket az olyan alapértelmezett mechanizmus, és JSON-nál több hatékony kommunikációt biztosít.

Ez a cikk ismerteti, hogyan használhatja az PQS REST SDK-t létrehozni a táblákat, upsert sorok, egyenként és tömegesen, és válassza ki az SQL-utasítások használatával adatokat. A példákban a [a Microsoft .NET-illesztő az Apache Phoenix-lekérdezéskiszolgáló](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Ez az SDK épül [Apache kalcit Avatica](https://calcite.apache.org/avatica/) API-k, amely kizárólag a szerializálási formátumot használja Protokollpuffereket.

További információkért lásd: [Apache pufferek kalcit Avatica Protokollreferenciáját](https://calcite.apache.org/avatica/docs/protobuf_reference.html).

## <a name="install-the-sdk"></a>Az SDK telepítése

Az Apache Phoenix-lekérdezéskiszolgáló a Microsoft .NET-illesztőprogram NuGet-csomagként, amely a Visual studióból is telepíthető biztosított **NuGet Package Manager Console** a következő paranccsal:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Új PhoenixClient objektumpéldányt

A kezdéshez a kódtár használatával hozható létre egy új `PhoenixClient` objektum átadása az `ClusterCredentials` tartalmazó a `Uri` , a fürt és a fürt Hadoop-felhasználónevet és jelszót.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Cserélje le a HDInsight HBase-fürt nevét, és a FELHASZNÁLÓNÉVVEL és JELSZÓVAL CLUSTERNAME a fürt létrehozásakor megadott Hadoop hitelesítő adatokat. Az alapértelmezett Hadoop felhasználónév **rendszergazdai**.

## <a name="generate-unique-connection-identifier"></a>Hozzon létre egyedi azonosítója

Egy vagy több kérést küld PQS, a kérelem társítása a kapcsolat egyedi kapcsolat azonosítónak tartalmaznia kell.

```csharp
string connId = Guid.NewGuid().ToString();
```

Minden példánál először hívást indít a `OpenConnectionRequestAsync` módot, a kapcsolat egyedi azonosítót ad át. Ezt követően adja meg `ConnectionProperties` és `RequestOptions`, átadja azokat az objektumokat és a létrehozott kapcsolat azonosító a `ConnectionSyncRequestAsync` metódust. A PQS `ConnectionSyncRequest` objektum segítségével győződjön meg arról, hogy az ügyfél és a kiszolgáló rendelkezik-e az adatbázis tulajdonságainak egységes megjelenítése.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest és annak ConnectionProperties

A hívás `ConnectionSyncRequestAsync`, adja át a `ConnectionProperties` objektum.

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

Az alábbiakban néhány lényeges tulajdonságát:

| Tulajdonság | Leírás |
| -- | -- |
| AutoCommit | Egy logikai jelzi, hogy e `autoCommit` Phoenix tranzakciók engedélyezve van. |
| ReadOnly | Egy logikai értéket jeleníti meg, hogy a kapcsolat csak olvasható. |
| TransactionIsolation | Egy egész számot jelzi, hogy a szint a JDBC - specifikációi a tranzakció elkülönítési lásd az alábbi táblázatot.|
| Katalógus | A katalógus kapcsolati tulajdonságok beolvasása során használandó neve. |
| Séma | Neve a kapcsolati tulajdonságok beolvasása során használandó sémát. |
| IsDirty | Jelzi, hogy módosultak-e a Tulajdonságok logikai érték. |

Az alábbiakban a `TransactionIsolation` értékeket:

| Elkülönítés érték | Leírás |
| -- | -- |
| 0 | Tranzakciók nem támogatottak. |
| 1 | A nem véglegesített adatokat, nem ismételhető olvasások és fantom olvasási fordulhat elő. |
| 2 | Inkonzisztencia olvasási ebben az esetben, de nem ismételhető olvasási és fantom olvasási fordulhat elő. |
| 4 | Inkonzisztencia olvasási és nem ismételhető olvasási akadályozza meg, de fantom olvasási fordulhat elő. |
| 8 | A nem véglegesített adatokat, nem ismételhető olvasások és fantom olvasási összes ebben az esetben. |

## <a name="create-a-new-table"></a>Új tábla létrehozása

Mint bármely más relációsadatbázis-kezelő rendszer, a HBase táblák tárolja az adatokat. Phoenix standard SQL-lekérdezések használatával hozzon létre új táblákat, miközben meghatározza az elsődleges kulcsot, az oszlop típusa.

Ebben a példában és minden későbbi példák, használja a példányosított `PhoenixClient` ahogyan az az objektum [új PhoenixClient objektumpéldányt](#instantiate-new-phoenixclient-object).

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

Az előző példában létrehoz egy új táblát nevű `Customers` használatával a `IF NOT EXISTS` lehetőséget. A `CreateStatementRequestAsync` hívás létrehoz egy új állapotkimutatást a Avitica (PQS) kiszolgálón. A `finally` blokk bezárja a visszaadott `CreateStatementResponse` és a `OpenConnectionResponse` objektumokat.

## <a name="insert-data-individually"></a>Adatok beszúrása a külön-külön

Ez a példa bemutatja egy egyéni adatok beszúrása, hivatkozik egy `List<string>` amerikai állami és területi rövidítések gyűjteményét:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

A tábla `StateProvince` oszlop értéket fogja használni, jelölje be ezt követő művelet.

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

Az insert utasítás végrehajtása struktúráját nagyon hasonlít egy új táblát. Vegye figyelembe, hogy a végén a `try` letiltása, a tranzakció számára fontos, explicit módon. Ebben a példában az egy insert tranzakció 300 többször ismétlődik. Az alábbi példa bemutatja egy hatékonyabb kötegelt insert folyamat.

## <a name="batch-insert-data"></a>Kötegelt insert adatok

A következő kódot majdnem megegyezik a kódot az adatok külön-külön beszúrása. Ez a példa a `UpdateBatch` objektum egy hívás `ExecuteBatchRequestAsync`, ahelyett, hogy ismételten hívása `ExecuteRequestAsync` rendelkező előkészített utasítás.

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

Egy tesztkörnyezetben 300 új rekordok külön-külön beszúrása majdnem 2 percet vett igénybe. Ezzel szemben a 300 rekordok beszúrása egy kötegelt szükséges csak 6 másodperc.

## <a name="select-data"></a>Adatok kiválasztása

Ez a példa bemutatja, hogyan használhat újból egy kapcsolatot, több lekérdezés végrehajtásához:

1. Válassza ki az összes rekordot, és majd beolvassa a hátralévő rekordok, után az alapértelmezett maximális 100 adja vissza.
2. Teljes sor száma select utasítás használatával kérje le az egyetlen skaláris eredményt.
3. Hajtsa végre egy kiválasztási utasítás által visszaadott állam vagy régió per ügyfelek teljes száma.

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

A kimenetét a `select` utasításokat az alábbi eredményt kell lennie:

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

* [Phoenix a HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [A HBase REST SDK-val](apache-hbase-rest-sdk.md)
