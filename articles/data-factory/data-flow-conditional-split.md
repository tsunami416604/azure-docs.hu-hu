---
title: Feltételes felosztásátalakítása az adatfolyam leképezésében
description: Adatok felosztása különböző adatfolyamokra az Azure Data Factory leképezési adatfolyamának feltételes felosztásos transzformációjával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 20def8ca51f21d914e7090999e8284244c5f3ec7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416497"
---
# <a name="conditional-split-transformation-in-mapping-data-flow"></a>Feltételes felosztásátalakítása az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A feltételes felosztástranszformáció az adatsorokat különböző adatfolyamokra irányítja az egyeztetési feltételek alapján. A feltételes felosztás átalakítása hasonló a PROGRAMnyelv CASE döntési struktúrájához. Az átalakítás kiértékeli a kifejezéseket, és az eredmények alapján az adatsort a megadott adatfolyamra irányítja.

## <a name="configuration"></a>Konfiguráció

A **Felosztás beállítás** határozza meg, hogy az adatfolyam-folyók sora az első egyező adatfolyamhoz vagy minden streamhez, amelynek megfelel.

Az adatfolyam-kifejezés szerkesztőjével adjon meg egy kifejezést a felosztott feltételhez. Új feltétel hozzáadásához kattintson a plusz ikonra egy meglévő sorban. Alapértelmezett adatfolyam is hozzáadható olyan sorokhoz, amelyek nem felelnek meg semmilyen feltételnek.

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

Az alábbi példa egy feltételes `SplitByYear` felosztásos transzformáció, amely a bejövő adatfolyamot `CleanData`veszi fel. Ennek az átalakulásnak `year < 1960` `year > 1980`két osztott feltétele van, és . `disjoint`hamis, mert az adatok az első egyezési feltételre lépnek. Az első feltételnek megfelelő minden `moviesBefore1960`sor kimeneti adatfolyamba kerül. A második feltételnek megfelelő összes `moviesAFter1980`többi sor a kimeneti adatfolyamba kerül. Az összes többi sor `AllOtherMovies`átfolyik az alapértelmezett adatfolyamon.

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![feltételes felosztás](media/data-flow/conditionalsplit1.png "feltételes felosztási beállítások")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

```
CleanData
    split(
        year < 1960,
        year > 1980,
        disjoint: false
    ) ~> SplitByYear@(moviesBefore1960, moviesAfter1980, AllOtherMovies)
```

## <a name="next-steps"></a>További lépések

A feltételes felosztással használt közös adatfolyam-átalakítások a [illesztési transzformáció](data-flow-join.md), [a keresgafon transzformáció](data-flow-lookup.md)és a [select transzformáció](data-flow-select.md)
