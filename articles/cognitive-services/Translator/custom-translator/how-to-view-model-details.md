---
title: Modell adatok megtekintése - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Modellek lap alatt minden projekt megjeleníti az egyes modellek részleteit, mint például a modell neve, modell állapota, BLEU pontszám, betanítás, hangolás, tesztelési mondatszám.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595562"
---
# <a name="view-model-details"></a>Modell részleteinek megtekintése

A Projekt alatt található Modellek lap az adott projekt összes modelljét megjeleníti. Az adott projekthez betanított összes modell szerepel ezen a lapon.

A projekt minden egyes modelljéhez ezek az adatok jelennek meg.

1.  Modell neve: Egy adott modell modellnevét jeleníti meg.

2.  Állapot: Egy adott modell állapotát jeleníti meg. Az új képzés állapota Elküldve, amíg el nem fogadják. Az állapot adatfeldolgozásra változik, miközben a szolgáltatás kiértékeli a dokumentumok tartalmát. Amikor a dokumentumok kiértékelése befejeződött, az állapot Futás állapotra változik, és láthatja a betanítás részét szolgáló mondatok számát, beleértve az automatikusan létrehozott hangolási és tesztelési készleteket is. Az alábbiakban a modellek állapotát leíró modellállapotot ismertetjük.

    -  Elküldött: Megadja, hogy a háttér-háttérfeldolgozás a dokumentumokat az adott modellhez.

    -  TrainingQueued: Itt adhatja meg, hogy a betanítás az adott modell MT-rendszeréhez kerül-e várólistára.

    -  Futás: Megadja, hogy a betanítás az adott modell MT-rendszerében fut-e.

    -  Sikeres: Itt adhatja meg, hogy a betanítás sikeres volt-e az MT rendszerben, és rendelkezésre áll-e egy modell. Ebben az állapotban bleu pontszám jelenik meg az adott modellhez.

    -  Üzembe helyezett: Itt adhatja meg, hogy a sikeres betanított modell az MT-rendszerbe kerül-e üzembe helyezésre.

    -  Telepítés kihelyezésének kihelyezése: Megadja, hogy az üzembe helyezett modell nem központitelepítés.

    -  Nem feltett: Itt adhatja meg, hogy a modell leépítési folyamata sikeresen befejeződött.Undeployd: Specifies that the undeployment process of a model has been completed successfully.

    -  Az oktatás sikertelen: Azt adja meg, hogy a betanítás nem sikerült. Ha betanítási hiba történik, próbálkozzon újra a betanítási feladat. Ha a hiba továbbra is fennáll, lépjen kapcsolatba velünk. Ne törölje a hibás modellt.

    - DataProcessingFailed: Megadja, hogy az adatfeldolgozás sikertelen volt a modellhez tartozó egy vagy több dokumentum esetében.

    - Telepítéssikertelenül: Megadja, hogy a modell központi telepítése sikertelen volt.

    - MigratedDraft: Itt adhatja meg, hogy a modell a Hubról az egyéni fordítóra való áttelepítés után vázlat állapotban van-e.

4.  BLEU pontszám: a modell BLEU (Kétnyelvű értékelés beugró) pontszámát mutatja, jelezve a fordítási rendszer minőségét. Ez a pontszám megmutatja, hogy a fordítási rendszer által végzett fordítások, amelyek ennek a betanításnak a szorosan megegyeznek a tesztadatkészlet referenciamondataival. A BLEU pontszám jelenik meg, ha a képzés sikeresen befejeződött. Ha a képzés nem teljes / sikertelen, akkor nem fog látni bleu pontszámot.

5.  Képzési mondatok száma: Az edzéskészletként használt mondatok teljes számát jeleníti meg.

6.  Hangolási mondatok száma: A hangolási készletként használt mondatok teljes számát jeleníti meg.

7.  Betanítási mondatok száma: A tesztelési halmazként használt mondatok teljes számát jeleníti meg.

8.  Mono Sentence count: A mono készletként használt mondatok teljes számát jeleníti meg.

9.  Művelet telepítése gomb: Egy sikeresen betanított modell, azt mutatja, "Üzembe helyezés" gombot, ha nincs telepítve. Ha egy modell telepítve van, egy "Undeploy" gomb jelenik meg.

10. Törlés: Ezt a gombot használhatja, ha törölni szeretné a modellt. A modell törlése nem törli a modell létrehozásához használt dokumentumokat.

    ![Modell részleteinek megtekintése](media/how-to/how-to-view-model-details.png)

>[!Note]
>Az ugyanazon rendszerek egymást követő edzései összehasonlításához fontos, hogy a hangolási készlet és a tesztelési készlet állandó maradjon.

## <a name="view-model-training-details"></a>Modellbetanítási részletek megtekintése

Amikor a képzés befejeződött, áttekintheti a részleteket a képzés a részleteket oldalon. Jelöljön ki egy projektet, keresse meg és jelölje ki a modellek lapot, és válasszon egy modellt.

A modelloldal két lappal rendelkezik: A betanítás részletei és a Tesztelés.

1.  **Képzési részletek:** Ez a lap a képzésben használt dokumentumok listáját jeleníti meg:

    -  Bizonylatneve: Ebben a mezőben a dokumentum neve látható.

    -  Bizonylattípus: Ez a mező azt mutatja, hogy ez a dokumentum párhuzamos/mono.

    -  Mondatok száma forrásnyelven: Ez a mező a forrásnyelv részeként a mondatok számát mutatja.

    -  Mondatok száma célnyelven: Ez a mező azt mutatja, hogy a célnyelv részeként több mondat található.

    -  Igazított mondatok: Ez a mező azt mutatja, hogy az egyéni fordító a folyamat során több mondatot igazított.

    -  Használt mondatok: Ez a mező azt mutatja, hogy az egyéni fordító a képzés során hány mondatot használt.

    ![Modellbetanítási részletek](media/how-to/how-to-model-training-details.png)

2.  **Vizsgálat:** Ez a lap a sikeres betanítás tesztrészleteit jeleníti meg.

## <a name="next-steps"></a>További lépések

- Tekintse át [a teszteredményeket,](how-to-view-system-test-results.md) és elemezze a betanítási eredményeket.
