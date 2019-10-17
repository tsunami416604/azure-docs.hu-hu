---
title: Azure Data Factory leképezési adatfolyam-Kifejezésszerkesztő
description: A Azure Data Factory leképezési adatforgalmához tartozó Kifejezésszerkesztő
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/30/2019
ms.openlocfilehash: 872c7ce6a0c39ab19165a5f16ea3e4f6ef8bd6a5
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72388054"
---
# <a name="mapping-data-flow-expression-builder"></a>Adatforgalmi kifejezés-szerkesztő leképezése



Azure Data Factory leképezési adatfolyamatban megtalálhatja azokat a kifejezéseket, amelyekben kifejezéseket adhat meg az adatátalakításhoz. Ezekben a mezőkben oszlopok, mezők, változók, paraméterek és függvények használhatók az adatforgalomból. A kifejezés létrehozásához használja a kifejezés-szerkesztőt, amelyet az átalakítás során a kifejezés szövegmezőre kattintva indít el. Időnként a "számított oszlop" lehetőséget is láthatja az átalakítási oszlopok kiválasztásakor. Ha erre kattint, megjelenik a Kifejezésszerkesztő elindítva.

![Expression Builder](media/data-flow/xpb1.png "Kifejezésszerkesztő")

A Kifejezésszerkesztő eszköz alapértelmezett értéke a szövegszerkesztő beállítás. az automatikus kiegészítés funkció a teljes Azure Data Factory adatfolyam-objektum modellből olvassa be a szintaxis-ellenőrzést és a kiemelést.

![Expression Builder automatikus kiegészítése](media/data-flow/expb1.png "Expression Builder automatikus kiegészítése")

## <a name="build-schemas-in-output-schema-pane"></a>Sémák összeállítása a kimeneti séma ablaktáblán

![Összetett oszlop hozzáadása](media/data-flow/complexcolumn.png "Oszlopok hozzáadása")

A bal oldali kimeneti séma ablaktáblán látni fogja a módosított oszlopokat, és hozzáadja a sémához. Itt interaktív módon hozhat létre egyszerű és összetett adatstruktúrákat. Vegyen fel további mezőket az "oszlop hozzáadása" és a hierarchiák létrehozása az "aloszlop hozzáadása" használatával.

![Aloszlop hozzáadása](media/data-flow/addsubcolumn.png "Aloszlop hozzáadása")

## <a name="data-preview-in-debug-mode"></a>Az adatelőnézet hibakeresési módban

![Expression Builder](media/data-flow/exp4b.png "Kifejezési adatelőnézet")

Ha az adatfolyam-kifejezéseken dolgozik, kapcsolja be a hibakeresési módot a Azure Data Factory adatfolyam tervezési felületéről, amely lehetővé teszi, hogy a rendszer az Ön által létrehozott kifejezésből élő, folyamatban lévő előnézetet jelenítse meg az adatok eredményeiről. A valós idejű élő hibakeresés engedélyezve van a kifejezésekhez.

![Hibakeresési mód](media/data-flow/debugbutton.png "Hibakeresés gomb")

A frissítés gombra kattintva frissítheti a kifejezés eredményét a forrás élő mintáján valós időben.

![Expression Builder](media/data-flow/exp5.png "Kifejezési adatelőnézet")

## <a name="comments"></a>Megjegyzések

Megjegyzések hozzáadása a kifejezésekhez az egysoros és a többsoros Megjegyzés szintaxisának használatával:

![Megjegyzések](media/data-flow/comments.png "Megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

A Azure Data Factory adatáramlás kifejezésének nyelve, a [teljes referenciák dokumentációja itt](https://aka.ms/dataflowexpressions)lehetővé teszi a reguláris kifejezések szintaxisát tartalmazó függvények használatát. Reguláris kifejezések funkcióinak használatakor a Kifejezésszerkesztő a fordított perjelet (\\) fogja értelmezni Escape-karakteres sorozatként. Ha a reguláris kifejezésben fordított perjeleket használ, zárja be a teljes regexet a kullancsokban (\`), vagy használjon dupla fordított perjelet.

Példa a ticks használatával

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

vagy dupla perjel használatával

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>Tömb indexek kezelése

A tömböket visszaadó Expression függvények a szögletes zárójelek ([]) használatával kezelik a visszaadott tömb objektumon belüli indexeket. A tömb a-alapú.

![Expression Builder-tömb](media/data-flow/expb2.png "Kifejezési adatelőnézet")

## <a name="handling-names-with-special-characters"></a>Nevek speciális karakterekkel való kezelésére

Ha speciális karaktereket vagy szóközöket tartalmazó oszlopnevek vannak, a nevet kapcsos zárójelekkel kell megadnia.
* ```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Következő lépések

[Az Adatátalakítási kifejezések létrehozásának megkezdése](data-flow-expression-functions.md)
