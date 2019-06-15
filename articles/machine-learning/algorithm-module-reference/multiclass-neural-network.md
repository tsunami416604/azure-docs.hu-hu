---
title: 'Multiclass Neurális hálózat: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a Neurális hálózati osztályú modul az Azure Machine Learning szolgáltatás Neurális hálózat, amely több érték tartozik egy cél előrejelzésére szolgáló modell létrehozása.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7bef225c001ebd9bbb9a45c8bcc301cfb49edb6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029385"
---
# <a name="multiclass-neural-network-module"></a>Multiclass Neurális hálózat-modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával Neurális hálózat, amely több érték tartozik egy cél előrejelzésére szolgáló modell létrehozása. 

Például ilyen típusú Neurális hálózatokkal előfordulhat, hogy használható komplex számítógépes látástechnológiai feladatokat, például számjegyet vagy betűt felismerés, a dokumentum besorolási és mintafelismerést.

Besorolási Neurális hálózatok használata egy felügyelt tanítás módszer, és ezért van szükség egy *adatkészlet címkézett* , amely tartalmaz egy címkét.

A modell betanításához biztosít a modell és a címkézett adatkészlet bemeneteként [Train Model](./train-model.md). A betanított modell majd az új bemeneti példák értékeinek használható.  

## <a name="about-neural-networks"></a>Neurális hálózatokkal kapcsolatos

Neurális hálózat olyan összekapcsolt rétegek. A bemeneti adatok az első rétegbeli, és egy aciklikus diagramhoz súlyozott élek és a csomópont csatlakozik egy kimenete rétegen.

A bemeneti és kimeneti rétegek között több rejtett réteget is beszúrhat. Legtöbb prediktív feladatot is elvégezhető egyszerűen csak egy vagy több rejtett réteget. Legutóbbi kutatási azonban azt mutatja, sok réteg Neurális hálózatok (DNN), hogy bonyolult feladatokat, köztük a képet, vagy a beszédfelismerést a hatékony. Az egymást követő rétegek szemantikai mélysége növekvő mértékű modell szolgálnak.

Bemenetek és kimenetek közötti kapcsolat tanulságot a bemeneti adatokat a Neurális hálózat betanítása. A gráf irányát a Rejtett réteg és a kimenete rétegen bemenetei a folytatódik. A réteg összes csomópontja a következő rétege a csomópontok súlyozott élek köti össze.

A kimenet a hálózat egy adott bevitel számítási, érték minden egyes csomópont a rejtett rétegben, és a kimenete rétegen számítja ki. Az értéket állítja be az értékeket az előző rétegből csomópont súlyozott összege kiszámítása. Az aktiválás függvényének alkalmazva lesz súlyozott az összeget.

## <a name="configure-multiclass-neural-network"></a>Multiclass Neurális hálózat konfigurálása

1. Adja hozzá a **osztályú Neurális hálózat** modult a kísérletvászonra a felületen. Ez a modul alatt található **Machine Learning**, **inicializálása**, a a **besorolási** kategória.

2. **A létrehozási mód trainer**: Ez a beállítás használatával adja meg, hogyan kell betanítani a modellt:

    - **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha már tudja, hogyan szeretné konfigurálni a modellt.

    

3. **Rejtett réteg specifikáció**: Válassza ki a hálózati architektúra hozhat létre.

    - **Teljes körűen csatlakoztatott eset**: Ezt a beállítást az alapértelmezett Neurális hálózat architektúra használatával egy modell létrehozásához. Multiclass Neurális hálózat esetében az alapértelmezett érték a következők:

        - Egy rejtett réteg
        - A kimenete rétegen teljes csatlakozik a rejtett rétegben.
        - A rejtett rétegben teljes mértékben a bemeneti réteg van csatlakoztatva.
        - A bemeneti rétegben lévő csomópontok számát a betanítási adatok a szolgáltatások száma határozza meg.
        - A rejtett rétegben található csomópontok számát a felhasználó által beállítható. Az alapértelmezett érték 100.
        - A kimenete rétegen csomópontok száma attól függ, hogy hány osztály.
  
   

5. **Rejtett csomópontok**: Ez a beállítás teszi lehetővé testre szabhatja az alapértelmezett architektúra rejtett csomópontok számát. Írja be a rejtett csomópontok számát. Az alapértelmezett érték 100 csomópont egy rejtett réteg.

6. **Megfelelő tanulási rátát**: Határozza meg a lépés minden egyes ismétléskor javítása előtt időpontokban méretét. Tanulási ráta nagyobb értéket a modell csoportba történő átszervezését gyorsabban okozhat, de azt is túllépése esetén helyi minimális követelménynek.

7. **Tanulási ismétlések száma**: Adja meg az algoritmus kell dolgoznia a betanítási esetek maximális száma.

8. **A kezdeti learning súlyozza átmérőjű**: Adja meg a csomópont súlyok a tanulási folyamat elején.

9. **A hír**: Adjon meg egy learning csomópontokhoz az előző lépés során alkalmazott súly.
  
11. **Példák shuffle**: Ezt a beállítást a véletlenszerűen rendezendő esetek ismétléseinek között.

    Törölje ezt a beállítást, ha esetek pontosan ugyanabban a sorrendben dolgozzák minden alkalommal, amikor a kísérlet futtatásához.

12. **Véletlenszerű szám kezdőérték**: Írjon be egy értéket a kezdőérték használandó, ha azt szeretné, ismételhetőség kísérletei során az ugyanazon kísérletben.

14. Betanítási adatkészletet és a egy összekapcsolása a [képzési modulok](module-reference.md): 

    - Ha **létrehozási trainer módban** való **egyetlen paramétert**, használjon [tanítási modell](train-model.md).  
  

## <a name="results"></a>Results (Eredmények)

Képzési befejezése után:

- A modell paraméterek együtt a szolgáltatás összegzését súlyok származó képzési és más paramétereket, a Neurális hálózat, kattintson a jobb gombbal kimenete [tanítási modell](./train-model.md) válassza **Visualize**.  

- Szeretné menteni a betanított modell pillanatképét, kattintson a jobb gombbal a **Trained model** kimenetét, és válassza ki **betanított modell mentése másként**. Ez a modell nem frissül az ugyanazon kísérletben, egymást követő futtatások.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 