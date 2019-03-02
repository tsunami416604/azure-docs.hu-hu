---
title: Modell részletei – egyéni a fordítót megtekintése
titleSuffix: Azure Cognitive Services
description: Modellek lapon minden projekt az egyes modellek, például a modell neve, a modell állapotának, BLEU pontszám, a képzés, hangolása, tesztelési mondat száma részleteket jeleníti meg.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 9dcfb40835202849761d5d81728caf6be63d9641
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57215472"
---
# <a name="view-model-details"></a>Modell részleteinek megtekintése

A modellek lapon, a projekt összes modellt, hogy a projekt jeleníti meg. Minden modell tanítása az adott projekt szerepel ezen a lapon.

A projektben egyes modellekre ezek az adatok jelennek meg.

1.  Modell neve: Egy adott modell modell nevét jeleníti meg.

2.  Állapot: Egy adott modell állapotát jeleníti meg. Az új képzési elküldött állapota lesz, amíg nem fogadja el a rendszer. Az állapot változik adatfeldolgozás közben a szolgáltatás kiértékeli a dokumentumok tartalmát. A dokumentumok értékelése befejezésekor az állapot fut értékre vált, és lesz a mondatokat, a képzés, beleértve a finomhangolási és tesztelési részét képező számát lásd automatikusan létrehozza az Ön számára. Alább felsoroljuk a modell állapotának a modellek állapotát leíró.

    -  Elküldve: Itt adhatja meg, hogy a háttérrendszer a dokumentumokat az adott modell feldolgozása.

    -  TrainingQueued: Itt adhatja meg, hogy a képzés MT rendszerre, hogy a modell várólistára kerülnek.

    -  Fut: Itt adhatja meg, hogy a képzés futtatja a fő Célkiszolgáló rendszert, hogy a modell.

    -  Sikeres: Megadja, hogy a képzés sikeres volt az MT rendszer, és egy modell érhető el. Ez az állapot egy BLEU pontszám, hogy a modell jelennek meg.

    -  Regisztrálva: Itt adhatja meg, hogy a sikeres betanított modell elküldve a központi telepítés rendszer MT.

    -  Undeploying: Itt adhatja meg, hogy az üzembe helyezett modell undeploying-e.

    -  Telepítését eltávolították: Itt adhatja meg, hogy a modell az eltávolításhoz folyamat sikeresen befejeződött.

    -  Nem sikerült a képzés: Itt adhatja meg, hogy a képzés sikertelen volt. Ha képzési hiba történik, próbálja megismételni a betanítási feladat. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk. Ne törölje a sikertelen modell.

    - DataProcessingFailed: Itt adhatja meg, hogy egy vagy több dokumentumot a modellhez tartozó adatok feldolgozása nem sikerült.

    - Sikertelen: Itt adhatja meg, hogy a modell üzembe helyezése sikertelen.

    - MigratedDraft: Megadja, hogy a modell a Vázlat állapot egyéni a fordítót a hubról migrálás után.

4.  BLEU pontszám: (kétnyelvű értékelési Understudy) BLEU pontszám modell, a fordítási rendszer minőségének jelző jeleníti meg. Ezt az értéket jelzi, hogy hogyan végezhető el a fordítási rendszer képzést keletkezett a fordítások illeszkedjenek a hivatkozási mondatokat, a teszt adatkészletben. A BLEU pontszám jelenik meg, ha a betanítás sikeresen befejeződött. Képzési nem teljes / sikertelen, ha nem jelenik meg semmilyen BLEU pontszámot.

5.  Képzési mondat száma: Látható teljes száma a mondatok használt betanítási készlete.

6.  Hangolási mondat száma: Hangolási set használt mondatokat száma összesen jeleníti meg.

7.  Képzési mondat száma: Mondatokat számát jeleníti meg a tesztelési készlethez használható.

8.  Monó mondat száma: Monó készletként használt mondatokat száma összesen jeleníti meg.

9.  Helyezze üzembe a művelet gombra: Egy sikeres betanított modell azt látható "Üzembe helyezés" gomb Ha nem telepített. Ha a modell üzembe lett helyezve, egy "Undeploy" gomb jelenik meg.

10. Törlés: Ez a gomb is használhatja, ha törli a modellt. A modell nem törlése a dokumentumokat, a modellek létrehozásához használt bármelyikét.

    ![Modell részleteinek megtekintése](media/how-to/how-to-view-model-details.png)

>[!Note]
>Hasonlítsa össze az eredeti rendszerekben az egymást követő betanítások, fontos, hogy a finomhangolási állítsa be, és állandó vizsgálat beállítása.

## <a name="view-model-training-details"></a>Modell betanítási részleteinek megtekintése

A képzési befejeződése után áttekintheti az részleteit megjelenítő oldalon a képzés részleteit. Válassza ki a projektet, keresse meg és válassza ki a modellek lapot, és válasszon ki egy modellt.

A modell lapon a két lap található: Képzési részletek és a teszteléshez.

1.  **Betanítási adatok:** Ezen a lapon a képzés során használt dokumentum(ok) listáját jeleníti meg:

    -  Dokumentum neve: Ez a mező jeleníti meg a dokumentum neve

    -  A dokumentum típusa: Ez a mező jeleníti meg, ha ez a dokumentum párhuzamos / monó-e.

    -  A Forrásnyelv mondat száma: A mező látható száma a mondatok is Forrásnyelv részeként.

    -  Célnyelv mondat száma: A mező látható száma a mondatok is Célnyelv részeként.

    -  Igazított mondatokat: Ez a mező mondatokat száma látható rendelkezik lett igazítva egyéni fordító során folyamat igazítása

    -  Mondatok használni: Ez a mező mondatok számát jeleníti meg a képzés során már használták egyéni a fordítót.

    ![Modell betanítási részletei](media/how-to/how-to-model-training-details.png)

2.  **Teszt:** Ezen a lapon egy sikeres képzéshez teszt részleteit jeleníti meg.

## <a name="next-steps"></a>További lépések

- Felülvizsgálat [teszteredmények](how-to-view-system-test-results.md) és képzési eredmények elemzésével.
