---
title: Modell részletei – egyéni a fordítót megtekintése
titleSuffix: Azure Cognitive Services
description: Modellek lapon minden projekt az egyes modellek, például a modell neve, a modell állapotának, BLEU pontszám, a képzés, hangolása, tesztelési mondat száma részleteket jeleníti meg.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 80ee99fc4490f1fdc591093f357c8db55a97abb9
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627458"
---
# <a name="view-model-details"></a>Modell részleteinek megtekintése

A modellek lapon, a projekt összes modellt, hogy a projekt jeleníti meg. Minden modell tanítása az adott projekt szerepel ezen a lapon.

A projektben egyes modell a következő modell részletei jelennek meg.

1.  Modell neve: Egy adott modell modell nevét jeleníti meg.

2.  Állapot: Egy adott modell állapotát jeleníti meg. Az új képzési elküldött állapota lesz, amíg nem fogadja el a rendszer. Az állapot változik adatfeldolgozás közben a szolgáltatás kiértékeli a dokumentumok tartalmát. A dokumentumok értékelése befejezésekor az állapot fut értékre vált, és lesz a mondatokat, a képzés, beleértve a finomhangolási és tesztelési részét képező számát lásd automatikusan létrehozza az Ön számára. Alább felsoroljuk a modell állapotának a modellek állapotát leíró.

    -  Elküldve: Itt adhatja meg, hogy a háttérrendszer a dokumentumokat az adott modell feldolgozása.

    -  TrainingQueued: Itt adhatja meg, hogy a képzés MT rendszerre, hogy a modell várólistára kerülnek.

    -  Futó: Itt adhatja meg, hogy a képzés futtatja a fő Célkiszolgáló rendszert, hogy a modell.

    -  Sikeres: Meghatározza, hogy a képzés sikeres volt, MT rendszerben, és a egy modell érhető el. Ez az állapot egy BLEU pontszám, hogy a modell jelennek meg.

    -  Regisztrálva: Itt adhatja meg, hogy a sikeres betanított modell elküldve a központi telepítés rendszer MT.

    -  Undeploying: Itt adhatja meg, hogy az üzembe helyezett modell undeploying-e.

    -  Nem telepített: Meghatározza, hogy a modell az eltávolításhoz folyamat sikeresen befejeződött.

    -  Képzési sikertelen: Itt adhatja meg, hogy a képzés sikertelen volt. Ha képzési hiba történik, próbálja megismételni a betanítási feladat. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk. Ne törölje a sikertelen modell.

    - DataProcessingFailed: Itt adhatja meg, hogy egy vagy több dokumentumot a modellhez tartozó adatok feldolgozása nem sikerült.

    - Sikertelen: Itt adhatja meg, hogy a modell üzembe helyezése sikertelen.

    - MigratedDraft: Megadja, hogy a modell a Vázlat állapot egyéni a fordítót a hubról migrálás után.

4.  BLEU pontszám: (kétnyelvű értékelési Understudy) BLEU pontszám modell, a fordítási rendszer minőségének jelző jeleníti meg. Ezt az értéket jelzi, hogy hogyan végezhető el a fordítási rendszer képzést keletkezett a fordítások illeszkedjenek a hivatkozási mondatokat, a teszt adatkészletben. A BLEU pontszám jelenik meg, ha a betanítás sikeresen befejeződött. Képzési nem teljes / sikertelen, ha nem jelenik meg semmilyen BLEU pontszámot.

5.  Képzési mondat száma: a mondatok betanítási készlete használt teljes számát jeleníti meg.

6.  Hangolási mondat száma: a mondatok set hangolása használt teljes számát jeleníti meg.

7.  Képzési mondat száma: a mondatok set teszteléshez használt teljes számát jeleníti meg.

8.  Monó mondat száma: a mondatok monó készletként használt teljes számát jeleníti meg.

9.  Üzembe helyezés művelet gomb: sikeresen betanított modell, a "Deploy" gombra, ha nem telepített mutatja. Ha a modell üzembe lett helyezve, egy "Undeploy" gomb jelenik meg.

10. Törlés: Használhatja erre a gombra, ha törli a modellt. A modell nem törlése a dokumentumokat, a modellek létrehozásához használt bármelyikét.

    ![Modell részleteinek megtekintése](media/how-to/how-to-view-model-details.png)

>[!Note]
>Hasonlítsa össze az eredeti rendszerekben az egymást követő betanítások, fontos, hogy a finomhangolási állítsa be, és állandó vizsgálat beállítása.

## <a name="view-model-training-details"></a>Modell betanítási részleteinek megtekintése

A képzési befejeződése után áttekintheti az részleteit megjelenítő oldalon a képzés részleteit. Válassza ki a projektet, keresse meg és válassza ki a modellek lapot, és válasszon ki egy modellt.

A modell lapon a két lap található: képzési részleteit és a teszteléshez.

1.  **Részletek betanítása:** ezen a lapon a képzés során használt dokumentum(ok) listáját jeleníti meg:

    -  Dokumentumok neve: Ez a mező jeleníti meg a dokumentum neve

    -  A dokumentum típusának: Ez a mező jeleníti meg, ha ez a dokumentum-e a párhuzamos / monó.

    -  Száma az adatforrás nyelvi Mondatkezdő: mezőt jeleníti meg ennyi mondatokat azok Forrásnyelv részeként.

    -  Célnyelv száma Mondatkezdő: mondatokat mező látható száma is Célnyelv részeként.

    -  Igazított mondatokat: Ebben a mezőben mondatokat száma látható rendelkezik lett igazítva egyéni fordító során igazítása folyamat.

    -  Használt mondatokat: Ez a mező mondatok számát jeleníti meg a képzés során már használták egyéni a fordítót.

    ![Modell betanítási részletei](media/how-to/how-to-model-training-details.png)

2.  **Teszt:** ezen a lapon egy sikeres képzéshez teszt részleteit jeleníti meg.

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [teszteredmények](how-to-view-system-test-results.md) és képzési eredmények elemzésével.
