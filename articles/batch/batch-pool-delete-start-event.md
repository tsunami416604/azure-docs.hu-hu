---
title: Az Azure Batch készlet törlésének indítása esemény |} A Microsoft Docs
description: Referencia a Batch készlet törlésének indítása esemény.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774539"
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

 Ez az esemény bocsásson ki, amikor egy alkalmazáskészlet-törlési művelet megkezdődött. Mivel a készlet törlése aszinkron esemény, várható a készlet törlése kész esemény a delete művelet befejezése után bocsátjuk rendelkezésre.

 Az alábbi példa bemutatja egy készlet törlésének indítása esemény törzsét.

```
{
    "id": "myPool1"
}
```

|Elem|Típus|Megjegyzések|
|-------------|----------|-----------|
|id|String|A készlet azonosítója.|

<!-- Update_Description: update metedata properties -->