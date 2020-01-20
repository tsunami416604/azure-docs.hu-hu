---
title: Felhasználó által definiált függvények létrehozása – az Azure digitális Twinsban | Microsoft Docs
description: Felhasználó által definiált függvények, egyeztetések és szerepkör-hozzárendelések létrehozása az Azure digitális Twins-ban.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276938"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények létrehozása az Azure Digital Twinsban

[Felhasználó által definiált függvények](./concepts-user-defined-functions.md) lehetővé teszik a felhasználók számára, hogy egyéni logikát konfiguráljanak a bejövő telemetria üzenetekből és a térbeli gráf metaadatainak. A felhasználók az előre meghatározott [végpontokra](./how-to-egress-endpoints.md)is küldhetnek eseményeket.

Ez az útmutató egy olyan példát mutat be, amely bemutatja, hogyan észlelheti és figyelmeztetheti az olyan olvasásokat, amelyek meghaladják az adott hőmérsékleti eseményektől kapott hőmérsékletet.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Ügyféloldali függvénytár-referenciák

A felhasználó által definiált függvények futtatókörnyezetében a segítő metódusként elérhető függvények az [ügyféloldali kódtár hivatkozási](./reference-user-defined-functions-client-library.md) dokumentumában vannak felsorolva.

## <a name="create-a-matcher"></a>Matcher létrehozása

Az egyeztetések olyan Graph-objektumok, amelyek meghatározzák, hogy a felhasználó által definiált függvények hogyan futnak egy adott telemetria-üzenetben.

- Érvényes Matcher feltétel-összehasonlítások:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Érvényes Matcher-feltételi célok:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

A következő példa a Matcher igaz értékre értékeli a `"Temperature"` adattípusának értékeként az összes érzékelő telemetria eseménynél. A felhasználó által definiált függvényekhez több egyezőt is létrehozhat, ha egy hitelesített HTTP POST-kérelmet küld a következőnek:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

JSON-törzstel:

```JSON
{
  "id": "3626464-f39b-46c0-d9b0c-436aysj55",
  "name": "Temperature Matcher",
  "spaceId": "YOUR_SPACE_IDENTIFIER",
  "conditions": [
    {
      "id": "ag7gq35cfu3-e15a-4e9c-6437-sj6w68sy44s",
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ]
}
```

| Value (Díj) | Csere erre |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | A példány által üzemeltetett kiszolgálói régió |

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása

Felhasználó által definiált függvény létrehozása magában foglalja egy többrészes HTTP-kérés létrehozását az Azure digitális Twins felügyeleti API-jai számára.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

Az egyeztetések létrehozása után töltse fel a függvény kódrészletét a következő hitelesített többrészes HTTP POST kérelemre:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Használja a következő törzset:

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

| Value (Díj) | Csere erre |
| --- | --- |
| USER_DEFINED_BOUNDARY | Egy többrészes tartalom határának neve |
| YOUR_SPACE_IDENTIFIER | A szóköz azonosítója  |
| YOUR_MATCHER_IDENTIFIER | A használni kívánt Matcher azonosítója |

1. Ellenőrizze, hogy a fejlécek tartalmazzák-e a következőket: `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`.
1. Győződjön meg arról, hogy a törzs egyrészes:

   - Az első rész tartalmazza a kötelező felhasználó által definiált függvény metaadatait.
   - A második rész tartalmazza a JavaScript számítási logikát.

1. A **USER_DEFINED_BOUNDARY** szakaszban cserélje le a **spaceId** (`YOUR_SPACE_IDENTIFIER`) és a **matchers** (`YOUR_MATCHER_IDENTIFIER`) értékeket.
1. Győződjön meg arról, hogy a JavaScript felhasználó által definiált függvény `Content-Type: text/javascript`ként van megadva.

### <a name="example-functions"></a>Függvények – példa

Állítsa be az érzékelő telemetria közvetlenül az érzékelőre az adattípusok **hőmérsékletének**`sensor.DataType`:

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

A **telemetria** paraméter a **SensorId** és az **üzenet** attribútumait jeleníti meg, amelyek egy érzékelő által küldött üzenetnek felelnek meg. A **executionContext** paraméter a következő attribútumokat teszi elérhetővé:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

A következő példában egy üzenet jelenik meg, ha az érzékelő telemetria olvasása meghaladja az előre meghatározott küszöbértéket. Ha a diagnosztikai beállítások engedélyezve vannak az Azure Digital Twins-példányon, a rendszer a felhasználó által definiált függvények naplófájljait is továbbítja:

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

A következő kód riasztást küld, ha a hőmérséklet szintje az előre meghatározott állandó fölé emelkedik:

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

Egy összetettebb, felhasználó által definiált Function code-minta esetén olvassa el a használati [útmutatót.](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

Hozzon létre egy szerepkör-hozzárendelést a felhasználó által definiált függvény számára a futtatásához. Ha nem létezik szerepkör-hozzárendelés a felhasználó által definiált függvényhez, akkor nem rendelkezik a megfelelő engedélyekkel a felügyeleti API-val való interakcióhoz, illetve a Graph-objektumokon végzett műveletek végrehajtásához. A felhasználó által definiált függvény által elvégezhető műveletek az Azure digitális Twins felügyeleti API-kon belül szerepköralapú hozzáférés-vezérléssel határozhatók meg és definiálhatók. A felhasználó által definiált függvények például bizonyos szerepkörök vagy bizonyos hozzáférés-vezérlési útvonalak megadásával korlátozhatók a hatókörben. További információért olvassa el a [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) dokumentációját.

1. A felhasználó által definiált függvényhez hozzárendelni kívánt szerepkör-azonosító lekérése az összes szerepkörhöz tartozó [System API lekérdezésével](./security-create-manage-role-assignments.md#retrieve-all-roles) . Ezt úgy teheti meg, hogy hitelesített HTTP GET kérelmet küld a következőnek:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Tartsa meg a kívánt szerepkör-azonosítót. A rendszer az alábbi JSON- **szerepkörazonosítónak** (`YOUR_DESIRED_ROLE_IDENTIFIER`) adja át.

1. a **objectId** (`YOUR_USER_DEFINED_FUNCTION_ID`) a felhasználó által definiált függvény azonosítója lesz, amely korábban lett létrehozva.
1. Keresse meg az **elérési út** (`YOUR_ACCESS_CONTROL_PATH`) értékét úgy, hogy lekérdezi a szóközt a `fullpath`.
1. Másolja a visszaadott `spacePaths` értéket. Ezt a következőt fogja használni. Hitelesített HTTP GET-kérés küldése a következőnek:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Value (Díj) | Csere erre |
    | --- | --- |
    | YOUR_SPACE_NAME | A használni kívánt hely neve |

1. Illessze be a visszaadott `spacePaths` értéket az **elérési útra** a felhasználó által definiált függvény szerepkör-hozzárendelés létrehozásához egy hitelesített http post-kérelem használatával:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    JSON-törzstel:

    ```JSON
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Value (Díj) | Csere erre |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | A kívánt szerepkör azonosítója |
    | YOUR_USER_DEFINED_FUNCTION_ID | A használni kívánt felhasználó által definiált függvény azonosítója |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | A felhasználó által definiált függvény típusát megadó azonosító (`UserDefinedFunctionId`) |
    | YOUR_ACCESS_CONTROL_PATH | A hozzáférés-vezérlés elérési útja |

>[!TIP]
> A felhasználó által definiált Function Management API-műveletekkel és-végpontokkal kapcsolatos további információkért olvassa el a [szerepkör-hozzárendelések létrehozása és kezelése](./security-create-manage-role-assignments.md) című cikket.

## <a name="send-telemetry-to-be-processed"></a>Feldolgozandó telemetria küldése

A térbeli intelligencia gráfban definiált érzékelő telemetria küld. A telemetria viszont elindítja a feltöltött felhasználó által definiált függvény végrehajtását. Az adatfeldolgozó felveszi a telemetria. Ezután létrejön egy végrehajtási terv a felhasználó által definiált függvény meghívásához.

1. Annak az érzékelőnek a beolvasása, amelyből az olvasó létrejött.
1. Attól függően, hogy az egyeztető kiértékelése sikeres volt-e, kérje le a társított felhasználó által definiált függvényeket.
1. Minden felhasználó által definiált függvény végrehajtása.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre Azure digitális Twins-végpontokat](./how-to-egress-endpoints.md) az események küldéséhez.

- Az Azure Digital Twins-útválasztással kapcsolatos további részletekért olvassa el az [útválasztási eseményeket és üzeneteket](./concepts-events-routing.md).

- Tekintse át az [ügyféloldali kódtár hivatkozási dokumentációját](./reference-user-defined-functions-client-library.md).