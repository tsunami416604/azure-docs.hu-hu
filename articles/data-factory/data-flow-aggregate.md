---
title: Összesített átalakítás a leképezési adatfolyamban
description: Megtudhatja, hogyan összesítheti a Azure Data Factory méretezési adatokat a leképezési adatfolyam összesített átalakításával.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 871f2b49e2dce9d762ef8a54923da04b0f24e4be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606535"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Összesített átalakítás a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Az összesített átalakítás az adatstreamek oszlopainak összesítését határozza meg. A Kifejezésszerkesztő használatával különböző típusú összesítéseket adhat meg, például a SUM, a MIN, a MAX és a COUNT függvényt meglévő vagy számított oszlopok szerint csoportosítva.

## <a name="group-by"></a>Csoportosítási szempont

Válasszon ki egy meglévő oszlopot, vagy hozzon létre egy új számított oszlopot, amelyet Group By záradékként kíván használni az összesítéshez. Meglévő oszlop használatához válassza ki azt a legördülő listából. Új számított oszlop létrehozásához vigye a kurzort a záradék fölé, és kattintson a **számított oszlop**elemre. Ekkor megnyílik az [adatfolyam-kifejezés szerkesztője](concepts-data-flow-expression-builder.md). Miután létrehozta a számított oszlopot, adja meg a kimeneti oszlop nevét a **név** mezőben. Ha további Group By záradékot szeretne hozzáadni, vigye a kurzort egy meglévő záradék fölé, és kattintson a plusz ikonra.

![Összesítő transzformációs csoport beállítások szerint](media/data-flow/agg.png "Összesítő transzformációs csoport beállítások szerint")

Group By záradék használata nem kötelező egy összesített átalakításban.

## <a name="aggregate-column"></a>Összesítő oszlop 

Az összesítési kifejezések létrehozásához nyissa meg az **összesítések** lapot. Egy meglévő oszlopot felülírhat összesítéssel, vagy létrehozhat egy új mezőt új névvel. Az összesítési kifejezés az oszlopnév-választó melletti jobb oldali mezőben van megadva. A kifejezés szerkesztéséhez kattintson a szövegmezőre, hogy megnyissa a Kifejezésszerkesztő párbeszédpanelt. További összesítések hozzáadásához vigye a kurzort egy meglévő kifejezés fölé, és kattintson a plusz ikonra egy új aggregációs oszlop vagy [oszlop minta](concepts-data-flow-column-pattern.md)létrehozásához.

Minden összesítési kifejezésnek legalább egy összesítő függvényt tartalmaznia kell.

![Összesített átalakítás összesített beállításai](media/data-flow/agg2.png "Összesített átalakítás összesített beállításai")


> [!NOTE]
> Hibakeresési módban a Kifejezésszerkesztő nem hozhat létre összesítő függvényekből származó adat-előnézeteket. Az összesített átalakításokhoz tartozó adatelőnézetek megtekintéséhez a Kifejezésszerkesztő bezárásához és az adatelőnézet lapon megtekintheti az adatokat.

## <a name="reconnect-rows-and-columns"></a>Sorok és oszlopok újracsatolása

Az összesített átalakítások hasonlóak az SQL aggregált választó lekérdezésekhez. A GROUP BY záradékban vagy az összesítő függvények között nem szereplő oszlopok nem áramlanak át az összesített átalakítás kimenetére. Ha más oszlopokat is szeretne szerepeltetni az összesített kimenetben, hajtsa végre az alábbi módszerek egyikét:

* Használjon egy összesítő függvényt, `last()` például `first()` a vagy a lehetőséget a további oszlop felvételéhez.
* Csatlakoztassa újra az oszlopokat a kimeneti adatfolyamhoz a [saját illesztési minta](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)használatával.

## <a name="removing-duplicate-rows"></a>Ismétlődő sorok eltávolítása

Az összesített átalakítás általános használata eltávolítja vagy azonosítja a forrásadatok ismétlődő bejegyzéseit. Ez a folyamat deduplikált néven ismert. A kulcsok csoportosítása alapján, a kiválasztott ismétlődő sorok meghatározásához használja a választott heurisztikus lehetőséget. `first()`Gyakori heurisztikus `last()` `max()`:,, és `min()`. Használjon [oszlopos mintákat](concepts-data-flow-column-pattern.md) a szabály minden oszlopra való alkalmazásához, kivéve a csoportosítási oszlopokat.

![Deduplikáció](media/data-flow/agg-dedupe.png "Deduplikáció")

A fenti példában az oszlopok `ProductID` és `Name` a csoportosításhoz használják őket. Ha két sor ugyanazokkal az értékekkel rendelkezik a két oszlophoz, azok ismétlődésnek tekintendők. Ebben az összesített átalakításban a rendszer megőrzi az első sor egyezésének értékeit, és az összes többi el fog dobni. Az oszlop mintájának szintaxisa alapján az összes olyan `ProductID` oszlop `Name` , amelynek a neve nem, és le van képezve a meglévő oszlop nevére, és az első megfeleltetett sorok értéke. A kimeneti séma megegyezik a bemeneti sémával.

Az adatérvényesítési forgatókönyvek `count()` esetében a függvény használatával megszámolható, hogy hány ismétlődés van.

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

Az alábbi példában a bejövő adatfolyam `MoviesYear` és a csoportok oszlop `year`szerint vannak sorba rendezve. Az átalakítás egy összesített oszlopot `avgrating` hoz létre, amely kiértékeli az oszlop `Rating`átlagát. Ez az összesített átalakítás neve `AvgComedyRatingsByYear`.

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Csoportosítás példa alapján](media/data-flow/agg-script1.png "Csoportosítás példa alapján")

![Példa összesítésre](media/data-flow/agg-script2.png "Példa összesítésre")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

![Összesített adatfolyam-parancsfájl](media/data-flow/aggdfs1.png "Összesített adatfolyam-parancsfájl")

```MoviesYear```: Származtatott oszlop, amely az év és ```AvgComedyRatingByYear```a cím oszlopokat definiálja: a vígjátékok átlagos minősítésének ```avgrating```összesített átalakítása év szerint csoportosítva: az összesített érték tárolásához létrehozott új oszlop neve.

```
MoviesYear aggregate(groupBy(year),
    avgrating = avg(toInteger(Rating))) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>További lépések

* Ablak alapú összesítés definiálása az ablak- [transzformáció](data-flow-window.md) használatával
