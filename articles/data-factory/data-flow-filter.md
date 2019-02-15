---
title: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
description: Az Azure Data Factory-folyamat szűrő adatátalakítás leképezése
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: b7e7b123560aae3a2d3086c8536969297d31f7ba
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271802"
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
