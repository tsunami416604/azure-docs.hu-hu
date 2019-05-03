---
title: 'Adatok optimalizálása: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Az adatok normalizálása modul használata az Azure Machine Learning szolgáltatás egy adatkészletből átalakítására *normalizálási*...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 95069bafa94770511c7ee40e82068960298fd6c5
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029445"
---
# <a name="normalize-data-module"></a>Adatok modul normalizálása

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul segítségével átalakíthatja egy adatkészletből *normalizálási*.

Normalizálási a machine Learning adat-előkészítés részeként gyakran alkalmazott technika. A normalizálási célja, hogy módosítsa az adatkészlet egy gyakori méretezési, anélkül, hogy az értékek vagy elveszett adatok tartományait torzító különbségek használandó számokat tartalmazó oszlop értékeit. Normalizálási is megfelelően modellezheti az adatokat az egyes algoritmusok szükség.

Tegyük fel, hogy a bemeneti adatkészlet tartalmaz egy oszlop értékei 0 és 1 közötti és a egy másik oszlop a 100 000 10 000 közötti értékeket. A nagy különbség az a *méretezési* számok problémákat okozhat, amikor megpróbál értékek kombinálja funkciók modellezés közben.

*Normalizálási* Ezzel elkerülheti ezeket a problémákat, melyek fenntartják a általános terjesztésű és arányok a forrásadatok, miközben gondoskodik a méretezési csoport alkalmazva a modellben használt összes numerikus oszlopok között szereplő új értékeket hozzon létre.

Ez a modul numerikus adatok átalakítása többféle lehetőséget kínál:

- Minden érték egy 0 és 1 módosíthatja méretezése, vagy az értékek átalakíthatja őket képviselő, ahelyett, hogy abszolút értékek ranks PERCENTILIS szerint.
- Normalizálási is alkalmazhat, csak egy oszlop, vagy több oszlop ugyanabból az adatkészletből.
- Ismételje meg a kísérletet, vagy a alkalmazni a normalizálási ugyanazokat a lépéseket a többi adattal kell, a lépések Mentés másként normalizálási átalakító, és alkalmazhatja azt egyéb adatkészletekhez, amelyek ugyanazzal a sémával rendelkezik.

> [!WARNING]
> Egyes algoritmusok van szükség, hogy adatokat a modell tanítása előtt lesz normalizálva. Más algoritmusok hajtsa végre a saját adatok méretezés, vagy normalizálási. Ezért ha egy gépi tanulási prediktív modell felépítése az algoritmus választja, mindenképpen tekintse át az adatok az algoritmus a betanítási adatok normalizálási alkalmazása előtt.

##  <a name="configure-normalize-data"></a>Konfigurálása adatok normalizálása

Ez a modul használatával egyszerre csak egy normalizálási módszert is alkalmazhat. Normalizálási ugyanezzel a módszerrel, ezért minden kiválasztott oszlopok vonatkozik. Normalizálási módszereket használja az egy második példányt **normalizálása adatok**.

1. Adja hozzá a **normalizálása adatok** modult a kísérletvászonra. Megtalálhatja a modul az Azure Machine Learning, a **adatátalakítás**, a a **méretezés és csökkentse** kategória.

2. Csatlakozzon egy adatkészletet, amely tartalmazza az összes legalább egy oszlopot.

3. Az Oszlopválasztó használatával válassza ki a numerikus oszlopok optimalizálására. Ha nem adja meg az egyes oszlopok alapértelmezés szerint **összes** numerikus típusú oszlop a bemeneti adatok megtalálhatók, és ugyanazon normalizálási folyamat lépéseit követve alkalmazott összes kijelölt oszlop. 

    Ez furcsa eredményekhez vezethet, hogy nem lesz normalizálva numerikus oszlopok belefoglalása! Mindig körültekintően ellenőrizze az oszlopok.

    Ha nem numerikus oszlop észlel, ellenőrizze annak ellenőrzéséhez, hogy az oszlop adattípusát támogatott numerikus típusú oszlop-metaadatot.

    > [!TIP]
    > Győződjön meg arról, hogy egy adott típusú oszlopok biztosított bemenetként, próbálkozzon a használatával a [Select Columns in Dataset](./select-columns-in-dataset.md) előtt modul **normalizálása adatok**.

4. **Használja a 0 értéket, ha be van jelölve a változatlan oszlopok**:  Válassza ezt a lehetőséget, ha bármely numerikus oszlopára tartalmaz egy állandó értéket. Ez biztosítja, hogy az ilyen oszlopokat normalizálási műveletek nem használja.

5. Az a **átalakítási metódus** legördülő menüben válassza ki a alkalmazni az összes kijelölt oszlop egy matematikai funkcióval. 
  
    - **Zscore**: Minden érték alakítja át a z-pontszám.
    
      Az oszlopban szereplő értékeket átalakításából származnak, használja a következő képletre:  
  
      ![z használatával normalizálási&#45;pontszámok](media/module/aml-normalization-z-score.png)
  
      Mean- és standard szórása arra az esetre vonatkoznak az egyes oszlopok külön-külön. Sokaság szórását szolgál.
  
    - **MinMax**: A min-max normalizáló a következőhöz költségráfordításokkal egyenes arányban, kiegészítve a [0,1] időköz szélességhez igazítva.
    
      A [0,1] időköz átméretezésekor végzi el időeltolású alapdokumentációjában értékeit, hogy a minimális értéke 0, és a az új maximális értéket (Ez a különbség az eredeti maximális és minimális értékeket).
      
      Az oszlopban szereplő értékeket átalakításából származnak, használja a következő képletre:  
  
      ![a minimális használatával normalizálási&#45;max függvény](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logisztikai**: Az oszlopban szereplő értékeket átalakításából származnak, használja a következő képletre:

      ![normalizálási logisztikai függvény által képletét](media/module/aml-normalization-logistic.png "AML_normalization logisztikai")  
  
    - **Lognormális**: Ez a beállítás lognormális méretezési csoport összes érték alakítja.
  
      Az oszlopban szereplő értékeket átalakításából származnak, használja a következő képletre:
  
      ![képlet log&#45;normális eloszlás](media/module/aml-normalization-lognormal.png "AML_normalization lognormális")
    
      Μ és σ az alábbiakban a terjesztés, számított tapasztalati az adatokból minden oszlophoz a legnagyobb valószínűséggel becslések szerint külön-külön paramétereit.  
  
    - **TanH**: Az összes lesznek átalakítva tangens hiperbolikuszát.
    
      Az oszlopban szereplő értékeket átalakításából származnak, használja a következő képletre:
    
      ![a tanh függvénnyel normalizálási](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Futtassa a kísérletet, vagy kattintson duplán a **normalizálása adatok** modul, és válassza ki **kijelölt futtatása**. 

## <a name="results"></a>Results (Eredmények)

A **normalizálása adatok** modul két kimenetek állít elő:

- Az átalakított értékek megtekintéséhez kattintson a jobb gombbal a modult, jelölje ki **átalakított adatkészlet**, és kattintson a **Visualize**.

    Alapértelmezés szerint az értékek helyen lesznek átalakítva. Ha meg szeretné hasonlítsa össze az átalakított értékeket az eredeti értékekre, használja a [oszlopok hozzáadása](./add-columns.md) modul újraegyesítésére az adatkészleteket, és megtekintheti az egymás melletti oszlopokat.

- Az átalakítást, hogy ugyanazzal a módszerrel normalizálási alkalmazhat egy másik hasonló adatkészlet mentéséhez kattintson a jobb gombbal a modult, válassza ki **transzformációs függvény**, és kattintson a **Mentés másként átalakító**.

    A mentett átalakítások, majd betöltheti a **alakítja át az** csoporthoz a bal oldali navigációs sávon, és alkalmazza azt ugyanazzal a sémával rendelkező adatkészlet használatával [. / Átalakítás alkalmazása](apply-transformation.md).  


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 