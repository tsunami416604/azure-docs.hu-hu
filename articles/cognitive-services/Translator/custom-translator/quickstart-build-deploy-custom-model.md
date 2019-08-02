---
title: 'Gyors útmutató: Egyéni modell létrehozása, üzembe helyezése és használata – egyéni fordító'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban részletesen áttekintheti a fordítási rendszerek létrehozását az egyéni fordító használatával.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: quickstart
ms.openlocfilehash: a450a91f29a9118c9e4c83f6bb964bac2d6ffb3c
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595556"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Gyors útmutató: Egyéni modell létrehozása, üzembe helyezése és használata fordításhoz

Ez a cikk részletes útmutatást nyújt a fordítási rendszer egyéni fordítóval való létrehozásához.

## <a name="prerequisites"></a>Előfeltételek

1. Az [Egyéni Translator](https://portal.customtranslator.azure.ai) portál használatához szüksége lesz egy [Microsoft-fiók](https://signup.live.com) vagy [Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) -fiókra (az Azure-ban üzemeltetett szervezeti fiókra) a bejelentkezéshez.

2. Előfizetés a Translator Text API a Azure Portal keresztül. Az egyéni fordítóban a munkaterülethez társítandó Translator Text API előfizetési kulcsra lesz szüksége. Tekintse [meg, hogyan regisztrálhat a Translator Text APIra](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Ha mindkét fentivel rendelkezik, jelentkezzen be az [Egyéni Translator](https://portal.customtranslator.azure.ai) portálra. Az egyéni Translator Portalon navigáljon a beállítások oldalra, ahol társíthatja a Microsoft Translator Text API előfizetési kulcsát a munkaterületéhez.

## <a name="create-a-project"></a>Projekt létrehozása

Az egyéni Translator portál kezdőlapján kattintson az új projekt elemre. A párbeszédpanelen megadhatja a kívánt projekt nevét, a nyelvi párokat és a kategóriát, valamint az egyéb releváns mezőket. Ezután mentse a projektet. További részletekért látogasson el a [projekt létrehozása](how-to-create-project.md)lehetőségre.

![Projekt létrehozása](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Dokumentumok feltöltése

Ezután töltse fel [](training-and-model.md#training-dataset-for-custom-translator)a betanítást, a [hangolást](training-and-model.md#tuning-dataset-for-custom-translator) és a [tesztelési](training-and-model.md#testing-dataset-for-custom-translator) dokumentum-készleteket. Egyszerre több [párhuzamos](what-are-parallel-documents.md) és kombinált dokumentumot is feltölthet. A szótárat is [](what-is-dictionary.md)feltöltheti.

A dokumentumokat a dokumentumok lapról vagy egy adott projekt oldaláról töltheti fel.

![Dokumentumok feltöltése](media/quickstart/ct-how-to-upload.png)

Dokumentumok feltöltésekor válassza ki a dokumentum típusát (képzés, hangolás vagy tesztelés) és a nyelvi párokat. Párhuzamos dokumentumok feltöltésekor Emellett meg kell adnia egy dokumentum nevét. További részletekért látogasson el a [dokumentum feltöltése](how-to-upload-document.md)elemre.

## <a name="create-a-model"></a>Modell létrehozása

Ha az összes szükséges dokumentumot feltöltötte, a következő lépés a modell felépítése.

Válassza ki a létrehozott projektet. Ekkor megjelenik az összes feltöltött dokumentum, amely a jelen projekttel közös nyelvi párral rendelkezik. Válassza ki a modellbe felvenni kívánt dokumentumokat. Kiválaszthatja [](training-and-model.md#training-dataset-for-custom-translator)az adatképzést, a [hangolást](training-and-model.md#tuning-dataset-for-custom-translator)és a [tesztelést](training-and-model.md#testing-dataset-for-custom-translator) , vagy kiválaszthatja a csak betanítási adatait, és lehetővé teheti, hogy a Custom Translator automatikusan kiépítse a modell

![Modell létrehozása](media/quickstart/ct-how-to-train.png)

Ha befejezte a kívánt dokumentumok kiválasztását, kattintson a modell létrehozása gombra a modell létrehozásához és a képzés megkezdéséhez. Megtekintheti a képzés állapotát, valamint az összes betanított modell részleteit a modellek lapon.

További részletekért látogasson el [a modell létrehozása](how-to-train-model.md)lehetőségre.

## <a name="analyze-your-model"></a>A modell elemzése

A képzés sikeres befejezését követően vizsgálja meg az eredményeket. A BLEU pontszám egy mérőszám, amely a fordítás minőségét jelzi. Az egyéni modellel készített fordításokat manuálisan is összehasonlíthatja a tesztek által megadott fordításokkal, ha megnyitja a "teszt" lapot, és a "System Results" (rendszereredmények) gombra kattint. Ezeknek a fordításoknak a manuális megvizsgálása jó képet nyújt a rendszer által létrehozott fordítás minőségéről. További részletekért látogasson el a [System A teszt eredményeira](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>Betanított modell üzembe helyezése

Ha készen áll a betanított modell üzembe helyezésére, kattintson az "üzembe helyezés" gombra. Projektenként egy üzembe helyezett modellt is használhat, és az Állapot oszlopban megtekintheti az üzemelő példány állapotát. További részletekért látogasson el a [modell üzembe helyezése](how-to-view-system-test-results.md#deploy-a-model)

![Betanított modell üzembe helyezése](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Üzembe helyezett modell használata

Az üzembe helyezett modellek a Microsoft Translator Text API v3-as verzióján keresztül érhetők [el a Kategóriakód](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)megadásával. A Translator Text APIról további információt az [API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) -referenciát ismertető weblapon találhat.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan navigálhat a [Custom Translator munkaterületen, és hogyan kezelheti a projektjeit](workspace-and-project.md).
