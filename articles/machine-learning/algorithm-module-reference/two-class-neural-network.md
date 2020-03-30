---
title: 'Kétosztályos neurális hálózat: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a kétosztályos neurális hálózati modul az Azure Machine Learning neurális hálózati modell, amely használható előre jelezni a cél, amely csak két értékkel rendelkezik.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a5cf2b210781d43ddd1a7aa87736a53df222cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477391"
---
# <a name="two-class-neural-network-module"></a>Kétosztályos neurális hálózati modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy neurális hálózati modellt, amely csak két értékkel rendelkezik egy olyan cél előrejelzésére használható.

Neurális hálózatok használatával történő besorolás felügyelt tanulási módszer, ezért egy *címkézett adatkészletet*igényel, amely tartalmaz egy címkeoszlopot. Ezt a neurális hálózati modellt például arról, hogy egy beteg rendelkezik-e egy bizonyos betegséggel, vagy hogy egy gép valószínűleg meghibásodik-e egy adott időalatt, használhatja-e a bináris eredményeket, például azt, hogy egy betegrendelkezik-e egy bizonyos betegséggel, vagy hogy egy gép valószínűleg meghibásodik-e egy adott időalatt.  

Miután definiálta a modellt, tanítsa be egy címkézett adatkészletet és a modellt a [betanítási modell bemeneteként.](./train-model.md) A betanított modell ezután új bemenetek értékeinek előrejelzésére használható.

### <a name="more-about-neural-networks"></a>További információk a neurális hálózatokról

A neurális hálózat összekapcsolt rétegek halmaza. A bemenetek az első réteg, és egy kimeneti réteghez egy súlyozott élekből és csomópontokból álló aciklikus grafikonon keresztül kapcsolódnak.

A bemeneti és a kimeneti rétegek között több rejtett réteget is beilleszthet. A legtöbb prediktív feladat egyszerűen elvégezhető egyetlen vagy néhány rejtett réteggel. Azonban a legújabb kutatások kimutatták, hogy a mély neurális hálózatok (DNN) sok réteget lehet hatékony összetett feladatok, mint a kép-vagy beszédfelismerés. Az egymást követő rétegek a szemantikai mélység növekvő szintjének modellezésére szolgálnak.

A bemenetek és kimenetek közötti kapcsolat a neurális hálózat betanítása a bemeneti adatok. A diagram iránya a bemenetektől a rejtett rétegen keresztül a kimeneti rétegig halad. A réteg összes csomópontját a súlyozott élek kötik össze a következő réteg csomópontjaival.

A hálózat kimenetének kiszámításához egy adott bemenethez egy értéket számít a rendszer a rejtett rétegek és a kimeneti réteg minden csomópontjára. Az értéket az előző réteg csomópontjai értékeinek súlyozott összegének kiszámításával állítja be. Ezután egy aktiválási funkciót alkalmaznak az adott súlyozott összegre.
  
## <a name="how-to-configure"></a>Hogyan kell beállítani

1.  Adja hozzá a **kétosztályos neurális hálózati** modult a folyamathoz. Ez a modul a **Machine Learning**, **Initialize**, a **Besorolás** kategóriában található.  
  
2.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.  
  
    -   **Egyetlen paraméter:** Válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    -   **Paramétertartomány**: Ha nem biztos a legjobb paraméterekben, megtalálhatja az optimális paramétereket a [Tune Model Hyperparameters](tune-model-hyperparameters.md) modul segítségével. Ön megad néhány értéktartományt, és a tréner a beállítások több kombinációján keresztül iterálja a legjobb eredményt adó értékek kombinációját.  

3.  A **Rejtett réteg specifikációja mezőben**válassza ki a létrehozandó hálózati architektúra típusát.  
  
    -   **Teljesen csatlakoztatott eset:** Az alapértelmezett neurális hálózati architektúrát használja, amely et kétosztályú neurális hálózatokhoz határozták meg az alábbiak szerint:
  
        -   Van egy rejtett rétege.
  
        -   A kimeneti réteg teljes mértékben a rejtett réteghez csatlakozik, a rejtett réteg pedig teljes mértékben a beviteli réteghez.
  
        -   A bemeneti rétegben lévő csomópontok száma megegyezik a betanítási adatokban lévő szolgáltatások számával.
  
        -   A rejtett rétegben lévő csomópontok számát a felhasználó állítja be. Az alapértelmezett érték 100.
  
        -   A csomópontok száma megegyezik az osztályok számával. Kétosztályos neurális hálózat esetén ez azt jelenti, hogy minden bemenetnek le kell képeznie a kimeneti réteg két csomópontjának egyikét.

5.  A **tanulási sebesség,** adja meg a mérete a megtett lépés minden iteráció, a javítás előtt. A tanulási arány nagyobb értéke a modell gyorsabb konvergenciáját okozhatja, de túlléphet a helyi minimumokon.

6.  A **tanulási ismétlések száma mezőben**adja meg, hogy az algoritmus hányszor dolgozza fel a betanítási eseteket.

7.  **A kezdeti tanulási súlyok átmérője**esetén adja meg a csomópont súlyait a tanulási folyamat kezdetén.

8.  **A Momentum**esetében adja meg a korábbi ismétlések ből származó csomópontokra való tanulás során alkalmazandó súlyt.  

10. Válassza a **Véletlen sorrendű példák** lehetőséget az ismétlések közötti esetek véletlen ek keveréséhez. Ha nem jelöli be ezt a beállítást, az esetek feldolgozása pontosan ugyanabban a sorrendben történik minden alkalommal, amikor futtatja a folyamatot.
  
11. A **véletlenszámkezdőmag**mezőbe írja be a vetőmagként használandó értéket.
  
     A kezdőérték megadása akkor hasznos, ha biztosítani szeretné az ismételhetőséget ugyanazon folyamat futtatásai között.  Ellenkező esetben a rendszeróra értéke lesz a mag, ami némileg eltérő eredményeket okozhat a folyamat minden egyes futtatásakor.
  
13. Adjon hozzá egy címkézett adatkészletet a folyamathoz, és csatlakoztassa az egyik [betanítási modult.](module-reference.md)  
  
    -   Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](train-model.md) modult.  
  
14. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

+ A modell pontozási használatához adja hozzá a **Score Model** modult egy folyamathoz.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
