---
title: Azure Digital Twins felhasználó által definiált függvények ügyféloldali függvénytár-referenciája | Microsoft Docs
description: Azure Digital Twins felhasználó által definiált függvények ügyféloldali függvénytár-referenciája.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: article
ms.date: 08/12/2019
ms.custom: seodec18
ms.openlocfilehash: 36fb8a5588321426ccae7d6c6577fb4b48f3a4db
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948901"
---
# <a name="user-defined-functions-client-library-reference"></a>Felhasználó által definiált függvények ügyféloldali függvénytár-referenciája

Ez a dokumentum az Azure Digital Twins felhasználó által definiált functions ügyféloldali függvénytárával kapcsolatos információkat tartalmaz.

## <a name="helper-methods"></a>Segítő módszerek

Az ügyféloldali kódtár a gyakran használt műveletekhez biztosít segítő metódusokat.

### <a name="getspacemetadataid--space"></a>getSpaceMetadata (azonosító) ⇒ `space`

A szóközzel megadott azonosító alapján a függvény lekéri a helyet a gráfból.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Lemezterület-azonosító |

### <a name="getsensormetadataid--sensor"></a>getSensorMetadata (azonosító) ⇒ `sensor`

Ez a függvény az érzékelő azonosítóját adja vissza a gráfból.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ---------- | ------------------- | ------------ |
| *id*  | `guid` | Érzékelő azonosítója |

### <a name="getdevicemetadataid--device"></a>getDeviceMetadata (azonosító) ⇒ `device`

Az eszköz azonosítója miatt ez a függvény lekéri az eszközt a gráfból.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *id* | `guid` | Eszköz azonosítója |

### <a name="getsensorvaluesensorid-datatype--value"></a>getSensorValue (sensorId, adattípus) ⇒ @no__t – 0

Az érzékelő azonosítójának és adattípusának megadásakor ez a függvény az adott érzékelő aktuális értékét kérdezi le.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId*  | `guid` | Érzékelő azonosítója |
| *Adattípus*  | `string` | Érzékelő adattípusa |

### <a name="getspacevaluespaceid-valuename--value"></a>getSpaceValue (spaceId, valueName) ⇒ @no__t – 0

A Space azonosító és az érték neve miatt ez a függvény lekéri az aktuális értéket a szóköz tulajdonsághoz.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId*  | `guid` | Lemezterület-azonosító |
| *valueName* | `string` | Terület tulajdonságának neve |

### <a name="getsensorhistoryvaluessensorid-datatype--value"></a>getSensorHistoryValues (sensorId, adattípus) ⇒ @no__t – 0

Az érzékelő azonosítójának és adattípusának megadásakor ez a függvény az adott érzékelő korábbi értékeit kérdezi le.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Érzékelő azonosítója |
| *Adattípus* | `string` | Érzékelő adattípusa |

### <a name="getspacehistoryvaluesspaceid-datatype--value"></a>getSpaceHistoryValues (spaceId, adattípus) ⇒ @no__t – 0

A Space azonosító és az érték neve miatt ez a függvény az adott tulajdonsághoz tartozó korábbi értékeket kérdezi le a tárhelyen.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Lemezterület-azonosító |
| *valueName* | `string` | Terület tulajdonságának neve |

### <a name="getspacechildspacesspaceid--space"></a>getSpaceChildSpaces (spaceId) ⇒ `space[]`

A szóközzel megadott azonosító miatt a függvény lekérdezi az adott szülő terület gyermekeit.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Lemezterület-azonosító |

### <a name="getspacechildsensorsspaceid--sensor"></a>getSpaceChildSensors (spaceId) ⇒ `sensor[]`

A Space azonosító megadásakor ez a függvény lekéri a gyermek érzékelőket az adott szülő területhez.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Lemezterület-azonosító |

### <a name="getspacechilddevicesspaceid--device"></a>getSpaceChildDevices (spaceId) ⇒ `device[]`

A szóközzel megadott azonosító miatt a függvény lekéri a gyermekeket az adott szülő területhez.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Lemezterület-azonosító |

### <a name="getdevicechildsensorsdeviceid--sensor"></a>getDeviceChildSensors (deviceId) ⇒ `sensor[]`

Az eszköz azonosítója miatt ez a függvény lekérdezi az adott szülő eszköz gyermek-érzékelőkét.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Eszköz azonosítója |

### <a name="getspaceparentspacechildspaceid--space"></a>getSpaceParentSpace (childSpaceId) ⇒ `space`

A szóközzel megadott azonosító alapján a függvény lekéri a szülő területét.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSpaceId* | `guid` | Lemezterület-azonosító |

### <a name="getsensorparentspacechildsensorid--space"></a>getSensorParentSpace(childSensorId) ⇒ `space`

A függvény az érzékelő azonosítójának alapján lekéri a szülő területét.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Érzékelő azonosítója |

### <a name="getdeviceparentspacechilddeviceid--space"></a>getDeviceParentSpace (childDeviceId) ⇒ `space`

Az eszköz azonosítója miatt ez a függvény lekéri a szülő területét.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *childDeviceId* | `guid` | Eszköz azonosítója |

### <a name="getsensorparentdevicechildsensorid--space"></a>getSensorParentDevice(childSensorId) ⇒ `space`

A függvény az érzékelő azonosítója alapján lekéri a fölérendelt eszközét.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *childSensorId* | `guid` | Érzékelő azonosítója |

### <a name="getspaceextendedpropertyspaceid-propertyname--extendedproperty"></a>getSpaceExtendedProperty (spaceId, propertyName) ⇒ @no__t – 0

A szóközzel megadott azonosító alapján a függvény lekéri a tulajdonságot és annak értékét a területről.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Lemezterület-azonosító |
| *propertyName* | `string` | Terület tulajdonságának neve |

### <a name="getsensorextendedpropertysensorid-propertyname--extendedproperty"></a>getSensorExtendedProperty (sensorId, propertyName) ⇒ @no__t – 0

A függvény az érzékelő azonosítója alapján lekéri a tulajdonságot és annak értékét az érzékelőből.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Érzékelő azonosítója |
| *propertyName* | `string` | Érzékelő tulajdonságának neve |

### <a name="getdeviceextendedpropertydeviceid-propertyname--extendedproperty"></a>getDeviceExtendedProperty (deviceId, propertyName) ⇒ `extendedProperty`

Az eszköz azonosítója miatt ez a függvény a tulajdonságot és annak értékét kérdezi le az eszközről.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *deviceId* | `guid` | Eszköz azonosítója |
| *propertyName* | `string` | Eszköz tulajdonságának neve |

### <a name="setsensorvaluesensorid-datatype-value"></a>setSensorValue(sensorId, dataType, value)

Ez a függvény egy értéket állít be az érzékelő objektumon a megadott adattípussal.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *sensorId* | `guid` | Érzékelő azonosítója |
| *Adattípus*  | `string` | Érzékelő adattípusa |
| *value*  | `string` | Value |

### <a name="setspacevaluespaceid-datatype-value"></a>setSpaceValue(spaceId, dataType, value)

Ez a függvény egy értéket állít be a szóköz objektumon a megadott adattípussal.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *spaceId* | `guid` | Lemezterület-azonosító |
| *Adattípus* | `string` | Adattípus |
| *value* | `string` | Value |

### <a name="logmessage"></a>napló (üzenet)

Ez a függvény a következő üzenetet naplózza a felhasználó által definiált függvényen belül.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *üzenetet* | `string` | Naplózandó üzenet |

### <a name="sendnotificationtopologyobjectid-topologyobjecttype-payload"></a>sendNotification (topologyObjectId, topologyObjectType, hasznos adatok)

Ez a függvény elküld egy egyéni értesítést a küldéshez.

**Típus**: globális függvény

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *topologyObjectId*  | `guid` | Gráf-objektumazonosító. Ilyenek például a szóköz, az érzékelő és az eszköz azonosítója.|
| *topologyObjectType*  | `string` | Ilyenek például az érzékelő és az eszköz.|
| *adattartalom*  | `string` | Az értesítéssel küldendő JSON-adattartalom. |

## <a name="return-types"></a>Visszatérési típusok

Az ügyfél-referenciák segítő módszereiből visszaadott válaszok modelljei alább olvashatók.

### <a name="space"></a>Szóköz

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

### <a name="space-methods"></a>Szóköz metódusok

#### <a name="parent--space"></a>Szülő () ⇒ @no__t – 0

Ez a függvény az aktuális terület szülő területét adja vissza.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ @no__t – 0

Ez a függvény az aktuális terület alárendelt érzékelőkből áll vissza.

#### <a name="childdevices--device"></a>ChildDevices () ⇒ @no__t – 0

Ez a függvény az aktuális terület alárendelt eszközeit adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Ez a függvény a kiterjesztett tulajdonságot és annak értékét adja vissza az aktuális területhez.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | A kiterjesztett tulajdonság neve |

#### <a name="valuevaluename--value"></a>Value (valueName) ⇒ @no__t – 0

Ez a függvény az aktuális terület értékét adja vissza.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Az érték neve |

#### <a name="historyvaluename--value"></a>Előzmények (valueName) ⇒ `value[]`

Ez a függvény az aktuális terület korábbi értékeit adja vissza.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *valueName* | `string` | Az érték neve |

#### <a name="notifypayload"></a>Értesítés (hasznos adat)

Ez a függvény értesítést küld a megadott adattartalommal.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *adattartalom* | `string` | Az értesítésbe belefoglalni kívánt JSON-adattartalom |

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

#### <a name="parent--space"></a>Szülő () ⇒ @no__t – 0

Ez a függvény az aktuális eszköz szülő területét adja vissza.

#### <a name="childsensors--sensor"></a>ChildSensors () ⇒ @no__t – 0

Ez a függvény az aktuális eszköz gyermek-érzékelőkét adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Ez a függvény a kiterjesztett tulajdonságot és annak értékét adja vissza az aktuális eszközhöz.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | A kiterjesztett tulajdonság neve |

#### <a name="notifypayload"></a>Értesítés (hasznos adat)

Ez a függvény értesítést küld a megadott adattartalommal.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *adattartalom* | `string` | Az értesítésbe belefoglalni kívánt JSON-adattartalom |

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

### <a name="sensor-methods"></a>Érzékelő módszerei

#### <a name="space--space"></a>Space() ⇒ `space`

Ez a függvény az aktuális érzékelő szülő területét adja vissza.

#### <a name="device--device"></a>Eszköz () ⇒ @no__t – 0

Ez a függvény az aktuális érzékelő fölérendelt eszközét adja vissza.

#### <a name="extendedpropertypropertyname--extendedproperty"></a>ExtendedProperty (propertyName) ⇒ `extendedProperty`

Ez a függvény a kiterjesztett tulajdonságot és annak értékét adja vissza az aktuális érzékelőhöz.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *propertyName* | `string` | A kiterjesztett tulajdonság neve |

#### <a name="value--value"></a>Value () ⇒ @no__t – 0

Ez a függvény az aktuális érzékelő értékét adja vissza.

#### <a name="history--value"></a>Előzmények () ⇒ @no__t – 0

Ez a függvény az aktuális érzékelő korábbi értékeit adja vissza.

#### <a name="notifypayload"></a>Értesítés (hasznos adat)

Ez a függvény értesítést küld a megadott adattartalommal.

| Paraméter  | Type                | Leírás  |
| ------ | ------------------- | ------------ |
| *adattartalom* | `string` | Az értesítésbe belefoglalni kívánt JSON-adattartalom |

### <a name="value"></a>Value

```JSON
{
  "dataType": "Temperature",
  "value": "70",
  "createdTime": ""
}
```

### <a name="extended-property"></a>Kiterjesztett tulajdonság

```JSON
{
  "name": "OccupancyStatus",
  "value": "Occupied"
}
```

## <a name="next-steps"></a>További lépések

- Ismerje meg az [Azure Digital Twins felhasználó által definiált függvényeit](./concepts-user-defined-functions.md).

- Megtudhatja [, hogyan hozhat létre felhasználó által definiált függvényeket](./how-to-user-defined-functions.md).

- Útmutató [a felhasználó által definiált függvények hibakereséséhez](./how-to-diagnose-user-defined-functions.md).
