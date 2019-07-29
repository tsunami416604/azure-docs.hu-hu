---
title: Modell részleteinek megtekintése – egyéni fordító
titleSuffix: Azure Cognitive Services
description: A modellek lap bármely projektben megjeleníti az egyes modellek részleteit, például a modell nevét, a modell állapotát, a BLEU pontszámát, a képzést, a hangolást, a tesztelési mondatok számát.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595562"
---
# <a name="view-model-details"></a>Modell részleteinek megtekintése

A Project alatti modellek lap a projekt összes modelljét megjeleníti. Ezen a lapon a projekthez betanított összes modell szerepel.

A projekt egyes modelljeinél ezek a részletek jelennek meg.

1.  Modell neve: Megjeleníti egy adott modell modelljének nevét.

2.  Állapot: Egy adott modell állapotát jeleníti meg. Az új képzés a beküldött állapotba kerül, amíg el nem fogadják. Az állapot az adatfeldolgozásra változik, miközben a szolgáltatás kiértékeli a dokumentumok tartalmát. Ha a dokumentumok kiértékelése befejeződött, az állapot fut értékre változik, és megtekintheti a képzés részét képező mondatok számát, beleértve az automatikusan létrehozott hangolási és tesztelési csoportokat is. Alább látható a modellek állapotát leíró modell állapotának listája.

    -  Beküldött Megadja, hogy a háttérrendszer dolgozza fel a modell dokumentumait.

    -  TrainingQueued: Azt adja meg, hogy a képzés várólistára kerüljön az MT rendszer számára a modell számára.

    -  Fut: Azt adja meg, hogy a képzés az MT rendszeren fut a modellhez.

    -  Sikerült Megadja, hogy az MT rendszer sikeres betanítása és a modell elérhető. Ebben az állapotban az adott modellhez tartozó BLEU-pontszám jelenik meg.

    -  Telepített Megadja, hogy a sikeres betanított modellt az MT rendszerbe kell elküldeni az üzembe helyezéshez.

    -  Központi telepítés: Megadja, hogy a központilag telepített modell telepítése megtörténjen.

    -  Nem telepített Megadja, hogy a modell központi telepítési folyamata sikeresen befejeződött.

    -  A képzés sikertelen: Megadja, hogy a betanítás sikertelen volt. Ha betanítási hiba történik, próbálja megismételni a betanítási feladatot. Ha a hiba továbbra is fennáll, vegye fel velünk a kapcsolatot. Ne törölje a sikertelen modellt.

    - DataProcessingFailed: Megadja, hogy az adatfeldolgozás nem sikerült egy vagy több olyan dokumentumhoz, amely a modellhez tartozik.

    - DeploymentFailed: Megadja, hogy a modell központi telepítése sikertelen volt.

    - MigratedDraft: Azt határozza meg, hogy a modell vázlat állapotban van-e az áttelepítést követően a hub-ról az egyéni fordítóra.

4.  BLEU pontszáma: megmutatja a modell (a kétnyelvű próbaverzió) pontszámát, amely a fordítási rendszerek minőségét mutatja. Ez a pontszám azt mutatja be, hogy a fordítási rendszer milyen módon hajtja végre a betanítás eredményeként létrejövő, a tesztelési adatkészletben szereplő hivatkozási mondatokat. A BLEU pontszám akkor jelenik meg, ha a képzés sikeresen befejeződött. Ha a képzés nem fejeződött be/nem sikerült, a BLEU egyik pontszáma sem jelenik meg.

5.  Betanítási mondatok száma: A betanítási készletként használt mondatok teljes számát jeleníti meg.

6.  Hangolási mondatok száma: A hangolási készletként használt mondatok teljes számát jeleníti meg.

7.  Betanítási mondatok száma: A tesztelési csoportként használt mondatok teljes számát jeleníti meg.

8.  Monó mondatok száma: A Mono-készletként használt mondatok teljes számát jeleníti meg.

9.  Üzembe helyezési művelet gomb: Egy sikeresen betanított modell esetében az "üzembe helyezés" gomb jelenik meg, ha nincs telepítve. Ha egy modellt telepítenek, megjelenik a "telepítés megkerülés" gomb.

10. Törlése Ezt a gombot akkor használhatja, ha törölni szeretné a modellt. A modell törlése nem törli a modell létrehozásához használt dokumentumokat.

    ![Modell részleteinek megtekintése](media/how-to/how-to-view-model-details.png)

>[!Note]
>Ugyanazon rendszerek egymást követő betanításának összehasonlításához fontos, hogy megőrizze a hangolási készletet és a tesztelési készlet állandó értékét.

## <a name="view-model-training-details"></a>Modell tanítási adatainak megtekintése

A képzés befejezésekor a részletek oldalon áttekintheti a képzés részleteit. Válasszon ki egy projektet, keresse meg és válassza ki a modellek fület, és válassza ki a modellt.

A modell lapon két lap található: Képzés részletei és tesztelés.

1.  **Képzés részletei:** Ezen a lapon a képzésben használt dokumentum (ok) listája látható:

    -  Dokumentumok neve: Ebben a mezőben a dokumentum neve látható

    -  Dokumentum típusa: Ebben a mezőben látható, hogy a dokumentum párhuzamos/monó-e.

    -  Mondatok száma a forrás nyelvén: Ebben a mezőben a forrás nyelvének részét képező mondatok száma látható.

    -  Mondatok száma a célként megadott nyelven: Ebben a mezőben a mondatok száma látható a cél nyelvének részeként.

    -  Igazított mondatok: Ebben a mezőben a mondatok száma látható az egyéni fordító által az igazítási folyamat során.

    -  Használt mondatok: Ebben a mezőben a betanítás során az egyéni fordító által használt mondatok száma látható.

    ![Modell képzésének részletei](media/how-to/how-to-model-training-details.png)

2.  **Teszt** Ezen a lapon látható a sikeres betanítási teszt részletei.

## <a name="next-steps"></a>További lépések

- Tekintse át a [teszteredmények](how-to-view-system-test-results.md) eredményeit, és elemezze a képzési eredményeket.
