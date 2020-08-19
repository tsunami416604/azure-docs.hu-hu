---
title: Az Azure Digital Twins API-k és SDK-k használata
titleSuffix: Azure Digital Twins
description: Ismerje meg, hogyan dolgozhat az Azure Digital Twins API-kkal, beleértve az SDK-t is.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 885394b2dd50b9f8a94ece409c47609c8f7f18fd
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587562"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>Az Azure Digital Twins API-k és SDK-k használata

Az Azure digitális Twins a **vezérlési sík API** -kkal és az **adatsík API** -kkal is rendelkezik a példány és az elemek kezeléséhez. Ez a cikk áttekintést nyújt az elérhető API-król, valamint a velük való interakció módszereiről. Használhatja a REST API-kat közvetlenül a hozzájuk kapcsolódó feladatokkal vagy egy SDK-val.

## <a name="overview-control-plane-apis"></a>Áttekintés: felügyeleti sík API-k

A vezérlési sík API-k az Azure Digital Twins-példányok egészének kezelésére szolgálnak, így olyan műveletekre is kiterjednek, mint például a teljes példány létrehozása vagy törlése. Ezeket a végpontok létrehozásához és törléséhez is használni fogja.

A nyilvános előzetes verzióhoz tartozó legújabb Control Plant API _**-verzió a 2020-03-01 előzetes**_ verzió.

A vezérlő sík API-k használata:
* Az API-kat közvetlenül az új [hencegő mappára](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins)hivatkozva hívhatja meg. Ez a tárház a használatot bemutató példákat is tartalmaz.
* Jelenleg a vezérlési API-k számára érhető el az SDK-ban...
  - [.Net (C#)](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/1.0.0-preview.1) ([forrás](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins)) ([hivatkozás [automatikusan generált]](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet-preview))
  - [Java](https://search.maven.org/artifact/com.microsoft.azure.digitaltwins.v2020_03_01_preview/azure-mgmt-digitaltwins) ([forrás](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins)) ([hivatkozás [automatikusan generált]](https://docs.microsoft.com/java/api/overview/azure/digitaltwins/management?view=azure-java-preview))
  - [JavaScript](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([forrás](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [Python](https://pypi.org/project/azure-mgmt-digitaltwins/) ([forrás](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [Go-Source](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/digitaltwins/mgmt/2020-03-01-preview/digitaltwins)

A vezérlési sík API-kat a [Azure Portal](https://portal.azure.com) és a [CLI](how-to-use-cli.md)használatával is használhatja az Azure digitális ikrekkel való interakcióval.

## <a name="overview-data-plane-apis"></a>Áttekintés: adatsík API-k

Az adatsík API-k az Azure Digital Twins-példányon belüli elemek kezelésére szolgálnak. Olyan műveleteket tartalmaznak, mint például útvonalak létrehozása, modellek feltöltése, kapcsolatok létrehozása és az ikrek kezelése. A következő kategóriákba sorolhatók:
* **DigitalTwinsModels** – a DigitalTwinsModels kategória API-kat tartalmaz az Azure Digital Twins-példány [modelljeinek](concepts-models.md) kezeléséhez. A felügyeleti tevékenységek közé tartozik a DTDL-ben létrehozott modellek feltöltése, ellenőrzése, beolvasása és törlése.
* **DigitalTwins** – a DigitalTwins kategória tartalmazza azokat az API-kat, amelyek segítségével a fejlesztők [digitális ikreket](concepts-twins-graph.md) és kapcsolataikat hozhatnak létre, módosíthatnak és törölhetnek egy Azure digitális Twins-példányban.
* **Lekérdezés** – a lekérdezési kategória lehetővé teszi a fejlesztők számára [, hogy a kettős gráfban a kapcsolatok között megtalálják a digitális ikrek készleteit](how-to-query-graph.md) .
* **EventRoutes** – a EventRoutes kategória olyan API-kat tartalmaz, amelyek az [adattovábbítást](concepts-route-events.md)a rendszeren és az alsóbb rétegbeli szolgáltatásokon keresztül irányítják.

A nyilvános előzetes verzióhoz tartozó legfrissebb adatsík API-verzió a _**2020-05-31 előzetes**_ verzió. Az adatsík műveletek _2020-03-01 – előzetes_ verzió API-verziója már elavult.

Az adatsík API-k használata:
* Az API-kat közvetlenül, a... alapján hívhatja le.
   - a legújabb [hencegő mappára](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins)hivatkozik. Ez a tárház a használatot bemutató példákat is tartalmaz. 
   - az [API-referenciák dokumentációjának](https://docs.microsoft.com/rest/api/azure-digitaltwins/)megtekintése.
* Használhatja a .NET (C#) SDK-t. Jelenleg ez az egyetlen közzétett SDK az API-kkal való interakcióhoz. A .NET SDK használata...
   - a csomagot a következő NuGet tekintheti meg: [Azure. DigitalTwins. Core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - megtalálhatja az SDK-forrást, beleértve a példákat tartalmazó mappát is a GitHubban: az [Azure IoT Digital Twins ügyféloldali kódtára a .net-hez](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - megtekintheti az [SDK-referenciák dokumentációját](https://docs.microsoft.com/dotnet/api/overview/azure/digitaltwins?view=azure-dotnet-preview).
   - a részletes információkat és használati példákat a jelen cikk [.net (C#) SDK (adatsíkja)](#net-c-sdk-data-plane) című szakaszában tekintheti meg.
* Az autorest használatával egy másik nyelvhez is létrehozhat SDK-t. Kövesse az útmutató [*: egyéni SDK-k létrehozása az Azure digitális ikrekhez az autorest használatával*](how-to-create-custom-sdks.md)című témakör utasításait.

A Date Plant API-kat a [CLI](how-to-use-cli.md)használatával is használhatja az Azure digitális ikrekkel.

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (adatsík)

Az Azure Digital Twins .NET (C#) SDK része a .NET-hez készült Azure SDK-nak. Nyílt forráskódú, és az Azure Digital Twins adatsíkja API-jai alapján érhető el.

> [!NOTE]
> Az SDK kialakításával kapcsolatos részletes információkért tekintse meg az Azure SDK-k általános [tervezési alapelveit](https://azure.github.io/azure-sdk/general_introduction.html) és az adott [.net tervezési irányelveket](https://azure.github.io/azure-sdk/dotnet_introduction.html).

Az SDK használatához foglalja bele az **Azure. DigitalTwins. Core** NuGet-csomagot a projektbe. Szüksége lesz az **Azure. Identity** csomagra is (1.1.1-es verzió).

* A Visual Studióban hozzáadhat csomagokat a NuGet Package Managerrel (az eszközökön keresztül érhető el *> NuGet csomagkezelő > NuGet-csomagok kezelése a megoldáshoz*). 
* A .NET parancssori eszköz használatával a következőket végezheti el:

    ```cmd/sh
    dotnet add package Azure.DigitalTwins.Core --version 1.0.0-preview.3
    dotnet add package Azure.identity --version 1.1.1
    ```

Az API-k gyakorlatban való használatának részletes ismertetését az [*oktatóanyag: az ügyfélalkalmazás kódolása*](tutorial-code.md)című témakörben tekintheti meg. 

### <a name="net-sdk-usage-examples"></a>Példák a .NET SDK használatára

Íme néhány kód, amely a .NET SDK használatát szemlélteti.

Hitelesítés a szolgáltatással:

```csharp
// Authenticate against the service and create a client
var credentials = new InteractiveBrowserCredential(tenantId, clientId);
DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credentials);
```

Modell és lista modelljeinek feltöltése:

```csharp
// Upload a model
var typeList = new List<string>();
string dtdl = File.ReadAllText("SampleModel.json");
typeList.Add(dtdl);
try {
    await client.CreateModelsAsync(typeList);
} catch (RequestFailedException rex) {
    Console.WriteLine($"Load model: {rex.Status}:{rex.Message}");
}
// Read a list of models back from the service
AsyncPageable<ModelData> modelDataList = client.GetModelsAsync();
await foreach (ModelData md in modelDataList)
{
    Console.WriteLine($"Type name: {md.DisplayName}: {md.Id}");
}
```

Ikrek létrehozása és lekérdezése:

```csharp
// Initialize twin metadata
BasicDigitalTwin twinData = new BasicDigitalTwin();

twinData.Id = $"firstTwin";
twinData.Metadata.ModelId = "dtmi:com:contoso:SampleModel;1";
twinData.CustomProperties.Add("data", "Hello World!");
try {
    await client.CreateDigitalTwinAsync("firstTwin", JsonSerializer.Serialize(twinData));
} catch(RequestFailedException rex) {
    Console.WriteLine($"Create twin error: {rex.Status}:{rex.Message}");  
}
 
// Run a query    
AsyncPageable<string> result = client.QueryAsync("Select * From DigitalTwins");
await foreach (string twin in result)
{
    // Use JSON deserialization to pretty-print
    object jsonObj = JsonSerializer.Deserialize<object>(twin);
    string prettyTwin = JsonSerializer.Serialize(jsonObj, new JsonSerializerOptions { WriteIndented = true });
    Console.WriteLine(prettyTwin);
    // Or use BasicDigitalTwin for convenient property access
    BasicDigitalTwin btwin = JsonSerializer.Deserialize<BasicDigitalTwin>(twin);
}
```

Tekintse meg a következő [*oktatóanyagot: az ügyfélalkalmazás kódjának*](tutorial-code.md) bejárására szolgáló alkalmazás. 

További mintákat is talál a GitHub-tárházban a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)-hoz.

#### <a name="serialization-helpers"></a>Szerializálási segítők

A korábban leírtaknak megfelelően az alapszintű SDK-metódusok a Twin-et JSON-ként adják vissza. Az SDK azonban a szerializálás segítő osztályait is tartalmazza. Ezek a segítő függvények lehetővé teszik a Twin adatok gyors létrehozását vagy deszerializálását az alapvető információk eléréséhez.

Az elérhető segítő osztályok a következők:
* `BasicDigitalTwin`: A digitális iker alapértékeit jelöli
* `BasicRelationship`: A kapcsolat alapvető adatmennyiségét jelöli.
* `UpdateOperationUtility`: A frissítési hívásokban használt JSON-javítások adatait jelöli.
* `WriteableProperty`: A tulajdonság metaadatait jelöli

##### <a name="deserialize-a-digital-twin"></a>Digitális Twin deszerializálása

A Twin-adatait bármikor deszerializálhatja a választott JSON-kódtár használatával, például `System.Test.Json` vagy `Newtonsoft.Json` . A Twin-hez való alapszintű hozzáféréshez a segítő osztályok egy kicsit kényelmesebben teszik ezt meg.

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
```

A `BasicDigitalTwin` Helper osztály emellett hozzáférést biztosít a Twin-en, a-on keresztül meghatározott tulajdonságokhoz `Dictionary<string, object>` . A Twin tulajdonságok listázásához a következőket használhatja:

```csharp
Response<string> res = client.GetDigitalTwin(twin_id);
BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(res.Value);
Console.WriteLine($"Model id: {twin.Metadata.ModelId}");
foreach (string prop in twin.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-digital-twin"></a>Digitális Twin létrehozása

A `BasicDigitalTwin` osztály használatával előkészítheti az adatkészletet egy kettős példány létrehozásához:

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

A fenti kód a következő "kézi" változattal egyenértékű:

```csharp
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:example:Room;1"}
};
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", 25.0 }
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

##### <a name="deserialize-a-relationship"></a>Kapcsolat deszerializálása

A kapcsolati adatait bármikor deszerializálhatja az Ön által választott JSON-kódtár használatával, például `System.Test.Json` vagy `Newtonsoft.Json` . A kapcsolat alapszintű eléréséhez a segítő osztályok sokkal kényelmesebben teszik ezt meg.

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
```

A `BasicRelationship` segítő osztály a kapcsolaton definiált tulajdonságokhoz is hozzáférést biztosít a használatával `Dictionary<string, object>` . A tulajdonságok listázásához a következőket használhatja:

```csharp
Response<string> res = client.GetRelationship(twin_id, rel_id);
BasicRelationship rel = JsonSerializer.Deserialize<BasicRelationship>(res.Value);
Console.WriteLine($"Relationship Name: {rel.Name}");
foreach (string prop in rel.CustomProperties.Keys)
{
    if (twin.CustomProperties.TryGetValue(prop, out object value))
        Console.WriteLine($"Property '{prop}': {value}");
}
```

##### <a name="create-a-relationship"></a>Kapcsolat létrehozása

A (z) osztály használatával létrehozhatók `BasicDigitalTwin` olyan kapcsolatok, amelyekkel kapcsolat hozható létre egy kettős példányon:

```csharp
BasicRelationship rel = new BasicRelationship();
rel.TargetId = "myTargetTwin";
rel.Name = "contains"; // a relationship with this name must be defined in the model
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("active", true);
rel.CustomProperties = props;
client.CreateRelationship("mySourceTwin", "rel001", JsonSerializer.Serialize<BasicRelationship>(rel));
```

##### <a name="create-a-patch-for-twin-update"></a>Javítás készítése a Twin Update szolgáltatáshoz

Az ikrek és a kapcsolatok frissítési hívásainak használata [JSON-javítási](http://jsonpatch.com/) struktúrát használ. A JSON-javítási műveletek listáját a `UpdateOperationsUtility` lent látható módon használhatja.

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
uou.AppendAddOp("/myComponent/Property", "Hello");
// Un-set a property
uou.AppendRemoveOp("/Humidity");
client.UpdateDigitalTwin("myTwin", uou.Serialize());
```

## <a name="general-apisdk-usage-notes"></a>Általános API-/SDK-használati megjegyzések

> [!NOTE]
> Vegye figyelembe, hogy az előzetes verzióban az Azure Digital Twins nem támogatja a **több eredetű erőforrás-megosztást (CORS)**. Ennek eredményeképpen, ha egy böngésző-alkalmazásból, egy [API Management (APIM)](../api-management/api-management-key-concepts.md) vagy egy [Power apps](https://docs.microsoft.com/powerapps/powerapps-overview) -összekötőből REST API hív meg, akkor a rendszer házirend-hibát észlelt.
> A hiba elhárításához a következők egyikét teheti:
> * A CORS fejlécének leszalaga `Access-Control-Allow-Origin` az üzenetből. Ez a fejléc azt jelzi, hogy a válasz megosztható-e. 
> * Alternatív megoldásként hozzon létre egy CORS-proxyt, és tegye elérhetővé az Azure digitális Twins REST API kérését. 

Az alábbi lista további részleteket és általános irányelveket tartalmaz az API-k és SDK-k használatához.

* Az SDK használatához hozza létre az `DigitalTwinsClient` osztályt. A konstruktorhoz olyan hitelesítő adatok szükségesek, amelyek különböző hitelesítési módszerekkel szerezhetők be a `Azure.Identity` csomagban. További `Azure.Identity` információ: a [névtér dokumentációja](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet). 
* Hasznos lehet az `InteractiveBrowserCredential` első lépések során, de több más lehetőség is van, beleértve a [felügyelt identitáshoz](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)tartozó hitelesítő adatokat is, amelyeket valószínűleg az MSI-vel az Azure Digital Twins szolgáltatásban [beállított Azure-függvények](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet) hitelesítésére fog használni. További információ `InteractiveBrowserCredential` : az [osztály dokumentációja](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet).
* Az összes szolgáltatás API-hívása tag-függvényként van kitéve az `DigitalTwinsClient` osztályban.
* Az összes szolgáltatási funkció szinkron és aszinkron verziókban található.
* Az összes szolgáltatási funkció kivételt jelez a 400-es vagy újabb visszaküldési állapot esetén. Ügyeljen arra, hogy a hívásokat egy `try` szakaszba csomagolja, és legalább a fogást `RequestFailedExceptions` . Az ilyen típusú kivételekről [itt](https://docs.microsoft.com/dotnet/api/azure.requestfailedexception?view=azure-dotnet)talál további információt.
* A legtöbb szolgáltatási módszer visszaadja `Response<T>` vagy (az `Task<Response<T>>` aszinkron hívások esetében), ahol a a `T` szolgáltatás hívásához tartozó visszatérési objektum osztálya. A [`Response`](https://docs.microsoft.com/dotnet/api/azure.response-1?view=azure-dotnet) osztály beágyazza a szolgáltatás visszaadását, és megadja a visszatérési értékeket a `Value` mezőben.  
* A lapozható eredményekkel `Pageable<T>` vagy eredményekkel rendelkező szolgáltatási metódusok `AsyncPageable<T>` . További információ az `Pageable<T>` osztályról: itt [here](https://docs.microsoft.com/dotnet/api/azure.pageable-1?view=azure-dotnet-preview)talál további információt `AsyncPageable<T>` . [here](https://docs.microsoft.com/dotnet/api/azure.asyncpageable-1?view=azure-dotnet-preview)
* A lapozható eredmények egy hurok használatával is megadhatók `await foreach` . További információ erről a folyamatról: [.](https://docs.microsoft.com/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* A mögöttes SDK a `Azure.Core` . Az SDK-infrastruktúrával és-típusokkal kapcsolatos tudnivalókat az [Azure névtér dokumentációjában](https://docs.microsoft.com/dotnet/api/azure?view=azure-dotnet-preview) találja.

A szolgáltatási metódusok nagy mértékben gépelt objektumokat adnak vissza, ahol lehetséges. Mivel azonban az Azure Digital Twins a felhasználó által a futásidőben konfigurált modelleken alapul (a szolgáltatásba feltöltött DTDL-modelleken keresztül), számos szolgáltatás-API-t fogad el és ad vissza a két, JSON formátumú formában.

## <a name="monitor-api-metrics"></a>API-metrikák figyelése

Az API-metrikák, például a kérelmek, a késések és a hibák aránya a [Azure Portal](https://portal.azure.com/)tekinthető meg. 

A portál kezdőlapján keresse meg az Azure Digital Twins-példányát, és adja meg a részleteit. Válassza ki a **mérőszámok** lehetőséget az Azure Digital Twins-példány menüjében a *metrikák* lap megjelenítéséhez.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Képernyőfelvétel az Azure Digital Twins metrikáinak oldaláról":::

Itt megtekintheti a példány metrikáit, és létrehozhat egyéni nézeteket is.

## <a name="next-steps"></a>További lépések

Tekintse meg, hogyan használható az API-k egy Azure digitális Twins-példány és-hitelesítés beállításához:
* [*Útmutató: példány és hitelesítés beállítása*](how-to-set-up-instance-scripted.md)

Vagy járjon végig egy ügyfélalkalmazás létrehozásához szükséges lépéseken, például a következő útmutatóban használt módon:
* [*Oktatóanyag: ügyfélalkalmazás kódolása*](tutorial-code.md)
