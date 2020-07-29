---
title: A IoT Plug and Play digitális Twins ismertetése
description: Ismerje meg, hogyan használja a IoT Plug and Play Preview digitális ikreket
author: prashmo
ms.author: prashmo
ms.date: 07/17/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 1908abfb3d0ea20c69a68344d54076c6760e9e63
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352273"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>A IoT Plug and Play digitális Twins ismertetése

A IoT Plug and Play-eszközök a [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) sémája által leírt modellt implementálják. A modell leírja az adott eszközhöz tartozó összetevők, tulajdonságok, parancsok és telemetria-üzenetek készletét. Az eszközök Twin és a digitális iker inicializálása az első alkalommal, amikor egy IoT Plug and Play eszköz csatlakozik egy IoT hubhoz.

A IoT Plug and Play a DTDL 2-es verzióját használja. További információ erről a verzióról: [Digital Twins Definition Language (DTDL) – 2. verzió](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) specifikáció a githubon.

A DTDL nem kizárólag IoT Plug and Play. Más IoT-szolgáltatások, például az [Azure digitális Twins](../digital-twins/overview.md), a teljes környezetek, például épületek és energiahálózatok ábrázolására használhatók. További információ: [Twin models ismertetése az Azure Digital twinsban](../digital-twins/concepts-models.md).

Ez a cikk azt ismerteti, hogyan jelennek meg az összetevők és a tulajdonságok a különálló eszközök *kívánt* és *jelentett* részeiben. Azt is leírja, hogyan képezi le ezeket a fogalmakat a megfelelő digitális Twin-re.

A cikk IoT Plug and Play eszköze, amely a hőmérséklet- [vezérlő modelljét](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) implementálja [termosztát](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -összetevővel.

## <a name="device-twins-and-digital-twins"></a>Eszközök ikrek és digitális Twins

Az ikrek olyan JSON-dokumentumok, amelyek az eszköz állapotával kapcsolatos információkat tárolnak, beleértve a metaadatokat, a konfigurációkat és a feltételeket. További információ: [az eszközök ikrek megismerése és használata a IoT Hubban](../iot-hub/iot-hub-devguide-device-twins.md). Mind az eszköz-, mind a megoldás-építők továbbra is használhatják a Device Twin API-k és SDK-k együttes használatát az eszközök és megoldások IoT Plug and Play konvenciók használatával történő megvalósításához.

A digitális Twin API-k magas szintű szerkezetekben működnek a digitális Twins Definition Language (DTDL) nyelvben (például összetevők, tulajdonságok és parancsok). A digitális Twin API-k megkönnyítik a megoldás-építők számára, hogy IoT Plug and Play megoldásokat hozzanak létre.

Egy különálló eszközön az írható tulajdonság állapota a kívánt és a jelentett szakaszokra oszlik. Az összes írásvédett tulajdonság a jelentett szakaszban található.

A Digital Twin-ben a tulajdonság jelenlegi és kívánt állapotának egységes nézete látható. Egy adott tulajdonság szinkronizációs állapotát a megfelelő gyökérszintű vagy összetevő `$metadata` szakasz tárolja.

### <a name="digital-twin-json-format"></a>Digitális kettős JSON formátum

Ha JSON-objektumként jelenik meg, a digitális Twin a következő mezőket tartalmazza:

| Mező neve | Leírás |
| --- | --- |
| `$dtId` | Egy felhasználó által megadott karakterlánc, amely az eszköz digitális Twin AZONOSÍTÓját jelöli |
| `{propertyName}` | A JSON-tulajdonság értéke |
| `$metadata.$model` | Választható A digitális IKeret jellemző modell felület azonosítója |
| `$metadata.{propertyName}.desiredValue` | [Csak írható tulajdonságok esetében] A megadott tulajdonság kívánt értéke |
| `$metadata.{propertyName}.desiredVersion` | [Csak írható tulajdonságok esetében] A IoT Hub által karbantartott kívánt érték verziója|
| `$metadata.{propertyName}.ackVersion` | [Kötelező, csak írható tulajdonságok esetében] A digitális IKeret megvalósító eszköz által elismert verzió, amelyet a kívánt verziónál nagyobb vagy egyenlően kell megadni |
| `$metadata.{propertyName}.ackCode` | [Kötelező, csak írható tulajdonságok esetében] A `ack` digitális IKeret megvalósító eszköz alkalmazás által visszaadott kód |
| `$metadata.{propertyName}.ackDescription` | [Nem kötelező, csak írható tulajdonságok esetében] A `ack` digitális IKeret megvalósító eszköz alkalmazás által visszaadott Leírás |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub fenntartja a tulajdonság utolsó frissítésének időbélyegét az eszközön. Az időbélyegek UTC szerint vannak kódolva, és a ISO8601 formátuma éééé-hh-NNTÓÓ: PP: SS. mmmZ |
| `{componentName}` | Egy olyan JSON-objektum, amely a gyökérszintű objektumhoz hasonló tulajdonság-értékeket és metaadatokat tartalmaz. |
| `{componentName}.{propertyName}` | Az összetevő tulajdonságának értéke a JSON-ban |
| `{componentName}.$metadata` | Az összetevő metaadat-információi, a legfelső szintűhez hasonlóan`$metadata` |

#### <a name="device-twin-sample"></a>Eszköz kettős mintája

Az alábbi kódrészlet egy IoT-Plug and Play-eszközt mutat be, amely JSON-objektumként van formázva:

```json
{
    "deviceId": "sample-device",
    "modelId": "dtmi:com:example:TemperatureController;1",
    "version": 15,
    "properties": {
        "desired": {
            "thermostat1": {
                "__t": "c",
                "targetTemperature": 21.8
            },
            "$metadata": {...},
            "$version": 4
        },
        "reported": {
            "serialNumber": "alwinexlepaho8329",
            "thermostat1": {
                "maxTempSinceLastReboot": 25.3,
                "__t": "c",
                "targetTemperature": {
                    "value": 21.8,
                    "ac": 200,
                    "ad": "Successfully executed patch",
                }
            },
            "$metadata": {...},
            "$version": 11
        }
    }
}
```

#### <a name="digital-twin-sample"></a>Digitális kettős minta

A következő kódrészletben a rendszer JSON-objektumként formázott Digital Twin-t jeleníti meg:

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 21.8,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 21.8,
                "desiredVersion": 4,
                "ackVersion": 4,
                "ackCode": 200,
                "ackDescription": "Successfully executed patch",
                "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
            },
            "maxTempSinceLastReboot": {
                "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
            }
        }
    },
    "$metadata": {
        "$model": "dtmi:com:example:TemperatureController;1",
        "serialNumber": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

### <a name="properties"></a>Tulajdonságok

A tulajdonságok olyan adatmezők, amelyek egy entitás állapotát jelölik (például a tulajdonságok számos objektumorientált programozási nyelven).

#### <a name="read-only-property"></a>Írásvédett tulajdonság

Séma

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

Ebben a példában az `alwinexlepaho8329` `serialNumber` eszköz által jelentett írásvédett tulajdonság aktuális értéke.
A következő kódrészletek a tulajdonság egymás melletti JSON-ábrázolását jelenítik meg `serialNumber` :

:::row:::
   :::column span="":::
      **Ikereszközök**

```json
"properties": {
    "reported": {
        "serialNumber": "alwinexlepaho8329"
    }
}
```

   :::column-end:::
   :::column span="":::
      **Digitális Twin**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Írható tulajdonság

Tegyük fel, hogy az eszközön a következő írható tulajdonság is szerepel a gyökérszintű szinten:

```json
{
    "@type": "Property",
    "name": "fanSpeed",
    "displayName": "Fan Speed",
    "writable": true,
    "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Ikereszközök**

```json
{
    "properties": {
        "desired": {
            "fanSpeed": 2.0,
        },
        "reported": {
            "fanSpeed": {
                "value": 3.0,
                "ac": 200,
                "av": 1,
                "ad": "Successfully executed patch version 1"
            }
        }
    },
}
```

   :::column-end:::
   :::column span="":::
      **Digitális Twin**

```json
{
    "fanSpeed": 3.0,
    "$metadata": {
        "fanSpeed": {
            "desiredValue": 2.0,
            "desiredVersion": 2,
            "ackVersion": 1,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch version 1",
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

Ebben a példában az `3.0` `fanSpeed` eszköz által jelentett tulajdonság aktuális értéke. `2.0`a megoldás által beállított kívánt érték. A gyökérszintű tulajdonság kívánt értékének és szinkronizálási állapotának beállítása a digitális Twin-ben a gyökérszintű szinten történik `$metadata` . Ha az eszköz online állapotba kerül, alkalmazza ezt a frissítést, és jelentse vissza a frissített értéket.

### <a name="components"></a>Összetevők

Az összetevők lehetővé teszik, hogy a modell felülete más felületek szerelvényként legyen felépítve.
Vegye fontolóra a modellként definiált [termosztát](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -felületet.
Ez az illesztőfelület mostantól összetevő-thermostat1 (és egy másik összetevő-thermostat2) is beépíthető a [hőmérséklet-vezérlő modell](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)meghatározásakor.

Egy különálló eszközön a jelölő azonosítja az összetevőket `{ "__t": "c"}` . Egy digitális Twin-ben a `$metadata` megjelölés egy összetevőt jelöl.

Ebben a példában `thermostat1` egy két tulajdonsággal rendelkező összetevő:

- `maxTempSinceLastReboot`egy írásvédett tulajdonság.
- `targetTemperature`az eszköz által sikeresen szinkronizált írható tulajdonság. Ezeknek a tulajdonságoknak a kívánt értéke és szinkronizálási állapota az összetevőben található `$metadata` .

Az alábbi kódrészletek az összetevő egymás melletti JSON-ábrázolását mutatják `thermostat1` :

:::row:::
   :::column span="":::
      **Ikereszközök**

```json
"properties": {
    "desired": {
        "thermostat1": {
            "__t": "c",
            "targetTemperature": 21.8
        },
        "$metadata": {
        },
        "$version": 4
    },
    "reported": {
        "thermostat1": {
            "maxTempSinceLastReboot": 25.3,
            "__t": "c",
            "targetTemperature": {
                "value": 21.8,
                "ac": 200,
                "ad": "Successfully executed patch",
                "av": 4
            }
        },
        "$metadata": {
        },
        "$version": 11
    }
}
```

   :::column-end:::
   :::column span="":::
      **Digitális Twin**

```json
"thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.8,
            "desiredVersion": 4,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        },
        "maxTempSinceLastReboot": {
            "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
        }
    }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Digitális Twin API-k

Az Azure Digital Twins a **Get Digital Twin**, a **Digital Twin frissítése**, az **Component parancs meghívása** és az eszközök digitális iker-kezelésének **meghívása paranccsal** érhető el. Használhatja a [REST API-kat](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) közvetlenül vagy egy [Service SDK](../iot-pnp/libraries-sdks.md)-n keresztül.

## <a name="digital-twin-change-events"></a>Digitális kettős változási események

Ha a digitális kettős változási események engedélyezve vannak, az esemény akkor aktiválódik, amikor az összetevő vagy a tulajdonság jelenlegi vagy kívánt értéke megváltozik. A digitális kettős változási események a [JSON-javítás](http://jsonpatch.com/) formátumában jönnek létre. A kapcsolódó események az eszköz Twin formátumában jönnek létre, ha a kettős módosítási esemény engedélyezve van.

Ha meg szeretné tudni, hogyan engedélyezheti az útválasztást az eszközök és a digitális kettős események esetében, tekintse meg az [eszközről a felhőbe irányuló üzenetek különböző végpontokra való küldésére szolgáló IoT hub üzenet-útválasztás használata](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events) Az üzenet formátumának megismeréséhez tekintse meg [IoT hub üzenetek létrehozása és olvasása](../iot-hub/iot-hub-devguide-messages-construct.md)című témakört.

Például a következő digitális kettős változási esemény aktiválódik, ha `targetTemperature` a megoldást a megoldás állítja be:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

A következő digitális kettős változási esemény akkor aktiválódik, amikor az eszköz jelentést készít a fenti kívánt módosítás alkalmazásáról:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a digitális ikreket, íme néhány további erőforrás:

- [A IoT Plug and Play Digital Twin API-k használata](howto-manage-digital-twin.md)
- [Eszköz használata a megoldásból](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
