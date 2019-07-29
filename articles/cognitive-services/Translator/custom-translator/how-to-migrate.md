---
title: A Microsoft Translator Hub munkaterületet és projektek migrálni? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Telepítse át a Hub munkaterület és projektek egyéni a fordítót.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: cd821ad8fce813d269ace8fb4945cb796c2ae758
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595733"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Egyéni a fordítót Hub munkaterületet és projektek áttelepítése

Egyszerűen áttelepítheti a [Microsoft Translator Hub](https://hub.microsofttranslator.com/) munkaterületet és projektek egyéni a fordítót. Jelölje ki a egy munkaterületet, vagy a projektet, majd egyéni a fordítót kell választania egy munkaterületet, és válassza az átvinni kívánt betanítások Microsoft Hub áttelepítési kezdeményezni. Az áttelepítés indítása után a a kiválasztott beállításokat az összes releváns dokumentumok lesz áthelyezve. Üzembe helyezett modellnél be van tanítva, és autodeployed befejezését követően.

Ezek a műveletek a migrálás során kell végrehajtani:
* Minden dokumentumnak és projekt definíciók nevük neve elé "hub_" igény szerinti hozzáadásával át lesz. Az automatikusan generált tesztelési és hangolási adatértékek\<a hub_systemtune_ ModelID >\<vagy a hub_systemtest_ ModelID > lesznek elnevezve.
* Bármely, amely a telepített állapotban volt, ha az áttelepítés történik betanítások automatikusan fog a használatával a dokumentumok a Hub képzés képezni. Ez a képzés az előfizetéshez nem számítunk fel. Ha automatikus telepítése választotta az áttelepítés befejezését követően a betanított modell fájltól. A normál üzemeltetési költségek lépnek érvénybe.
* Minden áttelepített betanítások, amelyek nem az üzembe helyezett állapotú az áttelepített draft állapotba fog kerülni. Ebben az állapotban lesz lehetősége az áttelepített definíció modell képzési, de rendszeres képzési díjakat számolunk fel.
* Bármikor, a BLEU pontszám átemelt képzés a modell a "Bleu pontszám MT hub" TrainingDetails lapján tekintheti meg a Hub fejléc.

> [!Note]
> A sikeres képzés érdekében a Custom Translator legalább 10 000 egyedi kinyert mondatot igényel. Az egyéni fordító nem végezheti el a [javasolt minimálisnál](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences)kevesebb tanítást.

## <a name="find-custom-translator-workspace-id"></a>Keresse meg az egyéni a fordítót a munkaterület-azonosító

Áttelepítendő [Microsoft Translator Hub](https://hub.microsofttranslator.com/) munkaterületen cél egyéni a fordítót a munkaterület-azonosító van szüksége. Az egyéni a fordítót a célként megadott munkaterület, ahol minden a Hub munkaterületeket és projekteket kell telepíthető át.

A cél a munkaterület-azonosító egyéni Translator beállítások oldalon talál:

1. Nyissa meg az egyéni a fordítót portál "Beállítás" lapján.

2. A munkaterület-Azonosítót az alapvető információkat szakaszban talál.

    ![Cél munkaterület Azonosítójának megkeresése](media/how-to/how-to-find-destination-ws-id.png)

3. Tartsa meg a cél, tekintse meg az áttelepítési folyamat során a munkaterület-Azonosítót.

## <a name="migrate-a-project"></a>Migrate-projekt

Ha azt szeretné, külön-külön migrálni a projekteket, a Microsoft Translator Hub lehetőséget nyújt a.

A projekt áttelepítéséhez:

1. Jelentkezzen be a Microsoft Translator Hub.

2. Nyissa meg a "Projektek" lap.

3. Kattintson a megfelelő projektet "Áttelepítése" hivatkozásra.

    ![A Hub áttelepítése](media/how-to/how-to-migrate-from-hub.png)

4. A migrate-hivatkozás megnyomásakor megjelenik egy űrlapot, amely lehetővé teszi:
   * Adja meg a kívánt egyéni a fordítót átvitele munkaterület
   * Adja meg, hogy szeretné-e minden betanítások sikeres betanítások vagy csak az üzembe helyezett betanítások átvitele. Alapértelmezés szerint az összes sikeres betanítások adjuk meg.
   * Adja meg, hogy szeretné-e a betanítási automatikus üzembe helyezett képzési befejezéséről. Alapértelmezés szerint a tanítási nem lesz automatikusan üzembe helyezett befejezését követően.

5. Kattintson a "Kérelem küldése".

## <a name="migrate-a-workspace"></a>A munkaterület áttelepítése

Mellett telepít át egy projekt, is át lehet, hogy sikeres betanítások egy adott munkaterület minden projektek. Ennek eredményeként minden projekt ki kell értékelni, mintha a migrate-hivatkozás aktiválták lett volna a munkaterületen. Ez a funkció lehetővé teszi a felhasználók az összes egyéni a fordítót áttelepítése ugyanazokkal a beállításokkal, akik projektek közül sok. Az áttelepítés munkaterületen kezdeményezhet Translator Hub beállításai lapján.

A munkaterület áttelepítéséhez:

1. Jelentkezzen be a Microsoft Translator Hub.

2. Nyissa meg a "Beállítások" lap.

3. A "Beállítások" lapon kattintson a "Egyéni a fordítót adat áttelepítése munkaterület".

    ![A Hub áttelepítése](media/how-to/how-to-migrate-workspace-from-hub.png)

4. A következő oldalon válassza ki a két lehetőség közül választhat:

    a. Csak központilag telepített képzések: Ha ezt a lehetőséget választja, csak a telepített rendszerek és a kapcsolódó dokumentumok lesznek áttelepítve.

    b. Minden sikeres tanítás: A beállítás kiválasztásával áttelepíti az összes sikeres betanítást és kapcsolódó dokumentumot.

    c. Adjon meg egyéni a fordítót a cél a munkaterület-azonosító.

    ![A Hub áttelepítése](media/how-to/how-to-migrate-from-hub-screen.png)

5. Kattintson a kérés elküldése.

## <a name="migration-history"></a>Áttelepítési előzmények

Munkaterület kért / hubról migrálási projekt, ha az áttelepítési előzmények egyéni Translator beállítások oldalán találhat.

Áttelepítési előzmények megtekintéséhez kövesse az alábbi lépéseket:

1. Nyissa meg az egyéni a fordítót portál "Beállítás" lapján.

2. Áttelepítési előzmények szakaszában a beállítások lapon kattintson az áttelepítési előzmények.

    ![Áttelepítési előzmények](media/how-to/how-to-migration-history.png)

Áttelepítési Előzmények lapon megjeleníti a következő minden kért áttelepítés összegző információkat.

1. Áttelepítette: Az áttelepítési kérelmet elküldő felhasználó neve és e-mail-címe

2. Migrálva: Az áttelepítés dátuma és időbélyegzője

3. Projektek: Azoknak a projekteknek a száma, amelyeket a rendszer az áttelepítéshez a sikeresen áttelepített projektek száma miatt igényelt.

4. Tréningek Az áttelepítéshez szükséges Betanítások száma.

5. Dokumentumok Az áttelepítéshez szükséges dokumentumok száma az áttelepítés során.

    ![Áttelepítési előzmények részletei](media/how-to/how-to-migration-history-details.png)

Ha azt szeretné, hogy a projektek, betanítások és dokumentumok. áttelepítési jelentés részletesebb, hogy beállítás részleteinek exportálása CSV-fájlként.

## <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
* Az egyéni fordítóban még nem elérhető nyelvi párokkal rendelkező rendszerek csak az adathozzáféréshez és az egyéni Translator használatával történő telepítéshez lesznek elérhetők. Ezek a projektek "nem érhető el" jelöléssel jelennek meg a projektek lapon. Mivel az egyéni fordítókkal új nyelvi párokat engedélyezünk, a projektek betanításra és üzembe helyezésre lesznek aktívak. 
* Áttelepítés egy projektet Hub egyéni a fordítót a nem fog a Hub betanítások vagy projektekhez hatást. Nem töröljük projektek vagy dokumentumok hubról áttelepítés során, és azt nem megszüntetheti a modellek.
* Csak egyszer áttelepítése projektenként engedélyezettek. Ha ismételje meg a projekt áttelepítés van szüksége, lépjen kapcsolatba velünk a következő címen.
* Az egyéni fordító támogatja a NMT nyelvét az angoltól és angolra. [Tekintse meg a támogatott nyelvi verziók teljes listáját](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Hub nem igényel kiindulási modelleket, és ezért a több ezer nyelveket támogatja. Nem támogatott nyelvet párjának, azonban a rendszer csak a dokumentumok az áttelepítéshez és a projekt definíciók áttelepítheti. A Microsoft nem fogja tudni az új modell betanításához. Továbbá ezeket a dokumentumokat és projektek jelenik meg inaktívként annak a megállapítására, hogy nem tudja használni őket most. Támogatás bevezetése esetén ezek projektek és/vagy a dokumentumok, aktív és trainable válnak.
* Egyéni a fordítót jelenleg nem támogatja a monolingual betanítási adatok. Nem támogatott nyelvet párok, például áttelepítheti monolingual dokumentumok, de azok megjelenítése inaktívként, amíg nem támogatott monolingual adatok.
* Egyéni a fordítót 10 k párhuzamos mondatokat igényel a betanításához. A Microsoft Hub egy kisebb adatkészletet a sikerült betanításához. Ha olyan betanítást telepít át, amely nem felel meg ennek a követelménynek, nem lesz betanítva.

## <a name="custom-translator-versus-hub"></a>Eseményközpont és egyéni fordító

Ez a tábla között a Microsoft Translator Hub és az egyéni a fordítót a szolgáltatásokat hasonlítja össze.

|   | Eseményközpont | Custom Translator |
|:-----|:----:|:----:|
|Testreszabás funkció állapota   | Általános rendelkezésre állás  | Általános rendelkezésre állás |
| Text API-verzió  | 2\. verzió    | V3  |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egyesített Speech services testreszabása | Nem    | Igen |
| Nincs nyomkövetés | Igen | Igen |

## <a name="new-languages"></a>Új nyelvek

Ha Ön egy Közösség vagy szervezet, amely a Microsoft Translator új nyelvi rendszerének létrehozásán dolgozik, további információkért [custommt@microsoft.com](mailto:custommt@microsoft.com) tekintse meg a következőt:.

## <a name="next-steps"></a>További lépések

- [A modell betanítását](how-to-train-model.md).
- Az üzembe helyezett egyéni fordítási modellben keresztül használatának megkezdéséhez [Microsoft Translator Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
