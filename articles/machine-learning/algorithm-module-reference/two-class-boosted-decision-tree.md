---
title: 'Kétosztályos gyorsított döntési fa: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használhatja a két osztályú súlyozott döntési fa modul az Azure Machine Learning szolgáltatásban hozzon létre egy gépi tanulási modellt a gyorsított döntési fák algoritmus alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 09ea530cac5bdbd62208f134177e5ceaccb545e2
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65027945"
---
# <a name="two-class-boosted-decision-tree-module"></a>Két osztályú súlyozott döntési fa modul

Ez a cikk ismerteti a vizuális felületen (előzetes verzió) az Azure Machine Learning szolgáltatás egy moduljához.

Ez a modul használatával létrehozhat egy machine learning-modellhez, amely a gyorsított döntési fák algoritmus alapul. 

Egy gyorsított döntési fa tanulási módszert, amelyben a második faszerkezetes kijavítja a hibákat, az első faszerkezetes ruhaegyüttes, a harmadik fa kijavítja a hibákat, az első és második fák és így tovább.  Előrejelzés az egész ensemble fák együtt, amely lehetővé teszi az előrejelzési alapulnak.
  
Ha megfelelően konfigurálva, gyorsított döntési fák algoritmus általában a legegyszerűbb módszer, amellyel számos különböző gépi tanulási feladatok a legjobb teljesítmény. Azonban szerepelnek a nagyobb memóriaigényű tanulók egyikét, és a jelenlegi megvalósítás a memóriában tárolja mindent. Egy gyorsított döntési fa modell, ezért nem feltétlenül tudni feldolgozni ezeket a nagyméretű adathalmazok, amelyek bizonyos lineáris tanulók képes kezelni.

## <a name="how-to-configure"></a>Konfigurálása

Ez a modul egy kellő osztályozási modell hoz létre. Mivel besorolást egy felügyelt tanítás módszer, a modell betanításához szükséges egy *adatkészlet címkézett* , amely tartalmaz egy címkét az összes sor értékét.

Az ilyen típusú modell segítségével betaníthatja [Train Model](././train-model.md). 

1.  Az Azure Machine Learning, adja hozzá a **súlyozott döntési fa** modult a kísérletvászonra.
  
2.  Adja meg, hogyan a modellt úgy vélik a **létrehozási trainer módban** lehetőséget.
  
    + **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhat egy adott értékhalmazt argumentumként.
  
  
3.  A **maximális száma kiszolgálónként fa leaves**, bármely fában hozható létre a terminál (levelek) csomópontok maximális számát jelzi.
  
     Ez az érték növelésével, potenciálisan a fa méretének növeléséhez és a nagyobb pontosság kockázatára overfitting, és már képzési az idő lekérése.
  
4.  A **minták levél csomópontonként minimális száma**, terminál csomópont (levél) létrehozásához szükséges egy fa esetek számát jelzi.  
  
     Ez az érték növelésével növeli az új szabályok létrehozása a küszöbértéket. Az alapértelmezett értéke 1, például egyetlen esetet okozhat egy új szabályt létrehozni. Az érték 5-re növeli, ha a betanítási adatok kellene az azonos feltételeket teljesítő legalább öt esetek tartalmaznak.
  
5.  A **tanulási ráta**, adjon meg egy 0 és 1, amely meghatározza a tanulás során lépésköz mérete közötti számot.  
  
     Megfelelő tanulási rátát határozza meg, hogyan gyors vagy lassú a learner az ideális megoldás a szerveződik át. Ha lépésköz mérete túl nagy, akkor előfordulhat, hogy túllépése esetén az ideális megoldás. Ha a lépés mérete túl kicsi, képzési hosszabb időt vehet igénybe összegyűjti az a legjobb megoldás.
  
6.  A **épített fák száma**, hozhat létre a ensemble döntési fák teljes számát. Több döntési fák létrehozásával lefedettségét esetlegesen kap, de megnöveli a képzési időt.
  
     Ez az érték vezérli jelenik meg, amikor megjelenítik a betanított modell fák számát is. Ha azt szeretné, vagy egy egyetlen fa nyomtatása, állítsa az értékét 1. Azonban ha így tesz, csak egy fa jön létre (a kezdeti paraméterkészlet fa), és nincs további ismétléseinek el kell végezni.
  
7.  A **véletlenszerű szám kezdőérték**, igény szerint írjon be egy nem negatív egész véletlenszám-generálás kezdőértéke értéket használni. Adja meg az egyik kezdőérték biztosítja, hogy az megismételhetőség ugyanazokhoz az adatokhoz és paraméterekkel rendelkező futtatás során.  
  
     A véletlenszerű kezdőérték 0, ami azt jelenti, hogy a kezdeti alapérték kérhető le a rendszeróra alapértelmezés szerint van beállítva.  Egymást követő fut egy véletlenszám-generálás kezdőértéke használatával különböző eredményeket is rendelkezhet.
  

9. A modell betanításához.
  
    + Ha **létrehozási trainer módban** való **egyetlen paramétert**, címkézett adatkészlet csatlakoztatása és a [tanítási modell](./train-model.md) modul.  
  
   
## <a name="results"></a>Results (Eredmények)

Modell betanítása befejezése után kattintson a jobb gombbal a kimenetét [tanítási modell](./train-model.md) az eredmények megtekintése:

+ A fa minden egyes ismétléskor létrehozott megtekintéséhez válasszon **Visualize**. 
+ Az elágazást feltárásához és a szabályokat az egyes csomópontok megtekintéséhez kattintson a fákban.


## <a name="next-steps"></a>További lépések

Tekintse meg a [modullistából készletét](module-reference.md) Azure Machine Learning szolgáltatáshoz. 