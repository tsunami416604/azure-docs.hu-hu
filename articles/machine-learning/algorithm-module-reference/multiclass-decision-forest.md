---
title: 'Többosztályos döntési erdő: modul hivatkozási'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a többosztályos döntési erdő modul az Azure Machine Learning hozzon létre egy gépi tanulási modell alapján a *döntési erdő* algoritmus.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 9a0a70f94be337eedf8f8ba4cc17af896f7a03b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477544"
---
# <a name="multiclass-decision-forest-module"></a>Többosztályos döntési erdő modul

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy gépi tanulási modell t a döntési erdő algoritmus a *döntési erdő* algoritmus a. A döntési erdő egy együttes modell, amely gyorsan épít egy sor döntési fák, miközben a címkézett adatokból tanul.

## <a name="more-about-decision-forests"></a>További információk a döntési erdőkről

A döntési erdő algoritmus egy együttes tanulási módszer osztályozás. Az algoritmus úgy működik, hogy több döntési fát épít, majd a legnépszerűbb kimeneti osztályra *szavaz.* A szavazás az összesítés egy formája, amelyben a besorolási döntésben részt vevő erdő minden egyes fája a címkék nem normalizált gyakoriságú hisztogramját adja elő. Az összesítési folyamat összegzi ezeket a histogramokat, és normalizálja az eredményt, hogy megkapja az egyes címkék "valószínűségeket". A fák, amelyek nagy előrejelzés bizalom nagyobb súlyt a végső döntést az együttes.

A döntési fák általában nem parametrikus modellek, ami azt jelenti, hogy különböző eloszlású adatokat támogatnak. Minden fán egyszerű tesztek sorozata fut minden osztályhoz, növelve a fastruktúra szintjét, amíg egy levélcsomópont (döntés) el nem ér.

Döntés fák számos előnye van:

+ Nem lineáris döntési határokat képviselhetnek.
+ Hatékonyak a számítás és a memória használat a képzés és előrejelzés során.
+ Integrált funkciók kiválasztását és besorolását végzik.
+ Zajos funkciók jelenlétében rugalmasak.

A döntési erdő osztályozó az Azure Machine Learning egy döntési fák együttese. Általában az együttes modellek jobb fedést és pontosságot biztosítanak, mint az egyes döntési fák. További információ: [Decision trees](https://go.microsoft.com/fwlink/?LinkId=403677).

## <a name="how-to-configure-multiclass-decision-forest"></a>A többosztályos döntési erdő beállítása

1. Adja hozzá a **többosztályos döntési erdő** modult a tervező ben lévő folyamathoz. Ezt a modult a **Machine Learning**, **Initialize Model**és **Classification**területen találja.

2. Kattintson duplán a modulra a **Tulajdonságok** ablaktábla megnyitásához.

3. Az **Újraszámítás módszerhez**válassza ki az egyes fák létrehozásához használt módszert.  A zsákolás vagy a replikáció közül választhat.

    + **Zsákolás:** Zsákolás is *nevezik bootstrap összesítés*. Ebben a módszerben minden fa egy új mintán van kifejlődve, amelyet úgy hoz létre, hogy véletlenszerűen mintavételezi az eredeti adatkészletet csereként, amíg az adatkészlet mérete meg nem történik az eredeti méretével. A modellek kimeneteit *szavazással*kombinálják, ami az összesítés egyik formája. További információ: A Bootstrap aggregating Wikipedia-bejegyzésében.

    + **Replikálás:** A replikáció során minden fa pontosan ugyanazokra a bemeneti adatokra van betanítva. Annak meghatározása, hogy melyik felosztási predikátumot használják az egyes facsomópontokhoz, véletlenszerű marad, különböző fákat hozva létre.

   

4. Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.

    + **Egyetlen paraméter**: Akkor válassza ezt a lehetőséget, ha tudja, hogyan szeretné konfigurálni a modellt, és argumentumként adja meg az értékek készletét.

    + **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt.   

5. **Döntési fák száma**: Írja be az együttesben létrehozható döntési fák maximális számát. Azáltal, hogy több döntési fák, akkor potenciálisan jobb lefedettséget, de a képzési idő növelheti.

    Ez az érték azt is szabályozza, hogy a betanított modell megjelenítésekor hány fa jelenik meg az eredményekben. Egyetlen fa megtekintéséhez vagy nyomtatásához az értéket 1-re állíthatja; ez azonban azt jelenti, hogy csak egy fa állítható elő (a fa a paraméterek kezdeti készletével), és nem történik további ismétlés.

6. **A döntési fák maximális mélysége**: Írjon be egy számot a döntési fa maximális mélységének korlátozásához. A fa mélységének növelése növelheti a pontosságot, a túlszerelés és a megnövekedett képzési idő kockázatával.

7. **Csomópontonkénti véletlenszerű felosztások száma**: Írja be a fa egyes csomópontjainak létrehozásához használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) minden szintjén a jellemzők véletlenszerűen oszlanak meg.

8. **A minták minimális száma levélcsomópontonként**: Adja meg a fa bármely terminálcsomó (levél) létrehozásához szükséges esetek minimális számát. Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét.

    Ha például az alapértelmezett érték 1, még egyetlen eset is új szabály létrehozását okozhatja. Ha az értéket 5-re növeli, a betanítási adatoknak legalább öt olyan esetet kell tartalmazniuk, amelyek megfelelnek az azonos feltételeknek.



10. Címkecímkével ellátott adatkészlet és a betanítási modulok egyikének csatlakoztatása:

    + Ha az **Oktató létrehozása módot** egy **paraméterre állítja**be, használja a [Betanítási modell](./train-model.md) modult.

11. Küldje el a folyamatot.



## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 