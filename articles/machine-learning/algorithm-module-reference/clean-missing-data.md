---
title: 'Hiányzó adatok tisztítása: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Hiányzó adatok tisztítása modult az Azure Machine Learningben a hiányzó értékek eltávolítására, cseréjére vagy kikövetkeztetésére.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 14c3bc968da9d398fbc14eda74378047cf28277b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477697"
---
# <a name="clean-missing-data-module"></a>Hiányzó adatok modul tisztítása

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal eltávolíthatja, kicserélheti vagy kikövetkeztetheti a hiányzó értékeket. 

Az adatszakértők gyakran ellenőrzik a hiányzó értékeket, majd különböző műveleteket hajtanak végre az adatok rögzítéséhez vagy új értékek beszúrásához. Az ilyen tisztítási műveletek célja, hogy megelőzzék a hiányzó adatok által okozott problémákat, amelyek a modell betanítása során merülhetnek fel. 

Ez a modul támogatja a hiányzó értékek "tisztítására" szolgáló műveletek több típusát, többek között a következőket:

+ A hiányzó értékek cseréje helyőrzővel, középértékkel vagy más értékkel
+ Hiányzó értékeket tartalmazó sorok és oszlopok teljes eltávolítása
+ Statisztikai módszereken alapuló kiferring értékek


A modul használata nem módosítja a forrásadatkészletet. Ehelyett létrehoz egy új adatkészletet a munkaterületen, amelyet a későbbi munkafolyamatban használhat. Az új, megtisztított adatkészletet is mentheti újrafelhasználásra.

Ez a modul a hiányzó értékek tisztításához használt transzformáció definícióját is kiadja. Ezt az átalakítást más, ugyanazzal a sémával rendelkező adatkészleteken is használhatja az [Átalakítás alkalmazása](./apply-transformation.md) modul használatával.  

## <a name="how-to-use-clean-missing-data"></a>A Hiányzó adatok tisztítása

Ez a modul lehetővé teszi a tisztítási művelet meghatározását. A tisztítási műveletet mentheti is, hogy később alkalmazhassa az új adatokra. A tisztítási folyamat létrehozásáról és mentéséről a következő szakaszokban található: 
 
+ [A hiányzó értékek cseréje](#replace-missing-values)
  
+ [Tisztítási átalakítás alkalmazása új adatokra](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> A hiányzó értékek kezelésére használt tisztítási módszer jelentősen befolyásolhatja az eredményeket. Azt javasoljuk, hogy kísérletezzen különböző módszerekkel. Vegye figyelembe egy adott módszer használatának indoklását és az eredmények minőségét.

### <a name="replace-missing-values"></a>Hiányzó értékek cseréje  

Minden alkalommal, amikor a [Hiányzó adatok tisztítása modult](./clean-missing-data.md) alkalmazza egy adathalmazra, ugyanazt a tisztítási műveletet alkalmazza a program az összes kijelölt oszlopra. Ezért ha különböző oszlopokat kell tisztítania különböző módszerekkel, használja a modul külön példányait.

1.  Adja hozzá a [Hiányzó adatok tisztítása modult](./clean-missing-data.md) a folyamathoz, és csatlakoztassa a hiányzó értékeket tartalmazó adatkészletet.  
  
2.  Az **Oszlopok tisztításához**válassza ki a módosítani kívánt hiányzó értékeket tartalmazó oszlopokat. Több oszlopot is választhat, de minden kijelölt oszlopban ugyanazt a cseremódszert kell használnia. Ezért általában külön kell tisztítani a karakterláncoszlopokat és a numerikus oszlopokat.

    Például a hiányzó értékek ellenőrzése az összes numerikus oszlopban:

    1. Válassza a **Hiányzó adatok tisztítása modult,** és kattintson a modul jobb oldali panelén található **Oszlop szerkesztése** elemre.

    3. A **Belefoglalás**csoportban válassza az **Oszloptípusok elemet** a legördülő listából, majd válassza a **Numerikus**lehetőséget. 
  
    A választott tisztítási vagy cseremódszernek a kiválasztás **minden** oszlopára vonatkoznia kell. Ha valamelyik oszlopban lévő adatok nem kompatibilisek a megadott művelettel, a modul hibát ad vissza, és leállítja a folyamatot.
  
3.  A **Minimális hiányzó értékarány**esetében adja meg a végrehajtandó művelethez szükséges minimális hiányzó értékek számát.  
  
    Ezzel a beállítással a **Maximális hiányzó értékaránynal** kombinálva határozhatja meg, hogy milyen feltételek mellett hajtson végre tisztítási műveletet az adatkészleten. Ha túl sok vagy túl kevés sor hiányzik az értékből, a művelet nem hajtható végre. 
  
    A beírt szám a hiányzó értékek és az oszlop összes értékének **arányát** jelöli. Alapértelmezés szerint a **Minimális hiányzó értékarány** tulajdonság értéke 0. Ez azt jelenti, hogy a hiányzó értékek akkor is törlődnek, ha csak egy hiányzik érték. 

    > [!WARNING]
    > A megadott művelet alkalmazásához ezt a feltételt minden egyes oszlopnak teljesítenie kell. Tegyük fel például, hogy három oszlopot választott ki, majd a hiányzó értékek minimális arányát 0,2-re (20%) állította be, de valójában csak egy oszlopban van 20% hiányzó érték. Ebben az esetben a tisztítási művelet csak a 20% -ot hiányzó értékekkel rendelkező oszlopra vonatkozna. Ezért a többi oszlop változatlan marad.
    > 
    > Ha bármilyen kétsége van azzal kapcsolatban, hogy a hiányzó értékek módosultak-e, válassza a **Hiányzó értékjelző oszlop létrehozása lehetőséget.** A program egy oszlopot fűz az adatkészlethez annak jelzésére, hogy az egyes oszlopok megfelelnek-e a minimális és maximális tartományokra meghatározott feltételeknek.  
  
4. A **Hiányzó érték arány maximális mezőben**adja meg a végrehajtandó művelethez megjelenítendő hiányzó értékek maximális számát.   
  
    Előfordulhat például, hogy csak akkor szeretné végrehajtani a hiányzó értékhelyettesítést, ha a sorok 30%-a vagy kevesebb esetben tartalmaz hiányzó értékeket, de az értékeket úgy kell hagyni, ahogy van, ha a sorok több mint 30%-a hiányzik.  
  
    A számot úgy kell megadni, mint a hiányzó értékek és az oszlop összes értékének arányát. Alapértelmezés szerint a Hiányzó érték maximális aránya 1.Default, the **Maximum missing value ratio** is set to 1. Ez azt jelenti, hogy a hiányzó értékek akkor is törlődnek, ha az oszlopban lévő értékek 100%-a hiányzik.  
  
   
  
5. **Tisztítási módban**az alábbi lehetőségek közül választhat a hiányzó értékek cseréjére vagy eltávolítására:  
  
  
    + **Egyéni helyettesítési érték:** Ezzel a beállítással olyan helyőrző értéket (például 0 vagy NA) adhat meg, amely az összes hiányzó értékre vonatkozik. A csereként megadott értéknek kompatibilisnek kell lennie az oszlop adattípusával.
  
    + **Csere átlaggal**: Kiszámítja az oszlop átlagát, és az átlagot használja az oszlop ban hiányzó értékek helyettesítésére.  
  
        Csak egész, dupla vagy logikai adattípusokkal rendelkező oszlopokra vonatkozik.  
  
    + **Csere mediánértékkel:** Kiszámítja az oszlop mediánértékét, és a medián értéket használja az oszlopban hiányzó értékek helyettesítésére.  
  
        Csak egész vagy dupla adattípust tartalmazó oszlopokra vonatkozik. 
  
    + **Csere móddal:** Kiszámítja az oszlop módját, és az oszlop minden hiányzó értékének helyettesítő értékeként a módot használja.  
  
        Egész, dupla, logikai vagy kategorikus adattípusokkal rendelkező oszlopokra vonatkozik. 
  
    + **Teljes sor eltávolítása**: Teljesen eltávolítja az adatkészlet egy vagy több hiányzó értékével rendelkező sorokat. Ez akkor hasznos, ha a hiányzó érték véletlenszerűen hiányzónak tekinthető.  
  
    + **Teljes oszlop eltávolítása:** Teljesen eltávolítja az adatkészlet egy vagy több hiányzó értékével rendelkező oszlopokat.  
  
    
  
6. A **Csere érték lehetőség** akkor érhető el, ha az **Egyéni helyettesítési érték lehetőséget**választotta. Írjon be egy új értéket, amelyet az oszlopban lévő összes hiányzó érték helyettesítő értékeként használ.  
  
    Ne feledje, hogy ezt a beállítást csak olyan oszlopokban használhatja, amelyek egész, dupla, logikai vagy karakterláncú.
  
7. **Hiányzó értékjelző oszlop létrehozása:** Akkor válassza ezt a lehetőséget, ha azt szeretné, hogy az oszlopban szereplő értékek megfeleljenek-e a hiányzó értéktisztítás feltételeinek. Ez a beállítás különösen akkor hasznos, ha új tisztítási műveletet állít be, és biztos szeretne lenni abban, hogy az a tervezettnek megfelel.
  
8. Küldje el a folyamatot.

### <a name="results"></a>Results (Eredmények)

A modul két kimenetet ad vissza:  

-   **Tisztított adatkészlet**: A kijelölt oszlopokból álló adatkészlet, amelynek hiányzó értékei a megadott módon vannak kezelve, egy jelzőoszloppal együtt, ha ezt a beállítást választotta.  

    A tisztításra nem kiválasztott oszlopok is "áthaladnak".  
  
-  **Tisztítási átalakítás**: A tisztításhoz használt adatátalakítás, amely a munkaterületre menthető és később új adatokra alkalmazható.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Mentett tisztítási művelet alkalmazása új adatokra  

Ha gyakran kell megismételnie a tisztítási műveleteket, javasoljuk, hogy az adattisztításreceptjét *átalakításként mentse,* hogy ugyanazt az adatkészletet újra felhasználhassa. A tisztítási átalakítás mentése különösen akkor hasznos, ha gyakran újra kell importálnia, majd meg kell tisztítania az azonos sémával rendelkező adatokat.  
      
1.  Adja hozzá az [Átalakítás alkalmazása](./apply-transformation.md) modult a folyamathoz.  
  
2.  Adja hozzá a tisztára szeretne menni, és csatlakoztassa az adatkészletet a jobb oldali bemeneti porthoz.  
  
3.  Bontsa ki az **Átalakítások** csoportot a tervező bal oldali ablaktáblájában. Keresse meg a mentett átalakítást, és húzza a folyamatba.  

4.  Csatlakoztassa a mentett átalakítást az [Átalakítás alkalmazása bal](./apply-transformation.md)oldali bemeneti portjához. 

    Mentett átalakítás alkalmazásakor nem választhatja ki azokat az oszlopokat, amelyekre az átalakítás vonatkozik. Ennek az az oka, hogy az átalakítás már definiálva van, és automatikusan vonatkozik az eredeti műveletben megadott oszlopokra.

    Tegyük fel azonban, hogy a numerikus oszlopok egy részhalmazán hozott létre átalakítást. Ezt az átalakítást vegyes oszloptípusok adatkészletére is alkalmazhatja hiba kiváltása nélkül, mert a hiányzó értékek csak a megfelelő numerikus oszlopokban módosulnak.

6.  Küldje el a folyamatot.  

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 