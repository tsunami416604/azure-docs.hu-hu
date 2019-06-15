---
title: 'Neurális hálózat regresszió: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, a Neurális hálózat regressziós modul használata az Azure Machine Learning szolgáltatás egy olyan testre szabható Neurális hálózat algoritmus használatával regressziós modell létrehozásához...
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: bc6a7505ab09e929e5add61eea687f871aedf242
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029310"
---
# <a name="neural-network-regression-module"></a>Neurális hálózat regressziós modul

*Egy regressziós modell létrehozása egy Neurális hálózat algoritmus használatával*  
  
 Kategória: Gépi tanulás / modell inicializálása / regresszió
  
## <a name="module-overview"></a>A modul áttekintése  

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával egy olyan testre szabható Neurális hálózat algoritmus használatával regressziós modell létrehozásához.
  
 Bár a deep learning és a modellezés komplex kapcsolatos, például képfelismerés széles körben ismert Neurális hálózatok, azok könnyedén igazítani regressziós problémák. Minden osztály statisztikai modellek nevezhető Neurális hálózat, ha azokat az adaptív súlyok használata, és megbecsülheti a bemenetek nem lineáris funkcióit. Így a Neurális hálózat regressziós megfelelő a problémákat, ahol egy hagyományos regressziós modell nem fér el a megoldás.
  
 Neurális hálózat regressziós egy felügyelt tanítás metódust, és ezért van szükség egy *adatkészlet címkézett*, amely tartalmaz egy címkét. Egy regressziós modell előre megbecsüli egy numerikus értéket, mert a címke oszlop numerikus adattípust kell lennie.  
  
 A modell betanításához biztosít a modell és a címkézett adatkészlet bemeneteként [Train Model](./train-model.md). A betanított modell majd az új bemeneti példák értékeinek használható.  
  
## <a name="configure-neural-network-regression"></a>Neurális hálózat regressziós konfigurálása 

Neurális hálózatokkal nagymértékben testre szabható. Ez a szakasz ismerteti, hogyan hozhat létre egy modell kétféleképpen:
  
+ [Az alapértelmezett architektúra használatával Neurális hálózat modell létrehozása](#bkmk_DefaultArchitecture)  
  
    Ha elfogadja az alapértelmezett Neurális hálózati architektúrát, használja a **tulajdonságok** ablaktáblán, a Neurális hálózat, például a csomópontok a rejtett rétegben, tanulási rátát és normalizálási viselkedését szabályozó paramétereinek a beállításához.

    Ha még nem ismeri a Neurális hálózatokat, kezdje itt. A modul támogatja számos testreszabásokat, és a modell hangolása, a Neurális hálózatokat részletes ismerete nélkül. 

+ Adja meg egy egyéni Neurális hálózat architektúrája 

    Használja ezt a beállítást, ha azt szeretné, nagyon rejtett réteget hozzáadásához, vagy teljes mértékben testre szabhatja a hálózati architektúrát, a kapcsolatokat és az aktiválási funkciók.
    
    Ez a beállítás akkor ajánlott, ha már ismeri némileg Neurális hálózatokkal. A Net # nyelv használatával adja meg a hálózati architektúra.  

##  <a name="bkmk_DefaultArchitecture"></a> Az alapértelmezett architektúra használatával Neurális hálózat modell létrehozása
  
1.  Adja hozzá a **Neurális hálózat regressziós** modult a kísérletvászonra a felületen. Ez a modul alatt található **Machine Learning**, **inicializálása**, a a **regressziós** kategória. 
  
2. Megadhatja, hogy hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.  

3.  A **rejtett réteg specifikáció**válassza **teljes csatlakoztatott eset**. Ezzel a beállítással létrehoz egy modellt, az alapértelmezett Neurális hálózat architektúra, amely egy Neurális hálózat regressziós modell, ezek az attribútumok rendelkezik használatával:  
  
    + A hálózat pontosan egy rejtett réteg rendelkezik.
    + A kimenete rétegen teljesen csatlakozni a rejtett rétegben, és a rejtett rétegben teljesen csatlakozni a bemeneti réteget.
    + A rejtett rétegben található csomópontok számát a felhasználó által beállítható (alapértelmezett érték 100).  
  
    A bemeneti rétegben lévő csomópontok számát a betanítási adatok a szolgáltatások száma határozza meg, mert a regressziós modellek lehetnek csak egy csomópont a kimenete rétegen.  
  
4. A **rejtett csomópontok**, írja be a rejtett csomópontok számát. Az alapértelmezett érték 100 csomópont egy rejtett réteg. (Ez a beállítás nem áll rendelkezésre, ha meghatároz egy egyéni architektúra használatával Net #.)
  
5.  A **tanulási ráta**, adjon meg egy értéket, amely meghatározza a lépés minden egyes ismétléskor javítása előtt időpontokban. Tanulási ráta nagyobb értéket a modell csoportba történő átszervezését gyorsabban okozhat, de azt is túllépése esetén helyi minimális követelménynek.

6.  A **tanulási ismétlések száma**, adja meg az algoritmus dolgozza fel a betanítási esetek maximális száma.

7.  A ** a betanulási súlyozza átmérőjű, be egy értéket, amely meghatározza, hogy a csomópont súlyok a tanulási folyamat elején.

8.  A **a hír**, írjon be egy értéket az előző lépés során a csomópontok a súlyozást tanulására alkalmazni.

10. A beállítást, **példák Shuffle**, az esetek közötti ismétlését a sorrendjének módosításához. Törölje ezt a beállítást, ha esetek pontosan ugyanabban a sorrendben dolgozzák minden alkalommal, amikor a kísérlet futtatásához.
  
11. A **véletlenszerű szám kezdőérték**, kitöltheti a kezdőérték használandó értéket. Adja meg az egyik kezdőérték értéket akkor hasznos, ha azt szeretné, az ismételhetőség az ugyanazon kísérletben frissítési kísérletei.
  
13. Betanítási adatkészletet és a egy összekapcsolása a [képzési modulok](module-reference.md): 
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**, használjon [tanítási modell](./train-model.md).  
  
   
14. Futtassa a kísérletet.  

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A modell paraméterek együtt a szolgáltatás összegzését súlyok származó képzési és más paramétereket, a Neurális hálózat, kattintson a jobb gombbal kimenete [tanítási modell](./train-model.md), és válassza ki **Visualize**.  

+ Szeretné menteni a betanított modell pillanatképét, kattintson a jobb gombbal a **Trained model** kimenetét, és válassza ki **betanított modell mentése másként**. Ez a modell nem frissül az ugyanazon kísérletben, egymást követő futtatások.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 