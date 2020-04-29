---
title: Feltételes felosztású átalakítás a leképezési adatfolyamban
description: Adatfelosztás különböző streamekre a feltételes felosztás átalakításával Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: bd9241e526d7cf42f0697afb8635c085a08c80d8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606479"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Feltételes felosztású átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A feltételes felosztott átalakítás az adatsorokat különböző streamekre irányítja a megfelelő feltételek alapján. A feltételes felosztott átalakítás a programozási nyelv eset döntési struktúrájához hasonlít. Az átalakítás kiértékeli a kifejezéseket, és az eredmények alapján a megadott adatfolyamra irányítja az adatsort.

## <a name="configuration"></a>Configuration

A **felosztás bekapcsolva** beállítás határozza meg, hogy az adatsorok az első egyező adatfolyamra vagy minden adatfolyamra vonatkoznak-e.

Az adatfolyam-Kifejezésszerkesztő használatával adjon meg egy kifejezést a felosztott feltételhez. Új feltétel hozzáadásához kattintson a plusz ikonra egy meglévő sorban. Egy alapértelmezett adatfolyamot is hozzáadhat a feltételnek nem megfelelő sorokhoz.

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    split(
        <conditionalExpression1>
        <conditionalExpression2>
        ...
        disjoint: {true | false}
    ) ~> <splitTx>@(stream1, stream2, ..., <defaultStream>)
```

### <a name="example"></a>Példa

Az alábbi példa egy nevű `SplitByYear` feltételes felosztású átalakítás, amely a bejövő adatfolyamot `CleanData`veszi át. Ez a transzformáció két felosztott `year < 1960` feltételt és `year > 1980`. `disjoint`hamis, mert az adatértékek az első egyező feltételnek felelnek meg. Minden, az első feltételnek megfelelő sor a `moviesBefore1960`kimeneti adatfolyamba kerül. A második feltételnek megfelelő összes fennmaradó sor a kimeneti `moviesAFter1980`adatfolyamra mutat. Az összes többi sor az alapértelmezett adatfolyamon `AllOtherMovies`keresztül folyik.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>További lépések

A feltételes felosztással használt közös adatfolyam-átalakítások az [összekapcsolási transzformáció](data-flow-join.md), a [keresési átalakítás](data-flow-lookup.md)és az [átalakítás kiválasztása](data-flow-select.md)
