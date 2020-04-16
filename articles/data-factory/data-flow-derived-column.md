---
title: Származtatott oszlopátalakítása az adatfolyam leképezésében
description: Ismerje meg, hogyan alakíthatja át az adatokat nagy méretekben az Azure Data Factory-ban a leképezési adatfolyam származtatott oszlop átalakítása.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 06cb868071612ae2825e86cac32734dcd279c99f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413853"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Származtatott oszlopátalakítása az adatfolyam leképezésében

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A származtatott oszlopátalakítással új oszlopokat hozhat létre az adatfolyamban, vagy módosíthatja a meglévő mezőket.

## <a name="derived-column-settings"></a>Származtatott oszlopbeállításai

Meglévő oszlop felülbírálásához jelölje ki azt az oszlop legördülő menün keresztül. Ellenkező esetben használja az oszlopkijelölő mezőt szövegdobozként, és írja be az új oszlop nevét. A származtatott oszlop kifejezésének létrehozásához kattintson az "Enter expression" mezőre az [Adatfolyam-kifejezésszerkesztő](concepts-data-flow-expression-builder.md)megnyitásához.

![Származtatott oszlopbeállításai](media/data-flow/dc1.png "Származtatott oszlopbeállításai")

További származtatott oszlopok hozzáadásához mutasson egy meglévő származtatott oszlopra, és kattintson a plusz ikonra. Válassza az **Oszlop hozzáadása** vagy **az Oszlopminta hozzáadása lehetőséget.** Az oszlopminták akkor jöhetnek hasznosak, ha az oszlopnevek változóak a forrásokból. További információ: [Column Patterns](concepts-data-flow-column-pattern.md).

![Új származtatott oszlopkijelölés](media/data-flow/columnpattern.png "Új származtatott oszlopkijelölés")

## <a name="build-schemas-in-output-schema-pane"></a>Sémák létrehozása a kimeneti séma ablaktáblában

A sémához módosítandó és hozzáadandó oszlopok a Kimeneti séma ablaktáblában jelennek meg. Itt interaktívan egyszerű és összetett adatstruktúrákat hozhat létre. További mezők hozzáadásához válassza az **Oszlop hozzáadása**lehetőséget. Hierarchiák létrehozásához válassza **az Aloszlop hozzáadása**lehetőséget.

![Aloszlop hozzáadása](media/data-flow/addsubcolumn.png "Aloszlop hozzáadása")

Az adatfolyam összetett típusainak kezeléséről a [JSON-kezelés az adatfolyam leképezése című témakörben talál](format-json.md#mapping-data-flow-properties)további információt.

![Összetett oszlop hozzáadása](media/data-flow/complexcolumn.png "Oszlopok hozzáadása")

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

Az alábbi példa egy `CleanData` származtatott oszlop `MoviesYear` neve, amely egy bejövő adatfolyamot vesz fel, és két származtatott oszlopot hoz létre. Az első származtatott oszlop lecseréli az oszlopot `Rating` a Rating egész típusként i rating értékére. A második származtatott oszlop egy olyan minta, amely minden olyan oszlopnak megfelel, amelynek neve "filmek" kezdetével kezdődik. Minden egyező oszlophoz létrehoz `movie` egy olyan oszlopot, amely megegyezik az "movie_" előtaggal ellátott egyező oszlop értékével. 

A Data Factory UX-ben ez az átalakítás az alábbi képre hasonlít:

![Példa származtatása](media/data-flow/derive-script1.png "Példa származtatása")

Az átalakítás adatfolyam-parancsfájlja az alábbi kódrészletben található:

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>További lépések

- További információ az [Adatfolyam leképezése kifejezés nyelvéről.](data-flow-expression-functions.md)
