---
title: Rendszer-teszt eredményeinek megtekintése és üzembe helyezés – egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Ha a tanítási sikeres, tekintse át a rendszer tesztek a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezze el a betanított modell központi telepítési kérelmet.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 67ddc7a8e9d79d0eef9c017b315385df62c18e33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997717"
---
# <a name="view-system-test-results"></a>Rendszer teszteredményeinek megtekintése

Ha a tanítási sikeres, tekintse át a rendszer tesztek a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezze el a betanított modell központi telepítési kérelmet.

## <a name="system-test-results-page"></a>Rendszer vizsgálati eredmények lap

Válasszon ki egy projektet, majd válassza ki, hogy a projekt modellek lapján keresse meg, melyik modellhez szeretne használni, és végül válassza ki a tesztelési lapot.

A tesztelési lapot láthatja:

1.  **Rendszer teszteredmények:** A tesztelési folyamat az betanítások eredménye. A tesztelési folyamat hoz létre a BLEU pontszámot.

    **Mondat száma:** Hány párhuzamos mondatokat használták a vizsgálat beállítása.

     **BLEU pontszám:** BLEU pontszám modell betanítási befejezése után jön létre.

    **Állapot:** Azt jelzi, ha a teszt befejeződött, vagy folyamatban van.

    ![Rendszer terhelésiteszt-eredményei](media/how-to/how-to-system-test-results.png)

2.  Kattintson a rendszer vizsgálati eredmények, és tesztelheti az eredmény részletei lap, amely vesz igénybe. Ezen a lapon látható a gépi fordítási, tesztelési adathalmazon részét képező a mondatok.

3.  A teszt eredménye részletező oldalon tábla két oszlopot – az egyes nyelvekhez a pár egyik. A Forrásnyelv az oszlop a mondat lehet látható. Az oszlop a Célnyelv tartalmazza az egyes sorokban szereplő két mondatokat.

    **REF:** Ezt a mondatot a forrás mondat a tesztelési adathalmazon referencia fordítása.

    **MT:** Ezt a mondatot a forrás mondat után a képzés hardverjavaslatok a modell által elvégzett automatikus fordítását.

    ![Rendszer vizsgálati eredmények összehasonlítása](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Töltse le a teszt

A fordítások letöltése hivatkozásra egy zip-fájl letöltéséhez. A zip-fájl tartalmazza a gép fordításokat teszt adatkészlet forrása a mondatok.

![Töltse le a teszt](media/how-to/how-to-system-test-download.png)

A letöltött zip-archívumot három fájlokat tartalmazza.

1.  **custom.mt.txt:** Ez a fájl tartalmazza a célként megadott nyelven, a felhasználó adatait a betanított modell által elvégzett Forrásnyelv a mondatok gép fordítások.

2.  **ref.txt:** Ez a fájl tartalmazza a felhasználó által megadott cél nyelven Forrásnyelv a mondatok fordítása.

3.  **source.txt:** Ez a fájl tartalmazza a forrás nyelven mondatokat.

    ![A letöltött rendszer terhelésiteszt-eredményei](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Modell üzembe helyezése

A kérjenek központi telepítést:

1.  Válassza ki a projektet, nyissa meg modellek lapot.

2. Egy sikeres betanított modell azt látható "Üzembe helyezés" gomb, ha nem telepített.

    ![Modell üzembe helyezése](media/how-to/how-to-deploy-model.png)

3.  A gombra kattintva helyezze üzembe.
4.  Válassza ki **telepített** lehet hatályba léptetni, és kattintson a Mentés gombra a modell, ahová a régió(k) esetében. Választhat **telepített** több régióban.

    ![Modell üzembe helyezése](media/how-to/how-to-deploy-model-regions.png)

5.  A modell állapotának a "Status" oszlopban tekintheti meg.

## <a name="update-deployment-settings"></a>Frissítés központi telepítési beállítások

Központi telepítési beállítások frissítése:

1.  Válassza ki a projektet, és nyissa meg a **modellek** fülre.

2. Egy sikeresen üzembe helyezett modell bemutatja egy **frissítés** gombra.

    ![Modell üzembe helyezése](media/how-to/how-to-update-undeploy-model.png)

3.  Válassza ki **frissítés**.
4.  Válassza ki **telepített** vagy **Undeployed** a régió, ahol azt szeretné, hogy a modell üzembe helyezhető, illetve telepítését eltávolították, majd kattintson **mentése**.

    ![Modell üzembe helyezése](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Ha **Undeployed** díját minden régióhoz, a modell összes régiójából származó telepítését eltávolították, és nem telepített állapotba helyezi. Már nem használható.

## <a name="next-steps"></a>További lépések

- Az üzembe helyezett egyéni fordítási modellben keresztül használatának megkezdéséhez [Microsoft Translator Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Ismerje meg, [beállítások kezelése](how-to-manage-settings.md) szeretne megosztani a munkaterülethez, kezelheti az előfizetési kulcsot.
- Ismerje meg, [migrálása a munkaterület és a projekt](how-to-migrate.md) a [Microsoft Translator Hub](https://hub.microsofttranslator.com)
