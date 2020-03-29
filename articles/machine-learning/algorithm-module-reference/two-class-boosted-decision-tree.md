---
title: 'Kétosztályos súlyozott döntési fa: Modulhivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a kétosztályos súlyozott döntési fa modult az Azure Machine Learningben egy gépi tanulási modell létrehozásához, amely a kiemelt döntési fák algoritmuson alapul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 1d144a48f79e59b35c88c5b338747d3186ebceda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920739"
---
# <a name="two-class-boosted-decision-tree-module"></a>Kétosztályos súlyozott döntési fa modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy gépi tanulási modell, amely a kiemelt döntési fák algoritmus on alapul. 

A kiemelt döntési fa egy együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább.  Előrejelzések alapján az egész együttese fák együtt, ami a jóslat.
  
Általában, ha megfelelően konfigurált, kiemelt döntési fák a legegyszerűbb módszer, amellyel a legjobb teljesítményt a legkülönbözőbb gépi tanulási feladatok. Azonban ők is az egyik több memória-intenzív tanulók, és a jelenlegi végrehajtás tart mindent a memóriában. Ezért előfordulhat, hogy egy kiemelt döntési famodell nem tudja feldolgozni azokat a nagy adatkészleteket, amelyeket egyes lineáris tanulók kezelni tudnak.

## <a name="how-to-configure"></a>Hogyan kell beállítani

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás felügyelt tanulási módszer, a modell betanításához egy *címkézett adatkészletre* van szükség, amely tartalmaz egy címkeoszlopot, amely minden sorhoz tartozik értéket.

Az ilyen típusú modellt a [Train Model](././train-model.md)segítségével taníthatja be. 

1.  Az Azure Machine Learningben adja hozzá a **kiemelt döntési fa** modult a folyamathoz.
  
2.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.
  
    + **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumként megadhat egy adott értékkészletet.
  
    + **Paramétertartomány**: Ha nem biztos a legjobb paraméterekben, megtalálhatja az optimális paramétereket a [Tune Model Hyperparameters](tune-model-hyperparameters.md) modul segítségével. Ön megad néhány értéktartományt, és a tréner a beállítások több kombinációján keresztül iterálja a legjobb eredményt adó értékek kombinációját.
  
3.  A **Levelek maximális száma egy fán,** adja meg a terminálcsomópontok (levelek) maximális számát, amely bármely fán létrehozható.
  
     Ennek az értéknek a növelésével potenciálisan növelheti a fa méretét, és jobb pontosságot kap, a túlszerelés és a hosszabb képzési idő kockázatával.
  
4.  A **minták minimális száma levélcsomópontonként**, adja meg a terminálcsomópontok (levél) létrehozásához szükséges esetek számát.  
  
     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, még egyetlen eset is új szabály létrehozását okozhatja. Ha az értéket 5-re növeli, a betanítási adatoknak legalább öt olyan esetet kell tartalmazniuk, amelyek megfelelnek az azonos feltételeknek.
  
5.  A **tanulási sebesség**hez írjon be egy 0 és 1 közötti számot, amely tanulási közben meghatározza a lépésméretet.  
  
     A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassúan közelít az optimális megoldáshoz. Ha a lépés mérete túl nagy, előfordulhat, hogy túllépi az optimális megoldást. Ha a lépés mérete túl kicsi, a betanítás hosszabb időt vesz igénybe a legjobb megoldás hoz.
  
6.  A **létrehozott fák száma**területen adja meg az együttesben létrehozandó döntési fák teljes számát. Azáltal, hogy több döntés fák, akkor potenciálisan jobb lefedettséget, de a képzési idő növekedni fog.
  
     Ez az érték azt is szabályozza, hogy a betanított modell megjelenítésekor hány fa jelenik meg. Ha egyetlen fát szeretne látni vagy nyomtatni, állítsa az értéket 1-re. Ha azonban így tesz, csak egy fa keletkezik (a fa a paraméterek kezdeti készletével), és nem történik további ismétlés.
  
7.  A **véletlenszámkezdőmag**esetében tetszés szerint írjon be egy nem negatív egész számot, amelyet véletlenszerű magértékként használ. A vetőmag megadása biztosítja a reprodukálhatóságot az azonos adatokkal és paraméterekkal rendelkező futtatások között.  
  
     A véletlenszerű mag alapértelmezés szerint 0, ami azt jelenti, hogy a kezdeti magérték a rendszerórából származik.  A véletlenszerű magot használó egymást követő futtatások eltérő eredményeket hozhatnak.
  

9. A modell betanítása.
  
    + Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, csatlakoztasson egy címkézett adatkészletet és a [Modell betanítása](./train-model.md) modult.  
   
## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

+ A modell pontozási használatához adja hozzá a **Score Model** modult egy folyamathoz.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 