---
title: 'Kétosztályos megnövelt döntési fa: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szolgáltatásban a kétosztályos növelt döntési famodult egy gépi tanulási modell létrehozásához, amely a megnövelt döntési fák algoritmusán alapul.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 812fb8d312b165bd43f600da520f390f9c6399fe
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128418"
---
# <a name="two-class-boosted-decision-tree-module"></a>Kétosztályos növelt döntési fa modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal létrehozhat egy gépi tanulási modellt, amely a megnövelt döntési fák algoritmusán alapul. 

A megnövelt döntési fa egy olyan együttes tanulási módszer, amelyben a második fa kijavítja az első fa hibáit, a harmadik fa kijavítja az első és a második fa hibáit, és így tovább.  Az előrejelzések a fák teljes egészére épülnek, amely az előrejelzést végzi.
  
Általában, ha megfelelően van konfigurálva, a megnövelt döntési fák a legkönnyebben használható módszerek, amelyekkel a gépi tanulási feladatok széles skáláját érhetik el. Ugyanakkor a memória-igényes tanulók egyike is, és a jelenlegi implementáció mindent megtart a memóriában. Ezért előfordulhat, hogy a megnövelt döntési famodell nem tudja feldolgozni a lineáris tanulók által kezelhető nagyméretű adatkészleteket.

## <a name="how-to-configure"></a>Konfigurálás

Ez a modul egy képzetlen besorolási modellt hoz létre. Mivel a besorolás egy felügyelt tanulási módszer, a modell betanításához olyan címkézett adatkészletre van szükség, amely tartalmazza az összes sor értékét tartalmazó címke oszlopot.

Ezt a modellt betaníthatja a betanítási [modell](././train-model.md)használatával. 

1.  A Azure Machine Learningban adja hozzá a megnövelt **döntési fa** modult a kísérlethez.
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.
  
    + **Egyetlen paraméter**: Ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek egy adott halmazát argumentumként.
  
  
3.  A **falevelek maximális számának**megadásához adja meg a fában létrehozható terminál-csomópontok (levelek) maximális számát.
  
     Ennek az értéknek a növelésével lehetőség van a fa méretének növelésére és jobb pontosságra, ha a túlillesztés és a hosszú betanítási idő fennáll.
  
4.  A **minták minimális száma a levél csomópontjainál**adja meg, hogy hány esetben szükséges a terminál csomópont (levél) létrehozása a fában.  
  
     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.
  
5.  A **tanulási arány**mezőben adjon meg egy 0 és 1 közötti számot, amely meghatározza a lépés méretét a tanulás során.  
  
     A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassabban konvergál az optimális megoldáson. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik.
  
6.  A **kiépített fák száma mezőben**adja meg az Ensemble-ben létrehozandó döntési fák teljes számát. További döntési fák létrehozásával lehetőség van jobb lefedettségre, de a képzési idő növekedni fog.
  
     Ez az érték a betanított modell megjelenítésekor megjelenő fák számát is szabályozza. Ha egyetlen fát szeretne megtekinteni vagy kinyomtatni, állítsa az értéket 1-re. Ha azonban így tesz, csak egy fát hoz létre (a kezdeti paraméterekkel rendelkező fát), és nem végez további iterációt.
  
7.  A **véletlenszerű számú magok**esetében opcionálisan adjon meg egy nem negatív egész számot, amelyet véletlenszerű mag értékként kíván használni. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.  
  
     A véletlenszerű magok alapértelmezett értéke 0, ami azt jelenti, hogy a kezdeti mag értékét a rendszer órája szerzi be.  A véletlenszerű magok használatával végzett egymást követő futtatások különböző eredményekkel rendelkezhetnek.
  

9. A modell betanítása.
  
    + Ha az **oktatói módot** **egyetlen paraméterként**állítja be, csatlakoztasson egy címkézett adatkészletet és a betanítási [modell](./train-model.md) modult.  
  
   
## <a name="results"></a>Results (Eredmények)

A modell képzésének befejezése után a jobb gombbal kattintson a [Train Model](./train-model.md) kimenetére az eredmények megtekintéséhez:

+ Ha meg szeretné tekinteni az egyes iterációkban létrehozott fát, válassza a **Megjelenítés**lehetőséget. 
+ A felosztások részletezéséhez és az egyes csomópontok szabályainak megtekintéséhez kattintson az egyes faszerkezetekre.


## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 