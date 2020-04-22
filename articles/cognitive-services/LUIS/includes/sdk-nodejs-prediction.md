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
ms.openlocfilehash: 9c15e4217c5331346c5a95329bae7e2a4f0e0841
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732066"
---
Használja a Language Understanding (LUIS) futásidejű ügyfélkódtár a Node.js a következőkhöz:

* Előrejelzés a nyíláson belül
* Előrejelzés verzió szerint

[Referenciadokumentációs](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/?view=azure-node-latest) | [függvénytár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-luis-runtime) | [runtime csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-luis-runtime) | [minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/LUIS/luis_prediction.js)

## <a name="prerequisites"></a>Előfeltételek

* Nyelvtudás futásidejű erőforrás: [Hozzon létre egyet az Azure Portalon](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
* [Node.js](https://nodejs.org)
* Luis-alkalmazásazonosító – használja a nyilvános IoT-alkalmazásazonosítóját. `df67dcdb-c37d-46af-88e1-8b97951ca1c2` A gyorsútmutató kódban használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="setting-up"></a>Beállítása

### <a name="get-your-language-understanding-luis-runtime-key"></a>A nyelvi ismeretek (LUIS) futásidejű kulcsának betöltése

A [futásidejű kulcs](../luis-how-to-azure-subscription.md) beszerezése egy LUIS futásidejű erőforrás létrehozásával. Tartsa meg a kulcsot, és a végpont a kulcs a következő lépéshez.

[!INCLUDE [Set up environment variables for prediction quickstart](sdk-prediction-environment-variables.md)]

### <a name="create-a-new-javascript-nodejs-file"></a>Új JavaScript-fájl (Node.js) létrehozása

Hozzon létre egy új JavaScript-fájlt `luis_prediction.js`a kívánt szerkesztőben vagy ide-ben, melynek neve .

### <a name="install-the-npm-library-for-the-luis-runtime"></a>Az NPM-kódtár telepítése a LUIS-futásórához

Az alkalmazáskönyvtáron belül telepítse a függőségeket a következő paranccsal:

```console
npm install @azure/cognitiveservices-luis-runtime @azure/ms-rest-js
```

## <a name="object-model"></a>Objektummodell

A language understanding (LUIS) szerzői ügyfél egy [LUISAuthoringClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) objektum, amely hitelesíti az Azure-ban, amely tartalmazza a szerzői kulcsot.

Az ügyfél létrehozása után ezzel az ügyféllel érheti el a funkciókat, például a következőket:

* [Előrejelzés](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-) `staging` vagy `production` bővítőhely szerint
* [Előrejelzés verzió szerint](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getversionprediction-string--string--predictionrequest--models-predictiongetversionpredictionoptionalparams-)

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a language understanding (LUIS) előrejelzési futásidejű ügyfélkódtármal:

* [Előrejelzés a nyíláson belül](#get-prediction-from-runtime)

## <a name="add-the-dependencies"></a>Függőségek hozzáadása

A projekt könyvtárából `luis_prediction.js` nyissa meg a fájlt a kívánt szerkesztőben vagy IDE-ben. Adja hozzá a következő függőségeket:

[!code-javascript [Dependencies](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Dependencies)]

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

1. Hozzon létre változókat a saját szükséges LUIS-adataihoz:

    Változók hozzáadása az előrejelzési kulcs kezeléséhez, `LUIS_RUNTIME_KEY`amelyet egy nevű környezeti változóból lehívnak. Ha az alkalmazás elindítása után hozta létre a környezeti változót, a szerkesztőt, az IDE-t vagy a rendszerhéjat futtató rendszernek be kell zárnia, és újra kell töltenie a változó eléréséhez. A metódusok később jönnek létre.

    Hozzon létre egy változót az erőforrás nevének tárolására. `LUIS_RUNTIME_ENDPOINT`

    [!code-javascript [Azure resource variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Variables)]

1. Hozzon létre egy változót az alkalmazásazonosítóhoz a nevű környezeti változóként. `LUIS_APP_ID` Állítsa be a környezeti változót **`df67dcdb-c37d-46af-88e1-8b97951ca1c2`** a nyilvános IoT-alkalmazásra. Hozzon létre egy `production` változót a közzétett tárolóhely beállításához.

    [!code-javascript [LUIS app variables](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=OtherVariables)]


1. Hozzon létre egy msRest.ApiKeyCredentials objektumot a kulccsal, és használja azt a végpontjával egy [LUIS létrehozásához. LUISRuntimeClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/luisruntimeclient?view=azure-node-latest) objektum.

    [!code-javascript [LUIS Runtime client is required to access predictions for LUIS apps](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=AuthoringCreateClient)]

## <a name="get-prediction-from-runtime"></a>Előrejelzés beszerezni a futásidejű

Adja hozzá a következő módszert a kérelem létrehozásához az előrejelzési futásórához.

A felhasználói utterance (kifejezés) része az [predictionRequest](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionrequest?view=azure-node-latest) objektum.

A **[luisRuntimeClient.prediction.getSlotPrediction](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-luis-runtime/predictionoperations?view=azure-node-latest#getslotprediction-string--string--predictionrequest--models-predictiongetslotpredictionoptionalparams-)** metódusnak több paraméterre van szüksége, például az alkalmazásazonosítóra, a tárolónévre és az előrejelzési kérelem objektumra a kérés teljesítéséhez. A többi lehetőség, például a részletes, az összes szándék megjelenítése és a napló megadása nem kötelező.

[!code-javascript [LUIS prediction request and response in Node.js NPM SDK](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=predict)]

## <a name="main-code-for-the-prediction"></a>Az előrejelzés fő kódja

A következő fő módszerrel kösse össze a változókat és módszereket az előrejelzés lekérése érdekében.

[!code-javascript [Main method and main call](~/cognitive-services-quickstart-code/javascript/LUIS/luis_prediction.js?name=Main)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az `node luis_prediction.js` alkalmazást az alkalmazáskönyvtárból származó paranccsal.

```console
node luis_prediction.js
```

Az előrejelzés eredménye egy JSON-objektumot ad vissza:

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

Ha végzett az előrejelzésekkel, a fájl és az alkönyvtárak törlésével tisztítsa meg a munkát ebből a rövid útmutatóból.
