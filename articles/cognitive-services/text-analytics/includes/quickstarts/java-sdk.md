---
title: 'Gyorsútmutató: A Text Analytics 3-as verziójának Java-ügyfélkódtára | Microsoft Docs'
description: Ismerkedés a Text Analytics 3-as verziójának Java-ügyfélkódtárával.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 11092b74c0256d298dece0f909d8d7dd241e7b13
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371323"
---
<a name="HOLTop"></a>

[Referenciadokumentáció](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Csomag](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) | [Minták](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Ingyenes létrehozás](https://azure.microsoft.com/free/)
* A [Java fejlesztői készlet](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) 8-as vagy újabb verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Hozzon létre egy Text Analytics-erőforrást,"  target="_blank">hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span></a> az Azure Portalon a kulcs és a végpont beszerzéséhez. 
    * Ahhoz, hogy az alkalmazást a Text Analytics API-hoz csatlakoztathassa, szüksége lesz egy kulcsra és egy végpontra a létrehozott erőforrásból. Ezt a rövid útmutató egy későbbi részében teheti meg.
    * A szolgáltatás kipróbálásához használhatja az ingyenes tarifacsomagot, amelyet frissíthet fizetős szintre az éles használathoz.

## <a name="setting-up"></a>Beállítás

### <a name="add-the-client-library"></a>Az ügyfélkódtár hozzáadása

Hozzon létre egy Maven-projektet egy szabadon választott IDE- vagy fejlesztői környezetben. Ezután adja hozzá a következő függőséget a projekt *pom.xml* fájljához. Az [egyéb buildelőeszközök](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.3) implementációs szintaxisát megtalálja az interneten.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.3</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Szeretné egyben megtekinteni a teljes gyorsútmutatós kódfájlt? Megtalálhatja a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java), amely a gyorsútmutató kódmintáit is tartalmazza. 

Hozzon létre egy `TextAnalyticsSamples.java` nevű Java-fájlt. Nyissa meg a fájlt, és adja hozzá a következő `import` utasításokat:

```java
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

A Java-fájlban adjon meg egy új osztályt, és adja meg az Azure-erőforrás kulcsát és végpontját az alább látható módon.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adja hozzá a következő fő metódust az osztályhoz. Az itt meghívott metódusokat később fogja definiálni.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizePIIEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient`-objektum, amely a kulcs használatával hitelesíti magát az Azure-ban, és egyedi sztringként vagy kötegelt formában megadott szövegeket fogadó függvényeket biztosít. Az API felé szinkron vagy aszinkron módon is küldhet szöveget. A válaszobjektum az egyes dokumentumok elemzési információit fogja tartalmazni. 

## <a name="code-examples"></a>Kódpéldák

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis) 
* [Nyelvfelismerés](#language-detection)
* [Nevesített entitások felismerése](#named-entity-recognition-ner) 
* [Entitáskapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy metódust a `TextAnalyticsClient` objektum példányosításához a Text Analytics-erőforrás kulcsával és végpontjával.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new TextAnalyticsApiKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

A program `main()` metódusában hívja meg a hitelesítési metódust az ügyfél példányosításához.

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre egy új függvényt `sentimentAnalysisExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `analyzeSentiment()` függvényét. A visszaadott `AnalyzeSentimentResult` objektum siker esetén a `documentSentiment` és `sentenceSentiments` elemet tartalmazza, sikertelenség esetén pedig egy `errorMessage` üzenetet. 

```java
static void sentimentAnalysisExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had the best day of my life. I wish you were there with me.";

    DocumentSentiment documentSentiment = client.analyzeSentiment(text);
    System.out.printf(
        "Recognized document sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
        documentSentiment.getSentiment(),
        documentSentiment.getConfidenceScores().getPositive(),
        documentSentiment.getConfidenceScores().getNeutral(),
        documentSentiment.getConfidenceScores().getNegative());

    for (SentenceSentiment sentenceSentiment : documentSentiment.getSentences()) {
        System.out.printf(
            "Recognized sentence sentiment: %s, positive score: %s, neutral score: %s, negative score: %s.%n",
            sentenceSentiment.getSentiment(),
            sentenceSentiment.getConfidenceScores().getPositive(),
            sentenceSentiment.getConfidenceScores().getNeutral(),
            sentenceSentiment.getConfidenceScores().getNegative());
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized document sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: positive, positive score: 1.0, neutral score: 0.0, negative score: 0.0.
Recognized sentence sentiment: neutral, positive score: 0.21, neutral score: 0.77, negative score: 0.02.
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy új függvényt `detectLanguageExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `detectLanguage()` függvényét. A visszaadott `DetectLanguageResult` objektum tartalmazni fog egy észlelt elsődleges nyelvet, siker esetén az észlelt egyéb nyelvek listáját, sikertelenség esetén pedig egy `errorMessage` üzenetet.

> [!Tip]
> Bizonyos esetekben nehéz lehet a nyelveket megkülönböztetni a bemenet alapján. A `countryHint` paraméterrel megadhat egy 2 karakteres országkódot. Alapértelmezés szerint az API a „US” országkódot használja alapértelmezett countryHint paraméterként. Ha ez a működés nem megfelelő, állítsa az értéket üres karakterláncra (`countryHint = ""`). Más alapértelmezés beállításához állítsa be a `TextAnalyticsClientOptions.DefaultCountryHint` tulajdonságot, és adja át az ügyfél inicializálásakor.

```java
static void detectLanguageExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Ce document est rédigé en Français.";

    DetectedLanguage detectedLanguage = client.detectLanguage(text);
    System.out.printf("Detected primary language: %s, ISO 6391 name: %s, score: %.2f.%n",
        detectedLanguage.getName(),
        detectedLanguage.getIso6391Name(),
        detectedLanguage.getScore());
}
```

### <a name="output"></a>Kimenet

```console
Detected primary language: French, ISO 6391 name: fr, score: 1.00.
```
## <a name="named-entity-recognition-ner"></a>Nevesített entitások felismerése (NER)

> [!NOTE]
> A `3.0-preview`-s verzióban:
> * A NER külön módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitáskapcsolás és a NER két külön kérés.

Hozzon létre egy új függvényt `recognizeEntitiesExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `recognizeEntities()` függvényét. A visszaadott `RecognizeEntitiesResult` objektum siker esetén egy `NamedEntity` listát tartalmaz, sikertelenség esetén pedig egy `errorMessage` üzenetet.

```java
static void recognizeEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "I had a wonderful trip to Seattle last week.";

    for (CategorizedEntity entity : client.recognizeEntities(text)) {
        System.out.printf(
            "Recognized entity: %s, entity category: %s, entity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized entity: Seattle, entity category: Location, entity sub-category: GPE, score: 0.92.
Recognized entity: last week, entity category: DateTime, entity sub-category: DateRange, score: 0.8.
```

## <a name="using-ner-to-recognize-personal-information"></a>A NER használata személyes adatok felismeréséhez

Hozzon létre egy új függvényt `recognizePIIEntitiesExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `recognizePiiEntities()` függvényét. A visszaadott `RecognizePiiEntitiesResult` objektum siker esetén egy `NamedEntity` listát tartalmaz, sikertelenség esetén pedig egy `errorMessage` üzenetet. 

```java
static void recognizePIIEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Insurance policy for SSN on file 123-12-1234 is here by approved.";

    for (PiiEntity entity : client.recognizePiiEntities(text)) {
        System.out.printf(
            "Recognized personal identifiable information entity: %s, entity category: %s, %nentity sub-category: %s, score: %s.%n",
            entity.getText(),
            entity.getCategory(),
            entity.getSubCategory(),
            entity.getConfidenceScore());
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized personal identifiable information entity: 123-12-1234, entity category: U.S. Social Security Number (SSN), 
entity sub-category: null, score: 0.85.
```

## <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy új függvényt `recognizeLinkedEntitiesExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `recognizeLinkedEntities()` függvényét. A visszaadott `RecognizeLinkedEntitiesResult` objektum siker esetén egy `LinkedEntity` listát tartalmaz, sikertelenség esetén pedig egy `errorMessage` üzenetet. Mivel a társított entitások egyedileg vannak azonosítva, ugyanannak az entitásnak az előfordulásai egy `LinkedEntity` objektum alatt vannak csoportosítva, `LinkedEntityMatch` objektumok listájaként.

```java
static void recognizeLinkedEntitiesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, " +
            "to develop and sell BASIC interpreters for the Altair 8800. " +
            "During his career at Microsoft, Gates held the positions of chairman, " +
            "chief executive officer, president and chief software architect, " +
            "while also being the largest individual shareholder until May 2014.";

    System.out.printf("Linked Entities:%n");
    for (LinkedEntity linkedEntity : client.recognizeLinkedEntities(text)) {
        System.out.printf("Name: %s, ID: %s, URL: %s, Data Source: %s.%n",
                linkedEntity.getName(),
                linkedEntity.getDataSourceEntityId(),
                linkedEntity.getUrl(),
                linkedEntity.getDataSource());
        System.out.printf("Matches:%n");
        for (LinkedEntityMatch linkedEntityMatch : linkedEntity.getLinkedEntityMatches()) {
            System.out.printf("Text: %s, Score: %.2f%n",
                    linkedEntityMatch.getText(),
                    linkedEntityMatch.getConfidenceScore());
        }
    }
}
```

### <a name="output"></a>Kimenet

```console
Linked Entities:
Name: Altair 8800, ID: Altair 8800, URL: https://en.wikipedia.org/wiki/Altair_8800, Data Source: Wikipedia.
Matches:
Text: Altair 8800, Score: 0.78
Name: Bill Gates, ID: Bill Gates, URL: https://en.wikipedia.org/wiki/Bill_Gates, Data Source: Wikipedia.
Matches:
Text: Bill Gates, Score: 0.55
Text: Gates, Score: 0.55
Name: Paul Allen, ID: Paul Allen, URL: https://en.wikipedia.org/wiki/Paul_Allen, Data Source: Wikipedia.
Matches:
Text: Paul Allen, Score: 0.53
Name: Microsoft, ID: Microsoft, URL: https://en.wikipedia.org/wiki/Microsoft, Data Source: Wikipedia.
Matches:
Text: Microsoft, Score: 0.47
Text: Microsoft, Score: 0.47
Name: April 4, ID: April 4, URL: https://en.wikipedia.org/wiki/April_4, Data Source: Wikipedia.
Matches:
Text: April 4, Score: 0.25
Name: BASIC, ID: BASIC, URL: https://en.wikipedia.org/wiki/BASIC, Data Source: Wikipedia.
Matches:
Text: BASIC, Score: 0.28
```
## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Hozzon létre egy új függvényt `extractKeyPhrasesExample()` néven, amely az előbb létrehozott ügyfelet használja, majd hívja meg annak `extractKeyPhrases()` függvényét. A visszaadott `ExtractKeyPhraseResult` objektum siker esetén a kulcskifejezések listáját tartalmazza, sikertelenség esetén pedig egy `errorMessage` üzenetet.

```java
static void extractKeyPhrasesExample(TextAnalyticsClient client)
{
    // The text that need be analyzed.
    String text = "My cat might need to see a veterinarian.";

    System.out.printf("Recognized phrases: %n");
    for (String keyPhrase : client.extractKeyPhrases(text)) {
        System.out.printf("%s%n", keyPhrase);
    }
}
```

### <a name="output"></a>Kimenet

```console
Recognized phrases: 
cat
veterinarian
```
