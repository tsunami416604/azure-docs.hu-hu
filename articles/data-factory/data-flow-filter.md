---
title: Transzformáció szűrése az adatfolyam leképezésében
description: Sorok kiszűrése az Azure Data Factory adatfolyamatának szűrőtranszformációjával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 3460c789909c6acd0537ef89a9ec8509bf6e135d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413727"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>Transzformáció szűrése az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Szűrő átalakítások lehetővé teszik a sorszűrést egy feltétel alapján. A kimeneti adatfolyam tartalmazza a szűrési feltételnek megfelelő összes sort. A szűrőátalakítás hasonló az SQL WHERE záradékához.

## <a name="configuration"></a>Konfiguráció

Az adatfolyam-kifejezésszerkesztővel adjon meg egy kifejezést a szűrőfeltételhez. A kifejezéskészítő megnyitásához kattintson a kék mezőre. A szűrőfeltételnek logikai típusúnak kell lennie. A kifejezések létrehozásáról a [kifejezésszerkesztő](concepts-data-flow-expression-builder.md) dokumentációjában olvashat bővebben.

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

Az alábbi példa egy `FilterBefore1960` szűrőátalakítás nevű, `CleanData`amely a bejövő stream . A szűrőfeltétel a `year <= 1960`kifejezés.

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Szűrő átalakítása](media/data-flow/filter1.png "Szűrő átalakítása")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>További lépések

Oszlopok kiszűrése a [kijelölési átalakítással](data-flow-select.md)
