---
title: Eseményszűrés az Azure Event Gridhöz
description: Bemutatja, hogyan szűrheti az eseményeket az Azure Event Grid-előfizetés létrehozásakor.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/21/2019
ms.author: spelluru
ms.openlocfilehash: f9fca0a9fefb5959747a4492139ae422a118db02
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70390182"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Az Event Grid-előfizetések eseményszűrésének ismertetése

Ez a cikk a végpontra küldött események szűrésének különböző módjait ismerteti. Esemény-előfizetés létrehozásakor három lehetőség közül választhat:

* Eseménytípusok
* A téma a
* Speciális mezők és operátorok

## <a name="event-type-filtering"></a>Eseménytípus-szűrés

Alapértelmezés szerint az eseményforrás összes [eseménytípusa](event-schema.md) a végpontra kerül. Dönthet úgy, hogy csak bizonyos eseménytípusokat küld a végpontra. Például értesítést kaphat az erőforrások frissítéseiről, de más műveletekről, például a törlésről nem. Ebben az esetben szűrje az `Microsoft.Resources.ResourceWriteSuccess` esemény típusa szerint. Adjon meg egy tömböt az `All` eseménytípusokkal, vagy adja meg, hogy az eseményforrás összes eseménytípusát leszeretné-e kérni.

Az eseménytípus szerinti szűrés JSON-szintaxisa a következő:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Tárgy szűrése

A tárgy szerinti egyszerű szűréshez adja meg a tárgy kezdő vagy záró értékét. Megadhatja például, `.txt` hogy a tárgy csak a szöveges fájl tárfiókba való feltöltésével kapcsolatos eseményeket kapjon. Vagy szűrheti a témát, `/blobServices/default/containers/testcontainer` amely a tároló összes eseményének lekérnie, de a tárfiók ban lévő többi tárolót nem.

Amikor eseményeket tesz közzé egyéni témakörökben, hozzon létre témákat az eseményekhez, amelyek megkönnyítik az előfizetők számára, hogy megtudják, érdekli-e őket az esemény. Az előfizetők a tárgy tulajdonsággal szűrhetik és irányíthatják az eseményeket. Fontolja meg az esemény eseményhelyének hozzáadását, hogy az előfizetők az adott útvonal szegmensei szerint szűrhessenek. Az elérési út lehetővé teszi az előfizetők számára az események szűk vagy széles körű szűrését. Ha a tárgyhoz hasonló `/A/B/C` háromszegmenses útvonalat ad meg, az előfizetők az első szegmens `/A` szerint szűrhetnek, hogy az események széles körét kapják. Ezek az előfizetők kap `/A/B/C` események `/A/D/E`témák, mint a vagy . Más előfizetők szűrhetnek, `/A/B` hogy szűkebb eseményeket kapjanak.

A tárgy szerinti szűrés JSON-szintaxisa:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Speciális szűrés

Ha az adatmezőkben értékek szerint szeretne szűrni, és meg szeretné adni az összehasonlító operátort, használja a speciális szűrési beállítást. A speciális szűrésben a következőket adhatja meg:

* operátor típusa - Az összehasonlítás típusa.
* kulcs – A szűréshez használt eseményadatok mezője. Ez lehet szám, logikai vagy karakterlánc.
* érték vagy értékek - A kulcshoz összehasonlítandó érték vagy értékek.

Ha egyetlen szűrőt ad meg több értékkel, a program **vagy** műveletet hajt végre, így a kulcsmező értékének ezen értékek egyikének kell lennie. Például:

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

Ha több különböző szűrőt ad meg, a rendszer **és** műveletet hajt végre, ezért minden szűrőfeltételnek teljesülnie kell. Például: 

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

### <a name="operator"></a>Művelet

A számokhoz rendelkezésre álló operátorok a következők:

* SzámGreaterThan
* NumberGreaterThanOrEquals
* NumberlessThan (Számtalan)
* NumberLessThanOrEquals (Szám nélküli thanOrEquals)
* Számszám
* Számnotin

A booleans elérhető operátora: BoolEquals

A karakterláncok elérhető operátorai a következők:

* Karakterlánctartalmazza
* StringBeginswith
* Karakterláncvégek
* Stringin között
* StringNotIn között

Minden karakterlánc-összehasonlítás eset-érzéketlen.

### <a name="key"></a>Kulcs

Az Event Grid séma eseményeihez használja a következő értékeket a kulcshoz:

* ID (Azonosító)
* Témakör
* Tárgy
* EventType (Eseménytípus)
* DataVersion
* Eseményadatok (például Data.key1)

A Cloud Events sémában lévő eseményekesetén használja a következő értékeket a kulcshoz:

* Napszállta
* Forrás
* EventType (Eseménytípus)
* EventTypeVersion
* Eseményadatok (például Data.key1)

Egyéni bemeneti séma esetén használja az eseményadatmezőket (például Data.key1).

### <a name="values"></a>Értékek

Az értékek a következők lehetnek:

* szám
* sztring
* logikai
* tömb

### <a name="limitations"></a>Korlátozások

A speciális szűrés a következő korlátozásokkal rendelkezik:

* Eseményrács-előfizetésenként öt speciális szűrő
* 512 karakter karakterlánconként
* Öt érték **a be-** és nem a kezelőkre **not in**

Ugyanaz a kulcs több szűrőben is használható.

## <a name="next-steps"></a>További lépések

* Az események PowerShell és Azure CLI használatával történő szűréséről az [Eseményrács eseményeinek szűrése(szűrő).](how-to-filter-events.md)
* Az Event Grid használatának gyors megkezdéséhez olvassa el az [Egyéni események létrehozása és irányítása az Azure Event Griddel című témakört.](custom-event-quickstart.md)
