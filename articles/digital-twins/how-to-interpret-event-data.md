---
title: Az események értelmezése
titleSuffix: Azure Digital Twins
description: 'Lásd: a különböző eseménytípus értelmezése és a különböző értesítési üzenetek.'
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4c95e686de23654688d0d7c3182c6565a907b750
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612962"
---
# <a name="understand-event-data"></a>Az események értelmezése

Az Azure Digital Twins különböző eseményeinek **értesítéseket**hoznak létre, amelyek lehetővé teszik a megoldási háttér használatát, ha különböző műveletek történnek. Ezeket a rendszer az Azure digitális Ikreken belüli és azon kívüli különböző helyszínekre [irányítja](concepts-route-events.md) át, amelyek felhasználhatják ezeket az információkat a művelet elvégzésére.

Számos típusú értesítés hozható létre, és az értesítési üzenetek eltérőek lehetnek attól függően, hogy milyen típusú eseményt generáltak. Ez a cikk részletesen ismerteti a különböző típusú üzeneteket, és azok megjelenését.

Ez a diagram a különböző értesítési típusokat mutatja:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

Az értesítések általában két részből állnak: a fejlécből és a törzsből. 

### <a name="event-notification-headers"></a>Esemény-értesítési fejlécek

Az értesítési üzenetek fejléce a kulcs-érték párokkal együtt jelenik meg. A használt protokolltól (MQTT, AMQP vagy HTTP) függően az üzenetek fejlécei másképp szerializálva lesznek. Ez a szakasz az értesítési üzenetek általános fejléc-információit ismerteti, a választott protokolltól és szerializálástól függetlenül.

Egyes értesítések megfelelnek az CloudEvents szabványnak. A CloudEvents-megfelelőség a következő.
* Az eszközökről kibocsátott értesítések továbbra is követik az értesítések meglévő specifikációit
* Az IoT Hub által feldolgozott és kibocsátott értesítések továbbra is követik az értesítés meglévő specifikációit, kivéve, ha IoT Hub úgy dönt, hogy támogatja a CloudEvents, például: Event Grid
* A [digitális ikrek](concepts-twins-graph.md) által kibocsátott, a [modellel](concepts-models.md) megegyező CloudEvents-értesítések
* Az Azure Digital Twins által feldolgozott és kibocsátott értesítések megfelelnek a CloudEvents

A szolgáltatásoknak sorozatszámot kell hozzáadniuk az összes értesítéshez, hogy megadják a sorrendjét, vagy más módon megtartsák a saját rendelését. Az Azure Digital Twins által kibocsátott értesítések Event Grid formázva vannak a Event Grid sémába, amíg a Event Grid támogatja a CloudEvents a bemeneten. A fejlécek bővítmény-attribútumai a Event Grid séma tulajdonságaiként lesznek hozzáadva a hasznos adatokhoz. 

### <a name="event-notification-bodies"></a>Esemény-értesítési testületek

Az értesítési üzenetek törzseit itt találja a JSON-ban. Az üzenet törzséhez (például JSON, CBOR, Protopuf stb.) való szerializálástól függően előfordulhat, hogy az üzenet törzse másképp szerializálható.

A törzs által tartalmazott mezők halmaza eltérő értesítési típusokkal rendelkezik. Íme két példa az üzenet törzsére, hogy megtudja, mit láthatnak általában, és hogyan tartalmazhatnak.

Telemetria-üzenet:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.iot.telemetry", 
    "source": "myhub.westus2.azuredigitaltwins.net", 
    "subject": "thermostat.vav-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a",
    "dataschema": "dtmi:com:contoso:DigitalTwins:VAV;1",
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "data":  
      {
          "temp": 70,
          "humidity": 40 
      }
}
```

Életciklus-értesítési üzenet:

```json
{ 
    "specversion": "1.0", 
    "type": "microsoft.digitaltwins.twin.create", 
    "source": "mydigitaltwins.westus2.azuredigitaltwins.net", 
    "subject": "device-123", 
    "id": "c1b53246-19f2-40c6-bc9e-4666fa590d1a", 
    "time": "2018-04-05T17:31:00Z", 
    "datacontenttype" : "application/json", 
    "dataschema": "dtmi:com:contoso:DigitalTwins:Device;1",           
    "data":  
      { 
        "$dtId": "room-123", 
        "property": "value",
        "$metadata": { 
                //...
        } 
      } 
}
```

## <a name="message-format-detail-for-different-event-types"></a>Az üzenet formátumának részletei a különböző típusú eseményekhez

Ez a szakasz részletesebben ismerteti a IoT Hub és az Azure Digital Twins (vagy más Azure IoT-szolgáltatások) által kibocsátott különböző típusú értesítéseket. Itt olvashat az egyes értesítési típusokat kiváltó dolgokról, valamint az egyes típusú értesítési törzsekhez tartozó mezők készletéről.

### <a name="digital-twin-life-cycle-notifications"></a>Digitális kettős életciklussal kapcsolatos értesítések

Minden [digitális ikrek](concepts-twins-graph.md) értesítéseket bocsát ki, függetlenül attól, hogy [IoT hub-eszközöket képviselnek-e az Azure Digital ikrekben](how-to-ingest-iot-hub-data.md) vagy sem. Ennek az az oka, hogy az **életciklussal kapcsolatos értesítések**, amelyek a digitális Twin-re vonatkoznak.

Az életciklussal kapcsolatos értesítések a következők esetén aktiválódnak:
* Létrejön egy digitális Twin
* Egy digitális Twin törlésre kerül

#### <a name="properties"></a>Tulajdonságok

Az életciklus-értesítés törzsének mezői.

| Name | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id`minden különböző esemény esetében egyedi. |
| `source` | Az IoT hub vagy az Azure Digital Twins-példány neve, például *myhub.Azure-Devices.net* vagy *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Create`<br>`Microsoft.DigitalTwins.TwinProxy.Delete`<br>`Microsoft.DigitalTwins.TwinProxy.Attach`<br>`Microsoft.DigitalTwins.TwinProxy.Detach` |
| `datacontenttype` | application/json |
| `subject` | A digitális Twin azonosító |
| `time` | Időbélyeg, amikor a művelet bekövetkezett a Twin |
| `sequence` | Érték, amely az esemény pozícióját a nagyobb rendezett események sorrendjében fejezi ki. A szolgáltatásoknak sorozatszámot kell hozzáadniuk az összes értesítéshez, hogy megadják a sorrendjét, vagy más módon megtartsák a saját rendelését. A sorozatszám minden üzenettel nő. A rendszer visszaállítja az 1 értékre, ha az objektumot törli, és ugyanazzal az AZONOSÍTÓval hozza létre újra. |
| `sequencetype` | További részletek a Sequence mező használatáról. Ez a tulajdonság például azt is megadhatja, hogy az értéknek egy aláírt 32 bites egész számnak kell lennie, amely az 1. időpontban kezdődik, és eggyel növekszik. |

#### <a name="body-details"></a>Törzs részletei

A törzs az érintett digitális Twin, amely JSON formátumban van megjelenítve. A séma ehhez a *digitális Twins-erőforrás 7,1*.

A létrehozási események esetében a hasznos adatok az erőforrás létrehozása után a Twin állapotot tükrözik, így az összes rendszer által generált elemnek kell szerepelnie, mint a `GET` hívás.

Íme egy példa egy [IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) eszközre, amely összetevőkkel és nem legfelső szintű tulajdonságokkal rendelkezik. Az eszközökre (például a jelentett tulajdonságokra) nem jellemző tulajdonságokat el kell hagyni.

```json
{
  "$dtId": "device-digitaltwin-01",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Íme egy másik példa a digitális Twin-re. Ez egy [modellen](concepts-models.md)alapul, és nem támogatja az összetevőket:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "$kind": "DigitalTwin",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Digitális kettős kapcsolat változási értesítései

A **kapcsolat változásával kapcsolatos értesítések** akkor aktiválódnak, ha a digitális kettős kapcsolat létrejött, frissítve vagy törölve lett. 

#### <a name="properties"></a>Tulajdonságok

Itt láthatók az Edge Change-értesítések törzsének mezői.

| Name    | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id`egyedi a különböző eseményekhez |
| `source` | Az Azure Digital Twins-példány neve, például *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`<br>`datacontenttype    application/json for Relationship.Create`<br>`application/json-patch+json for Relationship.Update` |
| `subject` | A kapcsolat azonosítója, például:`<twinID>/relationships/<relationshipName>/<edgeID>` |
| `time` | Időbélyeg, hogy mikor történt a művelet a kapcsolaton |
| `sequence` | Érték, amely az esemény pozícióját a nagyobb rendezett események sorrendjében fejezi ki. A szolgáltatásoknak sorozatszámot kell hozzáadniuk az összes értesítéshez, hogy megadják a sorrendjét, vagy más módon megtartsák a saját rendelését. A sorozatszám minden üzenettel nő. A rendszer visszaállítja az 1 értékre, ha az objektumot törli, és ugyanazzal az AZONOSÍTÓval hozza létre újra. |
| `sequencetype` | További részletek a Sequence mező használatáról. Ez a tulajdonság például azt is megadhatja, hogy az értéknek egy aláírt 32 bites egész számnak kell lennie, amely az 1. időpontban kezdődik, és eggyel növekszik. |

#### <a name="body-details"></a>Törzs részletei

A törzs a kapcsolat hasznos adatai JSON formátumban is. Ugyanazt a formátumot használja, mint egy `GET` kapcsolat kérése a [DigitalTwins API](how-to-use-apis-sdks.md)-n keresztül. 

A "kapcsolat frissítése" érték azt jelenti, hogy a kapcsolat tulajdonságai megváltoztak. 

Íme egy példa egy frissítési kapcsolati értesítésre, amely frissíti a tulajdonságot:

```json
[
  {
    "op": "replace",
    "path": "ownershipUser",
    "value": "user3"
  }
]
```

A esetében `Relationship.Delete` a törzs megegyezik a `GET` kérelemmel, és a törlés előtt lekéri a legutóbbi állapotot.

Íme egy példa egy Create vagy DELETE kapcsolati értesítésre:

```json
{
    "$relationshipId": "EdgeId1",
    "$sourceId": "building11",
    "$relationshipName": "Contains",
    "$targetId": "floor11",
    "ownershipUser": "user1",
    "ownershipDepartment": "Operations"
}
```

### <a name="digital-twin-model-change-notifications"></a>Digitális kettős modell változási értesítései

A **modell módosítására vonatkozó értesítések** akkor aktiválódnak, ha a digitális Twins Definition Language (DTDL) [modellt](concepts-models.md) feltöltik, újratöltve, megjavították, leszerelték vagy törölték.

#### <a name="properties"></a>Tulajdonságok

Itt láthatók a modell-változási értesítések törzsének mezői.

| Name    | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id`egyedi a különböző eseményekhez |
| `source` | Az IoT hub vagy az Azure Digital Twins-példány neve, például *myhub.Azure-Devices.net* vagy *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Model.Upload`<br>`Microsoft.DigitalTwins.Model.Reload`(Hub-specifikus)<br>`Microsoft.DigitalTwins.Model.Patch`(Hub-specifikus)<br>`Microsoft.DigitalTwins.Model.Decom`<br>`Microsoft.DigitalTwins.Model.Delete` |
| `datacontenttype` | application/json |
| `subject` | A modell azonosítója az űrlapon`dtmi:<domain>:<unique model identifier>;<model version number>` |
| `time` | Időbélyeg a modellen végzett művelet bekövetkeztekor |
| `sequence` | Érték, amely az esemény pozícióját a nagyobb rendezett események sorrendjében fejezi ki. A szolgáltatásoknak sorozatszámot kell hozzáadniuk az összes értesítéshez, hogy megadják a sorrendjét, vagy más módon megtartsák a saját rendelését. A sorozatszám minden üzenettel nő. A rendszer visszaállítja az 1 értékre, ha az objektumot törli, és ugyanazzal az AZONOSÍTÓval hozza létre újra. |
| `sequencetype` | További részletek a Sequence mező használatáról. Ez a tulajdonság például azt is megadhatja, hogy az értéknek egy aláírt 32 bites egész számnak kell lennie, amely az 1. időpontban kezdődik, és eggyel növekszik. |
| `modelstatus` | A modell feloldási állapota. Lehetséges értékek: sikeres/NotFound/sikertelen (csak IoT Hub) | 
| `updatereason` | Frissítési modell oka a sémában. Lehetséges értékek: létrehozás/alaphelyzetbe állítás/felülbírálás (csak IoT Hub) | 

#### <a name="body-details"></a>Törzs részletei

Nem áll rendelkezésre üzenet a modellek feltöltésére, újratöltésére és javítására szolgáló műveletekhez. A felhasználónak `GET` hívást kell kérnie a modell tartalmának beszerzéséhez. 

A és a esetében a `Model.Decom` javítás a JSON-javító formátumban fog megjelenni, például az Azure digitális Twins API felületének minden más javítási API-jának. A modellek leszereléséhez a következőt kell használnia:

```json
[
  {
    "op": "replace",
    "path": "/decommissionedState",
    "value": true
  }
]
```

Esetében `Model.Delete` a kérelem törzse megegyezik a `GET` kérelemmel, és a törlés előtt a legutolsó állapotot kapja.

### <a name="digital-twin-change-notifications"></a>Digitális kettős változások értesítései

A digitális **kettős változásokról szóló értesítéseket** a rendszer akkor indítja el, ha a digitális iker frissítése folyamatban van, például:
* A tulajdonságértékek vagy a metaadatok módosításakor.
* Ha a digitális iker-vagy összetevő-metaadatok módosulnak. Ebben a forgatókönyvben egy példa a digitális iker modelljét változtatja meg.

#### <a name="properties"></a>Tulajdonságok

Itt láthatók a digitális kettős változásokról szóló értesítés törzsének mezői.

| Name    | Érték |
| --- | --- |
| `id` | Az értesítés azonosítója, például egy UUID vagy a szolgáltatás által karbantartott számláló. `source` + `id`egyedi a különböző eseményekhez |
| `source` | Az IoT hub vagy az Azure Digital Twins-példány neve, például *myhub.Azure-Devices.net* vagy *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | 1.0 |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | alkalmazás/JSON-patch + JSON |
| `subject` | A digitális Twin azonosító |
| `time` | Időbélyeg a digitális Twin művelet bekövetkeztekor |
| `sequence` | Érték, amely az esemény pozícióját a nagyobb rendezett események sorrendjében fejezi ki. A szolgáltatásoknak sorozatszámot kell hozzáadniuk az összes értesítéshez, hogy megadják a sorrendjét, vagy más módon megtartsák a saját rendelését. A sorozatszám minden üzenettel nő. A rendszer visszaállítja az 1 értékre, ha az objektumot törli, és ugyanazzal az AZONOSÍTÓval hozza létre újra. |
| `sequencetype` | További részletek a Sequence mező használatáról. Ez a tulajdonság például azt is megadhatja, hogy az értéknek egy aláírt 32 bites egész számnak kell lennie, amely az 1. időpontban kezdődik, és eggyel növekszik. |

#### <a name="body-details"></a>Törzs részletei

Az értesítés törzse `Twin.Update` egy JSON-javítási dokumentum, amely a digitális dupla frissítést tartalmazza.

Tegyük fel például, hogy a digitális iker frissítése a következő javítás használatával történt.

```json
[
  {
    "op": "replace",
    "path": "/mycomp/prop1",
    "value": {"a":3}
  }
]
```

A megfelelő értesítés (ha a szolgáltatás szinkron módon hajtja végre, mint például az Azure digitális ikrek frissítése egy digitális Twin-et), a következőhöz hasonló szervnek kell lennie:

```json
[
    { "op": "replace", "path": "/myComp/prop1", "value": {"a": 3}},
    { "op": "replace", "path": "/myComp/$metadata/prop1",
        "value": {
            "desiredValue": { "a": 3 },
            "desiredVersion": 2,
                "ackCode": 200,
            "ackVersion": 2 
        }
    }
]
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg, hogyan hozhatók létre végpontok és útvonalak az események kézbesítéséhez:
* [Útmutató: végpontok és útvonalak kezelése](how-to-manage-routes.md)

Vagy további információ az Azure Digital Twins API-król és az SDK lehetőségeiről:
* [Útmutató: az Azure Digital Twins API-k és SDK-k használata](how-to-use-apis-sdks.md)