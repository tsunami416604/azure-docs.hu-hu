---
title: Ikergráf kezelése kapcsolatokkal
titleSuffix: Azure Digital Twins
description: Megtudhatja, hogyan kezelheti a digitális ikrek gráfját úgy, hogy összekapcsolja őket a kapcsolatokkal.
author: baanders
ms.author: baanders
ms.date: 11/03/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 78e0bfb0af494ecae2865fcc42679b8fcce44916
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359578"
---
# <a name="manage-a-graph-of-digital-twins-using-relationships"></a>Digitális ikrek gráfjának kezelése kapcsolatok használatával

Az Azure digitális ikrek szíve a teljes környezetet jelképező [kettős gráf](concepts-twins-graph.md) . A Twin gráf a **kapcsolatokon** keresztül összekapcsolt digitális ikrekből tevődik össze. 

Miután egy működő [Azure digitális Twins-példánnyal](how-to-set-up-instance-portal.md) rendelkezik, és beállította a [hitelesítési](how-to-authenticate-client.md) kódot az ügyfélalkalmazás számára, a [**DigitalTwins API**](/rest/api/digital-twins/dataplane/twins) -kkal digitális ikreket és azok kapcsolatait is létrehozhatja, módosíthatja és törölheti egy Azure digitális Twins-példányban. Használhatja a [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)-t vagy az [Azure Digital Twins CLI](how-to-use-cli.md)-t is.

Ez a cikk a kapcsolatok és a gráf egészének kezelésére koncentrál. az egyes digitális ikrekkel való munkavégzéshez tekintse meg az [*útmutató: digitális ikrek kezelése*](how-to-manage-twin.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]
    
[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-relationships"></a>Kapcsolatok létrehozása

A kapcsolatok leírják, hogy a különböző digitális ikrek hogyan kapcsolódnak egymáshoz, ami a Twin gráf alapját képezi.

A kapcsolatok a hívás használatával jönnek létre `CreateOrReplaceRelationshipAsync()` . 

Kapcsolat létrehozásához meg kell adnia a következőket:
* A forrásként szolgáló Twin azonosító (az `srcId` alábbi kódrészletben): annak a Twin-azonosítónak a azonosítója, amelyből a kapcsolat származik.
* A cél dupla azonosító (az `targetId` alábbi kódrészletben): annak a Twin-azonosítónak a azonosítója, amelyben a kapcsolat megérkezik.
* Kapcsolat neve (az `relName` alábbi kódrészletben): a kapcsolat általános típusa, például a _tartalmaz_.
* Egy kapcsolat azonosítója (az `relId` alábbi mintakód esetében): a kapcsolat adott neve, például _Relationship1_.

A kapcsolat AZONOSÍTÓjának egyedinek kell lennie az adott forrás ikerén belül. Nem kell globálisan egyedinek lennie.
A Twin *foo* esetében például minden egyes kapcsolat azonosítójának egyedinek kell lennie. Egy másik dupla *sáv* azonban rendelkezhet olyan kimenő kapcsolattal, amely megegyezik egy *foo* -kapcsolat ugyanazzal az azonosítóval.

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
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
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

Ez azt jelenti, hogy egyszerre több különböző típusú kapcsolatot tud kifejezni két ikrek között. Például a *Twin A egy* *tárolt* *kapcsolattal és egy* *dupla B* -vel létesített kapcsolattal is rendelkezhet.

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
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
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

Az Azure Digital Twins is rendelkezik egy API-val, amely az adott Twin-hez tartozó összes *bejövő* * kapcsolatot megkeresi. Ez gyakran hasznos a fordított navigáláshoz, illetve a Twin törléséhez.

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
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");

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
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            
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
await DeleteRelationship(client, srcId, relId);
```
## <a name="create-a-twin-graph"></a>Dupla gráf létrehozása 

A következő futtatható a jelen cikk kapcsolati műveleteivel létrehoz egy dupla gráfot a digitális ikrekből és a kapcsolatokból.

### <a name="set-up-the-runnable-sample"></a>A futtatható minta beállítása

A kódrészlet a [*Room.json*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json) és a [*Floor.js*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) modell-definíciókat használja az [*oktatóanyag: az Azure Digital Twins és egy minta ügyfélalkalmazás megismerése*](tutorial-command-line-app.md). Ezekkel a hivatkozásokkal közvetlenül megtekintheti a fájlokat, vagy letöltheti őket a teljes végpontok közötti [minta projekt](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)részeként. 

A minta futtatása előtt tegye a következőket:
1. Töltse le a modell fájljait, helyezze el őket a projektbe, és cserélje le az `<path-to>` alábbi kódban található helyőrzőket, hogy megtudja, hol találják meg a programot.
2. Cserélje le a helyőrzőt az `<your-instance-hostname>` Azure Digital ikrek példányának állomásnévre.
3. Adja hozzá ezeket a csomagokat a projekthez:
    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity
    ```

Ha közvetlenül szeretné futtatni a mintát, helyi hitelesítő adatokat is be kell állítania. A következő szakasz végigvezeti a lépéseken.
[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](../../includes/digital-twins-local-credentials-outer.md)]

### <a name="run-the-sample"></a>Minta futtatása

A fenti lépések elvégzése után közvetlenül futtathatja a következő mintakód-kódot.

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

        public static async Task Main(string[] args)
        {
            Console.WriteLine("Hello World!");

            //Create the Azure Digital Twins client for API calls
            DigitalTwinsClient client = createDTClient();
            Console.WriteLine($"Service client created – ready to go");
            Console.WriteLine();

            //Upload models
            Console.WriteLine($"Upload models");
            Console.WriteLine();
            string dtdl = File.ReadAllText("<path-to>/Room.json");
            string dtdl1 = File.ReadAllText("<path-to>/Floor.json");
            var typeList = new List<string>();
            typeList.Add(dtdl);
            typeList.Add(dtdl1);
            // Upload the models to the service
            await client.CreateModelsAsync(typeList);

            //Create new (Floor) digital twin
            BasicDigitalTwin floorTwin = new BasicDigitalTwin();
            string srcId = "myFloorID";
            floorTwin.Metadata = new DigitalTwinMetadata();
            floorTwin.Metadata.ModelId = "dtmi:example:Floor;1";
            //Floor twins have no properties, so nothing to initialize
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(srcId, floorTwin);
            Console.WriteLine("Twin created successfully");

            //Create second (Room) digital twin
            BasicDigitalTwin roomTwin = new BasicDigitalTwin();
            string targetId = "myRoomID";
            roomTwin.Metadata = new DigitalTwinMetadata();
            roomTwin.Metadata.ModelId = "dtmi:example:Room;1";
            // Initialize properties
            Dictionary<string, object> props = new Dictionary<string, object>();
            props.Add("Temperature", 35.0);
            props.Add("Humidity", 55.0);
            roomTwin.Contents = props;
            //Create the twin
            await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(targetId, roomTwin);
            
            //Create relationship between them
            await CreateRelationship(client, srcId, targetId, "contains");
            Console.WriteLine();

            //Print twins and their relationships
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();

            //Delete the relationship
            Console.WriteLine("Deleting the relationship");
            await DeleteRelationship(client, srcId, $"{srcId}-contains->{targetId}");
            Console.WriteLine();

            //Print twins and their relationships again
            Console.WriteLine("--- Printing details:");
            Console.WriteLine("Outgoing relationships from source twin:");
            await FetchAndPrintTwinAsync(srcId, client);
            Console.WriteLine();
            Console.WriteLine("Incoming relationships to target twin:");
            await FetchAndPrintTwinAsync(targetId, client);
            Console.WriteLine("--------");
            Console.WriteLine();
        }

        private static DigitalTwinsClient createDTClient()
        {
            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
        }
        private async static Task CreateRelationship(DigitalTwinsClient client, string srcId, string targetId, string relName)
        {
            // Create relationship between twins
            var relationship = new BasicRelationship
            {
                TargetId = targetId,
                Name = relName
            };

            try
            {
                string relId = $"{srcId}-{relName}->{targetId}";
                await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(srcId, relId, relationship);
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
            Response<BasicDigitalTwin> res = client.GetDigitalTwin(twin_Id);
            await FindOutgoingRelationshipsAsync(client, twin_Id);
            await FindIncomingRelationshipsAsync(client, twin_Id);

            return;
        }

        private static async Task<List<BasicRelationship>> FindOutgoingRelationshipsAsync(DigitalTwinsClient client, string dtId)
        {
            // Find the relationships for the twin
            
            try
            {
                // GetRelationshipsAsync will throw if an error occurs
                AsyncPageable<BasicRelationship> rels = client.GetRelationshipsAsync<BasicRelationship>(dtId);
                List<BasicRelationship> results = new List<BasicRelationship>();
                await foreach (BasicRelationship rel in rels)
                {
                    results.Add(rel);
                    Console.WriteLine($"Found relationship-{rel.Name}->{rel.TargetId}");
                }

                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"*** Error {ex.Status}/{ex.ErrorCode} retrieving relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

        private static async Task<List<IncomingRelationship>> FindIncomingRelationshipsAsync(DigitalTwinsClient client, string dtId)
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
                    Console.WriteLine($"Found incoming relationship-{incomingRel.RelationshipId}");
                }
                return results;
            }
            catch (RequestFailedException ex)
            {
                Console.WriteLine($"**_ Error {ex.Status}/{ex.ErrorCode} retrieving incoming relationships for {dtId} due to {ex.Message}");
                return null;
            }
        }

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
    }
}
```

Itt látható a fenti program konzoljának kimenete: 

:::image type="content" source="./media/how-to-manage-graph/console-output-twin-graph.png" alt-text="Konzol kimenete, amely a Twin részleteit, valamint az ikrek bejövő és kimenő kapcsolatait mutatja." lightbox="./media/how-to-manage-graph/console-output-twin-graph.png":::

> [!TIP]
> A Twin Graph az ikrek közötti kapcsolatok létrehozási fogalma. Ha meg szeretné tekinteni a Twin Graph vizualizációs ábrázolását, tekintse meg a jelen cikk [_Visualization *](how-to-manage-graph.md#visualization) című szakaszát. 

### <a name="create-a-twin-graph-from-a-csv-file"></a>Dupla gráf létrehozása CSV-fájlból

Gyakorlati használati esetekben a különálló hierarchiák gyakran egy másik adatbázisban tárolt adatokból jönnek létre, vagy akár egy táblázatkezelőben vagy CSV-fájlban is. Ez a szakasz azt szemlélteti, hogyan lehet beolvasni egy CSV-fájl adatait, és létrehozni egy különálló gráfot.

Vegye figyelembe a következő adattáblázatot, amely leírja a digitális ikrek és a kapcsolatok készletét.

|  Modellazonosító    | Dupla azonosító (egyedinek kell lennie) | Kapcsolat neve  | Cél dupla azonosító  | Kettős init-adathalmaz |
| --- | --- | --- | --- | --- |
| dtmi: példa: Floor; 1    | Floor1 | contains | Room1 | |
| dtmi: példa: Floor; 1    | Floor0 | contains | Room0 | |
| dtmi: példa: Room; 1    | Room1 | | | {"Hőmérséklet": 80} |
| dtmi: példa: Room; 1    | Room0 | | | {"Hőmérséklet": 70} |

Az Azure digitális Twins-ba való beszerzésének egyik módja, ha átalakítja a táblázatot egy CSV-fájlba, és kódot ír a fájl értelmezéséhez az ikrek és kapcsolatok létrehozásához szükséges parancsokra. Az alábbi mintakód szemlélteti a CSV-fájlból származó adatok olvasását és az Azure digitális Twins-ban létrehozott Twin gráfok létrehozását.

Az alábbi kódban a CSV-fájl neve *data.csv* , és az Azure Digital Twins-példány **állomásneve** jelképező helyőrző. A minta számos olyan csomagot is használ, amelyeket hozzáadhat a projekthez a folyamat segítése érdekében.

```csharp
using System;
using System.Collections.Generic;
using System.Text.Json;
using System.Threading.Tasks;
using Azure;
using Azure.DigitalTwins.Core;
using Azure.Identity;

namespace creating_twin_graph_from_csv
{
    class Program
    {
        static async Task Main(string[] args)
        {
            List<BasicRelationship> RelationshipRecordList = new List<BasicRelationship>();
            List<BasicDigitalTwin> TwinList = new List<BasicDigitalTwin>();
            List<List<string>> data = ReadData();
            DigitalTwinsClient client = createDTClient();

            // Interpret the CSV file data, by each row
            foreach (List<string> row in data)
            {
                string modelID = row.Count > 0 ? row[0].Trim() : null;
                string srcID = row.Count > 1 ? row[1].Trim() : null;
                string relName = row.Count > 2 ? row[2].Trim() : null;
                string targetID = row.Count > 3 ? row[3].Trim() : null;
                string initProperties = row.Count > 4 ? row[4].Trim() : null;
                Console.WriteLine($"ModelID: {modelID}, TwinID: {srcID}, RelName: {relName}, TargetID: {targetID}, InitData: {initProperties}");
                Dictionary<string, object> props = new Dictionary<string, object>();
                // Parse properties into dictionary (left out for compactness)
                // ...

                // Null check for source and target ID's
                if (srcID != null && srcID.Length > 0 && targetID != null && targetID.Length > 0)
                {
                    BasicRelationship br = new BasicRelationship()
                    {
                        SourceId = srcID,
                        TargetId = targetID,
                        Name = relName
                    };
                    RelationshipRecordList.Add(br);
                }
                BasicDigitalTwin srcTwin = new BasicDigitalTwin();
                srcTwin.Id = srcID;
                srcTwin.Metadata = new DigitalTwinMetadata();
                srcTwin.Metadata.ModelId = modelID;
                srcTwin.Contents = props;
                TwinList.Add(srcTwin);
            }

            // Create digital twins 
            foreach (BasicDigitalTwin twin in TwinList)
            {
                try
                {
                    await client.CreateOrReplaceDigitalTwinAsync<BasicDigitalTwin>(twin.Id, twin);
                    Console.WriteLine("Twin is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
            // Create relationships between the twins
            foreach (BasicRelationship rec in RelationshipRecordList)
            {
                try
                {
                    string relId = $"{rec.SourceId}-{rec.Name}->{rec.TargetId}";
                    await client.CreateOrReplaceRelationshipAsync<BasicRelationship>(rec.SourceId, relId, rec);
                    Console.WriteLine("Relationship is created");
                }
                catch (RequestFailedException e)
                {
                    Console.WriteLine($"Error {e.Status}: {e.Message}");
                }
            }
        }

        // Method to ingest data from the CSV file
        public static List<List<string>> ReadData()
        {
            string path = "<path-to>/data.csv";
            string[] lines = System.IO.File.ReadAllLines(path);
            List<List<string>> data = new List<List<string>>();
            int count = 0;
            foreach (string line in lines)
            {
                if (count++ == 0)
                    continue;
                List<string> cols = new List<string>();
                data.Add(cols);
                string[] columns = line.Split(',');
                foreach (string column in columns)
                {
                    cols.Add(column);
                }
            }
            return data;
        }
        // Method to create the digital twins client
        private static DigitalTwinsClient createDTClient()
        {

            string adtInstanceUrl = "https://<your-instance-hostname>";
            var credentials = new DefaultAzureCredential();
            DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
            return client;
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