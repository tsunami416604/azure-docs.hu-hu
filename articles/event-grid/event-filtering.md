---
title: Azure Event Grid eseményeinek szűrése
description: Ismerteti, hogyan szűrhetők az események Azure Event Grid előfizetés létrehozásakor.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390182"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid-előfizetések esemény-szűrésének ismertetése

Ez a cikk azokat a különböző módszereket ismerteti, amelyekkel szűrheti, hogy mely eseményeket küldi a rendszer a végpontnak. Egy esemény-előfizetés létrehozásakor három lehetőség van a szűrésre:

* Események típusai
* A tárgy kezdete vagy vége
* Speciális mezők és operátorok

## <a name="event-type-filtering"></a>Eseménytípus szűrése

Alapértelmezés szerint az eseményforrás összes [eseménytípus](event-schema.md) küldése a végpontnak történik. Dönthet úgy is, hogy csak bizonyos eseménytípus küldését küldi el a végpontnak. Például értesítést kaphat az erőforrásairól, de nem kap értesítést más műveletekhez, például törlésekhez. Ebben az esetben az `Microsoft.Resources.ResourceWriteSuccess` eseménytípus alapján szűrhet. Adjon meg egy tömböt az eseménytípus közül, vagy `All` adja meg az eseményforrás összes eseménytípus beolvasását.

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

Egyszerű szűréshez a tárgy mezőben meg kell adni a tulajdonos kezdő vagy záró értékét. Megadhatja például, `.txt` hogy a tárgy vége legyen, hogy csak a szövegfájlok Storage-fiókba való feltöltésével kapcsolatos események legyenek lekérdezve. Másik lehetőségként szűrheti a tulajdonost `/blobServices/default/containers/testcontainer` , hogy lekérje a tároló összes eseményét, de a Storage-fiókban nem található más tároló.

Az események egyéni témakörökbe való közzétételekor olyan témákat hozhat létre az eseményekhez, amelyek megkönnyítik az előfizetők számára, hogy megismerjék, hogy érdeklik-e az esemény. Az előfizetők a tárgy tulajdonságot használják az események szűrésére és irányítására. Vegye fontolóra azt az elérési utat, ahol az esemény megtörtént, így az előfizetők az elérési út szegmensei alapján szűrhetik. Az elérési út lehetővé teszi az előfizetők számára az események szűk vagy széles körű szűrését. Ha három szegmens elérési utat ad meg `/A/B/C` , mint a tárgy, az előfizetők az első szegmens `/A` alapján szűrhetik az események széles körét. Ezek az előfizetők olyan eseményeket kapnak, `/A/B/C` mint `/A/D/E`a vagy a. Más előfizetők is szűrhetik `/A/B` a t, hogy Szűkítse az események szűk körét.

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
* érték vagy értékek – a kulcshoz összehasonlítandó érték vagy értékek.

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

### <a name="operator"></a>Operator

A számok számára elérhető operátorok a következők:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

A logikai értékek számára elérhető operátor a következő: BoolEquals

A karakterláncok elérhető operátorai a következők:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Az összes karakterlánc-összehasonlítás a Case-insensitve.

### <a name="key"></a>Kulcs

A Event Grid sémában lévő eseményekhez használja a következő értékeket a kulcshoz:

* id
* Témakör
* Subject
* EventType
* DataVersion
* Esemény-és adatértékek (például az key1)

A Cloud Events sémában lévő események esetében használja a következő értékeket a kulcshoz:

* EventId
* Source
* EventType
* EventTypeVersion
* Esemény-és adatértékek (például az key1)

Egyéni bemeneti séma esetén használja az esemény adatmezőit (például az adatok. key1).

### <a name="values"></a>Értékek

Az értékek a következőket tehetik:

* number
* Karakterlánc
* boolean
* array

### <a name="limitations"></a>Korlátozások

A speciális szűrés a következő korlátozásokkal rendelkezik:

* Öt speciális szűrő/Event Grid-előfizetés
* 512 karakter/karakterlánc érték
* Öt érték a **-ben** és **nem az** operátorokban

Ugyanaz a kulcs több szűrőben is használható.

## <a name="next-steps"></a>További lépések

* Az események PowerShell-lel és az Azure CLI-vel való szűrésével kapcsolatos további tudnivalókért lásd: [Események szűrése Event Grid](how-to-filter-events.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).
