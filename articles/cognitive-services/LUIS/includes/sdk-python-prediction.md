---
title: fájl belefoglalása
description: fájl belefoglalása
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: db866da43310f5407ce4daae1cade2c7512b91ea
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87369259"
---
A Pythonhoz készült Language Understanding (LUIS) előrejelzési ügyféloldali kódtára a következőre használható:

* Előrejelzés beolvasása tárolóhely alapján
* Előrejelzés lekérése verzió alapján

[Dokumentáció](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/index?view=azure-python)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-luis/azure/cognitiveservices/language/luis)  |  [Előrejelzési futtatókörnyezet csomagja (PyPi)](https://pypi.org/project/azure-cognitiveservices-language-luis/)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding (LUIS) portál fiók – [hozzon létre egyet ingyen](https://www.luis.ai)
* [Python 3.x](https://www.python.org/)
* A LUIS-alkalmazás azonosítója – használja a nyilvános IoT-alkalmazás AZONOSÍTÓját `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A rövid útmutató kódjában használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="setting-up"></a>Beállítás

### <a name="get-your-language-understanding-luis-runtime-key"></a>A Language Understanding (LUIS) futtatókörnyezeti kulcs beszerzése

Szerezze be a [futásidejű kulcsot](../luis-how-to-azure-subscription.md) egy Luis Runtime-erőforrás létrehozásával. Tartsa a kulcsot és a kulcs végpontját a következő lépéshez.

### <a name="create-a-new-python-file"></a>Új Python-fájl létrehozása

Hozzon létre egy új Python-fájlt az előnyben részesített szerkesztőben vagy az IDE nevű fájlban `prediction_quickstart.py` .

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

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Language Understanding (LUIS) előrejelzési futásidejű ügyféloldali kódtár használatával:

* [Előrejelzés tárolóhely alapján](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a `prediction_quickstart.py` fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő függőségeket:

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a saját szükséges LUIS-adataihoz: az előrejelzési kulcs és a végpont.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=AuthorizationVariables)]

1. Hozzon létre egy változót az alkalmazás-AZONOSÍTÓhoz a nyilvános IoT alkalmazáshoz **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Hozzon létre egy változót a `production` közzétett tárolóhely beállításához.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=OtherVariables)]


1. Hozzon létre egy hitelesítő adatokat tartalmazó objektumot a kulccsal, és használja azt a végponttal egy [LUISRuntimeClientConfiguration](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.luisruntimeclientconfiguration?view=azure-python) objektum létrehozásához.

    [!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=Client)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő metódust az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó kimondása a [prediction_request](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionrequest?view=azure-python) objektum részét képezi.

A **[get_slot_prediction](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.operations.predictionoperations?view=azure-python#get-slot-prediction-app-id--slot-name--prediction-request--verbose-none--show-all-intents-none--log-none--custom-headers-none--raw-false----operation-config-)** metódusnak több paraméterre van szüksége, például az alkalmazás azonosítója, a tárolóhely neve és az előrejelzési kérelem objektum a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező. A kérelem egy [PredictionResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-language-luis/azure.cognitiveservices.language.luis.runtime.models.predictionresponse?view=azure-python) objektumot ad vissza.

[!code-python[Dependency statements](~/cognitive-services-quickstart-code/python/LUIS/python-sdk-authoring-prediction/prediction_quickstart.py?name=predict)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszer használatával összekapcsolhatja a változókat és a metódusokat az előrejelzés beszerzéséhez.

```python
predict(luisAppID, luisSlotName)
```
## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `python prediction_quickstart.py` paranccsal az alkalmazás könyvtárából.

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
