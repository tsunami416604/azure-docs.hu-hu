---
title: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
description: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: d1751c47ad4507260d9f8d6ea44fcb32ed0e7338
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57864728"
---
# <a name="azure-data-factoryfilter-transformation"></a>Az Azure Data FactoryFilter átalakítása

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

A szűrő átalakítások biztosít sorok szűrése. Hozhat létre a megadott szűrési feltételnek meghatározó kifejezés. Kattintson a mezőbe, indítsa el a Kifejezésszerkesztő. A Kifejezésszerkesztő belül hozhat létre egy kifejezést szabályozza, mely aktuális adatfolyam sorait (szűrő) áthaladását, a következő átalakító. Úgy gondolja, hogy a szűrő átalakítását, a WHERE záradékban az SQL-utasítással.

## <a name="filter-on-loanstatus-column"></a>A következő loan_status oszlop szűrése:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Az év oszlop filmek bemutató szűrése:

```
year > 1980
```

## <a name="next-steps"></a>További lépések

Próbálja ki egy oszlopot, átalakítási, szűrés a [átalakítási kiválasztása](data-flow-select.md)
