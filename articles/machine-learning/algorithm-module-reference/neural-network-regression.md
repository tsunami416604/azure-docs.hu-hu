---
title: 'Neurális hálózat regressziója: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning-szolgáltatás neurális hálózat regressziós modulját egy regressziós modell létrehozásához egy testreszabható neurális hálózati algoritmus használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 93f27458a2571b2e26a090c06b01d8abe3e79c2a
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128585"
---
# <a name="neural-network-regression-module"></a>Neurális hálózat regressziós modulja

*Egy regressziós modellt hoz létre egy neurális hálózati algoritmus használatával*  
  
 Kategória Machine Learning/inicializálási modell/regresszió
  
## <a name="module-overview"></a>Modul áttekintése  

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy regressziós modellt egy testreszabható neurális hálózati algoritmus használatával.
  
 Habár a neurális hálózatok széles körben ismertek a mélyreható tanulásban és az összetett problémák modellezésében, például a képfelismerésben, könnyen alkalmazkodnak a regressziós problémákhoz. A statisztikai modellek bármely osztálya lehet egy neurális hálózat, ha adaptív súlyozást használnak, és a bemenetük nem lineáris függvényeit is megközelítik. Így a neurális hálózat regressziója olyan problémákra alkalmas, amelyekben a hagyományos regressziós modell nem fér hozzá a megoldásokhoz.
  
 A neurális hálózat regressziója felügyelt tanulási módszer, ezért *címkézett*adatkészletet igényel, amely tartalmaz egy címke oszlopot. Mivel a regressziós modell egy numerikus értéket jelez, a Label oszlopnak numerikus adattípusnak kell lennie.  
  
 A modellt úgy is betaníthatja, hogy a modell és a címkézett adatkészlet bemenetként adja meg a betanítási [modellt](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.  
  
## <a name="configure-neural-network-regression"></a>Az neurális hálózat regressziójának konfigurálása 

A neurális hálózatokat széles körben lehet testreszabni. Ez a szakasz azt ismerteti, hogyan hozhat létre modellt két módszer használatával:
  
+ [Neurális hálózati modell létrehozása az alapértelmezett architektúra használatával](#bkmk_DefaultArchitecture)  
  
    Ha elfogadja az alapértelmezett neurális hálózati architektúrát, a **Tulajdonságok** ablaktáblán állíthatja be a neurális hálózat viselkedését vezérlő paramétereket, például a rejtett rétegben található csomópontok számát, a tanulási arányt és a normalizálás funkciót.

    Kezdje itt, ha ismerkedik a neurális hálózatokkal. A modul számos testreszabást támogat, valamint a modell finomhangolását a neurális hálózatok mélyreható ismerete nélkül. 

+ Egyéni architektúra definiálása egy neurális hálózathoz 

    Akkor használja ezt a beállítást, ha további rejtett rétegeket szeretne hozzáadni, vagy teljes mértékben testre szeretné szabni a hálózati architektúrát, a kapcsolatait és az aktiválási funkciókat.
    
    Ez a lehetőség akkor a legjobb, ha már némileg ismeri a neurális hálózatokat. A hálózati architektúrát a net # nyelv használatával határozhatja meg.  

##  <a name="bkmk_DefaultArchitecture"></a>Neurális hálózati modell létrehozása az alapértelmezett architektúra használatával
  
1.  Adja hozzá a **neurális hálózat regressziós** modulját a kísérlethez az illesztőfelületen. Ezt a modult a regressziós kategóriában, a **Machine learning**, az **inicializálás**lehetőség alatt találja. 
  
2. Adja meg, hogyan szeretné képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: Akkor válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.  

3.  A **rejtett réteg specifikációjában**válassza a **teljesen csatlakoztatott eset**elemet. Ez a lehetőség létrehoz egy modellt az alapértelmezett neurális hálózati architektúrával, amely egy neurális hálózat regressziós modelljéhez a következő attribútumokat használja:  
  
    + A hálózat pontosan egy rejtett réteggel rendelkezik.
    + A kimeneti réteg teljes mértékben csatlakozik a rejtett réteghez, és a rejtett réteg teljesen csatlakoztatva van a bemeneti réteghez.
    + A rejtett rétegben található csomópontok számát a felhasználó állíthatja be (az alapértelmezett érték 100).  
  
    Mivel a bemeneti rétegben lévő csomópontok számát a betanítási adatokban található szolgáltatások száma határozza meg, a regressziós modellben csak egy csomópont lehet a kimeneti rétegben.  
  
4. A **rejtett**csomópontok számának megírásával adja meg a rejtett csomópontok számát. Az alapértelmezett érték egy rejtett réteg, amely 100 csomóponttal rendelkezik. (Ez a beállítás nem érhető el, ha a NET # használatával egyéni architektúrát határoz meg.)
  
5.  A **tanulási arány**mezőben adjon meg egy értéket, amely meghatározza az egyes iterációk lépéseit a javítás előtt. A tanulási ráta nagyobb értéke miatt a modell gyorsabban konvergálhat, de a helyi minimumok túllépése is lehet.

6.  A **tanulási ismétlések számának**megadásával meghatározhatja, hogy az algoritmus hány alkalommal dolgozza fel a betanítási eseteket.

7.  A * * a kezdeti tanulási súlyok átmérője mezőbe írjon be egy értéket, amely meghatározza a csomópontok súlyait a tanulási folyamat elején.

8.  **A lendülethez**adja meg a tanulás során alkalmazandó értéket az előző iterációk csomópontjainak súlyozása alapján.

10. Az ismétlések közötti váltáshoz válassza ki a kívánt lehetőséget, majd a **shuffle példákat**. Ha kijelöli ezt a beállítást, a rendszer pontosan ugyanabban a sorrendben dolgozza fel az eseteket, amikor futtatja a kísérletet.
  
11. A **véletlenszerű számú magok**esetében igény szerint megadhatja a vetőmagként használandó értéket. A magok értékének megadásával akkor lehet hasznos, ha biztosítani szeretné az azonos kísérletben lévő futtatások közötti ismételhetőséget.
  
13. Csatlakoztasson egy betanítási adatkészletet és egy betanítási [modult](module-reference.md): 
  
    -   Ha úgy állítja be az **oktatói módot** , hogy az **egyetlen paraméter**legyen, használja a [Train modellt](./train-model.md).  
  
   
14. Futtassa a kísérletet.  

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha meg szeretné tekinteni a modell paramétereinek összegzését, valamint a képzésből beszerzett, valamint a neurális hálózat egyéb paramétereit, kattintson a jobb gombbal a betanítási [modell](./train-model.md)kimenetére, majd válassza a **Megjelenítés**lehetőséget.  

+ A betanított modell pillanatképének mentéséhez kattintson a jobb gombbal a **betanított modell** kimenetére, és válassza a **Mentés**betanítva modellként lehetőséget. Ez a modell nem frissül ugyanazon kísérlet egymást követő futtatásakor.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 