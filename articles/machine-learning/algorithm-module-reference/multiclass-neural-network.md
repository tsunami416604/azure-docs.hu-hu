---
title: 'Többosztályos neurális hálózat: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a többosztályos neurális hálózati modul t az Azure Machine Learning neurális hálózati modell, amely segítségével előre jelezheti a cél, amely több értéket.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 653b12ddd54c5ec9e4e7dd23a323f34460daa962
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920416"
---
# <a name="multiclass-neural-network-module"></a>Többosztályos neurális hálózati modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy neurális hálózati modellt, amely több értékkel rendelkező cél előrejelzésére használható. 

Az ilyen típusú neurális hálózatok például összetett számítógépes látási feladatokban használhatók, például számjegy- vagy betűfelismerésben, dokumentumbesorolásban és mintafelismerésben.

Neurális hálózatok használatával történő besorolás felügyelt tanulási módszer, ezért egy *címkézett adatkészletet* igényel, amely tartalmaz egy címkeoszlopot.

A modell betanításához adja meg a modellt és a címkézett adatkészletet a [betanítási modell bemeneteként.](./train-model.md) A betanított modell ezután az új bemeneti példák értékeinek előrejelzésére használható.  

## <a name="about-neural-networks"></a>A neurális hálózatokról

A neurális hálózat összekapcsolt rétegek halmaza. A bemenetek az első réteg, és egy kimeneti réteghez egy súlyozott élekből és csomópontokból álló aciklikus grafikonon keresztül kapcsolódnak.

A bemeneti és a kimeneti rétegek között több rejtett réteget is beilleszthet. A legtöbb prediktív feladat egyszerűen elvégezhető egyetlen vagy néhány rejtett réteggel. Azonban a legújabb kutatások kimutatták, hogy a mély neurális hálózatok (DNN) sok réteget lehet hatékony összetett feladatok, mint a kép-vagy beszédfelismerés. Az egymást követő rétegek a szemantikai mélység növekvő szintjének modellezésére szolgálnak.

A bemenetek és kimenetek közötti kapcsolat a neurális hálózat betanítása a bemeneti adatok. A diagram iránya a bemenetektől a rejtett rétegen keresztül a kimeneti rétegig halad. A réteg összes csomópontját a súlyozott élek kötik össze a következő réteg csomópontjaival.

A hálózat kimenetének kiszámításához egy adott bemenethez egy értéket számít a rendszer a rejtett rétegek és a kimeneti réteg minden csomópontjára. Az értéket az előző réteg csomópontjai értékeinek súlyozott összegének kiszámításával állítja be. Ezután egy aktiválási funkciót alkalmaznak az adott súlyozott összegre.

## <a name="configure-multiclass-neural-network"></a>Többosztályos neurális hálózat konfigurálása

1. Adja hozzá a **MultiClass Neurális hálózati** modult a tervező ben lévő folyamathoz. Ez a modul a **Machine Learning**, **Initialize**, a **Besorolás** kategóriában található.

2. **Tréner mód létrehozása:** Ezzel a beállítással megadhatja, hogyan szeretné betanítani a modellt:

    - **Egyetlen paraméter:** Válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    - **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.  

3. **Rejtett réteg specifikáció**: Válassza ki a létrehozásához kívánt hálózati architektúra típusát.

    - **Teljesen csatlakoztatott eset:** Válassza ezt a lehetőséget, ha az alapértelmezett neurális hálózati architektúra használatával hozhat létre modellt. Többosztályos neurális hálózati modellek esetén az alapértelmezett értékek a következők:

        - Egy rejtett réteg
        - A kimeneti réteg teljes mértékben a rejtett réteghez csatlakozik.
        - A rejtett réteg teljesen csatlakozik a beviteli réteghez.
        - A bemeneti rétegben lévő csomópontok számát a betanítási adatokban lévő szolgáltatások száma határozza meg.
        - A rejtett rétegben lévő csomópontok számát a felhasználó állíthatja be. Az alapértelmezett érték 100.
        - A kimeneti rétegben lévő csomópontok száma az osztályok számától függ.
  
   

5. **Rejtett csomópontok száma**: Ez a beállítás lehetővé teszi a rejtett csomópontok számának testreszabását az alapértelmezett architektúrában. Írja be a rejtett csomópontok számát. Az alapértelmezett érték egy rejtett réteg 100 csomóponton.

6. **A tanulási sebesség:** Határozza meg az egyes iterációknál megtett lépés méretét a javítás előtt. A tanulási arány nagyobb értéke a modell gyorsabb konvergenciáját okozhatja, de túlléphet a helyi minimumokon.

7. **Tanulási ismétlések száma**: Adja meg, hogy az algoritmus hányszor dolgozza fel a betanítási eseteket.

8. **A kezdeti tanulási súlyok átmérője:** Adja meg a csomópont súlyok elején a tanulási folyamat.

9. **A lendület:** Adja meg a korábbi iterációk csomópontjaira való tanulás során alkalmazandó súlyt.
  
11. **Véletlen sorrendű példák**: Ezzel a beállítással keverhetők az ismétlések közötti esetek.

    Ha nem jelöli be ezt a beállítást, az esetek feldolgozása pontosan ugyanabban a sorrendben történik minden alkalommal, amikor futtatja a folyamatot.

12. **Véletlenszámmag:** Írja be a vetőmagként használandó értéket, ha biztosítani szeretné az ismételhetőséget ugyanazon folyamat futásai között.

14. Képzési adatkészlet és az egyik [betanítási modul csatlakoztatása:](module-reference.md) 

    - Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell t.](train-model.md)  
  

## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

- A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 