---
title: 'Megnövelt döntési fa regressziója: Modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Megtudhatja, hogyan használhatja a Azure Machine Learning Service-ben a megnövelt döntési fa regressziós modult a regressziós fák együttes létrehozásához a boosting használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: de4135c2e516eb7f26e1b99a22a60501f4577cce
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128961"
---
# <a name="boosted-decision-tree-regression-module"></a>A döntési fa regressziós moduljának növelése

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal a regressziós fák együttesét hozhatja létre a boosting használatával. A *kiemelés* azt jelenti, hogy az egyes faszerkezetek az előző fáktől függenek. Az algoritmus az előtte lévő fák maradékának beépítésével tanul. Így a döntési fazenekarban egyre nagyobb a pontosság, és kisebb a lefedettségük.  
  
Ez a regressziós módszer egy felügyelt tanulási módszer, ezért címkével *ellátott*adatkészletet igényel. A Label oszlopnak numerikus értékeket kell tartalmaznia.  

> [!NOTE]
> Ezt a modult csak numerikus változókat használó adatkészletekkel használhatja.  

A modell meghatározása után betaníthatja azt a betanítási [modell](./train-model.md)használatával.

> [!TIP]
> Szeretne többet megtudni a létrehozott facsemetékről? A modell képzését követően kattintson a jobb gombbal a [Train Model](./train-model.md) modul kimenetére, és válassza a **Megjelenítés** lehetőséget az egyes iterációk során létrehozott fa megtekintéséhez. Megtekintheti az egyes faszerkezetek felosztását, és megtekintheti az egyes csomópontok szabályait.  
  
## <a name="more-about-boosted-regression-trees"></a>További információ a regressziós fák növeléséről  

A boosting számos klasszikus módszer az Ensemble-modellek létrehozásához, valamint a csomagok, a véletlenszerű erdők és így tovább.  Azure Machine Learning a megnövelt döntési fák a MART gradiens-növelő algoritmus hatékony megvalósítását használják. A színátmenet-növelés a gépi tanulási módszer a regressziós problémákhoz. Az egyes regressziós faszerkezeteket egy lépésenkénti, előre definiált Loss függvénnyel méri, hogy az egyes lépésekben a hibát megmérjük, és a következőben javítsa. Így az előrejelzési modell tulajdonképpen a gyengébb előrejelző modellek együttese.  
  
A regressziós problémák esetében a kibővíti a fák egy sorozatát egy részletesen megjelenő módon, majd kiválasztja az optimális fát egy tetszőleges differentiable Loss függvény használatával.  
  
További információkért tekintse meg a következő cikkeket:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Ez a wikipedia-cikk a színátmenet-növeléssel kapcsolatban nyújt némi hátteret a felerősített fákat illetően. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: A RankNet és a LambdaRank közötti LambdaMART: Áttekintés. J.C. szerint Burges.

A színátmenet-növelési módszer a besorolási problémákhoz is használható, ha csökkenti a regressziót egy megfelelő veszteség-függvénnyel. A besorolási feladatokhoz kapcsolódó felerősített fák megvalósításával kapcsolatos további információkért lásd: kétosztályos megnövelt [döntési fa](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>A megnövelt döntési fa regressziójának konfigurálása

1.  Adja hozzá a megnövelt **döntési fa** modult a kísérlethez. Ezt a modult **Machine learning**, **inicializálás**, a regressziós kategória alatt találja. 
  
2.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: Válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és adjon meg egy adott értékeket argumentumként.  
   
  
3. **Levelek maximális száma fa szerint**: A fában létrehozható terminál-csomópontok (levelek) maximális számát adja meg.  

    Ennek az értéknek a növelésével lehetőség van a fa méretének növelésére és jobb pontosságra, ha a túlillesztés és a hosszú betanítási idő fennáll.  

4. **Minták minimális száma a levél csomópontjain**: Itt adhatja meg, hogy a fában egy terminál-csomópont (levél) létrehozásához milyen esetek minimális száma szükséges.

    Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább 5 olyan esetet kell tartalmaznia, amely ugyanazokat a feltételeket teljesíti.

5. **Tanulási arány**: Írjon be egy 0 és 1 közötti számot, amely meghatározza a lépés méretét a tanulás során. A tanulási arány határozza meg, hogy a tanuló milyen gyorsan vagy lassabban konvergál az optimális megoldáson. Ha a lépés mérete túl nagy, lehet, hogy az optimális megoldást is lelövi. Ha a lépés mérete túl kicsi, a képzés továbbra is a legjobb megoldáshoz közeledik.

6. **A kiépített fák száma**: Az Ensemble-ban létrehozandó döntési fák teljes számát adja meg. További döntési fák létrehozásával jobb lefedettséget érhet el, de a képzési idő növekszik.

    Ez az érték a betanított modell megjelenítésekor megjelenő fák számát is szabályozza. Ha szeretné megtekinteni vagy kinyomtatni egy Ingle fát, beállíthatja az értéket 1-re; a rendszer azonban csak egy fát állít elő (a kezdeti paraméterekkel rendelkező fát), és nem végez további iterációt.

7. **Véletlenszerű számú mag**: Adjon meg egy nem negatív egész számot, amelyet véletlenszerű mag értékként kíván használni. A vetőmagok megadásával biztosítható az azonos adatokkal és paraméterekkel rendelkező futtatások közötti reprodukálhatóság.

    Alapértelmezés szerint a véletlenszerű mag értéke 0, ami azt jelenti, hogy a kezdeti mag értékét a rendszer órája szerzi be.
  
8. **Ismeretlen kategorikus szintek engedélyezése**: Válassza ezt a lehetőséget, ha a betanítási és az érvényesítési készletekben ismeretlen értékekhez szeretne csoportot létrehozni. Ha kijelöli ezt a beállítást, a modell csak a betanítási adatokban található értékeket fogadja el. Előfordulhat, hogy a modell kevésbé pontos az ismert értékeknél, de az új (ismeretlen) értékek jobb előrejelzését is lehetővé teszi.

9. Vegyen fel egy betanítási adatkészletet és egy betanítási modult:

    - Ha az **oktatói mód létrehozása** lehetőséget állítja be **egyetlen paraméterként**, használja a [Train Model](train-model.md) modult.  
  
    

10. Futtassa a kísérletet.  
  
### <a name="results"></a>Results (Eredmények)

A betanítás befejezése után:

+ Ha meg szeretné tekinteni az egyes iterációkban létrehozott fát, kattintson a jobb gombbal a [Train Model](train-model.md) modul kimenetére, és válassza a **Megjelenítés**lehetőséget.
  
     Kattintson az egyes faszerkezetekre a felosztások részletezéséhez, és tekintse meg az egyes csomópontok szabályait.  

+ Ha a modellt a pontozáshoz szeretné használni, kapcsolja össze a [pontszám modellel](./score-model.md), hogy megjósolja az új bemeneti példák értékeit.

+ A betanított modell pillanatképének mentéséhez kattintson a jobb gombbal az oktatási modul betanított **modell** kimenetére, és válassza a **Mentés másként**lehetőséget. A létrehozott betanított modell másolata nem frissül a kísérlet egymást követő futtatása során.

## <a name="next-steps"></a>További lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 