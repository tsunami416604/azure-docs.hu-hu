---
title: Azure Batch készlet delete eseményének törlése | Microsoft Docs
description: A Batch-készlet törlésének kezdési eseményének hivatkozása.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: d582e2b04d203484632a1781d1819f612de41fe7
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026730"
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

 Ezt az eseményt a rendszer a készlet törlési műveletének megkezdése után bocsátja ki. Mivel a készlet törlése egy aszinkron esemény, várhatóan a készlet törlésének befejezési eseményét kell kiállítani a törlési művelet befejeződése után.

 Az alábbi példa egy készlet-törlési esemény törzsét mutatja be.

```
{
    "id": "myPool1"
}
```

|Elem|Type (Típus)|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
