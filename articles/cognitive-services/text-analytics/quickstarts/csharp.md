---
title: 'Gyors útmutató: A .NET-hez készült ügyféloldali kódtár Text Analytics | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ezzel a rövid útmutatóval megkezdheti az Azure Cognitive Services Text Analytics API használatának megkezdését.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/05/2019
ms.author: assafi
ms.openlocfilehash: 4373cd8da8d302722c5edbe3ee716eec96e6419f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881052"
---
# <a name="quickstart-text-analytics-client-library-for-net"></a>Gyors útmutató: Text Analytics ügyféloldali kódtár a .NET-hez
<a name="HOLTop"></a>

Ismerkedjen meg a .NET-hez készült Text Analytics ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. 

A következő műveleteket végezheti el a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

* Hangulatelemzés
* Nyelvfelismerés
* Entitások felismerése
* A kulcsfontosságú kifejezések kinyerése

[](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/textanalytics?view=azure-dotnet-preview) | A dokumentációs[könyvtár forráskód](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Language.TextAnalytics) | [-csomagjához (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics/) | tartozó[minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples)

> [!NOTE] 
> A jelen cikkben szereplő bemutató kód a Text Analytics .NET SDK egyszerűségének szinkron módszereit használja. Éles környezetben azonban ajánlott a kötegelt aszinkron módszerek használata a saját alkalmazásaiban, hogy azok méretezhetők és rugalmasak maradjanak. Például a [SentimentBatchAsync ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentimentbatchasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet-preview) hívása az [érzelmek ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet)helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [.net Core SDK](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása

Az Azure Cognitive Services által az alkalmazások hitelesítéséhez szükséges kulcsot az Azure-erőforrások jelentik, amelyekhez előfizetett. Hozzon létre egy erőforrást Text Analytics a helyi gépen található [Azure Portal](../../cognitive-services-apis-create-account.md) vagy az [Azure CLI](../../cognitive-services-apis-create-account-cli.md) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Az erőforrás megtekintése a [Azure Portal](https://portal.azure.com/)

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű `TEXT_ANALYTICS_SUBSCRIPTION_KEY`kulcshoz.

### <a name="create-a-new-c-application"></a>Új C# alkalmazás létrehozása

Hozzon létre egy új .NET Core-alkalmazást az előnyben részesített szerkesztőben vagy az IDE-ben. 

A konzol ablakban (például cmd, PowerShell vagy bash) `dotnet new` az paranccsal hozzon létre egy új, a nevű `text-analytics quickstart`Console-alkalmazást. Ez a parancs egy egyszerű ""Helló világ!"alkalmazás" C# projektet hoz létre egyetlen forrásfájlban: *program.cs*. 

```console
dotnet new console -n text-analytics-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

A projekt könyvtárában nyissa meg a *program.cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben. Adja hozzá a `using` következő irányelveket:

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

Az alkalmazás `Main` metódusában hozzon létre változókat az erőforrás Azure-végpontja és kulcsa számára. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez be kell állítania és újra meg kell nyitnia a szerkesztőt, az IDE-t vagy a shellt. A metódusokat később kell megadnia.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```csharp
static void Main(string[] args)
{
    // replace this endpoint with the correct one for your Azure resource. 
    string endpoint = $"https://westus2.api.cognitive.microsoft.com";
    //This sample assumes you have created an environment variable for your key
    string key = Environment.GetEnvironmentVariable("TEXT_ANALYTICS_SUBSCRIPTION_KEY");
    ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials(key));
    Console.OutputEncoding = System.Text.Encoding.UTF8;
    SentimentAnalysisExample(client);
    // languageDetectionExample(client);
    // RecognizeEntitiesExample(client);
    // KeyPhraseExtractionExample(client);
    Console.ReadLine();
}
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse az Text Analytics .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Language.TextAnalytics --version 4.0.0
```

Ha a Visual Studio IDE-t használja, az ügyféloldali kódtár letölthető NuGet-csomagként érhető el.

## <a name="object-model"></a>Objektummodell

Az Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclient?view=azure-dotnet) objektum, amely az Azure-ban hitelesíti magát a kulccsal, és lehetővé teszi, hogy a függvényeket egyetlen karakterláncként vagy batchként fogadja el. Az API-ra szinkron módon vagy aszinkron módon küldhet szöveget. A válasz objektum fogja tartalmazni az összes elküldött dokumentum elemzési információit. 


## <a name="code-examples"></a>Példák a kódokra

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre `ApiKeyServiceClientCredentials` egy új osztályt a hitelesítő adatok tárolásához, és adja hozzá őket az ügyfél kéréseihez. Ezen belül hozzon létre egy felülbírálást `ProcessHttpRequestAsync()` , amely hozzáadja a kulcsot a `Ocp-Apim-Subscription-Key` fejléchez.

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

`main()` A metódusban hozza létre az ügyfelet.

```csharp
ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials(key));
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre egy nevű `SentimentAnalysisExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [hangulat ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.sentiment?view=azure-dotnet) függvényt. A visszaadott [SentimentResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.sentimentresult?view=azure-dotnet) objektum a `errorMessage` sikeres és `Score` a ha nem értéket fogja tartalmazni. 

Ha a 0 érték közel van, akkor a negatív, míg az 1. ponthoz közeledő pontszám pozitív érzést jelez.

```csharp
static void SentimentAnalysisExample(ITextAnalyticsClient client){
    var result = client.Sentiment("I had the best day of my life.", "en");
    Console.WriteLine($"Sentiment Score: {result.Score:0.00}");
}
```

### <a name="output"></a>Output

```console
Sentiment Score: 0.87
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy nevű `languageDetectionExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi át, és hívja meg a [DetectLanguage ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.detectlanguage?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_DetectLanguage_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. `DetectedLanguages` Ha [](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.languageresult?view=azure-dotnet) a`errorMessage` sikeres, és ha nem, a visszaadott LanguageResult objektum tartalmazza az észlelt nyelvek listáját.  Nyomtassa ki az első visszaadott nyelvet.

> [!Tip]
> Bizonyos esetekben nehéz lehet nyelveket egyértelműsítse a bemenet alapján. A `countryHint` paraméter használatával kétbetűs országkód adható meg. Alapértelmezés szerint az API az "USA"-t használja alapértelmezett countryHintként, hogy eltávolítsa ezt a paramétert úgy, hogy ezt az értéket üres sztringre `countryHint = ""` állítja.

```csharp
static void languageDetectionExample(ITextAnalyticsClient client){
    var result = client.DetectLanguage("This is a document written in English.");
    Console.WriteLine($"Language: {result.DetectedLanguages[0].Name}");
}
```
<!--
[!code-csharp[Language Detection example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

### <a name="output"></a>Output

```console
Language: English
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre egy nevű `RecognizeEntitiesExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg az [entitások ()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.entities?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_Entities_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Ismételje meg az eredményeket. A visszaadott [EntitiesResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.models.entitiesresult?view=azure-dotnet) objektum az észlelt entitások `Entities` listáját fogja tartalmazni, ha az `errorMessage` sikeres, és ha nem. Minden észlelt entitás esetében nyomtassa ki a típusát, altípusát, a wikipedia nevét (ha vannak), valamint az eredeti szöveg helyét.

```csharp
static void entityRecognitionExample(ITextAnalyticsClient client){

    var result = client.Entities("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.");
    Console.WriteLine("Entities:");
    foreach (var entity in result.Entities){
        Console.WriteLine($"\tName: {entity.Name},\tType: {entity.Type ?? "N/A"},\tSub-Type: {entity.SubType ?? "N/A"}");
        foreach (var match in entity.Matches){
            Console.WriteLine($"\t\tOffset: {match.Offset},\tLength: {match.Length},\tScore: {match.EntityTypeScore:F3}");
        }
    }
}
```
<!--
[!code-csharp[Entity Recognition example](~/cognitive-services-dotnet-sdk-samples/samples/language/Program.cs?name=language-detection)]
-->

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

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozzon létre egy nevű `KeyPhraseExtractionExample()` új függvényt, amely a korábban létrehozott ügyfelet veszi fel, és hívja meg a [()](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.language.textanalytics.textanalyticsclientextensions.keyphrases?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Language_TextAnalytics_TextAnalyticsClientExtensions_KeyPhrases_Microsoft_Azure_CognitiveServices_Language_TextAnalytics_ITextAnalyticsClient_System_String_System_String_System_Nullable_System_Boolean__System_Threading_CancellationToken_) függvényt. Az eredmény tartalmazza az észlelt kulcsfontosságú kifejezések `KeyPhrases` listáját, ha az sikeres volt, `errorMessage` és ha nem, akkor. Minden észlelt kulcs kifejezésének nyomtatása.

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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)


* [A Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)
