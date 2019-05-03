---
title: 'Kétosztályos Neurális hálózat: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogy a két osztályú Neurális hálózat modul használata az Azure Machine Learning szolgáltatás egy Neurális hálózat modell előre jelezni a cél, amely csak két értéke van használható létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 7ea852fcd312c6f7b1b716278ed538b7accde5bd
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029220"
---
# <a name="two-class-neural-network-module"></a>Kétosztályos Neurális hálózat modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával, amelyek segítségével előre jelezni a cél, amely csak két értéke van Neurális hálózat modell létrehozása.

Besorolási Neurális hálózatok használata egy felügyelt tanítás módszer, és ezért van szükség egy *adatkészlet címkézett*, amely tartalmaz egy címkét. Például használhatja a Neurális hálózat modell bináris eredmények előrejelzésére, mint például-e a betegek elbocsátását egy bizonyos betegségek rendelkezik-e, vagy a gép nagy eséllyel lesz sikertelen idő megadott időtartamon belül.  

A modell határozza meg, miután betanításra címkézett adatkészlet és a modell bemenetként való megadásával [Train Model](./train-model.md). A betanított modell használható értékeinek új bemenetei között.

### <a name="more-about-neural-networks"></a>További információ a Neurális hálózatokkal

Neurális hálózat olyan összekapcsolt rétegek. A bemeneti adatok az első rétegbeli, és egy aciklikus diagramhoz súlyozott élek és a csomópont csatlakozik egy kimenete rétegen.

A bemeneti és kimeneti rétegek között több rejtett réteget is beszúrhat. Legtöbb prediktív feladatot is elvégezhető egyszerűen csak egy vagy több rejtett réteget. Legutóbbi kutatási azonban azt mutatja, sok réteg Neurális hálózatok (DNN), hogy bonyolult feladatokat, köztük a képet, vagy a beszédfelismerést a hatékony. Az egymást követő rétegek szemantikai mélysége növekvő mértékű modell szolgálnak.

Bemenetek és kimenetek közötti kapcsolat tanulságot a bemeneti adatokat a Neurális hálózat betanítása. A gráf irányát a Rejtett réteg és a kimenete rétegen bemenetei a folytatódik. A réteg összes csomópontja a következő rétege a csomópontok súlyozott élek köti össze.

A kimenet a hálózat egy adott bevitel számítási, érték minden egyes csomópont a rejtett rétegben, és a kimenete rétegen számítja ki. Az értéket állítja be az értékeket az előző rétegből csomópont súlyozott összege kiszámítása. Az aktiválás függvényének alkalmazva lesz súlyozott az összeget.
  
## <a name="how-to-configure"></a>Konfigurálása

1.  Adja hozzá a **Two-Class Neurális hálózat** modult a kísérletvászonra. Ez a modul alatt található **Machine Learning**, **inicializálása**, a a **besorolási** kategória.  
  
2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.  
  
    -   **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.  

3.  A **rejtett réteg specifikáció**, válassza ki a hálózati architektúra hozhat létre.  
  
    -   **Teljes körűen csatlakoztatott eset**: Használja az alapértelmezett Neurális hálózat architektúra két osztályú Neurális hálózatok meghatározása a következő:
  
        -   Rendelkezik egy rejtett réteg.
  
        -   A kimenete rétegen teljesen csatlakozni a rejtett rétegben, és a rejtett rétegben teljesen csatlakozni a bemeneti réteget.
  
        -   A bemeneti rétegben lévő csomópontok számát a betanítási adatok a szolgáltatások száma egyenlő.
  
        -   A rejtett rétegben található csomópontok számát a felhasználó állítja be. Az alapértelmezett érték 100.
  
        -   A csomópontok számát az osztályok száma egyenlő. Kétosztályos Neurális hálózat Ez azt jelenti, hogy az összes bemenetei között meg kell felelnie egy két csomópontot a kimenete rétegen.

5.  A **tanulási ráta**, határozza meg a lépés minden egyes ismétléskor javítása előtt időpontokban méretét. Tanulási ráta nagyobb értéket a modell csoportba történő átszervezését gyorsabban okozhat, de azt is túllépése esetén helyi minimális követelménynek.

6.  A **tanulási ismétlések száma**, adja meg az algoritmus kell dolgoznia a betanítási esetek maximális száma.

7.  A **a betanulási súlyozza átmérőjű**, adja meg a csomópont súlyok a tanulási folyamat elején.

8.  A **a hír**, adjon meg egy learning csomópontokhoz az előző lépés során alkalmazott súly  

10. Válassza ki a **példák Shuffle** nak mozgatnia kell az esetek közötti ismétlési lehetőséget. Törölje ezt a beállítást, ha esetek pontosan ugyanabban a sorrendben dolgozzák minden alkalommal, amikor a kísérlet futtatásához.
  
11. A **véletlenszerű szám kezdőérték**, adjon meg egy értéket a kezdőérték adatokként.
  
     Adja meg az egyik kezdőérték értéket akkor hasznos, ha azt szeretné, az ismételhetőség az ugyanazon kísérletben frissítési kísérletei.  Ellenkező esetben a rendszer órája értéket használja, a kezdőérték, amely némileg különböző eredményekkel járhat minden alkalommal, amikor a kísérlet futtatásához.
  
13. Címkézett adatkészlet hozzáadása a kísérletet, és csatlakozzon az egyik a [képzési modulok](module-reference.md).  
  
    -   Ha **létrehozási trainer módban** való **egyetlen paramétert**, használja a [tanítási modell](train-model.md) modul.  
  
14. Futtassa a kísérletet.

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

+ A modell paraméterek együtt a szolgáltatás összegzését súlyok származó képzési és más paramétereket, a Neurális hálózat, kattintson a jobb gombbal kimenete [tanítási modell](./train-model.md), és válassza ki **Visualize**.  

+ Szeretné menteni a betanított modell pillanatképét, kattintson a jobb gombbal a **Trained model** kimenetét, és válassza ki **betanított modell mentése másként**. Ez a modell nem frissül az ugyanazon kísérletben, egymást követő futtatások.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 
