---
title: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
description: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: e0b41850c149ff7095333cf77b780dec1f03b882
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234415"
---
# <a name="azure-data-factory-filter-transformation"></a>Az Azure data factory szűrő átalakítása

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
