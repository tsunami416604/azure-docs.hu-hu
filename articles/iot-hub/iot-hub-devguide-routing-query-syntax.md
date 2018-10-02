---
title: Az Azure IoT Hub üzenet-útválasztása lekérdezés |} A Microsoft Docs
description: Fejlesztői útmutató – az Azure IoT hub üzenet-útválasztása lekérdezés synxtax.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 3967a1e2317bac76785d534ba04a93de552c1a40
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48018536"
---
# <a name="iot-hub-message-routing-query-syntax"></a>Az IoT Hub üzenet-útválasztás lekérdezési szintaxis

Üzenet-útválasztása lehetővé teszi a felhasználók különböző adattípusok nevezetesen irányíthatja, majd eszköz telemetriai üzeneteket, eszköz-életciklussal kapcsolatos események és ikereszköz események különféle végpontok. Ezeket az adatokat fogadni az adatokat az Ön számára fontos átirányítására előtt is alkalmazhat részletes lekérdezéseket. Ez a cikk ismerteti az IoT Hub üzenet útválasztási lekérdezési nyelvet, és gyakori lekérdezési minták biztosít.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Üzenet-útválasztása lehetővé teszi az üzenet tulajdonságai és üzenet szövegét, valamint device twin címkék és eszköz-ikertulajdonságok lekérdezést. Ha az üzenet törzse nem JSON, üzenet-útválasztással is továbbra is továbbítsa az üzenetet, de a lekérdezések az üzenet törzse nem lehet alkalmazni.  Lekérdezések olyan logikai kifejezésen, ahol a logikai változó értéke igaz teszi a lekérdezés sikeres legyen, amely továbbítja az összes beérkező adatot, és a logikai változó értéke HAMIS, sikertelen, és nincs adat legyen irányítva. Ha a kifejezés kiértékelésének eredménye null értékű vagy nincs definiálva, false számít, és hiba történt a diagnosztikai naplókban hiba esetén létrejön. A lekérdezés szintaxisa helyes mentett és kiértékelése az útvonal kell lennie.  

## <a name="message-routing-query-based-on-message-properties"></a>Állapotüzenet-útválasztási lekérdezés alapján üzenet tulajdonságai 

Az IoT Hub meghatározása egy [gyakran alkalmazott formátum](iot-hub-devguide-messages-construct.md) protokollok között interoperatbility az üzenetkezelés összes eszköz-felhő. Az IoT Hub üzenet feltételezi, hogy az üzenet a következő JSON-reprezentációval. Rendszertulajdonságok hozzáadja az összes felhasználó számára, és azonosítására az üzenet tartalma. Felhasználók külön-külön tulajdonságokat adhat hozzá alkalmazás az üzenetet. Azt javasoljuk, hogy egyedi tulajdonságneveket használatával, mert az IoT Hub eszköz – felhő üzenetek nem kis-és nagybetűket. Például ha ugyanazzal a névvel több tulajdonsággal rendelkezik, az IoT Hub csak küld egyik tulajdonsága.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Rendszertulajdonságok

Rendszertulajdonságok tartalmát, és az üzenetek forrás azonosíthatók. 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| contentType | sztring | A felhasználó adja meg az üzenet tartalomtípusa. Ahhoz, hogy a lekérdezés az üzenet törzsében, ezt az értéket meg kell application/JSON. |
| contentEncoding | sztring | A felhasználó adja meg az üzenet kódolási típusának. Megengedett értékek: UTF-8, az UTF-16, az UTF-32, ha a contentType application/JSON értékre van állítva. |
| ConnectionDeviceId | sztring | Ez az érték az IoT Hub által van beállítva, és azonosítja az üzenetek forrását. Ez lehet eszköz telemetriai üzeneteket, device twin változási értesítéseket vagy eszköz-életciklussal kapcsolatos események. Ez nem lehet lekérdezni. |
| iothub-enqueuedtime | sztring | Ezt az értéket az IoT Hub által van beállítva, és a tényleges idő (UTC), az üzenet sorba, jelöli. Lekérdezés, használja a `enqueuedTime`. |

Leírtak szerint a [IoT Hub-üzenetek](iot-hub-devguide-messages-construct.md), üzenet további rendszer tulajdonságai szerepelnek. Mellett **contentType**, **contentEncoding**, és **enqueuedTime**, a **connectionDeviceId** és  **connectionModuleId** is lekérdezhetők.

### <a name="application-properties"></a>Az alkalmazás tulajdonságai

Alkalmazástulajdonságok olyan felhasználó által definiált karakterláncok az üzenet adható hozzá. Ezek a mezők kitöltése nem kötelező.  

### <a name="query-expressions"></a>Lekérdezési kifejezések

A következő előtaggal kell egy lekérdezést a rendszer üzenettulajdonságok a `$` szimbólum. Az alkalmazás tulajdonságai lekérdezések érhetők el, amelyek neve, és nem kell pénznemszimbólumával kezdődik a `$`szimbólum. Ha egy alkalmazás tulajdonságnév kezdődik `$`, majd az IoT Hub fog keressen rá a tulajdonságainál, és nem található, majd fog kinézni az alkalmazás tulajdonságait. Példa: 

A rendszer tulajdonság contentEncoding lekérdezése 

```sql
$contentEncoding = 'UTF-8'
```

Az alkalmazás tulajdonság processingPath lekérdezése:

```sql
processingPath = 'hot'
```

Ezek a lekérdezések egyesítése, használhatja a logikai kifejezések és függvények:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

Teljes listáját támogatott operátorok és felsorolt funkciók [kifejezés és feltételek](iot-hub-devguide-query-language.md#expressions-and-conditions)

## <a name="message-routing-query-based-on-message-body"></a>Állapotüzenet-útválasztási lekérdezés alapján az üzenet törzse 

Ahhoz, hogy az üzenet törzse lekérdezése, az üzenet egy JSON kódolású vagy UTF-8, UTF-16, az UTF-32 kell lennie. A `contentType` értékre kell állítani `application/JSON` és `contentEncoding` a rendszer tulajdonságban támogatott UTF kódolásai a egyikére. Ha ezek a tulajdonságok nincsenek megadva, az IoT Hub nem értékeli a lekérdezési kifejezésben az üzenet törzsében. 

Az alábbi példa bemutatja, hogyan hozhat létre egy üzenetet egy megfelelően formázott és kódolt JSON-törzse: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

### <a name="query-expressions"></a>Lekérdezési kifejezések

Egy lekérdezést a üzenet szövege a következő előtaggal kell a `$body`. Használhatja egy szervezet hivatkozást, törzs tömb hivatkozás vagy több szervezet hivatkozást a lekérdezési kifejezésben. A lekérdezési kifejezés egy szervezet referencia üzenet Rendszertulajdonságok és üzenet tulajdonságai alkalmazásreferencia kombinálhatók. Ha például a következők az összes érvényes lekérdezési kifejezések: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

## <a name="message-routing-query-based-on-device-twin"></a>Állapotüzenet-útválasztási lekérdezés ikereszköz alapján 

Üzenet-útválasztása lehetővé teszi, hogy a lekérdezés [Ikereszköz](iot-hub-devguide-device-twins.md) címkék és tulajdonságok, amely JSON-objektumok. Vegye figyelembe, hogy az ikermodul lekérdezése nem támogatott. Ikereszközök címkék és tulajdonságok mintát alább látható.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Lekérdezési kifejezések

Egy lekérdezést a üzenet szövege a következő előtaggal kell a `$twin`. A lekérdezési kifejezés ikereszköz címke vagy tulajdonság hivatkozást törzs hivatkozást, üzenet Rendszertulajdonságok és üzenet tulajdonságai alkalmazásreferencia kombinálhatók. Azt javasoljuk, hogy a címkék és tulajdonságok egyedi nevek használata, mert a lekérdezés nem kis-és nagybetűket. Emellett ne használja `twin`, `$twin`, `body`, vagy `$body`, mint a tulajdonságnevek. Ha például a következők az összes érvényes lekérdezési kifejezések: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

## <a name="next-steps"></a>További lépések

* Ismerje meg [üzenet-útválasztása](iot-hub-devguide-messages-d2c.md).
* Próbálja ki a [útválasztási oktatóanyag üzenet](tutorial-routing.md).