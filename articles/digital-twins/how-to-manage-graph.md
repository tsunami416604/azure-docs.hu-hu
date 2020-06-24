---
title: Ikergráf kezelése kapcsolatokkal
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan kezelheti a digitális ikrek gráfját úgy, hogy összekapcsolja őket a kapcsolatokkal.
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: e332936543cc36e20b88da4ce5cc9115d0890967
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/12/2020
ms.locfileid: "84725698"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Digitális ikrek gráfjának kezelése kapcsolatok használatával

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Az Azure digitális ikrek szíve a teljes környezetet jelképező [kettős gráf](concepts-twins-graph.md) . A Twin gráf a **kapcsolatokon**keresztül összekapcsolt digitális ikrekből tevődik össze.

Ha rendelkezik egy működő [Azure digitális Twins-példánnyal](how-to-set-up-instance.md) , és beállította a [hitelesítést](how-to-authenticate-client.md) az ügyfélalkalmazás számára, a [**DigitalTwins API**](how-to-use-apis-sdks.md) -kkal digitális ikreket és azok kapcsolatait is létrehozhatja, módosíthatja és törölheti egy Azure digitális Twins-példányban. Használhatja a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)-t vagy az [Azure Digital Twins CLI](how-to-use-cli.md)-t is.

Ez a cikk a kapcsolatok és a gráf egészének kezelésére koncentrál. az egyes digitális ikrekkel való munkavégzéshez tekintse meg a következő témakört [: útmutató: digitális Twin-eszközök kezelése](how-to-manage-twin.md).

## <a name="create-relationships"></a>Kapcsolatok létrehozása

A kapcsolatok leírják, hogy a különböző digitális ikrek hogyan kapcsolódnak egymáshoz, ami a Twin gráf alapját képezi.

A kapcsolatok a hívás használatával jönnek létre `CreateRelationship` . 

Kapcsolat létrehozásához meg kell adnia a következőket:
* A forrásként szolgáló kettős azonosító (az a Twin, ahol a kapcsolat származik)
* A célként megadott Twin azonosító (a kapcsolat megérkezési helye)
* Kapcsolat neve
* Egy kapcsolat azonosítója

A kapcsolat AZONOSÍTÓjának egyedinek kell lennie az adott forrás ikerén belül. Globálisan nem kell egyedinek lennie.
A Twin *foo*esetében például minden egyes kapcsolat azonosítójának egyedinek kell lennie. Egy másik dupla *sáv* azonban rendelkezhet olyan kimenő kapcsolattal, amely megegyezik egy *foo* -kapcsolat ugyanazzal az azonosítóval. 

Az alábbi mintakód bemutatja, hogyan adhat hozzá kapcsolatot az Azure Digital Twins-példányához.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId)
{
    var relationship = new BasicRelationship
    {
        TargetId = targetId,
        Name = "contains"
    };

    try
    {
        string relId = $"{srcId}-contains->{targetId}";
        await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
    }
}
```

További információ a segítő osztályról `BasicRelationship` [: útmutató: az Azure digitális Twins API-k és SDK-k használata](how-to-use-apis-sdks.md).

## <a name="list-relationships"></a>Kapcsolatok listázása

A gráfban található adott Twin kapcsolatok listájának eléréséhez használhatja a következőt:

```csharp
await client.GetRelationshipsAsync(id);
```

Ez egy vagy értéket ad vissza `Azure.Pageable<T>` `Azure.AsyncPageable<T>` , attól függően, hogy a hívás szinkron vagy aszinkron verzióját használja-e.

Íme egy teljes példa, amely a kapcsolatok listáját kérdezi le:

```csharp
public async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw if an error occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);
        List<BasicRelationship> results = new List<BasicRelationship>();
        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            results.Add(rel);
        }
        return results;
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
        return null;
    }
}
```

A beolvasott kapcsolatok használatával más ikrek is megnyitható a gráfban. Ehhez olvassa el a `target` visszaadott kapcsolat mezőjét, majd használja azonosítóként a következő hívásához: `GetDigitalTwin` . 

### <a name="find-relationships-to-a-digital-twin"></a>Kapcsolatok keresése a digitális Twin szolgáltatásban

Az Azure Digital Twins is rendelkezik egy API-val, amely az adott iker összes bejövő kapcsolatát megkeresi. Ez gyakran hasznos a fordított navigáláshoz, illetve a Twin törléséhez.

Az előző kód minta a kimenő kapcsolatok megtalálására koncentrál. A következő példa hasonló, de megkeresi a bejövő kapcsolatokat. A megtalált elemek után is törli őket.

Vegye figyelembe, hogy a IncomingRelationship-hívások nem adják vissza a teljes

```csharp
async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin
    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        List<IncomingRelationship> results = new List<IncomingRelationship>();
        await foreach (IncomingRelationship incomingRel in incomingRels)
            results.Add(incomingRel);
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

## <a name="delete-relationships"></a>Kapcsolatok törlése

A kapcsolatait a használatával törölheti `DeleteRelationship(source, relId);` .

Az első paraméter határozza meg a forrást (Twin) (az a kapcsolat, amelyben a kapcsolat származik). A másik paraméter a kapcsolat azonosítója. A Twin azonosítót és a kapcsolat AZONOSÍTÓját egyaránt meg kell adni, mivel a kapcsolati azonosítók csak a Twin hatókörén belül egyediek.

## <a name="create-a-twin-graph"></a>Dupla gráf létrehozása 

A következő kódrészlet a jelen cikkben található kapcsolati műveletekkel hozza létre a digitális ikrekből és kapcsolatokból álló Twin gráfot.

```csharp
static async Task CreateTwins()
{
    // Create twins - see utility functions below 
    await CreateRoom("Room01", 68, 50, false, "");
    await CreateRoom("Room02", 70, 66, true, "EId-00124");
    await CreateFloorOrBuilding("Floor01", makeFloor:true);

    // Create relationships
    await AddRelationship("Floor01", "contains", "Floor-to-Room01", "Room01");
    await AddRelationship("Floor01", "contains", "Floor-to-Room02", "Room02");
}

static async Task<bool> AddRelationship(string source, string relationship, string id, string target)
{
    var relationship = new BasicRelationship
    {
        TargetId = target,
        Name = relationship
    };

    try
    {
        string relId = $"{source}-contains->{target}";
        await client.CreateRelationshipAsync(source, relId, JsonSerializer.Serialize(relationship));
        Console.WriteLine("Created relationship successfully");
        return true;
    }
    catch (RequestFailedException rex) {
        Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
        return false;
    }
}

static async Task<bool> CreateRoom(string id, double temperature, double humidity)
{
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = "dtmi:com:contoso:Room;2";
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("Temperature", temperature);
    props.Add("Humidity", humidity);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin)); 
        return true;       
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}

static async Task<bool> CreateFloorOrBuilding(string id, bool makeFloor=true)
{
    string type = "dtmi:com:contoso:Building;3";
    if (makeFloor==true)
        type = "dtmi:com:contoso:Floor;2";
    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.Metadata = new DigitalTwinMetadata();
    twin.Metadata.ModelId = type;
    // Initialize properties
    Dictionary<string, object> props = new Dictionary<string, object>();
    props.Add("AverageTemperature", 0);
    twin.CustomProperties = props;
    
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));  
        return true;      
    }
    catch (ErrorResponseException e)
    {
        Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}"); 
        return false;
    }
}
```

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Dupla gráf létrehozása egy táblázatból

Gyakorlati használati esetekben a különálló hierarchiák gyakran egy másik adatbázisban tárolt adatokból, vagy akár egy táblázatból is létrejönnek. Ez a szakasz azt szemlélteti, hogyan lehet elemezni a táblázatokat.

Vegye figyelembe a következő adattáblázatot, amely leírja a létrehozandó Digitális ikrek és kapcsolatok készletét.

| Modell    | ID | Szülő | Kapcsolat neve | Egyéb adatszolgáltatások |
| --- | --- | --- | --- | --- |
| padló    | Floor01 | | | … |
| szoba    | Room10 | Floor01 | tartalmazza a következőt: | … |
| szoba    | Room11 | Floor01 | tartalmazza a következőt: | … |
| szoba    | Room12 | Floor01 | tartalmazza a következőt: | … |
| padló    | Floor02 | | | … |
| szoba    | Room21 | Floor02 | tartalmazza a következőt: | … |
| szoba    | Room22 | Floor02 | tartalmazza a következőt: | … |

A következő kód a [Microsoft Graph API](https://docs.microsoft.com/graph/overview) -t használja a számolótáblák beolvasásához és az eredményekből származó Azure digitális Twins Twin gráf létrehozásához.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string type = row[0].GetString();
    string id = row[1].GetString();
    string relSource = row[2].GetString();
    string relName = row[3].GetString();
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (relSource != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = relSource,
            TargetId = id,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    switch (type)
    {
        case "room":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Room;2" };
            break;
        case "floor":
            twin.Metadata = new DigitalTwinMetadata() { ModelId = "dtmi:com:contoso:Floor;2" };
            break;
        ... handle additional types
    }
    try
    {
        client.CreateDigitalTwin(id, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
        Log.Error($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            client.CreateRelationship(rec.SourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Log.Error($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Kapcsolatok kezelése a CLI-vel

Az ikrek és kapcsolataik az Azure Digital Twins CLI használatával is kezelhetők. A parancsok a következő [útmutatóban találhatók: az Azure digitális Twins parancssori](how-to-use-cli.md)felületének használata.

## <a name="next-steps"></a>További lépések

Tudnivalók az Azure Digital Twins Twin Graph lekérdezéséről:
* [Fogalmak: lekérdezési nyelv](concepts-query-language.md)
* [Útmutató: a Twin gráf lekérdezése](how-to-query-graph.md)