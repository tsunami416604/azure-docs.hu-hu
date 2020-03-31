---
title: Az Azure Batch készlet törlése teljes esemény
description: Hivatkozás a kötegkészlet teljes eseményének törléséhez. Ez az esemény akkor kerül kibocsátásra, amikor egy készlettörlési művelet befejeződött.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: d317d7395a8246c109073a72338b55960cb50954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023616"
---
# <a name="pool-delete-complete-event"></a>Készlet törlése kész esemény

 Ez az esemény akkor kerül kibocsátásra, amikor egy készlettörlési művelet befejeződött.

 A következő példa egy készlet teljes eseményének törzsét mutatja be.

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
|`startTime`|DateTime|A készlet törlésének indítása.|
|`endTime`|DateTime|A készlet törlésének befejeződése.|

## <a name="remarks"></a>Megjegyzések
A készletátméretezési művelet állapotairól és hibakódjairól a [Készlet törlése fiókból című](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)témakörben talál további információt.
