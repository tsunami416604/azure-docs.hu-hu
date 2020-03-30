---
title: 'Kétosztályos logisztikai regresszió: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a kétosztályos logisztikai regressziós modul t az Azure Machine Learning egy logisztikai regressziós modell, amely két (és csak két) eredmények előrejelzésére használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 7eb1ad00c3c947c3ed6d4ca450bddc0956a08d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455825"
---
# <a name="two-class-logistic-regression-module"></a>Kétosztályos logisztikai regressziós modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy logisztikai regressziós modellt, amely két (és csak két) kimenetel előrejelzésére használható. 

A logisztikai regresszió egy jól ismert statisztikai technika, amelyet sokféle probléma modellezésére használnak. Ez az algoritmus felügyelt *tanulási* módszer;  ezért meg kell adnia egy adatkészletet, amely már tartalmazza a modell betanításához.  

### <a name="about-logistic-regression"></a>A logisztikai regresszióról  

A logisztikai regresszió egy jól ismert módszer a statisztikákban, amely az eredmény valószínűségének előrejelzésére szolgál, és különösen népszerű a besorolási feladatokban. Az algoritmus előre jelzi egy esemény előfordulásának valószínűségét azáltal, hogy adatokat illeszt egy logisztikai függvényhez.
  
Ebben a modulban a besorolási algoritmus dichotomous vagy bináris változókra van optimalizálva. ha több eredményt kell osztályoznia, használja a [többosztályos logisztikai regressziós modult.](./multiclass-logistic-regression.md)

##  <a name="how-to-configure"></a>Hogyan kell beállítani  

A modell betanításához meg kell adnia egy adatkészletet, amely egy címkét vagy osztályoszlopot tartalmaz. Mivel ez a modul kétosztályos problémákra szolgál, a címkének vagy az osztályoszlopnak pontosan két értéket kell tartalmaznia. 

A címkeoszlop például lehet [Szavazott] az "Igen" vagy "Nem" lehetséges értékekkel. Vagy lehet [Hitelkockázat], a "Magas" vagy "Alacsony" lehetséges értékekkel. 
  
1.  Adja hozzá a **kétosztályos logisztikai regressziós** modult a folyamathoz.  
  
2.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.  
  
    -   **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumként megadhat egy adott értékkészletet.  

    -   **Paramétertartomány**: Ha nem biztos a legjobb paraméterekben, megtalálhatja az optimális paramétereket a [Tune Model Hyperparameters](tune-model-hyperparameters.md) modul segítségével. Ön megad néhány értéktartományt, és a tréner a beállítások több kombinációján keresztül iterálja a legjobb eredményt adó értékek kombinációját.
  
3.  Az **optimalizálási tűréshez**adja meg a modell optimalizálásakor használandó küszöbértéket. Ha az iterációk közötti javulás a megadott küszöbérték alá esik, az algoritmus úgy tekintendő, hogy egy megoldáson konvergált, és a betanítás leáll.  
  
4.  **Az L1 regularizációs súly** és az **L2 regularizációs súly**mezőben írja be az L1 és L2 regularizációs paraméterekhez használandó értéket. Mindkettőhez nem nulla érték ajánlott.  
     *A szabályosítás* olyan módszer, amely megakadályozza a túlszerelést a szélsőséges együtthatóértékű modellek szankcionálásával. A szabályosítás úgy működik, hogy az együttható értékekhez kapcsolódó büntetést hozzáadja a hipotézis hibájához. Így egy szélsőséges együtthatóértékű pontos modellt jobban büntetnének, de egy kevésbé pontos, konzervatívabb értékekkel rendelkező modellt kevesebbbüntetéssel sújtanának.  
  
     L1 és L2 regularization különböző hatások és felhasználások.  
  
    -   Az L1 ritka modellekre alkalmazható, ami akkor hasznos, ha nagy dimenziós adatokkal dolgozik.  
  
    -   Ezzel szemben az L2-es regisztálás előnyösebb a nem ritka adatok esetében.  
  
     Ez az algoritmus támogatja az L1 és L2 regularizációs <code>x = L1</code> <code>y = L2</code>értékek <code>ax + by = c</code> lineáris kombinációját: azaz ha és a , meghatározza a regisztizálási kifejezések lineáris tartományát.  
  
    > [!NOTE]
    >  Szeretne többet megtudni az L1 és L2 szabályosításról? A következő cikk bemutatja, hogy az L1 és L2 legalizálása hogyan különbözik, és hogyan befolyásolják a modellilleszkedést, a logisztikai regressziós és neurális hálózati modellek kódmintáival: [L1 és L2 A gépi tanulás legalizálása](https://msdn.microsoft.com/magazine/dn904675.aspx)  
    >
    > Az L1 és L2 kifejezések különböző lineáris kombinációit dolgozták ki a logisztikai regressziós modellekhez: például [a rugalmas hálós regularizációt.](https://wikipedia.org/wiki/Elastic_net_regularization) Javasoljuk, hogy hivatkozzon ezekre a kombinációkra egy olyan lineáris kombináció definiálásához, amely hatékony a modellben.
      
5.  Az **L-BFGS memóriaméretéhez**adja meg az *L-BFGS* optimalizálásához használandó memória mennyiségét.  
  
     L-BFGS jelentése "korlátozott memória Broyden-Fletcher-Goldfarb-Shanno". Ez egy optimalizálási algoritmus, amely népszerű a paraméter becslés. Ez a paraméter a következő lépés kiszámításához tárolandó korábbi beosztások és színátmenetek számát jelzi.  
  
     Ez az optimalizálási paraméter korlátozza a következő lépés és irány kiszámításához használt memória mennyiségét. Ha kevesebb memóriát ad meg, az edzés gyorsabb, de kevésbé pontos.  
  
6.  A **Véletlenszám-mag**mezőbe írjon be egy egész értéket. A kezdőérték meghatározása fontos, ha azt szeretné, hogy az eredmények ugyanazon folyamat több futtatása esetén is reprodukálhatók legyenek.  
  
  
8. Adjon hozzá egy címkézett adatkészletet a folyamathoz, és csatlakoztassa az egyik [betanítási modult.](module-reference.md)  
  
    -   Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](./train-model.md) modult.  
  
9. Küldje el a folyamatot.  
  
## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:
 
  
+ Az új adatok előrejelzéséhez használja a betanított modellt és az új adatokat a Score Model modul [bemeneteként.](./score-model.md) 


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 