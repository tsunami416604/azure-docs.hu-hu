---
title: Telemetria-, tulajdonság-és parancssori adattartalom az Azure IoT Centralban | Microsoft Docs
description: Az Azure IoT Central-sablonjai lehetővé teszik az eszközök telemetria, tulajdonságainak és parancsainak megadását. Megtudhatja, hogy az eszközök milyen formátumban cserélhetik az IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 24fbe347aeb0b47ffd1ba694f761d909ff2950f8
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94989547"
---
# <a name="telemetry-property-and-command-payloads"></a>Telemetria, tulajdonságok és parancsok hasznos adatai

_Ez a cikk az eszközök fejlesztőire vonatkozik._

Az Azure IoT Central egy olyan tervrajz, amely az alábbiakat határozza meg:

* Telemetria egy eszköz küld IoT Central.
* Az eszközök IoT Centralsal való szinkronizálásának tulajdonságai.
* Olyan parancsok, amelyek IoT Central hívásokat kezdeményeznek az eszközön.

Ez a cikk azt ismerteti, hogy az eszközök fejlesztői számára milyen JSON-adattartalomot küldenek és fogadnak az eszköz sablonjában definiált telemetria, tulajdonságok és parancsok számára.

A cikk nem írja le az összes lehetséges telemetria, tulajdonságot és a parancs hasznos adatait, de a példák az összes típusú kulcsot bemutatják.

Mindegyik példa egy olyan kódrészletet mutat be az eszköz modelljéből, amely meghatározza a típust és a JSON-adattartalomot annak szemléltetésére, hogy az eszköz hogyan működjön együtt a IoT Central alkalmazással.

> [!NOTE]
> IoT Central fogadja el az érvényes JSON-t, de csak vizualizációk esetében használható, ha az megfelel az eszköz modelljében szereplő definíciónak. A definíciónak nem megfelelő adatexportálást a következő témakörben tekintheti meg: [IoT-információk exportálása célhelyekre az Azure-ban](howto-export-data.md).

Az eszköz modelljét definiáló JSON-fájl a [Digital Twin Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md)protokollt használja.

A használatban lévő hasznos adattartalmakat bemutató minta-eszközhöz tekintse meg az [ügyfélalkalmazás létrehozása és csatlakoztatása azure IoT Central alkalmazáshoz (Node.js)](tutorial-connect-device-nodejs.md) című témakört, és [hozzon létre és csatlakoztasson egy ügyfélalkalmazás-alkalmazást az Azure IoT Central Application (Python)](tutorial-connect-device-python.md) oktatóanyagokhoz.

## <a name="view-raw-data"></a>Nyers adattárolók megtekintése

IoT Central segítségével megtekintheti a nyers adatokat, amelyeket az eszköz elküld egy alkalmazásnak. Ez a nézet hasznos lehet az eszközről eljuttatott adattartalommal kapcsolatos hibák elhárításához. Az eszköz által küldött nyers adatok megtekintése:

1. A Devices ( **eszközök** ) lapon navigáljon az eszközre.

1. Válassza ki a **nyers** adatlapot:

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Nyers adatnézet":::

    Ebben a nézetben kiválaszthatja a megjelenítendő oszlopokat, és megadhatja a megtekinteni kívánt időtartományt. A nem **modellezett adatok** oszlop az eszköz azon adatait jeleníti meg, amelyek nem felelnek meg az eszköz sablonjának bármely tulajdonság-vagy telemetria-definíciójának.

## <a name="telemetry"></a>Telemetria

### <a name="primitive-types"></a>Primitív típusok

Ez a szakasz olyan primitív telemetria-típusokra mutat be példákat, amelyeket az eszköz egy IoT Central alkalmazásnak közvetít.

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `boolean` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria:

```json
{ "BooleanTelemetry": true }
```

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `string` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Az eszköz modelljének következő kódrészlete egy telemetria definícióját mutatja `integer` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria:

```json
{ "IntegerTelemetry": 23 }
```

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `double` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria:

```json
{ "DoubleTelemetry": 56.78 }
```

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `dateTime` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria `DateTime` : ISO 8061 formátumúnak kell lennie:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `duration` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria: az időtartamnak ISO 8601 formátumúnak kell lennie:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Összetett típusok

Ez a szakasz olyan összetett telemetria-típusokra mutat be példákat, amelyeket az eszköz egy IoT Central alkalmazásnak közvetít.

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `geopoint` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldenie a telemetria. IoT Central megjeleníti az értéket a térképen lévő PIN-kóddal:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Az eszköz modelljének következő kódrészlete egy telemetria definícióját mutatja `Enum` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldenie a telemetria. A lehetséges értékek a következők:, és a (z), és `0` `1` IoT Central a `2` következőképpen jelennek meg `Item1` `Item2` `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Az eszköz modelljében az alábbi kódrészlet egy telemetria-típus definícióját mutatja be `Object` . Ennek az objektumnak három típusa van `dateTime` `integer` `Enum` :

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldenie a telemetria. `DateTime` a típusoknak ISO 8061-kompatibilisnek kell lenniük. A (z) és a (z) és a (z) IoT Central lehetséges értékei a `Property3` következők `0` `1` `Item1` `Item2` `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Az eszköz modelljének következő kódrészlete a telemetria definícióját mutatja `vector` be:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

Az eszköz ügyfelének a következő példához hasonló JSON-ként kell elküldeni a telemetria:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Esemény-és állapot-típusok

Ez a szakasz példákat mutat be azokra a telemetria eseményekre és állapotokra, amelyeket az eszköz egy IoT Central alkalmazásnak küld.

Az eszköz modelljének következő kódrészlete az eseménytípus definícióját mutatja be `integer` :

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

Az eszköz ügyfelének az alábbi példához hasonló JSON-ként kell elküldeni az Event-adatbevitelt:

```json
{ "IntegerEvent": 74 }
```

Az eszköz modelljének következő kódrészlete az állapot definícióját mutatja `integer` be:

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

Az eszköz ügyfelének az alábbi példához hasonló JSON-ként kell elküldeni az állapotot. Lehetséges egész állapot értéke `1` :, `2` , vagy `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Tulajdonságok

> [!NOTE]
> A tulajdonságok adattartalom-formátuma a 07/14/2020-on vagy azt követően létrehozott alkalmazásokra vonatkozik.

### <a name="primitive-types"></a>Primitív típusok

Ez a szakasz azokat a primitív tulajdonságokat mutatja be, amelyeket az eszköz egy IoT Central alkalmazásnak küld.

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `boolean` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{ "BooleanProperty": false }
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `boolean` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{ "LongProperty": 439 }
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `date` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága. `Date` a típusoknak ISO 8061-kompatibilisnek kell lenniük:

```json
{ "DateProperty": "2020-05-17" }
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `duration` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

Az eszköz ügyfelének olyan JSON-adattartalomot kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz kettős időtartamának jelentett tulajdonsága, az ISO 8601 időtartamnak megfelelő:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `float` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{ "FloatProperty": 1.9 }
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `string` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Összetett típusok

Ez a szakasz olyan összetett tulajdonságokat mutat be, amelyeket az eszköz egy IoT Central alkalmazásnak küld.

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `geopoint` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `Enum` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága. A lehetséges értékek a következők:, és a (z), és `0` `1` IoT Central a következőképpen jelennek meg `Item1` `Item2` `Item3` :

```json
{ "EnumProperty": 1 }
```

Az eszköz modelljében az alábbi kódrészlet mutatja a `Object` Tulajdonságok típusának definícióját. Ez az objektum két típusú mezőt tartalmaz `string` `integer` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Az eszköz modelljének következő kódrészlete egy tulajdonság definícióját mutatja be `vector` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

Az eszköz ügyfelének egy JSON-adattartalmat kell küldenie, amely a következő példához hasonlóan jelenik meg, mint az eszköz Twin-beli jelentett tulajdonsága:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Írható tulajdonságok típusai

Ez a szakasz olyan írható tulajdonságokat mutat be, amelyeket az eszköz egy IoT Central alkalmazástól kap.

IoT Central az eszköztől az írható tulajdonságok frissítéseire választ vár. A válaszüzenetnek tartalmaznia kell a `ac` és a `av` mezőket. A `ad` mező kitöltése nem kötelező. Példákért tekintse meg az alábbi kódrészleteket.

`ac` egy numerikus mező, amely az alábbi táblázatban szereplő értékeket használja:

| Érték | Címke | Description |
| ----- | ----- | ----------- |
| `'ac': 200` | Befejeződött | A tulajdonság-módosítási művelet sikeresen befejeződött. |
| `'ac': 202`  vagy `'ac': 201` | Függőben | A tulajdonság-módosítási művelet függőben van vagy folyamatban van |
| `'ac': 4xx` | Hiba | A kért tulajdonság módosítása érvénytelen volt, vagy hiba történt. |
| `'ac': 5xx` | Hiba | Az eszköz váratlan hibát észlelt a kért módosítás feldolgozása során. |

`av` az eszközre eljuttatott verziószám.

`ad` egy paraméter-karakterlánc leírása.

Az eszköz modelljében az alábbi kódrészlet egy írható tulajdonság definícióját mutatja `string` be:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Az eszköz a következő hasznos adatokat kapja meg IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Az eszköznek a következő JSON-adattartalomot kell elküldenie IoT Central a frissítés feldolgozása után. Ez az üzenet tartalmazza a IoT Centraltól kapott eredeti frissítés verziószámát. Ha IoT Central fogadja ezt az üzenetet, az azt jelenti, hogy a tulajdonság a felhasználói felületen **szinkronizálva** jelenik meg:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Az eszköz modelljében az alábbi kódrészlet egy írható tulajdonság definícióját mutatja `Enum` be:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Az eszköz a következő hasznos adatokat kapja meg IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Az eszköznek a következő JSON-adattartalomot kell elküldenie IoT Central a frissítés feldolgozása után. Ez az üzenet tartalmazza a IoT Centraltól kapott eredeti frissítés verziószámát. Ha IoT Central fogadja ezt az üzenetet, az azt jelenti, hogy a tulajdonság a felhasználói felületen **szinkronizálva** jelenik meg:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Parancsok

> [!NOTE]
> A IoT Central webes felhasználói felületén kiválaszthatja a **várólistát, ha** a parancshoz offline lehetőség van. Ez a beállítás nem áll rendelkezésre, ha az eszköz sablonból exportál egy modellt vagy felületet.

Az eszköz modelljének következő kódrészlete egy olyan parancs definícióját jeleníti meg, amely nem rendelkezik paraméterekkel, és nem várta, hogy az eszköz semmit nem ad vissza:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

Az eszköz üres adattartalmat kap a kérelemben, és a válaszban egy `200` http-válasz kódjával üres adattartalmat ad vissza.

Az eszköz modelljében az alábbi kódrészlet egy Integer paraméterrel rendelkező parancs definícióját jeleníti meg, amely arra vár, hogy az eszköz egész értéket ad vissza:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

Az eszköz egész értéket kap a kérelem adattartalmaként. Az eszköznek egész értéket kell visszaadnia, amelynek a válasza a `200` http-válasz kódját adja vissza.

Az eszköz modelljének következő kódrészlete egy olyan parancs definícióját mutatja be, amely egy Object paraméterrel rendelkezik, és amely arra vár, hogy az eszköz visszaad egy objektumot. Ebben a példában mindkét objektum egész és sztring mezőket tartalmaz:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

Az alábbi kódrészlet egy példát mutat be az eszköznek elküldhető kérelemre:

```json
{ "Field1": 56, "Field2": "A string value" }
```

Az alábbi kódrészlet egy, az eszközről küldött válasz-adattartalmat mutat be. A `200` sikeres sikerességet a http-válasz kód használatával jelezheti:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>Hosszú ideig futó parancsok

Az eszköz modelljének következő kódrészlete egy parancs definícióját jeleníti meg. A parancs egy Integer paraméterrel rendelkezik, és az eszköz egy egész értéket ad vissza:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

Az eszköz egész értéket kap a kérelem adattartalmaként. Ha az eszköznek időre van szüksége a parancs feldolgozásához, az `202` eszköznek egy http-válasz kódját kell visszaadnia, amely jelzi, hogy az eszköz elfogadta a feldolgozásra vonatkozó kérést.

Amikor az eszköz befejezte a kérelem feldolgozását, a következő példához hasonló tulajdonságot kell küldenie az IoT Centralnak. A tulajdonság nevének meg kell egyeznie a parancs nevével:

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte az eszközök sablonjait, a következő lépésekből megtudhatja, hogyan regisztrálhat [Az Azure IoT Centralhoz](./concepts-get-connected.md) , és hogyan regisztrálja az eszközöket a IoT Central, és hogy miként IoT Central biztonságossá teszi az eszköz kapcsolatait.
