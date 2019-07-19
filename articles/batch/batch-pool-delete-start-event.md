---
title: Azure Batch készlet delete eseményének törlése | Microsoft Docs
description: A Batch-készlet törlésének kezdési eseményének hivatkozása.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 65d20f2194b2bf83ecf32e19c1ab5b0f7bc7a004
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323218"
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

 Ezt az eseményt a rendszer a készlet törlési műveletének megkezdése után bocsátja ki. Mivel a készlet törlése egy aszinkron esemény, várhatóan a készlet törlésének befejezési eseményét kell kiállítani a törlési művelet befejeződése után.

 Az alábbi példa egy készlet-törlési esemény törzsét mutatja be.

```
{
    "id": "myPool1"
}
```

|Elem|Type|Megjegyzések|
|-------------|----------|-----------|
|id|Sztring|A készlet azonosítója.|