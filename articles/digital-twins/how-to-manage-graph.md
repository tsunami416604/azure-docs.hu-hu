---
title: Ikergráf kezelése kapcsolatokkal
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan kezelheti a digitális ikrek gráfját úgy, hogy összekapcsolja őket a kapcsolatokkal.
author: baanders
ms.author: baanders
ms.date: 10/21/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6d197c8853521e0fb0c6e247ad05a046da559454
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427916"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Digitális ikrek gráfjának kezelése kapcsolatok használatával

Az Azure digitális ikrek szíve a teljes környezetet jelképező [kettős gráf](concepts-twins-graph.md) . A Twin gráf a **kapcsolatokon**keresztül összekapcsolt digitális ikrekből tevődik össze. 

Miután egy működő [Azure digitális Twins-példánnyal](how-to-set-up-instance-portal.md) rendelkezik, és beállította a [hitelesítési](how-to-authenticate-client.md) kódot az ügyfélalkalmazás számára, a [**DigitalTwins API**](how-to-use-apis-sdks.md) -kkal digitális ikreket és azok kapcsolatait is létrehozhatja, módosíthatja és törölheti egy Azure digitális Twins-példányban. Használhatja a [.net (C#) SDK](https://www.nuget.org/packages/Azure.DigitalTwins.Core)-t vagy az [Azure Digital Twins CLI](how-to-use-cli.md)-t is.

Ez a cikk a kapcsolatok és a gráf egészének kezelésére koncentrál. az egyes digitális ikrekkel való munkavégzéshez tekintse meg az [*útmutató: digitális ikrek kezelése*](how-to-manage-twin.md)című témakört.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Kapcsolatok létrehozása

A kapcsolatok leírják, hogy a különböző digitális ikrek hogyan kapcsolódnak egymáshoz, ami a Twin gráf alapját képezi.

A kapcsolatok a hívás használatával jönnek létre `CreateRelationship()` . 

Kapcsolat létrehozásához meg kell adnia a következőket:
* A forrásként szolgáló Twin azonosító (az `srcId` alábbi kódrészletben): annak a Twin-azonosítónak a azonosítója, amelyből a kapcsolat származik.
* A cél dupla azonosító (az `targetId` alábbi kódrészletben): annak a Twin-azonosítónak a azonosítója, amelyben a kapcsolat megérkezik.
* Kapcsolat neve (az `relName` alábbi kódrészletben): a kapcsolat általános típusa, például a _tartalmaz_.
* Egy kapcsolat azonosítója (az `relId` alábbi mintakód esetében): a kapcsolat adott neve, például _Relationship1_.

A kapcsolat AZONOSÍTÓjának egyedinek kell lennie az adott forrás ikerén belül. Nem kell globálisan egyedinek lennie.
A Twin *foo*esetében például minden egyes kapcsolat azonosítójának egyedinek kell lennie. Egy másik dupla *sáv* azonban rendelkezhet olyan kimenő kapcsolattal, amely megegyezik egy *foo* -kapcsolat ugyanazzal az azonosítóval.

Az alábbi mintakód azt szemlélteti, hogyan hozható létre kapcsolat az Azure Digital Twins-példányban.

```csharp
public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }
            
        }
```
A Main metódusban most meghívhatja a `CreateRelationship()` függvényt, hogy hozzon létre egy olyan kapcsolatot, amely _a_ következőhöz hasonló: 

```csharp
await CreateRelationship(client, srcId, targetId, "contains");
```
Ha több kapcsolatot szeretne létrehozni, megismételheti a hívásokat ugyanahhoz a metódushoz, és a különböző típusú kapcsolatokat átadva az argumentumba. 

További információ a segítő osztályról `BasicRelationship` [*: útmutató: az Azure digitális Twins API-k és SDK-k használata*](how-to-use-apis-sdks.md#serialization-helpers).

### <a name="create-multiple-relationships-between-twins"></a>Több kapcsolat létrehozása az ikrek között

A kapcsolatok besorolása a következők egyike lehet: 

* Kimenő kapcsolatok: az ehhez a twinhoz tartozó kapcsolatok, amelyek kifelé mutatnak a másik ikrekhez való kapcsolódáshoz. A `GetRelationshipsAsync()` metódus a kettős kapcsolat kimenő kapcsolatainak lekérésére szolgál.
* Bejövő kapcsolatok: a "beérkező" hivatkozás létrehozásához a másik ikrek felé mutató kapcsolatok. A `GetIncomingRelationshipsAsync()` metódus a kettős kapcsolat bejövő kapcsolatainak beolvasására szolgál.

A két ikrek közötti kapcsolatok száma nincs korlátozva – az ikrek tetszőleges számú kapcsolata lehet. 

Ez azt jelenti, hogy egyszerre több különböző típusú kapcsolatot tud kifejezni két ikrek között. Például a *Twin A egy* *tárolt* *kapcsolattal és egy* *dupla B*-vel létesített kapcsolattal is rendelkezhet.

Akár ugyanazon típusú kapcsolat több példányát is létrehozhatja ugyanazon két ikrek között, ha szükséges. Ebben a példában a *Twin A* két különböző *tárolt* kapcsolattal *rendelkezik, a 2.* számú kapcsolattal, feltéve, hogy a kapcsolatok eltérő kapcsolati azonosítókkal rendelkeznek.

## <a name="list-relationships"></a>Kapcsolatok listázása

A gráfban lévő egy adott Twin-hez tartozó **kimenő** kapcsolatok listájának eléréséhez az `GetRelationships()` alábbihoz hasonló módszert használhatja:

```csharp
await client.GetRelationships()
```

Ez egy vagy értéket ad vissza `Azure.Pageable<T>` `Azure.AsyncPageable<T>` , attól függően, hogy a hívás szinkron vagy aszinkron verzióját használja-e.

Íme egy példa, amely a kapcsolatok listáját kérdezi le:

```csharp
public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

```
Most meghívhatja ezt a metódust az ikrek kimenő kapcsolatainak megtekintéséhez:

```csharp
await FindOutgoingRelationshipsAsync(client, twin_Id);
```
A beolvasott kapcsolatok használatával más ikrek is megnyitható a gráfban. Ehhez olvassa el a `target` visszaadott kapcsolat mezőjét, majd használja azonosítóként a következő hívásához: `GetDigitalTwin()` .

### <a name="find-incoming-relationships-to-a-digital-twin"></a>Bejövő kapcsolatok keresése digitális Twin-re

Az Azure Digital Twins is rendelkezik egy API-val, amely az adott Twin-hez tartozó összes*bejövő** kapcsolatot megkeresi. Ez gyakran hasznos a fordított navigáláshoz, illetve a Twin törléséhez.

A korábbi kód minta a kimenő kapcsolatok egy Twin-ből való megtalálására koncentrál. A következő példa hasonló strukturált, de a *bejövő* kapcsolatokat is megkeresi a Twin helyett.

Vegye figyelembe, hogy a `IncomingRelationship` hívások nem adják vissza a kapcsolat teljes törzsét.

```csharp
public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }
```

Most meghívhatja ezt a metódust az ikrek bejövő kapcsolatainak megtekintéséhez:

```csharp
await FindIncomingRelationshipsAsync(client, twin_Id);
```
### <a name="list-all-twin-properties-and-relationships"></a>Az összes Twin tulajdonság és kapcsolat listázása

A fenti módszerek használatával a kimenő és a bejövő kapcsolatok egy Twin-re való listázásához létrehozhat egy metódust, amely a teljes Twin-adatokat, például a Twin tulajdonságait és a kapcsolatainak mindkét típusát kinyomtatja. Íme egy példa erre a metódusra, `FetchAndPrintTwinAsync()` amely bemutatja, hogyan teheti ezt meg.

```csharp  
private static async Task FetchAndPrintTwinAsync(DigitalTwinsClient client, string twin_Id)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }
```

Ezt a függvényt most a következőhöz hasonlóan hívhatja meg a fő metódusban: 

```csharp
await FetchAndPrintTwinAsync(client, targetId);
```
## <a name="delete-relationships"></a>Kapcsolatok törlése

Az első paraméter határozza meg a forrást (Twin) (az a kapcsolat, amelyben a kapcsolat származik). A másik paraméter a kapcsolat azonosítója. A Twin azonosítót és a kapcsolat AZONOSÍTÓját egyaránt meg kell adni, mivel a kapcsolati azonosítók csak a Twin hatókörén belül egyediek.

```csharp
private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId, string relId)
        {
            try
            {
                Response response = await client.DeleteRelationshipAsync(srcId, relId);
                await FetchAndPrintTwinAsync(srcId, client);
                Console.WriteLine("Deleted relationship successfully");
            }
            catch (RequestFailedException Ex)
            {
                Console.WriteLine($"Error {Ex.ErrorCode}");
            }
        }
```

Most meghívhatja ezt a metódust a következőhöz hasonló kapcsolat törléséhez:

```csharp
await DeleteRelationship(client, srcId, $"{targetId}-contains->{srcId}");
```
## <a name="create-a-twin-graph"></a>Dupla gráf létrehozása 

A következő futtatható a jelen cikk kapcsolati műveleteivel létrehoz egy dupla gráfot a digitális ikrekből és a kapcsolatokból.

A kódrészlet a [Room.json](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) és a [Floor.js](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) modell-definíciókat használja az [*oktatóanyag: az Azure Digital Twins és egy minta ügyfélalkalmazás megismerése*](tutorial-command-line-app.md). Ezekkel a hivatkozásokkal közvetlenül megtekintheti a fájlokat, vagy letöltheti őket a teljes végpontok közötti [minta projekt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)részeként.

Cserélje le a helyőrzőt az `<your-instance-hostname>` Azure Digital Twins-példány részleteire, és futtassa a mintát.

```csharp 
using System;
using Azure.DigitalTwins.Core;
using Azure.Identity;
using System.Threading.Tasks;
using System.IO;
using System.Collections.Generic;
using Azure;
using Azure.DigitalTwins.Core.Serialization;
using System.Text.Json;

namespace minimal
{
    class Program
    {

        static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");

            Console.WriteLine($"Upload a model");
            BasicDigitalTwin twin = new BasicDigitalTwin();
            var typeList = new List<string>();
            string srcId = "myRoomID";
            string targetId = "myFloorID";
            string dtdl = File.ReadAllText("Room.json");
            string dtdl1 = File.ReadAllText("Floor.json");
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the model to the service

            await client.CreateModelsAsync(typeList);

            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            twin.CustomProperties = props;

            await client.CreateDigitalTwinAsync(srcId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            // Creating twin data for second twin
            twin.Metadata = new DigitalTwinMetadata();
            twin.Metadata.ModelId = "dtmi:example:Floor;1";
            // Initialize properties
            Dictionary<string, object> props1 = new Dictionary<string, object>();
            props1.Add("Capacity", 5.0);
            twin.CustomProperties = props1;
            await client.CreateDigitalTwinAsync(targetId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
            Console.WriteLine();
            Console.WriteLine("Deleting existing relationships to the twin");
            Console.WriteLine();
            await DeleteRelationship(client, srcId);
            Console.WriteLine("Twin created successfully");
            await CreateRelationship(client, srcId, targetId, "contains");
            await CreateRelationship(client, srcId, targetId, "has");
            Console.WriteLine();
            Console.WriteLine("Printing srcId - Outgoing relationships");
            Console.WriteLine();
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Printing targetId - Incoming relationships");
            Console.WriteLine();
            await FetchAndPrintTwinAsync(targetId, client);

        }

        private static async Task DeleteRelationship(DigitalTwinsClient client, string srcId)
        {
            List<BasicRelationship> lists = await FindOutgoingRelationshipsAsync(client, srcId);
            foreach(BasicRelationship rel in lists) {
                await client.DeleteRelationshipAsync(srcId, rel.Id);
            }
            
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        public async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{targetId}-{relName}->{srcId}";
                await client.CreateRelationshipAsync(srcId, relId, JsonSerializer.Serialize(relationship));
                Console.WriteLine($"Created {relName} relationship successfully");
            }
            catch (RequestFailedException rex)
            {
                Console.WriteLine($"Create relationship error: {rex.Status}:{rex.Message}");
            }

        }

        private static async Task FetchAndPrintTwinAsync(string twin_Id, DigitalTwinsClient client)
        {
            BasicDigitalTwin twin;
            Response<string> res = client.GetDigitalTwin(twin_Id);
            twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        public static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine(relJson);
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        public static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw an error if a problem occurs
                AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

                List<IncomingRelationship> results = new List<IncomingRelationship>();
                await foreach (IncomingRelationship incomingRel in incomingRels)
                {
                    results.Add(incomingRel);
                    Console.WriteLine(incomingRel.RelationshipId);

                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

    }
}
```

Itt látható a fenti program konzoljának kimenete: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konzol kimenete, amely a Twin részleteit, valamint az ikrek bejövő és kimenő kapcsolatait mutatja." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> A Twin Graph az ikrek közötti kapcsolatok létrehozási fogalma. Ha meg szeretné tekinteni a Twin Graph vizualizációs ábrázolását, tekintse meg a jelen cikk [_Visualization *](how-to-manage-graph.md#visualization) című szakaszát. 

### <a name="create-a-twin-graph-from-a-spreadsheet"></a>Dupla gráf létrehozása egy táblázatból

Gyakorlati használati esetekben a különálló hierarchiák gyakran egy másik adatbázisban tárolt adatokból, vagy akár egy táblázatból is létrejönnek. Ez a szakasz azt szemlélteti, hogyan lehet elemezni a táblázatokat.

Vegye figyelembe a következő adattáblázatot, amely leírja a létrehozandó Digitális ikrek és kapcsolatok készletét.

| Modellazonosító| Dupla azonosító (egyedinek kell lennie) | Kapcsolat neve | Cél dupla azonosító | Kettős init-adathalmaz |
| --- | --- | --- | --- | --- |
| dtmi: példa: Floor; 1 | Floor1 |  contains | Room1 |{"Hőmérséklet": 80, "páratartalom": 60}
| dtmi: példa: Floor; 1 | Floor0 |  rendelkezik      | Room0 |{"Hőmérséklet": 70, "páratartalom": 30}
| dtmi: példa: Room; 1  | Room1 | 
| dtmi: példa: Room; 1  | Room0 |

A következő kód a [Microsoft Graph API](/graph/overview) -t használja a számolótáblák beolvasásához és az eredményekből származó Azure digitális Twins Twin gráf létrehozásához.

```csharp
var range = msftGraphClient.Me.Drive.Items["BuildingsWorkbook"].Workbook.Worksheets["Building"].usedRange;
JsonDocument data = JsonDocument.Parse(range.values);
List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
foreach (JsonElement row in data.RootElement.EnumerateArray())
{
    string modelId = row[0].GetString();
    string sourceId = row[1].GetString();
    string relName = row[2].GetString();
    string targetId = row[3].GetString();
    string initData = row[4].GetString();
    
    // Parse spreadsheet extra data into a JSON string to initialize the digital twin
    // Left out for compactness
    Dictionary<string, object> initData = new Dictionary<string, object>() { ... };

    if (sourceId != null)
    {
        BasicRelationship br = new BasicRelationship()
        {
            SourceId = sourceId,
            TargetId = targetId,
            Name = relName
        };
        RelationshipRecordList.Add(br);
    }

    BasicDigitalTwin twin = new BasicDigitalTwin();
    twin.CustomProperties = initData;
    // Set the type of twin to be created
    twin.Metadata = new DigitalTwinMetadata() { ModelId = modelId };
    
    try
    {
        await client.CreateDigitalTwinAsync(sourceId, JsonSerializer.Serialize<BasicDigitalTwin>(twin));
    }
    catch (RequestFailedException e)
    {
       Console.WriteLine($"Error {e.Status}: {e.Message}");
    }
    foreach (BasicRelationship rec in RelationshipRecordList)
    { 
        try { 
            await client.CreateRelationshipAsync(rec.sourceId, Guid.NewGuid().ToString(), JsonSerializer.Serialize<BasicRelationship>(rec));
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine($"Error {e.Status}: {e.Message}");
        }
    }
}
```
## <a name="manage-relationships-with-cli"></a>Kapcsolatok kezelése a CLI-vel

Az ikrek és kapcsolataik az Azure Digital Twins CLI használatával is kezelhetők. A parancsok a következő [*útmutatóban találhatók: az Azure digitális Twins parancssori*](how-to-use-cli.md)felületének használata.

## <a name="next-steps"></a>További lépések

Tudnivalók az Azure Digital Twins Twin Graph lekérdezéséről:
* [*Fogalmak: lekérdezési nyelv*](concepts-query-language.md)
* [*Útmutató: a Twin gráf lekérdezése*](how-to-query-graph.md)