---
title: 'Adatdarabolás: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan oszthat meg egy adatkészletet két különálló készletre a Azure Machine Learning szolgáltatásban az adatfelosztási modul használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 3fa6b61b1b1761756339d6a7f05c74eb4a968777
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692653"
---
# <a name="split-data-module"></a>Adategység felosztása

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal két különálló készletre oszthat egy adatkészletet.

Ez a modul különösen akkor hasznos, ha a képzési és tesztelési csoportokban külön kell elkülönítenie az adategységeket. Testre szabhatja az adatmegosztás módját is. Egyes lehetőségek támogatják az adatvéletlenszerűség használatát; mások egy adott adattípus vagy modell típusára vannak igazítva.

## <a name="how-to-configure"></a>Konfigurálás

> [!TIP]
> A felosztási mód kiválasztása előtt olvassa el az összes lehetőséget, hogy meghatározza a szükséges felosztás típusát.
> Ha megváltoztatja a felosztási módot, az összes többi beállítás alaphelyzetbe állítható.

1. Adja hozzá az **Adatfelosztási** modult a folyamathoz az illesztőfelületen. Ez a modul az **adatátalakítás**alatt, a **minta és a felosztás** kategóriában található.

2. **Felosztási mód**: válasszon az alábbi módok közül, attól függően, hogy milyen típusú adattípussal rendelkezik, és hogyan szeretné osztani. Az egyes felosztási módok különböző beállításokkal rendelkeznek. Részletes útmutatást és példákat a következő témakörökben talál. 

    - **Sorok felosztása**: akkor használja ezt a beállítást, ha csak két részre szeretné osztani az adatterületet. Megadhatja, hogy az egyes felosztásokban hány adatmennyiség legyen elhelyezve, de alapértelmezés szerint az adatmennyiség 50-50.

        Az egyes csoportokban lévő sorok kijelölését, valamint rétegzett mintavételezést is használhat. Rétegzett mintavételezés esetén ki kell választania egy olyan adatoszlopot, amelynek az értékeit egyenlően kell kiosztani a két eredmény-adatkészletek között.  

    - **Reguláris kifejezés felosztása**  Akkor válassza ezt a lehetőséget, ha az adatkészletet el szeretné osztani egy érték egyetlen oszlopának tesztelésével.

        Ha például a hangulat elemzését végezheti el, akkor érdemes megkeresni egy adott terméknév jelenlétét egy szövegmezőben, majd az adatkészletet a célként megadott terméknév és a nélküli sorokra osztani.

    - **Relatív kifejezés felosztása**: akkor használja ezt a beállítást, ha egy feltételt egy Number oszlopra kíván alkalmazni. A szám lehet dátum/idő mező, az Age vagy a dollár összegét tartalmazó oszlop, vagy akár egy százalék is. Előfordulhat például, hogy az adatkészletet az elemek díjszabása alapján szeretné felosztani, a személyeket korcsoport szerint csoportosítani, vagy az adatokat egy naptári dátum alapján elkülönítve.

### <a name="split-rows"></a>Sorok felosztása
1.  Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz a csatolón, és kapcsolódjon a felosztani kívánt adatkészlethez.
  
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

## <a name="relative-expression-split"></a>Relatív kifejezés felosztása.

1. Adja hozzá az [Adatfelosztási](./split-data.md) modult a folyamathoz, és kapcsolja bemenetként a felosztani kívánt adatkészlethez.
  
2. A **felosztási mód**beállításnál válassza a **relatív kifejezés felosztása**elemet.
  
3. A **relációs kifejezés** szövegmezőbe írjon be egy olyan kifejezést, amely egy összehasonlítási műveletet hajt végre egyetlen oszlopon:


 - Numerikus oszlop:
    - Az oszlop numerikus adattípusok számát tartalmazza, beleértve a dátum/idő adattípusokat is.

    - A kifejezés legfeljebb egy oszlop nevét hivatkozhat.

    - A és a művelethez használja az jel karaktert (&), és használja a (z) vagy művelethez tartozó pipe karaktert (|).

    - A következő operátorok támogatottak: `<`, `>`, `<=`, `>=`, `==`, `!=`

    - @No__t_0 és `)` használatával nem csoportosíthatjuk a műveleteket.

 - Karakterlánc-oszlop: 
    - A következő operátorok támogatottak: `==`, `!=`



4. A folyamat futtatása.

    A kifejezés két sorból osztja el az adatkészletet: a feltételnek megfelelő értékeket tartalmazó sorok és az összes többi sor.

## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 