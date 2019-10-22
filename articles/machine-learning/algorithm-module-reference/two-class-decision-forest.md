---
title: 'Kétosztályos döntési erdő: modul-hivatkozás'
titleSuffix: Azure Machine Learning service
description: Ismerje meg, hogyan használható a kétosztályos döntési erdő modul a Azure Machine Learning szolgáltatásban egy gépi tanulási modell létrehozásához a döntési erdők algoritmusa alapján.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 243f1774069f048d0e8a1ce11e3ac42e4e73f58b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693626"
---
# <a name="two-class-decision-forest-module"></a>Kétosztályos döntési erdő modul

Ez a cikk a Azure Machine Learning szolgáltatás vizuális felületének (előzetes verzió) modulját ismerteti.

Ezzel a modullal gépi tanulási modellt hozhat létre a döntési erdők algoritmusa alapján.  

A döntési erdők gyors, felügyelt Ensemble-modellek. Ez a modul jó választás, ha legfeljebb két kimenetet szeretne előre jelezni. 

## <a name="understanding-decision-forests"></a>Döntési erdők ismertetése

Ez a döntési erdő algoritmus a besorolási feladatokhoz készült együttes tanulási módszer. A Ensemble módszerei azon általános elv alapján működnek, amely nem egyetlen modellre támaszkodik, így jobb eredményeket érhet el, és egy általánosított modellt hozhat létre több kapcsolódó modell létrehozásával, és valamilyen módon kombinálhatja azokat. Az Ensemble-modellek általában jobb lefedettséget és pontosságot biztosítanak, mint az önálló döntési fák. 

Számos módon hozhat létre egyéni modelleket, és kombinálhatja őket egy együttesben. A döntési erdő adott implementációja több döntési fa kiépítése, majd a legnépszerűbb kimeneti osztályban való **szavazás** alapján működik. A szavazás az egyik jobb ismert módszer, amellyel az eredményeket egy Ensemble-modellben lehet létrehozni. 

+ Számos egyéni besorolási fa jön létre a teljes adatkészlet használatával, de eltérő (általában véletlenszerű) kezdőpontokkal. Ez eltér a véletlenszerű erdő megközelítéstől, amelyben az egyes döntési fák csak az adatok vagy szolgáltatások véletlenszerű részét használhatják.
+ A döntési erdő fájának minden fastruktúrája a címkék nem normalizált gyakorisági hisztogramját adja eredményül. 
+ Az összesítési folyamat összegzi ezeket a hisztogramokat, és normalizálja az eredményt az egyes címkék "valószínűségek" lekéréséhez. 
+ A magas előrejelző megbízhatósággal rendelkező fák nagyobb súlyt kapnak az Ensemble végső döntésében.

A döntési fák általában számos előnnyel rendelkeznek a besorolási feladatokhoz:
  
- A nem lineáris döntési határokat is rögzíthetik.
- Nagy mennyiségű adattal is betaníthat és megjósolhat, mivel ezek a számítási és memóriahasználat hatékonyak.
- A szolgáltatás kiválasztása a képzési és besorolási folyamatokban van integrálva.  
- A fák a zajos és sok funkciót is kielégítik.  
- Nem számszerű modellek, ami azt jelenti, hogy különböző eloszlásokkal kezelhetik az adatkezelést. 

Ugyanakkor az egyszerű döntési fák is overfit az adatmennyiséget, és kevésbé általánosítható, mint a faszerkezetek.

További információ: [döntési erdők](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Konfigurálás
  
1.  Adja hozzá a **kétosztályos döntési erdő** modult a folyamathoz Azure Machine learning, majd nyissa meg a modul **Tulajdonságok** paneljét. 

    A modul a **Machine learning**alatt található. Bontsa ki az **inicializálás**, majd a **besorolás**elemet.  
  
2.  Az **újramintavételezési módszernél**válassza ki az egyes fák létrehozásához használt módszert.  A **csomagok** és a **replikálás**lehetőség közül választhat.  
  
    -   **Poggyász**: a poggyászt rendszerindítási *összesítésnek*is nevezik. Ebben a módszerben minden fát egy új mintán termesztenek, és az eredeti adatkészlet véletlenszerűen történő mintavételezésével jön létre, és csak akkor történik meg, ha nem rendelkezik az eredeti méretű adatkészlettel.  
  
         A modellek kimeneteit a rendszer *szavazással*kombinálja, amely az Összesítés formáját képezi. A besorolási döntési erdőben lévő összes fa a címkék nem normalizált gyakorisági hisztogramját adja vissza. Az összesítés az, hogy összesítse ezeket a hisztogramokat, és normalizálja a "valószínűségek" kifejezést az egyes címkéknél. Ily módon a nagy előrejelzési megbízhatósággal rendelkező fák nagyobb súlyt kapnak az Ensemble végső döntésében.  
  
         További információ: a rendszerindítási összesítések Wikipedia-bejegyzése.  
  
    -   **Replikálás**: a replikáció során minden fát pontosan ugyanazok a bemeneti adatok képeznek. Az egyes facsomópontok esetében a kiosztott predikátumok véletlenszerűek maradnak, és a fák sokrétűek lesznek.   
  
3.  Határozza meg, hogyan kívánja képezni a modellt az **oktatói mód létrehozása** lehetőség beállításával.  
  
    -   **Egyetlen paraméter**: ha tudja, hogyan szeretné konfigurálni a modellt, megadhatja az értékek adott készletét argumentumként.
  
4.  A **döntési fák száma**mezőben adja meg az Ensemble-ban létrehozható döntési fák maximális számát. További döntési fák létrehozásával jobb lefedettséget érhet el, de a képzési idő növekszik.  
  
    > [!NOTE]
    >  Ez az érték a betanított modell megjelenítésekor megjelenő fák számát is szabályozza. Ha egyetlen fát szeretne látni vagy kinyomtatni, beállíthatja az értéket 1-re. Azonban csak egy fát lehet előállítani (a kezdeti paraméterekkel rendelkező fát), és a rendszer nem végez további iterációkat.
  
5.  **A döntési fák maximális mélységéhez**adjon meg egy számot, amely korlátozza a döntési fa maximális mélységét. A fa mélységének növelésével növelheti a pontosságot, és megnövelheti a beilleszkedő és a megnövekedett betanítási időt is.
  
6.  A **véletlenszerű felosztások száma a csomóponton**mezőben adja meg a fa egyes csomópontjainak kiépítésekor használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) egyes szintjeinek funkciói véletlenszerűen vannak osztva.
  
7.  A **minták minimális száma a levél csomópontjainál**adja meg az esetek minimális számát, amely szükséges ahhoz, hogy bármely terminál csomópont (levél) a fában legyen létrehozva.
  
     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, akkor akár egyetlen esetben is létrehozhat egy új szabályt. Ha az értéket 5-re emeli, a betanítási adatmennyiségnek legalább öt olyan esetet kellene tartalmaznia, amelyek megfelelnek ugyanazoknak a feltételeknek.  
  
8.  Jelölje be az **ismeretlen értékek engedélyezése a kategorikus funkciókhoz** lehetőséget, ha a képzési vagy ellenőrzési készletekben ismeretlen értékekhez szeretne csoportot létrehozni. Előfordulhat, hogy a modell kevésbé pontos az ismert értékeknél, de az új (ismeretlen) értékek jobb előrejelzését is lehetővé teszi. 

     Ha kijelöli ezt a beállítást, a modell csak a betanítási adatokban található értékeket fogadja el.
  
9. Egy címkézett adatkészlet és egy [betanítási modul](module-reference.md)csatolása:  
  
    -   Ha az **oktatói módot** **egyetlen paraméterként**állítja be, használja a [Train Model](./train-model.md) modult.  
  
    
## <a name="results"></a>Eredmények

A betanítás befejezése után:

+ Ha meg szeretné tekinteni az egyes iterációkban létrehozott fát, kattintson a jobb gombbal a [Train Model](./train-model.md) modul kimenetére, és válassza a **Megjelenítés**lehetőséget.
  
    Kattintson az egyes faszerkezetekre a felosztások részletezéséhez, és tekintse meg az egyes csomópontok szabályait.

+ A modell pillanatképének mentéséhez kattintson a jobb gombbal a **betanított modell** kimenetére, és válassza a **modell mentése**lehetőséget. A mentett modell nem frissül a folyamat egymást követő futtatásakor.

+ A modell pontozáshoz való használatához adja hozzá a **pontszám modell** modult egy folyamathoz.


## <a name="next-steps"></a>Következő lépések

Tekintse [meg Azure Machine learning szolgáltatás számára elérhető modulok készletét](module-reference.md) . 