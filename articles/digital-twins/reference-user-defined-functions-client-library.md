---
title: Felhasználó által definiált függvények ügyfélkönyvtárának hivatkozása - Azure Digital Twins | Microsoft dokumentumok
description: Az Azure Digital Twins felhasználó által definiált függvények ügyfélkönyvtár referenciadokumentációja.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 01/17/2020
ms.custom: seodec18
ms.openlocfilehash: bd6095daca51ddca0cfb4b34ca86e763df9a3d02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76276816"
---
# <a name="user-defined-functions-client-library-reference"></a>Felhasználó által definiált függvények ügyféltárának hivatkozása

Ez a dokumentum az Azure Digital Twins felhasználó által definiált függvényügyfél-kódtár referenciaadatait tartalmazza.

## <a name="helper-methods"></a>Segítő módszerek

Az ügyféltár a gyakran használt műveletek hez határozza meg a segítő módszereket.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata(id) ⇒`space`

Térazonosítóval a függvény lekéri a helyet a grafikonról.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Térazonosító |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata(id) ⇒`sensor`

Adott egy érzékelő azonosító, ez a függvény lekéri az érzékelőt a grafikonról.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Érzékelő azonosítója |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata(id) ⇒`device`

Adott eszközazonosító, ez a függvény lekéri az eszközt a grafikonról.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Eszközazonosító |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue(sensorId, dataType) ⇒`value`

Adott érzékelőazonosító és annak adattípusa, ez a függvény lekéri az adott érzékelő aktuális értékét.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Érzékelő azonosítója |
| *Adattípus*  | `string` | Érzékelő adattípusa |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue(spaceId, valueName) ⇒`value`

Térazonosító és az érték név alapján ez a függvény az adott területtulajdonság aktuális értékét olvassa be.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Térazonosító |
| *valueName* | `string` | Szóköz tulajdonság neve |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues(sensorId, dataType) ⇒`value[]`

Adott érzékelőazonosító és annak adattípusa, ez a függvény lekéri az érzékelő előzményértékeit.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Érzékelő azonosítója |
| *Adattípus* | `string` | Érzékelő adattípusa |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues(spaceId, dataType) ⇒`value[]`

Térazonosító és az érték neve alapján ez a függvény lekéri a tulajdonság előzményértékeit a térben.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Térazonosító |
| *valueName* | `string` | Szóköz tulajdonság neve |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces(spaceId) ⇒`space[]`

Térazonosító t adott meg, ez a függvény lekéri az adott szülőterület gyermekteréit.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Térazonosító |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId) ⇒`sensor[]`

Térazonosító val, ez a függvény lekéri az adott szülőterület gyermekérzékelőit.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Térazonosító |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices(spaceId) ⇒`device[]`

Térazonosító val, ez a függvény lekéri az adott szülőterület gyermekeszközeit.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Térazonosító |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors(deviceId) ⇒`sensor[]`

Adott eszközazonosító, ez a függvény lekéri a szülőeszköz gyermekérzékelőit.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Eszközazonosító |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace(childSpaceId) ⇒`space`

Térazonosító t adott meg, ez a függvény beolvassa a szülőterületét.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Térazonosító |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒`space`

Adott egy érzékelőazonosító, ez a függvény lekéri a szülőterületet.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Érzékelő azonosítója |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace(childDeviceId) ⇒`space`

Adott eszközazonosító, ez a függvény lekéri a szülőterületet.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Eszközazonosító |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒`space`

Adott egy érzékelőazonosító, ez a függvény lekéri a szülőeszközét.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Érzékelő azonosítója |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty(spaceId, propertyName) ⇒`extendedProperty`

Térazonosítót adva ez a függvény lekéri a tulajdonságot és annak értékét a térből.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Térazonosító |
| *tulajdonságNeve* | `string` | Szóköz tulajdonság neve |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty(sensorId, propertyName) ⇒`extendedProperty`

Adott egy érzékelő azonosító, ez a függvény lekéri a tulajdonság és annak értékét az érzékelő.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Érzékelő azonosítója |
| *tulajdonságNeve* | `string` | Érzékelő tulajdonságneve |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty(deviceId, propertyName) ⇒`extendedProperty`

Adott eszközazonosító, ez a függvény lekéri a tulajdonságot és annak értékét az eszközről.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Eszközazonosító |
| *tulajdonságNeve* | `string` | Eszköztulajdonság neve |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, érték)

Ez a függvény beállít egy értéket az érzékelőobjektumon a megadott adattípussal.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Érzékelő azonosítója |
| *Adattípus*  | `string` | Érzékelő adattípusa |
| *value*  | `string` | Érték |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId; dataType, value)

Ez a függvény a szóközobjektum adott adattípussal rendelkező értékét állítja be.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Térazonosító |
| *Adattípus* | `string` | Adattípus |
| *value* | `string` | Érték |

### <a name="logmessage"></a>log(üzenet)

Ez a függvény a következő üzenetet naplózza a felhasználó által definiált függvényben.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *Üzenetet* | `string` | Naplózandó üzenet |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification(topologyObjectId, topologyObjectType, hasznos adat)

Ez a függvény egy egyéni értesítést küld ki a küldéshez.

**Kedves**: globális függvény

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *topológiaObjectId*  | `guid` | Grafikonobjektum-azonosító. Ilyenpéldául a hely, az érzékelő és az eszközazonosító.|
| *topologyObjectType*  | `string` | Ilyenek például az érzékelő és az eszköz.|
| *payload*  | `string` | Az értesítéssel együtt elküldendő JSON-tartalom. |

## <a name="return-types"></a>Visszatérési típusok

Az ügyfélhivatkozás-segítő módszerektől visszaadott válaszmodelleket az alábbiakban ismertetjük.

### <a name="space"></a>Space (Szóköz)

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

### <a name="space-methods"></a>Térmódszerek

#### <a name="parent--space"></a>Szülő() ⇒`space`

Ez a függvény az aktuális tér szülőterületét adja vissza.

#### <a name="childsensors--sensor"></a>Gyermekérzékelők() ⇒`sensor[]`

Ez a függvény az aktuális tér gyermekérzékelőit adja vissza.

#### <a name="childdevices--device"></a>Gyermekeszközök() ⇒`device[]`

Ez a függvény az aktuális terület gyermekeszközeit adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Ez a függvény a kiterjesztett tulajdonságot és az aktuális terület értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *tulajdonságNeve* | `string` | A bővített tulajdonság neve |

#### <a name="valuevaluename--value"></a>Érték(értéknév) ⇒`value`

Ez a függvény az aktuális terület értékét adja eredményül.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Az érték neve |

#### <a name="historyvaluename--value"></a>Előzmények(értéknév) ⇒`value[]`

Ez a függvény az aktuális tér előzményértékeit adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Az érték neve |

#### <a name="notifypayload"></a>Értesítés(hasznos teher)

Ez a függvény értesítést küld a megadott hasznos adattal.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON hasznos teher, amelyet az értesítésbe kell foglalni |

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

#### <a name="parent--space"></a>Szülő() ⇒`space`

Ez a függvény az aktuális eszköz szülőterületét adja vissza.

#### <a name="childsensors--sensor"></a>Gyermekérzékelők() ⇒`sensor[]`

Ez a funkció az aktuális eszköz gyermekérzékelőit adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Ez a függvény a kiterjesztett tulajdonságot és az aktuális eszköz értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *tulajdonságNeve* | `string` | A bővített tulajdonság neve |

#### <a name="notifypayload"></a>Értesítés(hasznos teher)

Ez a függvény értesítést küld a megadott hasznos adattal.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON hasznos teher, amelyet az értesítésbe kell foglalni |

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

### <a name="sensor-methods"></a>Érzékelőmódszerek

#### <a name="space--space"></a>Szóköz() ⇒`space`

Ez a függvény az aktuális érzékelő szülőterületét adja vissza.

#### <a name="device--device"></a>Eszköz() ⇒`device`

Ez a függvény az aktuális érzékelő szülőeszközét adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty(propertyName) ⇒`extendedProperty`

Ez a függvény a kiterjesztett tulajdonságot és az aktuális érzékelő értékét adja vissza.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *tulajdonságNeve* | `string` | A bővített tulajdonság neve |

#### <a name="value--value"></a>Érték() ⇒`value`

Ez a függvény az aktuális érzékelő értékét adja vissza.

#### <a name="history--value"></a>Előzmények() ⇒`value[]`

Ez a függvény az aktuális érzékelő előzményértékeit adja vissza.

#### <a name="notifypayload"></a>Értesítés(hasznos teher)

Ez a függvény értesítést küld a megadott hasznos adattal.

| Paraméter  | Típus                | Leírás  |
| ------ | ------------------- | ------------ |
| *payload* | `string` | JSON hasznos teher, amelyet az értesítésbe kell foglalni |

### <a name="value"></a>Érték

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Bővített tulajdonság

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>További lépések

- További információ az [Azure Digital Twins által definiált funkciókról.](./concepts-user-defined-functions.md)

- További információ [a felhasználó által definiált függvények létrehozásáról.](./how-to-user-defined-functions.md)

- További információ [a felhasználó által definiált függvények hibakereséséről.](./how-to-diagnose-user-defined-functions.md)
