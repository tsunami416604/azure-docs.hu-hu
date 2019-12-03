---
title: Azure Data Factory leképezési adatfolyam-Kifejezésszerkesztő
description: A Azure Data Factory leképezési adatforgalmához tartozó Kifejezésszerkesztő
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2019
ms.openlocfilehash: 0eb2c2692ed2444a85e7253c6fdd8734385ff881
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672270"
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

A Azure Data Factory adatáramlás kifejezésének nyelve, a [teljes referenciák dokumentációja itt](https://aka.ms/dataflowexpressions)lehetővé teszi a reguláris kifejezések szintaxisát tartalmazó függvények használatát. Reguláris kifejezések funkcióinak használatakor a Kifejezésszerkesztő a fordított perjelet (\\) fogja értelmezni Escape-karakteres sorozatot használva. Ha a reguláris kifejezésben fordított perjeleket használ, zárja be a teljes regexet a kullancsokban (\`), vagy használjon dupla fordított perjelet.

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

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

* ```Ctrl-K Ctrl-C```: megjegyzések teljes sora
* ```Ctrl-K Ctrl-U```: Megjegyzés kifűzése
* ```F1```: a szerkesztő Súgó parancsainak megadása
* ```Alt-Down Arrow```: az aktuális sor áthelyezése lefelé
* ```Alt-Up Arrow```: az aktuális vonal feljebb helyezése
* ```Cntrl-Space```: a kontextus súgójának megjelenítése

## <a name="manual-comments"></a>Manuális megjegyzések

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Ha a kifejezés tetején lévő megjegyzést helyez el, az átalakítás szövegmezőben megjelenik az átalakítási kifejezések dokumentálására:

![Megjegyzések](media/data-flow/comments2.png "Megjegyzések")

## <a name="convert-to-dates-or-timestamps"></a>Konvertálás dátumokra vagy időbélyegekre

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Vegye figyelembe, hogy ha karakterlánc-konstansokat szeretne szerepeltetni az időbélyeg-kimenetben, ```toString()```belül kell becsomagolni az átalakítást.

Itt megtudhatja, hogyan alakíthatja át a másodperceket a korszakból egy dátumra vagy időbélyegre:

```toTimestamp(1574127407*1000l)```

Figyelje meg, hogy a fenti kifejezés végén a "l" záró látható. Ez azt jelenti, hogy a konverzió nem a sor szintaxisa szerint hosszú.

## <a name="handling-column-names-with-special-characters"></a>Oszlopnevek speciális karakterekkel való kezelésére

Ha speciális karaktereket vagy szóközöket tartalmazó oszlopnevek vannak, a nevet kapcsos zárójelekkel kell megadnia.

```{[dbo].this_is my complex name$$$}```

## <a name="next-steps"></a>Következő lépések

[Az Adatátalakítási kifejezések létrehozásának megkezdése](data-flow-expression-functions.md)
