---
title: Az Azure Batch-készlet törlése kész esemény |} A Microsoft Docs
description: Referencia a Batch-készlet törlése kész esemény.
services: batch
author: dlepow
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
origin.date: 04/20/2017
ms.date: 05/14/2018
ms.author: v-junlch
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775763"
---
# <a name="pool-delete-complete-event"></a>Készlet törlése kész esemény

 Ez az esemény bocsásson ki egy alkalmazáskészlet-törlési művelet befejezése után.

 Az alábbi példa bemutatja egy készlet törlése kész esemény törzsét.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|id|String|A készlet azonosítója.|
|startTime|DateTime|Az idő a készlet törlése megkezdődött.|
|endTime|DateTime|Az idő a készlet törlése befejeződött.|

## <a name="remarks"></a>Megjegyzések
További információ az állapotok és a készlet átméretezés hibakódok: [címkészlet törlése egy fiókból](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).

<!-- Update_Description: update metedata properties -->