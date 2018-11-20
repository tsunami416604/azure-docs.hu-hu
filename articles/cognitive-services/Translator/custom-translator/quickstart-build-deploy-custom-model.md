---
title: 'Gyors útmutató: Elkészítheti, telepítheti, és a egyéni modell - egyéni a fordítót'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban végigvesszük egy az egyéni translatorral fordítási rendszerek kiépítésének részletes folyamatán.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: quickstart
ms.openlocfilehash: cb713ac53b399d8ba79d003ee6d948bb5af5220b
ms.sourcegitcommit: 8314421d78cd83b2e7d86f128bde94857134d8e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2018
ms.locfileid: "51975772"
---
# <a name="quickstart-build-deploy-and-use-a-custom-model-for-translation"></a>Gyors útmutató: Elkészítheti, telepítheti, és egyéni modellt alkalmaznak a fordítás

Ebben a cikkben részletes útmutatást nyújt az egyéni a fordítót a fordítási rendszert hozhat létre.

## <a name="prerequisites"></a>Előfeltételek

1. Használatához a [egyéni a fordítót](https://portal.customtranslator.azure.ai) portál, szüksége lesz egy [Microsoft-fiók](https://signup.live.com) vagy [Azure AD-fiók](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (az Azure-ban üzemeltetett szervezeti fiók) való bejelentkezéshez.

2. Az Azure Portalon a Translator Text API-előfizetés. A Translator Text API előfizetési kulcs egyéni a fordítót a munkaterületek társítani kell. Lásd: [hogyan regisztrálhat a Translator Text API](https://docs.microsoft.com/azure/cognitive-services/translator/translator-text-how-to-signup).

3. Ha a fentiek mindegyikét, jelentkezzen be a [egyéni a fordítót](https://portal.customtranslator.azure.ai) portálon. Egyszer az egyéni a fordítót portálon lépjen a beállítások lapot, ahol hozzárendelheti a munkaterület az a Microsoft Translator Text API-előfizetési kulcs.

## <a name="create-a-project"></a>Projekt létrehozása

Az egyéni a fordítót portál kezdőlapjáról kattintson új projekt. A párbeszédpanelen adhatja meg a kívánt projekt neve, a nyelvi pár, és a kategória, valamint egyéb megfelelő mezőket. Mentse a projekthez. További részletekért látogasson el [projekt létrehozása](how-to-create-project.md).

![Projekt létrehozása](media/quickstart/ct-how-to-create-project.png)


## <a name="upload-documents"></a>Dokumentumok feltöltése

Ezután töltse fel [képzési](training-and-model.md#training-dataset-for-custom-translator), [hangolása](training-and-model.md#tuning-dataset-for-custom-translator) és [tesztelés](training-and-model.md#testing-dataset-for-custom-translator) dokumentumkészletek. Mindkét feltöltheti [párhuzamos](what-are-parallel-documents.md) és kombinált dokumentumokat. Emellett feltölthet [szótár](what-is-dictionary.md).

Dokumentumok, a dokumentumok lapról, vagy egy meghatározott projektben lapon tölthet fel.

![Dokumentumok feltöltése](media/quickstart/ct-how-to-upload.png)

Dokumentumok feltöltésekor válassza ki a dokumentum típusa (képzés, hangolása vagy tesztelés), és a nyelvi pár. Párhuzamos dokumentumok feltöltésekor kell emellett adja meg a dokumentum nevét. További részletekért látogasson el [feltöltési dokumentum](how-to-upload-document.md).

## <a name="create-a-model"></a>Modell létrehozása

A szükséges dokumentumok feltöltésekor a rendszer a következő lépés esetén a modell létrehozásához.

Válassza ki a létrehozott projekt. Látni fogja a dokumentumokat Ön által feltöltött, amely egy nyelvi pár megoszthatja a projekthez. Válassza ki a dokumentumok, amelyeket szeretne a modell tartalmazza. Választhat [képzési](training-and-model.md#training-dataset-for-custom-translator), [hangolása](training-and-model.md#tuning-dataset-for-custom-translator), és [tesztelés](training-and-model.md#testing-dataset-for-custom-translator) adatokat, vagy válassza ki, csak a betanítási adatok, és lehetővé teszik egyéni a fordítót automatikusan finomhangolási és teszteli a modell csoportjainak.

![Modell létrehozása](media/quickstart/ct-how-to-train.png)

Amikor elkészült, válassza a kívánt dokumentumokat, kattintson a modell létrehozásához, és indítsa el a tanítási modell létrehozása gombra. Láthatja, hogy a képzés és részleteit, hogy betanított, a modellek lapon minden modell állapotát.

További részletekért látogasson el [létrehoz egy modellt](how-to-train-model.md).

## <a name="analyze-your-model"></a>A modell elemzése

Miután a tanítási sikeresen befejeződött, vizsgálja meg az eredményeket. A BLEU pontszám, amely azt jelzi, hogy a fordítás minősége egy metrika. Manuálisan is összehasonlíthatja a fordítások végzett az egyéni modell a fordítások található a tesztet, állítsa be a "Test" lapot, és kattintson a "Rendszer eredményeket." Manuális ellenőrzést végző fordítások néhány felkínálja a rendszer által előállított fordítási minőség célszerű. További részletekért látogasson el [rendszer teszteredmények](how-to-view-system-test-results.md).

## <a name="deploy-a-trained-model"></a>A betanított modell üzembe helyezése

Ha készen áll a betanított modell üzembe helyezése, kattintson a "Üzembe helyezés" gomb. Használhat egy üzembe helyezett modell projektenként, és megtekintheti a központi telepítés állapotát az Állapot oszlopban. További részletekért látogasson el [modell-üzembehelyezés](how-to-view-system-test-results.md#deploy-a-model)

![A betanított modell üzembe helyezése](media/quickstart/ct-how-to-deploy.png)

## <a name="use-a-deployed-model"></a>Üzembe helyezett modellt

A Microsoft Translator-n keresztül elérhető üzembe helyezett modellnél [Text API v3-as a CategoryID megadásával](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl). A Translator Text API-val kapcsolatos további információk találhatók a [API-referencia](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) weblapon.

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan navigálhat a [egyéni a fordítót munkaterületen kezelheti a projektjeit és](workspace-and-project.md).
