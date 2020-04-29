---
title: Átalakítás szűrése a leképezési adatfolyamban
description: Sorok kiszűrése a szűrő átalakításával Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 32a40f3d4da93318c6d2ec25295c89a7b53141e4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606429"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Átalakítás szűrése a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A szűrő átalakítások lehetővé teszik a sorok szűrését egy adott feltétel alapján. A kimeneti adatfolyam tartalmazza a szűrési feltételnek megfelelő összes sort. A szűrő átalakítása hasonló a WHERE záradékhoz az SQL-ben.

## <a name="configuration"></a>Configuration

A szűrési feltétel kifejezésének megadásához használja az adatfolyam-Kifejezésszerkesztő kifejezést. A Kifejezésszerkesztő megnyitásához kattintson a kék mezőre. A szűrési feltételnek logikai típusúnak kell lennie. A kifejezések létrehozásával kapcsolatos további tudnivalókért tekintse meg a Kifejezésszerkesztő [dokumentációját](concepts-data-flow-expression-builder.md) .

![Szűrő átalakítása](media/data-flow/filter1.png "Szűrő átalakítása")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű `FilterBefore1960` szűrő-átalakítás, amely a bejövő adatfolyamot `CleanData`veszi igénybe. A szűrési feltétel a kifejezés `year <= 1960`.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Szűrő átalakítása](media/data-flow/filter1.png "Szűrő átalakítása")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>További lépések

Oszlopok kiszűrése az [átalakítás kiválasztása](data-flow-select.md)
