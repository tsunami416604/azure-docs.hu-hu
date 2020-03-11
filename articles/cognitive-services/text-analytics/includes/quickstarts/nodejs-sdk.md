---
title: 'Gyorsútmutató: A Text Analytics 3-as verziója Node.js-ügyfélkódtárának használata | Microsoft Docs'
description: Ismerkedés a Text Analytics 3-as verziója Node.js-ügyfélkódtárával
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: 4ce2da3070105f6e098373108164b6f590d423c6
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2020
ms.locfileid: "78925542"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

[3-as verzió referenciadokumentációja](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [3-as verzió kódtárforráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [3-as verzió csomagja (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [3-as verzió mintái](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[2-es verzió referenciadokumentációja](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [2-es verzió kódtárforráskódja](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [2-es verzió csomagja (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [2-es verzió mintái](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Ingyenes létrehozás](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org/) aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Hozzon létre egy Text Analytics-erőforrást,"  target="_blank">hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span></a> az Azure Portalon a kulcs és a végpont beszerzéséhez. 
    * Ahhoz, hogy az alkalmazást a Text Analytics API-hoz csatlakoztathassa, szüksége lesz egy kulcsra és egy végpontra a létrehozott erőforrásból. Ezt a rövid útmutató egy későbbi részében teheti meg.
    * A szolgáltatás kipróbálásához használhatja az ingyenes tarifacsomagot, amelyet frissíthet fizetős szintre az éles használathoz.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

> [!NOTE]
> A Text Analytics ügyfélkódtár e verzióját a [böngészőben](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md) is futtathatja.

Egy konzolablakban (pl. cmd, PowerShell vagy Bash) hozzon létre egy új mappát az alkalmazásnak, majd navigáljon oda. 

```console
mkdir myapp && cd myapp
```

Futtassa az `npm init` parancsot egy Node-alkalmazás `package.json` fájllal való létrehozásához. 

```console
npm init
```
### <a name="install-the-client-library"></a>Telepítse az ügyfélkódtárat

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Telepítse az `@azure/ai-text-analytics` NPM-csomagokat:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Telepítse az `@azure/cognitiveservices-textanalytics` NPM-csomagokat:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Az alkalmazás `package.json` fájlja frissül a függőségekkel.

Hozzon létre egy `index.js` nevű fájlt, és adja hozzá a következő kódtárakat:

---

Az alkalmazás `package.json` fájlja frissül a függőségekkel.
Hozzon létre egy `index.js` nevű fájlt, és adja hozzá a következő kódtárakat:

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Hozzon létre változókat az erőforrás Azure-végpontja és -kulcsa számára.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient` objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szövegelemzéshez, egyetlen sztringként vagy kötegelve.

A rendszer az API-nak `documents` listájaként küldi el a szöveget, amely a használt módszertől függően `id`, `text` és `language` attribútumok kombinációját tartalmazó `dictionary` objektumokból áll. A `text` attribútum az elemzendő szöveget tárolja az eredeti `language` nyelven, az `id` bármilyen érték lehet. 

A válaszobjektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Kódpéldák

* [Ügyfél-hitelesítés](#client-authentication)
* [Hangulatelemzés](#sentiment-analysis) 
* [Nyelvfelismerés](#language-detection)
* [Nevesített entitások felismerése](#named-entity-recognition-ner)
* [Entitáskapcsolás](#entity-linking)
* [Kulcskifejezések kinyerése](#key-phrase-extraction)

## <a name="client-authentication"></a>Ügyfél-hitelesítés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy új `TextAnalyticsClient` objektumot, paraméterként megadva a kulcsot és a végpontot.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre új [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) objektumot a `credentials` és `endpoint` paraméterekkel.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy sztringtömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és kérje le a visszaadott `SentimentBatchResult` objektumot. Haladjon végig az eredménylistán, és jelenítse meg az egyes dokumentumok azonosítóit, a dokumentumszintű hangulatot a kapcsolódó megbízhatósági pontszámokkal. A végeredmény minden egyes dokumentumnál a mondatszintű hangulatot fogja tartalmazni az eltolással, hosszal és megbízhatósági pontszámmal együtt.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ];
    const sentimentResult = await client.analyzeSentiment(sentimentInput);

    sentimentResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.sentimentScores.positive.toFixed(2)} \tNegative: ${document.sentimentScores.negative.toFixed(2)} \tNeutral: ${document.sentimentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentimentScores.positive.toFixed(2)} \tNegative: ${sentence.sentimentScores.negative.toFixed(2)} \tNeutral: ${sentence.sentimentScores.neutral.toFixed(2)}`);
            console.log(`\t\tLength: ${sentence.length}, Offset: ${sentence.offset}`);
        })
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Sentiment: positive
        Document Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
        Sentences Sentiment(2):
                Sentence sentiment: positive
                Sentences Scores:
                Positive: 1.00  Negative: 0.00  Neutral: 0.00
                Length: 30, Offset: 0
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
                Length: 30, Offset: 31
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy szótárobjektumokat tartalmazó listát, amely az elemezni kívánt dokumentumokat tartalmazza. Hívja meg az ügyfél [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) metódusát, és kérje le a visszaadott [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) eredményt. Haladjon végig az eredménylistán, és jelenítse meg az egyes dokumentumok azonosítóit és a dokumentumszintű hangulati pontszámot. A 0-hoz közelebbi értékek negatív, az 1-hez közelebbiek pozitív érzelmekre utalnak.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy sztringtömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az ügyfél `detectLanguage()` metódusát, és kérje le a visszaadott `DetectLanguageResultCollection` értéket. Ezután haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóit a megfelelő elsődleges nyelvvel.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ];
    const languageResult = await client.detectLanguage(languageInputArray);

    languageResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy szótárobjektumokat tartalmazó listát, amely a dokumentumokat tartalmazza. Hívja meg az ügyfél [detectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) metódusát, és kérje le a visszaadott [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) értéket. Ezután haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóit és nyelvét.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Nevesített entitások felismerése (NER)

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

> [!NOTE]
> A `3.0-preview`-s verzióban:
> * A NER külön módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitáskapcsolás és a NER két külön kérés.

Hozzon létre egy sztringtömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az ügyfél `recognizeEntities()` metódusát, és kérje le az `RecognizeEntitiesResult` objektumot. Haladjon végig az eredménylistán, és jelenítse meg az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

```javascript
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Offset: 25, Length: 10  Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Offset: 40, Length: 10  Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Offset: 95, Length: 12  Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Offset: 21, Length: 9   Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Offset: 60, Length: 7   Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Offset: 71, Length: 2   Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
```

## <a name="using-ner-to-detect-personal-information"></a>A NER használata személyes adatok észlelésére

Hozzon létre egy sztringtömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az ügyfél `recognizePiiEntities()` metódusát, és kérje le az `EntitiesBatchResult` objektumot. Haladjon végig az eredménylistán, és jelenítse meg az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ];
    const entityPiiResults = await client.recognizePiiEntities(entityPiiInput);

    entityPiiResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tOffset: ${entity.offset}, Length: ${entity.length} \tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy sztringtömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és kérje le az `RecognizeLinkedEntitiesResult` objektumot. Haladjon végig az eredmények listáján és jelenítse meg az entitás nevét, azonosítóját, adatforrását, URL-címét és egyezéseit. A `matches` tömb minden objektuma az adott egyezés eltolását, hosszát és pontszámát fogja tartalmazni.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800
                Offset: 116, Length: 11         Score: 0.777
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.555
                Text: Gates
                Offset: 161, Length: 5  Score: 0.555
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.533
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.469
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.469
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.248
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.281
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

> [!NOTE]
> A 2.1-es verzióban az entitáskapcsolás a NER-válasz részét képezi.

Hozzon létre egy objektumlistát, amely a dokumentumokat tartalmazza. Hívja meg az ügyfél [entities()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) metódusát, és kérje le az [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) objektumot. Haladjon végig az eredménylistán, és jelenítse meg az egyes dokumentumok azonosítóit. Minden észlelt entitás esetében jelenítse meg az entitás Wikipédia-nevét, típusát és altípusait (ha vannak), valamint a helyét az eredeti szövegben.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
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

---

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

#### <a name="version-30-preview"></a>[3.0-s előzetes verzió](#tab/version-3)

Hozzon létre egy sztringtömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és kérje le a visszaadott `ExtractKeyPhrasesResult` objektumot. Haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóit és az észlelt kulcskifejezéseket.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ];
    const keyPhraseResult = await client.extractKeyPhrases(keyPhrasesInput);
    
    keyPhraseResult.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Key Phrases: ${document.keyPhrases}`);
    });
}
keyPhraseExtraction(textAnalyticsClient);
```

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy objektumlistát, amely a dokumentumokat tartalmazza. Hívja meg az ügyfél [keyPhrases()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) metódusát, és kérje le a visszaadott [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) objektumot. Haladjon végig az eredményeken, és jelenítse meg az egyes dokumentumok azonosítóit és az észlelt kulcskifejezéseket.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Futtassa az alkalmazást a `node index.js` segítségével a konzolablakban.

### <a name="output"></a>Kimenet

```console
[
    { id: '1', keyPhrases: [ '幸せ' ] }
    { id: '2', keyPhrases: [ 'Stuttgart', "hotel", "Fahrt", "Fu" ] }
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] }
    { id: '3', keyPhrases: [ 'fútbol' ] }
]
```

---

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a `node` paranccsal a gyorsútmutatós fájlon.

```console
node index.js
```
