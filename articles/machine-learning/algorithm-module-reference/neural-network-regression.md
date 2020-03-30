---
title: 'Neurális hálózati regresszió: modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a Neurális hálózati regressziós modul az Azure Machine Learning ben egy regressziós modell tanoncok segítségével egy testreszabható neurális hálózati algoritmus..
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3a591badab29a1669d109f01f8a93732704d2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456098"
---
# <a name="neural-network-regression-module"></a>Neurális hálózati regressziómodul

*Regressziós modell létrehozása neurális hálózati algoritmus használatával*  
  
 Kategória: Gépi tanulás / Modell inicializálása / regresszió
  
## <a name="module-overview"></a>Modul – áttekintés  

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal egy regressziós modellt hozhat létre egy testreszabható neurális hálózati algoritmus használatával.
  
 Bár a neurális hálózatok széles körben ismert a mély tanulás és modellezés e komplex problémák, mint például a képfelismerés, könnyen alkalmazkodik a regressziós problémák. A statisztikai modellek bármely osztálya neurális hálózatnak nevezhető, ha adaptív súlyokat használ, és megközelítheti a bemenetek nem lineáris funkcióit. Így a neurális hálózati regresszió olyan problémákhoz illeszkedik, ahol egy hagyományosabb regressziós modell nem fér el a megoldás.
  
 Neurális hálózati regresszió egy felügyelt tanulási módszer, és ezért egy *címkézett adatkészletet*igényel, amely tartalmaz egy címke oszlop. Mivel a regressziós modell numerikus értéket jósol, a feliratoszlopnak numerikus adattípusnak kell lennie.  
  
 A modell betanításához adja meg a modellt és a címkézett adatkészletet a [betanítási modell bemeneteként.](./train-model.md) A betanított modell ezután az új bemeneti példák értékeinek előrejelzésére használható.  
  
## <a name="configure-neural-network-regression"></a>Neurális hálózati regresszió konfigurálása 

Neurális hálózatok széles körben testre szabható. Ez a szakasz azt ismerteti, hogyan hozhat létre modellt két módszerrel:
  
+ [Neurális hálózati modell létrehozása az alapértelmezett architektúrával](#bkmk_DefaultArchitecture)  
  
    Ha elfogadja az alapértelmezett neurális hálózati architektúrát, a **Tulajdonságok** ablaktábla segítségével állítsa be a neurális hálózat viselkedését szabályozó paramétereket, például a rejtett rétegben lévő csomópontok számát, a tanulási sebességet és a normalizálást.

    Kezdje itt, ha új a neurális hálózatokban. A modul számos testreszabást támogat, valamint a modellhangolást, a neurális hálózatok mély ismerete nélkül. 

+ Neurális hálózat egyéni architektúrájának meghatározása 

    Akkor használja ezt a beállítást, ha további rejtett rétegeket szeretne hozzáadni, vagy teljesen testre szeretné szabni a hálózati architektúrát, annak kapcsolatait és aktiválási funkcióit.
    
    Ez az opció akkor a legjobb, ha már némileg ismeri a neurális hálózatokat. A hálózati architektúra definiálásához használja a Net# nyelvet.  

##  <a name="create-a-neural-network-model-using-the-default-architecture"></a><a name="bkmk_DefaultArchitecture"></a>Neurális hálózati modell létrehozása az alapértelmezett architektúrával

1.  Adja hozzá a **neurális hálózati regressziós** modult a tervező ben a folyamathoz. Ez a modul a **Machine Learning**, Initialize ( **Inicicia**) csoportban található a **Regresszió kategóriában.** 
  
2. Adja meg, hogyan szeretné betanítani a modellt, az **Oktató létrehozása mód beállítása** beállítással.  
  
    -   **Egyetlen paraméter:** Válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    -   **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.   

3.  A **Rejtett réteg specifikációja**csoportban válassza **a Teljesen csatlakoztatott eset**lehetőséget. Ez a beállítás létrehoz egy modellt az alapértelmezett neurális hálózati architektúra használatával, amely egy neurális hálózati regressziós modell, rendelkezik a következő attribútumok:  
  
    + A hálózat pontosan egy rejtett réteggel rendelkezik.
    + A kimeneti réteg teljes mértékben a rejtett réteghez csatlakozik, a rejtett réteg pedig teljes mértékben a beviteli réteghez.
    + A rejtett rétegben lévő csomópontok számát a felhasználó állíthatja be (az alapértelmezett érték 100).  
  
    Mivel a bemeneti rétegben lévő csomópontok számát a betanítási adatokban lévő szolgáltatások száma határozza meg, a regressziós modellben csak egy csomópont lehet a kimeneti rétegben.  
  
4. A **Rejtett csomópontok száma**mezőbe írja be a rejtett csomópontok számát. Az alapértelmezett érték egy rejtett réteg 100 csomóponton. (Ez a beállítás nem érhető el, ha egyéni architektúrát definiál a Net#használatával.)
  
5.  A **tanulási sebesség**mezőbe írjon be egy értéket, amely meghatározza az egyes iterációknál a javítás előtt megtett lépést. A tanulási arány nagyobb értéke a modell gyorsabb konvergenciáját okozhatja, de túlléphet a helyi minimumokon.

6.  A **tanulási ismétlések száma mezőben**adja meg, hogy az algoritmus hányszor dolgozza fel a betanítási eseteket.


8.  **A Momentum**mezőbe írjon be egy értéket, amelyet a tanulás során a korábbi iterációk csomópontjainak súlyaként kell alkalmazni.

10. Válassza a **Véletlen sorrendű példák**lehetőséget az ismétlések közötti esetek sorrendjének módosításához. Ha nem jelöli be ezt a beállítást, az esetek feldolgozása pontosan ugyanabban a sorrendben történik minden alkalommal, amikor futtatja a folyamatot.
  
11. A **véletlenszám-vetőmag**esetében tetszés szerint beírhat egy értéket, amelyet vetőmagként szeretne használni. A kezdőérték megadása akkor hasznos, ha biztosítani szeretné az ismételhetőséget ugyanazon folyamat futtatásai között.
  
13. Képzési adatkészlet és az egyik [betanítási modul csatlakoztatása:](module-reference.md) 
  
    -   Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell t.](./train-model.md)  
  
   
14. Küldje el a folyamatot.  

## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

- A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 