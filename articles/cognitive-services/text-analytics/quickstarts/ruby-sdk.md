---
title: 'Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Ruby SDK-val'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan Ismerkedés a szövegelemzési API-val az Azure Cognitive Servicesben.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/08/2019
ms.author: tasharm
ms.openlocfilehash: 7def77c0b1cf99fcc2cee77a28782dddaf2ac45d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65994544"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-ruby-sdk"></a>Gyors útmutató: Meghívja a Text Analytics szolgáltatást, a Ruby SDK-val

<a name="HOLTop"></a>


Ez a rövid útmutató segítségével megkezdheti a Text Analytics SDK a Rubyhoz nyelvi elemzése. Bár a [Szövegelemzés](//go.microsoft.com/fwlink/?LinkID=759711) REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-ruby-sdk-samples/blob/master/samples/text_analytics.rb).

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../How-tos/text-analytics-how-to-access-key.md) is rendelkeznie kell. 

Töltse le és telepítse a Rubyt 2.5.5 verzió alapján a rendszer-architektúra a [Itt](https://rubyinstaller.org/downloads/).

> [!Tip]
>  Bár a [REST API-végpontokon](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) közvetlenül a Ruby, a `Microsoft.Azure.CognitiveServices.TextAnalytics` SDK megkönnyíti a szolgáltatás meghívása nélkül szerializálásához és deszerializálásához JSON.
>
> Néhány fontos hivatkozás:
> - [Ruby gem lap](https://rubygems.org/gems/azure_cognitiveservices_textanalytics)
> - [SDK-kódját a Githubon](https://github.com/Azure/azure-sdk-for-ruby/tree/master/data/azure_cognitiveservices_textanalytics/lib/v2.1/generated/azure_cognitiveservices_textanalytics)

<a name="RubyProject"></a>

## <a name="create-a-ruby-project-and-install-the-sdk"></a>Hozzon létre egy Ruby-projektet, és az SDK telepítése

1. Hozzon létre egy új ruby-projektet, és adjon hozzá egy új fájlt `Gemfile`.
2. A Text Analytics SDK hozzáadása a projekthez adja hozzá az alábbi kód használatával `Gemfile`.

    ```ruby
    source 'https://rubygems.org'
    gem 'azure_cognitiveservices_textanalytics', '~>0.17.3'
    ```

## <a name="create-a-text-analytics-client"></a>A Text analytics-ügyfél létrehozása

1. Hozzon létre egy új fájlt `TextAnalyticsExamples.rb` a kedvenc szerkesztőjében, vagy IDE. Importálja a Text Analytics SDK-t.

2. Egy hitelesítő objektumot a Text Analytics-ügyfél által használható. Hozza létre a `CognitiveServicesCredentials.new()` és az előfizetési kulcs átadásával.

3. Az ügyfél létrehozása a megfelelő Szövegelemzés-végponthoz.

    ```ruby
    require 'azure_cognitiveservices_textanalytics'
    
    include Azure::CognitiveServices::TextAnalytics::V2_1::Models
    
    credentials =
        MsRestAzure::CognitiveServicesCredentials.new("enter key here")
    # Replace 'westus' with the correct region for your Text Analytics subscription
    endpoint = String.new("https://westus.api.cognitive.microsoft.com/")
    
    textAnalyticsClient =
        Azure::TextAnalytics::Profiles::Latest::Client.new({
            credentials: credentials
        })
    textAnalyticsClient.endpoint = endpoint
    ```

<a name="SentimentAnalysis"></a>

## <a name="sentiment-analysis"></a>Hangulatelemzés

A Text Analytics SDK vagy API-t használ, végezhet hangulatelemzést egy csoportján megadott szöveges. Az alábbi példa a több dokumentum véleménypontszámának jeleníti meg.

1. Hozzon létre egy új függvényt nevű `SentimentAnalysisExample()` paraméterként a fent létrehozott text analytics-ügyfél, amely veszi.

2. Meghatároz egy olyan `MultiLanguageInput` vizsgálandó objektumokat. Adja hozzá a nyelvet és a szöveg minden objektum esetén. Az azonosító bármilyen érték lehet.

    ```ruby
    def SentimentAnalysisExample(client)
      # The documents to be analyzed. Add the language of the document. The ID can be any value.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'en'
      input_1.text = 'I had the best day of my life.'
    
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'en'
      input_2.text = 'This was a waste of my time. The speaker put me to sleep.'
    
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'es'
      input_3.text = 'No tengo dinero ni nada que dar...'
    
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'it'
      input_4.text = "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."
    ```

3. Belül ugyanezt a funkciót a dokumentumok egyesítése listáját. Adja hozzá a az `documents` mezőjét, egy `MultiLanguageBatchInput` objektum. 

4. Az ügyfél hívja `sentiment()` függvény és a `MultiLanguageBatchInput` a dokumentumok paraméterként objektum. Ha minden olyan eredményeket ad vissza, kinyomtathatja őket.
    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.sentiment(
          multi_language_batch_input: input_documents
      )
      
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        puts '===== SENTIMENT ANALYSIS ====='
        result.documents.each do |document|
          puts "Document Id: #{document.id}: Sentiment Score: #{document.score}"
        end
      end
    end
    ```

5. Hívja a `SentimentAnalysisExample()` függvény.

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

A Text Analytics szolgáltatás is észlelje a nyelvet, a szöveges dokumentum, nyelv és területi beállítás nagy számú. Az alábbi példa a nyelvet, több dokumentumot is írt jeleníti meg.

1. Hozzon létre egy új függvényt nevű `DetectLanguageExample()` átkerül a text analytics ügyfél paraméterként a fent létrehozott. 

2. Meghatároz egy olyan `LanguageInput` vizsgálandó objektumokat. Adja hozzá a nyelvet és a szöveg minden objektum esetén. Az azonosító bármilyen érték lehet.

    ```ruby
    def DetectLanguageExample(client)
       # The documents to be analyzed.
       language_input_1 = LanguageInput.new
       language_input_1.id = '1'
       language_input_1.text = 'This is a document written in English.'
    
       language_input_2 = LanguageInput.new
       language_input_2.id = '2'
       language_input_2.text = 'Este es un document escrito en Español..'
    
       language_input_3 = LanguageInput.new
       language_input_3.id = '3'
       language_input_3.text = '这是一个用中文写的文件'
    ```

3. Belül ugyanezt a funkciót a dokumentumok egyesítése listáját. Adja hozzá a az `documents` mezőjét, egy `LanguageBatchInput` objektum. 

4. Az ügyfél hívja `detect_language()` függvény és a `LanguageBatchInput` a dokumentumok paraméterként objektum. Ha minden olyan eredményeket ad vissza, kinyomtathatja őket.
    ```ruby
       input_documents = LanguageBatchInput.new
       input_documents.documents = [language_input_1, language_input_2, language_input_3]
    
    
       result = client.detect_language(
           language_batch_input: input_documents
       )
    
       if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
         puts '===== LANGUAGE DETECTION ====='
         result.documents.each do |document|
           puts "Document ID: #{document.id} , Language: #{document.detected_languages[0].name}"
         end
       else
         puts 'No results data..'
       end
     end
    ```

5. A függvény meghívása `DetectLanguageExample`

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

A Text Analytics szolgáltatás is különbséget, és bontsa ki a különböző entitások (személyek, helyek és dolgot) szereplő szöveges dokumentumok. Az alábbi példában található példa több dokumentumot az entitásokat jeleníti meg.

1. Hozzon létre egy új függvényt nevű `Recognize_Entities()` paraméterként a fent létrehozott text analytics-ügyfél, amely veszi.

2. Meghatároz egy olyan `MultiLanguageInput` vizsgálandó objektumokat. Adja hozzá a nyelvet és a szöveg minden objektum esetén. Az azonosító bármilyen érték lehet.

    ```ruby
      def RecognizeEntitiesExample(client)
        # The documents to be analyzed.
        input_1 = MultiLanguageInput.new
        input_1.id = '1'
        input_1.language = 'en'
        input_1.text = 'Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800.'
    
        input_2 = MultiLanguageInput.new
        input_2.id = '2'
        input_2.language = 'es'
        input_2.text = 'La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle.'
    ```

3. Belül ugyanezt a funkciót a dokumentumok egyesítése listáját. Adja hozzá a az `documents` mezőjét, egy `MultiLanguageBatchInput` objektum. 

4. Az ügyfél hívja `entities()` függvény és a `MultiLanguageBatchInput` a dokumentumok paraméterként objektum. Ha minden olyan eredményeket ad vissza, kinyomtathatja őket.

    ```ruby
        input_documents =  MultiLanguageBatchInput.new
        input_documents.documents = [input_1, input_2]
    
        result = client.entities(
            multi_language_batch_input: input_documents
        )
    
        if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
          puts '===== ENTITY RECOGNITION ====='
          result.documents.each do |document|
            puts "Document ID: #{document.id}"
              document.entities.each do |entity|
                puts "\tName: #{entity.name}, \tType: #{entity.type == nil ? "N/A": entity.type},\tSub-Type: #{entity.sub_type == nil ? "N/A": entity.sub_type}"
                entity.matches.each do |match|
                  puts "\tOffset: #{match.offset}, \Length: #{match.length},\tScore: #{match.entity_type_score}"
                end
                puts
              end
          end
        else
          puts 'No results data..'
        end
      end
    ```

5. A függvény meghívása `RecognizeEntitiesExample`
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

A Text Analytics szolgáltatás is bontsa ki a kulcskifejezések a mondatokban. Az alábbi példában található néhány példa dokumentumok több nyelven is az entitásokat jeleníti meg.

1. Hozzon létre egy új függvényt nevű `KeyPhraseExtractionExample()` paraméterként a fent létrehozott text analytics-ügyfél, amely veszi.

2. Meghatároz egy olyan `MultiLanguageInput` vizsgálandó objektumokat. Adja hozzá a nyelvet és a szöveg minden objektum esetén. Az azonosító bármilyen érték lehet.

    ```ruby
    def KeyPhraseExtractionExample(client)
      # The documents to be analyzed.
      input_1 = MultiLanguageInput.new
      input_1.id = '1'
      input_1.language = 'ja'
      input_1.text = '猫は幸せ'
  
      input_2 = MultiLanguageInput.new
      input_2.id = '2'
      input_2.language = 'de'
      input_2.text = 'Fahrt nach Stuttgart und dann zum Hotel zu Fu.'
  
      input_3 = MultiLanguageInput.new
      input_3.id = '3'
      input_3.language = 'en'
      input_3.text = 'My cat is stiff as a rock.'
  
      input_4 = MultiLanguageInput.new
      input_4.id = '4'
      input_4.language = 'es'
      input_4.text = 'A mi me encanta el fútbol!'
      ```

3. Belül ugyanezt a funkciót a dokumentumok egyesítése listáját. Adja hozzá a az `documents` mezőjét, egy `MultiLanguageBatchInput` objektum. 

4. Az ügyfél hívja `key_phrases()` függvény és a `MultiLanguageBatchInput` a dokumentumok paraméterként objektum. Ha minden olyan eredményeket ad vissza, kinyomtathatja őket.

    ```ruby
      input_documents =  MultiLanguageBatchInput.new
      input_documents.documents = [input_1, input_2, input_3, input_4]
    
      result = client.key_phrases(
          multi_language_batch_input: input_documents
      )
    
      if (!result.nil? && !result.documents.nil? && result.documents.length > 0)
        result.documents.each do |document|
          puts "Document Id: #{document.id}"
          puts '  Key Phrases'
          document.key_phrases.each do |key_phrase|
            puts "    #{key_phrase}"
          end
        end
      else
        puts 'No results data..'
      end
    end
    ```

5. A függvény meghívása `KeyPhraseExtractionExample`

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

 [A Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
