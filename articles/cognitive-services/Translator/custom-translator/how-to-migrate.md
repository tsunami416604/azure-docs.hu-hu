---
title: A Microsoft Translator Hub munkaterületet és projektek migrálni? -Egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Telepítse át a Hub munkaterület és projektek egyéni a fordítót.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 378baad0735238dc0921e5e78e2a27b3ae907e19
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627543"
---
# <a name="migrate-hub-workspace-and-projects-to-custom-translator"></a>Egyéni a fordítót Hub munkaterületet és projektek áttelepítése

Áttelepítheti a [Microsoft Translator Hub](https://hub.microsofttranslator.com/) munkaterületet és projektek egyéni a fordítót. Áttelepítési elindítja a hubról.


Ezeket az elemeket a folyamat során települnek át:

1.  A projekt(ek) definíciókat.

2.  A képzési definíció egyéni a fordítót új modell-definíció létrehozására használható.

3.  A betanítások belül használt párhuzamos és monolingual-fájlok összes átkerülnek az egyéni Translator új dokumentumok formájában.

4.  Az automatikusan létrehozott rendszer teszt- és finomhangolási adatokat fog exportálhatók és egyéni Translator új dokumentumok létrehozott.

Az összes üzembe helyezett betanítások egyéni a fordítót meg fog betanítja a modellt, bármely költségek nélkül. Lehetősége van manuálisan telepítheti őket.

>[!Note]
>Sikeres képzéshez egyéni fordító szükséges minimális 10 000 kinyert mondatokat. Kevesebb számú kinyert mondatokat, mint a [javasolt minimális](sentence-alignment.md#suggested-minimum-number-of-extracted-and-aligned-sentences), egyéni a fordítót képzési nem végez.

Az összes sikeres betanítások, amelyek nem telepítik, lesznek áttelepítve az egyéni a fordítót vázlatként.

## <a name="find-custom-translator-workspace-id"></a>Keresse meg az egyéni a fordítót a munkaterület-azonosító

Áttelepítendő [Microsoft Translator Hub](https://hub.microsofttranslator.com/) munkaterület cél egyéni a fordítót a munkaterület-azonosító szükséges. Az egyéni a fordítót a célként megadott munkaterület, ahol minden a Hub munkaterületeket és projekteket kell telepíthető át.

A cél a munkaterület-azonosító egyéni Translator beállítások oldalon talál: 

1.  Nyissa meg az egyéni a fordítót portál "Beállítás" lapján.

2.  A munkaterület-azonosítót az alapvető információkat szakaszban talál.

    ![Cél munkaterület Azonosítójának megkeresése](media/how-to/how-to-find-destination-ws-id.png)

3. Tartsa meg a cél, tekintse meg az áttelepítési folyamat során a munkaterület-azonosítót.

## <a name="migrate-workspace"></a>Munkaterület áttelepítése

A teljes Eseményközpont munkaterületen egyéni a fordítót áttelepítésekor a projektek, dokumentumok és betanítások egyéni fordítónak települnek át. Az áttelepítés előtt kell választania, ha csak telepített betanítások áttelepíteni kívánt, vagy a sikeres betanítások összes áttelepíteni kívánt.

A munkaterület áttelepítéséhez:

1.  Jelentkezzen be a Microsoft Translator Hub.

2.  Nyissa meg a "Beállítások" lap.

3.  A "Beállítások" lapon kattintson a "Egyéni a fordítót adat áttelepítése munkaterület".

    ![A Hub áttelepítése](media/how-to/how-to-migrate-workspace-from-hub.png)

4.  A következő oldalon válassza ki a két lehetőség közül választhat:

    a.  Üzembe helyezett csak Betanítások: Ez a beállítás csak a telepített rendszerek és a kapcsolódó dokumentumok áttelepíti.

    b.  Az összes sikeres Betanítások: Ezzel a beállítással áttelepíti, a sikeres betanítások és a kapcsolódó dokumentumokat.

    c.  Adjon meg egyéni a fordítót a cél a munkaterület-azonosító.

    ![A Hub áttelepítése](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Kattintson a kérés elküldése.

## <a name="migrate-project"></a>Migrate-projekt

Ha azt szeretné, külön-külön migrálni a projekteket, a Microsoft Translator Hub lehetőséget nyújt a.

A projekt áttelepítéséhez:

1.  Jelentkezzen be a Microsoft Translator Hub.

2.  Nyissa meg a "Projektek" lap.

3.  Kattintson a megfelelő projektet "Áttelepítése" hivatkozásra.

    ![A Hub áttelepítése](media/how-to/how-to-migrate-from-hub.png)

4.  A következő oldalon válassza ki a két lehetőség közül választhat:

    a.  Üzembe helyezett csak Betanítások: Ez a beállítás csak a telepített rendszerek és a kapcsolódó dokumentumok áttelepíti. 

    b.  Az összes sikeres Betanítások: Ezzel a beállítással áttelepíti, a sikeres betanítások és a kapcsolódó dokumentumokat.

    c.  Adjon meg egyéni a fordítót a cél a munkaterület-azonosító.

    ![A Hub áttelepítése](media/how-to/how-to-migrate-from-hub-screen.png)

5.  Kattintson a "Kérelem küldése".

## <a name="migration-history"></a>Áttelepítési előzmények

Munkaterület kért / hubról migrálási projekt, ha az áttelepítési előzmények egyéni Translator beállítások oldalán találhat. 

Áttelepítési előzmények megtekintéséhez kövesse az alábbi lépéseket:

1.  Nyissa meg az egyéni a fordítót portál "Beállítás" lapján.

2.  Áttelepítési előzmények szakaszában a beállítások lapon kattintson az áttelepítési előzmények.

    ![Áttelepítési előzmények](media/how-to/how-to-migration-history.png)

Áttelepítési Előzmények lapon megjeleníti a következő minden kért áttelepítés összegző információkat.

1.  Áttelepített által: És az e-mail a felhasználó a migrálás kérelem elküldése

2.  Az áttelepített: Dátum és idő bélyeg az áttelepítés

3.  Projektek: A kért migrálási projektek számaként v/s sikeresen projektek száma át.

4.  Betanítások: Áttelepítési v/s számú betanítások sikeresen migrálva kérte betanítások száma.

5.  Dokumentumok: A dokumentumok száma, a kért migrálási v/s számú dokumentumok sikeresen migrálva.

    ![Áttelepítési előzmények részletei](media/how-to/how-to-migration-history-details.png)

Ha azt szeretné, hogy a projektek, betanítások és dokumentumok. áttelepítési jelentés részletesebb, hogy beállítás részleteinek exportálása CSV-fájlként.

>[!Note]
>Migrálás csak a nyelvi párok, ahol NMT nyelvek létezik-e a támogatott. Ellenőrizze a listája jelenleg [támogatott nyelvek NMT](https://www.microsoft.com/translator/business/languages/). A nyelvi párok, ahol NMT nyelvek nem létezik, a adat központból átkerül egyéni a fordítót, de betanítások nem tudja elvégezni az adott nyelv párok.

## <a name="next-steps"></a>További lépések

- [A modell betanítását](how-to-train-model.md).
- Az üzembe helyezett egyéni fordítási modellben keresztül használatának megkezdéséhez [Microsoft Translator Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).