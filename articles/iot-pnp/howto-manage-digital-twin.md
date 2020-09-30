---
title: A IoT Plug and Play digitális ikrek kezelése
description: A IoT Plug and Play-eszköz kezelése digitális Twin API-k használatával
author: prashmo
ms.author: prashmo
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: bfbfc6e5e4a0f5721d620c2936e5ea0aa685f8ad
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577594"
---
# <a name="manage-iot-plug-and-play-digital-twins"></a>IoT-Plug and Play digitális ikrek kezelése

A IoT Plug and Play **támogatja a digitális iker és** a digitális **dupla műveletek frissítését** a digitális ikrek kezelésére. Használhatja a [REST API-kat](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) vagy az egyik [Service SDK](libraries-sdks.md)-t is.

Az írás időpontjában a digitális Twin API verziója a következő: `2020-09-30` .

## <a name="update-a-digital-twin"></a>Digitális iker frissítése

A IoT Plug and Play-eszközök a [digitális Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)által leírt modellt implementálják. A megoldás fejlesztői a **digitális Twin API frissítésével** frissíthetik az összetevő állapotát és a digitális iker tulajdonságait.

A cikkben példaként használt IoT Plug and Play eszköz a [hőmérséklet-vezérlő modelljét](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) implementálja [termosztát](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -összetevőkkel.

A következő kódrészlet a **Get Digital Twin** kérelem JSON-objektumként való formázásának válaszát mutatja. Ha többet szeretne megtudni a digitális kettős formátumról, tekintse meg a [IoT Plug and Play digitális ikrek ismertetése](./concepts-digital-twin.md#digital-twin-json-format)című témakört:

```json
{
    "$dtId": "sample-device",
    "serialNumber": "alwinexlepaho8329",
    "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "targetTemperature": 20.4,
        "$metadata": {
            "targetTemperature": {
                "desiredValue": 20.4,
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

A digitális Twins lehetővé teszi, hogy egy teljes összetevőt vagy tulajdonságot egy [JSON-javítás](http://jsonpatch.com/)használatával frissítsen.

A tulajdonságot például a következőképpen frissítheti `targetTemperature` :

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    }
]
```

Az előző frissítés egy tulajdonság kívánt értékét állítja be a megfelelő összetevő-szinten `$metadata` , ahogy az a következő kódrészletben látható. IoT Hub frissíti a tulajdonság kívánt verzióját:

```json
"thermostat1": {
    "targetTemperature": 20.4,
    "$metadata": {
        "targetTemperature": {
            "desiredValue": 21.4,
            "desiredVersion": 5,
            "ackVersion": 4,
            "ackCode": 200,
            "ackDescription": "Successfully executed patch",
            "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
        }
    }
}
```

### <a name="add-replace-or-remove-a-component"></a>Összetevő hozzáadása, cseréje vagy eltávolítása

Az összetevő szintű műveletekhez üres objektum `$metadata` jelölőre van szükség az értéken belül.

A Hozzáadás vagy csere összetevő-művelet beállítja a megadott tulajdonságok kívánt értékeit. Emellett törli a kívánt értékeket a frissítéshez nem megadott írható tulajdonságok esetében is.

Az összetevők eltávolítása törli az összes írható tulajdonság kívánt értékét. Az eszköz végül szinkronizálja az eltávolítást, és leállítja az egyes tulajdonságok jelentését. Az összetevő ezután el lesz távolítva a digitális Twin-ből.

A következő JSON-javítási minta bemutatja, hogyan adhat hozzá, cserélhet vagy távolíthat el egy összetevőt:

```json
[
    {
        "op": "add",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "anotherWritableProperty": 42,
            "$metadata": {}
        }
    },
    {
        "op": "replace",
        "path": "/thermostat1",
        "value": {
            "targetTemperature": 21.4,
            "$metadata": {}
        }
    },
    {
        "op": "remove",
        "path": "/thermostat2"
    }
]
```

### <a name="add-replace-or-remove-a-property"></a>Tulajdonság hozzáadása, cseréje vagy eltávolítása

A Hozzáadás vagy csere művelet beállítja egy tulajdonság kívánt értékét. Az eszköz szinkronizálhatja az állapotot, és a `ack` kóddal, a verzióval és a leírással együtt az érték frissítését is bejelentheti.

A tulajdonság eltávolítása törli a tulajdonság kívánt értékét, ha be van állítva. Az eszköz ezután leállíthatja a tulajdonság jelentését, és a rendszer eltávolítja az összetevőből. Ha ez a tulajdonság az utolsó az összetevőben, akkor a rendszer eltávolítja az összetevőt is.

A következő JSON-javítási minta bemutatja, hogyan adhat hozzá, cserélhet vagy távolíthat el egy tulajdonságot egy összetevőn belül:

```json
[
    {
        "op": "add",
        "path": "/thermostat1/targetTemperature",
        "value": 21.4
    },
    {
        "op": "replace",
        "path": "/thermostat1/anotherWritableProperty",
        "value": 42
    },
    {
        "op": "remove",
        "path": "/thermostat2/targetTemperature",
    }
]
```

### <a name="rules-for-setting-the-desired-value-of-a-digital-twin-property"></a>A digitális Twin tulajdonság kívánt értékének beállítására vonatkozó szabályok

**Név**

Egy összetevő vagy tulajdonság nevének érvényes DTDL v2-névnek kell lennie.

Az engedélyezett karakterek: a-z, A-Z, 0-9 (nem az első karakter), az aláhúzás (nem pedig az első vagy az utolsó karakter).

A név 1-64 karakter hosszú lehet.

**Tulajdonság értéke**

Az értéknek érvényes [DTDL v2 tulajdonságnak](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property)kell lennie.

Az összes primitív típus támogatott. Összetett típusokon, enumerálásokon, térképeken és objektumokon belül támogatottak. További információ: [DTDL v2 sémák](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#schemas).

A tulajdonságok nem támogatják a tömböt vagy a tömbhöz tartozó összetett sémákat.

Egy összetett objektum esetében az öt szint maximális mélysége támogatott.

Az összetett objektumon belüli összes mezőnév érvényes DTDL v2-névvel kell rendelkeznie.

Az összes térképi kulcsnak érvényes DTDL v2-névnek kell lennie.

## <a name="troubleshoot-update-digital-twin-api-errors"></a>A digitális Twin API-hibák frissítésének hibaelhárítása

A nyilvános előzetes verzióban az Update Digital Twin API a következő általános hibaüzenetet jeleníti meg:

`ErrorCode:ArgumentInvalid;'{propertyName}' exists within the device twin and is not digital twin conformant property. Please refer to aka.ms/dtpatch to update this to be conformant.`

Győződjön meg arról, hogy a frissítési javítás a [digitális Twin tulajdonság kívánt értékének beállítására vonatkozó szabályokat](#rules-for-setting-the-desired-value-of-a-digital-twin-property) követi

Amikor frissít egy összetevőt, győződjön meg arról, hogy az [üres objektum $metadata jelölője](#add-replace-or-remove-a-component) be van állítva.

A frissítések sikertelenek lehetnek, ha egy eszköz jelentett értékei nem felelnek meg a [IoT Plug and Play konvencióinak](./concepts-convention.md#writable-properties).

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a digitális ikreket, íme néhány további erőforrás:

- [Kommunikáció egy eszközzel a megoldásból](quickstart-service-node.md)
- [IoT Digital Twin REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)
