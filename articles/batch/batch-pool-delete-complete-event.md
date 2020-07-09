---
title: Azure Batch készlet törlése kész esemény
description: A Batch-készlet törlésének befejezési eseménye. Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2020
ms.locfileid: "85962458"
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

|Elem|Típus|Jegyzetek|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`startTime`|DateTime|A készlet törlésének időpontja.|
|`endTime`|DateTime|A készlet törlésének időpontja.|

## <a name="remarks"></a>Megjegyzések
A készlet-átméretezési művelet állapotával és hibakódokkal kapcsolatos további információkért lásd: [készlet törlése egy fiókból](/rest/api/batchservice/delete-a-pool-from-an-account).
