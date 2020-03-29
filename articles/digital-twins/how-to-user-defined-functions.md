---
title: Felhasználó által definiált függvények létrehozása - az Azure Digital Twins-ben | Microsoft dokumentumok
description: Felhasználó által definiált függvények, egyeztetők és szerepkör-hozzárendelések létrehozása az Azure Digital Twins alkalmazásban.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: 232d85789c25e905873286eba6fda32c327a6e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276938"
---
# <a name="how-to-create-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények létrehozása az Azure Digital Twins-ben

[A felhasználó által definiált függvények](./concepts-user-defined-functions.md) lehetővé teszik a felhasználók számára, hogy egyéni logikát konfiguráljanak a bejövő telemetriai üzenetekből és a térbeli gráf metaadataiból. A felhasználók előre definiált végpontokra is küldhetnek [eseményeket.](./how-to-egress-endpoints.md)

Ez az útmutató végigvezeti egy példa bemutatja, hogyan észleli és riasztást minden olyan olvasás, amely meghaladja a kapott hőmérsékleti események egy bizonyos hőmérséklet.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Ügyféltár hivatkozása

A felhasználó által definiált függvények futásidejű segítő metódusaként elérhető függvények az [ügyféltár referenciadokumentumában](./reference-user-defined-functions-client-library.md) jelennek meg.

## <a name="create-a-matcher"></a>Matcher létrehozása

A matchers olyan gráfobjektumok, amelyek meghatározzák, hogy milyen felhasználó által definiált függvények futnak egy adott telemetriai üzenethez.

- Érvényes egyező feltétel összehasonlítások:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Érvényes egyező feltétel célok:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

A következő példa matcher kiértékeli, hogy igaz `"Temperature"` minden érzékelő telemetriai esemény adattípus értékeként. Egy felhasználó által definiált függvényben több egyeztetőt is létrehozhat, ha hitelesített HTTP POST-kérelmet küld a következőknek:

```URL
YOUR_MANAGEMENT_API_URL/matchers
```

JSON-testtel:

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

| Érték | Csere erre |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | A példány mely kiszolgálórégiójában található |

## <a name="create-a-user-defined-function"></a>Felhasználó által meghatározott függvény létrehozása

A felhasználó által definiált függvény létrehozása magában foglalja egy többrészes HTTP-kérelem az Azure digitális twins felügyeleti API-k.

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

A matchers létrehozása után töltse fel a függvénykódrészletet a következő hitelesített többrészes HTTP POST-kérelemmel:

```URL
YOUR_MANAGEMENT_API_URL/userdefinedfunctions
```

Használja a következő testet:

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
| USER_DEFINED_BOUNDARY | Többrészes tartalomhatár neve |
| YOUR_SPACE_IDENTIFIER | A térazonosító  |
| YOUR_MATCHER_IDENTIFIER | A használni kívánt matcher azonosítója |

1. Ellenőrizze, hogy a `Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`fejlécek a következőket tartalmazzák-e: .
1. Ellenőrizze, hogy a test többrészes-e:

   - Az első rész tartalmazza a szükséges felhasználó által definiált függvény metaadatokat.
   - A második rész tartalmazza a JavaScript számítási logikát.

1. A **USER_DEFINED_BOUNDARY** szakaszban cserélje le`YOUR_SPACE_IDENTIFIER`a **szóközazonosító** ( ) és **a matchers** (`YOUR_MATCHER_IDENTIFIER`) értékeket.
1. Ellenőrizze, hogy a JavaScript-felhasználó által `Content-Type: text/javascript`definiált függvény a rendszer szerint van-e megadva.

### <a name="example-functions"></a>Példa függvények

Állítsa be az érzékelő telemetriai olvasását közvetlenül `sensor.DataType`az érzékelőhöz a **Hőmérséklet**adattípussal, amely a következőképpen szól:

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

A **telemetriai** paraméter elérhetővé teszi a **SensorId** és **az Üzenet** attribútumokat, amelyek egy érzékelő által küldött üzenetnek felelnek meg. A **executionContext** paraméter a következő attribútumokat teszi elérhetővé:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

A következő példában naplózunk egy üzenetet, ha az érzékelő telemetriai olvasása meghaladja az előre meghatározott küszöbértéket. Ha a diagnosztikai beállítások engedélyezve vannak az Azure Digital Twins példányon, a felhasználó által definiált függvények naplói is továbbításra kerülnek:

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

A következő kód értesítést vált ki, ha a hőmérséklet imperációs szint az előre meghatározott állandó fölé emelkedik:

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

Összetettebb, felhasználó által definiált függvénykód-minta, olvassa el a [Kihasználtság rövid útmutató](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

Hozzon létre egy szerepkör-hozzárendelést a felhasználó által definiált függvényhez. Ha nincs szerepkör-hozzárendelés a felhasználó által definiált függvényhez, nem rendelkezik a megfelelő engedélyekkel a Felügyeleti API-val való interakcióhoz, vagy hozzáférhet a diagramobjektumokon végrehajtott műveletekhez. A felhasználó által definiált függvények által végrehajtott műveletek et az Azure Digital Twins Management API-k szerepköralapú hozzáférés-vezérlése határozza meg és definiálja. A felhasználó által definiált függvények hatóköre például korlátozható bizonyos szerepkörök vagy bizonyos hozzáférés-vezérlési útvonalak megadásával. További információt a [szerepköralapú hozzáférés-vezérlési](./security-role-based-access-control.md) dokumentációban talál.

1. [A rendszer API-lekérdezése](./security-create-manage-role-assignments.md#retrieve-all-roles) az összes szerepkör höz a felhasználó által definiált függvényhez rendelni kívánt szerepkör-azonosító lehívásához. Ehhez hozzon egy hitelesített HTTP GET kérést:

    ```URL
    YOUR_MANAGEMENT_API_URL/system/roles
    ```
   Tartsa meg a kívánt szerepkör-azonosítót. Ez lesz át, mint a JSON body`YOUR_DESIRED_ROLE_IDENTIFIER`attribútum **roleId** ( ) alatt.

1. **objectId** `YOUR_USER_DEFINED_FUNCTION_ID`( ) lesz a korábban létrehozott felhasználó által definiált függvényazonosító.
1. Keresse meg az **elérési út** (`YOUR_ACCESS_CONTROL_PATH`) `fullpath`értékét a szóközök lekérdezésével a segítségével.
1. Másolja a `spacePaths` visszaadott értéket. Ezt fogja használni az alábbiakban. Hitelesített HTTP GET-kérelem kérése:

    ```URL
    YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Érték | Csere erre |
    | --- | --- |
    | YOUR_SPACE_NAME | A használni kívánt hely neve |

1. Illessze `spacePaths` be a visszaadott értéket az **elérési útra** egy felhasználó által definiált függvényszerepkör-hozzárendelés létrehozásához, ha hitelesített HTTP POST-kérelmet küld a következőknek:

    ```URL
    YOUR_MANAGEMENT_API_URL/roleassignments
    ```
    JSON-testtel:

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
    | YOUR_DESIRED_ROLE_IDENTIFIER | A kívánt szerepkör azonosítója |
    | YOUR_USER_DEFINED_FUNCTION_ID | A használni kívánt felhasználó által definiált függvény azonosítója |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | A felhasználó által definiált függvénytípust`UserDefinedFunctionId`( ) meghatározó azonosító |
    | YOUR_ACCESS_CONTROL_PATH | A hozzáférés-vezérlés iut. |

>[!TIP]
> Olvassa el a [szerepkör-hozzárendelések létrehozása és kezelése című cikket,](./security-create-manage-role-assignments.md) ha további információt szeretne a felhasználó által definiált függvényfelügyeleti API-műveletekről és végpontokról.

## <a name="send-telemetry-to-be-processed"></a>Telemetriai adatok küldése feldolgozásra

A térintelligencia-grafikonban definiált érzékelő telemetriai adatokat küld. A telemetriai adatok pedig elindítja a felhasználó által definiált függvény, amely feltöltött végrehajtása. Az adatfeldolgozó felveszi a telemetriai adatokat. Ezután létrejön egy végrehajtási terv a felhasználó által definiált függvény meghívásához.

1. Az értékleolvasáshoz való egyeztetők lekérése.
1. Attól függően, hogy milyen egyezők kiértékelése sikerült, olvassa be a társított felhasználó által definiált függvényeket.
1. Minden felhasználó által definiált függvény végrehajtása.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [hozhat létre Azure Digital Twins-végpontokat,](./how-to-egress-endpoints.md) amelyeknek eseményeket küldhet.

- Az Azure Digital Twins útválasztásával kapcsolatos további részletekért olvassa el [az Útválasztási események és üzenetek](./concepts-events-routing.md)című részt.

- Tekintse át az [ügyféltár referenciadokumentációját.](./reference-user-defined-functions-client-library.md)