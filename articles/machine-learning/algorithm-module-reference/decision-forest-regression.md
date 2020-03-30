---
title: 'Döntési erdő regressziója: Modul hivatkozása'
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használhatja a döntési erdő regressziós modul az Azure Machine Learning ben hozzon létre egy regressziós modell alapján egy döntési fák együttese.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 63d90a5239e6bf350d8a6b66f35157e4c7d15aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456539"
---
# <a name="decision-forest-regression-module"></a>Döntési erdő regressziós modulja

Ez a cikk ismerteti a modul az Azure Machine Learning designer (előzetes verzió).

Ezzel a modullal hozzon létre egy regressziós modell alapján együttese döntési fák.

A modell konfigurálása után be kell tanítania a modellt egy címkézett adatkészlet és a [Modell betanítása](./train-model.md) modul használatával. A betanított modell ezután előrejelzéseket készíthet. 

## <a name="how-it-works"></a>Működés

Döntési fák nem parametrikus modellek, amelyek egyszerű tesztek sorozatát hajtják végre minden példányban, bináris fa adatstruktúrán haladva, amíg egy levélcsomópont (döntés) meg nem születik.

Döntés fák a következő előnyökkel:

- Ezek a hatékony mind a számítási és a memória használat a képzés és előrejelzés során.

- Nem lineáris döntési határokat képviselhetnek.

- Integrált funkciók kiválasztását és besorolását végzik, és zajos funkciók jelenlétében rugalmasak.

Ez a regressziós modell döntési fák együtteséből áll. A regressziós döntési erdőminden fája egy Gauss-eloszlást ad ki előrejelzésként. A fák együttese felett összesítést végeznek, hogy megtalálják a modell összes fájának együttes eloszlásához legközelebb eső Gauss-eloszlást.

Az algoritmus elméleti keretéről és megvalósításáról a következő cikkben olvashat: [Döntés erdők: Egységes osztályozási keretrendszer, regresszió, sűrűségbecslés, sokrétű tanulás és félig felügyelt tanulás](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>A döntési erdő regressziós modelljének konfigurálása

1. Adja hozzá a **döntési erdő regressziós** modult a folyamathoz. A modulaatervezőben a **Machine Learning**, **Initialize Model**és **Regression**területen található.

2. Nyissa meg a modul tulajdonságait, és az **Újramintavételi módszer esetében**válassza ki az egyes fák létrehozásához használt módszert.  A **zsákolás** vagy a **Replikálás**közül választhat.

    - **Zsákolás:** Zsákolás is *nevezik bootstrap összesítés*. A regressziós döntési erdőminden fája egy Gauss-eloszlást ad ki előrejelzés útján. Az összesítés, hogy megtalálja a Gauss-iak, akinek az első két pillanatban egyezik a pillanatokat a keverék Gauss-eloszlások adott kombinálásával minden eloszlások vissza az egyes fák.

         További információ: A [Bootstrap aggregating](https://wikipedia.org/wiki/Bootstrap_aggregating)Wikipedia-bejegyzésében talál.

    - **Replikálás:** A replikáció során minden fa pontosan ugyanazokra a bemeneti adatokra van betanítva. Annak meghatározása, hogy melyik felosztási predikátumot használják minden facsomóponthoz, véletlenszerű marad, és a fák változatosak lesznek.

         A képzési folyamatról a **Replikálás** lehetőséggel kapcsolatos további információkért [lásd: Döntéserdők a számítógépes látáshoz és az orvosi képelemzéshez. Criminisi és J. Shotton. Springer 2013.](https://research.microsoft.com/projects/decisionforests/)

3. Adja meg, hogyan szeretné betanítani a modellt az **Oktatói mód létrehozása** beállítás beállításával.

    - **Egyetlen paraméter**

      Ha tudja, hogyan szeretné konfigurálni a modellt, argumentumként megadhat egy adott értékkészletet. Lehet, hogy ezeket az értékeket kísérletezéssel tanulta meg, vagy útmutatásként kapta meg.

    - **Paramétertartomány**: Akkor válassza ezt a lehetőséget, ha nem biztos a legjobb paraméterekben, és paraméterkeresést szeretne futtatni. Válasszon ki egy értéktartományt, amely felett iterálni, és a [Tune Model Hyperparameters](tune-model-hyperparameters.md) iterálja az összes lehetséges kombinációa a megadott beállítások meghatározásához a hiperparaméterek, amelyek az optimális eredményt. 



4. A **Döntési fák száma**területen adja meg az együttesben létrehozandó döntési fák teljes számát. Azáltal, hogy több döntés fák, akkor potenciálisan jobb lefedettséget, de a képzési idő növekedni fog.

    > [!TIP]
    > Ez az érték azt is szabályozza, hogy a betanított modell megjelenítésekor hány fa jelenik meg. Ha egyetlen fát szeretne látni vagy nyomtatni, az értéket 1-re állíthatja; ez azonban azt jelenti, hogy csak egy fa keletkezik (a fa a paraméterek kezdeti készletével), és nem kerül sor további ismétlésekre.

5. A **döntési fák maximális mélységéhez**írjon be egy számot a döntési fa maximális mélységének korlátozásához. A fa mélységének növelése növelheti a pontosságot, a túlszerelés és a megnövekedett képzési idő kockázatával.

6. A **csomópontonkénti véletlenszerű felosztások száma**mezőbe írja be a fa egyes csomópontjainak létrehozásához használandó felosztások számát. A *felosztás* azt jelenti, hogy a fa (csomópont) minden szintjén a jellemzők véletlenszerűen oszlanak meg.

7. A **minták minimális száma levélcsomópontonként**, adja meg a minimális számú esetben, amelyek létrehozásához szükséges bármely terminál csomópont (levél) egy fa.

     Az érték növelésével növelheti az új szabályok létrehozásának küszöbértékét. Ha például az alapértelmezett érték 1, még egyetlen eset is új szabály létrehozását okozhatja. Ha az értéket 5-re növeli, a betanítási adatoknak legalább öt olyan esetet kell tartalmazniuk, amelyek megfelelnek az azonos feltételeknek.


9. Csatlakoztasson egy címkézett adatkészletet, jelöljön ki egy legfeljebb két eredményt tartalmazó címkeoszlopot, és csatlakozzon a [Betanítási modellhez.](./train-model.md)

    - Ha a **Trainer mód létrehozása** beállítást egy **paraméterre állítja**be, a modellbe tanítása a [Betanítási modell](./train-model.md) modul használatával történik.

   

10. Küldje el a folyamatot.

### <a name="results"></a>Results (Eredmények)

A képzés befejezése után:

+ A betanított modell pillanatképének mentéséhez válassza ki a betanítási **modult, majd váltson** a jobb oldali panel Kimenetek fülére. Kattintson a **Regisztráció modell**ikonra.  A mentett modell modulként található a modulfában. 

## <a name="next-steps"></a>További lépések

Tekintse meg az Azure Machine Learning [számára elérhető modulok készletét.](module-reference.md) 