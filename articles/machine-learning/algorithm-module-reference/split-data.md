---
title: 'Adatdarabolás: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan oszthat meg egy adatkészletet két különálló készletre a Azure Machine Learning az adatfelosztási modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: d889cd3325784f564d03e5d75dde1ec760c66804
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268537"
---
# <a name="split-data-module"></a>Adategység felosztása

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal két különálló készletre oszthat egy adatkészletet.

Ez a modul különösen akkor hasznos, ha a képzési és tesztelési csoportokban külön kell elkülönítenie az adategységeket. Testre szabhatja az adatmegosztás módját is. Egyes lehetőségek támogatják az adatvéletlenszerűség használatát; mások egy adott adattípus vagy modell típusára vannak igazítva.

## <a name="how-to-configure"></a>Konfigurálás

> [!TIP]
> A felosztási mód kiválasztása előtt olvassa el az összes lehetőséget, hogy meghatározza a szükséges felosztás típusát.
> Ha megváltoztatja a felosztási módot, az összes többi beállítás alaphelyzetbe állítható.

1. Adja hozzá a **feldarabolt** adatmodult a folyamathoz a tervezőben. Ez a modul az **adatátalakítás**alatt, a **minta és a felosztás** kategóriában található.

2. **Felosztási mód**: válasszon az alábbi módok közül, attól függően, hogy milyen típusú adattípussal rendelkezik, és hogyan szeretné osztani. Az egyes felosztási módok különböző beállításokkal rendelkeznek. Részletes útmutatást és példákat a következő témakörökben talál. 

    - **Sorok felosztása**: akkor használja ezt a beállítást, ha csak két részre szeretné osztani az adatterületet. Megadhatja, hogy az egyes felosztásokban hány adatmennyiség legyen elhelyezve, de alapértelmezés szerint az adatmennyiség 50-50.

        Az egyes csoportokban lévő sorok kijelölését, valamint rétegzett mintavételezést is használhat. Rétegzett mintavételezés esetén ki kell választania egy olyan adatoszlopot, amelynek az értékeit egyenlően kell kiosztani a két eredmény-adatkészletek között.  

    - **Reguláris kifejezés felosztása**  Akkor válassza ezt a lehetőséget, ha az adatkészletet el szeretné osztani egy érték egyetlen oszlopának tesztelésével.

        Ha például a hangulat elemzését végezheti el, akkor érdemes megkeresni egy adott terméknév jelenlétét egy szövegmezőben, majd az adatkészletet a célként megadott terméknév és a nélküli sorokra osztani.

    - **Relatív kifejezés felosztása**: akkor használja ezt a beállítást, ha egy feltételt egy Number oszlopra kíván alkalmazni. A szám lehet dátum/idő mező, az Age vagy a dollár összegét tartalmazó oszlop, vagy akár egy százalék is. Előfordulhat például, hogy az adatkészletet az elemek díjszabása alapján szeretné felosztani, a személyeket korcsoport szerint csoportosítani, vagy az adatokat egy naptári dátum alapján elkülönítve.

### <a name="split-rows"></a>Sorok felosztása

1.  Adja hozzá a [feldarabolt](./split-data.md) adatmodult a folyamathoz a tervezőben, és kapcsolódjon a felosztani kívánt adatkészlethez.
  
2.  A **felosztási mód**beállításnál válassza a **sorok felosztása**lehetőséget. 

3.  **Az első kimeneti adatkészletben szereplő sorok töredéke**. Ezzel a beállítással meghatározhatja, hogy hány sor kerüljön be az első (bal oldali) kimenetbe. Minden más sor a második (jobb oldali) kimenetre lép.

    Az arány az első kimeneti adatkészletbe küldendő sorok százalékos arányát jelöli, ezért 0 és 1 közötti decimális számot kell megadnia.
     
     Ha például a 0,75 értéket adja meg értékként, az adatkészlet egy 75:25-arány használatával oszlik el, az első kimeneti adatkészletbe elküldett sorok 75%-ában, a második kimeneti adatkészletbe pedig 25%-ot.
  
4. Válassza ki a **randomizált felosztási** lehetőséget, ha az adatválasztást véletlenszerűen szeretné kijelölni a két csoportban. Ez az előnyben részesített lehetőség a képzési és tesztelési adatkészletek létrehozásakor.

5.  **Véletlenszerű mag**: adjon meg egy nem negatív egész értéket a használni kívánt példányok bájtján elvégeznek-sorozatának inicializálásához. Ezt az alapértelmezett magot használja a rendszer minden olyan modulban, amely véletlenszerű számokat eredményez. 

     A magok megadásával az eredmények általánosan megismételhetők. Ha meg kell ismételnie egy felosztási művelet eredményét, meg kell adnia egy magot a véletlenszám-generátorhoz. Ellenkező esetben a véletlenszerű magok értéke alapértelmezés szerint 0, ami azt jelenti, hogy a kezdeti mag értékét a rendszer órája szerzi be. Ennek eredményeképpen az adatok eloszlása némileg eltérő lehet minden alkalommal, amikor elvégez egy felosztást. 

6. **Rétegzett felosztás**: ezt a beállítást állítsa **igaz** értékre, hogy a két kimeneti adatkészlet a *rétegek oszlopban* vagy a *csoportosítási kulcs oszlopban*szereplő értékek reprezentatív mintáját tartalmazza. 

    Rétegzett mintavételezés esetén az adatokat a rendszer úgy osztja szét, hogy mindegyik kimeneti adatkészlet nagyjából az egyes célértékek azonos százalékát kapja meg. Például érdemes lehet biztosítani, hogy a képzési és tesztelési készletek nagyjából egyensúlyban legyenek az eredményekkel, vagy egy másik oszlop, például a nemek tekintetében.

7. A folyamat futtatása.


## <a name="regular-expression-split"></a>Reguláris kifejezés felosztása

1.  Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz, és kapcsolja bemenetként a felosztani kívánt adatkészlethez.  
  
2.  A **felosztási mód**beállításnál válassza a **reguláris kifejezés felosztása**elemet.

3. A **reguláris kifejezés** mezőbe írjon be egy érvényes reguláris kifejezést. 
  
   A reguláris kifejezésnek a Python reguláris kifejezés szintaxisát kell követnie.


4. A folyamat futtatása.

    Az Ön által megadott reguláris kifejezés alapján az adatkészlet két sorra van osztva: sorokba, amelyek megfelelnek a kifejezésnek és az összes többi sornak. 

Az alábbi példák bemutatják, hogyan oszthat meg egy adatkészletet a **reguláris kifejezés** lehetőség használatával. 

### <a name="single-whole-word"></a>Egyetlen egész szó 

Ez a példa az első adatkészletbe helyezi az összes olyan sort, amely tartalmazza az oszlop `Text``Gryphon` szövegét, és más sorokat helyez el a **felosztott adat**második kimenetére:

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>substring

Ez a példa a megadott karakterláncot az adatkészlet második oszlopában található bármely pozícióban keresi, amelyet az 1. index értéke jelöl. A egyezés megkülönbözteti a kis-és nagybetűket.

```text
(\1) ^[a-f]
```

Az első eredmény adatkészlet minden olyan sort tartalmaz, amelyben az index oszlop a következő karakterek egyikével kezdődik: `a`, `b`, `c`, `d`, `e`, `f`. Minden más sor a második kimenetre lesz irányítva.

## <a name="relative-expression-split"></a>Relatív kifejezés felosztása.

1. Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz, és kapcsolja bemenetként a felosztani kívánt adatkészlethez.
  
2. A **felosztási mód**beállításnál válassza a **relatív kifejezés felosztása**elemet.
  
3. A **relációs kifejezés** szövegmezőbe írja be azt a kifejezést, amely egy összehasonlítási műveletet hajt végre egyetlen oszlopon:

   A **numerikus oszlophoz**:
   - Az oszlop a numerikus adattípusok számát tartalmazza, beleértve a dátum-és időtípusokat is.
   - A kifejezés legfeljebb egy oszlop nevét hivatkozhat.
   - A és a művelethez használja a & jel karaktert `&`. Használja a vagy a művelethez `|` pipe karaktert.
   - A következő operátorok támogatottak: `<`, `>`, `<=`, `>=`, `==`, `!=`.
   - `(` és `)`használatával nem csoportosíthatjuk a műveleteket.
   
   A **String oszlop**esetében:
   - A következő operátorok támogatottak: `==`, `!=`.

4. A folyamat futtatása.

    A kifejezés két sorból osztja el az adatkészletet: a feltételnek megfelelő értékeket tartalmazó sorok és az összes többi sor.

Az alábbi példák bemutatják, hogyan oszthat meg egy adatkészletet a **relatív kifejezés** lehetőséggel az **adatfelosztási** modulban:  

### <a name="using-calendar-year"></a>Naptári év használata

Gyakori forgatókönyv, hogy az adatkészletet évek szerint osztják el. A következő kifejezés kiválasztja azokat a sorokat, amelyekben az oszlopban szereplő értékek `Year` nagyobbak, mint `2010`.

```text
\"Year" > 2010
```

A Date kifejezésnek meg kell felelnie az adatoszlopban szereplő összes dátum résznek, és az adatoszlopban lévő dátumok formátumának konzisztensnek kell lennie. 

Például egy Date (dátum) oszlopban a következő formátumban `mmddyyyy`a kifejezésnek a következőképpen kell megjelennie:

```text
\"Date" > 1/1/2010
```

### <a name="using-column-indices"></a>Column indexek használata

A következő kifejezés azt mutatja be, hogyan használható az oszlop indexe az adatkészlet első oszlopában lévő összes olyan sor kiválasztására, amelyek értéke nem lehet kisebb, mint 30, de nem egyenlő 20 értékkel.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
