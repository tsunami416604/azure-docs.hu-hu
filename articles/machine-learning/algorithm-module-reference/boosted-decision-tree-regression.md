---
title: 'Kiemelt döntési fa regressziója: Modul hivatkozás'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a kiemelt döntési fa regressziós modul az Azure Machine Learning regressziós fák növelése használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 79a2ccae31fac31d8d10bb643c35a41a3d7cb5d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456726"
---
# <a name="boosted-decision-tree-regression-module"></a>Kiemelt döntési fa regressziós modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy együttest a regressziós fák segítségével fellendítése. *A kiemelés* azt jelenti, hogy minden fa a korábbi fáktól függ. Az algoritmus megtanulja felszerelésével a maradék a fák, hogy megelőzte azt. Így, fellendítése a döntés fa együttes e jellemzően javítja a pontosságot néhány kis kockázata kevesebb lefedettség.  
  
Ez a regressziós módszer felügyelt tanulási módszer, ezért *egy címkézett adatkészletet*igényel. A feliratoszlopnak numerikus értékeket kell tartalmaznia.  

> [!NOTE]
> Ezt a modult csak numerikus változókat használó adatkészletekkel használja.  

Miután definiálta a modellt, tanítsa be a [Vonatmodell használatával.](./train-model.md)

  
## <a name="more-about-boosted-regression-trees"></a>További információk a kiemelt regressziós fákról  

Növelése az egyik a számos klasszikus módszerek létrehozására együttes modellek, valamint zsákolás, véletlenszerű erdők, és így tovább.  Az Azure Machine Learningben a kiemelt döntési fák a MART gradienskiemelési algoritmus hatékony megvalósítását használják. A gradiens kiemelés egy gépi tanulási technika a regressziós problémákhoz. Minden regressziós fát lépésenként épít, egy előre definiált veszteségfüggvényt használva az egyes lépésekben lévő hibák mérésére, és a következőben helyesbítheti azt. Így az előrejelzési modell valójában egy együttese gyengébb előrejelzési modellek.  
  
A regressziós problémák, fellendítése épít egy sor fák egy lépésenként, majd kiválasztja az optimális fa segítségével tetszőleges differenciálható veszteség funkció.  
  
További információt az alábbi cikkekben talál:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Ez a Wikipedia cikk a gradiens növelése nyújt némi háttér a felturbózott fák. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: A RankNet a LambdaRank a LambdaMART: Áttekintés. Készítette: J.C. Burges, ez az.

A gradiens kiemelési módszer osztályozási problémákra is használható, ha megfelelő veszteségfunkcióval regresszióvá csökkenti azokat. A kiemelt fák besorolási feladatokhoz való megvalósításáról további információt a [Kétosztályos kiemelt döntési fa című témakörben](./two-class-boosted-decision-tree.md)talál.  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>A kiemelt döntési fa regressziójának beállítása

1.  Adja hozzá a **Kiemelt döntési fa** modult a folyamathoz. Ez a modul a **Machine Learning**, Initialize ( **Inicicia**) csoportban található a **Regresszió kategóriában.** 
  
2.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.  
  
    -   **Egyetlen paraméter**: Akkor válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és argumentumként megadott értékkészletet adjon meg. 
     
    -   **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.    
   
  
3. **A levelek maximális száma egy fán:** Adja meg a bármely fán létrehozható terminálcsomópontok (levelek) maximális számát.  

    Ennek az értéknek a növelésével potenciálisan növelheti a fa méretét, és jobb pontosságot kap, a túlszerelés és a hosszabb képzési idő kockázatával.  

4. **A minták minimális száma levélcsomópontonként**: Adja meg a fa terminálcsomóinak (levéltörzsének) létrehozásához szükséges esetek minimális számát.

    Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, még egyetlen eset is új szabály létrehozását okozhatja. Ha az értéket 5-re növeli, a betanítási adatoknak legalább 5 olyan esetet kell tartalmazniuk, amelyek megfelelnek az azonos feltételeknek.

5. **Tanulási sebesség:** Írjon be egy 0 és 1 közötti számot, amely tanulási közben meghatározza a lépésméretet. A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassúan közelít az optimális megoldáshoz. Ha a lépés mérete túl nagy, előfordulhat, hogy túllépi az optimális megoldást. Ha a lépés mérete túl kicsi, a betanítás hosszabb időt vesz igénybe a legjobb megoldás hoz.

6. **Épített fák száma**: Adja meg az együttesben létrehozandó döntési fák teljes számát. Azáltal, hogy több döntés ifák, akkor potenciálisan jobb lefedettséget, de a képzési idő növekszik.

    Ez az érték azt is szabályozza, hogy a betanított modell megjelenítésekor hány fa jelenik meg. Ha egyetlen fát szeretne látni vagy nyomtatni, az értéket 1-re állíthatja; azonban csak egy fa keletkezik (a fa a paraméterek kezdeti készletével), és nem történik további ismétlés.

7. **Véletlenszám-vetőmag**: Írjon be egy választható nem negatív egész számot, amelyet véletlenszerű magértékként szeretne használni. A vetőmag megadása biztosítja a reprodukálhatóságot az azonos adatokkal és paraméterekkal rendelkező futtatások között.

    Alapértelmezés szerint a véletlenszerű mag értéke 0, ami azt jelenti, hogy a kezdeti magérték a rendszerórából származik.
  

9. Adjon hozzá egy betanítási adatkészletet és az egyik betanítási modult:

    - Ha az **Oktatói mód létrehozása** beállítást egy **paraméterre állítja**be, használja a [Betanítási modell](train-model.md) modult.  
  
    

10. Küldje el a folyamatot.  
  
## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A modell pontozási használatához csatlakoztassa a [Score Model](./score-model.md), új bemeneti példák értékeinek előrejelzéséhez.

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Betanított modell** jobb oldali paneljén, és kattintson az **Adatkészlet ikonregisztrálása** gombra. A betanított modell másolata modulként lesz mentve a modulfába, és nem frissül a folyamat egymást követő futtatásaikor.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 
