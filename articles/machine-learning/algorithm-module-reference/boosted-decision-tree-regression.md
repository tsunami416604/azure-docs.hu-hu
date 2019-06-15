---
title: 'Gyorsított regressziós döntési fa: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Útmutató a súlyozott regressziós döntési fa modul használata az Azure Machine Learning szolgáltatás használatával a gyorsított regressziós fák ruhaegyüttes létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67e54f10074ee566ce974dbd27485904bfe0a653
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411550"
---
# <a name="boosted-decision-tree-regression-module"></a>Gyorsított regressziós döntési fa modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával a gyorsított regressziós fák ruhaegyüttes létrehozásához használja. *Kiemelési* azt jelenti, hogy minden egyes fa előzetes fák függ. Az algoritmus által a fák, amely azt a fennmaradó igyekeznek tanul. Így az a döntési fa ensemble kiemelési általában néhány kisebb érvényességének kis kockázattal rendelkező pontosságának növelése érdekében.  
  
Ez a módszer regressziós egy felügyelt tanítás módszer, és ezért igényel egy *adatkészlet feliratú*. A címke oszlop numerikus értékeket kell tartalmaznia.  

> [!NOTE]
> Ez a modul használata csak numerikus változókat használó adatkészletekkel.  

Miután meghatározta a modell, betanítás, a a [Train Model](./train-model.md).

> [!TIP]
> Szeretne többet megtudni a létrehozott fák? Miután a modell rendelkezik betanítva, kattintson a jobb gombbal a kimenetét a [tanítási modell](./train-model.md) modul, és válassza **Visualize** a fa minden egyes ismétléskor létrehozott megtekintéséhez. Az egyes fa elágazást feltárásához, és minden egyes csomópont esetében a szabályokat.  
  
## <a name="more-about-boosted-regression-trees"></a>További információ a gyorsított regressziós fák  

Kiemelési módszerek egyike, több klasszikus létrehozásához ensemble modellek zsákolási, véletlenszerű erdők és így tovább.  Az Azure Machine Learning a gyorsított döntési fák algoritmus kiemelési ADATKÖZPONT színátmenet hatékony megvalósítását használja. Machine learning-során a regressziós problémák átmenetes kiemelési. Többlépcsős módon, egy előre meghatározott adatveszteség funkció segítségével a hiba az egyes lépések mérni, és javítsa ki azt a következő regressziós fákban épít azt. Így az előrejelzési modell ténylegesen gyengébb prediktív modellek ruhaegyüttes.  
  
Regressziós problémákat kiemelési összeállít egy sorozat többlépcsős módon fák, majd kiválasztja az optimális fa egy tetszőleges differentiable adatveszteség függvény használatával.  
  
További információkért tanulmányozza a következő cikkeket:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    A Wikipedia-cikk a színátmenet kiemelési biztosít bizonyos tapasztalattal a gyorsított fa. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: Az, hogy LambdaMART LambdaRank RankNet: Áttekintése. J.C. szerint Burges.

Kiemelési metódus színátmenet is használható a besorolási problémákat a megfelelő adatveszteség funkcióval rendelkező regressziós csökkentésével. A gyorsított fa megvalósítása fájlosztályozási feladatokhoz kapcsolatos további információkért lásd: [két osztályú súlyozott döntési fa](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Súlyozott regressziós döntési fa konfigurálása

1.  Adja hozzá a **súlyozott döntési fa** modult a kísérletvászonra. Ez a modul alatt található **Machine Learning**, **inicializálása**alatt a **regressziós** kategória. 
  
2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modell, és adjon meg egy meghatározott készletének argumentumokkal értékek.  
   
  
3. **Maximális számát egy fa hagyja**: Adja meg, hogy bármely fában hozható létre a terminál (levelek) csomópontok maximális számát.  

    Ez az érték növelésével, potenciálisan a fa méretének növeléséhez és a nagyobb pontosság kockázatára overfitting, és már képzési az idő lekérése.  

4. **Minták levél csomópontok minimális száma**: Adja meg a terminál csomópont (levél) létrehozásához szükséges egy fa esetek minimális számát.

    Ez az érték növelésével növeli az új szabályok létrehozása a küszöbértéket. Az alapértelmezett értéke 1, például egyetlen esetet okozhat egy új szabályt létrehozni. Az érték 5-re növeli, ha a betanítási adatok legalább 5 esetek ugyanazon feltételeknek eleget tevő tartalmaznia kellene.

5. **Tanulási ráta**: Írjon be egy 0 és 1, amely meghatározza a tanulás során lépésköz mérete közötti számot. Megfelelő tanulási rátát határozza meg, hogyan gyors vagy lassú a learner az ideális megoldás a szerveződik át. Ha a lépés mérete túl nagy, akkor előfordulhat, hogy túllépése esetén a az optimális megoldást. Ha a lépés mérete túl kicsi, képzési hosszabb időt vehet igénybe összegyűjti az a legjobb megoldás.

6. **Fák összeállított száma**: Adja meg, hozhat létre a ensemble döntési fák teljes száma. További döntési fák létrehozásával lefedettségét esetlegesen kap, de képzési idő növeli.

    Ez az érték vezérli jelenik meg, amikor megjelenítik a betanított modell fák számát is. Ha azt szeretné, vagy egy tlen fa nyomtatása, az érték 1; megadható azonban csak egy fa jön létre (a kezdeti paraméterkészlet fa), és nincs további ismétléseinek el kell végezni.

7. **Véletlenszerű szám kezdőérték**: Írja be a véletlenszerű seed értéket használata nem kötelező nem negatív egész szám. Adja meg az egyik kezdőérték biztosítja, hogy az megismételhetőség ugyanazokhoz az adatokhoz és paraméterekkel rendelkező futtatás során.

    Alapértelmezés szerint a véletlenszám-generálás kezdőértéke értéke 0, ami azt jelenti, hogy a kezdeti alapérték kérhető le a rendszeróra.
  
8. **Lehetővé teszi az ismeretlen kategorikus szintek**: Válassza ki ezt a beállítást, a képzés és érvényesítési csoportok ismeretlen értékek csoport létrehozása. Törölje ezt a beállítást, ha a modell elfogadhatja csak az abban található a betanítási adatok értékeket. A modell ismert értékek kevésbé pontos lehet, de azt is lehetővé teszi új (ismeretlen) értékek jobb javaslatok érdekében.

9. Betanítási adatkészletet, és a egy képzési modulok hozzáadása:

    - Ha **létrehozási trainer módban** beállítást **egyetlen paramétert**, használja a [tanítási modell](train-model.md) modul.  
  
    

10. Futtassa a kísérletet.  
  
### <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A fa minden egyes ismétléskor létrehozott megtekintéséhez kattintson a jobb gombbal a kimenetét a [tanítási modell](train-model.md) modul, és válassza ki **Visualize**.
  
     Kattintson az elágazást feltárásához és a szabályokat az egyes csomópontok-fákban.  

+ A modell használatának pontozó, csatlakoztathatja az [Score Model](./score-model.md), új bemeneti példák értékeinek.

+ Szeretné menteni a betanított modell pillanatképét, kattintson a jobb gombbal a **Trained model** a képzési modult, majd válassza a kimeneti **Mentés másként**. A kísérlet futtatásának egymást követő nem frissül a betanított modell menteni kívánt példányát.

## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 