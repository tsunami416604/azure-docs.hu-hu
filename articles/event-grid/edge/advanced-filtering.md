---
title: Speciális szűrés – Azure Event Grid IoT Edge | Microsoft Docs
description: Speciális szűrés a IoT Edge Event Gridban.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992560"
---
# <a name="advanced-filtering"></a>Speciális szűrés
Event Grid lehetővé teszi a JSON-adattartalomban lévő bármely tulajdonsághoz tartozó szűrők megadását. Ezek a szűrők `AND` feltételek szerint vannak modellezve, és minden külső feltétel opcionális belső `OR` feltételekkel rendelkezik. Minden `AND` feltételnél a következő értékeket kell megadnia:

* `OperatorType` – az összehasonlítás típusa.
* `Key` – a szűrő alkalmazásához használt tulajdonság JSON-elérési útja.
* `Value` – a szűrő futtatására szolgáló hivatkozási érték (vagy) `Values` – azon hivatkozási értékek összessége, amelyeken a szűrő fut.

## <a name="json-syntax"></a>JSON-szintaxis

A speciális szűrő JSON-szintaxisa a következő:

```json
{
    "filter": {
        "advancedFilters": [{
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key1",
                "value": 5
            }, {
                "operatorType": "StringContains",
                "key": "Subject",
                "values": ["container1", "container2"]
            }
        ]
    }
}
```

## <a name="filtering-on-array-values"></a>Tömb értékeinek szűrése

A Event Grid jelenleg nem támogatja a szűrést az értékek tömbje számára. Ha egy bejövő esemény tömb értéke a speciális szűrő kulcsa, a megfeleltetési művelet meghiúsul. A bejövő esemény nem egyezik az esemény-előfizetéssel.

## <a name="and-or-not-semantics"></a>ÉS-vagy-nem szemantika

Figyelje meg, hogy a korábban megadott JSON-példában `AdvancedFilters` egy tömb. Minden `AdvancedFilter` Array elemnek `AND` feltételnek kell lennie.

A több értéket támogató operátorok (például `NumberIn`, `NumberNotIn`, `StringIn`stb.) esetében az egyes értékeket `OR` feltételként kezeli a rendszer. Így a `StringBeginsWith("a", "b", "c")` minden olyan karakterlánc-értékhez meg fog egyezni, amely `a` vagy `b` vagy `c`.

> [!CAUTION]
> A nem operátorok – a `Values` mezőben megadott értékeken a `NumberNotIn` és a `StringNotIn` viselkednek.
>
> Ezzel a művelettel a szűrő fogadja az összes szűrőt, és legyőzze a szűrés célját.

## <a name="floating-point-rounding-behavior"></a>Lebegőpontos kerekítési viselkedés

A Event Grid a `decimal` .NET-típust használja az összes numerikus érték kezelésére. Az esemény-előfizetési JSON-ban megadott számértékek nem vonatkoznak a lebegőpontos kerekítési viselkedésre.

## <a name="case-sensitivity-of-string-filters"></a>Karakterlánc-szűrők kis-és nagybetűk megkülönböztetése

Az összes karakterlánc-összehasonlítás a kis-és nagybetűk megkülönböztetése nélkül történik. Ez a viselkedés jelenleg nem módosítható.

## <a name="allowed-advanced-filter-keys"></a>Engedélyezett speciális szűrő kulcsok

A `Key` tulajdonság lehet egy jól ismert legfelső szintű tulajdonság, vagy lehet egy több pontot tartalmazó JSON-útvonal, ahol minden egyes pont egy beágyazott JSON-objektumra mutat.

A Event Grid nem rendelkezik speciális jelentéssel a kulcsban szereplő `$` karakterhez, a JSONPath-specifikációtól eltérően.

### <a name="event-grid-schema"></a>Event Grid-séma

A Event Grid sémában lévő eseményekhez:

* ID (Azonosító)
* Témakör
* Tárgy
* EventType
* dataVersion
* Az Prop1.
* Az adat. prop * Prop2. Prop3. Prop4. Prop5

### <a name="custom-event-schema"></a>Egyéni esemény sémája

Nincs korlátozás a `Key` az egyéni esemény sémájában, mivel Event Grid nem kényszeríti ki a hasznos adatokhoz tartozó összes boríték-sémát.

## <a name="numeric-single-value-filter-examples"></a>Példák numerikus egyértékű szűrőre

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberGreaterThan",
                "key": "Data.Key1",
                "value": 5
            },
            {
                "operatorType": "NumberGreaterThanOrEquals",
                "key": "Data.Key2",
                "value": *456
            },
            {
                "operatorType": "NumberLessThan",
                "key": "Data.P*P2.P3",
                "value": 1000
            },
            {
                "operatorType": "NumberLessThanOrEquals",
                "key": "Data.P*P2",
                "value": 999
            }
        ]
    }
}
```

## <a name="numeric-range-value-filter-examples"></a>Numerikus tartomány – érték szűrő – példák

* NumberIn
* NumberNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "NumberIn",
                "key": "Data.Key1",
                "values": [1, 10, 100]
            },
            {
                "operatorType": "NumberNotIn",
                "key": "Data.Key2",
                "values": [2, 3, 4.56]
            }
        ]
    }
}
```

## <a name="string-range-value-filter-examples"></a>Karakterlánc-tartomány – példák az értékek szűrésére

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "StringContains",
                "key": "Data.Key1",
                "values": ["microsoft", "azure"]
            },
            {
                "operatorType": "StringBeginsWith",
                "key": "Data.Key2",
                "values": ["event", "grid"]
            },
            {
                "operatorType": "StringEndsWith",
                "key": "Data.P3.P4",
                "values": ["jpg", "jpeg", "png"]
            },
            {
                "operatorType": "StringIn",
                "key": "RootKey",
                "values": ["exact", "string", "matches"]
            },
            {
                "operatorType": "StringNotIn",
                "key": "RootKey",
                "values": ["aws", "bridge"]
            }
        ]
    }
}
```

## <a name="boolean-single-value-filter-examples"></a>Logikai egyértékű szűrési példák

* BoolEquals

```json
{
    "filter": {
        "advancedFilters": [
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey1",
                "value": true
            },
            {
                "operatorType": "BoolEquals",
                "key": "BoolKey2",
                "value": false
            }
        ]
    }
}
```
