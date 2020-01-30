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
ms.openlocfilehash: 0539f4a8080056f96319a7a75a355782ee80018d
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772417"
---
A Node. js-hez készült Language Understanding (LUIS) futásidejű ügyféloldali kódtár használatával:

* Előrejelzés tárolóhely alapján
* Előrejelzés verziója szerint

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [Runtime Package (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [Samples](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding futásidejű erőforrás: [hozzon létre egyet a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)

## <a name="setting-up"></a>Beállítás

### <a name="get-your-language-understanding-luis-runtime-key"></a>A Language Understanding (LUIS) futtatókörnyezeti kulcs beszerzése

Szerezze be a [futásidejű kulcsot](../luis-how-to-azure-subscription.md) egy Luis Runtime-erőforrás létrehozásával. Tartsa a kulcsot és a kulcs végpontját a következő lépéshez.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Új JavaScript-fájl (node. js) létrehozása

Hozzon létre egy új JavaScript-fájlt az előnyben részesített szerkesztőben vagy IDE `luis_prediction.js`néven.

### <a name="install-the-npm-library-for-the-luis-runtime"></a>A LUIS Runtime NPM-könyvtárának telepítése

Az alkalmazás könyvtárában telepítse a függőségeket a következő paranccsal:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) authoring Client egy [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) objektum, amely a szerzői kulcsot tartalmazó Azure-ba hitelesíti.

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* [Előrejelzés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) `staging` vagy `production` tárolóhely alapján
* [Előrejelzés verziója szerint](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Language Understanding (LUIS) előrejelzési futásidejű ügyféloldali kódtár használatával:

* [Előrejelzés tárolóhely alapján](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a `luis_prediction.js` fájlt az előnyben részesített szerkesztőben vagy az IDE-ben. Adja hozzá a következő függőségeket:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a saját szükséges LUIS-adataihoz:

    Adja hozzá a változókat az előrejelzési kulcs egy `LUIS_RUNTIME_KEY`nevű környezeti változóból való kezeléséhez. Ha az alkalmazás elindítása után hozta létre a környezeti változót, akkor a változó eléréséhez be kell zárnia és újra kell töltenie a szerkesztőt, az IDE-t vagy a shellt. A metódusok később lesznek létrehozva.

    Hozzon létre egy változót, amely az erőforrás nevét `LUIS_RUNTIME_ENDPOINT`fogja tárolni.

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Hozzon létre egy változót az alkalmazás-AZONOSÍTÓhoz `LUIS_APP_ID`nevű környezeti változóként. Állítsa a környezeti változót a nyilvános IoT alkalmazásra, **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Hozzon létre egy változót a `production` közzétett tárolóhely beállításához.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Hozzon létre egy msRest. ApiKeyCredentials objektumot a kulccsal, és használja a végpontján egy Luis létrehozásához [. LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) objektum.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő metódust az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó teljes értéke a [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) objektum része.

A **[luisRuntimeClient. Jóslás. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** metódusnak számos paramétert kell tartalmaznia, például az alkalmazás azonosítóját, a tárolóhely nevét és az előrejelzési kérelem objektumát a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszer használatával összekapcsolhatja a változókat és a metódusokat az előrejelzés beszerzéséhez.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az `node luis_prediction.js` paranccsal az alkalmazás könyvtárából.

```console
node luis_prediction.js
```

Az előrejelzési eredmény egy JSON-objektumot ad vissza:

```console
{
   "query":"turn on all lights",
   "prediction":{
      "topIntent":"HomeAutomation.TurnOn",
      "intents":{
         "HomeAutomation.TurnOn":{
            "score":0.5375382
         },
         "None":{
            "score":0.08687421
         },
         "HomeAutomation.TurnOff":{
            "score":0.0207554
         }
      },
      "entities":{
         "HomeAutomation.Operation":[
            "on"
         ],
         "$instance":{
            "HomeAutomation.Operation":[
               {
                  "type":"HomeAutomation.Operation",
                  "text":"on",
                  "startIndex":5,
                  "length":2,
                  "score":0.724984169,
                  "modelTypeId":-1,
                  "modelType":"Unknown",
                  "recognitionSources":[
                     "model"
                  ]
               }
            ]
         }
      }
   }
}
```


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült a jóslatokkal, törölje a munkát ebből a rövid útmutatóból a fájl és az alkönyvtárak törlésével.
