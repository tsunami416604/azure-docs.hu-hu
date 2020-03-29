---
title: A rendszer teszteredményeinek és telepítésének megtekintése - Egyéni fordító
titleSuffix: Azure Cognitive Services
description: Ha a képzés sikeres, tekintse át a rendszerteszteket a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezzen el egy üzembe helyezési kérelmet a betanított modellhez.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595600"
---
# <a name="view-system-test-results"></a>Rendszer teszteredményeinek megtekintése

Ha a képzés sikeres, tekintse át a rendszerteszteket a betanítási eredmények elemzéséhez. Ha elégedett a betanítási eredményekkel, helyezzen el egy üzembe helyezési kérelmet a betanított modellhez.

## <a name="system-test-results-page"></a>Rendszerteszt eredménylapja

Jelöljön ki egy projektet, majd válassza ki a projekt modellek lapját, keresse meg a használni kívánt modellt, és végül válassza ki a teszt lapot.

A teszt lap a következőket mutatja:

1.  **A rendszer teszteredményei:** Az eredmény a vizsgálati folyamat a tréningek. A vizsgálati folyamat létrehozza a BLEU pontszámot.

    **Mondatszám:** Hány párhuzamos mondatot használtak a tesztkészletben.

     **BLEU pontszám:** Bleu pontszám generált egy modell betanítás befejezése után.

    **Állapot:** Azt jelzi, hogy a vizsgálati folyamat befejeződött vagy folyamatban van.

    ![A rendszer vizsgálati eredményei](media/how-to/how-to-system-test-results.png)

2.  Kattintson a rendszer teszt eredményei, és hogy elviszi a vizsgálati eredmény részleteit oldalon. Ezen a lapon a tesztadatkészlet részét játszó mondatok gépi fordítása látható.

3.  A teszteredmény-részletek oldalon lévő táblázat két oszlopból áll - a pár minden nyelvéhez tartozik egy. A forrásnyelv oszlopa a fordítandó mondatot mutatja. A célnyelv oszlopa minden sorban két mondatot tartalmaz.

    **Ref:** Ez a mondat a forrásmondat referenciafordítása a teszt adatkészletben megadottak szerint.

    **MT:** Ez a mondat a forrásmondat automatikus fordítása, amelyet a betanítás után épített modell hajt végre.

    ![A rendszer vizsgálati eredményeinek összehasonlítása](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Teszt letöltése

Zip-fájl letöltéséhez kattintson a Fordítások letöltése hivatkozásra. A zip a tesztadatkészlet forrásmondatainak gépi fordításait tartalmazza.

![Teszt letöltése](media/how-to/how-to-system-test-download.png)

Ez a letöltött zip archívum három fájlt tartalmaz.

1.  **custom.mt.txt:** Ez a fájl tartalmazza a számítógépi fordítások forrásnyelvi mondatok a célnyelv által végzett modell betanított felhasználói adatokkal.

2.  **ref.txt:** Ez a fájl a forrásnyelvi mondatok felhasználói által biztosított fordításait tartalmazza a célnyelven.

3.  **forrás.txt:** Ez a fájl a forrásnyelven lévő mondatokat tartalmaz.

    ![Letöltött rendszerteszt eredményei](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Modell üzembe helyezése

Telepítés kérése:

1.  Jelöljön ki egy projektet, és lépjen a Modellek lapra.

2. Egy sikeresen betanított modell esetén a "Telepítés" gombot jeleníti meg, ha nincs telepítve.

    ![Modell üzembe helyezése](media/how-to/how-to-deploy-model.png)

3.  Kattintson a Telepítés gombra.
4.  Válassza **az Üzembe helyezve** lehetőséget ahhoz a régió(khoz) lehetőséghez, ahol a modellt telepíteni szeretné, és kattintson a Mentés gombra. Több régióhoz is **kiválaszthat telepített.**

    ![Modell üzembe helyezése](media/how-to/how-to-deploy-model-regions.png)

5.  A modell állapotát az "Állapot" oszlopban tekintheti meg.

>[!Note]
>Az egyéni fordító 10 üzembe helyezett modellt támogat egy munkaterületen belül bármikor.

## <a name="update-deployment-settings"></a>Telepítési beállítások frissítése

A központi telepítési beállítások frissítése:

1.  Jelöljön ki egy projektet, és lépjen a **Modellek** lapra.

2. A sikeresen üzembe helyezett modell egy **Frissítés** gombot jelenít meg.

    ![Modell üzembe helyezése](media/how-to/how-to-update-undeploy-model.png)

3.  Válassza a **Frissítés** lehetőséget.
4.  Válassza **az Üzembe helyezett** vagy nem **telepített** lehetőséget ahhoz a régió(khoz) lehetőségbe, ahol a modellt telepíteni vagy nem telepíteni szeretné, majd kattintson a **Mentés gombra.**

    ![Modell üzembe helyezése](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Ha az Összes régióra vonatkozóan a Nem telepített lehetőséget **választja,** a modell nem lesz telepítve az összes régióból, és nem központiállapotba kerül. Most már nem használható.

## <a name="next-steps"></a>További lépések

- Kezdje el használni a telepített egyéni fordítási modellt a [Microsoft Translator Text API V3-on](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)keresztül.
- Ismerje meg, hogyan kezelheti a munkaterület megosztására és az előfizetési kulcs kezelésére [vonatkozó beállításokat.](how-to-manage-settings.md)
- [A munkaterület és a projekt áttelepítése a](how-to-migrate.md) Microsoft Translator [Hubról](https://hub.microsofttranslator.com)
