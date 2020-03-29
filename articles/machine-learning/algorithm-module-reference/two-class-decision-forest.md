---
title: 'Kétosztályos döntési erdő: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a kétosztályos döntési erdő modul az Azure Machine Learning hozzon létre egy gépi tanulási modell alapján a döntési erdők algoritmus.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: c9388da449e75dee00fd43af9a4e0407c46f597a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916710"
---
# <a name="two-class-decision-forest-module"></a>Kétosztályos döntési erdő modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal a döntési erdők algoritmusa alapján gépi tanulási modellt hozhat létre.  

Döntés erdők gyors, felügyelt együttes modellek. Ez a modul jó választás, ha legfeljebb két eredménnyel szeretné megjósolni a célt. 

## <a name="understanding-decision-forests"></a>A döntési erdők ismertetése

Ez a döntési erdő algoritmus egy együttes tanulási módszer, amelyet osztályozási feladatokra szántak. Ensemble módszerek alapján az általános elv, hogy ahelyett, hogy támaszkodva egy modell, akkor jobb eredményeket és egy általánosabb modell létrehozásával több kapcsolódó modellek és kombinálja őket valamilyen módon. Általában az együttes modellek jobb fedést és pontosságot biztosítanak, mint az egyes döntési fák. 

Sok módja van, hogy hozzon létre az egyes modellek és összekapcsolják őket egy együttest. Ez a konkrét végrehajtása döntés erdő művek épület több döntési fák, majd **szavazni** a legnépszerűbb kimeneti osztály. A szavazás az egyik legismertebb módszer az együttesmodell eredményeinek létrehozására. 

+ Számos egyedi besorolási fák jönnek létre, a teljes adatkészlet, de a különböző (általában randomizált) kiindulási pontok. Ez eltér a véletlenszerű erdőmegközelítéstől, amelyben az egyes döntési fák csak az adatok vagy szolgáltatások véletlenszerű részét használhatják.
+ A döntési erdőfa minden egyes fája nem normalizált gyakoriságú címkéket eredményez. 
+ Az összesítési folyamat összegzi ezeket a histogramokat, és normalizálja az eredményt, hogy megkapja az egyes címkék "valószínűségeket". 
+ A fák, amelyek nagy előrejelzés bizalom lesz nagyobb súlyt a végső döntést az együttes.

A döntési fák nak általában számos előnye van az osztályozási feladatokhoz:
  
- Nem lineáris döntési határokat rögzíthetnek.
- Sok adat betanításával és előrejelzésével hatékonyan használható a számítás és a memóriahasználat.
- A funkciókiválasztása integrálva van a képzési és osztályozási folyamatokba.  
- A fák zajos adatokat és számos funkciót képesek befogadni.  
- Ezek nem parametrikus modellek, ami azt jelenti, hogy különböző eloszlásokkal tudják kezelni az adatokat. 

Azonban az egyszerű döntési fák túlilleszkedhetnek az adatokra, és kevésbé általánosíthatók, mint a faegyüttesek.

További információ: [Decision Forests](https://go.microsoft.com/fwlink/?LinkId=403677).  

## <a name="how-to-configure"></a>Hogyan kell beállítani
  
1.  Adja hozzá a **kétosztályos döntési erdő** modult a folyamathoz az Azure Machine Learningben, és nyissa meg a modul **Tulajdonságok** ablaktábláját. 

    A modul a **Machine Learning**( machine learning ) alatt található. Bontsa ki **az Inicializálás,** majd **a Besorolás kibontását.**  
  
2.  Az **Újraszámítás módszerhez**válassza ki az egyes fák létrehozásához használt módszert.  A **zsákolás** vagy a **Replikálás**közül választhat.  
  
    -   **Zsákolás:** Zsákolás is *nevezik bootstrap összesítés*. Ebben a módszerben minden fa egy új mintán van kifejlődve, amelyet úgy hoz létre, hogy véletlenszerűen mintavételezi az eredeti adatkészletet csereként, amíg az adatkészlet mérete meg nem történik az eredeti méretével.  
  
         A modellek kimeneteit *szavazással*kombinálják, ami az összesítés egyik formája. Minden fa egy besorolási döntés erdő kimenetek egy nem normalizált gyakorisága hisztogram címkék. Az összesítés az, hogy összefoglalja ezeket a histogramok és normalizálja, hogy a "valószínűségek" minden címke. Ily módon, a fák, amelyek nagy előrejelzés bizalom lesz nagyobb súlyt a végső döntést az együttes.  
  
         További információ: A Bootstrap aggregating Wikipedia-bejegyzésében.  
  
    -   **Replikálás:** A replikáció során minden fa pontosan ugyanazokra a bemeneti adatokra van betanítva. Annak meghatározása, hogy melyik felosztási predikátumot használják minden facsomóponthoz, véletlenszerű marad, és a fák változatosak lesznek.   
  
3.  Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.  
  
    -   **Egyetlen paraméter:** Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumként megadhat egy adott értékkészletet.

    -   **Paramétertartomány**: Ha nem biztos a legjobb paraméterekben, megtalálhatja az optimális paramétereket a [Tune Model Hyperparameters](tune-model-hyperparameters.md) modul segítségével. Ön megad néhány értéktartományt, és a tréner a beállítások több kombinációján keresztül iterálja a legjobb eredményt adó értékek kombinációját.
  
4.  A **Döntési fák száma mezőbe**írja be az együttesben létrehozható döntési fák maximális számát. Azáltal, hogy több döntés ifák, akkor potenciálisan jobb lefedettséget, de a képzési idő növekszik.  
  
    > [!NOTE]
    >  Ez az érték azt is szabályozza, hogy a betanított modell megjelenítésekor hány fa jelenik meg. Ha egyetlen fát szeretne látni vagy nyomtatni, az értéket 1-re állíthatja. Azonban csak egy fa állítható elő (a fa a paraméterek kezdeti készletével), és nem történik további ismétlés.
  
5.  A **döntési fák maximális mélységéhez**írjon be egy számot a döntési fa maximális mélységének korlátozásához. A fa mélységének növelése növelheti a pontosságot, a túlszerelés és a megnövekedett képzési idő kockázatával.
  
6.  A **csomópontonkénti véletlenszerű felosztások száma**mezőbe írja be a fa egyes csomópontjainak létrehozásához használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) minden szintjén a jellemzők véletlenszerűen oszlanak meg.
  
7.  A **minták minimális száma levélcsomópontonként**, adja meg a minimális számú esetben, amelyek létrehozásához szükséges bármely terminál csomópont (levél) egy fa.
  
     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, még egyetlen eset is új szabály létrehozását okozhatja. Ha az értéket 5-re növeli, a betanítási adatoknak legalább öt olyan esetet kell tartalmazniuk, amelyek megfelelnek az azonos feltételeknek.  
  
8.  Jelölje be az **Ismeretlen értékek engedélyezése a kategorikus funkciókhoz** lehetőséget, ha a betanítási vagy érvényesítési készletekben ismeretlen értékekhez szeretne csoportot létrehozni. Előfordulhat, hogy a modell kevésbé pontos az ismert értékekhez, de jobb előrejelzéseket biztosíthat az új (ismeretlen) értékekhez. 

     Ha kijelöli ezt a beállítást, a modell csak a betanítási adatokban szereplő értékeket fogadhatja el.
  
9. Címkézett adatkészlet és az egyik [betanítási modul csatolása:](module-reference.md)  
  
    -   Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](./train-model.md) modult.  
    
## <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza a **Kimenetek** lapot a **Train modell** modul jobb oldali paneljén. Az **adatkészlet regisztrálása** ikonra lehetőséget választva mentse a modellt újrafelhasználható modulként.

+ A modell pontozási használatához adja hozzá a **Score Model** modult egy folyamathoz.

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 