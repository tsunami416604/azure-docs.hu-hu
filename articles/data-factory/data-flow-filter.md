---
title: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
description: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 1930ca7406b6ef1b9fd20e1651bc031150dd952e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729543"
---
# <a name="azure-data-factory-mapping-data-flow-filter-transformation"></a>Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A szűrő átalakítások biztosít sorok szűrése. Hozhat létre a megadott szűrési feltételnek meghatározó kifejezés. Kattintson a mezőbe, indítsa el a Kifejezésszerkesztő. A Kifejezésszerkesztő belül hozhat létre egy kifejezést szabályozza, mely aktuális adatfolyam sorait (szűrő) áthaladását, a következő átalakító.

Azaz loan_status oszlop szűrése:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Az év oszlop filmek bemutató szűrése:

```
year > 1980
```
