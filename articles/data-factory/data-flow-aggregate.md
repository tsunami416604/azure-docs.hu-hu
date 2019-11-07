---
title: Összesített átalakítás Azure Data Factory leképezési adatfolyamban
description: Megtudhatja, hogyan összesítheti a Azure Data Factory méretezési adatokat a leképezési adatfolyam összesített átalakításával.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 3f21367c36ae31aa2115c109933a581bef464baf
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676918"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Összesített átalakítás a leképezési adatfolyamban 

Az összesített átalakítás az adatstreamek oszlopainak összesítését határozza meg. A Kifejezésszerkesztő használatával különböző típusú összesítéseket adhat meg, például a SUM, a MIN, a MAX és a COUNT függvényt meglévő vagy számított oszlopok szerint csoportosítva.

## <a name="group-by"></a>Csoportosítási alapja

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

* Használjon összesítő függvényt, például `last()` vagy `first()` a további oszlopok belefoglalásához.
* Csatlakoztassa újra az oszlopokat a kimeneti adatfolyamhoz a [saját illesztési minta](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)használatával.

## <a name="data-flow-script"></a>Adatfolyam-parancsfájl

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

Az alábbi példa egy bejövő stream `MoviesYear` és a groups by Column `year`alapján csoportosítja a sorokat. Az átalakítás létrehoz egy összesített oszlopot `avgrating`, amely a `Rating`oszlop átlagát értékeli ki. Az összesített átalakítás neve `AvgComedyRatingsByYear`.

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

## <a name="next-steps"></a>További lépések

* Ablak alapú összesítés definiálása az ablak- [transzformáció](data-flow-window.md) használatával
