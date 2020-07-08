---
title: Azure Event Grid eseményeinek szűrése
description: Ismerteti, hogyan szűrhetők az események Azure Event Grid előfizetés létrehozásakor.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: spelluru
ms.openlocfilehash: afe97fd1736fbaa6858adb2fc658b4ab34546f84
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84456846"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid-előfizetések esemény-szűrésének ismertetése

Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel szűrheti, hogy mely eseményeket küldi a rendszer a végpontnak. Egy esemény-előfizetés létrehozásakor három lehetőség van a szűrésre:

* Eseménytípusok
* A tárgy kezdete vagy vége
* Speciális mezők és operátorok

## <a name="event-type-filtering"></a>Eseménytípus szűrése

Alapértelmezés szerint az eseményforrás összes [eseménytípus](event-schema.md) küldése a végpontnak történik. Dönthet úgy is, hogy csak bizonyos eseménytípus küldését küldi el a végpontnak. Például értesítést kaphat az erőforrásairól, de nem kap értesítést más műveletekhez, például törlésekhez. Ebben az esetben az eseménytípus alapján szűrhet `Microsoft.Resources.ResourceWriteSuccess` . Adjon meg egy tömböt az eseménytípus közül, vagy adja meg az eseményforrás `All` összes eseménytípus beolvasását.

Az Eseménytípus szerinti szűrés JSON-szintaxisa a következő:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Tulajdonos szűrése

Egyszerű szűréshez a tárgy mezőben meg kell adni a tulajdonos kezdő vagy záró értékét. Megadhatja például, hogy a tárgy vége legyen, `.txt` hogy csak a szövegfájlok Storage-fiókba való feltöltésével kapcsolatos események legyenek lekérdezve. Másik lehetőségként szűrheti a tulajdonost, `/blobServices/default/containers/testcontainer` hogy lekérje a tároló összes eseményét, de a Storage-fiókban nem található más tároló.

Az események egyéni témakörökbe való közzétételekor olyan témákat hozhat létre az eseményekhez, amelyek megkönnyítik az előfizetők számára, hogy megismerjék, hogy érdeklik-e az esemény. Az előfizetők a tárgy tulajdonságot használják az események szűrésére és irányítására. Vegye fontolóra azt az elérési utat, ahol az esemény megtörtént, így az előfizetők az elérési út szegmensei alapján szűrhetik. Az elérési út lehetővé teszi az előfizetők számára az események szűk vagy széles körű szűrését. Ha három szegmens elérési utat ad meg `/A/B/C` , mint a tárgy, az előfizetők az első szegmens alapján szűrhetik az `/A` események széles körét. Ezek az előfizetők olyan eseményeket kapnak, mint a `/A/B/C` vagy a `/A/D/E` . Más előfizetők is szűrhetik a `/A/B` t, hogy Szűkítse az események szűk körét.

A következő JSON-szintaxis a tárgy szerinti szűréshez:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Speciális szűrés

Az adatmezőkben lévő értékek alapján történő szűréshez és az összehasonlító operátor megadásához használja a speciális szűrési lehetőséget. A speciális szűrés területen a következőket kell megadnia:

* operátor típusa – az összehasonlítás típusa.
* kulcs – a szűréshez használt esemény adatai mezője. Ez lehet szám, logikai vagy karakterlánc.
* értékek – a kulcshoz összehasonlítandó érték vagy értékek.

Ha több értékkel rendelkező egyetlen szűrőt ad meg, a rendszer egy **vagy** műveletet hajt végre, így a Key mező értékének az alábbi értékek egyikének kell lennie. Például:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Ha több különböző szűrőt is megad, a **és** a műveletet hajtja végre, így minden szűrési feltételnek teljesülnie kell. Például: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

### <a name="operators"></a>Operátorok

A **számok** számára elérhető operátorok a következők:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

A **logikai értékek** számára elérhető operátor a következő: 
- BoolEquals

A **karakterláncok** elérhető operátorai a következők:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Az összes karakterlánc-összehasonlítás **nem** megkülönbözteti a kis-és nagybetűket.

### <a name="key"></a>Kulcs

A Event Grid sémában lévő eseményekhez használja a következő értékeket a kulcshoz:

* ID
* Témakör
* Tárgy
* EventType
* DataVersion
* Esemény-és adatértékek (például az key1)

A Cloud Events sémában lévő események esetében használja a következő értékeket a kulcshoz:

* Napszállta
* Forrás
* EventType
* EventTypeVersion
* Esemény-és adatértékek (például az key1)

Egyéni bemeneti séma esetén használja az esemény adatmezőit (például az adatok. key1).

### <a name="values"></a>Értékek

Az értékek a következőket tehetik:

* szám
* sztring
* logikai
* tömb

### <a name="limitations"></a>Korlátozások

A speciális szűrés a következő korlátozásokkal rendelkezik:

* 5 speciális szűrő és 25 szűrő érték az összes szűrő/Event Grid-előfizetés alapján
* 512 karakter/karakterlánc érték
* Öt érték a **-ben** és **nem az** operátorokban
* Kulcsok ** `.` (pont)** karakterrel. Például: `http://schemas.microsoft.com/claims/authnclassreference` vagy `john.doe@contoso.com` . Jelenleg a kulcsok nem támogatják a Escape-karakterek használatát. 

Ugyanaz a kulcs több szűrőben is használható.

### <a name="examples"></a>Példák

### <a name="stringcontains"></a>StringContains

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

### <a name="stringbeginswith"></a>StringBeginsWith

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "grid"
    ]
}]
```

### <a name="stringendswith"></a>StringEndsWith

```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

### <a name="stringin"></a>StringIn

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "exact", 
        "string", 
        "matches"
    ]
}]
```

### <a name="stringnotin"></a>StringNotIn

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

### <a name="numberin"></a>NumberIn

```json

"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]

```

### <a name="numbernotin"></a>NumberNotIn

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0,
        0
    ]
}]
```

### <a name="numberlessthan"></a>NumberLessThan

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthan"></a>NumberGreaterThan

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

### <a name="numberlessthanorequals"></a>NumberLessThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

### <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

### <a name="boolequals"></a>BoolEquals

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```


## <a name="next-steps"></a>További lépések

* Az események PowerShell-lel és az Azure CLI-vel való szűrésével kapcsolatos további tudnivalókért lásd: [Események szűrése Event Grid](how-to-filter-events.md).
* Az Event Grid használatának gyors megkezdéséhez tekintse meg [az egyéni események létrehozása és irányítása Azure Event Grid](custom-event-quickstart.md)használatával című témakört.
