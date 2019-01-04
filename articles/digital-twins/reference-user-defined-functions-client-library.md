---
title: Az Azure digitális Twins, felhasználó által definiált függvények ügyféloldali kódtár – referencia |} A Microsoft Docs
description: Az Azure digitális Twins, felhasználó által definiált függvények ügyféloldali kódtár – referencia.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
ms.openlocfilehash: d309765e6c4b0b0cc8a7e92977134d4135a69a13
ms.sourcegitcommit: fd488a828465e7acec50e7a134e1c2cab117bee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2019
ms.locfileid: "53995484"
---
# <a name="user-defined-functions-client-library-reference"></a>Felhasználó által definiált függvények ügyféloldali kódtár – referencia

Ez a dokumentum az Azure digitális Twins felhasználó által definiált függvények ügyféloldali kódtárral kapcsolatos referenciainformációk nyújt.

## <a name="helper-methods"></a>Segédmetódusokat

Az ügyféloldali kódtár a gyakran használt műveletek segédmetódusokat határozza meg.

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

Az ügyfél hivatkozási segédmetódusokat által visszaadott válasz modellek az alábbiakban tekintheti át.

### <a name="space"></a>Űr

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "Space",
  "friendlyName": "Conference Room",
  "typeId": 0,
  "parentSpaceId": "00000000-0000-0000-0000-000000000001",
  "subtypeId": 0
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
  "id": "00000000-0000-0000-0000-000000000002",
  "name": "Device",
  "friendlyName": "Temperature Sensing Device",
  "description": "This device contains a sensor that captures temperature readings.",
  "type": "None",
  "subtype": "None",
  "typeId": 0,
  "subtypeId": 0,
  "hardwareId": "ABC123",
  "gatewayId": "ABC",
  "spaceId": "00000000-0000-0000-0000-000000000000"
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
  "id": "00000000-0000-0000-0000-000000000003",
  "port": "30",
  "pollRate": 3600,
  "dataType": "Temperature",
  "dataSubtype": "None",
  "type": "Classic",
  "portType": "None",
  "dataUnitType": "FahrenheitTemperature",
  "spaceId": "00000000-0000-0000-0000-000000000000",
  "deviceId": "00000000-0000-0000-0000-000000000001",
  "portTypeId": 0,
  "dataUnitTypeId": 0,
  "dataTypeId": 0,
  "dataSubtypeId": 0,
  "typeId": 0  
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
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>A kiterjesztett tulajdonság

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>További lépések

- Ismerje meg [Azure digitális Twins felhasználó által definiált függvények](./concepts-user-defined-functions.md).

- Ismerje meg, [hogyan hozhat létre felhasználó által definiált függvények](./how-to-user-defined-functions.md).

- Ismerje meg, [felhasználó által definiált függvények hibakeresése](./how-to-diagnose-user-defined-functions.md).
