---
title: Esemény-sémák – Azure Event Grid IoT Edge | Microsoft Docs
description: A IoT Edge Event Gridban található esemény-sémák.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242464"
---
# <a name="event-schemas"></a>Eseménysémák

Event Grid modul JSON formátumban fogadja és kézbesíti az eseményeket. A Event Grid jelenleg három sémát támogat: –

* **EventGridSchema**
* **CustomSchema**
* **CloudEventSchema**

Beállíthatja azt a sémát, amelyet a közzétevőnek meg kell felelnie a témakör létrehozása során. Ha nincs megadva, az alapértelmezett érték a **EventGridSchema**. Azok az események, amelyek nem felelnek meg a várt sémának, el lesznek utasítva.

Az előfizetők is konfigurálhatják azt a sémát, amelyben az eseményeket el szeretnék juttatni. Ha nincs megadva, az alapértelmezett érték a témakör sémája.
A jelenleg előfizetői kézbesítési sémának meg kell egyeznie a témakör bemeneti sémájával. 

## <a name="eventgrid-schema"></a>EventGrid séma

A EventGrid séma olyan kötelező tulajdonságokat tartalmaz, amelyeket a közzétételi entitásnak meg kell felelnie. Minden közzétevőnek fel kell töltenie a legfelső szintű mezőket.

```json
[
  {
    "topic": string,
    "subject": string,
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string,
    "metadataVersion": string
  }
]
```

### <a name="eventgrid-schema-properties"></a>EventGrid-séma tulajdonságai

Minden esemény a következő legfelső szintű adattal rendelkezik:

| Tulajdonság | Type (Típus) | Szükséges | Leírás |
| -------- | ---- | ----------- |-----------
| témakör | sztring | Nem | Meg kell egyeznie azzal a témával, amelyen közzé lett téve. Event Grid feltölti azt a témakör nevével, amelyen közzé van téve, ha nincs meghatározva. |
| Tulajdonos | sztring | Igen | Közzétevő által megadott elérési út az esemény tárgya számára. |
| EventType | sztring | Igen | Az eseményforrás eseménytípus, például BlobCreated. |
| eventTime | sztring | Igen | Az esemény a szolgáltató UTC-ideje alapján történő létrehozásakor. |
| ID (Azonosító) | sztring | Nem | Az esemény egyedi azonosítója. |
| adatok | objektum | Nem | A közzétételi entitásra jellemző esemény-adatmennyiség rögzítésére szolgál. |
| dataVersion | sztring | Igen | Az adatobjektum séma-verziója. A közzétevő határozza meg a séma verzióját. |
| metadataVersion | sztring | Nem | Az esemény metaadatainak séma-verziója. Event Grid a legfelső szintű tulajdonságok sémáját határozza meg. Event Grid megadja ezt az értéket. |

### <a name="example--eventgrid-schema-event"></a>Példa: EventGrid-séma esemény

```json
[
  {
    "id": "1807",
    "eventType": "recordInserted",
    "subject": "myapp/vehicles/motorcycles",
    "eventTime": "2017-08-10T21:03:07+00:00",
    "data": {
      "make": "Ducati",
      "model": "Monster"
    },
    "dataVersion": "1.0"
  }
]
```

## <a name="customevent-schema"></a>CustomEvent séma

Az egyéni sémában nincsenek olyan kötelező tulajdonságok, amelyek a EventGrid sémához hasonlóan vannak kikényszerítve. A közzétételi entitás teljes mértékben tudja szabályozni az esemény sémáját. Maximális rugalmasságot biztosít, és olyan forgatókönyveket tesz lehetővé, ahol már van egy eseményvezérelt rendszer, és a meglévő eseményeket újra fel szeretné használni, és/vagy nem kívánja lekötni egy adott sémához.

### <a name="custom-schema-properties"></a>Egyéni séma tulajdonságai

Nincsenek kötelező tulajdonságok. A közzétételi entitás a hasznos adatok megállapításához szükséges.

### <a name="example--custom-schema-event"></a>Példa – egyéni séma-esemény

```json
[
  {
    "eventdata": {
      "make": "Ducati",
      "model": "Monster"
    }
  }
]
```

## <a name="cloudevent-schema"></a>CloudEvent séma

A fenti sémák mellett Event Grid natív módon támogatja az eseményeket a [CLOUDEVENTS JSON-sémában](https://github.com/cloudevents/spec/blob/master/json-format.md). A CloudEvents egy nyílt specifikáció az események leírásához. Megkönnyíti az együttműködési képességet azáltal, hogy egy közös esemény-sémát biztosít az események közzétételéhez és felhasználásához. A [CNCF](https://www.cncf.io/) része, és jelenleg az 1,0-RC1-es verzió érhető el.

### <a name="cloudevent-schema-properties"></a>CloudEvent-séma tulajdonságai

A kötelező boríték tulajdonságainál tekintse meg a [CloudEvents specifikációját](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) .

### <a name="example--cloud-event"></a>Példa: Felhőbeli esemény
```json
[{
       "id": "1807",
       "type": "recordInserted",
       "source": "myapp/vehicles/motorcycles",
       "time": "2017-08-10T21:03:07+00:00",
       "datacontenttype": "application/json",
       "data": {
            "make": "Ducati",
            "model": "Monster"
        },
        "dataVersion": "1.0",
        "specVersion": "1.0-rc1"
}]
```
