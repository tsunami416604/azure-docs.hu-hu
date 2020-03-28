---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: ff4c33aea3d3ce604f44c38e6e3856242388b0e9
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77372361"
---
Használja a Language Understanding (LUIS) előrejelzési ügyfélkódtár a Python hoz:

* Előrejelzés a horony szerint
* Előrejelzés verzió szerint

[Referenciadokumentációkönyvtár](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [forráskódjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [előrejelzési futásidejű csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [ minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portálfiók - [Hozzon létre egyet ingyen](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Beállítása

### <a name="get-your-language-understanding-luis-runtime-key"></a>A nyelvi ismeretek (LUIS) futásidejű kulcsának betöltése

A [futásidejű kulcs](../luis-how-to-azure-subscription.md) beszerezése egy LUIS futásidejű erőforrás létrehozásával. Tartsa meg a kulcsot, és a végpont a kulcs a következő lépéshez.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Új python-fájl létrehozása

Hozzon létre egy új python fájlt `prediction_quickstart.py`a kívánt szerkesztőben vagy IDE-ben, melynek neve .

### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazáskönyvtáron belül telepítse a Language Understanding (LUIS) előrejelzési futásidejű ügyfélkódtár python a következő paranccsal:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) előrejelzési futásidejű ügyfél egy [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) objektum, amely hitelesíti az Azure-ban, amely tartalmazza az erőforráskulcsot.

Az ügyfél létrehozása után ezzel az ügyféllel érheti el a funkciókat, például a következőket:

* Előrejelzés [átmeneti vagy éles tárolóhely szerint](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)
* Előrejelzés [verzió](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-) szerint

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Language Understanding (LUIS) előrejelzési futásidejű ügyfélkódtár a Python:

* [Előrejelzés a nyíláson belül](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából `prediction_quickstart.py` nyissa meg a fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá a következő függőségeket:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a saját szükséges LUIS-adataihoz:

    Változók hozzáadása az előrejelzési kulcs kezeléséhez, `LUIS_RUNTIME_KEY`amelyet egy nevű környezeti változóból lehívnak. Ha az alkalmazás elindítása után hozta létre a környezeti változót, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia, és újra kell töltenie a változó eléréséhez. A metódusok később jönnek létre.

    Hozzon létre egy változót az erőforrás nevének tárolására. `LUIS_RUNTIME_ENDPOINT`

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Hozzon létre egy változót az alkalmazásazonosítóhoz a nevű környezeti változóként. `LUIS_APP_ID` Állítsa be a környezeti változót **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** a nyilvános IoT-alkalmazásra. Hozzon létre egy `production` változót a közzétett tárolóhely beállításához.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Hozzon létre egy hitelesítő adatokat objektumot a kulccsal, és használjahttps://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() azt a végpontmal egy [LUISRuntimeClientConfiguration] objektum létrehozásához.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés beszerezni a futásidejű

Adja hozzá a következő módszert a kérelem létrehozásához az előrejelzési futásórához.

A felhasználói utterance (kifejezés) része a [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) objektum.

A **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** metódusnak több paraméterre van szüksége, például az alkalmazásazonosítóra, a tárolóhely nevére és az előrejelzési kérelem objektumra a kérés teljesítéséhez. A többi lehetőség, például a részletes, az összes szándék megjelenítése és a napló megadása nem kötelező. A kérelem egy [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) objektumot ad vissza.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszerrel kösse össze a változókat és módszereket az előrejelzés lekérése érdekében.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `python prediction_quickstart.py` alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```console
python prediction_quickstart.py
```

A rövid útmutató konzol megjeleníti a kimenetet:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az előrejelzésekkel, a fájl és az alkönyvtárak törlésével tisztítsa meg a munkát ebből a rövid útmutatóból.
