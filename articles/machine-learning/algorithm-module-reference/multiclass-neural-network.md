---
title: 'Többosztályos neurális hálózat: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a többosztályos neurális hálózati modul a Azure Machine Learning egy olyan neurális hálózati modell létrehozásához, amely egy több értékkel rendelkező cél előrejelzésére használható.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: b49ff44e898a78d865278df087aca75e4f8e50ca
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466029"
---
# <a name="multiclass-neural-network-module"></a>Többosztályos neurális hálózati modul

Ez a cikk a Azure Machine Learning Designer (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy olyan neurális hálózati modellt, amely egy több értékkel rendelkező cél előrejelzésére használható. 

Előfordulhat például, hogy az ilyen típusú neurális hálózatokat összetett számítógépes látási feladatokban lehet használni, például a Digit vagy a levél felismerése, a dokumentumok besorolása és a minta felismerése.

A neurális hálózatokat használó besorolás felügyelt tanulási módszer, ezért olyan *címkézett adatkészletet* igényel, amely tartalmaz egy címke oszlopot.

A modellt úgy is betaníthatja, hogy a modell és a címkézett adatkészlet bemenetként adja meg a [betanítási modellt](./train-model.md). A betanított modell ezután felhasználható az új bemeneti példák értékeinek előrejelzésére.  

## <a name="about-neural-networks"></a>A neurális hálózatok ismertetése

A neurális hálózatok egymással összekapcsolt rétegekből állnak. A bemenetek az első réteg, és egy kimeneti réteghez csatlakoznak egy súlyozott élekből és csomópontokból álló aciklikus gráf használatával.

A bemeneti és a kimeneti rétegek között több rejtett réteget is beszúrhat. A legtöbb prediktív feladat egyszerűen elvégezhető egy vagy több rejtett réteg használatával. A legújabb kutatások azonban azt mutatták, hogy a sok réteggel rendelkező Deep neurális hálózatok (DNN) hatékonyak lehetnek olyan összetett feladatokban, mint például a képek vagy a beszédfelismerés. Az egymást követő rétegek a szemantikai mélység növekvő szintjének modellezésére szolgálnak.

A bemenetek és a kimenetek közötti kapcsolatot a rendszer betanítja a neurális hálózat betanítása a bemeneti adatokon. A gráf iránya a bemeneti rétegből és a kimeneti rétegből folytatódik. A rétegben lévő összes csomópontot a súlyozott élek kötik a következő réteg csomópontjaira.

Egy adott bemenethez tartozó hálózat kimenetének kiszámításához egy értéket kell kiszámítani a rejtett rétegek és a kimeneti réteg minden csomópontján. Az érték úgy van beállítva, hogy kiszámítja az előző réteg csomópontjainak értékeinek súlyozott összegét. Ezután az adott súlyozott összegre alkalmazza az aktiválási függvényt.

## <a name="configure-multiclass-neural-network"></a>Többosztályos neurális hálózat konfigurálása

1. Adja hozzá a **Többosztályos neurális hálózati** modult a folyamathoz a tervezőben. Ezt a modult a **besorolás** kategóriában, a **Machine learning**, az **inicializálás**lehetőség alatt találja.

2. **Oktatói mód létrehozása**: ezzel a beállítással adhatja meg, hogyan kívánja képezni a modellt:

    - **Egyetlen paraméter**: akkor válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    

3. **Rejtett réteg specifikációja**: válassza ki a létrehozandó hálózati architektúra típusát.

    - **Teljes mértékben csatlakoztatott eset**: válassza ezt a lehetőséget, ha az alapértelmezett neurális hálózati architektúrát használó modellt szeretne létrehozni. A többosztályos neurális hálózati modellek esetében az alapértelmezett értékek a következők:

        - Egy rejtett réteg
        - A kimeneti réteg teljes mértékben csatlakozik a rejtett réteghez.
        - A rejtett réteg teljes mértékben csatlakozik a bemeneti réteghez.
        - A bemeneti rétegben lévő csomópontok számát a betanítási adatokban található szolgáltatások száma határozza meg.
        - A rejtett rétegben található csomópontok számát a felhasználó állíthatja be. Az alapértelmezett érték a 100.
        - A kimeneti rétegben lévő csomópontok száma az osztályok számától függ.
  
   

5. **Rejtett csomópontok száma**: Ez a beállítás lehetővé teszi, hogy testreszabja a rejtett csomópontok számát az alapértelmezett architektúrában. Adja meg a rejtett csomópontok számát. Az alapértelmezett érték egy rejtett réteg, amely 100 csomóponttal rendelkezik.

6. **A tanulási arány**: az egyes iterációkban végrehajtott lépés méretének meghatározása a javítás előtt. A tanulási ráta nagyobb értéke miatt a modell gyorsabban konvergálhat, de a helyi minimumok túllépése is lehet.

7. **Tanulási Ismétlések száma**: Itt adhatja meg, hogy az algoritmus hány alkalommal dolgozza fel a betanítási eseteket.

8. **A kezdeti tanulási súlyok átmérő**: a betanítási folyamat elején határozza meg a csomópontok súlyozását.

9. **A lendület: az**előző iterációk csomópontjain való tanulás során alkalmazandó súlyozást határozza meg.
  
11. **Példák a shuffle**-re: válassza ezt a lehetőséget az ismétlések közötti esetek rendezéséhez.

    Ha kijelöli ezt a beállítást, a rendszer a folyamat minden egyes futtatásakor pontosan ugyanabban a sorrendben dolgozza fel az eseteket.

12. **Véletlenszerű számú mag**: adjon meg egy, a vetőmagként használandó értéket, ha meg szeretné győződni az azonos folyamaton futó futtatások közötti ismételhetőségről.

14. Csatlakoztasson egy betanítási adatkészletet és egy [betanítási modult](module-reference.md): 

    - Ha úgy állítja be az **oktatói módot** , hogy az **egyetlen paraméter**legyen, használja a [Train modellt](train-model.md).  
  

## <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

- Ha meg szeretné tekinteni a modell paramétereinek összegzését, valamint a képzésből beszerzett, valamint a neurális hálózat egyéb paramétereit, kattintson a jobb gombbal a [vonat modell](./train-model.md) kimenetére, majd válassza a **Megjelenítés**lehetőséget.  

- A betanított modell pillanatképének mentéséhez kattintson a jobb gombbal a **betanított modell** kimenetére, és válassza a **Mentés betanítva modellként**lehetőséget. Ez a modell nem frissül ugyanazon folyamat egymást követő futtatásakor.


## <a name="next-steps"></a>További lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 