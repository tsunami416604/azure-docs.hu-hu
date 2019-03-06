---
title: A Microsoft Translator Hub munkaterületet és projektek migrálni? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Telepítse át a Hub munkaterület és projektek egyéni a fordítót.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 3442e322ac75f7757d7a59f6035fc35d77e70702
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431580"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Egyéni a fordítót Hub munkaterületet és projektek áttelepítése

Egyszerűen áttelepítheti a [Microsoft Translator Hub](https://hub.microsofttranslator.com/) munkaterületet és projektek egyéni a fordítót. Jelölje ki a egy munkaterületet, vagy a projektet, majd egyéni a fordítót kell választania egy munkaterületet, és válassza az átvinni kívánt betanítások Microsoft Hub áttelepítési kezdeményezni. Az áttelepítés indítása után a a kiválasztott beállításokat az összes releváns dokumentumok lesz áthelyezve. Üzembe helyezett modellnél be van tanítva, és autodeployed befejezését követően.

Ezek a műveletek a migrálás során kell végrehajtani:
* Minden dokumentumnak és projekt definíciók nevük neve elé "hub_" igény szerinti hozzáadásával át lesz. Automatikusan létrehozott teszt- és finomhangolási adatokat kapja hub_systemtune_\<modelid > vagy hub_systemtest_\<modelid >.
* Bármely, amely a telepített állapotban volt, ha az áttelepítés történik betanítások automatikusan fog a használatával a dokumentumok a Hub képzés képezni. Ez a képzés az előfizetéshez nem számítunk fel. Ha automatikus telepítése választotta az áttelepítés befejezését követően a betanított modell fájltól. A normál üzemeltetési költségek lépnek érvénybe.
* Minden áttelepített betanítások, amelyek nem az üzembe helyezett állapotú az áttelepített draft állapotba fog kerülni. Ebben az állapotban lesz lehetősége az áttelepített definíció modell képzési, de rendszeres képzési díjakat számolunk fel.
* Bármikor, a BLEU pontszám átemelt képzés a modell a "Bleu pontszám MT hub" TrainingDetails lapján tekintheti meg a Hub fejléc.

>[!Note]
Sikeres képzéshez egyéni fordító szükséges legalább 10 000 egyedi kinyert mondatokat. Egyéni a fordítót kevesebb képzési nem végez a [javasolt minimális](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences).

## <a name="enable-account-migration"></a>Fiókáttelepítés engedélyezése

Annak érdekében, hogy az áttelepítési eszköz használata, szüksége van a Hub fiókáttelepítés engedélyezve van. Ehhez az szükséges, e-mailben [ custommt@microsoft.com ](mailto:custommt@microsoft.com) az összes live ID azonosító fiókok listáját, amelyet engedélyezve van. Ezeket a fiókokat kell jelentkeznie az e-mail-címek.

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

    a. Csak telepített Betanítások: Ez a beállítás csak a telepített rendszerek és a kapcsolódó dokumentumok telepítse át.

    b. Az összes sikeres Betanítások: Ez a beállítás a sikeres betanítások és a kapcsolódó dokumentumok telepítse át.

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

1. Áttelepített: Név és e-mail a felhasználó a migrálás kérelem elküldése

2. Az áttelepített: Az áttelepítés dátum és idő bélyeg

3. Projektek: A kért migrálási projektek számaként v/s sikeresen projektek száma át.

4. Betanítások: Áttelepítési v/s számú betanítások sikeresen migrálva kérte betanítások száma.

5. Dokumentumok: A dokumentumok száma, a kért migrálási v/s számú dokumentumok sikeresen migrálva.

    ![Áttelepítési előzmények részletei](media/how-to/how-to-migration-history-details.png)

Ha azt szeretné, hogy a projektek, betanítások és dokumentumok. áttelepítési jelentés részletesebb, hogy beállítás részleteinek exportálása CSV-fájlként.

## <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
* Áttelepítés egy projektet Hub egyéni a fordítót a nem fog a Hub betanítások vagy projektekhez hatást. Nem töröljük projektek vagy dokumentumok hubról áttelepítés során, és azt nem megszüntetheti a modellek.
* Csak egyszer áttelepítése projektenként engedélyezettek. Ha ismételje meg a projekt áttelepítés van szüksége, lépjen kapcsolatba velünk a következő címen.
* Egyéni a fordítót NMT nyelvi párok, illetve onnan angol támogatja. [Támogatott langauges teljes listájának megtekintéséhez](https://docs.microsoft.com/azure/cognitive-services/translator/language-support#customization). Hub nem igényel kiindulási modelleket, és ezért a több ezer nyelveket támogatja. Nem támogatott nyelvet párjának, azonban a rendszer csak a dokumentumok az áttelepítéshez és a projekt definíciók áttelepítheti. A Microsoft nem fogja tudni az új modell betanításához. Továbbá ezeket a dokumentumokat és projektek jelenik meg inaktívként annak a megállapítására, hogy nem tudja használni őket most. Támogatás bevezetése esetén ezek projektek és/vagy a dokumentumok, aktív és trainable válnak.
* Egyéni a fordítót jelenleg nem támogatja a monolingual betanítási adatok. Nem támogatott nyelvet párok, például áttelepítheti monolingual dokumentumok, de azok megjelenítése inaktívként, amíg nem támogatott monolingual adatok.
* Egyéni a fordítót 10 k párhuzamos mondatokat igényel a betanításához. A Microsoft Hub egy kisebb adatkészletet a sikerült betanításához. Képzési telepít át, amely nem felel meg ennek a követelménynek, ha azt nem kell betanítani.

## <a name="custom-translator-versus-hub"></a>Eseményközpont és egyéni fordító

Ez a tábla között a Microsoft Translator Hub és az egyéni a fordítót a szolgáltatásokat hasonlítja össze.

|   | Eseményközpont | Custom Translator |
|:-----|:----:|:----:|
|Testreszabás funkció állapota   | Általános rendelkezésre állás  | Általános rendelkezésre állás |
| Text API-verzió  | 2. verzió    | V3  |
| SMT testreszabása | Igen   | Nem |
| NMT testreszabása | Nem    | Igen |
| Új egyesített Speech services testreszabása | Nem    | Igen |
| Nincs nyomkövetés | Igen | Igen |

## <a name="new-languages"></a>Új nyelvek

Ha egy közösségi vagy a szervezet új nyelvi rendszert hoz létre a Microsoft Translator dolgozik, bizalommal az [ custommt@microsoft.com ](mailto:custommt@microsoft.com) további információt.

## <a name="next-steps"></a>További lépések

- [A modell betanítását](how-to-train-model.md).
- Az üzembe helyezett egyéni fordítási modellben keresztül használatának megkezdéséhez [Microsoft Translator Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
