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
ms.custom: include file, devx-track-javascript
ms.author: diberry
ms.openlocfilehash: 2cf75056cd045822e083a15f7590bf020db8cd5e
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87425205"
---
A Node.jshoz használja a Language Understanding (LUIS) futásidejű ügyféloldali kódtárat a következőhöz:

* Előrejelzés tárolóhely alapján
* Előrejelzés verziója szerint

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime)  |  [Futtatókörnyezeti csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js)

## <a name="prerequisites"></a>Előfeltételek

* Language Understanding futásidejű erőforrás: [hozzon létre egyet a Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* A LUIS-alkalmazás azonosítója – használja a nyilvános IoT-alkalmazás AZONOSÍTÓját `df67dcdb-c37d-46af-88e1-8b97951ca1c2` . A rövid útmutató kódjában használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="setting-up"></a>Beállítás

### <a name="get-your-language-understanding-luis-runtime-key"></a>A Language Understanding (LUIS) futtatókörnyezeti kulcs beszerzése

Szerezze be a [futásidejű kulcsot](../luis-how-to-azure-subscription.md) egy Luis Runtime-erőforrás létrehozásával. Tartsa a kulcsot és a kulcs végpontját a következő lépéshez.

### <a name="create-a-new-javascript-nodejs-file"></a>Új JavaScript-fájl (Node.js) létrehozása

Hozzon létre egy új JavaScript-fájlt az előnyben részesített szerkesztőben vagy az IDE nevű fájlban `luis_prediction.js` .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>A LUIS Runtime NPM-könyvtárának telepítése

Az alkalmazás könyvtárában telepítse a függőségeket a következő paranccsal:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objektummodell

A Language Understanding (LUIS) authoring Client egy [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) objektum, amely a szerzői kulcsot tartalmazó Azure-ba hitelesíti.

Az ügyfél létrehozása után ezt az ügyfelet használhatja a következő funkciók eléréséhez, többek között:

* [Előrejelzés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) `staging` vagy `production` tárolóhely
* [Előrejelzés verziója szerint](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Language Understanding (LUIS) előrejelzési futásidejű ügyféloldali kódtár használatával:

* [Előrejelzés tárolóhely alapján](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárában nyissa meg a `luis_prediction.js` fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a következő függőségeket:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a saját szükséges LUIS-adataihoz:

    Adja hozzá a változókat az előrejelzési kulcs és a végpont kulcsának kezeléséhez. 
    
    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Variables)]

1. Hozzon létre egy változót a nevű alkalmazás-AZONOSÍTÓhoz `LUIS_APP_ID` . Állítsa a változót a nyilvános IoT alkalmazásra **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** . Hozzon létre egy változót a `production` közzétett tárolóhely beállításához.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=OtherVariables)]


1. Hozzon létre egy msRest. ApiKeyCredentials objektumot a kulccsal, és használja a végpontján egy Luis létrehozásához [. LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) objektum.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés lekérése futtatókörnyezetből

Adja hozzá a következő metódust az előrejelzési futtatókörnyezethez való kérelem létrehozásához.

A felhasználó teljes értéke a [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) objektum része.

A **[luisRuntimeClient. Jóslás. getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** metódusnak számos paramétert kell tartalmaznia, például az alkalmazás azonosítóját, a tárolóhely nevét és az előrejelzési kérelem objektumát a kérelem teljesítéséhez. A többi lehetőség, például a részletes, az összes leképezés megjelenítése és a napló megadása nem kötelező.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszer használatával összekapcsolhatja a változókat és a metódusokat az előrejelzés beszerzéséhez.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/node-sdk-authoring-prediction/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node luis_prediction.js` paranccsal az alkalmazás könyvtárából.

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
