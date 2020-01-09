---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: ea526648b1b37919eb41953937d3afa72f7f39e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446257"
---
<a name="HOLTop"></a>

[Dokumentáció](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | [minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE]
> A cikkben található kód a Text Analytics .NET SDK egyszerűségének szinkron módszereit használja. Éles környezetekben javasolt a kötegelt aszinkron módszerek használata a teljesítmény és a méretezhetőség érdekében. Például a [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) hívása az [érzelmek ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet)helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/)

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-net-core-application"></a>Új .NET Core-alkalmazás létrehozása

Hozzon létre egy új .NET Core Console-alkalmazást a Visual Studio IDE használatával. Ez egy egyszerű ""Helló világ!"alkalmazás" projektet hoz létre egyetlen C# forrásfájl esetén: *program.cs*.

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

## <a name="object-model"></a>Objektummodell

Az Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum, amely az Azure-ban hitelesíti magát a kulccsal, és lehetővé teszi, hogy a függvényeket egyetlen karakterláncként vagy batchként fogadja el. Az API-ra szinkron módon vagy aszinkron módon küldhet szöveget. A válasz objektum fogja tartalmazni az összes elküldött dokumentum elemzési információit. 

## <a name="code-examples"></a>Kódpéldák

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy új `ApiKeyServiceClientCredentials` osztályt a hitelesítő adatok tárolásához, és adja hozzá őket az ügyfél kéréseihez. Ezen belül hozzon létre egy felülbírálást `ProcessHttpRequestAsync()`, amely hozzáadja a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

[!code-csharp[Client class](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=clientClass)]

Hozzon létre egy metódust a [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum létrehozásához a végponttal és egy, a kulcsot tartalmazó `ApiKeyServiceClientCredentials` objektummal.

[!code-csharp[Client authentication](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=authentication)]

A program `main()` metódusában hívja meg a hitelesítési módszert az ügyfél létrehozásához.

## <a name="sentiment-analysis"></a>Véleményelemzés

Hozzon létre egy új, `SentimentAnalysisExample()` nevű függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [hangulat ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) függvényt. A visszaadott [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) objektum a `Score` sikerességét fogja tartalmazni, és ha nem, akkor `errorMessage`. 

Ha a 0 érték közel van, akkor a negatív, míg az 1. ponthoz közeledő pontszám pozitív érzést jelez.

[!code-csharp[Sentiment analysis](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=sentiment)]

### <a name="output"></a>Kimenet

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy `languageDetectionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. A visszaadott [LanguageResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) objektum az észlelt nyelvek listáját fogja tartalmazni `DetectedLanguages` ha sikeres, és egy `errorMessage`, ha nem.  Nyomtassa ki az első visszaadott nyelvet.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméterrel megadhatja a kétbetűs országkódot. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztring `countryHint = ""` értékre állítja.

[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=languageDetection)]

### <a name="output"></a>Kimenet

```console
Language: English
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre egy `RecognizeEntitiesExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg az [entitások ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ismételje meg az eredményeket. A visszaadott [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) objektum az észlelt entitások listáját fogja tartalmazni `Entities` ha sikeres, és egy `errorMessage`, ha nem. Minden észlelt entitás esetében nyomtassa ki a típusát, altípusát, a wikipedia nevét (ha vannak), valamint az eredeti szöveg helyét.

[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=entityRecognition)]


### <a name="output"></a>Kimenet

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

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozzon létre egy `KeyPhraseExtractionExample()` nevű új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg a [alkifejezések ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ha a művelet sikeres, az eredmény tartalmazni fogja a `KeyPhrases` észlelt kulcsos kifejezéseket, és egy `errorMessage`, ha nem. Minden észlelt kulcs kifejezésének nyomtatása.

[!code-csharp[Key phrase extraction example](~/cognitive-services-dotnet-sdk-samples/samples/TextAnalytics/synchronous/Program.cs?name=keyPhraseExtraction)]


### <a name="output"></a>Kimenet

```console
Key phrases:
    cat
    veterinarian
```
