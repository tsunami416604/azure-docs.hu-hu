---
title: Az Azure Batch-készlet törlése kész esemény |} A Microsoft Docs
description: Referencia a Batch-készlet törlése kész esemény.
services: batch
author: laurenhughes
manager: jeconnoc
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: e715ccd0f5e79f9c640a3c060b0252b798748b4d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474548"
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

|Elem|Typo|Megjegyzések|
|-------------|----------|-----------|
|id|String|A készlet azonosítója.|
|startTime|DateTime|Az idő a készlet törlése megkezdődött.|
|endTime|DateTime|Az idő a készlet törlése befejeződött.|

## <a name="remarks"></a>Megjegyzések
További információ az állapotok és a készlet átméretezés hibakódok: [címkészlet törlése egy fiókból](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account).