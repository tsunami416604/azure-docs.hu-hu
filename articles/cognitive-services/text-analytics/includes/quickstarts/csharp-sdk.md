---
title: 'Gyorsútmutató: A Text Analytics C#-ügyfélkódtárának használata | Microsoft Docs'
description: Ismerkedés a Text Analytics C#-ügyfélkódtárával
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 6adce0ed6b5b5768bd9a489fced25ce439a33e0a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203410"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

[3-as verzió referenciadokumentációja](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [3-as verzió kódtárforráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [3-as verzió csomagja (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [3-as verzió mintái](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[2-es verzió referenciadokumentációja](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [2-es verzió kódtárforráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [2-es verzió csomagja (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [2-es verzió mintái](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Ingyenes létrehozás](https://azure.microsoft.com/free/)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Hozzon létre egy Text Analytics-erőforrást,"  target="_blank">hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span></a> az Azure Portalon a kulcs és a végpont beszerzéséhez. 
    * Ahhoz, hogy az alkalmazást a Text Analytics API-hoz csatlakoztathassa, szüksége lesz egy kulcsra és egy végpontra a létrehozott erőforrásból. Ezt a rövid útmutató egy későbbi részében teheti meg.
    * A szolgáltatás kipróbálásához használhatja az ingyenes tarifacsomagot, amelyet frissíthet fizetős szintre az éles használathoz.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-net-core-application"></a>Egy új .NET Core-alkalmazás létrehozása

A Visual Studio IDE használatával hozzon létre egy új .NET Core-konzolalkalmazást. Ez létrehoz egy „Hello World!” projektet egy *program.cs* nevű C#-forráskódfájllal.

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Az ügyfélkódtár telepítéséhez először kattintson a **Megoldáskezelőben** a megoldáson a jobb gombbal, és válassza a **NuGet-csomagok kezelése** pontot. A megnyíló csomagkezelőben válassza a **Tallózás** elemet, jelölje be az **Előzetes verzió belefoglalása** lehetőséget, és keresse meg a következőt: `Azure.AI.TextAnalytics`. Válassza ki az `1.0.0-preview.2` verziót, majd a **Telepítés** parancsot. Használhatja a [Csomagkezelő konzolját](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) is.

> [!TIP]
> Szeretné egyben megtekinteni a teljes gyorsútmutatós kódfájlt? Megtalálhatja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), amely a gyorsútmutató kódmintáit is tartalmazza. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Az ügyfélkódtár telepítéséhez először kattintson a **Megoldáskezelőben** a megoldáson a jobb gombbal, és válassza a **NuGet-csomagok kezelése** pontot. A megnyíló csomagkezelőben válassza a **Tallózás** elemet, és keresse meg a következőt: `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kattintson erre, majd a **Telepítés** parancsra. Használhatja a [Csomagkezelő konzolját](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package) is.

> [!TIP]
> Szeretné egyben megtekinteni a teljes gyorsútmutatós kódfájlt? Megtalálhatja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), amely a gyorsútmutató kódmintáit is tartalmazza. 

---

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Nyissa meg a *program.cs* fájlt, majd adja hozzá a következő `using`-direktívákat:

```csharp
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly TextAnalyticsApiKeyCredential credentials = new TextAnalyticsApiKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az alkalmazás `Main` metódusát. Az itt meghívott metódusokat később fogja definiálni.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityPIIExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Nyissa meg a *program.cs* fájlt, majd adja hozzá a következő `using`-direktívákat:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Cserélje le az alkalmazás `Main` metódusát. Az itt meghívott metódusokat később fogja definiálni.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient`-objektum, amely a kulcs használatával hitelesíti magát az Azure-ban, és egyedi sztringként vagy kötegelt formában megadott szövegeket fogadó függvényeket biztosít. Az API felé szinkron vagy aszinkron módon is küldhet szöveget. A válaszobjektum az egyes dokumentumok elemzési információit fogja tartalmazni. 

Ha a `3.0-preview` verziót használja, egy opcionális `TextAnalyticsClientOptions`-példánnyal beállíthatja az ügyfél alapértelmezett beállításait (pl. az alapértelmezett nyelvet vagy az országra utaló tippet). Emellett használhat Azure Active Directory-jogkivonatot is hitelesítésre. 

## <a name="code-examples"></a>Kódpéldák

* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Nevesített entitások felismerése](#named-entity-recognition-ner)
* [Személyes adatok észlelése](#detect-personal-information)
* [Entitáskapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Bizonyosodjon meg arról, hogy az előbbi fő metódus létrehoz egy új ügyfélobjektumot a végponttal és a hitelesítő adatokkal.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy új `ApiKeyServiceClientCredentials` osztályt a hitelesítő adatok tárolásához, illetve hogy ezeket hozzáadhassa az egyes ügyfélkérésekhez. Ezen belül bírálja felül a `ProcessHttpRequestAsync()` függvényt, amely hozzáadja a kulcsát az `Ocp-Apim-Subscription-Key` fejléchez.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Hozzon létre egy metódust a [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum példányosításához a végpontjával és a kulcsát tartalmazó `ApiKeyServiceClientCredentials` objektummal.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy új függvényt `SentimentAnalysisExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `AnalyzeSentiment()` függvényét. A visszaadott `Response<DocumentSentiment>` objektum tartalmazni fogja a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, illetve az egyes mondatokhoz tartozó hangulatelemzést, ha sikeres volt. Hiba esetén egy `RequestFailedException` kivételt fog megjeleníteni.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [length {sentence.GraphemeLength}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.GraphemeOffset, sentence.GraphemeLength)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.ConfidenceScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.ConfidenceScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.ConfidenceScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Document sentiment: Positive

        Sentence [length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy új függvényt `SentimentAnalysisExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) függvényét. A visszaadott [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) objektum siker esetén tartalmazni fogja a hangulat `Score` pontszámát, ha sikeres volt, és egy `errorMessage` üzenetet, ha nem. 

A 0-hoz közeli értékek negatív, az 1-hez közeliek pozitív érzelmekre utalnak.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)


Hozzon létre egy új függvényt `LanguageDetectionExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `DetectLanguage()` függvényét. A visszaadott `Response<DetectedLanguage>` objektum tartalmazni fogja az észlelt nyelvet a nevével és ISO-6391-es kódjával. Hiba esetén egy `RequestFailedException` kivételt fog megjeleníteni.

> [!Tip]
> Bizonyos esetekben nehéz lehet a nyelveket megkülönböztetni a bemenet alapján. A `countryHint` paraméterrel megadhat egy 2 karakteres országkódot. Alapértelmezés szerint az API a „US” országkódot használja alapértelmezett countryHint paraméterként. Ha ez a működés nem megfelelő, állítsa az értéket üres karakterláncra (`countryHint = ""`). Más alapértelmezés beállításához állítsa be a `TextAnalyticsClientOptions.DefaultCountryHint` tulajdonságot, és adja át az ügyfél inicializálásakor.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    DetectedLanguage detectedLanguage = client.DetectLanguage("Ce document est rédigé en Français.");
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Kimenet

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy új függvényt `languageDetectionExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényét. A visszaadott [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) objektum tartalmazza az észlelt nyelvek listáját a `DetectedLanguages` alatt, ha sikeres volt, és egy `errorMessage` üzenetet, ha nem. Jelenítse meg az első visszaadott nyelvet.

> [!Tip]
> Bizonyos esetekben nehéz lehet a nyelveket megkülönböztetni a bemenet alapján. A `countryHint` paraméterrel megadhat egy 2 karakteres országkódot. Alapértelmezés szerint az API a „US” országkódot használja alapértelmezett countryHint paraméterként. Ha ez a működés nem megfelelő, állítsa az értéket üres karakterláncra (`countryHint = ""`).

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Kimenet

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Nevesített entitások felismerése (NER)

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)


> [!NOTE]
> A `3.0-preview` verzió újdonságai:
> * Az entitásfelismerés mostantól képes személyes adatok felismerésére is a szövegben.
> * Az entitáskapcsolás és az entitásfelismerés el lett választva egymástól.


Hozzon létre egy új függvényt `EntityRecognitionExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `RecognizeEntities()` függvényét, és végigmegy az eredményeken. A visszaadott `Response<IReadOnlyCollection<CategorizedEntity>>` objektum tartalmazza az észlelt entitások listáját. Hiba esetén egy `RequestFailedException` kivételt fog megjeleníteni.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Length: 7,      Score: 0.92

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Length: 9,      Score: 0.80
```

## <a name="detect-personal-information"></a>Személyes adatok észlelése

Hozzon létre egy új függvényt `EntityPIIExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `RecognizePiiEntities()` függvényét, és végigmegy az eredményeken. Az előző függvényhez hasonló módon a visszaadott `Response<IReadOnlyCollection<CategorizedEntity>>` objektum tartalmazza az észlelt entitások listáját. Hiba esetén egy `RequestFailedException` kivételt fog megjeleníteni.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tLength: {entity.GraphemeLength},\tScore: {entity.ConfidenceScore:F2}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category:
                Length: 11,     Score: 0.85
```


## <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy új függvényt `EntityLinkingExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `RecognizeLinkedEntities()` függvényét, és végigmegy az eredményeken. A visszaadott `Response<IReadOnlyCollection<LinkedEntity>>` objektum az észlelt entitások listáját jelöli. Hiba esetén egy `RequestFailedException` kivételt fog megjeleníteni. Mivel a társított entitások egyedileg vannak azonosítva, ugyanannak az entitásnak az előfordulásai egy `LinkedEntity` objektum alatt vannak csoportosítva, `LinkedEntityMatch` objektumok listájaként.

```csharp
static void EntityLinkingExample(TextAnalyticsClient client)
{
    var response = client.RecognizeLinkedEntities(
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
        "to develop and sell BASIC interpreters for the Altair 8800. " +
        "During his career at Microsoft, Gates held the positions of chairman, " +
        "chief executive officer, president and chief software architect, " +
        "while also being the largest individual shareholder until May 2014.");
    Console.WriteLine("Linked Entities:");
    foreach (var entity in response.Value)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.DataSourceEntityId},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tLength: {match.GraphemeLength},\tScore: {match.ConfidenceScore:F2}\n");
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Linked Entities:
        Name: Altair 8800,      ID: Altair 8800,        URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Length: 11,     Score: 0.78

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Length: 10,     Score: 0.55

                Text: Gates
                Length: 5,      Score: 0.55

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Length: 10,     Score: 0.53

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Length: 9,      Score: 0.47

                Text: Microsoft
                Length: 9,      Score: 0.47

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Length: 7,      Score: 0.25

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Length: 5,      Score: 0.28
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

> [!NOTE]
> A 2.1-es verzióban az entitáskapcsolás a NER-válasz részét képezi.

Hozzon létre egy új függvényt `RecognizeEntitiesExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak [Entities()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényét. Haladjon végig az eredményeken. A visszaadott [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) objektum tartalmazza az észlelt entitások listáját az `Entities` alatt, ha sikeres volt és egy `errorMessage` üzenetet, ha nem. Minden észlelt entitás esetében jelenítse meg az entitás típusát, altípusát és Wikipédia-nevét (ha van), valamint a helyét az eredeti szövegben.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy új függvényt `KeyPhraseExtractionExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `ExtractKeyPhrases()` függvényét. A visszaadott `<Response<IReadOnlyCollection<string>>` objektum tartalmazza az észlelt kulcskifejezések listáját. Hiba esetén egy `RequestFailedException` kivételt fog megjeleníteni.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value)
    {
        Console.WriteLine($"\t{keyphrase}");
    }
}
```

### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy új függvényt `KeyPhraseExtractionExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényét. Az eredmény tartalmazza az észlelt kulcskifejezések listáját a `KeyPhrases` alatt, ha sikeres volt, és egy `errorMessage` objektumot, ha nem. Jelenítse meg az észlelt kulcskifejezéseket.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

---
