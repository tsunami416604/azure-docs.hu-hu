---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/02/2019
ms.author: aahi
ms.openlocfilehash: ffa14a4e3628bdc3453e8d536797b0edf6129a12
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75446211"
---
[Dokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [csomag (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Ruby](https://www.ruby-lang.org/) aktuális verziója

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása 

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

### <a name="create-a-new-ruby-application"></a>Új Ruby-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. Ezután hozzon létre egy `GemFile`nevű fájlt, és egy Ruby-fájlt a kódjához.

```console
mkdir myapp && cd myapp
```

A `GemFile`adja hozzá a következő sorokat az ügyféloldali függvénytár függőségként való hozzáadásához.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

A Ruby-fájlban importálja a következő csomagokat.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. 

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objektummodell 

A Text Analytics ügyfél a kulcsával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként. 

A rendszer elküldi a szöveget az API-nak `documents`-listaként, amely a használt módszertől függően `id`, `text`és `language` attribútumok kombinációját tartalmazó objektumokat `dictionary`. A `text` attribútum tárolja a forrás `language`elemezni kívánt szöveget, és a `id` értéke lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy `TextAnalyticsClient`nevű osztályt. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Ebben az osztályban hozzon létre egy `initialize` nevű függvényt az ügyfél hitelesítéséhez a kulcs és a végpont használatával. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Az osztályon kívül használja az ügyfél `new()` függvényét a létrehozásához.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Véleményelemzés

Az ügyfél objektumban hozzon létre egy `AnalyzeSentiment()` nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját fogadja. Hívja meg az ügyfél `sentiment()` függvényét, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a hangulat pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Az ügyfél függvényen kívül hozzon létre egy új, `SentimentAnalysisExample()` nevű függvényt, amely a korábban létrehozott `TextAnalyticsClient` objektumot veszi fel. Hozza létre `MultiLanguageInput` objektumok listáját, amely tartalmazza az elemezni kívánt dokumentumokat. Minden objektum tartalmaz egy `id`, `Language` és egy `text` attribútumot. A `text` attribútum az elemezni kívánt szöveget tárolja, `language` a dokumentum nyelve, a `id` pedig bármilyen érték lehet. Ezután hívja meg az ügyfél `AnalyzeSentiment()` függvényét.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Hívja meg a `SentimentAnalysisExample()` függvényt.

```ruby
SentimentAnalysisExample(textAnalyticsClient)
```

### <a name="output"></a>Kimenet

```console
===== SENTIMENT ANALYSIS =====
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

<a name="LanguageDetection"></a>

## <a name="language-detection"></a>Nyelvfelismerés

Az ügyfél objektumban hozzon létre egy `DetectLanguage()` nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját fogadja. Hívja meg az ügyfél `detect_language()` függvényét, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt nyelvet.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Az ügyfél függvényen kívül hozzon létre egy új, `DetectLanguageExample()` nevű függvényt, amely a korábban létrehozott `TextAnalyticsClient` objektumot veszi fel. Hozza létre `LanguageInput` objektumok listáját, amely tartalmazza az elemezni kívánt dokumentumokat. Minden objektum tartalmaz egy `id`és egy `text` attribútumot. A `text` attribútum tárolja az elemezni kívánt szöveget, és a `id` bármilyen érték lehet. Ezután hívja meg az ügyfél `DetectLanguage()` függvényét.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Hívja meg a `DetectLanguageExample()` függvényt.

```ruby
DetectLanguageExample(textAnalyticsClient)
```

### <a name="output"></a>Kimenet

```console
===== LANGUAGE EXTRACTION ======
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

<a name="EntityRecognition"></a>

## <a name="entity-recognition"></a>Entitások felismerése

Az ügyfél objektumban hozzon létre egy `RecognizeEntities()` nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját fogadja. Hívja meg az ügyfél `entities()` függvényét, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az elismert entitásokat.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Az ügyfél függvényen kívül hozzon létre egy új, `RecognizeEntitiesExample()` nevű függvényt, amely a korábban létrehozott `TextAnalyticsClient` objektumot veszi fel. Hozza létre `MultiLanguageInput` objektumok listáját, amely tartalmazza az elemezni kívánt dokumentumokat. Mindegyik objektum egy `id`, egy `language`és egy `text` attribútumot fog tartalmazni. A `text` attribútum az elemezni kívánt szöveget tárolja, `language` a szöveg nyelve, a `id` pedig bármilyen érték lehet. Ezután hívja meg az ügyfél `RecognizeEntities()` függvényét.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Hívja meg a `RecognizeEntitiesExample()` függvényt.

```ruby
RecognizeEntitiesExample(textAnalyticsClient)
```

### <a name="output"></a>Kimenet

```console
===== ENTITY RECOGNITION =====
Document ID: 1
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 0, Length: 9,   Score: 1.0

        Name: Bill Gates,       Type: Person,   Sub-Type: N/A
        Offset: 25, Length: 10, Score: 0.999847412109375

        Name: Paul Allen,       Type: Person,   Sub-Type: N/A
        Offset: 40, Length: 10, Score: 0.9988409876823425

        Name: April 4,  Type: Other,    Sub-Type: N/A
        Offset: 54, Length: 7,  Score: 0.8

        Name: April 4, 1975,    Type: DateTime, Sub-Type: Date
        Offset: 54, Length: 13, Score: 0.8

        Name: BASIC,    Type: Other,    Sub-Type: N/A
        Offset: 89, Length: 5,  Score: 0.8

        Name: Altair 8800,      Type: Other,    Sub-Type: N/A
        Offset: 116, Length: 11,        Score: 0.8

Document ID: 2
        Name: Microsoft,        Type: Organization,     Sub-Type: N/A
        Offset: 21, Length: 9,  Score: 0.999755859375

        Name: Redmond (Washington),     Type: Location, Sub-Type: N/A
        Offset: 60, Length: 7,  Score: 0.9911284446716309

        Name: 21 kilómetros,    Type: Quantity, Sub-Type: Dimension
        Offset: 71, Length: 13, Score: 0.8

        Name: Seattle,  Type: Location, Sub-Type: N/A
        Offset: 88, Length: 7,  Score: 0.9998779296875
```

<a name="KeyPhraseExtraction"></a>

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Az ügyfél objektumban hozzon létre egy `ExtractKeyPhrases()` nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját fogadja. Hívja meg az ügyfél `key_phrases()` függvényét, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a kinyert kulcs kifejezéseit.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Az ügyfél függvényen kívül hozzon létre egy új, `KeyPhraseExtractionExample()` nevű függvényt, amely a korábban létrehozott `TextAnalyticsClient` objektumot veszi fel. Hozza létre `MultiLanguageInput` objektumok listáját, amely tartalmazza az elemezni kívánt dokumentumokat. Mindegyik objektum egy `id`, egy `language`és egy `text` attribútumot fog tartalmazni. A `text` attribútum az elemezni kívánt szöveget tárolja, `language` a szöveg nyelve, a `id` pedig bármilyen érték lehet. Ezután hívja meg az ügyfél `ExtractKeyPhrases()` függvényét.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Hívja meg a `KeyPhraseExtractionExample()` függvényt.

```ruby
KeyPhraseExtractionExample(textAnalyticsClient)
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
                rock
Document ID: 4
         Key phrases:
                fútbol
```
