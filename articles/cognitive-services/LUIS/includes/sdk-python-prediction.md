---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 31280b1efa3db22e8008f401a25ca627dda4153f
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772402"
---
A Pythonhoz készült Language Understanding (LUIS) előrejelzési ügyféloldali kódtára a következőre használható:

* Előrejelzés beolvasása tárolóhely alapján
* Előrejelzés lekérése verzió alapján

[Dokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis) | [előrejelző futtatókörnyezet-csomag (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/) | [minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portál fiók – [hozzon létre egyet ingyen](https://www.luis.ai)
* [Python 3. x](https://www.python.org/)

## <a name="setting-up"></a>Beállítás

### <a name="get-your-language-understanding-luis-runtime-key"></a>A Language Understanding (LUIS) futtatókörnyezeti kulcs beszerzése

Szerezze be a [futásidejű kulcsot](../luis-how-to-azure-subscription.md) egy Luis Runtime-erőforrás létrehozásával. Tartsa a kulcsot és a kulcs végpontját a következő lépéshez.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-python-file"></a>Új Python-fájl létrehozása

Hozzon létre egy új, `prediction_quickstart.py`nevű Python-fájlt az előnyben részesített szerkesztőben vagy IDE.

### <a name="install-the-sdk"></a>Az SDK telepítése

Az alkalmazás könyvtárában telepítse a Pythonhoz készült Language Understanding (LUIS) előrejelzési futtatókörnyezet ügyféloldali függvénytárát a következő paranccsal:

```python
python -m pip install azure-cognitiveservices-language-luis
```

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) előrejelzési futtatókörnyezet ügyfele egy [LUISRuntimeClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclient?view=azure-python) objektum, amely az Azure-ba hitelesíti az erőforrás kulcsát.

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* Előrejelzés [átmeneti vagy üzemi tárolóhely](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-) alapján
* Előrejelzés [verziója](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-version-prediction-app-id--version-id--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-) szerint

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Language Understanding (LUIS) előrejelzési futásidejű ügyféloldali kódtár használatával:

* [Előrejelzés tárolóhely alapján](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a `prediction_quickstart.py` fájlt az előnyben részesített szerkesztőben vagy az IDE-ben. Adja hozzá a következő függőségeket:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a saját szükséges LUIS-adataihoz:

    Adja hozzá a változókat az előrejelzési kulcs egy `LUIS_RUNTIME_KEY`nevű környezeti változóból való kezeléséhez. Ha az alkalmazás elindítása után hozta létre a környezeti változót, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusok később lesznek létrehozva.

    Hozzon létre egy változót, amely az erőforrás nevét `LUIS_RUNTIME_ENDPOINT`fogja tárolni.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=AuthorizationVariables)]

1. Hozzon létre egy változót az alkalmazás-AZONOSÍTÓhoz `LUIS_APP_ID`nevű környezeti változóként. Állítsa a környezeti változót a nyilvános IoT alkalmazásra, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Hozzon létre egy változót a `production` közzétett tárolóhely beállításához.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=OtherVariables)]


1. Hozzon létre egy hitelesítő adatokat tartalmazó objektumot a kulccsal, és használja a végpontján egy [LUISRuntimeClientConfiguration]https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python() objektum létrehozásához.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő metódust az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó kimondása a [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) objektum részét képezi.

A **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** metódusnak több paraméterre van szüksége, például az alkalmazás azonosítója, a tárolóhely neve és az előrejelzési kérelem objektum a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező. A kérelem egy [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) objektumot ad vissza.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszer használatával összekapcsolhatja a változókat és a metódusokat az előrejelzés beszerzéséhez.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `python prediction_quickstart.py` paranccsal az alkalmazás könyvtárából.

```console
python prediction_quickstart.py
```

A Gyorsindítás konzol megjeleníti a kimenetet:

```console
Top intent: HomeAutomation.TurnOn
Sentiment: None
Intents:
        "HomeAutomation.TurnOn"
Entities: {'HomeAutomation.Operation': ['on']}
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a jóslatokkal, törölje a munkát ebből a rövid útmutatóból a fájl és az alkönyvtárak törlésével.
