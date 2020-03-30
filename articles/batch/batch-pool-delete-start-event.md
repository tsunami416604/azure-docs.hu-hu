---
title: Az Azure Batch-készlet törlése indítási esemény
description: Hivatkozás a kötegkészlet törlési kezdőeseményére. Ez az esemény akkor kerül kibocsátásra, amikor egy készlettörlési művelet elindult.
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
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022205"
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

 Ez az esemény akkor kerül kibocsátásra, amikor egy készlettörlési művelet elindult. Mivel a készlet törlése egy aszinkron esemény, a törlési művelet befejezése után a készlet törlésének teljes eseménye várható.

 A következő példa egy készlet törlési kezdőeseményének törzsét mutatja be.

```
{
    "id": "myPool1"
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
