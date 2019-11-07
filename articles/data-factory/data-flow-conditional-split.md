---
title: Feltételes felosztás átalakítása Azure Data Factory leképezési adatfolyamatban
description: Adatfelosztás különböző streamekre a feltételes felosztás átalakításával Azure Data Factory leképezési adatfolyamban
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/16/2019
ms.openlocfilehash: 9ace415aa725a82d8feda5702d25d7e5ff9875d9
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676814"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Feltételes felosztású átalakítás a leképezési adatfolyamban

A feltételes felosztott átalakítás az adatsorokat különböző streamekre irányítja a megfelelő feltételek alapján. A feltételes felosztott átalakítás a programozási nyelv eset döntési struktúrájához hasonlít. Az átalakítás kiértékeli a kifejezéseket, és az eredmények alapján a megadott adatfolyamra irányítja az adatsort.

## <a name="configuration"></a>Konfiguráció

A **felosztás bekapcsolva** beállítás határozza meg, hogy az adatsorok az első egyező adatfolyamra vagy minden adatfolyamra vonatkoznak-e.

Az adatfolyam-Kifejezésszerkesztő használatával adjon meg egy kifejezést a felosztott feltételhez. Új feltétel hozzáadásához kattintson a plusz ikonra egy meglévő sorban. Egy alapértelmezett adatfolyamot is hozzáadhat a feltételnek nem megfelelő sorokhoz.

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

## <a name="data-flow-script"></a>Adatfolyam-parancsfájl

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

Az alábbi példa egy `SplitByYear` nevű feltételes felosztású átalakítás, amely bekerül a bejövő stream `CleanData`ba. Az átalakításnak két felosztott feltétele van `year < 1960` és `year > 1980`. a `disjoint` hamis, mert az adatértékek az első egyező feltételnek felelnek meg. Minden, az első feltételnek megfelelő sor az adatfolyam-`moviesBefore1960`kimenetére mutat. A második feltételnek megfelelő összes fennmaradó sor a kimeneti adatfolyam `moviesAFter1980`. Az összes többi sor az alapértelmezett stream-`AllOtherMovies`on keresztül folyik.

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
