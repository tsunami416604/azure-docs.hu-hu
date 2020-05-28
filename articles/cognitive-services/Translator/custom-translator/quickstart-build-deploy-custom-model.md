---
title: 'Gyors útmutató: egyéni modell létrehozása, üzembe helyezése és használata – egyéni fordító'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban részletesen áttekintheti a fordítási rendszerek létrehozását az egyéni fordító használatával.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: ea01fcd259075caee1e21eb091b8df1b326c553f
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996465"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Gyors útmutató: egyéni modell összeállítása, üzembe helyezése és használata fordításhoz

Ez a cikk részletes útmutatást nyújt a fordítási rendszer egyéni fordítóval való létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

1. Az [Egyéni Translator](https://portal.customtranslator.azure.ai) portál használatához szüksége lesz egy [Microsoft-fiók](https://signup.live.com) vagy [Azure ad-fiókra](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (az Azure-ban üzemeltetett szervezeti fiókra) a bejelentkezéshez.

2. A fordító előfizetése a Azure Portalon keresztül. Az egyéni fordítóban a munkaterülethez való hozzárendeléshez szüksége lesz a Translator előfizetési kulcsra. Lásd: [regisztráció a translatorre](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Ha mindkét fentivel rendelkezik, jelentkezzen be az [Egyéni Translator](https://portal.customtranslator.azure.ai) portálra munkaterületek, projektek, fájlok feltöltése és modellek létrehozása/üzembe helyezése létrehozásához.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Ha először felhasználó, akkor a rendszer felkéri, hogy fogadja el a használati feltételeket, hozzon létre egy munkaterületet, és társítsa a munkaterületet fordítói előfizetéssel.

![Munkaterület létrehozása munkaterület létrehozás munkaterület létrehozása munkaterület létrehozás munkaterületének létrehozása munkaterület létrehozás munkaterületének létrehozása ](media/quickstart/terms-of-service.png)
 ![ ](media/quickstart/create-workspace-1.png)
 ![ ](media/quickstart/create-workspace-2.png)
 ![ ](media/quickstart/create-workspace-3.png)
 ![ ](media/quickstart/create-workspace-4.png)
 ![ ](media/quickstart/create-workspace-5.png)
 ![](media/quickstart/create-workspace-6.png)

Az egyéni Translator Portalon való későbbi látogatások alkalmával navigáljon a beállítások oldalra, ahol kezelheti a munkaterületet, több munkaterületet hozhat létre, társíthatja a fordítói előfizetési kulcsot a munkaterületekhez, hozzáadhat közös tulajdonosokat és módosíthatja az előfizetési kulcsot.

## <a name="create-a-project"></a>Projekt létrehozása

Az egyéni Translator portál kezdőlapján kattintson az új projekt elemre. A párbeszédpanelen megadhatja a kívánt projekt nevét, a nyelvi párokat és a kategóriát, valamint az egyéb releváns mezőket. Ezután mentse a projektet. További részletekért látogasson el a [projekt létrehozása](how-to-create-project.md)lehetőségre.

![Projekt létrehozása](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Dokumentumok feltöltése

Ezután töltse fel a [betanítást](training-and-model.md#training-document-type-for-custom-translator), a [hangolást](training-and-model.md#tuning-document-type-for-custom-translator) és a [tesztelési](training-and-model.md#testing-dataset-for-custom-translator) dokumentum-készleteket. Egyszerre több [párhuzamos](what-are-parallel-documents.md) és kombinált dokumentumot is feltölthet. A [szótárat](what-is-dictionary.md)is feltöltheti.

A dokumentumokat a dokumentumok lapról vagy egy adott projekt oldaláról töltheti fel.

![Dokumentumok feltöltése](media/quickstart/ct-how-to-upload.png)

Dokumentumok feltöltésekor válassza ki a dokumentum típusát (képzés, hangolás vagy tesztelés) és a nyelvi párokat. Párhuzamos dokumentumok feltöltésekor Emellett meg kell adnia egy dokumentum nevét. További részletekért látogasson el a [dokumentum feltöltése](how-to-upload-document.md)elemre.

## <a name="create-a-model"></a>Modell létrehozása

Ha az összes szükséges dokumentumot feltöltötte, a következő lépés a modell felépítése.

Válassza ki a létrehozott projektet. Ekkor megjelenik az összes feltöltött dokumentum, amely a jelen projekttel közös nyelvi párral rendelkezik. Válassza ki a modellbe felvenni kívánt dokumentumokat. Kiválaszthatja az [adatképzést](training-and-model.md#training-document-type-for-custom-translator), a [hangolást](training-and-model.md#tuning-document-type-for-custom-translator)és a [tesztelést](training-and-model.md#testing-dataset-for-custom-translator) , vagy kiválaszthatja a csak betanítási adatait, és lehetővé teheti, hogy a Custom Translator automatikusan kiépítse a modell

![Modell létrehozása](media/quickstart/ct-how-to-train.png)

Ha befejezte a kívánt dokumentumok kiválasztását, kattintson a modell létrehozása gombra a modell létrehozásához és a képzés megkezdéséhez. Megtekintheti a képzés állapotát, valamint az összes betanított modell részleteit a modellek lapon.

További részletekért látogasson el [a modell létrehozása](how-to-train-model.md)lehetőségre.

## <a name="analyze-your-model"></a>A modell elemzése

A képzés sikeres befejezését követően vizsgálja meg az eredményeket. A BLEU pontszám egy mérőszám, amely a fordítás minőségét jelzi. Az egyéni modellel készített fordításokat manuálisan is összehasonlíthatja a tesztek által megadott fordításokkal, ha megnyitja a "teszt" lapot, és a "System Results" (rendszereredmények) gombra kattint. Ezeknek a fordításoknak a manuális megvizsgálása jó képet nyújt a rendszer által létrehozott fordítás minőségéről. További részletekért látogasson el a [System A teszt eredményeira](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Betanított modell üzembe helyezése

Ha készen áll a betanított modell üzembe helyezésére, kattintson az "üzembe helyezés" gombra. Projektenként egy üzembe helyezett modellt is használhat, és az Állapot oszlopban megtekintheti az üzemelő példány állapotát. További részletekért látogasson el a [modell üzembe helyezése](how-to-view-system-test-results.md#deploy-a-model)

![Betanított modell üzembe helyezése](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Üzembe helyezett modell használata

Az üzembe helyezett modellek a Microsoft Translator [text API v3-as verzióján keresztül érhetők el a Kategóriakód megadásával](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). A fordítóval kapcsolatos további információkért tekintse meg az [API-referenciát](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) ismertető weblapon.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan navigálhat a [Custom Translator munkaterületen, és hogyan kezelheti a projektjeit](workspace-and-project.md).
