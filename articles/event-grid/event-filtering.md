---
title: Az események szűrésének az Azure Event Gridhez
description: Ismerteti, hogyan használatával szűrhetők az események Azure Event Grid-előfizetés létrehozásakor.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: tomfitz
ms.openlocfilehash: 24337863d4e3f8e093c2e33afbb39364ec37516d
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50252186"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Az események szűrésének az Event Grid-előfizetések ismertetése

Ez a cikk ismerteti a végpont melyik események küldhetők szűrő különböző módjait. Egy esemény-előfizetés létrehozásakor, szűrésre három lehetősége van:

* Eseménytípusok
* Tulajdonos kezdődik vagy végződik.
* Speciális mezők és operátorok

## <a name="event-type-filtering"></a>Eseményszűrés típusa

Alapértelmezés szerint minden [eseménytípusok](event-schema.md) az eseményforrás a rendszer elküldte a végpontnak. Ha dönt, hogy csak bizonyos eseménytípusok végpontra küldhet. Például, is első értesítést a frissítések az erőforrásokhoz, de más műveletek, mint például a törlés nem kap értesítést. Ebben az esetben szűrés a `Microsoft.Resources.ResourceWriteSuccess` eseménytípus. Egy tömb rendelkezésére bocsátja az eseménytípusok, vagy adja meg `All` minden eseménytípushoz beolvasni az esemény forrását.

A JSON eseménytípus szerinti szűrés szintaxisa:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Szűrés tulajdonos

Egyszerű szűrés tulajdonos, adja meg egy kezdő vagy záró értéket a tárgyához. Például megadhatja, a tulajdonos végződik `.txt` csak beolvasni egy szöveges fájl feltöltése a storage-fiók kapcsolódó eseményeket. Vagy a tulajdonos szűrheti kezdődik `/blobServices/default/containers/testcontainer` az adott tárolóban, de nem más a tárfiókban lévő tárolók összes eseményének lekérdezése.

Amikor eseményeket tesz közzé egyéni témaköröket, hozzon létre a tulajdonosok az eseményeket, amelyek megkönnyítik a tudja-e azok érdeklik az esemény-előfizetők számára. Előfizetők használhatják a tulajdonos tulajdonság események szűrése és útvonalat. Fontolja meg az elérési út esetében, ahol az esemény történt, hozzáadását, így az elérési út szegmensei lehet szűrni az előfizetőknek. Az elérési út révén széles körben vagy szűken események szűrése előfizetők. Ha megad egy három szegmens elérési utat, például `/A/B/C` az e-mail tárgyát, az előfizetők az első szegmens szerint szűrheti `/A` különböző események beolvasásához. Ezeket az előfizetőket témákat, mint például az események beolvasása `/A/B/C` vagy `/A/D/E`. Más előfizetőkkel szerint szűrheti `/A/B` beolvasni a szűkebb események egy meghatározott készletének.

A JSON eseménytípus szerinti szűrés szintaxisa:

```json
"filter": {
  "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Speciális szűrés

Szűrés az adatokat a mezőkben szereplő értékek alapján, és adja meg az összehasonlító operátor, használja a speciális szűrési lehetőséget. A Speciális szűrés, megadhatja a:

* operátor - összehasonlítás típusa.
* kulcs – az eseményadatokat a szűréshez használt mezője. Egy szám, logikai vagy karakterlánc lehet.
* értékek – az értékek összehasonlításához a kulcsot.

A speciális szűrők használatát a JSON-szintaxisra van:

```json
"filter": {
  "advancedFilters": [
    {
      "Operator": "NumberGreaterThanOrEquals",
      "Key": "Data.Key1",
      "Values": 5
    },
    {
      "Operator": "StringContains",
      "Key": "Subject",
      "Values": ["container1", "container2"]
    }
  ]
}
```

### <a name="operator"></a>Művelet

A számok az elérhető operátorok a következők:

* NumberGreaterThan
* NumberGreaterThanOrEquals
* NumberLessThan
* NumberLessThanOrEquals
* NumberIn
* NumberNotIn

A rendelkezésre álló logikai operátort van: BoolEquals

Karakterláncokat az elérhető operátorok a következők:

* StringContains
* StringBeginsWith
* StringEndsWith
* StringIn
* StringNotIn

Az összes karakterlánc-összehasonlítások megkülönböztetik a kis-insensitve.

### <a name="key"></a>Kulcs

Az eseményeket az Event Grid-sémát használja a kulcs a következő értékeket:

* Azonosító
* Témakör
* Tárgy
* EventType
* dataVersion
* Eseményadatok (például Data.key1)

A felhő események sémája események a kulcs a következő értékeket használja:

* EventId
* Forrás
* EventType
* eventTypeVersion
* Eseményadatok (például Data.key1)

Egyéni bemeneti sémát használja (például Data.key1 Data.key1.key2) esemény adatmezőket.

### <a name="values"></a>Értékek

Az értékek lehetnek:

* szám
* sztring
* logikai
* tömb

### <a name="limitations"></a>Korlátozások

Speciális szűrés a következő korlátozások vonatkoznak:

* Event grid előfizetésenként öt speciális szűrők
* 512 karakter / karakterlánc értéke
* Az öt értékei **a** és **nem** operátorok
* A kulcs legfeljebb két (például data.key1.key2) beágyazási szintet

Ugyanazzal a kulccsal egynél több szűrőt is használható.

## <a name="next-steps"></a>További lépések

* Eseményszűrés a PowerShell és az Azure CLI-vel kapcsolatos további információkért lásd: [események szűréséhez állítsa be az Event Gridhez](how-to-filter-events.md).
* Tekintse meg a gyors kezdéshez Event Grid használatával [az Azure Event Griddel egyéni események létrehozása és útvonal](custom-event-quickstart.md).