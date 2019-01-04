---
title: Felhasználó által definiált függvények létrehozása az Azure digitális Twins |} A Microsoft Docs
description: Szerepkör-hozzárendelések, matchers és felhasználó által definiált függvények létrehozása az Azure digitális Twins módja.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: 06c6d2935358650eb9f7ef1cda55d5292e203daf
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54019928"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények létrehozása az Azure digitális Twins

[Felhasználó által definiált függvények](./concepts-user-defined-functions.md) engedélyezése a felhasználók számára a beérkező telemetriai üzeneteket és a térbeli graph metaadatok futtatandó egyéni logikát. A felhasználók is elküldhetik az eseményeket az előre meghatározott [végpontok](./how-to-egress-endpoints.md).

Ez az útmutató ismerteti, hogyan észlelheti a kódírást példán keresztül, és minden olvasási riasztás, amely meghaladja a hőmérséklet fogadott események egy bizonyos hőmérséklet.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Ügyféloldali kódtár – referencia

A felhasználó által definiált függvények futtatókörnyezetben segédmetódusokat elérhető funkciók szerepelnek a [ügyféloldali kódtár – referencia](./reference-user-defined-functions-client-library.md) dokumentumot.

## <a name="create-a-matcher"></a>Hozzon létre egy megfeleltetőben megadott

Matchers graph-objektumok, amelyek meghatározzák, milyen felhasználó által definiált függvények futtatása egy adott telemetriai üzenetet is.

- Érvényes megfeleltetőben megadott feltétel összehasonlítások:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Érvényes megfeleltetőben megadott feltétel célok:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

A következő példa megfeleltetőben megadott bármely érzékelő telemetriai esemény az IGAZ értéket ad vissza `"Temperature"` adatok típusú értékként. A felhasználó által definiált függvény több matchers azáltal, hogy a hitelesített HTTP POST-kérelmet hozhat létre:

```plaintext
YOUR_MANAGEMENT_API_URL/matchers
```

A JSON-törzse:

```JSON
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Érték | Csere erre |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása

A matchers létrehozása után töltse fel a függvény kódrészletet a következő hitelesített többrészes HTTP POST kérés:

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

```plaintext
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Használja a következő JSON-törzse:

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "name": "User Defined Function",
  "description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Érték | Csere erre |
| --- | --- |
| USER_DEFINED_BOUNDARY | A többrészes tartalom határcsoport neve |
| YOUR_SPACE_IDENTIFIER | Hely azonosítója  |
| YOUR_MATCHER_IDENTIFIER | A használni kívánt megfeleltetőben megadott azonosítója |

1. Győződjön meg arról, hogy a fejlécek belefoglalása: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Győződjön meg arról, hogy a szervezet többrészes:

   - Az első rész a felhasználó által definiált függvény szükséges metaadatokat tartalmaz.
   - A második rész a JavaScript számítási logikáját tartalmazza.

1. Az a **USER_DEFINED_BOUNDARY** szakaszban, cserélje le a **spaceId** (`YOUR_SPACE_IDENTIFIER`) és **matchers** (`YOUR_MATCHER_IDENTIFIER`) értékeket.
1. Győződjön meg arról, hogy a felhasználó által definiált JavaScript-függvény objektumnevet `Content-Type: text/javascript`.

### <a name="example-functions"></a>Példa funkciók

Állítsa be az érzékelő telemetriai típusú adatokat közvetlenül az érzékelő olvasása **hőmérséklet**, amely `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

A **telemetriai** paraméter tesz elérhetővé a **SensorId** és **üzenet** attribútumokat, egy érzékelő által küldött üzenet megfelelő. A **executionContext** paramétert mutatja meg a következő attribútumokat:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

A következő példában azt az üzenetek naplózásához, ha az érzékelő telemetria olvasása idővesztesége egy előre meghatározott küszöbértéket. Ha a diagnosztikai beállítások engedélyezve vannak az Azure digitális Twins-példányon, felhasználó által definiált függvények származó naplók is továbbítja:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

A következő kódot egy értesítést aktivál, ha az előre definiált konstans fölé emelkedik a hőmérséklet szint:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

Felhasználó által definiált függvény összetettebb kódja minta tekintse meg a [foglaltsága rövid](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

A felhasználó által definiált függvény alatt szeretné futtatni a szerepkör-hozzárendelés létrehozása. Ha nincsenek szerepkör-hozzárendelés már létezik a felhasználó által definiált függvény, azt nem kell a megfelelő engedélyekkel, a felügyeleti API-val vagy műveleteket hajthat végre gráfsémákkal hozzá. Egy felhasználó által definiált függvény hajthat végre műveleteket a megadott és keresztül szerepköralapú hozzáférés-vezérlés az Azure digitális Twins felügyeleti API-k belül definiált. Például felhasználó által definiált függvények is lehet korlátozott hatókör egyes szerepkörök vagy a megadott access control elérési útjait megadásával. További információkért lásd: a [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) dokumentációját.

1. [A rendszer API lekérdezése](./security-create-manage-role-assignments.md#all) beolvasni a szerepkör-azonosító, a felhasználó által definiált függvényt hozzárendelni kívánt összes szerepköre. Ehhez a azáltal, hogy egy hitelesített HTTP GET kérést:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Tartsa meg a kívánt szerepkör-azonosítót. A JSON-törzse attribútumként lesznek átadva **roleId** (`YOUR_DESIRED_ROLE_IDENTIFIER`) alatt.

1. **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) lesz a korábban létrehozott felhasználó által definiált függvény azonosítója.
1. Keresse meg az értéket a **elérési** (`YOUR_ACCESS_CONTROL_PATH`) és a tárolóhelyek lekérdezésével `fullpath`.
1. Másolja a visszaadott `spacePaths` értéket. Szeretné használni, amely alatt. Győződjön meg arról, egy hitelesített HTTP GET kérést:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_SPACE_NAME | A használni kívánt terület neve |

1. Illessze be a visszaadott `spacePaths` be érték **elérési út** azáltal, hogy hitelesített HTTP POST-kérelmet a felhasználó által definiált függvény szerepkör-hozzárendelés létrehozásához:

    ```plaintext
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    A JSON-törzse:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | A kívánt szerepkör esetében az azonosító |
    | YOUR_USER_DEFINED_FUNCTION_ID | A felhasználó által definiált függvényt használni kívánt azonosítója |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | A felhasználó által definiált függvény típusát megadó azonosítója |
    | YOUR_ACCESS_CONTROL_PATH | A hozzáférés-vezérlési elérési útja |

>[!TIP]
> A cikk a [létrehozása és kezelése a szerepkör-hozzárendelések](./security-create-manage-role-assignments.md) további információ a felhasználó által definiált függvény felügyeleti API-műveleteket és a végpontok.

## <a name="send-telemetry-to-be-processed"></a>A feldolgozásra telemetria küldése

Az érzékelő, a térbeli intelligencia Graph definiált telemetriát küld. A telemetria elindítja a végrehajtás a felhasználó által definiált függvény feltöltött. A data-feldolgozó szerzi be a telemetriát. Ezután egy végrehajtási terv létrejön a felhasználó által definiált függvény meghívását.

1. A matchers a leolvasás jött létre az érzékelő lekéréséhez.
1. Attól függően, hogy milyen matchers értékelése sikeres a társított felhasználó által definiált függvények lekéréséhez.
1. Minden felhasználó által definiált függvény végrehajtásához.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [létrehozása az Azure digitális Twins végpontok](./how-to-egress-endpoints.md) események küldéséhez.

- Az Azure digitális Twins útválasztás kapcsolatos további információkért olvassa el a [útválasztás események és az üzenetek](./concepts-events-routing.md).

- Tekintse át a [ügyféloldali kódtár dokumentációja](./reference-user-defined-functions-client-library.md).
