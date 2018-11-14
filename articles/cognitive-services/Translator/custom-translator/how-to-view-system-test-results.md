---
title: Rendszer-teszt eredményeinek megtekintése és üzembe helyezés – egyéni a fordítót
titleSuffix: Azure Cognitive Services
description: Ha a tanítási sikeres, tekintse át a rendszer tesztek a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezze el a betanított modell központi telepítési kérelmet.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
ms.openlocfilehash: 61d3869559d88e14c0b9a3c3e23cd8a1f9c2b6c4
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51627457"
---
# <a name="view-system-test-results"></a>Rendszer-teszt eredményeinek megtekintése

Ha a tanítási sikeres, tekintse át a rendszer tesztek a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezze el a betanított modell központi telepítési kérelmet. 

## <a name="system-test-results-page"></a>Rendszer vizsgálati eredmények lap

Válasszon ki egy projektet, majd válassza ki, hogy a projekt modellek lapján keresse meg, melyik modellhez szeretne használni, és végül válassza ki a tesztelési lapot.

A tesztelési lapot láthatja:

1.  Rendszer teszteredmények: A tesztelési folyamat az betanítások eredménye. A tesztelési folyamat hoz létre a BLEU pontszámot.

    **Mondat száma:** hány párhuzamos mondatokat használták a vizsgálat beállítása.

     **BLEU pontszám:** BLEU pontszám modell betanítási befejezése után jön létre.

    **Állapot:** azt jelzi, ha a teszt befejeződött, vagy folyamatban van.

    ![Rendszer terhelésiteszt-eredményei](media/how-to/how-to-system-test-results.png)

2.  Kattintson a rendszer vizsgálati eredmények, és tesztelheti az eredmény részletei lap, amely vesz igénybe. Ezen a lapon látható a gépi fordítási, tesztelési adathalmazon részét képező a mondatok.

3.  A teszt eredménye részletező oldalon tábla két oszlopot – az egyes nyelvekhez a pár egyik. A Forrásnyelv az oszlop a mondat lehet látható. Az oszlop a Célnyelv tartalmazza az egyes sorokban szereplő két mondatokat.

    **REF:** ezt a mondatot a forrás mondat a tesztelési adathalmazon referencia fordítását.

    **Fő Célkiszolgáló:** ezt a mondatot a forrás mondat után a képzés hardverjavaslatok a modell által elvégzett automatikus fordítását.

    ![Rendszer vizsgálati eredmények összehasonlítása](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Töltse le a teszt

A fordítások letöltése hivatkozásra egy zip-fájl letöltéséhez. A zip-fájl tartalmazza a gép fordításokat teszt adatkészlet forrása a mondatok.

![Töltse le a teszt](media/how-to/how-to-system-test-download.png)

A letöltött zip-archívumot három fájlokat tartalmazza.

1.  Custom.MT.txt: Ez a fájl tartalmazza a célként megadott nyelven, a felhasználó adatait a betanított modell által elvégzett Forrásnyelv a mondatok gép fordítások.

2.  Ref.txt: Ez a fájl tartalmazza a felhasználó által megadott fordítások Forrásnyelv a mondatok cél nyelven.

3.  Source.txt: Ez a fájl tartalmazza a forrás nyelven mondatokat.

    ![A letöltött rendszer terhelésiteszt-eredményei](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Modell üzembe helyezése

A kérjenek központi telepítést:

1.  Válassza ki a projektet, nyissa meg modellek lapot.

2. Egy sikeres betanított modell azt látható "Üzembe helyezés" gomb, ha nem telepített.

    ![Modell üzembe helyezése](media/how-to/how-to-deploy-model.png)

3.  A gombra kattintva helyezze üzembe.
4.  Válassza ki a régió(k), hol szeretné üzembe helyezni a modellt, és kattintson a Mentés gombra. Több régióban is választhat.

    ![Modell üzembe helyezése](media/how-to/how-to-deploy-model-regions.png)

5.  A modell állapotának a "Status" oszlopban tekintheti meg.

>[!Note]
>Ha a modell már üzembe helyezte, látni fogja, hogy a modell egy "Undeploy" gombra. "Undeploy" kattintva megszüntetheti a modell.

## <a name="next-steps"></a>További lépések

- Az üzembe helyezett egyéni fordítási modellben keresztül használatának megkezdéséhez [Microsoft Translator Text API v3-as](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Ismerje meg, [beállítások kezelése](how-to-manage-settings.md) szeretne megosztani a munkaterülethez, kezelheti az előfizetési kulcsot.
- Ismerje meg, [migrálása a munkaterület és a projekt](how-to-migrate.md) a [Microsoft Translator Hub](https://hub.microsofttranslator.com)