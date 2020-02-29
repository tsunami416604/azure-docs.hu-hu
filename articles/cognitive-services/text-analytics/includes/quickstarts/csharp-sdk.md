---
title: 'Gyors útmutató: Text Analytics ügyféloldali kódtár C# | Microsoft Docs'
description: Ismerkedés a Text Analytics ügyféloldali függvénytárávalC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 02/26/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: b7c4bac7523cdec36beac64e2cd204588b0935fb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155514"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

[v3 hivatkozási dokumentáció](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 minta](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

[v2 hivatkozási dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hozzon létre egy Text Analytics-erőforrást,"  target="_blank">hozzon létre egy Text Analytics erőforrás <span class="docon docon-navigate-external x-hidden-focus"></span> -</a> a Azure Portal a kulcs és a végpont beszerzéséhez. 
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. Ezt később is megteheti a rövid útmutatóban.
    * Az ingyenes díjszabási csomaggal kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

Hozzon létre egy új .NET Core Console-alkalmazást a Visual Studio IDE használatával. Ezzel létrehoz egy ""Helló világ!"alkalmazás" projektet egyetlen C# forrásfájlban: *program.cs*.

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Az ügyféloldali kódtár telepítéséhez kattintson a jobb gombbal a megoldásra a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése**lehetőséget. A megnyíló csomagkezelő válassza a **Tallózás**lehetőséget, jelölje be az **előzetes verzió**használata jelölőnégyzetet, és keressen `Azure.AI.TextAnalytics`. Válassza a verzió `1.0.0-preview.2`, majd a **telepítés**lehetőséget. Használhatja a [Package Manager konzolt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is.

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Az ügyféloldali kódtár telepítéséhez kattintson a jobb gombbal a megoldásra a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése**lehetőséget. A megnyíló csomagkezelő lapon válassza a **Tallózás** lehetőséget, és keresse meg a `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kattintson rá, majd **telepítse**a következőt:. Használhatja a [Package Manager konzolt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is.

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza. 

---

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

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

Cserélje le az alkalmazás `Main` metódusát. Az itt megnevezett metódusokat később kell meghatároznia.

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

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Cserélje le az alkalmazás `Main` metódusát. Az itt megnevezett metódusokat később kell meghatároznia.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient`-objektum, amely az Azure-ban hitelesíti a kulcsot, és függvényeket biztosít a szöveg egyszerű karakterláncként vagy kötegként való fogadásához. Az API-ra szinkron módon vagy aszinkron módon küldhet szöveget. A válasz objektum fogja tartalmazni az összes elküldött dokumentum elemzési információit. 

Ha a `3.0-preview`verzióját használja, egy opcionális `TextAnalyticsClientOptions`-példánnyal is elvégezheti az ügyfél inicializálását különböző alapértelmezett beállításokkal (például az alapértelmezett nyelv vagy az ország-emlékeztető). Azure Active Directory jogkivonat használatával is végezhet hitelesítést. 

## <a name="code-examples"></a>Példák a kódokra

* [Hangulat elemzése](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner)
* [Személyes adatok észlelése](#detect-personal-information)
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Győződjön meg arról, hogy a korábban létrehozott fő metódus létrehoz egy új ügyfél-objektumot a végponttal és a hitelesítő adatokkal.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy új `ApiKeyServiceClientCredentials` osztályt a hitelesítő adatok tárolásához, és adja hozzá őket az ügyfél kéréseihez. Ezen belül hozzon létre egy felülbírálást `ProcessHttpRequestAsync()`, amely hozzáadja a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Hozzon létre egy metódust a [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum létrehozásához a végponttal és egy, a kulcsot tartalmazó `ApiKeyServiceClientCredentials` objektummal.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Hozzon létre egy `SentimentAnalysisExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `AnalyzeSentiment()` függvényt. A visszaadott `Response<DocumentSentiment>` objektum a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok sikerességi elemzését fogja tartalmazni. Ha hiba történt, egy `RequestFailedException`fog eldobni.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    string inputText = "I had the best day of my life. I wish you were there with me.";
    DocumentSentiment documentSentiment = client.AnalyzeSentiment(inputText);
    Console.WriteLine($"Document sentiment: {documentSentiment.Sentiment}\n");

    var si = new StringInfo(inputText);
    foreach (var sentence in documentSentiment.Sentences)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tText: \"{si.SubstringByTextElements(sentence.Offset, sentence.Length)}\"");
        Console.WriteLine($"\tSentence sentiment: {sentence.Sentiment}");
        Console.WriteLine($"\tPositive score: {sentence.SentimentScores.Positive:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.SentimentScores.Negative:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.SentimentScores.Neutral:0.00}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Text: "I had the best day of my life."
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Text: "I wish you were there with me."
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy új, `SentimentAnalysisExample()` nevű függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [hangulat ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) függvényt. A visszaadott [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) objektum a `Score` sikerességét fogja tartalmazni, és ha nem, akkor `errorMessage`. 

Ha a 0 érték közel van, akkor a negatív, míg az 1. ponthoz közeledő pontszám pozitív érzést jelez.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)


Hozzon létre egy `LanguageDetectionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `DetectLanguage()` függvényt. A visszaadott `Response<DetectedLanguage>` objektum az észlelt nyelvet és a hozzá tartozó nevet, valamint az ISO-6391 kódot fogja tartalmazni. Ha hiba történt, egy `RequestFailedException`fog eldobni.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméterrel megadhatja a kétbetűs országkódot. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztring `countryHint = ""`értékre állítja. Másik alapértelmezett beállításhoz állítsa be a `TextAnalyticsClientOptions.DefaultCountryHint` tulajdonságot, és adja át azt az ügyfél inicializálásakor.

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

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy `languageDetectionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. A visszaadott [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) objektum az észlelt nyelvek listáját fogja tartalmazni `DetectedLanguages` ha sikeres, és egy `errorMessage`, ha nem. Nyomtassa ki az első visszaadott nyelvet.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméterrel megadhatja a kétbetűs országkódot. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztring `countryHint = ""` értékre állítja.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Kimenet

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)


> [!NOTE]
> A `3.0-preview`verziójának új verziója:
> * Az entitások felismerése mostantól lehetővé teszi a személyes adatok szövegbeli észlelését.
> * Az entitások összekapcsolása mostantól el van különítve az entitások felismerésével.


Hozzon létre egy `EntityRecognitionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<IReadOnlyCollection<CategorizedEntity>>` objektum az észlelt entitások listáját fogja tartalmazni. Ha hiba történt, egy `RequestFailedException`fog eldobni.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Named Entities:
        Text: Seattle,  Category: Location,     Sub-Category: GPE
                Offset: 26,     Length: 7,      Score: 0.920

        Text: last week,        Category: DateTime,     Sub-Category: DateRange
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="detect-personal-information"></a>Személyes adatok észlelése

Hozzon létre egy `EntityPIIExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizePiiEntities()` függvényt, és ismételje meg az eredményeket. Az előző függvényhez hasonlóan a visszaadott `Response<IReadOnlyCollection<CategorizedEntity>>` objektum fogja tartalmazni az észlelt entitások listáját. Ha hiba történt, egy `RequestFailedException`fog eldobni.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    string inputText = "Insurance policy for SSN on file 123-12-1234 is here by approved.";
    var response = client.RecognizePiiEntities(inputText);
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value)
    {
        Console.WriteLine($"\tText: {entity.Text},\tCategory: {entity.Category},\tSub-Category: {entity.SubCategory}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Category: U.S. Social Security Number (SSN),    Sub-Category: None
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Entitás összekapcsolása

Hozzon létre egy `EntityLinkingExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeLinkedEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<IReadOnlyCollection<LinkedEntity>>` az észlelt entitások listáját jelöli. Ha hiba történt, egy `RequestFailedException`fog eldobni. Mivel a csatolt entitások egyedi módon vannak azonosítva, az ugyanahhoz az entitáshoz tartozó előfordulások egy `LinkedEntity` objektum alatt vannak csoportosítva `LinkedEntityMatch` objektumok listájában.

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
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Url}\tData Source: {entity.DataSource}");
        Console.WriteLine("\tMatches:");
        foreach (var match in entity.Matches)
        {
            Console.WriteLine($"\t\tText: {match.Text}");
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.Score:F3}\n");
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
                Offset: 116,    Length: 11,     Score: 0.777

        Name: Bill Gates,       ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25,     Length: 10,     Score: 0.555

                Text: Gates
                Offset: 161,    Length: 5,      Score: 0.555

        Name: Paul Allen,       ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40,     Length: 10,     Score: 0.533

        Name: Microsoft,        ID: Microsoft,  URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0,      Length: 9,      Score: 0.469

                Text: Microsoft
                Offset: 150,    Length: 9,      Score: 0.469

        Name: April 4,  ID: April 4,    URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54,     Length: 7,      Score: 0.248

        Name: BASIC,    ID: BASIC,      URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89,     Length: 5,      Score: 0.281
```

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

> [!NOTE]
> Az 2,1-es verzióban az entitások összekapcsolása szerepel a megjelenő válaszban.

Hozzon létre egy `RecognizeEntitiesExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg az [entitások ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ismételje meg az eredményeket. A visszaadott [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) objektum az észlelt entitások listáját fogja tartalmazni `Entities` ha sikeres, és egy `errorMessage`, ha nem. Minden észlelt entitás esetében nyomtassa ki a típusát, altípusát, a wikipedia nevét (ha vannak), valamint az eredeti szöveg helyét.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

#### <a name="version-30-preview"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Hozzon létre egy `KeyPhraseExtractionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `ExtractKeyPhrases()` függvényt. A visszaadott `<Response<IReadOnlyCollection<string>>` objektum az észlelt legfontosabb kifejezések listáját fogja tartalmazni. Ha hiba történt, egy `RequestFailedException`fog eldobni.

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

#### <a name="version-21"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy `KeyPhraseExtractionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg a [alkifejezések ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ha a művelet sikeres, az eredmény tartalmazni fogja a `KeyPhrases` észlelt kulcsos kifejezéseket, és egy `errorMessage`, ha nem. Minden észlelt kulcs kifejezésének nyomtatása.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

---
