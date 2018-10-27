---
title: Felhasználó által definiált függvények használata az Azure digitális Twins |} A Microsoft Docs
description: A felhasználó által definiált függvények, matchers és szerepkör-hozzárendelések létrehozása az Azure digitális Twins útmutató arra az esetre.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: alinast
ms.openlocfilehash: 49566d21fa6897f5c1371bbea2bb602a393de66d
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50140789"
---
# <a name="how-to-use-user-defined-functions-in-azure-digital-twins"></a>Felhasználó által definiált függvények használata az Azure digitális Twins

[Felhasználó által definiált függvények](./concepts-user-defined-functions.md) engedélyezése a felhasználó egyéni logikát beérkező telemetriai üzeneteket és a térbeli graph metaadatok, a felhasználó eseményeket küld az előre definiált végpontjainak futtatásához. Ebben az útmutatóban végigvezetjük működő példát hőmérséklet események észleléséhez, és minden olvasási riasztás, amely meghaladja egy bizonyos hőmérséklet.

Az alábbi példákban `https://yourManagementApiUrl` URI-ját a digitális Twins API-k hivatkozik:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *Példánynév* | Az Azure digitális Twins-példány nevét |
| *yourLocation* | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

## <a name="client-library-reference"></a>Ügyféloldali kódtár – referencia

A felhasználó által definiált függvények futtatókörnyezetben segédmetódusokat elérhető funkciókat felsorolása a következő [ügyfél hivatkozási](#Client-Reference).

## <a name="create-a-matcher"></a>Hozzon létre egy megfeleltetőben megadott

Matchers olyan graph objektumok, amelyek meghatározzák, hogy mely felhasználó által definiált függvények mindenképpen végrehajtja az adott telemetriai üzenetet.

Érvényes megfeleltetőben megadott feltétel összehasonlítások:

- `Equals`
- `NotEquals`
- `Contains`

Érvényes megfeleltetőben megadott feltétel célok:

- `Sensor`
- `SensorDevice`
- `SensorSpace`

A következő példa megfeleltetőben megadott kiértékelik igaz értékre az összes érzékelő telemetriai esemény `Temperature` adatok típusú értékként. A felhasználó által definiált függvény több matchers hozhat létre.

```text
POST https://yourManagementApiUrl/api/v1.0/matchers
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
  "SpaceId": "yourSpaceIdentifier"
}
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *yourManagementApiUrl* | A felügyeleti API teljes URL-címe  |
| *yourSpaceIdentifier* | Melyik kiszolgáló régióban lévő üzemeltetett a példány |

## <a name="create-a-user-defined-function-udf"></a>Hozzon létre egy felhasználói függvény (UDF)

A matchers után létrehozott, és töltse fel a függvény kódrészletet a következő bejegyzés hívással:

> [!IMPORTANT]
> - A fejlécek, állítsa be a következő `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - A szervezetnek többrészes:
>   - Az első rész tárgya UDF szükséges metaadatokat.
>   - A második része a javascript számítási logika.
> - Cserélje le a `userDefinedBoundary` szakasz `SpaceId` és `Machers` GUID-azonosítói.

```plaintext
POST https://yourManagementApiUrl/api/v1.0/userdefinedfunctions with Content-Type: multipart/form-data; boundary="userDefinedBoundary"
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *yourManagementApiUrl* | A felügyeleti API teljes URL-címe  |

Szövegtörzs:

```plaintext
--userDefinedBoundary
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "yourSpaceIdentifier",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["yourMatcherIdentifier"]
}
--userDefinedBoundary
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--userDefinedBoundary--
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *yourSpaceIdentifier* | Hely azonosítója  |
| *yourMatcherIdentifier* | A használni kívánt megfeleltetőben megadott azonosítója |

### <a name="example-functions"></a>Példa funkciók

Állítsa be az érzékelő telemetriai típusú adatokat közvetlenül az érzékelő olvasása `Temperature`, amely `sensor.DataType`:

```javascript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

A `telemetry` paraméter tesz közzé egy `SensorId` és `Message`. A `executionContext` paramétert mutatja meg a következő attribútumokat:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

A következő példában azt fog rögzít egy üzenetet, ha az érzékelő telemetria olvasása idővesztesége egy előre meghatározott küszöbértéket. A diagnosztikai beállítások engedélyezve vannak a digitális Twins-példányon, ha a rendszer felhasználó által definiált függvények származó naplók is továbbítja:

```javascript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

Az alábbi kód értesítést aktivál, ha hőmérséklet szintje meghaladja a előre definiált konstans.

```javascript
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

Egy összetettebb UDF kódmintát talál [elérhető tárolóhelyek friss vezeték nélkül regisztrálja az UDF-ellenőrzése](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js)

## <a name="create-a-role-assignment"></a>Szerepkör-hozzárendelés létrehozása

A felhasználó által definiált függvény végrehajtása alatt a szerepkör-hozzárendelés létrehozása kell. Ha nem, nem lesz kommunikál a felügyeleti API-t gráfsémákkal műveleteket hajthat végre a megfelelő engedélyekkel. A végrehajtandó műveleteket végez a felhasználó által definiált függvény nem mentesülnek a szerepköralapú hozzáférés-vezérlést a digitális Twins felügyeleti API-k belül. Ezek is lehet korlátozott hatókör egyes szerepkörök vagy a megadott access control elérési útjait megadásával. További információkért lásd: [szerepköralapú hozzáférés-vezérlés](./security-role-based-access-control.md) dokumentációját.

- A szerepkörök le, és az UDF; hozzárendelni kívánt szerepkört Azonosítójának lekéréséhez Adja meg azt az alábbi szerepkör azonosítója.

```plaintext
GET https://yourManagementApiUrl/api/v1.0/system/roles
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *yourManagementApiUrl* | A felügyeleti API teljes URL-címe  |

- ObjectId lesz a korábban létrehozott UDF-azonosító
- Keresés `Path` szóközt azok teljes elérési útja, és másolja az lekérdezésével a `spacePaths` értéket. Illessze be az alábbi elérési út a UDF szerepkör-hozzárendelés létrehozása során

```plaintext
GET https://yourManagementApiUrl/api/v1.0/spaces?name=yourSpaceName&includes=fullpath
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *yourManagementApiUrl* | A felügyeleti API teljes URL-címe  |
| *yourSpaceName* | A használni kívánt terület neve |

```plaintext
POST https://yourManagementApiUrl/api/v1.0/roleassignments
{
  "RoleId": "yourDesiredRoleIdentifier",
  "ObjectId": "yourUserDefinedFunctionId",
  "ObjectIdType": "UserDefinedFunctionId",
  "Path": "yourAccessControlPath"
}
```

| Egyéni attribútum neve | Cserélje le |
| --- | --- |
| *yourManagementApiUrl* | A felügyeleti API teljes URL-címe  |
| *yourDesiredRoleIdentifier* | A kívánt szerepkör esetében az azonosító |
| *yourUserDefinedFunctionId* | Az UDF-ben használni kívánt azonosítója |
| *yourAccessControlPath* | A hozzáférés-vezérlési elérési útja |

## <a name="send-telemetry-to-be-processed"></a>A feldolgozásra telemetria küldése

Az érzékelő, a graph leírtak szerint létrehozott telemetriát a végrehajtás a felhasználó által definiált függvény feltöltött váltanak ki. A telemetriai adatokat a data-feldolgozó dolgozza fel, ha a felhasználó által definiált függvény meghívását egy végrehajtási terv jön létre.

1. A matchers a leolvasás jött létre, ki az érzékelő lekéréséhez.
1. Attól függően, mi matchers értékelése sikeres a társított felhasználó által definiált függvények beolvasni.
1. Minden felhasználó által definiált függvény végrehajtásához.

## <a name="client-reference"></a>Ügyfél-hivatkozása

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒ `space`

Egy helyet azonosító, a megadott kérdezi le a területet a diagramon.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `id`  | `guid` | Hely azonosítója |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒ `sensor`

Érzékelő azonosítónak a megadott lekérdezi a diagram az érzékelő.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `id`  | `guid` | érzékelő azonosítója |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒ `device`

Eszközazonosító, a megadott lekérdezi az eszköz a diagramon.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `id`  | `guid` | Eszközazonosító |

### <a name="getsensorvaluesensorid-datatype--value"></a>(sensorId, dataType) getSensorValue ⇒ `value`

Adja meg az érzékelő-azonosítót és jeho datovému typu, kérje le az aktuális értéket, az adott érzékelő.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `sensorId`  | `guid` | érzékelő azonosítója |
| `dataType`  | `string` | érzékelő adattípus |

### <a name="getspacevaluespaceid-valuename--value"></a>(spaceId, értéknév) getSpaceValue ⇒ `value`

A jelenlegi érték az adott hely tulajdonságnál adja meg a helyet azonosító és a neve, beolvasni.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId`  | `guid` | Hely azonosítója |
| `valueName` | `string` | lemezterület-tulajdonság neve |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>(sensorId, dataType) getSensorHistoryValues ⇒ `value[]`

Adja meg az érzékelő-azonosítót és jeho datovému typu, lekérni az adott érzékelő korábbi értékekkel.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `sensorId` | `guid` | érzékelő azonosítója |
| `dataType` | `string` | érzékelő adattípus |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>(spaceId, dataType) getSpaceHistoryValues ⇒ `value[]`

Adja meg a helyet azonosító és a neve, lekérni az adott tulajdonságnál a tárhelyen a korábbi értékekkel.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId` | `guid` | Hely azonosítója |
| `valueName` | `string` | lemezterület-tulajdonság neve |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒ `space[]`

Adja meg a helyet azonosító, beolvasni az, hogy a fölérendelt hely alárendelt címterét.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId` | `guid` | Hely azonosítója |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors(spaceId) ⇒ `sensor[]`

Adja meg a helyet azonosító, beolvasni a gyermek érzékelőket, hogy a fölérendelt hely számára.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId` | `guid` | Hely azonosítója |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒ `device[]`

Adja meg a helyet azonosító, lekéréséhez, hogy a fölérendelt hely a gyermek eszközöket.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId` | `guid` | Hely azonosítója |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒ `sensor[]`

Adja meg egy eszközazonosítót, beolvasni a gyermek érzékelők szülő eszköznek.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `deviceId` | `guid` | Eszközazonosító |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒ `space`

Adja meg a helyet azonosító, beolvasni a fölérendelt hely.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `childSpaceId` | `guid` | Hely azonosítója |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

Adja meg az érzékelő azonosítót, beolvasni a fölérendelt hely.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `childSensorId` | `guid` | érzékelő azonosítója |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒ `space`

Adja meg egy eszközazonosítót, beolvasni a fölérendelt hely.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `childDeviceId` | `guid` | Eszközazonosító |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

Adja meg az érzékelő azonosítót, beolvasni a hozzá tartozó szülő eszköz.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `childSensorId` | `guid` | érzékelő azonosítója |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>(spaceId, propertyName) getSpaceExtendedProperty ⇒ `extendedProperty`

Adja meg a helyet azonosító, lekérdezni a tulajdonság és az értékét a területet.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId` | `guid` | Hely azonosítója |
| `propertyName` | `string` | lemezterület-tulajdonság neve |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>(sensorId, propertyName) getSensorExtendedProperty ⇒ `extendedProperty`

Adja meg az érzékelő azonosítót, lekérdezni a tulajdonság és annak értékét az érzékelő.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `sensorId` | `guid` | érzékelő azonosítója |
| `propertyName` | `string` | érzékelő tulajdonság neve |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>(deviceId, propertyName) getDeviceExtendedProperty ⇒ `extendedProperty`

Adja meg egy eszközazonosítót, lekérdezni a tulajdonság és annak értékét az eszközön.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `deviceId` | `guid` | Eszközazonosító |
| `propertyName` | `string` | eszköz tulajdonság neve |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue (sensorId, dataType, érték)

Beállít egy értéket a megadott adattípus-érzékelő objektumon.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `sensorId` | `guid` | érzékelő azonosítója |
| `dataType`  | `string` | érzékelő adattípus |
| `value`  | `string` | érték |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue (spaceId, dataType, érték)

Beállít egy értéket a megadott adattípus-terület objektumon.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `spaceId` | `guid` | Hely azonosítója |
| `dataType` | `string` | adattípus |
| `value` | `string` | érték |

### <a name="logmessage"></a>log(Message)

A felhasználó által definiált függvényen belül a következő üzenetet naplózza.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `message` | `string` | üzenet be kell jelentkeznie |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, hasznos adat)

Küld értesítő üzenet egyéni szövegében küldik.

**Milyen**: globális függvény

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `topologyObjectId`  | `guid` | Graph-objektum azonosítóját (például. terület / érzékelő/Device ID)|
| `topologyObjectType`  | `string` | (például. terület / érzékelő vagy eszköz)|
| `payload`  | `string` | az értesítéssel elküldendő JSON-adattartalmat |

## <a name="return-types"></a>Návratové Typy

Az alábbiakban a fenti ügyfél hivatkozási visszatérési objektumait leíró modelleket.

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

Az aktuális hely a fölérendelt hely adja vissza.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

A gyermeket érzékelőktől az aktuális hely adja vissza.

#### <a name="childdevices--device"></a>ChildDevices() ⇒ `device[]`

Az aktuális hely eszközöket adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

A kiterjesztett tulajdonság és az aktuális hely értékét adja vissza.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `propertyName` | `string` | a bővített tulajdonság neve |

#### <a name="valuevaluename--value"></a>Value(VALUENAME) ⇒ `value`

Az aktuális hely értékét adja vissza.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `valueName` | `string` | az érték neve |

#### <a name="historyvaluename--value"></a>History(VALUENAME) ⇒ `value[]`

Az aktuális hely korábbi értékeit adja vissza.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `valueName` | `string` | az érték neve |

#### <a name="notifypayload"></a>Notify(Payload)

A megadott hasznos adattal értesítést küld.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `payload` | `string` | Az értesítés foglalandó JSON-adattartalmat |

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

A szülő hely az aktuális eszköz adja vissza.

#### <a name="childsensors--sensor"></a>ChildSensors() ⇒ `sensor[]`

A gyermeket érzékelőktől az aktuális eszköz adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

A kiterjesztett tulajdonság és az aktuális eszköz értékét adja vissza.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `propertyName` | `string` | a bővített tulajdonság neve |

#### <a name="notifypayload"></a>Notify(Payload)

A megadott hasznos adattal értesítést küld.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `payload` | `string` | Az értesítés foglalandó JSON-adattartalmat |

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

A szülő hely az aktuális érzékelő adja vissza.

#### <a name="device--device"></a>Device() ⇒ `device`

A szülő eszköz az aktuális érzékelő adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒ `extendedProperty`

A kiterjesztett tulajdonság és az aktuális érzékelő értékét adja vissza.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `propertyName` | `string` | a bővített tulajdonság neve |

#### <a name="value--value"></a>Value() – ⇒ `value`

Az aktuális érzékelő értékét adja vissza.

#### <a name="history--value"></a>History() ⇒ `value[]`

A korábbi az aktuális érzékelő-értékeit adja eredményül.

#### <a name="notifypayload"></a>Notify(Payload)

A megadott hasznos adattal értesítést küld.

| Param  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| `payload` | `string` | Az értesítés foglalandó JSON-adattartalmat |

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

Megtudhatja, hogyan küldhet eseményeket a digitális Twins végpontok létrehozása, olvasása [digitális Twins létrehozása végpontok](how-to-egress-endpoints.md).

Digitális Twins végpontokon további részletekért olvassa el a [tudjon meg többet a végpontok](concepts-events-routing.md).
