---
title: "Azure Batch-készlet törlése befejeződésének eseményét. |} Microsoft Docs"
description: "Útmutató a Batch-készlet törlése befejeződésének eseményét."
services: batch
author: tamram
manager: timlt
ms.assetid: 
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: tamram
ms.openlocfilehash: 890f2ba7fda37060c56177868d6214d517d91831
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="pool-delete-complete-event"></a>Készlet törlés befejeződésének eseményét.

 Ez az esemény is ki lesz adva a készlet törlési művelet befejeződésekor.

 A következő példa bemutatja a készletben törlés befejeződésének eseményét törzsét.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|id|Karakterlánc|A készlet azonosítóját.|
|startTime|Dátum és idő|A készlet törlése indulásakor.|
|Befejezés időpontja|Dátum és idő|Az az idő a készlet törlése befejeződött.|

## <a name="remarks"></a>Megjegyzések
Állapotok és hibakódok készlet átméretezés kapcsolatos további információkért lásd: [címkészlet törlése fiókból](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).