---
title: 'Rövid útmutató: Text Analytics v3 ügyfélkönyvtár Java-hoz | Microsoft dokumentumok'
description: Ismerkedés a v3 Text Analytics java-alapú ügyféltárral.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/17/2020
ms.author: aahi
ms.reviewer: tasharm, assafi, sumeh
ms.openlocfilehash: 31afb7bc00250887841adccc8c3cc4dc69462d55
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642885"
---
<a name="HOLTop"></a>

[Referenciadokumentáció](https://aka.ms/azsdk-java-textanalytics-ref-docs) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/textanalytics/azure-ai-textanalytics) | [Csomagminták](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) | [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* [Java Development Kit](https://www.oracle.com/technetwork/java/javase/downloads/index.html) (JDK) 8-as vagy újabb verzióval
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez.  Üzembe helyezése után kattintson **az Ugrás az erőforrásra**gombra.
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. A kulcs és a végpont beillesztése az alábbi kódba később a rövid útmutatóban.
    * Használhatja az ingyenes tarifacsomag ( )`F0`kipróbálni a szolgáltatást, és frissítse később egy fizetett szint éles környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="add-the-client-library"></a>Az ügyféltár hozzáadása

Hozzon létre egy Maven projektet az előnyben részesített IDE vagy fejlesztői környezetben. Ezután adja hozzá a következő függőséget a projekt *pom.xml* fájljához. Más [buildeszközök](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/1.0.0-beta.4) implementációs szintaxisát online is megtalálhatja.

```xml
<dependencies>
     <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-ai-textanalytics</artifactId>
        <version>1.0.0-beta.4</version>
    </dependency>
</dependencies>
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/TextAnalytics/TextAnalyticsSamples.java)található. 

Hozzon létre `TextAnalyticsSamples.java`egy Java nevű fájlt. Nyissa meg a fájlt, és adja hozzá a következő `import` állításokat:

```java
import com.azure.core.credential.AzureKeyCredential;
import com.azure.ai.textanalytics.models.*;
import com.azure.ai.textanalytics.TextAnalyticsClientBuilder;
import com.azure.ai.textanalytics.TextAnalyticsClient;
```

A java fájlban adjon hozzá egy új osztályt, és adja hozzá az azure-erőforrás kulcsát és végpontját az alábbiak szerint.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```java
public class TextAnalyticsSamples {
    private static String KEY = "<replace-with-your-text-analytics-key-here>";
    private static String ENDPOINT = "<replace-with-your-text-analytics-endpoint-here>";
}
```

Adja hozzá a következő fő metódust az osztályhoz. Később meg fogja határozni az itt megnevezett módszereket.

```java
public static void main(String[] args) {
    //You will create these methods later in the quickstart.
    TextAnalyticsClient client = authenticateClient(KEY, ENDPOINT);

    sentimentAnalysisExample(client);
    detectLanguageExample(client);
    recognizeEntitiesExample(client);
    recognizeLinkedEntitiesExample(client);
    extractKeyPhrasesExample(client);
}
```

## <a name="object-model"></a>Objektummodell

A Text Analytics-ügyfél egy `TextAnalyticsClient` olyan objektum, amely hitelesíti magát az Azure-ban a kulcs használatával, és funkciókat biztosít a szöveg egyetlen karakterláncként vagy kötegként történő fogadásához. Az API-ba szinkron módon vagy aszinkron módon is küldhet szöveget. A válaszobjektum minden elküldött dokumentum elemzési adatait tartalmazza. 

## <a name="code-examples"></a>Kódpéldák

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis) 
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitás felismerése](#named-entity-recognition-ner) 
* [Entitáscsatolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy módszert `TextAnalyticsClient` az objektum példányosításához a Szövegelemzési erőforrás kulcsával és végpontjával.

```java
static TextAnalyticsClient authenticateClient(String key, String endpoint) {
    return new TextAnalyticsClientBuilder()
        .apiKey(new AzureKeyCredential(key))
        .endpoint(endpoint)
        .buildClient();
}
```

A program metódusában `main()` hívja meg a hitelesítési módszert az ügyfél példányosítására.

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre `sentimentAnalysisExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `analyzeSentiment()` A `AnalyzeSentimentResult` visszaadott `documentSentiment` objektum `sentenceSentiments` tartalmazni fog, `errorMessage` és ha sikeres, vagy ha nem. 

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

Hozzon létre `detectLanguageExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `detectLanguage()` A `DetectLanguageResult` visszaadott objektum tartalmazni fog egy elsődleges nyelvet, egy listát `errorMessage` az észlelt többi nyelvről, ha sikeres, vagy ha nem.

> [!Tip]
> Bizonyos esetekben nehéz lehet a bevitel alapján félretenni a nyelveket. A `countryHint` paraméter segítségével kétbetűs országkódot adhat meg. Alapértelmezés szerint az API az "US" az alapértelmezett countryHint, hogy távolítsa el ezt a `countryHint = ""`viselkedést vissza állíthatja ezt a paramétert beállításával ezt az értéket üres karakterlánc . Másik alapértelmezett beállításhoz állítsa `TextAnalyticsClientOptions.DefaultCountryHint` be a tulajdonságot, és adja át az ügyfél inicializálása során.

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
## <a name="named-entity-recognition-ner"></a>Elnevezett entitás felismerése (NER)

> [!NOTE]
> Verzióban `3.0-preview`:
> * A NER külön módszereket tartalmaz a személyes adatok felderítésére. 
> * Az entitásösszekapcsolás külön kérés, mint a NER.

Hozzon létre `recognizeEntitiesExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `recognizeEntities()` A `RecognizeEntitiesResult` visszaadott objektum tartalmaz `NamedEntity` egy listát, `errorMessage` ha sikeres, vagy ha nem.

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

## <a name="entity-linking"></a>Entitáscsatolás

Hozzon létre `recognizeLinkedEntitiesExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `recognizeLinkedEntities()` A `RecognizeLinkedEntitiesResult` visszaadott objektum tartalmaz `LinkedEntity` egy listát, `errorMessage` ha sikeres, vagy ha nem. Mivel a csatolt entitások egyedi azonosítása történik, ugyanazon entitás `LinkedEntity` előfordulásai `LinkedEntityMatch` objektum alatt objektumok listájaként vannak csoportosítva.

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

Hozzon létre `extractKeyPhrasesExample()` egy új függvényt, amely a korábban létrehozott ügyfelet veszi, és meghívja annak funkcióját. `extractKeyPhrases()` A `ExtractKeyPhraseResult` visszaadott objektum tartalmazza a kulcskifejezések listáját, `errorMessage` ha sikeresek, vagy ha nem.

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
