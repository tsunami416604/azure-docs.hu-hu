---
title: 'Előrejelzés a REST-hívással a C-ben #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/20/2020
ms.author: diberry
ms.openlocfilehash: 20916ff80ae52ee9fc215d87c0987900d89e590a
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733248"
---
## <a name="prerequisites"></a>Előfeltételek

* [.NET Core V2.2+](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)
* Luis-alkalmazásazonosító – használja a nyilvános IoT-alkalmazásazonosítóját. `df67dcdb-c37d-46af-88e1-8b97951ca1c2` A gyorsútmutató kódban használt felhasználói lekérdezés az adott alkalmazásra jellemző.

## <a name="create-luis-runtime-key-for-predictions"></a>LUIS-futásidejű kulcs létrehozása előrejelzésekhez

1. Bejelentkezés az [Azure-portálra](https://portal.azure.com)
1. Kattintson [a **Nyelvismertetés létrehozása gombra.** ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne)
1. Adja meg a Runtime kulcs összes szükséges beállítását:

    |Beállítás|Érték|
    |--|--|
    |Name (Név)|Kívánt név (2-64 karakter)|
    |Előfizetés|Válassza ki a megfelelő előfizetést|
    |Hely|Válassza ki a közeli és elérhető helyeket|
    |Tarifacsomag|`F0`- a minimális tarifaszint|
    |Erőforráscsoport|Elérhető erőforráscsoport kiválasztása|

1. Kattintson a **Létrehozás gombra,** és várja meg az erőforrás létrehozását. Létrehozása után keresse meg az erőforráslapot.
1. Gyűjtse `endpoint` össze `key`a konfigurált és a.

## <a name="get-intent-programmatically"></a>Szándék lekérése programozott módon

A C# (.NET Core) használatával lekérdezheti az [előrejelzési végpontot,](https://aka.ms/luis-apim-v3-prediction) és előrejelzési eredményt kaphat.

1. Hozzon létre egy új konzolalkalmazást, amely a C# `predict-with-rest`nyelvet célozza meg, a projekt és a mappa nevével.

    ```console
    dotnet new console -lang C# -n predict-with-rest
    ```

1. Módosítsa az `predict-with-rest` imént létrehozott könyvtárat, és telepítse a szükséges függőségeket az alábbi parancsokkal:

    ```console
    cd predict-with-rest
    dotnet add package System.Net.Http
    ```

1. Nyissa `Program.cs` meg a kedvenc IDE vagy szerkesztő. Ezután `Program.cs` írja felül a következő kóddal:

   ```csharp
    using System;
    using System.Net.Http;
    using System.Web;

    namespace predict_with_rest
    {
        class Program
        {
            static void Main(string[] args)
            {
                // YOUR-KEY: 32 character key
                var key = "YOUR-KEY";

                // YOUR-ENDPOINT: example is your-resource-name.api.cognitive.microsoft.com
                var endpoint = "YOUR-ENDPOINT";

                // //public sample app
                var appId = "df67dcdb-c37d-46af-88e1-8b97951ca1c2";

                var utterance = "turn on all lights";

                MakeRequest(key, endpoint, appId, utterance);

                Console.WriteLine("Hit ENTER to exit...");
                Console.ReadLine();
            }
            static async void MakeRequest(string key, string endpoint, string appId, string utterance)
            {
                var client = new HttpClient();
                var queryString = HttpUtility.ParseQueryString(string.Empty);

                // The request header contains your subscription key
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", key);

                // The "q" parameter contains the utterance to send to LUIS
                queryString["query"] = utterance;

                // These optional request parameters are set to their default values
                queryString["verbose"] = "true";
                queryString["show-all-intents"] = "true";
                queryString["staging"] = "false";
                queryString["timezoneOffset"] = "0";

                var endpointUri = String.Format("https://{0}/luis/prediction/v3.0/apps/{1}/slots/production/predict?query={2}", endpoint, appId, queryString);

                var response = await client.GetAsync(endpointUri);

                var strResponseContent = await response.Content.ReadAsStringAsync();

                // Display the JSON result from LUIS
                Console.WriteLine(strResponseContent.ToString());
            }
        }
    }

   ```

1. Cserélje `YOUR-KEY` le `YOUR-ENDPOINT` a és az értékeket a saját előrejelzési kulcs és végpont.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakteres előrejelzési kulcs.|
    |`YOUR-ENDPOINT`| Az előrejelzési URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`.|

1. A konzolalkalmazás létrehozása ezzel a paranccsal:

    ```console
    dotnet build
    ```

1. Futtassa a konzolalkalmazást. A konzol kimenete ugyanazt a JSON-t jeleníti meg, mint amit korábban a böngészőablakban látott.

    ```console
    dotnet run
    ```

1. Tekintse át az előrejelzési választ, amely JSON-ként ad vissza:

    ```console
    Hit ENTER to exit...
    {'query': 'turn on all lights', 'prediction': {'topIntent': 'HomeAutomation.TurnOn', 'intents': {'HomeAutomation.TurnOn': {'score': 0.5375382}, 'None': {'score': 0.08687421}, 'HomeAutomation.TurnOff': {'score': 0.0207554}}, 'entities': {'HomeAutomation.Operation': ['on'], '$instance': {'HomeAutomation.Operation': [{'type': 'HomeAutomation.Operation', 'text': 'on', 'startIndex': 5, 'length': 2, 'score': 0.724984169, 'modelTypeId': -1, 'modelType': 'Unknown', 'recognitionSources': ['model']}]}}}}
    ```

    A JSON válasz formázott olvashatóság:

    ```JSON
    {
        "query": "turn on all lights",
        "prediction": {
            "topIntent": "HomeAutomation.TurnOn",
            "intents": {
                "HomeAutomation.TurnOn": {
                    "score": 0.5375382
                },
                "None": {
                    "score": 0.08687421
                },
                "HomeAutomation.TurnOff": {
                    "score": 0.0207554
                }
            },
            "entities": {
                "HomeAutomation.Operation": [
                    "on"
                ],
                "$instance": {
                    "HomeAutomation.Operation": [
                        {
                            "type": "HomeAutomation.Operation",
                            "text": "on",
                            "startIndex": 5,
                            "length": 2,
                            "score": 0.724984169,
                            "modelTypeId": -1,
                            "modelType": "Unknown",
                            "recognitionSources": [
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

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Kimondott szöveg hozzáadása és betanítása](../get-started-get-model-rest-apis.md)