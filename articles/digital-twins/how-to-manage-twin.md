---
title: Digitális ikereszközök kezelése
titleSuffix: Azure Digital Twins
description: 'Lásd: egyéni ikrek és kapcsolatok lekérése, frissítése és törlése.'
author: baanders
ms.author: baanders
ms.date: 4/10/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e37c680f6bf9e296230232c0d4e0fab5f50ad3cd
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142380"
---
# <a name="manage-digital-twins"></a>Digitális ikereszközök kezelése

A környezetben található entitásokat a [digitális ikrek](concepts-twins-graph.md)jelölik. A digitális ikrek kezelése magában foglalhatja a létrehozását, módosítását és eltávolítását. Ezeknek a műveleteknek a végrehajtásához használhatja a [**DigitalTwins API-kat**](how-to-use-apis-sdks.md), a [.net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)-t vagy az [Azure digitális Twins parancssori](how-to-use-cli.md)felületét.

Ez a cikk a digitális ikrek kezelésére koncentrál. Ha a kapcsolatokkal és a [Twin gráfmal](concepts-twins-graph.md) szeretne dolgozni, tekintse meg [az útmutató: a Twin gráf kezelése a kapcsolatokkal](how-to-manage-graph.md)című témakört.

> [!TIP]
> Minden SDK-függvény szinkron és aszinkron verzióban érhető el.

## <a name="create-a-digital-twin"></a>Digitális Twin létrehozása

A kettős létrehozásához használja a `CreateDigitalTwin` metódust a szolgáltatás ügyfélen, például:

```csharp
await client.CreateDigitalTwinAsync("myNewTwinID", initData);
```

Digitális dupla létrehozásához a következőket kell megadnia:
* A digitális iker kívánt azonosítója
* A használni kívánt [modell](concepts-models.md) 

Opcionálisan megadhatja a digitális iker összes tulajdonságának kezdeti értékeit. 

> [!TIP]
> Csak a legalább egyszer beállított tulajdonságokat adja vissza a rendszer, amikor a GetDigitalTwin-t egy Twin értékkel tölti le.  

A modell és a kezdeti tulajdonságértékek a paraméteren keresztül érhetők el `initData` , amely egy JSON-karakterlánc, amely tartalmazza a megfelelő adatokat.

### <a name="initialize-properties"></a>Tulajdonságok inicializálása

A Twin Creation API olyan objektumot fogad el, amely a Twin tulajdonságok érvényes JSON-leírásában szerializálható. Lásd a következő [fogalmakat: digitális ikrek és a Twin gráf](concepts-twins-graph.md) a Twin-fájl JSON-formátumának leírásához.

A paramétereket manuálisan vagy egy megadott segítő osztály használatával is létrehozhatja. Íme egy példa.

#### <a name="create-twins-using-manually-created-data"></a>Ikrek létrehozása manuálisan létrehozott adatai alapján

```csharp
// Define the model type for the twin to be created
Dictionary<string, object> meta = new Dictionary<string, object>()
{
    { "$model", "dtmi:com:contoso:Room;1" }
};
// Initialize the twin properties
Dictionary<string, object> twin = new Dictionary<string, object>()
{
    { "$metadata", meta },
    { "Temperature", temperature},
    { "Humidity", humidity},
};
client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<Dictionary<string, object>>(twin));
```

#### <a name="create-twins-with-the-helper-class"></a>Ikrek létrehozása a segítő osztállyal

```csharp
BasicDigitalTwin twin = new BasicDigitalTwin();
twin.Metadata = new DigitalTwinMetadata();
twin.Metadata.ModelId = "dtmi:example:Room;1";
// Initialize properties
Dictionary<string, object> props = new Dictionary<string, object>();
props.Add("Temperature", 25.0);
props.Add("Humidity", 50.0);
twin.CustomProperties = props;

client.CreateDigitalTwin("myNewRoomID", JsonSerializer.Serialize<BasicDigitalTwin>(twin));
```

## <a name="get-data-for-a-digital-twin"></a>Digitális Twin-adatlekérdezés

Bármely digitális Twin teljes adatát elérheti az alábbiak meghívásával:

```csharp
object result = await client.GetDigitalTwin(id);
```

Ez a hívás a Twin-adatok JSON-karakterláncként való visszaadása. 

Ha több Twins egyetlen API-hívással szeretne beolvasni, tekintse meg a következő témakörben található lekérdezési API-példákat [: a Twin Graph lekérdezése](how-to-query-graph.md).

Vegye figyelembe a következő modellt (a [digitális Twins Definition Language (DTDL) nyelven](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL)írt), amely a *holdat*határozza meg:

```json
{
    "@id": " dtmi:com:contoso:Moon;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "contents": [
        {
            "@type": "Property",
            "name": "radius",
            "schema": "double",
            "writable": true
        },
        {
            "@type": "Property",
            "name": "mass",
            "schema": "double",
            "writable": true
        }
    ]
}
```

A `object result = await client.DigitalTwins.GetByIdAsync("my-moon");` *Hold*típusú dupla hívás eredménye a következőképpen néz ki:

```json
{
  "$dtId": "myMoon-001",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "radius": 1737.1,
  "mass": 0.0734,
  "$metadata": {
    "$model": "dtmi:com:contoso:Moon;1",
    "radius": {
      "desiredValue": 1737.1,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "mass": {
      "desiredValue": 0.0734,
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

A digitális iker definiált tulajdonságai a digitális iker felső szintű tulajdonságaiként lesznek visszaadva. A DTDL-definíciónak nem részét képező metaadatokat vagy rendszeradatokat előtaggal adja vissza a rendszer `$` . A metaadatok tulajdonságai a következők:
* Az Azure Digital Twins-példányban lévő digitális iker azonosítója, mint `$dtId` .
* `$etag`, a webkiszolgáló által hozzárendelt szabványos HTTP-mező
* További tulajdonságok egy `$metadata` szakaszban. Ezek a következők:
    - A digitális iker modell DTMI.
    - Az egyes írható tulajdonságok szinkronizálási állapota. Ez a leghasznosabb az eszközök esetében, ahol lehetséges, hogy a szolgáltatás és az eszköz eltérő állapotú (például ha egy eszköz offline állapotban van). Ez a tulajdonság jelenleg csak IoT Hubhoz csatlakoztatott fizikai eszközökre vonatkozik. A metaadatok szakaszban található adatokkal megismerheti a tulajdonságok teljes állapotát, valamint az utolsó módosítás időbélyegét is. A szinkronizálási állapottal kapcsolatos további információkért tekintse meg [ezt az IoT hub oktatóanyagot](../iot-hub/tutorial-device-twins.md) az eszköz állapotának szinkronizálásához.
    - Szolgáltatás-specifikus metaadatok, például IoT Hub vagy Azure digitális Twins. 

A visszaadott JSON-t elemezheti a Twin paranccsal egy tetszőleges JSON-elemzési kódtár használatával, például: `System.Text.Json` .

Használhatja az SDK részét képező szerializálási segítő osztályt is `BasicDigitalTwin` , amely az alapszintű Twin metaadatokat és tulajdonságokat az előre elemzett űrlapon fogja visszaadni. Például:

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

További információ a szerializálási segítő osztályokról [: az Azure Digital Twins API-k és SDK-k használata](how-to-use-apis-sdks.md).

## <a name="update-a-digital-twin"></a>Digitális iker frissítése

A digitális Twin tulajdonságok frissítéséhez írja be a cserélni kívánt adatokat a [JSON-javítás](http://jsonpatch.com/) formátumában. Így egyszerre több tulajdonságot is lecserélhet. Ezután átadja a JSON-javítási dokumentumot a következő `Update` metódusnak:

`await client.UpdateDigitalTwin(id, patch);`.

Íme egy példa a JSON-javítási kódra. Ez a dokumentum helyettesíti a digitális Twin-fájl *tömeg* -és *RADIUS-* tulajdonságának értékét.

```json
[
  {
    "op": "replace",
    "path": "/mass",
    "value": 0.0799
  },
  {
    "op": "replace",
    "path": "/radius",
    "value": 0.800
  }
]
```

Az [SDK](how-to-use-apis-sdks.md)-ban manuálisan is létrehozhatja a javításokat vagy a szerializálási segítő osztály használatával. Íme egy példa.

#### <a name="create-patches-manually"></a>Javítások manuális létrehozása
```csharp
List<object> twinData = new List<object>();
twinData.Add(new Dictionary<string, object>() {
    { "op", "add"},
    { "path", "/Temperature"},
    { "value", 25.0}
});

await client.UpdateDigitalTwinAsync(twinId, JsonConvert.SerializeObject(twinData));
```

#### <a name="create-patches-using-the-helper-class"></a>Javítások létrehozása a segítő osztály használatával

```csharp
UpdateOperationsUtility uou = new UpdateOperationsUtility();
uou.AppendAddOp("/Temperature", 25.0);
await client.UpdateDigitalTwinAsync(twinId, uou.Serialize());
```

### <a name="update-properties-in-digital-twin-components"></a>Tulajdonságok frissítése a digitális dupla összetevőkben

Ne felejtse el, hogy egy modell tartalmazhat összetevőket, így más modellekből is készíthető. 

A digitális Twin összetevőkben lévő tulajdonságok javításához elérésiút-szintaxist kell használni a JSON-javításban:

```json
[
  {
    "op": "replace",
    "path": "/mycomponentname/mass",
    "value": 0.0799
  }
]
```

### <a name="update-a-digital-twins-model"></a>Digitális Twin-modell frissítése

A `Update` függvény a digitális Twin-et egy másik modellre is áttelepítheti. 

Vegyük például a következő JSON-javítási dokumentumot, amely a digitális Twin metaadatok `$model` mezőjét helyettesíti:

```json
[
  {
    "op": "replace",
    "path": "/$metadata/$model",
    "value": "dtmi:com:contoso:foo;1"
  }
]
```

Ez a művelet csak akkor lesz sikeres, ha a javítás által módosított digitális kettős modell megfelel az új modellel. 

Tekintse meg a következő példát:
1. Képzelje el, hogy egy digitális Twin *foo_old*modellel rendelkezik. *foo_old* meghatározza a szükséges tulajdonság *tömegét*.
2. Az új modell *foo_new* meghatározza a tulajdonság tömegét, és hozzáadja az új szükséges tulajdonság *hőmérsékletét*.
3. A javítás után a digitális ikernek mind a Mass, mind a hőmérséklet tulajdonsággal rendelkeznie kell. 

Ennek a helyzetnek a javításához frissítenie kell a modellt és a Twin 's hőmérséklet tulajdonságot is, például:

```json
[
  {
    "op": "replace",
    "path": "$metadata.$model",
    "value": "dtmi:com:contoso:foo_new"
  },
  {
    "op": "add",
    "path": "temperature",
    "value": 60
  }
]
```

## <a name="delete-a-digital-twin"></a>Digitális Twin törlése

Az ikreket a használatával törölheti `DeleteDigitalTwin(ID)` . Azonban csak akkor törölhet egy IKeret, ha nincs több kapcsolata. Először törölnie kell az összes kapcsolatot. 

Íme egy példa erre a kódra:

```csharp
static async Task DeleteTwin(string id)
{
    await FindAndDeleteOutgoingRelationshipsAsync(id);
    await FindAndDeleteIncomingRelationshipsAsync(id);
    try
    {
        await client.DeleteDigitalTwin(id);
    } catch (RequestFailedException exc)
    {
        Console.WriteLine($"*** Error:{exc.Error}/{exc.Message}");
    }
}

public async Task FindAndDeleteOutgoingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipsAsync will throw an error if a problem occurs
        AsyncPageable<string> relsJson = client.GetRelationshipsAsync(dtId);

        await foreach (string relJson in relsJson)
        {
            var rel = System.Text.Json.JsonSerializer.Deserialize<BasicRelationship>(relJson);
            await client.DeleteRelationshipAsync(dtId, rel.Id).ConfigureAwait(false);
            Log.Ok($"Deleted relationship {rel.Id} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting relationships for {dtId} due to {ex.Message}");
    }
}

async Task FindAndDeleteIncomingRelationshipsAsync(string dtId)
{
    // Find the relationships for the twin

    try
    {
        // GetRelationshipssAsync will throw an error if a problem occurs
        AsyncPageable<IncomingRelationship> incomingRels = client.GetIncomingRelationshipsAsync(dtId);

        await foreach (IncomingRelationship incomingRel in incomingRels)
        {
            await client.DeleteRelationshipAsync(incomingRel.SourceId, incomingRel.RelationshipId).ConfigureAwait(false);
            Log.Ok($"Deleted incoming relationship {incomingRel.RelationshipId} from {dtId}");
        }
    }
    catch (RequestFailedException ex)
    {
        Log.Error($"*** Error {ex.Status}/{ex.ErrorCode} retrieving or deleting incoming relationships for {dtId} due to {ex.Message}");
    }
}
```

### <a name="delete-all-digital-twins"></a>Az összes digitális ikrek törlése

Az összes ikrek egyszerre történő törléséről például töltse le az oktatóanyagban használt minta alkalmazást [: Fedezze fel az alapokat egy minta ügyfélalkalmazás](tutorial-command-line-app.md)használatával. A *CommandLoop.cs* fájl ezt egy `CommandDeleteAllTwins` függvényben végzi el.

## <a name="manage-twins-with-cli"></a>Ikrek kezelése a CLI-vel

Az ikrek az Azure Digital Twins CLI használatával is kezelhetők. A parancsok a következő [útmutatóban találhatók: az Azure digitális Twins parancssori](how-to-use-cli.md)felületének használata.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan hozhat létre és kezelhet kapcsolatokat a digitális ikrek között:
* [Útmutató: a Twin gráf kezelése kapcsolatok használatával](how-to-manage-graph.md)