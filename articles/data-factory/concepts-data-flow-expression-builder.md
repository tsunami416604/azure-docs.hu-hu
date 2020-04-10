---
title: Kifejezésszerkesztő az adatfolyam leképezésében
description: Kifejezések létrehozása az Expression Builder használatával az Azure Data Factory adatfolyamainak leképezésében
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: 196d917d31eb08af80587bba30d9f7e67bf8cbea
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991705"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Kifejezések létrehozása az adatfolyam leképezésében

Az adatfolyam leképezése során számos átalakítási tulajdonság ot kell kifejezésként beírni. Ezek a kifejezések oszlopértékekből, paraméterekből, függvényekből, operátorokból és konstansokból állnak, amelyek futásidőben kiértékelik a Spark-adattípust.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Kifejezésszerkesztő megnyitása

Az Azure Data Factory felhasználói felületének kifejezésszerkesztő felülete kifejezésszerkesztő néven ismert. A kifejezéslogikába való belépéskor a Data Factory [az IntelliSense-kód](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) kiegészítést használja a kiemeléshez, a szintaktikai ellenőrzéshez és az automatikus kiegészítéshez.

![Kifejezésszerkesztő](media/data-flow/xpb1.png "Kifejezésszerkesztő")

Az olyan átalakításoknál, mint a származtatott oszlop és a szűrő, ahol a kifejezések kötelezőek, a kék kifejezésmező kiválasztásával nyissa meg a Kifejezésszerkesztőt.

![Kék kifejezés mező](media/data-flow/expressionbox.png "Kifejezésszerkesztő")

Ha egyező vagy csoportonkénti feltétel oszlopaira hivatkozik, a kifejezés értékeket nyerhet ki oszlopokból. Kifejezés létrehozásához válassza **a Számított oszlop lehetőséget.**

![Számított oszlop beállítás](media/data-flow/computedcolumn.png "Kifejezésszerkesztő")

Azokban az esetekben, ahol egy kifejezés vagy egy literális érték érvényes bemenet, válassza **a Dinamikus tartalom hozzáadása** lehetőséget egy olyan kifejezés létrehozásához, amely konstans értékre értékel.

![Dinamikus tartalom hozzáadása beállítás](media/data-flow/add-dynamic-content.png "Kifejezésszerkesztő")

## <a name="expression-language-reference"></a>Kifejezés nyelvének hivatkozása

Az adatfolyamok leképezése olyan beépített függvényekkel és operátorokkal rendelkezik, amelyek kifejezésekben használhatók. Az elérhető függvények listáját a [Kifejezés függvények a leképezési adatfolyamban](data-flow-expression-functions.md)című témakörben található.

## <a name="column-names-with-special-characters"></a>Különleges karakterekkel rendelkező oszlopnevek

Ha speciális karaktereket vagy szóközöket tartalmazó oszlopnevekkel rendelkezik, a nevet kapcsos zárójelekkel veszi körül, hogy egy kifejezésben hivatkozzon rájuk.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Kifejezés eredmények előnézetének megtekintése

Ha [a hibakeresési mód](concepts-data-flow-debug-mode.md) be van kapcsolva, az élő Spark-fürt segítségével megtekintheti a kifejezés kiértékelésének folyamatban lévő előnézetét. A logika kiépítése közben valós időben debugolhatja a kifejezést. 

![Folyamatban lévő előzetes verzió](media/data-flow/exp4b.png "Kifejezésadatok előnézete")

Válassza a **Frissítés** lehetőséget a kifejezés eredményeinek frissítéséhez a forrás élő mintájához.

![Frissítés gomb](media/data-flow/exp5.png "Kifejezésadatok előnézete")

## <a name="string-interpolation"></a>Karakterlánc interpolációja

Idézőjelek használatával a literális karakterlánc-szöveget kifejezésekközé fűzte. Kifejezésfüggvényeket, oszlopokat és paramétereket is tartalmazhat. A karakterlánc-interpoláció akkor hasznos, ha a lekérdezési karakterláncok paramétereket tartalmaznak. A kifejezés szintaxisának használatához tegyezsünk meg kapcsos zárójelekbe,

Néhány példa a karakterlánc-interpolációra:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Megjegyzéskifejezések

Megjegyzéseket fűzzön a kifejezésekhez az egysoros és a többsoros megjegyzés szintaxisával.

![Egysoros és többsoros megjegyzés szintaxisa](media/data-flow/comments.png "Megjegyzések")

A következő példák érvényes megjegyzések:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

Ha megjegyzést helyez a kifejezés tetejére, az az átalakítási kifejezések dokumentálása ként jelenik meg az átalakítási szövegmezőben.

![Megjegyzés az átalakítás szövegmezőjében](media/data-flow/comments2.png "Megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

Számos kifejezésnyelvi függvény reguláris kifejezésszintaxist használ. Reguláris kifejezésfüggvények használatakor az Expression Builder\\megpróbálja a fordított perjelet ( ) escape karaktersorozatként értelmezni. Ha fordított perjeleket használ a reguláris kifejezésben, vagy a\`teljes regexet mellékelje backticks ( ) vagy dupla fordított perjel használatával.

Egy példa, amely backticks:A example that uses backticks:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Kettős vágást használó példa:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Címtömb-indexek

A tömböket visszaadó kifejezésfüggvények esetén szögletes zárójelek ([]) használatával címezz meg bizonyos indexeket az adott tömbobjektumokban. A tömb alapja is.

![Kifejezésszerkesztő tömb](media/data-flow/expb2.png "Kifejezésadatok előnézete")

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

* Ctrl+K Ctrl+C: A teljes sor megjegyzése.
* Ctrl+K Ctrl+U: Megjegyzés fésületlenítése
* F1: Szerkesztői súgóparancsok biztosítása.
* Alt+Le nyílbillentyű: Lépés az aktuális sor lefelé.
* Alt+Fel nyílbillentyű: Feljebb az aktuális sor.
* Ctrl+Szóköz: Környezetsúgó megjelenítése.

## <a name="convert-to-dates-or-timestamps"></a>Konvertálás dátumokká vagy időbélyeggé

Ha karakterlánc-konstansokat szeretne felvenni az ```toString()```időbélyeg kimenetébe, csomagolja be a konverziót a alkalmazásban.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Ha ezredmásodperceket szeretne konvertálni korszakból `toTimestamp(<number of milliseconds>)`dátummá vagy időbélyeggé, használja a használatát. Ha az idő másodpercben jön, szorozzuk meg 1000-el.

```toTimestamp(1574127407*1000l)```

Az előző kifejezés végén található záró "l" kifejezés egy hosszú típusra való áttérést jelent szövegközi szintaxisként.

## <a name="next-steps"></a>További lépések

[Adatátalakítási kifejezések létrehozásának megkezdése](data-flow-expression-functions.md)
