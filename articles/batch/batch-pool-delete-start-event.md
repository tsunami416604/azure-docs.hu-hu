---
title: Azure Batch készlet törlése – indítási esemény
description: A Batch-készlet törlésének kezdési eseményének hivatkozása. Ezt az eseményt a rendszer a készlet törlési műveletének megkezdése után bocsátja ki.
ms.topic: reference
ms.date: 04/20/2017
ms.openlocfilehash: d4fac833b6c77258e48b38838f6b7b133738a6e8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83723935"
---
# <a name="pool-delete-start-event"></a>Készlet törlésének indítása esemény

 Ezt az eseményt a rendszer a készlet törlési műveletének megkezdése után bocsátja ki. Mivel a készlet törlése egy aszinkron esemény, várhatóan a készlet törlésének befejezési eseményét kell kiállítani a törlési művelet befejeződése után.

 Az alábbi példa egy készlet-törlési esemény törzsét mutatja be.

```
{
    "id": "myPool1"
}
```

|Elem|Típus|Jegyzetek|
|-------------|----------|-----------|
|`id`|Sztring|A készlet azonosítója.|
