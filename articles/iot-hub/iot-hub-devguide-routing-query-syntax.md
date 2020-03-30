---
title: Lekérdezés az Azure IoT Hub üzenetútválasztásán | Microsoft dokumentumok
description: Ismerje meg az IoT Hub üzenet-útválasztási lekérdezési nyelv, amely segítségével gazdag lekérdezések az üzenetekre, hogy megkapja az adatokat, amelyek fontos az Ön számára.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: b76ef431e4c0ad63929378c1f48c6ab06776cb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271108"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT-központ üzenet-útválasztásának lekérdezési szintaxisa

Az üzenet-útválasztás lehetővé teszi a felhasználók számára, hogy különböző adattípusokat, nevezetesen az eszköztelemetriai üzeneteket, az eszköz életciklus-eseményeit és az eszköz ikermódosítási eseményeit különböző végpontokra irányítsák. Ezekre az adatokra is alkalmazhat bővített lekérdezéseket, mielőtt útbahozna őket, hogy megkapja az Ön számára fontos adatokat. Ez a cikk ismerteti az IoT Hub üzenet-útválasztási lekérdezési nyelv, és néhány gyakori lekérdezési mintákat tartalmaz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Az üzenet-útválasztás lehetővé teszi az üzenet tulajdonságainak és az üzenettörzsnek, valamint az ikercímkéknek és az ikereszköz-tulajdonságoknak a lekérdezését. Ha az üzenet törzse nem JSON, az üzenettovábbítás továbbra is továbbíthatja az üzenetet, de a lekérdezések nem alkalmazhatók az üzenet törzsére.  A lekérdezések logikai kifejezésekként vannak leírva, ahol a logikai igaz érték sikeressé teszi a lekérdezést, amely az összes bejövő adatot irányítja, és a logikai hamis sikertelen a lekérdezés, és nincs adat irányítva. Ha a kifejezés eredménye null vagy nem definiált, a rendszer hamisként kezeli, és hiba esetén hiba történik a diagnosztikai naplókban. A lekérdezés szintaxisának helyesnek kell lennie az útvonal mentéséhez és kiértékeléséhez.  

## <a name="message-routing-query-based-on-message-properties"></a>Üzenet-útválasztási lekérdezés az üzenet tulajdonságai alapján 

Az IoT Hub közös [formátumot](iot-hub-devguide-messages-construct.md) határoz meg az összes eszközről a felhőbe irányuló üzenetküldéshez a protokollok közötti együttműködés érdekében. Az IoT Hub-üzenet az üzenet következő JSON-ábrázolását feltételezi. A rendszer tulajdonságok minden felhasználóhoz hozzáadódik, és azonosítja az üzenet tartalmát. A felhasználók szelektíven adhatnak hozzá alkalmazástulajdonságokat az üzenethez. Azt javasoljuk, hogy egyedi tulajdonságnevek et az IoT Hub eszköz-felhő üzenetek nem kis- és nagybetűk et. Ha például több tulajdonsággal rendelkezik ugyanazzal a névvel, az IoT Hub csak az egyik tulajdonságot küldi el.  

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

A rendszertulajdonságok segítenek azonosítani az üzenetek tartalmát és forrását. 

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| contentType típusú | sztring | A felhasználó megadja az üzenet tartalomtípusát. Ahhoz, hogy a lekérdezés az üzenet törzsében, ezt az értéket kell beállítani az alkalmazás/JSON. |
| contentEncoding (tartalomkódolás) | sztring | A felhasználó megadja az üzenet kódolási típusát. Megengedett értékek: UTF-8, UTF-16, UTF-32, ha a contentType alkalmazás/JSON értékre van állítva. |
| iothub-kapcsolat-eszköz-id | sztring | Ezt az értéket az IoT Hub állítja be, és azonosítja az eszköz azonosítóját. A lekérdezéshez `$connectionDeviceId`használja a használatát. |
| iothub-enqueuedtime | sztring | Ezt az értéket az IoT Hub állítja be, és az üzenet UTC-ben történő enqueuing tényleges idejét jelöli. A lekérdezéshez `enqueuedTime`használja a használatát. |
| iothub-interface-name | sztring | Ezt az értéket a felhasználó állítja be, és a telemetriai üzenetet megvalósító kétdigitális csatoló nevét jelöli. A lekérdezéshez `$interfaceName`használja a használatát. Ez a funkció az [IoT Plug and Play nyilvános előzetes verziójának](../iot-pnp/overview-iot-plug-and-play.md)részeként érhető el. |

Az [IoT Hub-üzenetek](iot-hub-devguide-messages-construct.md)ben leírtak szerint további rendszertulajdonságok találhatók az üzenetben. A **contentType**, **contentEncoding**és **enqueuedTime**mellett a **connectionDeviceId** és a **connectionModuleId** is lekérdezhető.

### <a name="application-properties"></a>Az alkalmazás tulajdonságai

Az alkalmazástulajdonságok felhasználó által definiált karakterláncok, amelyek hozzáadhatók az üzenethez. Ezek a mezők nem kötelezőek.  

### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenetrendszer tulajdonságaira vonatkozó lekérdezést `$` a szimbólummal kell előtagozni. Az alkalmazástulajdonságokra vonatkozó lekérdezések a nevükkel érhetők `$`el, és nem lehetnek a szimbólummal előtaggal. Ha egy alkalmazástulajdonság `$`neve kezdődik , majd az IoT Hub megkeresi azt a rendszer tulajdonságait, és nem található, majd meg fogja keresni az alkalmazás tulajdonságait. Példa: 

Lekérdezés a rendszertulajdonság tartalomraKódolás 

```sql
$contentEncoding = 'UTF-8'
```

Lekérdezés az alkalmazástulajdonság processingPath-on:

```sql
processingPath = 'hot'
```

A lekérdezések kombinálásához logikai kifejezéseket és függvényeket használhat:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

A támogatott operátorok és függvények teljes listája a Kifejezés és a [feltételek között](iot-hub-devguide-query-language.md#expressions-and-conditions)látható.

## <a name="message-routing-query-based-on-message-body"></a>Üzenet-útválasztási lekérdezés az üzenettörzs alapján

Az üzenettörzslekérdezés engedélyezéséhez az üzenetnek UTF-8, UTF-16 vagy UTF-32 kódolású JSON-ban kell lennie. A `contentType` rendszertulajdonságban `application/JSON` `contentEncoding` lévő támogatott UTF-kódolásegyikére és az egyikre kell beállítani. Ha ezek a tulajdonságok nincsenek megadva, az IoT Hub nem értékeli ki a lekérdezési kifejezést az üzenet törzsén. 

A következő példa bemutatja, hogyan hozhat létre egy üzenetet egy megfelelően formázott és kódolt JSON-törzskel: 

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

> [!NOTE] 
> Ez azt mutatja, hogyan kell kezelni a kódolása a test javascript. Ha c#-ban szeretne mintát látni, töltse le az [Azure IoT C# mintákat.](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip) Csomagolja ki a master.zip fájlt. A Visual *Studio-megoldás SimulatedDevice's*Program.cs fájl bemutatja, hogyan kódolhatja és küldheti el az üzeneteket egy IoT Hub. Ez ugyanaz a minta, amelyet az üzenetútválasztás teszteléséhez használnak, ahogy azt az [Üzenetútválasztás oktatóanyag ismerteti.](tutorial-routing.md) Alján Program.cs, ez is birtokol egy módszer-hoz olvas az egyik kódolt fájlokat, dekódolni, és írja vissza, mint ASCII így el tudja olvasni. 


### <a name="query-expressions"></a>Lekérdezési kifejezések

Az üzenet törzsére vonatkozó lekérdezést `$body`előtaggal kell előadni a segítségével. A lekérdezési kifejezésben használhat törzshivatkozást, törzstömb-hivatkozást vagy több törzshivatkozást. A lekérdezési kifejezés a törzshivatkozást az üzenetrendszer tulajdonságaival és az üzenetalkalmazás tulajdonságaival is kombinálhatja. A következők például mind érvényes lekérdezési kifejezések: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Üzenet-átirányítási lekérdezés az ikereszköz alapján 

Az üzenet-útválasztás lehetővé teszi az [Eszköz ikercímkék](iot-hub-devguide-device-twins.md) és -tulajdonságok lekérdezését, amelyek JSON-objektumok. Az ikermodul lekérdezése is támogatott. Az alábbiakban az Eszköz ikercímkék és -tulajdonságok mintája látható.

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

Az ikerüzenet-lekérdezést előtaggal `$twin`kell előadni a segítségével. A lekérdezési kifejezés egy ikercímkét vagy tulajdonsághivatkozást is kombinálhat törzshivatkozással, üzenetrendszer-tulajdonságokkal és üzenetalkalmazás-tulajdonságokkal. Azt javasoljuk, hogy egyedi neveket használjon a címkékben és a tulajdonságokban, mivel a lekérdezés nem érzékeny a kis- és nagybetűkre. Ez vonatkozik az eszköz ikrek és a modul ikrek. Tartózkodjon a `twin` `$twin` `body`tulajdonságnevek `$body`használatától is. A következők például mind érvényes lekérdezési kifejezések: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

A törzs- vagy ikerpáron lévő, a hasznos adatban vagy a tulajdonságnévben lévő ponttal rendelkező útválasztó lekérdezés nem támogatott.

## <a name="next-steps"></a>További lépések

* További információ az [üzenetek útválasztásáról.](iot-hub-devguide-messages-d2c.md)
* Próbálja ki az [üzenettovábbítási oktatóanyagot.](tutorial-routing.md)
