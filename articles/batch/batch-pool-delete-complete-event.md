---
title: Azure Batch készlet törlése kész esemény
description: A Batch-készlet törlésének befejezési eseménye. Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: f00d32fd5e4eb49fddf4975a9b64ce792d0226dd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
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
