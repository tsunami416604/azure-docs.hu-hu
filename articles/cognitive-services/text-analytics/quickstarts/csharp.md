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
ms.date: 07/30/2019
ms.author: assafi
ms.openlocfilehash: 6bd3907392dad626c1eeb1823c929f1a35d544dd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697676"
---
# <a name="quickstart-use-the-net-sdk-and-c-to-call-the-text-analytics-service"></a>Gyors útmutató: A .NET SDK használata és C# a Text Analytics szolgáltatás meghívása
<a name="HOLTop"></a>

Ez a rövid útmutató segítséget nyújt az Azure SDK for .NET használatának C# megkezdéséhez és a nyelv elemzéséhez. Bár a [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API kompatibilis a legtöbb programozási nyelvvel, az SDK egyszerű módszert kínál a szolgáltatás integrálására az alkalmazásokba.

> [!NOTE]
> A jelen cikkben szereplő bemutató kód a Text Analytics .NET SDK egyszerűségének szinkron módszereit használja. Éles környezetben azonban ajánlott a kötegelt aszinkron módszerek használata a saját alkalmazásaiban, hogy azok méretezhetők és rugalmasak maradjanak. A helyett használhatja `SentimentBatchAsync` például a `Sentiment`következőt:.
>
> Ennek a példának a kötegelt aszinkron változata a githubon érhető [](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/TextAnalytics)el.

Technikai részletekért tekintse meg a .NET-hez készült SDK [text Analytics dokumentációját](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet).

## <a name="prerequisites"></a>Előfeltételek

* A Visual Studio 2017-es vagy újabb verziójának bármely kiadása
* A [.net-hez](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics) készült Text Analytics SDK

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

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
        SentimentAnalysisExample(client);
        // DetectLanguageExample(client);
        // RecognizeEntitiesExample(client);
        // KeyPhraseExtractionExample(client);
        Console.ReadLine();
    }
```

A következő szakaszok azt ismertetik, hogyan hívhatja meg az egyes szolgáltatások funkcióit.

## <a name="perform-sentiment-analysis"></a>Érzelmek elemzésének elvégzése

1. Hozzon létre egy `SentimentAnalysisExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. Ugyanebben a függvényben hívja `client.Sentiment()` meg és szerezze be az eredményt. Az eredmény `Score` abban az esetben fog szerepelni, ha a művelet `errorMessage` sikeres, és ha nem. Ha a 0 érték közel van, akkor a negatív, míg az 1. ponthoz közeledő pontszám pozitív érzést jelez.

    ```csharp
    var result = client.Sentiment("I had the best day of my life.", "en");

    // Printing sentiment results
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
    ```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="perform-language-detection"></a>Nyelvfelismerés végrehajtása

1. Hozzon létre egy `DetectLanguageExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. Ugyanebben a függvényben hívja `client.DetectLanguage()` meg és szerezze be az eredményt. `DetectedLanguages` Ha a művelet sikeres, `errorMessage` és ha nem, akkor az eredmény tartalmazza az észlelt nyelvek listáját. Ezután nyomtassa ki az első visszaadott nyelvet.

    ```csharp
    var result = client.DetectLanguage("This is a document written in English.");

    // Printing detected languages
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
    ```

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméter használatával kétbetűs országkód adható meg. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre `countryHint = ""` állítja.

### <a name="output"></a>Output

```console
Language: English
```

## <a name="perform-entity-recognition"></a>Entitás-felismerés végrehajtása

1. Hozzon létre egy `RecognizeEntitiesExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át.
2. Ugyanebben a függvényben hívja `client.Entities()` meg és szerezze be az eredményt. Ezután ismételje meg az eredményeket. `Entities` Ha a művelet sikeres, `errorMessage` és ha nem, akkor az eredmény az észlelt entitások listáját fogja tartalmazni. Minden észlelt entitás esetében nyomtassa ki a típusát, altípusát, a wikipedia nevét (ha vannak), valamint az eredeti szöveg helyét.

    ```csharp
    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");

    // Printing recognized entities
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
    ```

### <a name="output"></a>Output

```console
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
2. Ugyanebben a függvényben hívja `client.KeyPhrases()` meg és szerezze be az eredményt. Az eredmény tartalmazza az észlelt kulcsfontosságú kifejezések `KeyPhrases` listáját, ha az sikeres volt, `errorMessage` és ha nem, akkor. Ezután nyomtassa ki az észlelt kulcsfontosságú kifejezéseket.

    ```csharp
    var result = client.KeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in result.KeyPhrases)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
    ```

### <a name="output"></a>Output

```console
Key phrases:
    cat
    veterinarian
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)


* [A Text Analytics áttekintése](../overview.md)
* [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
