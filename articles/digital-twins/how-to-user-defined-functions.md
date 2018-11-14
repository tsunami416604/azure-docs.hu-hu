---
title: Felhasználó által definiált függvények használata az Azure digitális Twins |} A Microsoft Docs
description: A szerepkör-hozzárendelések, matchers és felhasználó által definiált függvények létrehozása az Azure digitális Twins útmutató arra az esetre.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: alinast
ms.openlocfilehash: 33190472215e7a02b94951a73054ebe3e1994e54
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623910"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények használata az Azure digitális Twins

[Felhasználó által definiált függvények](./concepts-user-defined-functions.md) (UDF) engedélyezése a felhasználó egyéni logikát futtatásához a beérkező telemetriai üzeneteket és a térbeli graph metaadatokat. A felhasználó ezután előre definiált végpontjainak is küldhet eseményeket. Ez az útmutató végigvezeti egy működő példát hőmérséklet események észleléséhez, és minden olvasási riasztás, amely meghaladja egy bizonyos hőmérséklet.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## <a name="client-library-reference"></a>Ügyféloldali kódtár – referencia

A függvényeket, amelyek állnak rendelkezésre, a felhasználó által definiált függvények futtatókörnyezetben segítő szerepelnek a [ügyfél hivatkozási](#Client-Reference) szakaszban.

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

A következő példa megfeleltetőben megadott bármely érzékelő telemetriai esemény az IGAZ értéket ad vissza `"Temperature"` adatok típusú értékként. A felhasználó által definiált függvény több matchers hozhat létre:

```plaintext
POST yourManagementApiUrl/matchers
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

| Az érték | Csere erre |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

## <a name="create-a-user-defined-function-udf"></a>Hozzon létre egy felhasználói függvény (UDF)

A matchers létrehozása után töltse fel a függvény kódrészlet az alábbi **POST** hívása:

> [!IMPORTANT]
> - A fejlécek, állítsa be a következő `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - A szervezetnek többrészes:
>   - Az első rész az UDF szükséges metaadatokat szól.
>   - A második része a JavaScript számítási logika.
> - Az a **userDefinedBoundary** szakaszban, cserélje le a **SpaceId** és **Machers** értékeket.

```plaintext
POST yourManagementApiUrl/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Paraméter értéke | Csere erre |
| --- | --- |
| *userDefinedBoundary* | A többrészes tartalom határcsoport neve |

### <a name="body"></a>Törzs

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Az érték | Csere erre |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Hely azonosítója  |
| YOUR_MATCHER_IDENTIFIER | A használni kívánt megfeleltetőben megadott azonosítója |

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

Az UDF összetettebb kódja minta [ellenőrizze a rendelkezésre álló szóközöket egy friss vezeték nélkül regisztrálja az UDF-](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

A felhasználó által definiált függvény alatt szeretné futtatni a szerepkör-hozzárendelés létrehozása kell. Ha nem, hogy azt nem kell kommunikál a felügyeleti API-t gráfsémákkal műveleteket hajthat végre a megfelelő engedélyekkel. A végrehajtandó műveleteket végez a felhasználó által definiált függvény nem érvényes a szerepköralapú hozzáférés-vezérlés az Azure digitális Twins felügyeleti API-k belül. Akkor is lehet korlátozott hatókör egyes szerepkörök vagy a megadott access control elérési útjait megadásával. További információkért lásd: [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) dokumentációját.

1. A szerepkörök lekérdezését, és az UDF hozzárendelni kívánt szerepkört Azonosítójának lekéréséhez. Adja át azt, hogy **RoleId**:

    ```plaintext
    GET yourManagementApiUrl/system/roles
    ```

1. **ObjectId** lesz a korábban létrehozott UDF-azonosító.
1. Keresse meg az értéket a **elérési** a szóközt az lekérdezésével `fullpath`.
1. Másolja a visszaadott `spacePaths` értéket. Az alábbi kódot, amely használni:

    ```plaintext
    GET yourManagementApiUrl/spaces?name=yourSpaceName&includes=fullpath
    ```

    | Paraméter értéke | Csere erre |
    | --- | --- |
    | *yourSpaceName* | A használni kívánt terület neve |

1. Illessze be a visszaadott `spacePaths` be érték **elérési út** UDF szerepkör-hozzárendelés létrehozásához:

    ```plaintext
    POST yourManagementApiUrl/roleassignments
    {
      "RoleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "ObjectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "ObjectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "Path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Az érték | Csere erre |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | A kívánt szerepkör esetében az azonosító |
    | YOUR_USER_DEFINED_FUNCTION_ID | Az UDF-ben használni kívánt azonosítója |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | Az UDF típusát megadó azonosítója |
    | YOUR_ACCESS_CONTROL_PATH | A hozzáférés-vezérlési elérési útja |

## <a name="send-telemetry-to-be-processed"></a>A feldolgozásra telemetria küldése

Az érzékelő, a graph leírtak szerint létrehozott telemetriát a Futtatás, a felhasználó által definiált függvény feltöltött aktivál. A data-feldolgozó szerzi be a telemetriát. Ezután a felhasználó által definiált függvény meghívását futtatási csomag jön létre.

1. A matchers a leolvasás jött létre, ki az érzékelő lekéréséhez.
1. Attól függően, mi matchers értékelése sikeres a társított felhasználó által definiált függvények beolvasni.
1. Minden felhasználó által definiált függvény futtatása.

## <a name="client-reference"></a>Ügyfél-hivatkozása

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Adja meg a helyet azonosító, ez a függvény átveszi a terület a grafikon.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Hely azonosítója |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Adja meg az érzékelő azonosítót, ez a függvény átveszi az érzékelő a diagramon.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | érzékelő azonosítója |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Adja meg egy eszközazonosítót, ez a függvény átveszi az eszköz a diagramon.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Eszközazonosító |

### <a name="getsensorvaluesensorid-datatype--value"></a>(sensorId, dataType) getSensorValue ⇒ `value`

Adja meg az érzékelő-azonosítót és jeho datovému typu, ez a függvény lekéri a jelenlegi érték az adott érzékelő.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | érzékelő azonosítója |
| *Adattípus*  | `string` | érzékelő adattípus |

### <a name="getspacevaluespaceid-valuename--value"></a>(spaceId, értéknév) getSpaceValue ⇒ `value`

Adja meg a helyet azonosító és a neve, ez a függvény kérdezi le a hely aktuális értékkel.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Hely azonosítója |
| *Értéknév* | `string` | lemezterület-tulajdonság neve |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>(sensorId, dataType) getSensorHistoryValues ⇒ `value[]`

Adja meg az érzékelő-azonosítót és jeho datovému typu, ez a függvény átveszi az adott érzékelő korábbi értékekkel.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | érzékelő azonosítója |
| *Adattípus* | `string` | érzékelő adattípus |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>(spaceId, dataType) getSpaceHistoryValues ⇒ `value[]`

Adja meg a helyet azonosító és a neve, ez a függvény átveszi az adott tulajdonságnál a tárhelyen a korábbi értékekkel.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Hely azonosítója |
| *Értéknév* | `string` | lemezterület-tulajdonság neve |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Adja meg a helyet azonosító, ez a függvény átveszi az, hogy a fölérendelt hely alárendelt címterét.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Hely azonosítója |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Adja meg a helyet azonosító, ez a függvény átveszi a gyermek érzékelő számára, hogy a fölérendelt hely.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Hely azonosítója |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Adja meg a helyet azonosító, ez a függvény átveszi a, hogy a fölérendelt hely alárendelt eszközöket.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Hely azonosítója |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Adja meg egy eszközazonosítót, ez a függvény átveszi a gyermek érzékelők szülő eszköz.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *az eszközazonosító* | `guid` | Eszközazonosító |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Adja meg a helyet azonosító, ez a függvény átveszi a fölérendelt hely.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Hely azonosítója |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Adja meg az érzékelő azonosítót, ez a függvény átveszi a fölérendelt hely.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | érzékelő azonosítója |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Adja meg egy eszközazonosítót, ez a függvény átveszi a fölérendelt hely.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Eszközazonosító |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Adja meg az érzékelő azonosítót, ez a függvény átveszi a szülő eszköz.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | érzékelő azonosítója |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>(spaceId, propertyName) getSpaceExtendedProperty ⇒ `extendedProperty`

Adja meg a helyet azonosító, ez a függvény átveszi a tulajdonságot, és annak értékét a területet.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Hely azonosítója |
| *a propertyName* | `string` | lemezterület-tulajdonság neve |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>(sensorId, propertyName) getSensorExtendedProperty ⇒ `extendedProperty`

Adja meg az érzékelő azonosítót, ez a függvény átveszi a tulajdonságot, és annak értékét az érzékelő.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | érzékelő azonosítója |
| *a propertyName* | `string` | érzékelő tulajdonság neve |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>(deviceId, propertyName) getDeviceExtendedProperty ⇒ `extendedProperty`

Adja meg egy eszközazonosítót, ez a függvény átveszi a tulajdonságot, és annak értékét az eszközről.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *az eszközazonosító* | `guid` | Eszközazonosító |
| *a propertyName* | `string` | eszköz tulajdonság neve |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, dataType, érték)

Ez a függvény a megadott adattípus-érzékelő objektumon beállít egy értéket.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | érzékelő azonosítója |
| *Adattípus*  | `string` | érzékelő adattípus |
| *value*  | `string` | Érték |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, dataType, érték)

Ez a függvény a megadott adattípus-terület objektumon beállít egy értéket.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Hely azonosítója |
| *Adattípus* | `string` | Adattípus |
| *value* | `string` | Érték |

### <a name="logmessage"></a>log(Message)

Ez a függvény a felhasználó által definiált függvényen belül a következő üzenetet naplózza.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *üzenet* | `string` | üzenet be kell jelentkeznie |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, hasznos adat)

Ez a függvény küld értesítő üzenet egyéni szövegében küldik.

**Milyen**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Graph-objektum azonosítója. Példák: a lemezterület, érzékelő és eszközazonosító.|
| *topologyObjectType*  | `string` | Példák érzékelő- és.|
| *hasznos adat*  | `string` | A JSON-adattartalom-értesítés küldésének. |

## <a name="return-types"></a>Návratové typy

Az alábbi minták ismertetik az előző ügyfél hivatkozási visszatérési objektumokat.

### <a name="space"></a>Űr

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000000",
  "Name": "Space",
  "FriendlyName": "Conference Room",
  "TypeId": 0,
  "ParentSpaceId": "00000000-0000-0000-0000-000000000001",
  "SubtypeId": 0
}
```

### <a name="space-methods"></a>Lemezterület-metódusok

#### <a name="parent--space"></a>Parent() ⇒ `space`

Ez a függvény az aktuális hely a fölérendelt hely adja vissza.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Ez a függvény az aktuális hely érzékelők a gyermek adja vissza.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Ez a függvény az aktuális lemezterület-eszközeinek a gyermek adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Ez a funkció a bővített tulajdonság és az aktuális hely értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *a propertyName* | `string` | a bővített tulajdonság neve |

#### <a name="valuevaluename--value"></a>Value(VALUENAME) ⇒ `value`

Ez a függvény az aktuális hely értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *Értéknév* | `string` | az érték neve |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Ez a függvény az aktuális hely korábbi értékeit adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *Értéknév* | `string` | az érték neve |

#### <a name="notifypayload"></a>Notify(Payload)

Ez a függvény a megadott hasznos adattal értesítést küld.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *hasznos adat* | `string` | Az értesítés foglalandó JSON-adattartalmat |

### <a name="device"></a>Eszköz

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000002",
  "Name": "Device",
  "FriendlyName": "Temperature Sensing Device",
  "Description": "This device contains a sensor that captures temperature readings.",
  "Type": "None",
  "Subtype": "None",
  "TypeId": 0,
  "SubtypeId": 0,
  "HardwareId": "ABC123",
  "GatewayId": "ABC",
  "SpaceId": "00000000-0000-0000-0000-000000000000"
}
```

### <a name="device-methods"></a>Eszközmetódusok

#### <a name="parent--space"></a>Parent() ⇒ `space`

Ez a függvény az aktuális eszköz a fölérendelt hely adja vissza.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

Ez a függvény az aktuális eszköz érzékelők a gyermek adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Ez a funkció a bővített tulajdonság és az aktuális eszköz értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *a propertyName* | `string` | a bővített tulajdonság neve |

#### <a name="notifypayload"></a>Notify(Payload)

Ez a függvény a megadott hasznos adattal értesítést küld.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *hasznos adat* | `string` | Az értesítés foglalandó JSON-adattartalmat |

### <a name="sensor"></a>Érzékelő

```JSON
{
  "Id": "00000000-0000-0000-0000-000000000003",
  "Port": "30",
  "PollRate": 3600,
  "DataType": "Temperature",
  "DataSubtype": "None",
  "Type": "Classic",
  "PortType": "None",
  "DataUnitType": "FahrenheitTemperature",
  "SpaceId": "00000000-0000-0000-0000-000000000000",
  "DeviceId": "00000000-0000-0000-0000-000000000001",
  "PortTypeId": 0,
  "DataUnitTypeId": 0,
  "DataTypeId": 0,
  "DataSubtypeId": 0,
  "TypeId": 0  
}
```

### <a name="sensor-methods"></a>Érzékelő módszerek

#### <a name="space--space"></a>Space() ⇒ `space`

Ez a függvény visszaad a fölérendelt hely áramerősség-érzékelő.

#### <a name="device--device"></a>Device() ⇒ `device`

Ez a függvény az aktuális érzékelő szülő eszköz adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

Ez a funkció a bővített tulajdonság és az aktuális érzékelő értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *a propertyName* | `string` | a bővített tulajdonság neve |

#### <a name="value--value"></a>Value() – ⇒ `value`

Ez a függvény az aktuális érzékelő értékét adja vissza.

#### <a name="history--value"></a>History() ⇒ `value[]`

Ez a függvény az aktuális érzékelő korábbi értékét adja vissza.

#### <a name="notifypayload"></a>Notify(Payload)

Ez a függvény a megadott hasznos adattal értesítést küld.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *hasznos adat* | `string` | Az értesítés foglalandó JSON-adattartalmat |

### <a name="value"></a>Érték

```JSON
{
  "DataType": "Temperature",
  "Value": "70",
  "CreatedTime": ""
}
```

### <a name="extended-property"></a>A kiterjesztett tulajdonság

```JSON
{
  "Name": "OccupancyStatus",
  "Value": "Occupied"
}
```

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan [létrehozása az Azure digitális Twins végpontok](how-to-egress-endpoints.md) események küldéséhez.

- Az Azure digitális Twins végpontokon további részletekért tekintse meg [további információk a végpontok](concepts-events-routing.md).
