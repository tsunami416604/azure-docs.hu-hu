---
title: Azure Batch készlet törlése start esemény |} Microsoft Docs
description: Útmutató a Batch-készlet törlése esemény indítása.
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
ms.openlocfilehash: 8737b9ff6452730ff5a55fa7324e37f0fe715433
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

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