---
title: 'Rövid útmutató: Text Analytics ügyféltár a Ruby | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban észlelheti a nyelvet az Azure Cognitive Services Ruby Text Analytics ügyfélkönyvtárának használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0d4d32a413dd22c55f1b2f01dce3a3df81f5f729
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77919668"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-ruby"></a>Rövid útmutató: A Ruby Text Analytics ügyfélkönyvtárának használata

Ismerkedés a Text Analytics ügyféltárral. Az alábbi lépésekkel telepítheti a csomagot, és kipróbálhatja az alapvető feladatok példakódját.

A Szövegelemzési ügyféltár segítségével hajtsa végre a következőket:

* Hangulatelemzés
* Nyelvfelismerés
* Entitások felismerése
* Kulcskifejezések kinyerése

> [!NOTE]
> Ez a rövid útmutató csak a Text Analytics 2.1-es verziójára vonatkozik. Jelenleg a Ruby v3-as ügyfélkönyvtára nem érhető el.

[Referenciadokumentáció](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics) | [csomag (RubyGems)](https://rubygems.org/gems/azure_cognitiveservices_textanalytics) | [minták](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

<a name="HOLTop"></a>

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Ruby](https://www.ruby-lang.org/) jelenlegi verziója
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez. 
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. Ezt később a rövid útmutatóban fogja megtenni.
    * Használhatja az ingyenes tarifacsomag a szolgáltatás kipróbálásához, és frissítse később egy fizetett szint éles környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-ruby-application"></a>Új Ruby-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. Ezután hozzon `GemFile`létre egy nevű fájlt és egy Ruby fájlt a kódhoz.

```console
mkdir myapp && cd myapp
```

A `GemFile`alkalmazásban adja hozzá a következő sorokat az ügyféltár függőségként való hozzáadásához.

```ruby
source 'https://rubygems.org'
gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
```

A Ruby-fájlban importálja a következő csomagokat.

[!code-ruby[Import statements](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=includeStatement)]

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának. 

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```ruby
const subscription_key = '<paste-your-text-analytics-key-here>'
const endpoint = `<paste-your-text-analytics-endpoint-here>`
```

## <a name="object-model"></a>Objektummodell 

A Text Analytics-ügyfél a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szöveg elemzésére, egyetlen karakterláncként vagy kötegként. 

A program a szöveget a `documents`használt `dictionary` módszertől függően `id`a `text`, `language` a kombinációját és attribútumokat tartalmazó objektumok listájaként küldi el az API-nak. Az `text` attribútum az eredeti `language`helyen tárolja az elemzendő szöveget, és az `id` bármilyen érték lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzési adatait tartalmazza. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Pythonszöveg-elemzési ügyfélkódtárban végezni:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Kulcskifejezés kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre `TextAnalyticsClient`egy nevű osztályt. 

```ruby
class TextAnalyticsClient
  @textAnalyticsClient
  #...
end
```

Ebben az osztályban hozzon létre egy függvényt, amelynek célja `initialize` az ügyfél hitelesítése a kulcs és a végpont használatával. 

[!code-ruby[initialize function for authentication](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=initialize)]

Az osztályon kívül használja az `new()` ügyfél funkcióját a példányosítására.

[!code-ruby[client creation](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=clientCreation)] 

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Hangulatelemzés

Az ügyfélobjektumban hozzon `AnalyzeSentiment()` létre egy nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját veszi fel. Hívja fel az `sentiment()` ügyfél funkcióját, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját és a hangulatpontszámát. A 0-hoz közelebbi pontszám negatív érzést, míg az 1-hez közelebbi pontszám pozitív véleményt jelez.

[!code-ruby[client method for sentiment analysis](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=analyzeSentiment)] 

Az ügyfélfüggön kívül hozzon `SentimentAnalysisExample()` létre egy `TextAnalyticsClient` új függvényt, amely a korábban létrehozott objektumot veszi fel. Hozzon létre `MultiLanguageInput` egy objektumlistát, amely tartalmazza az elemezni kívánt dokumentumokat. Minden objektum tartalmaz `id` `Language` egy `text` t és egy attribútumot. Az `text` attribútum tárolja az elemzendő szöveget, `language` a dokumentum `id` nyelve, és bármilyen érték lehet. Ezután hívja meg `AnalyzeSentiment()` az ügyfél funkcióját.

[!code-ruby[sentiment analysis document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=sentimentCall)] 

Hívja `SentimentAnalysisExample()` meg a függvényt.

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

Az ügyfélobjektumban hozzon `DetectLanguage()` létre egy nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját veszi fel. Hívja fel az `detect_language()` ügyfél funkcióját, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját, és észlelte a nyelvet.

[!code-ruby[client method for language detection](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguage)] 

Az ügyfélfüggön kívül hozzon `DetectLanguageExample()` létre egy `TextAnalyticsClient` új függvényt, amely a korábban létrehozott objektumot veszi fel. Hozzon létre `LanguageInput` egy objektumlistát, amely tartalmazza az elemezni kívánt dokumentumokat. Minden objektum tartalmaz `id`egy `text` t és egy attribútumot. Az `text` attribútum tárolja az elemzendő `id` szöveget, és bármilyen érték lehet. Ezután hívja meg `DetectLanguage()` az ügyfél funkcióját.

[!code-ruby[language detection document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=detectLanguageCall)] 

Hívja `DetectLanguageExample()` meg a függvényt.

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

Az ügyfélobjektumban hozzon `RecognizeEntities()` létre egy nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját veszi fel. Hívja fel az `entities()` ügyfél funkcióját, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját és a felismert entitásokat.

[!code-ruby[client method for entity recognition](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntities)]

Az ügyfélfüggön kívül hozzon `RecognizeEntitiesExample()` létre egy `TextAnalyticsClient` új függvényt, amely a korábban létrehozott objektumot veszi fel. Hozzon létre `MultiLanguageInput` egy objektumlistát, amely tartalmazza az elemezni kívánt dokumentumokat. Minden objektum tartalmaz `id`egy `language`, `text` a és egy attribútumot. Az `text` attribútum tárolja az elemzendő szöveget, `language` a szöveg `id` nyelve, és bármilyen érték lehet. Ezután hívja meg `RecognizeEntities()` az ügyfél funkcióját.

[!code-ruby[entity recognition documents and method call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=recognizeEntitiesCall)] 

Hívja `RecognizeEntitiesExample()` meg a függvényt.

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

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Az ügyfélobjektumban hozzon `ExtractKeyPhrases()` létre egy nevű függvényt, amely a később létrehozandó bemeneti dokumentumok listáját veszi fel. Hívja fel az `key_phrases()` ügyfél funkcióját, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját és a kinyert kulcskifejezéseket.

[!code-ruby[key phrase extraction client method](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=extractKeyPhrases)] 

Az ügyfélfüggön kívül hozzon `KeyPhraseExtractionExample()` létre egy `TextAnalyticsClient` új függvényt, amely a korábban létrehozott objektumot veszi fel. Hozzon létre `MultiLanguageInput` egy objektumlistát, amely tartalmazza az elemezni kívánt dokumentumokat. Minden objektum tartalmaz `id`egy `language`, `text` a és egy attribútumot. Az `text` attribútum tárolja az elemzendő szöveget, `language` a szöveg `id` nyelve, és bármilyen érték lehet. Ezután hívja meg `ExtractKeyPhrases()` az ügyfél funkcióját.

[!code-ruby[key phrase document creation and call](~/cognitive-services-ruby-sdk-samples/samples/text_analytics.rb?name=keyPhrasesCall)]


Hívja `KeyPhraseExtractionExample()` meg a függvényt.

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
