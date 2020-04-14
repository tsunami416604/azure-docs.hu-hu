---
title: 'Rövid útmutató: Text Analytics ügyféltár C# | Microsoft dokumentumok'
description: 'Első lépések a C-hez készült Text Analytics ügyféltárral #'
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: assafi
ms.openlocfilehash: 2fa2e40ba2a7fe84b6df57bfb711d01332b8f523
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81274729"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

[v3 Referenciadokumentáció](https://aka.ms/azsdk-net-textanalytics-ref-docs) | [v3 Könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics) | [v3 csomag (NuGet)](https://www.nuget.org/packages/Azure.AI.TextAnalytics) | [v3 minták](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics/samples)

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[v2 Referenciadokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [v2 Könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [v2 csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [v2 minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/)
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez.  Üzembe helyezése után kattintson **az Ugrás az erőforrásra**gombra.
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. A kulcs és a végpont beillesztése az alábbi kódba később a rövid útmutatóban.
    * Használhatja az ingyenes tarifacsomag ( )`F0`kipróbálni a szolgáltatást, és frissítse később egy fizetett szint éles környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-net-core-application"></a>Új .NET Core alkalmazás létrehozása

A Visual Studio IDE használatával hozzon létre egy új .NET Core konzolalkalmazást. Ez létrehoz egy "Hello World" projekt egyetlen C# forrásfájl: *program.cs*.

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Telepítse az ügyfélkönyvtárat úgy, hogy a jobb gombbal a **megoldásra** kattint a Solution Explorer ben, és válassza a **NuGet-csomagok kezelése parancsot.** A megnyíló csomagkezelőben válassza a **Tallózás**lehetőséget, jelölje **be az Előzetes kiadás felvétele**jelölőnégyzetet, és keresse meg a mezőt. `Azure.AI.TextAnalytics` Válassza `1.0.0-preview.4`a verziót, majd **a Telepítés lehetőséget.** A [Csomagkezelő konzolt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is használhatja.

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/TextAnalytics/program.cs)található. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Telepítse az ügyfélkönyvtárat úgy, hogy a jobb gombbal a **megoldásra** kattint a Solution Explorer ben, és válassza a **NuGet-csomagok kezelése parancsot.** A megnyíló csomagkezelőben válassza `Microsoft.Azure.CognitiveServices.Language.TextAnalytics`a **Tallózás** és keresés lehetőséget. Kattintson rá, majd telepítse a **programot.** A [Csomagkezelő konzolt](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-powershell#find-and-install-a-package)is használhatja.

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/samples/TextAnalytics/synchronous/Program.cs)található. 

---

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Nyissa meg a *program.cs* `using` fájlt, és adja hozzá a következő irányelveket:

```csharp
using Azure;
using System;
using System.Globalization;
using Azure.AI.TextAnalytics;
```

Az alkalmazás osztályában `Program` hozzon létre változókat az erőforrás kulcsához és végpontjához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly AzureKeyCredential credentials = new AzureKeyCredential("<replace-with-your-text-analytics-key-here>");
private static readonly Uri endpoint = new Uri("<replace-with-your-text-analytics-endpoint-here>");
```

Cserélje le az `Main` alkalmazás metódusát. Később meg fogja határozni az itt megnevezett módszereket.

```csharp
static void Main(string[] args)
{
    var client = new TextAnalyticsClient(endpoint, credentials);
    // You will implement these methods later in the quickstart.
    SentimentAnalysisExample(client);
    LanguageDetectionExample(client);
    EntityRecognitionExample(client);
    EntityLinkingExample(client);
    KeyPhraseExtractionExample(client);

    Console.Write("Press any key to exit.");
    Console.ReadKey();
}
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Nyissa meg a *program.cs* `using` fájlt, és adja hozzá a következő irányelveket:

[!code-csharp[Import directives](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=imports)]

Az alkalmazás osztályában `Program` hozzon létre változókat az erőforrás kulcsához és végpontjához. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```csharp
private static readonly string key = "<replace-with-your-text-analytics-key-here>";
private static readonly string endpoint = "<replace-with-your-text-analytics-endpoint-here>";
```

Cserélje le az `Main` alkalmazás metódusát. Később meg fogja határozni az itt megnevezett módszereket.

[!code-csharp[main method](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=main)]

---

## <a name="object-model"></a>Objektummodell

A Text Analytics-ügyfél egy `TextAnalyticsClient` olyan objektum, amely hitelesíti magát az Azure-ban a kulcs használatával, és funkciókat biztosít a szöveg egyetlen karakterláncként vagy kötegként történő fogadásához. Az API-ba szinkron módon vagy aszinkron módon is küldhet szöveget. A válaszobjektum minden elküldött dokumentum elemzési adatait tartalmazza. 

Ha a szolgáltatás `3.0-preview` verzióját használja, egy választható `TextAnalyticsClientOptions` példány segítségével inicializálhatja az ügyfelet különböző alapértelmezett beállításokkal (például alapértelmezett nyelv vagy országemlékeztető). Azure Active Directory-jogkivonat használatával is hitelesítheti. 

## <a name="code-examples"></a>Kódpéldák

* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Megnevezett entitások felismerése](#named-entity-recognition-ner)
* [Entitáscsatolás](#entity-linking)
* [Kulcskifejezés kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Győződjön meg arról, hogy a fő módszer a korábbi létrehoz egy új ügyfélobjektumot a végpont és a hitelesítő adatok.

```csharp
var client = new TextAnalyticsClient(endpoint, credentials);
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon `ApiKeyServiceClientCredentials` létre egy új osztályt a hitelesítő adatok tárolásához, és adja hozzá őket az ügyfél kéréseihez. Ezen belül hozzon létre `ProcessHttpRequestAsync()` egy felülbírálási, amely hozzáadja a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Hozzon létre egy módszert a [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum és `ApiKeyServiceClientCredentials` a kulcsot tartalmazó objektum példányosítására.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre `SentimentAnalysisExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `AnalyzeSentiment()` A `Response<DocumentSentiment>` visszaadott objektum tartalmazza a hangulatfeliratot és a teljes bemeneti dokumentum pontszámát, valamint az egyes mondatok hangulatelemzését, ha sikeres. Ha volt egy hiba, akkor `RequestFailedException`dobja a .

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

Hozzon létre `SentimentAnalysisExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és hívja meg a [Sentiment()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) függvényt. A visszaadott [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) objektum `Score` tartalmazza a `errorMessage` hangulatot, ha sikeres, és ha nem. 

A 0-hoz közeli pontszám negatív érzést jelez, míg az 1-hez közelebbi pontszám pozitív érzést jelez.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

```console
Sentiment Score: 0.87
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)


Hozzon létre `LanguageDetectionExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `DetectLanguage()` A `Response<DetectedLanguage>` visszaadott objektum tartalmazza az észlelt nyelvet a nevével és az ISO-6391 kódjával együtt. Ha volt egy hiba, akkor `RequestFailedException`dobja a .

> [!Tip]
> Bizonyos esetekben nehéz lehet a bevitel alapján félretenni a nyelveket. A `countryHint` paraméter segítségével kétbetűs országkódot adhat meg. Alapértelmezés szerint az API az "US" az alapértelmezett countryHint, hogy távolítsa el ezt a `countryHint = ""`viselkedést vissza állíthatja ezt a paramétert beállításával ezt az értéket üres karakterlánc . Másik alapértelmezett beállításhoz állítsa `TextAnalyticsClientOptions.DefaultCountryHint` be a tulajdonságot, és adja át az ügyfél inicializálása során.

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

Hozzon létre `languageDetectionExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és hívja meg a [DetectLanguage()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. A visszaadott [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) objektum tartalmazza az `DetectedLanguages` észlelt nyelvek `errorMessage` listáját, ha sikeres, és egy ha nem. Nyomtassa ki az első visszaadott nyelvet.

> [!Tip]
> Bizonyos esetekben nehéz lehet a bevitel alapján félretenni a nyelveket. A `countryHint` paraméter segítségével kétbetűs országkódot adhat meg. Alapértelmezés szerint az API az "US" az alapértelmezett countryHint, hogy távolítsa el ezt a `countryHint = ""` viselkedést vissza állíthatja ezt a paramétert beállításával ezt az értéket üres karakterlánc .

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Kimenet

```console
Language: English
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitásfelismerés (NER)

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)


> [!NOTE]
> Új verzió: `3.0-preview`
> * A gazdálkodó egység csatolása mostantól elkülönül a gazdálkodó egység kiismerésétől.


Hozzon létre `EntityRecognitionExample()` egy új függvényt, amely a `RecognizeEntities()` korábban létrehozott ügyfelet veszi, meghívja annak függvényét, és végighalad az eredményeken. A `Response<IReadOnlyCollection<CategorizedEntity>>` visszaadott objektum az észlelt entitások listáját fogja tartalmazni. Ha volt egy hiba, akkor `RequestFailedException`dobja a .

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

## <a name="entity-linking"></a>Entitáscsatolás

Hozzon létre `EntityLinkingExample()` egy új függvényt, amely a `RecognizeLinkedEntities()` korábban létrehozott ügyfelet veszi, meghívja annak függvényét, és végighalad az eredményeken. A `Response<IReadOnlyCollection<LinkedEntity>>` visszaadott az észlelt entitások listáját jelöli. Ha volt egy hiba, akkor `RequestFailedException`dobja a . Mivel a csatolt entitások egyedi azonosítása történik, ugyanazon entitás `LinkedEntity` előfordulásai `LinkedEntityMatch` objektum alatt objektumok listájaként vannak csoportosítva.

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
> A 2.1-es verzióban az entitásösszekapcsolás szerepel a NER-válaszban.

Hozzon létre `RecognizeEntitiesExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja az [Entitások()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Az eredményeket átkell itatni. A visszaadott [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) objektum tartalmazza az `Entities` észlelt entitások `errorMessage` listáját, ha sikeres, és egy ha nem. Minden észlelt entitáshoz nyomtassa ki a típusát, altípusát, Wikipédia-nevét (ha vannak ilyenek), valamint az eredeti szövegben lévő helyeket.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]

--- 


## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre `KeyPhraseExtractionExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `ExtractKeyPhrases()` A `<Response<IReadOnlyCollection<string>>` visszaadott objektum az észlelt kulcskifejezések listáját fogja tartalmazni. Ha volt egy hiba, akkor `RequestFailedException`dobja a .

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

Hozzon létre `KeyPhraseExtractionExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja a [KeyPhrases()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Az eredmény tartalmazza az észlelt kulcskifejezések `KeyPhrases` listáját, ha `errorMessage` sikeresek, és ha nem. Nyomtasson ki minden észlelt kulcskifejezést.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```

---
