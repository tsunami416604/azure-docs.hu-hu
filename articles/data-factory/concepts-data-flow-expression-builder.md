---
title: Expression Builder az adatforgalom leképezéséhez
description: Kifejezések létrehozása a Azure Data Factory az adatfolyamok leképezése a Expression Builder használatával
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/10/2020
ms.openlocfilehash: f522812f762b55ec61794101e6cd1ec15fb171ca
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212105"
---
# <a name="build-expressions-in-mapping-data-flow"></a>Kifejezések létrehozása a leképezési adatfolyamban

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

A leképezési adatfolyamban számos átalakítási tulajdonságot kifejezésként kell megadni. Ezek a kifejezések olyan oszlop-értékekkel, paraméterekkel, függvényekkel, operátorokkal és literálokból állnak, amelyek futási időben kiértékelik a Spark adattípust.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>Expression Builder megnyitása

A Azure Data Factory felhasználói élményben a kifejezés szerkesztési felülete Expression Builder néven ismert. A kifejezés logikájának megadásakor Data Factory az [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) -kód befejezését használja a kiemeléshez, a szintaxis ellenőrzéséhez és az autocomplete utasításhoz.

![Expression Builder](media/data-flow/xpb1.png "Kifejezésszerkesztő")

Az átalakításokban, mint például a származtatott oszlop és a szűrő, ahol a kifejezések kötelezőek, a Kifejezésszerkesztő megnyitásához válassza a kék kifejezés mezőt.

![Kék kifejezés mező](media/data-flow/expressionbox.png "Kifejezésszerkesztő")

Ha egyező vagy csoportosított oszlopokra hivatkozik, akkor egy kifejezés kinyerheti az oszlopokból származó értékeket. Kifejezés létrehozásához válassza a **számított oszlop**lehetőséget.

![Számított oszlop beállítás](media/data-flow/computedcolumn.png "Kifejezésszerkesztő")

Azokban az esetekben, amikor egy kifejezés vagy literális érték érvényes bemenet, válassza a **dinamikus tartalom hozzáadása** lehetőséget, ha olyan kifejezést szeretne létrehozni, amely kiértékeli a konstans értéket.

![Dinamikus tartalom hozzáadása lehetőség](media/data-flow/add-dynamic-content.png "Kifejezésszerkesztő")

## <a name="expression-language-reference"></a>Kifejezés nyelvi referenciája

Az adatfolyamatok leképezése beépített függvényekkel és operátorokkal rendelkezik, amelyek kifejezésekben használhatók. Az elérhető függvények listáját lásd: [Expression functions a leképezési adatfolyamban](data-flow-expression-functions.md).

## <a name="column-names-with-special-characters"></a>Oszlopok nevei speciális karakterekkel

Ha speciális karaktereket vagy szóközöket tartalmazó oszlopnevek vannak, a nevet kapcsos zárójelekkel kell megadnia, hogy azok egy kifejezésben legyenek hivatkozva.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>Előnézet kifejezés eredményei

Ha a [hibakeresési mód](concepts-data-flow-debug-mode.md) be van kapcsolva, az élő Spark-fürt használatával megtekintheti a kifejezés kiértékelésének folyamatát. A logikájának összeállítása során valós időben hibakeresést végezhet a kifejezésben. 

![Folyamatban lévő előzetes verzió](media/data-flow/exp4b.png "Kifejezési adatelőnézet")

A **frissítés** gombra kattintva frissítheti a kifejezés eredményét a forrás élő mintáján.

![Frissítés gomb](media/data-flow/exp5.png "Kifejezési adatelőnézet")

## <a name="string-interpolation"></a>Karakterlánc-interpoláció

Az idézőjelek használatával literális karakterlánc-szöveget és kifejezéseket is lehet csatolni. Kifejezési függvények, oszlopok és paraméterek is megadhatók. A karakterlánc-interpolációval elkerülhető a karakterlánc-Összefűzés nagy mértékű használata, ha paraméterek szerepelnek a lekérdezési karakterláncokban. A kifejezés szintaxisának használatához kapcsos zárójelek közé kell tenni,

Néhány példa a karakterlánc-interpolációra:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>Megjegyzés kifejezései

Megjegyzéseket fűzhet a kifejezésekhez egy egysoros és többsoros Megjegyzés szintaxisának használatával.

Az alábbi példák érvényes megjegyzéseket tartalmaznak:

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```

Ha a kifejezés tetején egy megjegyzést helyez el, az átalakítás szövegmezőben megjelenik az átalakítási kifejezések dokumentálása.

![Megjegyzés az átalakítás szövegmezőben](media/data-flow/comments2.png "Megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

Számos kifejezés nyelvi függvénye reguláris kifejezési szintaxist használ. Reguláris kifejezések használata esetén az Expression Build Escape-karakterként próbálja értelmezni a fordított perjelet ( \\ ). Ha a reguláris kifejezésben fordított perjeleket használ, zárja be a teljes regexet a aposztrófokkal () metódusba, \` vagy használjon dupla fordított perjelet.

Aposztrófokkal használó példa:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

Dupla perjelet használó példa:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>Címterület-indexek

A tömböket visszaadó Expression függvények a zárójelek ([]) használatával kezelik a visszaadott tömb objektumain belüli indexeket. A tömb alapja az is.

![Expression Builder-tömb](media/data-flow/expb2.png "Kifejezési adatelőnézet")

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

* CTRL + K CTRL + C: Megjegyzés teljes sor.
* CTRL + K CTRL + U: Megjegyzés visszatartása.
* F1: adja meg a szerkesztő súgójának utasításait.
* ALT + lefelé nyíl gomb: az aktuális sor lejjebb helyezése.
* ALT + fel nyíl gomb: az aktuális sor feljebb helyezése.
* CTRL + SZÓKÖZ: környezetfüggő súgó megjelenítése.

## <a name="convert-to-dates-or-timestamps"></a>Konvertálás dátumokra vagy időbélyegekre

Ha karakterlánc-konstansokat szeretne szerepeltetni az időbélyeg-kimenetben, csomagolja be a konverzióját ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Az ezredmásodpercek időpontról dátumra vagy időbélyegre való konvertálásához használja a következőt: `toTimestamp(<number of milliseconds>)` . Ha az idő másodpercben érkezik, szorozza meg a 1 000-as értékkel.

```toTimestamp(1574127407*1000l)```

Az előző kifejezés végén lévő "l" záró érték hosszú típusra, beágyazott szintaxisként való átalakítást jelent.

## <a name="find-time-from-epoch-or-unix-time"></a>Idő megkeresése az EPOCH vagy a Unix idő alapján

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' éééé-hh-nn óó: PP: mm. ÉER) *) * 1000

## <a name="next-steps"></a>Következő lépések

[Az Adatátalakítási kifejezések létrehozásának megkezdése](data-flow-expression-functions.md)
