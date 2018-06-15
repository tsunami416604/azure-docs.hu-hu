---
title: Azure Media Services események esemény rács sémái
description: A Media Services események Azure esemény rácshoz tartozó tulajdonságait ismerteti
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788129"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure Media Services események esemény rács sémái

A cikkben a sémák és a tulajdonságok a Media Services események.

## <a name="media-services-job-state-change-event"></a>Media Services feladat állapotmódosítási esemény

Ez a témakör a tulajdonságok és -sémát a Media Services feladatállapotot változási eseményt.  

### <a name="available-event-types"></a>Rendelkezésre álló eseményoszlopok típusok

A Media Services **feladat** bocsát ki a következő esemény típusa:

| Esemény típusa | Leírás |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Jelenik meg, ha a feladat módosításokat állam. |

### <a name="example-event"></a>Példa esemény

A következő példa bemutatja a séma feladat állapota kész esemény: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Esemény tulajdonságai

Az esemény a következő legfelső szintű adatokat tartalmaz:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| A témakör | karakterlánc | Esemény rács biztosítja ezt az értéket. |
| Tulajdonos | karakterlánc | A Media Services-fiók erőforrás az erőforrás elérési útja |
| eventType | karakterlánc | Az esemény adatforrás regisztrált esemény típusok egyike. Például "Microsoft.Media.JobStateChange." |
| eventTime | karakterlánc | Az esemény jön létre az idő alapján a szolgáltató UTC idő szerint. |
| id | karakterlánc | Az esemény egyedi azonosítója. |
| adat | objektum | A Media Services eseményadatok. |
| dataVersion | karakterlánc | Az objektum séma verziója. A közzétevő a sémaverziót határozza meg. |
| metadataVersion | karakterlánc | Az esemény-metaadatok séma verziója. Esemény rács a séma legfelső szintű tulajdonság határozza meg. Esemény rács biztosítja ezt az értéket. |

Az objektum tulajdonságai a következők:

| Tulajdonság | Típus | Leírás |
| -------- | ---- | ----------- |
| previousState | karakterlánc | Az esemény előtt a feladat állapotát. |
| state | karakterlánc | Az új állapot a feladat ebben az esetben bejelentett. Például "Queued: erőforrások vár a feladat" vagy "ütemezett: A feladat készen áll a start".|

Ahol a feladat állapotát a értékek egyike lehet: *várakozik*, *ütemezett*, *feldolgozása*, *befejezett*, *hiba*, *Megszakítva*, *megszakítása*

## <a name="next-steps"></a>További lépések

[Feladat az állapotváltozási eseményekhez tartozó regisztrálása](job-state-events-cli-how-to.md)
