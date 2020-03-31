---
title: 'Ismétlődő sorok eltávolítása: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Duplikált sorok eltávolítása modult az Azure Machine Learningben a lehetséges ismétlődések eltávolításához egy adatkészletből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/22/2019
ms.openlocfilehash: 490d3305abcbcd906a0f727d736db8cab7e4287e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456021"
---
# <a name="remove-duplicate-rows-module"></a>Ismétlődő sorok eltávolítása modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal eltávolíthatja a lehetséges ismétlődéseket egy adatkészletből.

Tegyük fel például, hogy az adatok a következőképpen néznek ki, és több rekordot jelölnek a betegek számára. 

| Betegazonosító | Monogram| Nem|Kor|Elismerte|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Feb.|
|4| F.M.| M| 23| Feb.|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Nyilvánvaló, hogy ebben a példában több oszlop is található, amelyek potenciálisan ismétlődő adatokat tartalmaznak. Az, hogy valóban ismétlődnek-e, az adatok ismeretétől függ. 

+ Például, lehet, hogy tudja, hogy sok beteg ugyanazt a nevet. Az ismétlődéseket nem szüntetné meg névoszlopok, csak az **Azonosító** oszlop használatával. Így csak a duplikált azonosítóértékekkel rendelkező sorokat szűri ki a rendszer, függetlenül attól, hogy a betegeknek ugyanaz a nevük vagy sem.

+ Azt is megteheti, hogy engedélyezi az ismétlődéseket az Azonosító mezőben, és a fájlok más kombinációjával egyedi rekordokat keres, például keresztnevet, vezetéknevet, kort és nemet.  

Ha meg szeretné határozni, hogy egy sor ismétlődik-e vagy sem, meg kell adnia egy oszlopot vagy oszlopkészletet, amelyet kulcsként kell **használnia.** Két sor csak akkor számít ismétlődésnek, ha az **összes** kulcsoszlop értékei egyenlőek. Ha bármelyik sorból hiányzik a **kulcs**értéke, azok nem számítanak ismétlődő soroknak. Ha például a fenti táblázatban a Nem és az Életkor billentyűként van beállítva, a 6.

A modul futtatásakor létrehoz egy jelölt adatkészletet, és olyan sorokat ad vissza, amelyek nem tartalmaznak ismétlődéseket a megadott oszlopok között.

> [!IMPORTANT]
> A forrásadatkészlet nem módosul, a forrásadatkészlet nem módosul. ez a modul létrehoz egy új adatkészletet, amely a megadott feltételek alapján szűrve van az ismétlődések kizárása érdekében.

## <a name="how-to-use-remove-duplicate-rows"></a>Az ismétlődő sorok eltávolítása

1. Adja hozzá a modult a folyamathoz. Az Ismétlődő **sorok eltávolítása** modul az **Adatátalakítás**, **Manipuláció**területen található.  

2. Kapcsolja össze azt az adatkészletet, amelynek ismétlődő sorait ellenőrizni szeretné.

3. A **Tulajdonságok** ablaktábla **Kulcsoszlop kijelölési szűrőkifejezése**területén kattintson az **Oszlopkijelölő gombra**az ismétlődések azonosításához használni kívánt oszlopok kiválasztásához.

    Ebben az összefüggésben a **kulcs** nem jelent egyedi azonosítót. Az Oszlopkijelölő vel kiválasztott összes oszlop **kulcsoszlopként**van megjelölve. Minden ki nem jelölt oszlop nem kulcsoszlopnak minősül. A billentyűként kijelölt oszlopok kombinációja határozza meg a rekordok egyediségét. (Gondolj rá úgy, mint egy SQL utasításra, amely több equalities illesztést használ.)

    Példák:

    + "Biztosítani szeretném, hogy az azonosítók egyediek legyenek": Csak az azonosító oszlopot válassza ki.
    + "Biztosítani szeretném, hogy a keresztnév, a vezetéknév és az azonosító kombinációja egyedi legyen": Mindhárom oszlop kijelölése.

4. Az **Első ismétlődő sor megőrzése** jelölőnégyzet segítségével jelezheti, hogy melyik sort kell visszaadni, ha ismétlődéseket talál:

    + Ha be van jelölve, a program visszaadja az első sort, a többi ta- 
    + Ha nem jelöli be ezt a beállítást, az utolsó ismétlődő sor az eredmények között marad, a többi pedig elvetésre kerül. 

5. Küldje el a folyamatot.

6. Az eredmények áttekintéséhez kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés parancsot.** 

> [!TIP]
> Ha az eredmények nehezen érthetők, vagy ha néhány oszlopot ki szeretne zárni a megfontolásból, az oszlopokat eltávolíthatja az [Adatkészlet oszlopok kijelölése](./select-columns-in-dataset.md) modullal.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 