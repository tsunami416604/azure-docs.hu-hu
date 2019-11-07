---
title: Átalakítás szűrése Azure Data Factory leképezési adatfolyamban
description: Sorok kiszűrése a szűrő átalakításával Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 77587d007d9bfcdc1461201f676c79880bd89d6b
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676176"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Átalakítás szűrése a leképezési adatfolyamban

A szűrő átalakítások lehetővé teszik a sorok szűrését egy adott feltétel alapján. A kimeneti adatfolyam tartalmazza a szűrési feltételnek megfelelő összes sort. A szűrő átalakítása hasonló a WHERE záradékhoz az SQL-ben.

## <a name="configuration"></a>Konfiguráció

A szűrési feltétel kifejezésének megadásához használja az adatfolyam-Kifejezésszerkesztő kifejezést. A Kifejezésszerkesztő megnyitásához kattintson a kék mezőre. A szűrési feltételnek logikai típusúnak kell lennie. A kifejezések létrehozásával kapcsolatos további tudnivalókért tekintse meg a Kifejezésszerkesztő [dokumentációját](concepts-data-flow-expression-builder.md) .

![Szűrő átalakítása](media/data-flow/filter1.png "Szűrő átalakítása")

## <a name="data-flow-script"></a>Adatfolyam-parancsfájl

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy `FilterBefore1960` nevű feltételes felosztású átalakítás, amely bekerül a bejövő stream `CleanData`ba. A szűrési feltétel a `year <= 1960`kifejezés.

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
