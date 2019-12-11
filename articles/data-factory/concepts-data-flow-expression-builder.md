---
title: Expression Builder az adatforgalom leképezéséhez
description: Kifejezések készítése a Kifejezésszerkesztő használatával az adatfolyamatok leképezése Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969361"
---
# <a name="building-expressions-in-mapping-data-flow"></a>Kifejezések készítése a leképezési adatfolyamban

A leképezési adatfolyamban számos átalakítási tulajdonságot kifejezésként kell megadni. Ezek a kifejezések olyan oszlop-értékekkel, paraméterekkel, függvényekkel, operátorokkal és literálokból állnak, amelyek futási időben kiértékelik a Spark adattípust.

## <a name="opening-the-expression-builder"></a>A Kifejezésszerkesztő megnyitása

Az adatfeldolgozó UX-ben a kifejezés-szerkesztési felület ismeri a Kifejezésszerkesztő **kifejezést**. A kifejezés logikájában megadott módon a Factory [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) -kódokat használ a kiemeléshez, a szintaxis ellenőrzéséhez és az autocomplete értékhez.

![Expression Builder](media/data-flow/xpb1.png "Kifejezésszerkesztő")

Olyan átalakításokban, mint például a származtatott oszlop és a szűrő, ahol a kifejezések kötelezőek, a kék kifejezés mezőre kattintva nyissa meg a Kifejezésszerkesztő kifejezést.

![Expression Builder](media/data-flow/expressionbox.png "Kifejezésszerkesztő")

Ha egy egyező vagy egy csoportba tartozó oszlopra hivatkozik, egy kifejezés kinyerheti az oszlopokból származó értékeket. Kifejezés létrehozásához válassza a "számított oszlop" lehetőséget.

![Expression Builder](media/data-flow/computedcolumn.png "Kifejezésszerkesztő")

Azokban az esetekben, amikor egy kifejezés vagy literális érték érvényes bemenet, a "dinamikus tartalom hozzáadása" lehetőséggel olyan kifejezést hozhat létre, amely kiértékeli a konstanst.

![Expression Builder](media/data-flow/add-dynamic-content.png "Kifejezésszerkesztő")

## <a name="expression-language-reference"></a>Kifejezés nyelvi referenciája

Az adatfolyamatok leképezése olyan függvényeket és operátorokat tartalmaz, amelyek kifejezésekben használhatók. Az elérhető függvények listája a leképezési adatfolyam- [kifejezés nyelvi](data-flow-expression-functions.md) referenciája oldalon található.

## <a name="column-names-with-special-characters"></a>Oszlopok nevei speciális karakterekkel

Ha speciális karaktereket vagy szóközöket tartalmazó oszlopnevek vannak, a nevet kapcsos zárójelekkel kell megadnia, hogy azok egy kifejezésben legyenek hivatkozva.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>Kifejezés eredményeinek megtekintése

Ha a [hibakeresési mód](concepts-data-flow-debug-mode.md) be van kapcsolva, az élő Spark-fürt használatával megtekintheti a kifejezés kiértékelésének folyamaton belüli előzetes verzióját. A logikájának létrehozásakor valós időben lehet hibakeresést végezni a kifejezésben. 

![Expression Builder](media/data-flow/exp4b.png "Kifejezési adatelőnézet")

A frissítés gombra kattintva frissítheti a kifejezés eredményét a forrás élő mintáján.

![Expression Builder](media/data-flow/exp5.png "Kifejezési adatelőnézet")

## <a name="string-interpolation"></a>Karakterlánc-interpoláció

A kettős idézőjelek használatával literális karakterlánc-szöveget és kifejezéseket is használhat. Kifejezési függvények, oszlopok és paraméterek is megadhatók. A karakterlánc-interpolációval elkerülhető a karakterlánc-összefűzések széles körű használata, ha a lekérdezési karakterláncokban is szerepelnek paraméterek. A kifejezés szintaxisának használatához kapcsos zárójelek közé kell tenni,

Néhány példa a karakterlánc-interpolációra:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>Megjegyzések kifejezései

Megjegyzések hozzáadása a kifejezésekhez az egysoros és a többsoros Megjegyzés szintaxisának használatával:

![Megjegyzések](media/data-flow/comments.png "Megjegyzések")

Az alábbi példák érvényes megjegyzéseket tartalmaznak:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Ha a kifejezés tetején lévő megjegyzést helyez el, az átalakítás szövegmezőben megjelenik az átalakítási kifejezések dokumentálására:

![Megjegyzések](media/data-flow/comments2.png "Megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

Számos kifejezés nyelvi függvénye reguláris kifejezési szintaxist használ. Reguláris kifejezések funkcióinak használatakor a Kifejezésszerkesztő a fordított perjelet (\\) fogja értelmezni Escape-karakteres sorozatot használva. Ha a reguláris kifejezésben fordított perjeleket használ, zárja be a teljes regexet a kullancsokban (\`), vagy használjon dupla fordított perjelet.

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

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

* ```Ctrl-K Ctrl-C```: megjegyzések teljes sora
* ```Ctrl-K Ctrl-U```: Megjegyzés kifűzése
* ```F1```: a szerkesztő Súgó parancsainak megadása
* ```Alt-Down Arrow```: az aktuális sor lejjebb helyezése
* ```Alt-Up Arrow```: az aktuális sor feljebb helyezése
* ```Cntrl-Space```: a kontextus súgójának megjelenítése

## <a name="convert-to-dates-or-timestamps"></a>Konvertálás dátumokra vagy időbélyegekre

Ha karakterlánc-konstansokat szeretne szerepeltetni az időbélyeg-kimenetben, be kell csomagolnia a konverziót ```toString()```ba.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Az időpontok és az időbélyegek közötti ezredmásodpercek konvertálásához használja a `toTimestamp(<number of milliseconds>)`. Ha az idő másodpercben érkezik, szorozza meg a 1000-as értékkel.

```toTimestamp(1574127407*1000l)```

A fenti kifejezés végén lévő "l" záró érték a hosszú típusra való átalakítást mutatja be, a soron belüli szintaxisként.

## <a name="next-steps"></a>Következő lépések

[Az Adatátalakítási kifejezések létrehozásának megkezdése](data-flow-expression-functions.md)
