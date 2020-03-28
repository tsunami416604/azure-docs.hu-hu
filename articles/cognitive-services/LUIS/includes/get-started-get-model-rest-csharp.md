---
title: 'Modell beszereznie A REST-hívással c-ben #'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/31/2020
ms.author: diberry
ms.openlocfilehash: 96129b9141b4759fd61b539fa08354f02af3af7b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80151075"
---
## <a name="prerequisites"></a>Előfeltételek

* Azure Language Understanding – Authoring resource 32 karakterkulcs és szerzői végpont URL-címe. Hozzon létre az [Azure Portalon](../luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) vagy az [Azure CLI-n](../luis-how-to-azure-subscription.md#create-resources-in-azure-cli)keresztül.
* Importálja a [TravelAgent](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/change-model/TravelAgent.json) alkalmazást a cognitive-services-language-understanding GitHub-tárházból.
* Az importált TravelAgent alkalmazás LUIS-alkalmazásazonosítója. Az alkalmazásazonosító az alkalmazás irányítópultján látható.
* A kimondott szövegeket fogadó alkalmazáson belüli verzióazonosító. Az alapértelmezett azonosító a „0.1”.
* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio kód](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>Kimondott példaszövegek JSON-fájlja

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="change-model-programmatically"></a>Modell módosítása programozott módon

1. Hozzon létre egy új konzolalkalmazást, amely a C# `model-with-rest`nyelvet célozza meg, a projekt és a mappa nevével.

    ```console
    dotnet new console -lang C# -n model-with-rest
    ```

1. Telepítse a szükséges függőségeket a következő dotnet CLI-parancsokkal.

    ```console
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```
1. Írja felül a Program.cs fájlt a következő kóddal:

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Text;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    using System.Linq;

    // 3rd party NuGet packages
    using JsonFormatterPlus;

    namespace AddUtterances
    {
        class Program
        {
            // NOTE: use your LUIS authoring key - 32 character value
            static string authoringKey = "YOUR-KEY";

            // NOTE: Replace this endpoint with your authoring key endpoint
            // for example, your-resource-name.api.cognitive.microsoft.com
            static string endpoint = "YOUR-ENDPOINT";

            // NOTE: Replace this with the ID of your LUIS application
            static string appID = "YOUR-APP-ID";

            // NOTE: Replace this your version number
            static string appVersion = "0.1";

            static string host = String.Format("https://{0}/luis/authoring/v3.0-preview/apps/{1}/versions/{2}/", endpoint, appID, appVersion);

            // GET request with authentication
            async static Task<HttpResponseMessage> SendGet(string uri)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Get;
                    request.RequestUri = new Uri(uri);
                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // POST request with authentication
            async static Task<HttpResponseMessage> SendPost(string uri, string requestBody)
            {
                using (var client = new HttpClient())
                using (var request = new HttpRequestMessage())
                {
                    request.Method = HttpMethod.Post;
                    request.RequestUri = new Uri(uri);

                    if (!String.IsNullOrEmpty(requestBody))
                    {
                        request.Content = new StringContent(requestBody, Encoding.UTF8, "text/json");
                    }

                    request.Headers.Add("Ocp-Apim-Subscription-Key", authoringKey);
                    return await client.SendAsync(request);
                }
            }
            // Add utterances as string with POST request
            async static Task AddUtterances(string utterances)
            {
                string uri = host + "examples";

                var response = await SendPost(uri, utterances);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Added utterances.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Train app after adding utterances
            async static Task Train()
            {
                string uri = host  + "train";

                var response = await SendPost(uri, null);
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Sent training request.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Check status of training
            async static Task Status()
            {
                var response = await SendGet(host  + "train");
                var result = await response.Content.ReadAsStringAsync();
                Console.WriteLine("Requested training status.");
                Console.WriteLine(JsonFormatter.Format(result));
            }
            // Add utterances, train, check status
            static void Main(string[] args)
            {
                string utterances = @"
                [
                    {
                    'text': 'go to Seattle today',
                    'intentName': 'BookFlight',
                    'entityLabels': [
                        {
                        'entityName': 'Location::LocationTo',
                        'startCharIndex': 6,
                        'endCharIndex': 12
                        }
                    ]
                    },
                    {
                        'text': 'a barking dog is annoying',
                        'intentName': 'None',
                        'entityLabels': []
                    }
                ]
                ";
                AddUtterances(utterances).Wait();
                Train().Wait();
                Status().Wait();
            }
        }
    }
    ```

1. Cserélje le a `YOUR-` saját értékeivel kezdődő értékeket.

    |Információ|Cél|
    |--|--|
    |`YOUR-KEY`|A 32 karakteres szerzői kulcs.|
    |`YOUR-ENDPOINT`| A szerzői URL-végpont. Például: `replace-with-your-resource-name.api.cognitive.microsoft.com`. Az erőforrás nevét az erőforrás létrehozásakor állította be.|
    |`YOUR-APP-ID`| A LUIS-alkalmazásazonosító. |

    A hozzárendelt kulcsok és erőforrások a LUIS-portálon, a Kezelés szakaszban, az **Azure-erőforrások** lapon láthatók. Az alkalmazásazonosító ugyanabban a Kezelés szakaszban, az **Alkalmazás beállítások** lapján érhető el.

1. Hozza létre a konzolalkalmazást.

    ```console
    dotnet build
    ```

1. Futtassa a konzolalkalmazást. A konzol kimenete ugyanazt a JSON-t jeleníti meg, mint amit korábban a böngészőablakban látott.

    ```console
    dotnet run
    ```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte ezt a rövid útmutatót, törölje a fájlt a fájlrendszerből.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyakorlati tanácsok egy alkalmazáshoz](../luis-concept-best-practices.md)
