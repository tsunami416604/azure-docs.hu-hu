---
title: "Azure Batch készlet törlése start esemény |} Microsoft Docs"
description: "Útmutató a Batch-készlet törlése esemény indítása."
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
ms.openlocfilehash: f8a5241dce422e5c826ab428da6d7bc93284a1cf
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="pool-delete-start-event"></a>Készlet törlése start esemény

 Ez az esemény kibocsátott, amikor egy alkalmazáskészlet törlési művelete megkezdődött. Mivel a készlet törlése egy aszinkron esemény, egy készlet törlése teljes esemény a törlési művelet befejeződése után kell kibocsátott számíthat.

 A következő példa bemutatja a készlet törlése start esemény törzsét.

```
{
    "id": "myPool1"
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|id|Karakterlánc|A készlet azonosítóját.|