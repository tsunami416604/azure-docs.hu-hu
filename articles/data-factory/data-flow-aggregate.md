---
title: Összesített transzformáció az adatfolyam leképezésében
description: Megtudhatja, hogyan összesíthető konként az Azure Data Factory a leképezési adatfolyam összesített átalakítása.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 1830a16108e6d8bb251d7ca45ae471e2f606874b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240585"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Összesített transzformáció az adatfolyam leképezésében 

Az Összesítő transzformáció az adatfolyamok oszlopainak összesítését határozza meg. A Kifejezésszerkesztő használatával különböző típusú összesítéseket definiálhat, például SZUM, MIN, MAX és COUNT meglévő vagy számított oszlopok szerint csoportosítva.

## <a name="group-by"></a>Csoportosítási szempont

Jelöljön ki egy meglévő oszlopot, vagy hozzon létre egy új számított oszlopot, amelyet az összesítéshez záradékként használhat. Meglévő oszlop használatához jelölje ki azt a legördülő menüből. Új számított oszlop létrehozásához mutasson a záradékra, és kattintson a **Számított oszlop parancsra.** Ezzel megnyitja az [adatfolyam-kifejezésszerkesztőt.](concepts-data-flow-expression-builder.md) Miután létrehozta a számított oszlopot, írja be a kimeneti oszlop nevét a **Név mezőben.** Ha további csoportról záradékra szeretne kattintani, vigye az egérmutatót egy meglévő záradék fölé, és kattintson a plusz ikonra.

![Transzformációs csoport összesítése beállítások szerint](media/data-flow/agg.png "Transzformációs csoport összesítése beállítások szerint")

A szakaszonkénti csoportosítás nem kötelező az összesítő átalakításban.

## <a name="aggregate-column"></a>Összesítő oszlop 

Az **Összesítések** lapon összesítési kifejezéseket hozhat létre. Felülírhat egy meglévő oszlopot összesítéssel, vagy létrehozhat egy új mezőt új névvel. Az összesítési kifejezés az oszlopnév-választó melletti jobb oldali mezőbe kerül. A kifejezés szerkesztéséhez kattintson a szövegdobozra a kifejezésszerkesztő megnyitásához. További összesítések hozzáadásához mutasson egy meglévő kifejezésre, és kattintson a Plusz ikonra egy új aggregációs oszlop vagy [oszlopminta](concepts-data-flow-column-pattern.md)létrehozásához.

Minden összesítési kifejezésnek legalább egy összesítő függvényt tartalmaznia kell.

![Összesítő átalakítási összesítő beállítások](media/data-flow/agg2.png "Összesítő átalakítási összesítő beállítások")


> [!NOTE]
> Debug módban a kifejezésszerkesztő nem tud összesített függvényekkel adatelőnézeteket létrehozni. Az összesített átalakítások adatelőnézetének megtekintéséhez zárja be a kifejezésszerkesztőt, és tekintse meg az adatokat az "Adatelőnézet" lapon.

## <a name="reconnect-rows-and-columns"></a>Sorok és oszlopok újracsatlakoztatása

Az összesítő átalakítások hasonlóak az SQL összesítő választó lekérdezésekhez. Azok az oszlopok, amelyek nem szerepelnek a csoportban záradék vagy összesítő függvények szerint, nem jutnak át az összesített átalakítás kimenetén. Ha további oszlopokat szeretne felvenni az összesített kimenetbe, tegye az alábbi módszerek egyikét:

* Használjon egy összesítő függvényt, például `last()` a további oszlopot, vagy `first()` szerepeljenek benne.
* Csatlakozzon újra az oszlopokhoz a kimeneti adatfolyamhoz az [önillesztési mintával.](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)

## <a name="removing-duplicate-rows"></a>Ismétlődő sorok eltávolítása

Az összesítő transzformáció általános használata a forrásadatok ismétlődő bejegyzéseinek eltávolítása vagy azonosítása. Ezt a folyamatot deduplikációnak nevezzük. A kulcsok szerinti csoportcsoport alapján egy ön által választott heurisztikával határozhatja meg, hogy melyik ismétlődő sort tartsa meg. A gyakori heurisztikák a `first()`következők: `last()`, `max()`, és `min()`. [Oszlopminták](concepts-data-flow-column-pattern.md) használatával a szabályt minden oszlopra alkalmazhatja, kivéve az oszlopok szerint csoportosítandó csoportot.

![Deduplikáció](media/data-flow/agg-dedupe.png "Deduplikáció")

A fenti példában `ProductID` `Name` oszlopok, és a csoportosításhoz használatos. Ha két sor azonos értékekkel rendelkezik a két oszlophoz, akkor azok ismétlődésnek minősülnek. Ebben az összesített átalakításban az első egyező sor értékei megmaradnak, és az összes többi el lesz dobva. Az oszlopminta szintaxisának használatával minden `ProductID` `Name` olyan oszlop, amelynek neve nem, és a meglévő oszlopnévhez van rendelve, és az első egyező sorok értékét adja meg. A kimeneti séma megegyezik a bemeneti sémával.

Adatérvényesítési forgatókönyvek `count()` esetén a függvény segítségével megszámolhatja, hogy hány ismétlődés van.

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy `MoviesYear` bejövő adatfolyamot `year`vesz fel, és oszloponként csoportosítja a sorokat. Az átalakítás létrehoz `avgrating` egy összesítő oszlopot, `Rating`amely az oszlop átlagára értékelésre kerül. Ennek az összesítő átalakításnak a neve `AvgComedyRatingsByYear`.

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Csoportosítás példa szerint](media/data-flow/agg-script1.png "Csoportosítás példa szerint")

![Összesített példa](media/data-flow/agg-script2.png "Összesített példa")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>További lépések

* Ablakalapú összesítés definiálása az [Ablak átalakítással](data-flow-window.md)
