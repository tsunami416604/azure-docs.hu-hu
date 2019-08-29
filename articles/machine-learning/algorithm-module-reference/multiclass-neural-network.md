---
title: 'Többosztályos neurális hálózat: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a többosztályos neurális hálózati modul a Azure Machine Learning szolgáltatásban egy olyan neurális hálózati modell létrehozásához, amely egy több értékkel rendelkező cél előrejelzésére használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 44d1e7606efd5bc6d2286254dc4863728e3edbfd
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128597"
---
# <a name="multiclass-neural-network-module"></a>Többosztályos neurális hálózati modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy olyan neurális hálózati modellt, amely egy több értékkel rendelkező cél előrejelzésére használható. 

Előfordulhat például, hogy az ilyen típusú neurális hálózatokat összetett számítógépes látási feladatokban lehet használni, például a Digit vagy a levél felismerése, a dokumentumok besorolása és a minta felismerése.

A neurális hálózatokat használó besorolás felügyelt tanulási módszer, ezért olyan címkézett adatkészletet igényel, amely tartalmaz egy címke oszlopot.

A modellt úgy is betaníthatja, hogy a modell és a címkézett adatkészlet bemenetként adja meg a betanítási [modellt](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.  

## <a name="about-neural-networks"></a>A neurális hálózatok ismertetése

A neurális hálózatok egymással összekapcsolt rétegekből állnak. A bemenetek az első réteg, és egy kimeneti réteghez csatlakoznak egy súlyozott élekből és csomópontokból álló aciklikus gráf használatával.

A bemeneti és a kimeneti rétegek között több rejtett réteget is beszúrhat. A legtöbb prediktív feladat egyszerűen elvégezhető egy vagy több rejtett réteg használatával. A legújabb kutatások azonban azt mutatták, hogy a sok réteggel rendelkező Deep neurális hálózatok (DNN) hatékonyak lehetnek olyan összetett feladatokban, mint például a képek vagy a beszédfelismerés. Az egymást követő rétegek a szemantikai mélység növekvő szintjének modellezésére szolgálnak.

A bemenetek és a kimenetek közötti kapcsolatot a rendszer betanítja a neurális hálózat betanítása a bemeneti adatokon. A gráf iránya a bemeneti rétegből és a kimeneti rétegből folytatódik. A rétegben lévő összes csomópontot a súlyozott élek kötik a következő réteg csomópontjaira.

Egy adott bemenethez tartozó hálózat kimenetének kiszámításához egy értéket kell kiszámítani a rejtett rétegek és a kimeneti réteg minden csomópontján. Az érték úgy van beállítva, hogy kiszámítja az előző réteg csomópontjainak értékeinek súlyozott összegét. Ezután az adott súlyozott összegre alkalmazza az aktiválási függvényt.

## <a name="configure-multiclass-neural-network"></a>Többosztályos neurális hálózat konfigurálása

1. Adja hozzá a többosztályos **neurális hálózati** modult a kísérlethez az illesztőfelületen. Ezt a modult a **besorolás** kategóriában, a **Machine learning**, az **inicializálás**lehetőség alatt találja.

2. **Oktatói mód létrehozása**: Ezzel a beállítással adhatja meg, hogyan kívánja betanítani a modellt:

    - **Egyetlen paraméter**: Akkor válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    

3. **Rejtett réteg specifikációja**: Válassza ki a létrehozandó hálózati architektúra típusát.

    - **Teljes mértékben csatlakoztatott eset**: Válassza ezt a lehetőséget, ha az alapértelmezett neurális hálózati architektúrát használó modellt szeretne létrehozni. A többosztályos neurális hálózati modellek esetében az alapértelmezett értékek a következők:

        - Egy rejtett réteg
        - A kimeneti réteg teljes mértékben csatlakozik a rejtett réteghez.
        - A rejtett réteg teljes mértékben csatlakozik a bemeneti réteghez.
        - A bemeneti rétegben lévő csomópontok számát a betanítási adatokban található szolgáltatások száma határozza meg.
        - A rejtett rétegben található csomópontok számát a felhasználó állíthatja be. Az alapértelmezett érték a 100.
        - A kimeneti rétegben lévő csomópontok száma az osztályok számától függ.
  
   

5. **Rejtett csomópontok száma**: Ez a beállítás lehetővé teszi a rejtett csomópontok számának testreszabását az alapértelmezett architektúrában. Adja meg a rejtett csomópontok számát. Az alapértelmezett érték egy rejtett réteg, amely 100 csomóponttal rendelkezik.

6. **A tanulási arány**: Megadhatja az egyes iterációkban végrehajtott lépés méretét a javítás előtt. A tanulási ráta nagyobb értéke miatt a modell gyorsabban konvergálhat, de a helyi minimumok túllépése is lehet.

7. **Tanulási Ismétlések száma**: Itt adhatja meg, hogy az algoritmus hány alkalommal dolgozza fel a betanítási eseteket.

8. **A kezdeti tanulási súlyok átmérő**: Határozza meg a csomópontok súlyozásait a tanulási folyamat elején.

9. **A lendület**: Határozza meg az előző iterációk csomópontjain való tanulás során alkalmazandó súlyozást.
  
11. **Példák a shuffle**-re: Válassza ezt a lehetőséget az ismétlések közötti esetek rendezéséhez.

    Ha kijelöli ezt a beállítást, a rendszer pontosan ugyanabban a sorrendben dolgozza fel az eseteket, amikor futtatja a kísérletet.

12. **Véletlenszerű számú mag**: Adja meg a magokként használni kívánt értéket, ha meg kívánja bizonyosodni arról, hogy az azonos kísérletben lévő futtatások között ismételhető.

14. Csatlakoztasson egy betanítási adatkészletet és egy betanítási [modult](module-reference.md): 

    - Ha úgy állítja be az **oktatói módot** , hogy az **egyetlen paraméter**legyen, használja a [Train modellt](train-model.md).  
  

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

- Ha meg szeretné tekinteni a modell paramétereinek összegzését, valamint a képzésből beszerzett, valamint a neurális hálózat egyéb paramétereit, kattintson a jobb gombbal a [vonat modell](./train-model.md) kimenetére, majd válassza a **Megjelenítés**lehetőséget.  

- A betanított modell pillanatképének mentéséhez kattintson a jobb gombbal a **betanított modell** kimenetére, és válassza a **Mentés**betanítva modellként lehetőséget. Ez a modell nem frissül ugyanazon kísérlet egymást követő futtatásakor.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 