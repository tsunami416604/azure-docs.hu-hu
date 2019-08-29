---
title: Azure Batch készlet delete eseményének törlése | Microsoft Docs
description: A Batch-készlet törlésének kezdési eseményének hivatkozása.
services: batch
author: laurenhughes
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: lahugh
ms.openlocfilehash: 6f8b1bdd680d43cb14707338d2e37e41114126b0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70094744"
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