---
title: Azure Data Factory leképezési adatfolyam-szűrő átalakítása
description: Azure Data Factory leképezési adatfolyam-szűrő átalakítása
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029364"
---
# <a name="azure-data-factory-filter-transformation"></a>Az Azure-beli adatfeldolgozó-szűrő átalakítása



A szűrő átalakítja a sorok szűrését. Hozzon létre egy kifejezést, amely meghatározza a szűrési feltételt. Kattintson a szövegmezőbe a Kifejezésszerkesztő elindításához. A kifejezés-szerkesztőben hozzon létre egy szűrési kifejezést, amely azt határozza meg, hogy az aktuális adatfolyamból milyen sorok vihetők át (szűrő) a következő átalakításra. Gondoljon úgy, hogy a szűrő átalakítását egy SQL-utasítás WHERE záradékának tekinti.

## <a name="filter-on-loan_status-column"></a>Szűrés a loan_status oszlopon:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Szűrés az év oszlopban a filmek bemutatójában:

```
year > 1980
```

## <a name="next-steps"></a>További lépések

Próbálkozzon egy oszlop szűrésének átalakításával, az [átalakítás kiválasztása](data-flow-select.md)
