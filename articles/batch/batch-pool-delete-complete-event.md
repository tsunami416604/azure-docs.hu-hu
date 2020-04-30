---
title: Azure Batch készlet törlése kész esemény
description: A Batch-készlet törlésének befejezési eseménye. Ez az esemény akkor van kibocsátva, ha a készlet törlési művelete befejeződött.
ms.topic: article
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d28223c79f96f35a6ee11f98e0f09f21d7db4451
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115941"
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

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
|`startTime`|DateTime|A készlet törlésének időpontja.|
|`endTime`|DateTime|A készlet törlésének időpontja.|

## <a name="remarks"></a>Megjegyzések
A készlet-átméretezési művelet állapotával és hibakódokkal kapcsolatos további információkért lásd: [készlet törlése egy fiókból](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).
