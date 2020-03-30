---
title: Speciális szűrés – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Speciális szűrés az IoT Edge Eseményrácsban.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d7fdc5074f3c92eea4f236a9b1f7c823b930f391
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992560"
---
# <a name="advanced-filtering"></a>Speciális szűrés
Az Event Grid lehetővé teszi a szűrők megadását a json hasznos adat bármely tulajdonságán. Ezek a szűrők feltételek `AND` halmazaként vannak modellezve, és minden külső feltétel opcionális belső `OR` feltételekkel rendelkezik. Minden `AND` feltételhez a következő értékeket kell megadni:

* `OperatorType`- Az összehasonlítás típusa.
* `Key`- A json elérési útja ahhoz a tulajdonsághoz, amelyre a szűrőt alkalmazni szeretné.
* `Value`- Az a referenciaérték, amelyhez a `Values` szűrő fut (vagy) - Az a referenciaértékkészlet, amelyhez a szűrő fut.

## <a name="json-syntax"></a>JSON szintaxis

A speciális szűrő JSON szintaxisa a következő:

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

## <a name="filtering-on-array-values"></a>Szűrés tömbértékeken

Az Event Grid ma nem támogatja az értékek tömbjének szűrését. Ha egy bejövő esemény tömbértéke a speciális szűrő kulcsának, az egyeztetési művelet sikertelen lesz. A bejövő esemény végül nem felel meg az esemény-előfizetésnek.

## <a name="and-or-not-semantics"></a>ÉS-VAGY-NEM szemantika

Figyelje meg, hogy a json példa korábban megadott, `AdvancedFilters` egy tömb. Gondoljon `AdvancedFilter` az egyes `AND` tömbelemekre, mint egy feltételre.

A több értéket támogató operátorok `NumberIn`(például `NumberNotIn`, , `StringIn`, stb.) esetében minden egyes értéket `OR` feltételként kezel a rendszer. Tehát a `StringBeginsWith("a", "b", "c")` akarat megegyezik minden olyan `a` `b` karakterláncértékkel, amely vagy vagy `c`.

> [!CAUTION]
> A NOT operátorok - `NumberNotIn` és `StringNotIn` viselkednek, mint `Values` és feltételek et minden értéket adott a területen.
>
> Ha ezt nem teszi meg, a szűrő tetsző szűrővé válik, és meghiúsítja a szűrés célját.

## <a name="floating-point-rounding-behavior"></a>Lebegőpontos kerekítési viselkedés

Az Event `decimal` Grid a .NET típust használja az összes numerikus érték kezelésére. A JSON esemény-előfizetésben megadott számértékekre nem vonatkozik a lebegőpontos kerekítési viselkedés.

## <a name="case-sensitivity-of-string-filters"></a>Karakterlánc-szűrők kis- és nagybetűk megkülönböztetése

Minden karakterlánc-összehasonlítás nem imitot. Ma már nem lehet változtatni ezen a viselkedésen.

## <a name="allowed-advanced-filter-keys"></a>Engedélyezett speciális szűrőbillentyűk

A `Key` tulajdonság lehet egy jól ismert legfelső szintű tulajdonság, vagy több pontból álló json elérési út, ahol minden pont egy beágyazott json objektumba lép.

Az Event Grid nek nincs különleges `$` jelentése a kulcsban lévő karakterszámára, ellentétben a JSONPath specifikációval.

### <a name="event-grid-schema"></a>Eseményrács sémája

Az Eseményrács sémájában lévő események esetén:

* ID (Azonosító)
* Témakör
* Tárgy
* EventType (Eseménytípus)
* DataVersion
* Data.Prop1
* Data.Prop*Prop2.Prop3.Prop4.Prop5

### <a name="custom-event-schema"></a>Egyéni eseményséma

Nincs korlátozás az `Key` egyéni eseményséma, mivel event grid nem kényszerít i borítékséma a hasznos adat.

## <a name="numeric-single-value-filter-examples"></a>Példa numerikus egyértékű szűrőre

* SzámGreaterThan
* NumberGreaterThanOrEquals
* NumberlessThan (Számtalan)
* NumberLessThanOrEquals (Szám nélküli thanOrEquals)

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

## <a name="numeric-range-value-filter-examples"></a>Példák numerikus tartomány-érték szűrőre

* Számszám
* Számnotin

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

## <a name="string-range-value-filter-examples"></a>Példák karakterlánc-tartomány-érték szűrőre

* Karakterlánctartalmazza
* StringBeginswith
* Karakterláncvégek
* Stringin között
* StringNotIn között

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

## <a name="boolean-single-value-filter-examples"></a>Példák logikai egyértékű szűrőre

* BoolEgyenlő

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
