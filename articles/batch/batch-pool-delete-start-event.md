---
title: Az Azure Batch készlet törlésének indítása esemény |} A Microsoft Docs
description: Referencia a Batch készlet törlésének indítása esemény.
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
ms.openlocfilehash: 2352971af3844b56f93c16ebaf6cb23bd5fd8a5a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55474292"
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

 Ez az esemény bocsásson ki, amikor egy alkalmazáskészlet-törlési művelet megkezdődött. Mivel a készlet törlése aszinkron esemény, várható a készlet törlése kész esemény a delete művelet befejezése után bocsátjuk rendelkezésre.

 Az alábbi példa bemutatja egy készlet törlésének indítása esemény törzsét.

```
{
    "id": "myPool1"
}
```

|Elem|Typo|Megjegyzések|
|-------------|----------|-----------|
|id|String|A készlet azonosítója.|