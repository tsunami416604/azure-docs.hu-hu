---
title: Azure Batch készlet törlése kész esemény
description: A Batch-készlet törlésének befejezési eseménye. Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: 56dc4c91566cc5c41de6c91618d09e7a0ebb7172
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2020
ms.locfileid: "76929861"
---
# <a name="pool-delete-complete-event"></a>Készlet törlése kész esemény

 Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.

 Az alábbi példa egy készlet törlésének befejezése eseményt mutatja.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elem|Type (Típus)|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`startTime`|Dátum és idő|A készlet törlésének időpontja.|
|`endTime`|Dátum és idő|A készlet törlésének időpontja.|

## <a name="remarks"></a>Megjegyzések
A készlet-átméretezési művelet állapotával és hibakódokkal kapcsolatos további információkért lásd: [készlet törlése egy fiókból](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
