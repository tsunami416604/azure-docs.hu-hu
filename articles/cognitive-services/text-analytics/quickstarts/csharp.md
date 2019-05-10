---
title: 'Gyors útmutató: Használatával C# a szövegelemzési API meghívására.'
titleSuffix: Azure Cognitive Services
description: Ezekkel a rövid útmutatókkal és kódmintákkal gyorsan elsajátíthatja a Text Analytics API használatának alapjait.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 04/29/2019
ms.author: assafi
ms.openlocfilehash: c521be03f884227116a21c8c5396d47cdd1ae253
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466536"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: Használatával C# a Text Analytics kognitív szolgáltatás hívásához
<a name="HOLTop"></a>

Ez a rövid útmutató segítségével megkezdheti a Text Analytics SDK-val nyelvi elemzése C#. Bár a [Szövegelemzés](//go.microsoft.com/fwlink/?LinkID=759711) REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics).

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../How-tos/text-analytics-how-to-access-key.md) is rendelkeznie kell.

> [!Tip]
>  Bár a [HTTP-végpontokat](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) közvetlenül C#-ból is meg lehet hívni, a Microsoft.Azure.CognitiveServices.Language SDK jelentősen megkönnyíti a szolgáltatás meghívását, mivel nem kell a JSON szerializálásával és deszerializálásával foglalkozni.
>
> Néhány fontos hivatkozás:
> - [SDK Nuget oldal](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-kód](https://github.com/Azure/azure-sdk-for-net/tree/master/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)

## <a name="create-the-visual-studio-solution-and-install-the-sdk"></a>A Visual Studio-megoldás létrehozása és az SDK telepítése

1. Hozzon létre egy új Konzolalkalmazás (.NET Core) projektet [Visual Studio](https://visualstudio.microsoft.com/vs/).
1. Kattintson a jobb gombbal a megoldásra, majd kattintson a **Manage NuGet Packages for Solution** (NuGet-csomagok kezelése a megoldáshoz) parancsra.
1. Válassza ki a **Browse** (Tallózás) lapot, majd keressen rá a **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** szövegre.

## <a name="authenticate-your-credentials"></a>A hitelesítő adatokat

1. Adja hozzá a következő `using` utasítások a fő osztályban fájlba (`Program.cs` alapértelmezés szerint).

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

2. Hozzon létre egy új `ApiKeyServiceClientCredentials` tárolja a hitelesítő adatokat, és hozzáadhatja őket minden egyes kérelem osztályt.

    ```csharp
    /// <summary>
    /// Allows authentication to the API using a basic apiKey mechanism
    /// </summary>
    class ApiKeyServiceClientCredentials : ServiceClientCredentials
    {
        private readonly string subscriptionKey;

        /// <summary>
        /// Creates a new instance of the ApiKeyServiceClientCredentails class
        /// </summary>
        /// <param name="subscriptionKey">The subscription key to authenticate and authorize as</param>
        public ApiKeyServiceClientCredentials(string subscriptionKey)
        {
            this.subscriptionKey = subscriptionKey;
        }

        /// <summary>
        /// Add the Basic Authentication Header to each outgoing request
        /// </summary>
        /// <param name="request">The outgoing request</param>
        /// <param name="cancellationToken">A token to cancel the operation</param>
        public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            if (request == null)
            {
                throw new ArgumentNullException("request");
            }

            request.Headers.Add("Ocp-Apim-Subscription-Key", this.subscriptionKey);
            return base.ProcessHttpRequestAsync(request, cancellationToken);
        }
    }
    ```

3. Frissítés a `Program` osztályt, és a egy állandó tagot a Text Analytics előfizetési kulcs és egy másikat a a szolgáltatásvégpont hozzáadása. Fontos, hogy a megfelelő Azure-régiót használni a Szövegelemzés előfizetéshez.

    ```csharp
    private const string SubscriptionKey = "enter-your-key-here";

    private const string Endpoint = "enter-your-service-endpoint-here"; // For example: "https://westus.api.cognitive.microsoft.com";
    ```
> [!Tip]
> A titkos kulcsok az éles rendszereket a telepítés biztonságának használatát javasoljuk [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)
>

## <a name="create-a-text-analytics-client"></a>A Text Analytics-ügyfél létrehozása

Az a `Main` függvény a projekt hívja meg a minta metódust szeretne meghívni, és adja át a `Endpoint` és `SubscriptionKey` definiált paraméterek.

```csharp
    public static void Main(string[] args)
    {
        var credentials = new ApiKeyServiceClientCredentials(SubscriptionKey);
        var client = new TextAnalyticsClient(credentials)
        {
            //Replace 'westus' with the correct region for your Text Analytics subscription
            Endpoint = "https://westus.api.cognitive.microsoft.com"
        };

        // Change console encoding to display non-ASCII characters
        Console.OutputEncoding = System.Text.Encoding.UTF8;
        SentimentAnalysisExample(client).Wait();
        // DetectLanguageExample(client).Wait();
        // RecognizeEntitiesExample(client).Wait();
        // KeyPhraseExtractionExample(client).Wait();
        Console.ReadLine();
    }
```

Következő szakasz ismerteti, hogyan lehet hívni az API-szolgáltatások mindegyike.

## <a name="sentiment-analysis"></a>Hangulatelemzés

1. Hozzon létre egy új függvényt nevű `SentimentAnalysisExample()` az ügyfél korábban létrehozott paraméterrel.
2. Lista készítése `MultiLanguageInput` objektumokat, az elemezni kívánt dokumentumokat tartalmazó.

    ```csharp
    public static async Task SentimentAnalysisExample(TextAnalyticsClient client)
    {
        // The documents to be analyzed. Add the language of the document. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "I had the best day of my life."),
                new MultiLanguageInput("en", "2", "This was a waste of my time. The speaker put me to sleep."),
                new MultiLanguageInput("es", "3", "No tengo dinero ni nada que dar..."),
                new MultiLanguageInput("it", "4", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
            });
        //...
    }
    ```

3. Ugyanabban a függvényben hívja `client.SentimentAsync()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és véleménypontszámot. 0 közelebb pontszámot azt jelzi, hogy egy negatív véleményt jelölnek, míg 1 közelebb pontszámot azt jelzi, hogy egy pozitív véleményt.

    ```csharp
    var result = await client.SentimentAsync(false, inputDocuments);

    // Printing sentiment results
    foreach (var document in result.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Sentiment Score: {document.Score:0.00}");
    }
    ```

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Nyelvfelismerés

1. Hozzon létre egy új függvényt nevű `DetectLanguageExample()` az ügyfél korábban létrehozott paraméterrel.
2. Lista készítése `LanguageInput` objektumokat, amely tartalmazza a dokumentumok.

    ```csharp
    public static async Task DetectLanguageExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for language detection. The ID can be any value.
        var inputDocuments = new LanguageBatchInput(
                new List<LanguageInput>
                    {
                        new LanguageInput(id: "1", text: "This is a document written in English."),
                        new LanguageInput(id: "2", text: "Este es un document escrito en Español."),
                        new LanguageInput(id: "3", text: "这是一个用中文写的文件")
                    });
        //...
    }
    ```

3. Ugyanabban a függvényben hívja `client.DetectLanguageAsync()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és az első visszaadott nyelv.

    ```csharp
    var langResults = await client.DetectLanguageAsync(false, inputDocuments);

    // Printing detected languages
    foreach (var document in langResults.Documents)
    {
        Console.WriteLine($"Document ID: {document.Id} , Language: {document.DetectedLanguages[0].Name}");
    }
    ```

### <a name="output"></a>Kimenet

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

1. Hozzon létre egy új függvényt nevű `RecognizeEntitiesExample()` az ügyfél korábban létrehozott paraméterrel.
2. Lista készítése `MultiLanguageBatchInput` objektumokat, amely tartalmazza a dokumentumok.

    ```csharp
    public static async Task RecognizeEntitiesExample(TextAnalyticsClient client)
    {

        // The documents to be submitted for entity recognition. The ID can be any value.
        var inputDocuments = new MultiLanguageBatchInput(
            new List<MultiLanguageInput>
            {
                new MultiLanguageInput("en", "1", "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
                new MultiLanguageInput("es", "2", "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.")
            });
        //...
    }
    ```

3. Ugyanabban a függvényben hívja `client.EntitiesAsync()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum azonosítója. Az entitás minden egyes észlel, nyomtatási, a wikipedia neve, a típus és altípusok (ha létezik) valamint a helyeken és az eredeti szöveg.

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

### <a name="output"></a>Kimenet

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
Document ID: 2
         Entities:
                Name: Microsoft,        Type: Organization,     Sub-Type: N/A
                        Offset: 21,     Length: 9,      Score: 1.000
                Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
                        Offset: 60,     Length: 7,      Score: 0.991
                Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
                        Offset: 71,     Length: 13,     Score: 0.800
                Name: Seattle,  Type: Location, Sub-Type: N/A
                        Offset: 88,     Length: 7,      Score: 1.000
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

1. Hozzon létre egy új függvényt nevű `KeyPhraseExtractionExample()` az ügyfél korábban létrehozott paraméterrel.
2. Lista készítése `MultiLanguageBatchInput` objektumokat, amely tartalmazza a dokumentumok.

    ```csharp
    public static async Task KeyPhraseExtractionExample(TextAnalyticsClient client)
    {
        var inputDocuments = new MultiLanguageBatchInput(
                    new List<MultiLanguageInput>
                    {
                        new MultiLanguageInput("ja", "1", "猫は幸せ"),
                        new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                        new MultiLanguageInput("en", "3", "My cat might need to see a veterinarian."),
                        new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                    });
        //...
    }
    ```

3. Ugyanabban a függvényben hívja `client.KeyPhrasesAsync()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki az egyes dokumentumok azonosítója, és bármely felismert kulcskifejezéseket.

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

### <a name="output"></a>Kimenet

```console
Document ID: 1
         Key phrases:
                幸せ
Document ID: 2
         Key phrases:
                Stuttgart
                Hotel
                Fahrt
                Fu
Document ID: 3
         Key phrases:
                cat
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)


* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

