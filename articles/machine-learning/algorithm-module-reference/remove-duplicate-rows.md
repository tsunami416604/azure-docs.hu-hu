---
title: 'Távolítsa el az ismétlődő sorok: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató az ismétlődő sorok eltávolítása modul használata az Azure Machine Learning szolgáltatás eltávolítja a potenciális ismétlődő egy adatkészletből.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: dce90d911085c1f7330a2e0952bb9576c1d765fa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029295"
---
# <a name="remove-duplicate-rows-module"></a>Távolítsa el az ismétlődő sorok modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával lehetséges ismétlődések eltávolítása egy adatkészletből.

Tegyük fel, hogy az adatok a következőhöz hasonló, és a betegek több rekordot jelöli. 

| PatientID | Monogram| Nem|Kor|Beléptetett|
|----|----|----|----|----|
|1|F.M.| M| 53| Jan|
|2| F.A.M.| M| 53| Jan|
|3| F.A.M.| M| 24| Jan|
|3| F.M.| M| 24| Február|
|4| F.M.| M| 23| Február|
| | F.M.| M| 23| |
|5| F.A.M.| M| 53| |
|6| F.A.M.| M| NaN| |
|7| F.A.M.| M| NaN| |

Természetesen az ebben a példában az esetlegesen ismétlődő adatok több oszlopot tartalmaz. Csatlakoznak-e ismétlődések ténylegesen attól függ, hogy az adatok kapcsolatos ismereteit. 

+ Például előfordulhat, hogy biztos lehet, hogy sok betegek rendelkezik ugyanazzal a névvel. Nem lenne letiltjuk a duplikált elemek használatával bármilyen nevet tartalmazó oszlopot, csak a **azonosító** oszlop. Ezzel a módszerrel csak az ismétlődő azonosító értéket tartalmazó sorok ki van szűrve, függetlenül a betegek rendelkeznek-e ugyanazzal a névvel vagy sem.

+ Azt is megteheti dönthet, hogy engedélyezi az ismétlődő elemeket az azonosító mezőben, és keresse meg az egyedi bejegyzések, például utónév, utolsó nevét, kor, és a nemek egyéb kombinációjáig terjedhet fájlokat használja.  

Állítsa be a feltételeket, akár egy sor nem ismétlődő vagy nem, csak egy oszlop vagy egy a használni kívánt oszlopokat adjon meg **kulcsok**. Két sort számítanak ismétlődések csak akkor, ha szereplő értékek **összes** Kulcsoszlopok megegyeznek. Ha bármely sor nullértékkel rendelkezik, a hiányzó értékeket **kulcsok**, nem minősülnek ismétlődő sorok. Például, ha a nemek és a Korszűrő vannak beállítva, mivel fenti 6 és 7 sort a táblában a kulcsok nem ismétlődő sorok megadott van hiányzó értékük korában.

A modul futtatásakor jelölt adatkészletet hoz létre, és nincsenek ismétlődések között megadott oszlopok csoportját tartalmazó sorok készletét adja vissza.

> [!IMPORTANT]
> A forrás adatkészlet nem változtak meg; Ez a modul egy új adatkészlet kizárandó ismétlődéseket, a megadott feltételek alapján szűrt hoz létre.

## <a name="how-to-use-remove-duplicate-rows"></a>Hogyan használható az ismétlődő sorok eltávolítása

1. Adja hozzá a modul a kísérletben. Annak a **ismétlődő sorok eltávolítása** alatt modul **adatátalakítás**, **adatkezelési**.  

2. Csatlakozzon, amely ismétlődő sorok ellenőrizni kívánja az adatkészletet.

3. A a **tulajdonságok** ablaktáblán, a **oszlop kiválasztása szűrőkifejezés kulcs**, kattintson a **Oszlopválasztás indítása**, válassza az ismétlődések azonosításához használni kívánt oszlopokat.

    Ebben a környezetben **kulcs** jelenti azt, hogy egyedi azonosítója. Az Oszlopválasztó használatával választhat, minden oszlop van megjelölve **kulcs oszlopok**. Az összes ki nem jelölt oszlopok nem kulcs oszlop minősülnek. Oszlopok kulcsként kiválasztott kombinációja határozza meg a rekordok egyediségét. (Úgy is, mint egy SQL-utasítást, amely több equalities illesztéseket.)

    Példák:

    + "Szeretnék arról, hogy egyedi azonosítók": Válassza ki az azonosító oszlop csak.
    + "Szeretném, hogy az Utónév, Vezetéknév és azonosító kombinációja egyedi": Válassza ki az összes három oszlopot.

4. Használja a **első ismétlődő sorok megőrzése** jelölőnégyzet bejelölésével jelezze, amelyben vissza, ha a duplikátumok találhatók sorok:

    + Ha kiválasztva, az első sort adja vissza, és mások elveti. 
    + Ha a beállítás jelölését, az utolsó ismétlődő sor maradjanak az eredményeket, és mások elvesznek. 

5. Futtassa a kísérletet.

6. Tekintse át az eredményeket, kattintson a jobb gombbal a modult, jelölje ki **eredmények adatkészlet**, és kattintson a **Visualize**. 

> [!TIP]
> Ha az eredmények nehezen érthető, vagy ha azt szeretné, az egyes oszlopok kizárása a figyelmet, eltávolíthatja az oszlopok a [Select Columns in Dataset](./select-columns-in-dataset.md) modul.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 