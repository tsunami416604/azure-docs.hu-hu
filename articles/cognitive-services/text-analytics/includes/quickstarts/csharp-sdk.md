---
title: 'Gyors útmutató: Text Analytics ügyféloldali kódtár C# | Microsoft Docs'
description: Ismerkedés a Text Analytics ügyféloldali függvénytárávalC#
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/15/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: a907fb5347e6559e066f678c53bb16c7727a74b5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2020
ms.locfileid: "76987900"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

[v3 hivatkozási dokumentáció](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 minta](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

[v2 hivatkozási dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 minta](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/)

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

Hozzon létre egy új .NET Core Console-alkalmazást a Visual Studio IDE használatával. Ezzel létrehoz egy ""Helló világ!"alkalmazás" projektet egyetlen C# forrásfájlban: *program.cs*.

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Az ügyféloldali kódtár telepítéséhez kattintson a jobb gombbal a megoldásra a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése**lehetőséget. A megnyíló csomagkezelő válassza a **Tallózás**lehetőséget, jelölje be az **előzetes verzió**használata jelölőnégyzetet, és keressen `Azure.AI.TextAnalytics`. Kattintson rá, majd **telepítse**a következőt:. Használhatja a [Package Manager konzolt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is.

Nyissa meg a *program.cs* fájlt, és adja hozzá a következő `using` irányelveket:

```csharp
using System;
using Azure.AI.TextAnalytics;
```

Az alkalmazás `Program` osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az alkalmazás `Main` metódusát. Az itt megnevezett metódusokat később kell meghatároznia.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, key);
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

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Az ügyféloldali kódtár telepítéséhez kattintson a jobb gombbal a megoldásra a **megoldáskezelő** , majd válassza a **NuGet-csomagok kezelése**lehetőséget. A megnyíló csomagkezelő lapon válassza a **Tallózás** lehetőséget, és keresse meg a `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`. Kattintson rá, majd **telepítse**a következőt:. Használhatja a [Package Manager konzolt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is.

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
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Győződjön meg arról, hogy a korábban létrehozott fő metódus létrehoz egy új ügyfél-objektumot a végponttal és a kulccsal. 

```csharp
var client = new TextAnalyticsClient(endpoint, key);
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy új `ApiKeyServiceClientCredentials` osztályt a hitelesítő adatok tárolásához, és adja hozzá őket az ügyfél kéréseihez. Ezen belül hozzon létre egy felülbírálást `ProcessHttpRequestAsync()`, amely hozzáadja a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Hozzon létre egy metódust a [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum létrehozásához a végponttal és egy, a kulcsot tartalmazó `ApiKeyServiceClientCredentials` objektummal.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Véleményelemzés

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Hozzon létre egy `SentimentAnalysisExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `AnalyzeSentiment()` függvényt. A visszaadott `Response<AnalyzeSentimentResult>` objektum tartalmazni fogja a teljes bemeneti dokumentum hangulati címkéjét és pontszámát, valamint az egyes mondatok sikerességi elemzését, ha ez sikeres, és egy `Value.ErrorMessage`, ha nem.

```csharp
static void SentimentAnalysisExample(TextAnalyticsClient client)
{
    var response = client.AnalyzeSentiment("I had the best day of my life. I wish you were there with me.");
    Console.WriteLine($"Document sentiment: {response.Value.DocumentSentiment.SentimentClass}\n");
    foreach (var sentence in response.Value.SentenceSentiments)
    {
        Console.WriteLine($"\tSentence [offset {sentence.Offset}, length {sentence.Length}]");
        Console.WriteLine($"\tSentence sentiment: {sentence.SentimentClass}");
        Console.WriteLine($"\tPositive score: {sentence.PositiveScore:0.00}");
        Console.WriteLine($"\tNegative score: {sentence.NegativeScore:0.00}");
        Console.WriteLine($"\tNeutral score: {sentence.NeutralScore:0.00}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Document sentiment: Positive

        Sentence [offset 0, length 30]
        Sentence sentiment: Positive
        Positive score: 1.00
        Negative score: 0.00
        Neutral score: 0.00

        Sentence [offset 31, length 30]
        Sentence sentiment: Neutral
        Positive score: 0.21
        Negative score: 0.02
        Neutral score: 0.77
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy új, `SentimentAnalysisExample()` nevű függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [hangulat ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) függvényt. A visszaadott [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) objektum a `Score` sikerességét fogja tartalmazni, és ha nem, akkor `errorMessage`. 

Ha a 0 érték közel van, akkor a negatív, míg az 1. ponthoz közeledő pontszám pozitív érzést jelez.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)


Hozzon létre egy `LanguageDetectionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `DetectLanguage()` függvényt. A visszaadott `Response<DetectLanguageResult>` objektum az észlelt nyelvet fogja tartalmazni `Value.PrimaryLanguage` ha sikeres, és egy `Value.ErrorMessage`, ha nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméterrel megadhatja a kétbetűs országkódot. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztring `countryHint = ""`értékre állítja. Másik alapértelmezett beállításhoz állítsa be a `TextAnalyticsClientOptions.DefaultCountryHint` tulajdonságot, és adja át azt az ügyfél inicializálásakor.

```csharp
static void LanguageDetectionExample(TextAnalyticsClient client)
{
    var response = client.DetectLanguage("Ce document est rédigé en Français.");
    var detectedLanguage = response.Value.PrimaryLanguage;
    Console.WriteLine("Language:");
    Console.WriteLine($"\t{detectedLanguage.Name},\tISO-6391: {detectedLanguage.Iso6391Name}\n");
}
```

### <a name="output"></a>Kimenet

```console
Language:
        French, ISO-6391: fr
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy `languageDetectionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. A visszaadott [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) objektum az észlelt nyelvek listáját fogja tartalmazni `DetectedLanguages` ha sikeres, és egy `errorMessage`, ha nem.  Nyomtassa ki az első visszaadott nyelvet.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméterrel megadhatja a kétbetűs országkódot. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztring `countryHint = ""` értékre állítja.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Kimenet

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)


> [!NOTE]
> A (z) `3.0-preview`verzióban:
> * A tájékoztató külön módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitások összekapcsolása egy külön kérelem, mint a kapcsolatfelvétel.

Az alábbi kód a személyes adatok észlelésére szolgál, amely nyilvános előzetes verzióban érhető el.


Hozzon létre egy `EntityRecognitionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<RecognizeEntitiesResult>` objektum az észlelt entitások listáját fogja tartalmazni `Value.NamedEntities` ha sikeres, és egy `Value.ErrorMessage`, ha nem. Minden észlelt entitás esetében nyomtassa ki a típusát és altípusát, ha létezik.

```csharp
static void EntityRecognitionExample(TextAnalyticsClient client)
{
    var response = client.RecognizeEntities("I had a wonderful trip to Seattle last week.");
    Console.WriteLine("Named Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Named Entities:
        Text: Seattle,  Type: Location, Sub-Type: N/A
                Offset: 26,     Length: 7,      Score: 0.806

        Text: last week,        Type: DateTime, Sub-Type: N/A
                Offset: 34,     Length: 9,      Score: 0.800
```

## <a name="using-ner-to-detect-personal-information"></a>A személyes adatok észlelése a következővel

Hozzon létre egy `EntityPIIExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizePiiEntities()` függvényt, és ismételje meg az eredményeket. Az előző függvényhez hasonlóan a visszaadott `Response<RecognizeEntitiesResult>` objektum az észlelt entitások listáját fogja tartalmazni `Value.NamedEntities` ha sikeres, és egy `Value.ErrorMessage`, ha nem.

```csharp
static void EntityPIIExample(TextAnalyticsClient client)
{
    var response = client.RecognizePiiEntities("Insurance policy for SSN on file 123-12-1234 is here by approved.");
    Console.WriteLine("Personally Identifiable Information Entities:");
    foreach(var entity in response.Value.NamedEntities)
    {
        Console.WriteLine($"\tText: {entity.Text},\tType: {entity.Type},\tSub-Type: {entity.SubType ?? "N/A"}");
        Console.WriteLine($"\t\tOffset: {entity.Offset},\tLength: {entity.Length},\tScore: {entity.Score:F3}\n");
    }
}
```

### <a name="output"></a>Kimenet

```console
Personally Identifiable Information Entities:
        Text: 123-12-1234,      Type: U.S. Social Security Number (SSN),        Sub-Type: N/A
                Offset: 33,     Length: 11,     Score: 0.850
```


## <a name="entity-linking"></a>Entitás összekapcsolása

Hozzon létre egy `EntityLinkingExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, hívja meg a `RecognizeLinkedEntities()` függvényt, és ismételje meg az eredményeket. A visszaadott `Response<RecognizeLinkedEntitiesResult>` objektum az észlelt entitások listáját fogja tartalmazni `Value.LinkedEntities` ha sikeres, és egy `Value.ErrorMessage`, ha nem. Mivel a csatolt entitások egyedi módon vannak azonosítva, az ugyanahhoz az entitáshoz tartozó előfordulások egy `LinkedEntity` objektum alatt vannak csoportosítva `LinkedEntityMatch` objektumok listájában.

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
    foreach (var entity in response.Value.LinkedEntities)
    {
        Console.WriteLine($"\tName: {entity.Name},\tID: {entity.Id},\tURL: {entity.Uri}\tData Source: {entity.DataSource}");
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

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

> [!NOTE]
> Az 2,1-es verzióban az entitások összekapcsolása szerepel a megjelenő válaszban.

Hozzon létre egy `RecognizeEntitiesExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg az [entitások ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ismételje meg az eredményeket. A visszaadott [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) objektum az észlelt entitások listáját fogja tartalmazni `Entities` ha sikeres, és egy `errorMessage`, ha nem. Minden észlelt entitás esetében nyomtassa ki a típusát, altípusát, a wikipedia nevét (ha vannak), valamint az eredeti szöveg helyét.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Hozzon létre egy `KeyPhraseExtractionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a `ExtractKeyPhrases()` függvényt. Ha a művelet sikeres volt, az eredmény tartalmazza az észlelt `Value.KeyPhrases`ek listáját, és ha nem, akkor `Value.ErrorMessage`. Minden észlelt kulcs kifejezésének nyomtatása.

```csharp
static void KeyPhraseExtractionExample(TextAnalyticsClient client)
{
    var response = client.ExtractKeyPhrases("My cat might need to see a veterinarian.");

    // Printing key phrases
    Console.WriteLine("Key phrases:");

    foreach (string keyphrase in response.Value.KeyPhrases)
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

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy `KeyPhraseExtractionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg a [alkifejezések ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ha a művelet sikeres, az eredmény tartalmazni fogja a `KeyPhrases` észlelt kulcsos kifejezéseket, és egy `errorMessage`, ha nem. Minden észlelt kulcs kifejezésének nyomtatása.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

---