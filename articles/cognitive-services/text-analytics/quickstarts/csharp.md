---
title: 'Gyors útmutató: A Text Analytics szolgáltatás meghívása a .NET-hez készült Azure SDK-val ésC#'
titleSuffix: Azure Cognitive Services
description: Az Text Analytics szolgáltatás és C#a használatának megkezdését segítő információk és kódok.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/18/2019
ms.author: assafi
ms.openlocfilehash: 09713528f51675f6e9d7f3073b6c81b095d23631
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356960"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Gyors útmutató: A .NET SDK használata és C# a Text Analytics szolgáltatás meghívása
<a name="HOLTop"></a>

Ez a rövid útmutató segítséget nyújt az Azure SDK for .NET használatának C# megkezdéséhez és a nyelv elemzéséhez. Bár a [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba.

> [!NOTE]
> A minta forráskódja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics) érhető el.

Technikai részletekért tekintse meg a .NET-hez készült SDK [text Analytics dokumentációját](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Előfeltételek

* A [Visual Studio 2017 vagy újabb verziójának bármely kiadása]
* A [.net-hez](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) készült Text Analytics SDK

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

Szükség van a [végpontra és a hozzáférési kulcsra](../How-tos/text-analytics-how-to-access-key.md) is, amelyet a rendszer a regisztráció során generált.

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>A Visual Studio-megoldás létrehozása és az SDK telepítése

1. Hozzon létre egy új Console app (.NET Core) projektet. Nyissa meg a [Visual studiót](https://visualstudio.microsoft.com/vs/).
1. Kattintson a jobb gombbal a megoldásra, és válassza a **megoldás NuGet-csomagok kezelése**lehetőséget.
1. Válassza ki a **Browse** (Tallózás) lapot. Keressen rá a **Microsoft. Azure. CognitiveServices. Language. TextAnalytics**kifejezésre.

## <a name="authenticate-your-credentials"></a>Hitelesítő adatok hitelesítése

1. Adja hozzá a `using` következő utasításokat a fő Class-fájlhoz (amely alapértelmezés szerint program.cs).

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Net.Http;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
    using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
    using Microsoft.Rest;
    ```

2. Hozzon létre `ApiKeyServiceClientCredentials` egy új osztályt a hitelesítő adatok tárolásához és az egyes kérelmekhez való hozzáadásához.

    ```csharp
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string apiKey;

        public ApiKeyServiceClientCredentials(string apiKey)
        {
            this.apiKey = apiKey;
        }

        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }
            request.Headers.Add("Ocp-Apim-Subscription-Key", this.apiKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Frissítse az `Program` osztályt. Adjon hozzá egy állandó tagot a Text Analytics API kulcshoz, és egy másikat a szolgáltatási végponthoz. Ne felejtse el használni a Text Analytics erőforrásának megfelelő Azure-helyet.

    ```csharp
    //Enter your Text Analytics (TA) API Key (available in Azure Portal -> your TA resource -> Keys)
    private const string ApiKey = "enter-your-textanalytics-api-key-here";
    //You can get the resource location from Azure Portal -> your TA resource -> Overview
    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://<your-location>.api.cognitive.microsoft.com";
    ```
> [!Tip]
> Az éles rendszerekben a titkok biztonságának növelése érdekében javasoljuk, hogy használja a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>Text Analytics-ügyfél létrehozása

A projekt `Main` függvényében hívja meg a meghívni kívánt mintavételi módszert. Adja meg `Endpoint` a `ApiKey` definiált és paramétereket.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(ApiKey);
        var client = new TextAnalyticsClient(credentials)
        {
            Endpoint = Endpoint
        };

        // Change the console encoding to display non-ASCII characters.
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

A következő szakaszok azt ismertetik, hogyan hívhatja meg az egyes szolgáltatások funkcióit.

## <a name="perform-sentiment-analysis"></a>Érzelmek elemzésének elvégzése

1. Hozzon létre egy `SentimentAnalysisExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. Az elemezni kívánt `MultiLanguageInput` dokumentumokat tartalmazó objektumok listájának létrehozása.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life.")
            });
        //...
    }
    ```

3. Ugyanebben a függvényben hívja `client.SentimentAsync()` meg és szerezze be az eredményt. Ezután ismételje meg az eredményeket. Nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és hangulati pontszámát. Ha a 0 érték közel van, akkor a negatív, míg az 1. ponthoz közeledő pontszám pozitív érzést jelez.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Nyelvfelismerés végrehajtása

1. Hozzon létre egy `DetectLanguageExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. A dokumentumokat tartalmazó `LanguageInput` objektumok listájának létrehozása.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English.")
                    });
        //...
    }
    ```

3. Ugyanebben a függvényben hívja `client.DetectLanguageAsync()` meg és szerezze be az eredményt. Ezután ismételje meg az eredményeket. Nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és az első visszaadott nyelvet.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
```

## <a name="perform-entity-recognition"></a>Entitás-felismerés végrehajtása

1. Hozzon létre egy `RecognizeEntitiesExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. A dokumentumokat tartalmazó `MultiLanguageBatchInput` objektumok listájának létrehozása.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {
        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.")
            });
        //...
    }
    ```

3. Ugyanebben a függvényben hívja `client.EntitiesAsync()` meg és szerezze be az eredményt. Ezután ismételje meg az eredményeket. Nyomtassa ki az egyes dokumentumok AZONOSÍTÓit. Minden észlelt entitás esetében nyomtassa ki a wikipedia nevét, típusát és altípusait (ha vannak ilyenek), valamint az eredeti szöveg helyét.

    ```csharp
    var entitiesResult = await client.EntitiesAsync(false, inputDocuments);

    // Printing recognized entities
    foreach (var document in entitiesResult.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Entities:");
        foreach (var entity in document.Entities)
        {
            Console.WriteLine($"\t\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
            foreach (var match in entity.Matches)
            {
                Console.WriteLine($"\t\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
            }
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 0,      Length: 9,      Score: 1.000
                Name: Bill Gates,       Type: Person,   Sub-Type: N/A
                        Offset: 25,     Length: 10,     Score: 1.000
                Name: Paul Allen,       Type: Person,   Sub-Type: N/A
                        Offset: 40,     Length: 10,     Score: 0.999
                Name: April 4,  Type: Other,    Sub-Type: N/A
                        Offset: 54,     Length: 7,      Score: 0.800
                Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
                        Offset: 54,     Length: 13,     Score: 0.800
                Name: BASIC,    Type: Other,    Sub-Type: N/A
                        Offset: 89,     Length: 5,      Score: 0.800
                Name: Altair 8800,      Type: Other,    Sub-Type: N/A
                        Offset: 116,    Length: 11,     Score: 0.800
```

## <a name="perform-key-phrase-extraction"></a>Fő kifejezés kibontásának elvégzése

1. Hozzon létre egy `KeyPhraseExtractionExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. A dokumentumokat tartalmazó `MultiLanguageBatchInput` objektumok listájának létrehozása.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("en", "1", "My cat might need to see a veterinarian.")
                    });
        //...
    }
    ```

3. Ugyanebben a függvényben hívja `client.KeyPhrasesAsync()` meg és szerezze be az eredményt. Ezután ismételje meg az eredményeket. Nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és az észlelt kulcsfontosságú kifejezéseket.

    ```csharp
    var kpResults = await client.KeyPhrasesAsync(false, inputDocuments);

    // Printing keyphrases
    foreach (var document in kpResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} ");

        Console.WriteLine("\t Key phrases:");

        foreach (string keyphrase in document.KeyPhrases)
        {
            Console.WriteLine($"\t\t{keyphrase}");
        }
    }
    ```

### <a name="output"></a>Output

```console
Document ID: 1
         Key phrases:
                cat
                veterinarian
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)


* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
