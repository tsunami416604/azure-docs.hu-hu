---
title: Származtatott oszlop átalakítása a leképezési adatfolyamban
description: Megtudhatja, hogyan alakíthatja át az adatmennyiséget a Azure Data Factory a leképezési adatfolyam származtatott oszlopainak átalakításával.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: ce774868acc281f769e7f2991472e7d2a5da9739
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930396"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Származtatott oszlop átalakítása a leképezési adatfolyamban

A származtatott oszlop átalakításával új oszlopokat hozhatja ki az adatfolyamban, vagy módosíthatja a meglévő mezőket.

## <a name="derived-column-settings"></a>Származtatott oszlop beállításai

Meglévő oszlop felülbírálásához válassza ki azt az oszlop legördülő listából. Ellenkező esetben használja az oszlop kijelölése mezőt a szövegmezőbe, és írja be az új oszlop nevét. A származtatott oszlop kifejezésének létrehozásához kattintson a "kifejezés beírása" mezőre az adatfolyam- [kifejezés-szerkesztő](concepts-data-flow-expression-builder.md)megnyitásához.

![Származtatott oszlop beállításai](media/data-flow/dc1.png "Származtatott oszlop beállításai")

További származtatott oszlopok hozzáadásához vigye a kurzort egy meglévő származtatott oszlop fölé, és kattintson a plusz ikonra. Válassza az **oszlop hozzáadása** vagy az **oszlop hozzáadása mintát**. Az oszlop mintái hasznosak lehetnek, ha az oszlopnevek a forrásokból származó változók. További információ: [Column Patterns](concepts-data-flow-column-pattern.md).

![Új származtatott oszlop kiválasztása](media/data-flow/columnpattern.png "Új származtatott oszlop kiválasztása")

## <a name="data-flow-script"></a>Adatfolyamszkript

### <a name="syntax"></a>Szintaxis

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>Példa

Az alábbi példa egy `CleanData` nevű származtatott oszlop, amely egy bejövő adatfolyamot `MoviesYear`, és két származtatott oszlopot hoz létre. Az első származtatott oszlop az oszlop `Rating` a minősítés értékének egész típusúra vált. A második származtatott oszlop egy minta, amely minden olyan oszlopnak megfelel, amelynek a neve "Movies" karakterrel kezdődik. Minden egyező oszlophoz létrehoz egy olyan oszlopot `movie`, amely megegyezik az "movie_" előtaggal ellátott egyező oszlop értékével. 

Az Data Factory UX-ben ez az átalakítás az alábbi képhez hasonlóan néz ki:

![Példa származtatása](media/data-flow/derive-script1.png "Példa származtatása")

Az átalakításhoz tartozó adatfolyam-szkript az alábbi kódrészletben található:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>Következő lépések

- További információ a [leképezési adatfolyam kifejezésének nyelvéről](data-flow-expression-functions.md).
