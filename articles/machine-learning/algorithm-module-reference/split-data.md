---
title: 'Adatok felosztása: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Split Data modult az Azure Machine Learningben egy adatkészlet két különálló készletre osztásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 9eba6f2c47629b708dde4a5a2888b76dbd24b4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455893"
---
# <a name="split-data-module"></a>Adatok felosztása modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Az Adatok felosztása modul segítségével az adatkészletet két különálló halmazra oszthatja.

Ez a modul akkor hasznos, ha az adatokat betanítási és tesztelési készletekre kell elkülöníteni. Az adatok felosztásának módját is testreszabhatja. Egyes beállítások támogatják az adatok véletlenszerűvé t. Mások egy bizonyos adattípushoz vagy modelltípushoz vannak szabva.

## <a name="configure-the-module"></a>A modul konfigurálása

> [!TIP]
> A felosztási mód kiválasztása előtt olvassa el az összes beállítást a szükséges felosztás típusának meghatározásához.
> Ha módosítja a felosztási módot, az összes többi beállítás alaphelyzetbe állhat.

1. Adja hozzá a **Split Data** modult a tervező folyamatához. Ezt a modult az **Adatátalakítás**csoportban, a **Minta és** a Felosztás kategóriában találja.

1. **Felosztási mód:** Válasszon egyet az alábbi módok közül, attól függően, hogy milyen típusú adatokkal rendelkezik, és hogyan szeretné osztani. Minden felosztási módnak különböző lehetőségei vannak.

   - **Sorok felosztása:** Akkor használja ezt a beállítást, ha csak két részre szeretné osztani az adatokat. Megadhatja, hogy az egyes felosztásokban hány százalékot kell behelyezni. Alapértelmezés szerint az adatok 50/50 arányban vannak felosztva.

     Az egyes csoportok sorainak kiválasztását is véletlenszerűvé teheti, és rétegzett mintavételt használhat. Rétegzett mintavételezésesetén ki kell választania egy adatoszlopot, amelyhez egyenlő en fel szeretné osztani az értékeket a két eredményadatkészlet között.  

   - **Reguláris kifejezés felosztása:** Akkor válassza ezt a lehetőséget, ha az adatkészletet egy érték egyetlen oszlopának tesztelésével szeretné felosztani.

     Ha például a véleményeket elemzi, ellenőrizheti, hogy egy adott terméknév szerepel-e egy szövegmezőben. Ezután az adatkészletet sorokra oszthatja a céltermék nevével és a céltermék neve nélküli sorokkal.

   - **Relatív kifejezés felosztása:** Ezt a beállítást akkor használja, ha feltételt szeretne alkalmazni egy számoszlopra. A szám lehet dátum/idő mező, egy oszlop, amely kor- vagy dollárösszegeket, vagy akár százalékot tartalmaz. Előfordulhat például, hogy az adatkészletet az elemek költsége, a személyek kortartományok szerint való csoportosítása vagy az adatok naptári dátum szerint való elkülönítése alapján szeretné elosztani.

### <a name="split-rows"></a>Sorok felosztása

1. Adja hozzá a [Split Data modult](./split-data.md) a tervező folyamatához, és csatlakoztassa a felosztani kívánt adatkészletet.
  
1. **Felosztási módban**válassza **a Sorok felosztása**lehetőséget. 

1. **Az első kimeneti adatkészlet sorainak töredéke**: Ezzel a beállítással határozhatja meg, hogy hány sor kerül az első (bal oldali) kimenetbe. Az összes többi sor a második (jobb oldali) kimenetbe kerül.

   Az arány az első kimeneti adatkészletbe küldött sorok százalékos arányát jelöli, ezért 0 és 1 közötti tizedesszámot kell megadnia.
     
   Ha például **0,75** értéket ad meg értékként, az adatkészlet 75/25 arányban lesz felosztva. Ebben a felosztásban a sorok 75 százaléka az első kimeneti adatkészletbe kerül. A fennmaradó 25 százalék a második kimeneti adatkészletbe kerül.
  
1. Válassza a **Randomized split** opciót, ha az adatok két csoportba történő véletlenszerű kiválasztását szeretné véletlenszerűvé tenni. Ez az előnyben részesített lehetőség, amikor betanítási és tesztelési adatkészletek létrehozása.

1. **Véletlen mag:** Adjon meg egy nem negatív egész értéket a használandó példányok pszeudovéletlenszerű sorozatának elindításához. Ez az alapértelmezett mag minden olyan modulban használatos, amely véletlenszerű számokat generál. 

   A mag megadásával az eredmények reprodukálhatók lesznek. Ha meg kell ismételnie egy felosztási művelet eredményeit, meg kell adnia egy magot a véletlenszám-generátorhoz. Ellenkező esetben a véletlenszerű mag alapértelmezés szerint **0-ra**van állítva, ami azt jelenti, hogy a kezdeti magértéket a rendszerórából kapjuk meg. Ennek eredményeképpen az adatok eloszlása kissé eltérő lehet minden alkalommal, amikor felosztást hajt végre. 

1. **Rétegzett felosztás**: Állítsa ezt a beállítást **Igaz** értékre annak érdekében , hogy a két kimeneti adatkészlet reprezentatív mintát tartalmazzon a *rétegoszlopban* vagy a *rétegzési kulcs oszlopban*szereplő értékekből . 

   Rétegzett mintavételezéssel az adatok úgy oszlanak meg, hogy minden kimeneti adatkészlet nagyjából azonos százalékot kap az egyes célértékekből. Például érdemes lehet biztosítani, hogy a betanítási és tesztelési készletek nagyjából kiegyensúlyozottak legyenek az eredmény vagy más oszlop (például a nem) tekintetében.

1. Küldje el a folyamatot.


## <a name="select-a-regular-expression"></a>Reguláris kifejezés kijelölése

1. Adja hozzá a [Split Data](./split-data.md) modult a folyamathoz, és csatlakoztassa bemenetként a felosztani kívánt adatkészlethez.  
  
1. **Felosztási módban**válassza a **Reguláris kifejezés felosztása**lehetőséget.

1. A **Reguláris kifejezés** mezőbe írjon be egy érvényes reguláris kifejezést. 
  
   A reguláris kifejezésnek python szintaxist kell követnie a reguláris kifejezésekhez.

1. Küldje el a folyamatot.

   A megadott reguláris kifejezés alapján az adatkészlet két sorcsoportra oszlik: a kifejezésnek megfelelő értékekkel rendelkező sorokra és az összes többi sorra. 

Az alábbi példák bemutatják, hogyan lehet felosztani egy adatkészletet a **Reguláris kifejezés** beállítással. 

### <a name="single-whole-word"></a>Egyetlen egész szó 

Ez a példa az első adatkészletbe helyezi `Gryphon` az `Text`összes olyan sort, amely az oszlopban lévő szöveget tartalmazza. Más sorokat helyez a Split Data második **kimenetébe.**

```text
    \"Text" Gryphon  
```

### <a name="substring"></a>Substring

Ez a példa az adatkészlet második oszlopán belül bármely pozícióban keresi a megadott karakterláncot. A pozíciót itt az 1 indexérték jelöli. Az egyezés a kis- és nagybetűket nem érzékeny.

```text
(\1) ^[a-f]
```

Az első eredményadatkészlet tartalmazza az összes olyan sort, ahol `a` `b`az `c` `d`indexoszlop a következő karakterek egyikével kezdődik: , , , , `e`, , . `f` Az összes többi sor a második kimenetre lesz irányítva.

## <a name="select-a-relative-expression"></a>Relatív kifejezés kijelölése

1. Adja hozzá a [Split Data](./split-data.md) modult a folyamathoz, és csatlakoztassa bemenetként a felosztani kívánt adatkészlethez.
  
1. **Felosztási mód esetén**válassza a **Relatív kifejezés**lehetőséget.
  
1. A **Relációs kifejezés** mezőbe írjon be egy olyan kifejezést, amely egyetlen oszlopon végez összehasonlító műveletet.

   **Numerikus oszlop**esetén:
   - Az oszlop bármilyen numerikus adattípus tanusát tartalmazza, beleértve a dátum- és időadattípusokat is.
   - A kifejezés legfeljebb egy oszlopnévre hivatkozhat.
   - Használja az ampersand `&`karaktert, az ÉS művelethez. Használja a cső `|`karaktert, a vagy művelethez.
   - A következő operátorok `<`támogatottak: `==` `!=`, `>`, `<=` `>=`, , , .
   - A és a használatával nem `(` `)`csoportosíthatók a műveletek.
   
   Karakterlánc **oszlop esetén:**
   - A következő operátorok `==`támogatottak: . `!=`.

1. Küldje el a folyamatot.

   A kifejezés az adatkészletet két sorcsoportra osztja: a feltételnek megfelelő értékekkel rendelkező sorokra és az összes többi sorra.

Az alábbi példák bemutatják, hogyan lehet felosztani egy adatkészletet a **Relatív kifejezés** beállítás használatával a **Split Data** modulban.  

### <a name="calendar-year"></a>Naptári év

Gyakori forgatókönyv az adatkészlet ek évek közötti felosztása. A következő kifejezés minden olyan sort kijelöl, ahol az oszlopban `Year` lévő értékek nagyobbak, mint `2010`a .

```text
\"Year" > 2010
```

A dátumkifejezésnek figyelembe kell vennie az adatoszlopban szereplő összes dátumrészt. Az adatoszlopban szereplő dátumok formátumának konzisztensnek kell lennie. 

A formátumot `mmddyyyy`használó dátumoszlopokban például a kifejezésnek a következőhez hasonlónak kell lennie:

```text
\"Date" > 1/1/2010
```

### <a name="column-index"></a>Oszlopindex

A következő kifejezés bemutatja, hogyan használhatja az oszlopindexet az adatkészlet első oszlopának minden olyan sorának kijelölésére, amely legfeljebb 30, de legfeljebb 20 értéket tartalmaz.

```text
(\0)<=30 & !=20
```


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
