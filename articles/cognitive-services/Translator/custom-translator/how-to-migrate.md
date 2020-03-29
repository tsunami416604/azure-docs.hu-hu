---
title: Áttelepíti a Microsoft Translator Hub munkaterületét és projektjeit? - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan telepítheti át a központi munkaterületet és a projekteket az Azure Cognitive Services egyéni fordítóba.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 2fa90a8099778bf37ce8534e968a2b1b4345c2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75446782"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Hub-munkaterület és projektek áttelepítése egyéni fordítóba

A Microsoft Translator [Hub](https://hub.microsofttranslator.com/) munkaterületét és projektjeit egyszerűen áttelepítheti az egyéni fordítóba. Az áttelepítés a Microsoft Hubról egy munkaterület vagy projekt kiválasztásával, majd egy munkaterület kijelölésével az Egyéni fordítóban, majd az átvinni kívánt betanítások kiválasztásával történik. Az áttelepítés megkezdése után a kiválasztott betanítási beállítások az összes vonatkozó dokumentummal együtt kerülnek átvitelre. Telepített modellek betanítása, és a befejezés után automatikusan telepíthető.

Ezek a műveletek az áttelepítés során történnek:
* Minden dokumentum és projektdefiníció neve a névhez "hub_" előtaggal lesz átruházva. Az automatikusan létrehozott teszt- és\<hangolási adatok\<neve hub_systemtune_ modelid> vagy hub_systemtest_ modelid> lesz.
* Minden olyan betanítást, amely az áttelepítés során üzembe helyezett állapotban voltak, automatikusan betanításra kerülnek a Hub-képzés dokumentumai alapján. Ez a képzés nem kerül felszámolásra az előfizetés. Ha az automatikus üzembe helyezés lett kiválasztva az áttelepítéshez, a betanított modell a befejezéskor lesz telepítve. Rendszeres hosting díjakat kell alkalmazni.
* Az áttelepített betanítások, amelyek nem voltak telepítve állapotban kerülnek az áttelepített vázlat állapotba kerül. Ebben az állapotban lehetősége lesz egy modell betanítására az áttelepített definícióval, de a rendszeres képzési díjak vonatkoznak.
* A Hub-képzésből áttelepített BLEU-pontszám bármikor megtalálható a modell TrainingDetails oldalán a "Bleu pontszám az MT Hubban" fejlécben.

> [!Note] 
> A sikeres képzéshez az egyéni fordítóhoz legalább 10 000 egyedi kibontott mondat szükséges. Az egyéni fordító nem tud a [javasolt minimumnál](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences)kevesebb elévüléssel rendelkező képzést végezni.

## <a name="find-custom-translator-workspace-id"></a>Egyéni fordító-munkaterület-azonosító keresése

A [Microsoft Translator Hub](https://hub.microsofttranslator.com/) munkaterületáttelepítéséhez az egyéni fordítóban található célmunkaterület-azonosítóra van szükség. Az egyéni fordító célmunkaterülete az a hely, ahová az összes hub-munkaterületet és projektet át kell telepíteni.

A cél munkaterület-azonosítóját az Egyéni fordító beállítások lapján találja:

1. Nyissa meg a "Beállítás" lapot az egyéni fordító portálon.

2. A munkaterület-azonosítót az Alapszintű információk szakaszban találja.

    ![A célmunkaterület-azonosító megkeresése](media/how-to/how-to-find-destination-ws-id.png)

3. Tartsa meg a cél munkaterület-azonosítóját, amelyre hivatkozni szeretne az áttelepítési folyamat során.

## <a name="migrate-a-project"></a>Projekt áttelepítése

Ha azt szeretné, hogy a projektek szelektíven, a Microsoft Translator Hub biztosítja ezt a képességet.

Projekt áttelepítése:

1. Jelentkezzen be a Microsoft Translator Hubba.

2. Nyissa meg a "Projektek" lapot.

3. Kattintson az "Áttelepítés" hivatkozásra a megfelelő projekthez.

    ![Az áttelepítés a Hubról](media/how-to/how-to-migrate-from-hub.png)

4. Az áttelepítési hivatkozás megnyomásakor megjelenik egy űrlap, amely lehetővé teszi, hogy:
   * Adja meg azt a munkaterületet, amelybe át szeretne vinni az egyéni fordítón
   * Adja meg, hogy az összes képzést sikeres képzésekkel kívánja-e átvinni, vagy csak a telepített képzéseket. Alapértelmezés szerint az összes sikeres képzés átkerül.
   * Adja meg, hogy szeretné-e telepíteni a betanítási automatikus betanítást, amikor a betanítás befejeződik. Alapértelmezés szerint a betanítás nem lesz automatikusan telepítve a befejezésután.

5. Kattintson a "Kérelem elküldése" gombra.

## <a name="migrate-a-workspace"></a>Munkaterület áttelepítése

Egyetlen projekt áttelepítése mellett átis telepítheti az összes sikeres képzést tartalmazó projektet egy munkaterületen. Ez azt eredményezi, hogy a munkaterület minden projektje úgy lesz kiértékelve, mintha az áttelepítési hivatkozást megnyomták volna. Ez a funkció olyan felhasználók számára alkalmas, akik számos projekttel rendelkeznek, akik mindegyiket át szeretnék telepíteni az egyéni fordítóba ugyanazzal a beállítással. A munkaterület áttelepítése kezdeményezhető a Translator Hub beállítások lapjáról.

Munkaterület áttelepítése:

1. Jelentkezzen be a Microsoft Translator Hubba.

2. Lépjen a "Beállítások" oldalra.

3. A "Beállítások" lapon kattintson a "Munkaterületadatainak áttelepítése az egyéni fordítóba" elemre.

    ![Az áttelepítés a Hubról](media/how-to/how-to-migrate-workspace-from-hub.png)

4. A következő oldalon válasszon a következő két lehetőség közül:

    a. Csak telepített betanítások: Ez a beállítás csak az üzembe helyezett rendszereket és a kapcsolódó dokumentumokat telepíti át.

    b. Minden sikeres képzés: Ha ezt a lehetőséget választja, az összes sikeres oktatóprogramot és kapcsolódó dokumentumot áttelepíti.

    c. Adja meg a cél munkaterület-azonosítóját az Egyéni fordítóban.

    ![Az áttelepítés a Hubról](media/how-to/how-to-migrate-from-hub-screen.png)

5. Kattintson a Kérelem küldése gombra.

## <a name="migration-history"></a>Áttelepítési előzmények

Miután kérte a munkaterület/ projekt áttelepítését a Hubról, az áttelepítési előzményeket az Egyéni fordítóbeállítások lapon találja.

Az áttelepítési előzmények megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a "Beállítás" lapot az egyéni fordító portálon.

2. A Beállítások lap Áttelepítési előzmények szakaszában kattintson az Áttelepítési előzmények elemre.

    ![Áttelepítési előzmények](media/how-to/how-to-migration-history.png)

Az Áttelepítési előzmények lap a következő információkat jeleníti meg összegzésként minden kért áttelepítéskor.

1. Áttelepítés: Az áttelepítési kérelmet beküldő felhasználó neve és e-mail címe

2. Áttelepítés bekapcsolva: Az áttelepítés dátuma és időbélyegzője

3. Projektek: A sikeresen áttelepített projektek áttelepítéséhez kért projektek száma.

4. Betanítások: A sikeresen áttelepített betanítások száma szükséges betanítások száma.

5. Dokumentumok: A sikeresen áttelepített dokumentumok áttelepítéséhez kért dokumentumok száma.

    ![Áttelepítési előzmények részletei](media/how-to/how-to-migration-history-details.png)

Ha részletesebb áttelepítési jelentést szeretne a projektekről, képzésekről és dokumentumokról, akkor csv-ként is rendelkezik az exportálásrészleteivel.

## <a name="implementation-notes"></a>Végrehajtási megjegyzések
* Az egyéni fordítóban még nem elérhető nyelvpárral rendelkező rendszerek csak az adatok eléréséhez vagy az egyéni fordítón keresztüli telepítés ük megszüntetése esetén érhetők el. Ezek a projektek a Projektek lapon "Nem érhető el" jelöléssel lesznek ellátva. Ahogy engedélyezzük az új nyelvi párok egyéni fordító, a projektek aktívvá válnak a betanítása és üzembe helyezése. 
* A projekt áttelepítése a Hubról az egyéni fordítóra nem lesz hatással a Hub-képzésekre vagy -projektekre. Nem törölünk projekteket vagy dokumentumokat a Hubról az áttelepítés során, és nem távolodunk el a modellektől.
* Projektenként csak egyszer telepíthet. Ha meg kell ismételnie egy projekt áttelepítését, kérjük, vegye fel velünk a kapcsolatot.
* Az Egyéni fordító támogatja az NMT nyelvpárokat angolra és angolról. [A támogatott nyelvek teljes listájának megtekintése.](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization) A hub nem igényel alapmodelleket, ezért több ezer nyelvet támogat. Nem támogatott nyelvi pár áttelepítése, azonban csak a dokumentumok és a projektdefiníciók áttelepítését hajtjuk végre. Nem fogjuk tudni beiktatni az új modellt. Továbbá ezek a dokumentumok és projektek inaktívként jelennek meg annak jelzésére, hogy jelenleg nem használhatók. Ha ezekhez a projektekhez és/vagy dokumentumokhoz támogatást adnak hozzá, azok aktívak és taníthatóvá válnak.
* Egyéni fordító jelenleg nem támogatja az egynyelvű betanítási adatokat. A nem támogatott nyelvi párokhoz hasonlóan áttelepítheti az egynyelvű dokumentumokat, de inaktívként jelennek meg mindaddig, amíg az egynyelvű adatok nem támogatottak.
* Egyéni fordító igényel 10k párhuzamos mondatokat annak érdekében, hogy a vonat. A Microsoft Hub kisebb adathalmazokon is betanítható. Ha egy olyan képzést telepítát, amely nem felel meg ennek a követelménynek, akkor nem lesz betanítva.

## <a name="custom-translator-versus-hub"></a>Egyéni fordító kontra központ

Ez a táblázat a Microsoft Translator Hub és az Egyéni fordító szolgáltatásait hasonlítja össze.

|   | Hub | Custom Translator |
|:-----|:----:|:----:|
|Testreszabási szolgáltatás állapota   | Általános elérhetőség  | Általános elérhetőség |
| Szöveges API-verzió  | 2. verzió    | V3  |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egységes beszédfelismerési szolgáltatások testreszabása | Nem    | Igen |
| Nincs nyomkövetés | Igen | Igen |

## <a name="new-languages"></a>Új nyelvek

Ha Ön olyan közösség vagy szervezet, amely a Microsoft Translator [custommt@microsoft.com](mailto:custommt@microsoft.com) új nyelvi rendszerének létrehozásán dolgozik, további információkért forduljon.

## <a name="next-steps"></a>További lépések

- [Modell betanítása](how-to-train-model.md).
- Kezdje el használni a telepített egyéni fordítási modellt a [Microsoft Translator Text API V3-on](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)keresztül.
