---
title: Eseménysémák – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Eseménysémák az IoT Edge Eseményrácsban.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ba261aeedf6574f69d3c05f8fd005c912dcc59d1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73242464"
---
# <a name="event-schemas"></a>Eseménysémák

Az Event Grid modul JSON formátumban fogadja és kézbesíti az eseményeket. Jelenleg három séma van, amelyet az Event Grid támogat: -

* **EventGridSchema**
* **EgyéniSéma**
* **CloudEventSchema**

Konfigurálhatja azt a sémát, amelynek a közzétevőnek meg kell felelnie a témakör létrehozása során. Ha nincs megadva, akkor alapértelmezés szerint **az EventGridSchema**lesz. A várt sémának nem megfelelő eseményeket a rendszer elutasítja.

Az előfizetők konfigurálhatják azt a sémát is, amelyben az eseményeket szállítani szeretnék. Ha nincs megadva, az alapértelmezett a témakör sémája.
Jelenleg az előfizetői kézbesítési sémának meg kell egyeznie a témakör bemeneti sémájával. 

## <a name="eventgrid-schema"></a>EventGrid-séma

Az EventGrid séma olyan szükséges tulajdonságokból áll, amelyeknek a közzétételi entitásnak meg kell felelnie. Minden közzétevőnek fel kell töltsee a legfelső szintű mezőket.

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

### <a name="eventgrid-schema-properties"></a>Az EventGrid séma tulajdonságai

Minden esemény a következő legfelső szintű adatokkal rendelkezik:

| Tulajdonság | Típus | Kötelező | Leírás |
| -------- | ---- | ----------- |-----------
| témakör | sztring | Nem | Meg kell egyeznie a témakör, amelyen megjelent. Az Event Grid feltölti azt annak a témakörnek a nevével, amelyen közzé lett téve, ha nincs megadva. |
| Tárgy | sztring | Igen | Az esemény tárgyra mutató, a közzétevő által megadott elérési út. |
| eventType | sztring | Igen | Az eseményforrás eseménytípusa, például blobCreated. |
| eventTime | sztring | Igen | Az esemény létrehozásának időpontja a szolgáltató UTC-ideje alapján. |
| ID (Azonosító) | sztring | Nem | Az esemény egyedi azonosítója |
| data | objektum | Nem | A közzétételi entitásra jellemző eseményadatok rögzítésére szolgál. |
| dataVersion | sztring | Igen | Az adatobjektum sémaverziója. A sémaverziót a közzétevő határozza meg. |
| metadataVersion | sztring | Nem | Az esemény metaadatok sémaverziója. A legfelső szintű tulajdonságokra az Event Grid határozza meg a sémát. Az értéket az Event Grid adja meg. |

### <a name="example--eventgrid-schema-event"></a>Példa – EventGrid-sémaesemény

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

Az egyéni sémában nincsenek kötelező tulajdonságok, amelyek az EventGrid sémához hasonlóan vannak kényszerítve. A közzétételi entitás teljes mértékben szabályozhatja az eseménysémát. Maximális rugalmasságot biztosít, és lehetővé teszi azokat a forgatókönyveket, amelyekben már van egy eseményalapú rendszer, és szeretné újra felhasználni a meglévő eseményeket, és/vagy nem szeretné, hogy egy adott sémához legyen kötve.

### <a name="custom-schema-properties"></a>Egyéni sématulajdonságai

Nincs kötelező tulajdonság. A tartalom meghatározása a közzétételi entitás feladata.

### <a name="example--custom-schema-event"></a>Példa – Egyéni sémaesemény

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

A fenti sémák mellett az Event Grid natív módon támogatja a [CloudEvents JSON-séma eseményeit](https://github.com/cloudevents/spec/blob/master/json-format.md)is. A CloudEvents egy nyílt specifikáció az eseményadatok leírására. Egyszerűsíti az interoperabilitást azáltal, hogy közös eseménysémát biztosít az események közzétételéhez és az események fogyasztásához. Ez része a [CNCF](https://www.cncf.io/) és a jelenleg rendelkezésre álló változat 1.0-rc1.

### <a name="cloudevent-schema-properties"></a>CloudEvent séma tulajdonságai

Tekintse meg a [CloudEvents specifikációt](https://github.com/cloudevents/spec/blob/master/json-format.md#3-envelope) a kötelező borítéktulajdonságokon.

### <a name="example--cloud-event"></a>Példa – felhőalapú esemény
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
