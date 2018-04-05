---
title: Azure Batch-készlet törlése befejeződésének eseményét. |} Microsoft Docs
description: Útmutató a Batch-készlet törlése befejeződésének eseményét.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: danlep
ms.openlocfilehash: bfcbcf40efc64ab1c79ee1a86e02502c68ad6d47
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-complete-event"></a>Készlet törlése kész esemény

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
|startTime|DateTime|A készlet törlése indulásakor.|
|endTime|DateTime|Az az idő a készlet törlése befejeződött.|

## <a name="remarks"></a>Megjegyzések
Állapotok és hibakódok készlet átméretezés kapcsolatos további információkért lásd: [címkészlet törlése fiókból](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).