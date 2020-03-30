---
title: 'Kétosztályos támogató vektorgép: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a **kétosztályos támogatás vektorgép** modul az Azure Machine Learning hozzon létre egy modellt, amely a támogatási vektorgép algoritmusa alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: ba788518951e72c1701d99decf46350e8665dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455808"
---
# <a name="two-class-support-vector-machine-module"></a>Kétosztályos támogató vektorgép modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy modellt, amely a támogatási vektorgép algoritmuson alapul. 

A support vektorgépek (SVM-ek) a felügyelt tanulási módszerek jól kutatott osztályai. Ez a konkrét megvalósítás alkalmas két lehetséges kimenetel előrejelzésére, akár folyamatos, akár kategorikus változók alapján.

A modell paramétereinek meghatározása után tanítsa be a modellt a betanítási modulok használatával, és adjon meg egy *címkézett adatkészletet,* amely tartalmaz egy címkét vagy eredményoszlopot.

## <a name="about-support-vector-machines"></a>A támogatási vektorgépek – be

A támogatási vektorgépek a legkorábbi gépi tanulási algoritmusok közé tartoznak, és az SVM-modelleket számos alkalmazásban használták, az információlekéréstől a szöveg- és képbesorolásig. Az SVM-ek osztályozási és regressziós feladatokhoz is használhatók.

Ez az SVM-modell egy felügyelt tanulási modell, amely címkézett adatokat igényel. A betanítási folyamat során az algoritmus elemzi a bemeneti adatokat, és felismeri a mintákat egy többdimenziós jellemzőtérben, a *hipersíkban.*  Minden bemeneti példa pontként jelenik meg ezen a területen, és a kimeneti kategóriákhoz van rendelve oly módon, hogy a kategóriák a lehető legszélesebb réssel legyenek elosztva, és a lehető legbőszkedő réssel legyenek elosztva.

Az előrejelzéshez az SVM algoritmus új példákat rendel az egyik vagy a másik kategóriába, és leképezi őket ugyanabba a térbe. 

## <a name="how-to-configure"></a>Hogyan kell beállítani 

Ebben a modelltípusban ajánlott normalizálni az adatkészletet, mielőtt betanítanák az osztályozót.
  
1.  Adja hozzá a **kétosztályos támogatás vektorgép** modult a folyamathoz.  
  
2.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.  
  
    -   **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumként megadhat egy adott értékkészletet.  

    -   **Paramétertartomány**: Ha nem biztos a legjobb paraméterekben, megtalálhatja az optimális paramétereket a [Tune Model Hyperparameters](tune-model-hyperparameters.md) modul segítségével. Ön megad néhány értéktartományt, és a tréner a beállítások több kombinációján keresztül iterálja a legjobb eredményt adó értékek kombinációját.

3.  Az **ismétlések száma**mezőbe írjon be egy számot, amely a modell létrehozásakor használt ismétlések számát jelöli.  
  
     Ez a paraméter az edzési sebesség és a pontosság közötti kompromisszumok szabályozására használható.  
  
4.  A **Lambda**mezőben írjon be egy értéket, amelyet az L1 regularizálás súlyaként használ.  
  
     Ez a regularizálási együttható használható a modell hangolására. A nagyobb értékek büntetik az összetettebb modelleket.  
  
5.  Válassza a **Beállítás, Normalize funkciók**, ha azt szeretné, hogy normalizálja funkciók edzés előtt.
  
     Ha normalizálást alkalmaz, az edzés előtt az adatpontok középértékre kerülnek, és úgy vannak méretezve, hogy egy mértékegységnyi szórásuk legyen.
  
6.  Válassza ki a **Project to the unit sphere (Projekt az egységszférára**) lehetőséget az együtthatók normalizálásához.
  
     Az értékek egységterületre vetítése azt jelenti, hogy a betanítás előtt az adatpontok 0-ra vannak középre helyezve, és úgy vannak méretezve, hogy egy mértékegységnyi szórásuk legyen.
  
7.  A **Véletlen számmag**mezőbe írjon be egy egész értéket, amelyet magként szeretne használni, ha biztosítani szeretné a futtathatóságre való reprodukálhatóságot.  Ellenkező esetben a rendszeróra értéke magként van használva, ami némileg eltérő eredményeket eredményezhet a futtatások között.
  
9. Címkeként jelölt adatkészlet és az egyik [betanítási modul csatlakoztatása:](module-reference.md)
  
    -   Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](train-model.md) modult.
  
10. Küldje el a folyamatot.

## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

+ A modell pontozási használatához adja hozzá a **Score Model** modult egy folyamathoz.


## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 