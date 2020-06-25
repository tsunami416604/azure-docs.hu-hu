---
title: Digitális ikermodellek és az ikergráf
titleSuffix: Azure Digital Twins
description: Ismerje meg a digitális Twin fogalmát, és azt, hogy a kapcsolataik hogyan használják a diagramot.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 79035a2fa2f7f3bed4890faa749661f025cea312
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85362866"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>A digitális ikrek és a Twin Graph ismertetése

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Egy Azure digitális Twins-megoldásban a környezetében lévő entitásokat az Azure **digitális ikrek**képviselik. A digitális Twin a saját egyéni [modelljeinek](concepts-models.md)egyik példánya. Összeköthető más digitális Twins **kapcsolatokkal** a **kettős gráf**kialakításához: Ez a Twin gráf a teljes környezet ábrázolását mutatja be.

> [!TIP]
> Az "Azure digitális Twins" az Azure-szolgáltatás egészére vonatkozik. A "digitális twin (k)" vagy csak a "twin (k)" a szolgáltatás példányán belüli különálló csomópontokra utal.

## <a name="creating-digital-twins"></a>Digitális ikrek létrehozása

Ahhoz, hogy létre lehessen hozni egy Digital Twin-et az Azure Digital Twins-példányban, rendelkeznie kell egy, a szolgáltatásba feltöltött *modellel* . A modellek a tulajdonságok, a telemetria és az adott Twin kapcsolatok készletét írják le, egyebek között. A modellben definiált információk típusaiért lásd [: fogalmak: egyéni modellek](concepts-models.md).

Egy modell létrehozása és feltöltése után az ügyfélalkalmazás létrehozhat egy típusú példányt; Ez egy digitális Twin. Például a *Floor*modell létrehozása után létrehozhat egy vagy több olyan digitális ikreket, amelyek ezt a típust használják (például a *Floor*típust, egy másikat *, a* *Floor2*stb.). 

Az alábbi kódrészlet a [DigitalTwins API](how-to-use-apis-sdks.md) -kat használja egy Twin típusú *szoba*létrehozásához.

Az Azure Digital Twins aktuális előnézetében a Twin tulajdonságot a Twin létrehozása előtt inicializálni kell. Ezt egy JSON-dokumentum létrehozásával teheti meg, amely megadja a szükséges inicializálási értékeket.

```csharp
public Task<boolean> CreateRoom(string id, double temperature, double humidity) 
{
    // Define the model for the twin to be created
    Dictionary<string, object> meta = new Dictionary<string, object>()
    {
      { "$model", "dtmi:com:contoso:Room;2" }
    };
    // Initialize the twin properties
    Dictionary<string, object> initData = new Dictionary<string, object>()
    {
      { "$metadata", meta },
      { "Temperature", temperature},
      { "Humidity", humidity},
    };
    try
    {
      await client.DigitalTwins.AddAsync(id, initData);
      return true;
    }
    catch (ErrorResponseException e)
    {
      Console.WriteLine($"*** Error creating twin {id}: {e.Response.StatusCode}");
      return false;
    }
}
```

## <a name="relationships-creating-a-graph-of-digital-twins"></a>Kapcsolatok: digitális ikrek gráfjának létrehozása

Az ikrek a kapcsolataik között kettős gráfba csatlakoznak. A Twin típusú kapcsolatok a modell részeként definiálhatók.  

Például a modell *emeleten* definiálhat egy olyan kapcsolatot, *amely a* *szoba*típusú ikreket célozza meg. Ezzel a definícióval az Azure Digital Twins lehetővé teszi, hogy az összes *emeleti* Twin-ből *származó kapcsolatot* hozzon létre a Twin *Room* (beleértve az olyan ikreket, amelyek a *szoba* altípusai). 

Íme néhány példa arra, hogy a [DigitalTwins API](how-to-use-apis-sdks.md) *-kat*használó ügyfél létrehozza a kapcsolatot *egy, a* *földszintes*és egy *Cafe*típusú digitális Twin.

```csharp
// Create Twins, using functions similar to the previous sample
await CreateRoom("Cafe", 70, 66);
await CreateFloor("GroundFloor", averageTemperature=70);
// Create relationships
Dictionary<string, object> targetrec = new Dictionary<string, object>()
{
    { "$targetId", "Cafe" }
};
try
{
    await client.DigitalTwins.AddEdgeAsync("GroundFloor", "contains", "GF-to-Cafe", targetrec);
} catch(ErrorResponseException e)
{
    Console.WriteLine($"*** Error creating relationship: {e.Response.StatusCode}");
}
```

Ennek a folyamatnak az eredménye egy gráfon (a kapcsolatokon) keresztül összekapcsolt csomópontok (a digitális ikrek) halmaza.

## <a name="json-representations-of-graph-elements"></a>Graph-elemek JSON-ábrázolásai

A digitális Twin-és a kapcsolati adatfájlok egyaránt JSON formátumban vannak tárolva. Ez azt jelenti, hogy amikor [lekérdezi a Twin Graphot](how-to-query-graph.md) az Azure Digital Twins-példányban, az eredmény a digitális ikrek és a létrehozott kapcsolatok JSON-ábrázolása lesz.

### <a name="digital-twin-json-format"></a>Digitális kettős JSON formátum

Ha JSON-objektumként jelenik meg, a Digital Twin a következő mezőket jeleníti meg:

| Mező neve | Leírás |
| --- | --- |
| `$dtId` | A digitális Twin AZONOSÍTÓját jelölő, felhasználó által megadott karakterlánc |
| `$conformance` | A digitális iker megfelelőségi állapotát tartalmazó felsorolás ( *nem**megfelelő,* *ismeretlen*) |
| `{propertyName}` | Egy tulajdonság értéke a JSON-ban ( `string` , szám típusa vagy objektum) |
| `$relationships` | A kapcsolatok gyűjtemény elérési útjának URL-címe Ez a mező hiányzik, ha a digitális Twin nem rendelkezik kimenő kapcsolati élekkel. |
| `$metadata.$model` | Választható A digitális IKeret jellemző modell felület azonosítója |
| `$metadata.{propertyName}.desiredValue` | [Csak írható tulajdonságok esetében] A megadott tulajdonság kívánt értéke |
| `$metadata.{propertyName}.desiredVersion` | [Csak írható tulajdonságok esetében] A kívánt érték verziója |
| `$metadata.{propertyName}.ackVersion` | A digitális twint megvalósító eszköz alkalmazás által elfogadott verzió |
| `$metadata.{propertyName}.ackCode` | [Csak írható tulajdonságok esetében] A `ack` digitális IKeret megvalósító eszköz alkalmazás által visszaadott kód |
| `$metadata.{propertyName}.ackDescription` | [Csak írható tulajdonságok esetében] A `ack` digitális IKeret megvalósító eszköz alkalmazás által visszaadott Leírás |
| `{componentName}` | Egy JSON-objektum, amely tartalmazza az összetevő tulajdonságának értékeit és metaadatait, hasonlóan a gyökérszintű objektumhoz. Ez az objektum akkor is létezik, ha az összetevő nem rendelkezik tulajdonsággal. |
| `{componentName}.{propertyName}` | Az összetevő tulajdonságának értéke a JSON-ban ( `string` , szám típusa vagy objektum) |
| `{componentName}.$metadata` | Az összetevő metaadat-információi, a legfelső szintűhez hasonlóan`$metadata` |

Íme egy példa egy, a JSON-objektumként formázott Digital Twin-re:

```json
{
  "$dtId": "Cafe",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "$model": "dtmi:com:contoso:Table;1",
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Kapcsolat JSON-formátuma

Ha JSON-objektumként jelenik meg, a digitális iker kapcsolata a következő mezőket jeleníti meg:

| Mező neve | Leírás |
| --- | --- |
| `$edgeId` | Egy felhasználó által megadott karakterlánc, amely a kapcsolati szegély AZONOSÍTÓját jelöli. Ez a karakterlánc egyedi a forrásként szolgáló digitális Twin környezetben, ami azt is jelenti, hogy az `sourceId`  +  `edgeId` Azure Digital Twins-példány kontextusában egyedi. |
| `$sourceId` | A forrásként szolgáló digitális Twin azonosító |
| `$targetId` | A cél digitális iker azonosítója |
| `$relationshipName` | A kapcsolat neve |
| `{propertyName}` | Választható A kapcsolat tulajdonságának értéke JSON ( `string` , number Type vagy Object) |

Íme egy példa JSON-objektumként formázott kapcsolatra:

```json
{
  "$edgeId": "Edge-01",
  "$sourceId": "GroundFloor",
  "$relationship": "contains",
  "$targetId": "Cafe",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>További lépések

Lásd: gráf-elemek kezelése az Azure Digital Twin API-kkal:
* [Útmutató: digitális ikrek kezelése](how-to-manage-twin.md)
* [Útmutató: a Twin gráf kezelése kapcsolatok használatával](how-to-manage-graph.md)

Vagy Ismerje meg az Azure Digital ikrek Twin Graph lekérdezését az információkhoz:
* [Fogalmak: lekérdezési nyelv](concepts-query-language.md)