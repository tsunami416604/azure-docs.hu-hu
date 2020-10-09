---
title: StylesObject dinamikus Azure Maps
description: A dinamikus Azure Maps létrehozásához használt StylesObject tartozó JSON-séma és-szintaxis hivatkozási útmutatója.
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/19/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 4b085fbc6e330d38b59fce0c494f672b00c712b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85120520"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>StylesObject-séma – útmutató dinamikus térképekhez

Ez a cikk egy útmutató a JSON-sémához és szintaxisához `StylesObject` . A a `StylesObject` `StyleObject` stateset-stílusokat jelképező tömb. A Azure Maps Creator [szolgáltatás állapotának szolgáltatásával](https://docs.microsoft.com/rest/api/maps/featurestate) alkalmazhatja a stateset stílusait a beltéri Térkép adatszolgáltatásaira. Miután létrehozta a stateset stílusait, és hozzárendelte őket a beltéri térképi funkciókhoz, használhatja őket dinamikus beltéri térképek létrehozásához. A dinamikus beltéri térképek létrehozásával kapcsolatos további információkért lásd: [dinamikus stílus implementálása a Creator beltéri térképekhez](indoor-map-dynamic-styling.md).

## <a name="styleobject"></a>StyleObject

A a `StyleObject` [`BooleanTypeStyleRule`](#booleantypestylerule) vagy a vagy a [`NumericTypeStyleRule`](#numerictypestylerule) .

Az alábbi JSON egy névvel ellátott `BooleanTypeStyleRule` `occupied` és egy névvel ellátott nevet mutat be `NumericTypeStyleRule` `temperature` .

```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 A a `NumericTypeStyleRule`  [`StyleObject`](#styleobject) és a a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `keyName` | sztring | Az *állapot* vagy a dinamikus tulajdonság neve. Az A `keyName` tömbön belül egyedinek kell lennie `StyleObject` .| Igen |
| `type` | sztring | Az érték "numerikus". | Igen |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| Numerikus stílusú tartományok tömbje, amelyekhez társított színek tartoznak. Az egyes tartományok olyan színeket határoznak meg, amelyeket akkor kell használni, ha az *állapot* megfelel a tartománynak.| Igen |

### <a name="numberruleobject"></a>NumberRuleObject

A egy `NumberRuleObject` [`RangeObject`](#rangeobject) és egy `color` tulajdonságot tartalmaz. Ha az *állapot* értéke a tartományba esik, a megjelenített szín a tulajdonságban megadott szín lesz `color` .

Ha több átfedésben lévő tartományt határoz meg, a választott szín az első tartományba tartozó szín lesz.

A következő JSON-mintában mindkét tartomány igaz értéket fog tartani, ha az *állapot* értéke 50-60. Azonban a használni kívánt szín a `#343deb` lista első olyan tartománya, amely teljesült.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `range` | [RangeObject](#rangeobject) | A [RangeObject](#rangeobject) logikai tartományra vonatkozó feltételeket határoz meg, amelyek esetében az `true` *állapot* megjelenítési színét a tulajdonságban megadott színre kell módosítani `color` . Ha `range` nincs megadva, akkor a tulajdonságban definiált szín `color` mindig fel lesz használva.   | Nem |
| `color` | sztring | Az a szín, amelyet akkor kell használni, ha az állapot értéke a tartományba esik. A `color` tulajdonság egy JSON-karakterlánc a következő formátumok valamelyikében: <ul><li> HTML-stílusú hexadecimális értékek </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Előre definiált HTML-színek nevei, például sárga és kék.</li></ul> | Igen |

### <a name="rangeobject"></a>RangeObject

A `RangeObject` meghatározza az a numerikus tartomány értékét [`NumberRuleObject`](#numberruleobject) . Ahhoz, hogy az *állapot* értéke a tartományba essen, az összes megadott feltételnek igaznak kell lennie. 

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `minimum` | double | Az x ≥ x `minimum` . szám.| Nem |
| `maximum` | double | Az x összes x ≤ betűje `maximum` . | Nem |
| `exclusiveMinimum` | double | Az x által > x szám `exclusiveMinimum` .| Nem |
| `exclusiveMaximum` | double | Az x által < x szám `exclusiveMaximum` .| Nem |

### <a name="example-of-numerictypestylerule"></a>Példa NumericTypeStyleRule

A következő JSON egy `NumericTypeStyleRule` nevű *állapotot* ábrázol `temperature` . Ebben a példában a [`NumberRuleObject`](#numberruleobject) két meghatározott hőmérsékleti tartományt és a hozzájuk társított színstílusokat tartalmazza. Ha a hőmérséklet tartománya 50-69, a kijelzőnek a színt kell használnia `#343deb` .  Ha a hőmérséklet tartománya 31-70, a kijelzőnek a színt kell használnia `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

A a `BooleanTypeStyleRule` [`StyleObject`](#styleobject) és a a következő tulajdonságokat tartalmazza:

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `keyName` | sztring |  Az *állapot* vagy a dinamikus tulajdonság neve.  Az értéknek `keyName` egyedinek kell lennie a Style tömbben.| Igen |
| `type` | sztring |Az érték "boolean". | Igen |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)1| Logikai pár, amely színekkel `true` és `false` *állapotokkal* rendelkezik.| Igen |

### <a name="booleanruleobject"></a>BooleanRuleObject

A `BooleanRuleObject` meghatározza a színeket `true` és az `false` értékeket.

| Tulajdonság | Típus | Leírás | Kötelező |
|-----------|----------|-------------|-------------|
| `true` | sztring | Az *állapot* értékének megadásakor használandó szín `true` A `color` tulajdonság egy JSON-karakterlánc a következő formátumok valamelyikében: <ul><li> HTML-stílusú hexadecimális értékek </li><li> RGB ("#ff0", "#ffff00", "RGB (255, 255, 0)")</li><li> RGBA ("RGBA (255, 255, 0, 1)")</li><li> HSL ("HSL (100, 50%, 50%)")</li><li> HSLA ("HSLA (100, 50%, 50%, 1)")</li><li> Előre definiált HTML-színek nevei, például sárga és kék.</li></ul>| Igen |
| `false` | sztring | Az *állapot* értékének megadásakor használandó szín `false` | Igen |

### <a name="example-of-booleantypestylerule"></a>Példa BooleanTypeStyleRule

A következő JSON egy `BooleanTypeStyleRule` nevű *állapotot* ábrázol `occupied` . A [`BooleanRuleObject`](#booleanruleobject) meghatározza a színeket `true` és az `false` értékeket.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```
