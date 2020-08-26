---
title: IoT Plug and Play konvenciók | Microsoft Docs
description: A IoT konvenciók leírása Plug and Play a telemetria és a tulajdonságok elküldésére, valamint a parancsok és a tulajdonság-frissítések kezelésére szolgáló eszközök használatát várja.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856058"
---
# <a name="iot-plug-and-play-conventions"></a>Az IoT Plug and Playhez kapcsolódó konvenciók

Az IoT Plug and Play az előnézeti eszközöknek az IoT hub-vel való üzenetváltáskor követendő konvenciókat kell követniük. A IoT Plug and Play Preview-eszközök a MQTT protokollt használják a IoT Hub való kommunikációhoz.

Ismerteti azokat a telemetria, tulajdonságokat és parancsokat, amelyeket egy IoT Plug and Play-eszköz a [digitális Twins Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) _modellel_implementál. Ebben a cikkben a modell két típusát említi:

- **Nincs összetevő** – olyan modell, amely nem rendelkezik összetevőkkel. A modell az telemetria, a tulajdonságokat és a parancsokat deklarálja legfelső szintű tulajdonságokként a fő felület tartalom szakaszában.
- **Több összetevő** – két vagy több illesztőfelületből álló modell. Fő felület telemetria, tulajdonságokkal és parancsokkal. Egy vagy több, összetevőként deklarált illesztőfelület további telemetria, tulajdonságokkal és parancsokkal.

További információ: [IoT Plug and Play-összetevők a modellekben](concepts-components.md).

## <a name="identify-the-model"></a>A modell azonosítása

Ha be szeretné jelenteni az általa megvalósított modellt, egy IoT Plug and Play eszköz tartalmazza a modell AZONOSÍTÓját a MQTT-kapcsolati csomagban a `model-id` mezőhöz való hozzáadásával `USERNAME` .

Az eszköz által megvalósított modell azonosításához a szolgáltatás a következő helyről szerezheti be a modell AZONOSÍTÓját:

- Az eszköz Twin `modelId` mezője
- A digitális dupla `$metadata.$model` mező.
- Egy digitális kettős változásról szóló értesítés.

## <a name="telemetry"></a>Telemetria

A nem telemetria eljuttatott eszközök nem igényelnek további metaadatokat. A szolgáltatás hozzáadja a `dt-dataschema` tulajdonságot.

A több összetevőből küldött telemetria az `$.sub` üzenet tulajdonságként kell hozzáadni. A System hozzáadja a `dt-subject` és a `dt-dataschema` tulajdonságokat.

## <a name="read-only-properties"></a>Csak olvasható tulajdonságok

### <a name="sample-no-component-read-only-property"></a>Minta nincs írásvédett összetevő tulajdonsága

Egy eszköz bármilyen érvényes JSON-t tud küldeni, amely a DTDL v2-szabályokat követi.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Minta hasznos adat**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>Több összetevő írásvédett tulajdonságának mintája

Az eszköznek hozzá kell adnia a `{"__t": "c"}` jelölőt, amely jelzi, hogy az elem egy összetevőre hivatkozik.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Jelentett tulajdonság**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>Írható tulajdonságok

Az eszköznek meg kell győződnie arról, hogy megkapta a tulajdonságot egy jelentett tulajdonság elküldésével. A jelentett tulajdonságnak a következőket kell tartalmaznia:

- `value` – a tulajdonság tényleges értéke (általában a fogadott érték, de az eszköz más érték bejelentését is eldöntheti).
- `ac` – egy HTTP-állapotkódot használó nyugtázási kód.
- `av` – a visszaigazolási verzió, amely a `$version` kívánt tulajdonságra hivatkozik.
- `ad` – a visszaigazolás nem kötelező leírása.

### <a name="sample-no-component-writable-property"></a>Minta – az összetevő írható tulajdonsága nem

Egy eszköz bármilyen érvényes JSON-t küldhet, amely a DTDL v2 szabályait követi:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Kívánt tulajdonság**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Jelentett tulajdonság**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>Több összetevő írható tulajdonságának mintája

Az eszköznek hozzá kell adnia a `{"__t": "c"}` jelölőt, amely jelzi, hogy az elem egy összetevőre hivatkozik.

A jelölő csak összetevő-szintű frissítésekhez lesz elküldve, így az eszközök nem keresik ezt a jelzőt.

Az eszköznek meg kell győződnie arról, hogy megkapta a tulajdonságot egy jelentett tulajdonság elküldésével:

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Kívánt tulajdonság**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **Jelentett tulajdonság**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>Parancsok

Az összetevő-illesztőfelületek nem a parancs nevét használják előtag nélkül.

Egy eszközön több összetevő-illesztőfelület is a következő formátumú parancsokat használja: `componentName*commandName` .

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a IoT Plug and Play konvenciókat, néhány további erőforrást is talál:

- [Digitális Twins-definíciós nyelv (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C eszköz SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [Modell összetevői](./concepts-components.md)
