---
title: 'Gyors útmutató: Text Analytics v3 ügyféloldali kódtár a Node. js-hez | Microsoft Docs'
description: Ismerkedés a Node. js-hez készült v3 Text Analytics ügyféloldali kódtáraval.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 01/13/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: dd8f8e415f2e83b6f08aa00953e42daecead7652
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77372236"
---
<a name="HOLTop"></a>

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

[v3 hivatkozási dokumentáció](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 minta](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

[v2 hivatkozási dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 minta](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org/)jelenlegi verziója.

[!INCLUDE [text-analytics-resource-creation](resource-creation.md)]

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

> [!NOTE]
> A [böngészőben](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md)a Text Analytics ügyféloldali kódtár ezen verzióját is futtathatja.

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

A `npm init` parancs futtatásával hozzon létre egy csomópont-alkalmazást egy `package.json`-fájllal. 

```console
npm init
```
### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Telepítse a `@azure/ai-text-analytics` NPM csomagokat:

```console
npm install --save @azure/ai-text-analytics
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Telepítse a `@azure/cognitiveservices-textanalytics` NPM csomagokat:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

Hozzon létre egy `index.js` nevű fájlt, és adja hozzá a következő könyvtárakat:

---

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.
Hozzon létre egy `index.js` nevű fájlt, és adja hozzá a következő könyvtárakat:

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

```javascript
"use strict";

const { TextAnalyticsClient, TextAnalyticsApiKeyCredential } = require("@azure/ai-text-analytics");
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```
---

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient`-objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként.

A rendszer elküldi a szöveget az API-nak `documents`-listaként, amely a használt módszertől függően `id`, `text`és `language` attribútumok kombinációját tartalmazó objektumokat `dictionary`. A `text` attribútum tárolja a forrás `language`elemezni kívánt szöveget, és a `id` értéke lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Példák a kódokra

* [Ügyfél-hitelesítés](#client-authentication)
* [Hangulatelemzés](#sentiment-analysis) 
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-ner)
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="client-authentication"></a>Ügyfél-hitelesítés

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Hozzon létre egy új `TextAnalyticsClient` objektumot a kulcs és a végpont paraméterként.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy új [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) objektumot `credentials` és `endpoint` paraméterként.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és szerezze be a visszaadott `SentimentBatchResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, a dokumentum szintjének véleményét megbízhatósági pontszámokkal. Az eredmény az egyes dokumentumokhoz tartozó mondatok szintjének, valamint az eltolások, a hossz és a megbízhatósági pontszámok értékeit tartalmazza.

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

Futtassa a kódot `node index.js` a konzol ablakában.

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

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy listát az elemezni kívánt dokumentumokat tartalmazó szótár-objektumok listájáról. Hívja meg az ügyfél [hangulati ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) metódusát, és szerezze be a visszaadott [SentimentBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult). Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és hangulati pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `detectLanguage()` metódusát, és szerezze be a visszaadott `DetectLanguageResultCollection`. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit a megfelelő elsődleges nyelvvel.

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

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozzon létre egy listát a dokumentumokat tartalmazó szótár-objektumokról. Hívja meg az ügyfél [detectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) metódusát, és szerezze be a visszaadott [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és nyelvét.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitások felismerése

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

> [!NOTE]
> A (z) `3.0-preview`verzióban:
> * A tájékoztató külön módszereket tartalmaz a személyes adatok észlelésére. 
> * Az entitások összekapcsolása egy külön kérelem, mint a kapcsolatfelvétel.

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeEntities()` metódusát, és szerezze be a `RecognizeEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

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

Futtassa a kódot `node index.js` a konzol ablakában.

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

## <a name="using-ner-to-detect-personal-information"></a>A személyes adatok észlelése a következővel

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizePiiEntities()` metódusát, és szerezze be a `EntitiesBatchResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.


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

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entitáskapcsolás

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és szerezze be a `RecognizeLinkedEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, AZONOSÍTÓját, forrását, URL-címét és egyezéseit. `matches` tömb minden objektuma az adott egyezés eltolását, hosszát és pontszámát fogja tartalmazni.

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

Futtassa a kódot `node index.js` a konzol ablakában.

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

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

> [!NOTE]
> Az 2,1-es verzióban az entitások összekapcsolása szerepel a megjelenő válaszban.

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Hívja meg az ügyfél [entitások ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) metódusát, és kérje le a [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit. Minden észlelt entitás esetében nyomtassa ki a wikipedia nevét, típusát és altípusait (ha van), valamint az eredeti szöveg helyét.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Futtassa a kódot `node index.js` a konzol ablakában.

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

#### <a name="version-30-previewtabversion-3"></a>[3,0-es verzió – előzetes verzió](#tab/version-3)

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és szerezze be a visszaadott `ExtractKeyPhrasesResult` objektumot. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

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

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21tabversion-2"></a>[2,1-es verzió](#tab/version-2)

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Hívja meg az ügyfél [()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) metódusát, és kérje le a visszaadott [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) objektumot. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Futtassa a kódot `node index.js` a konzol ablakában.

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

Futtassa az alkalmazást a gyors üzembe helyezési fájlban lévő `node` paranccsal.

```console
node index.js
```
