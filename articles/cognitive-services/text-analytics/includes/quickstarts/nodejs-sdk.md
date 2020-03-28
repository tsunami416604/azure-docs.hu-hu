---
title: 'Rövid útmutató: Text Analytics v3 ügyfélkönyvtár a Node.js fájlhoz | Microsoft dokumentumok'
description: Ismerkedés a node.js v3 Text Analytics ügyféltárával.
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.reviewer: sumeh, assafi
ms.openlocfilehash: a0616a8a2cc401b6b4c42c9882c14da2f123c6df
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "79481874"
---
<a name="HOLTop"></a>

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

[v3 Referenciadokumentáció](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [v3 Könyvtár forráskód](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [v3 csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [v3 minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)


#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

[v2 Referenciadokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [v2 Könyvtár forráskód](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [v2 csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [v2 minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

---

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node.js](https://nodejs.org/)jelenlegi verziója .
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez. Üzembe helyezése után kattintson **az Ugrás az erőforrásra**gombra.
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. A kulcs és a végpont beillesztése az alábbi kódba később a rövid útmutatóban.
    * Használhatja az ingyenes tarifacsomag ( )`F0`kipróbálni a szolgáltatást, és frissítse később egy fizetett szint éles környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg azt. 

```console
mkdir myapp 

cd myapp
```

Futtassa a `npm init` parancsot egy `package.json` csomópontalkalmazás fájllal való létrehozásához. 

```console
npm init
```
### <a name="install-the-client-library"></a>Az ügyféltár telepítése

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Telepítse `@azure/ai-text-analytics` az NPM-csomagokat:

```console
npm install --save @azure/ai-text-analytics
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/TextAnalytics/text-analytics-v3-client-library.js)található. 

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Telepítse `@azure/cognitiveservices-textanalytics` az NPM-csomagokat:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódfájlt? Ebben a rövid útmutatóban a kódpéldákat tartalmazó [gitHubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)található. 

---

Az alkalmazás `package.json` fájlja frissül a függőségekkel.
Hozzon létre `index.js` egy nevű fájlt, és adja hozzá a következőket:

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

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

Hozzon létre változókat az erőforrás Azure-végpontjának és kulcsának.

[!INCLUDE [text-analytics-find-resource-information](../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektummodell

A Text Analytics-ügyfél egy `TextAnalyticsClient` olyan objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szöveg elemzésére, egyetlen karakterláncként vagy kötegként.

A program a szöveget a `documents`használt `dictionary` módszertől függően `id`a `text`, `language` a kombinációját és attribútumokat tartalmazó objektumok listájaként küldi el az API-nak. Az `text` attribútum az eredeti `language`helyen tárolja az elemzendő szöveget, és az `id` bármilyen érték lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzési adatait tartalmazza. 

## <a name="code-examples"></a>Kódpéldák

* [Ügyfél hitelesítése](#client-authentication)
* [Hangulatelemzés](#sentiment-analysis) 
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitás felismerése](#named-entity-recognition-ner)
* [Entitáscsatolás](#entity-linking)
* [Kulcskifejezés kinyerése](#key-phrase-extraction)

## <a name="client-authentication"></a>Ügyfél-hitelesítés

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon `TextAnalyticsClient` létre egy új objektumot a kulccsal és a végpontparaméterekkel.

```javascript
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new TextAnalyticsApiKeyCredential(key));
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy új `credentials` `endpoint` [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) objektumot paraméterrel és paraméterként.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]

---

## <a name="sentiment-analysis"></a>Hangulatelemzés

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre egy karakterlánctömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az `analyzeSentiment()` ügyfél metódusát, és kapja meg a visszaadott `SentimentBatchResult` objektumot. Végighaladhat az eredmények listáján, és kinyomtathatja az egyes dokumentumok azonosítóját, a dokumentumszintű véleményt megbízhatósági pontszámokkal. Az eredmény minden dokumentumhoz mondatszintű véleményt, valamint eltolásokat, hosszt és megbízhatósági pontszámokat tartalmaz.

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
        console.log(`\t\tPositive: ${document.confidenceScores.positive.toFixed(2)} \tNegative: ${document.confidenceScores.negative.toFixed(2)} \tNeutral: ${document.confidenceScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.confidenceScores.positive.toFixed(2)} \tNegative: ${sentence.confidenceScores.negative.toFixed(2)} \tNeutral: ${sentence.confidenceScores.neutral.toFixed(2)}`);
        });
    });
}
sentimentAnalysis(textAnalyticsClient)
```

Futtassa `node index.js` a kódot a konzol ablakában.

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
                Sentence sentiment: neutral
                Sentences Scores:
                Positive: 0.21  Negative: 0.02  Neutral: 0.77
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

Hozzon létre egy szótárobjektumokat tartalmazó listát, amely tartalmazza az elemezni kívánt dokumentumokat. Hívja meg az ügyfél [sentiment()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#sentiment-models-textanalyticsclientsentimentoptionalparams-) metódusát, és kapja meg a visszaadott [SentimentBatchResult értéket.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/sentimentbatchresult) Végighaladhat az eredmények listáján, és kinyomtathatja az egyes dokumentumok azonosítóját és hangulatpontszámát. A 0-hoz közelebbi pontszám negatív érzést, míg az 1-hez közelebbi pontszám pozitív véleményt jelez.

[!code-javascript[Sentiment analysis](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=sentimentAnalysis)]

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
[ { id: '1', score: 0.87 } ]
[ { id: '2', score: 0.11 } ]
[ { id: '3', score: 0.44 } ]
[ { id: '4', score: 1.00 } ]
```

---

## <a name="language-detection"></a>Nyelvfelismerés

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre egy karakterlánctömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja fel az `detectLanguage()` ügyfél metódusát, és kapja meg a visszaküldött `DetectLanguageResultCollection`. Ezután végighaladhat az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját a megfelelő elsődleges nyelvvel.

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

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Primary Language French
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

A dokumentumokat tartalmazó szótárobjektumok listájának létrehozása. Hívja meg az ügyfél [detectLanguage()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) metódusát, és kapja meg a visszaadott [LanguageBatchResult metódust.](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult) Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját és nyelvét.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

---

## <a name="named-entity-recognition-ner"></a>Elnevezett entitásfelismerés (NER)

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

> [!NOTE]
> Verzióban `3.0-preview`:
> * A NER külön módszereket tartalmaz a személyes adatok felderítésére. 
> * Az entitásösszekapcsolás külön kérés, mint a NER.

Hozzon létre egy karakterlánctömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja fel az `recognizeEntities()` ügyfél metódusát, és kapja meg az `RecognizeEntitiesResult` objektumot. Végighaladva az eredmények listáján, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.

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
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 1
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.67
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.81
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: interpreters      Category: PersonType    Subcategory: N/A
        Score: 0.6
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.96
        Name: Redmond   Category: Location      Subcategory: GPE
        Score: 0.09
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.31
```

## <a name="using-ner-to-detect-personal-information"></a>A NER használata a személyes adatok felderítésére

Hozzon létre egy karakterlánctömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja fel az `recognizePiiEntities()` ügyfél metódusát, és kapja meg az `EntitiesBatchResult` objektumot. Végighaladva az eredmények listáján, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.


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
            console.log(`\tScore: ${entity.score}`);
        });
    });
}
entityPiiRecognition(textAnalyticsClient);
```

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: 123-12-1234       Category: U.S. Social Security Number (SSN)     Subcategory: N/A
        Score: 0.85
```

## <a name="entity-linking"></a>Entitáskapcsolás

Hozzon létre egy karakterlánctömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja fel az `recognizeLinkedEntities()` ügyfél metódusát, és kapja meg az `RecognizeLinkedEntitiesResult` objektumot. Végighaladhat az eredmények listáján, és nyomtassa ki az entitás nevét, azonosítóját, adatforrását, URL-címét és egyezéseit. A tömb `matches` minden objektuma tartalmazni fogja az adott egyezés eltolását, hosszát és pontszámát.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ];
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.dataSourceEntityId} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text} \tScore: ${match.score.toFixed(2)}`);
            });
        });
    });
}
linkedEntityRecognition(textAnalyticsClient);
```

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 0
        Name: Altair 8800       ID: Altair 8800         URL: https://en.wikipedia.org/wiki/Altair_8800  Data Source: Wikipedia
        Matches:
                Text: Altair 8800       Score: 0.78
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates        Score: 0.55
                Text: Gates     Score: 0.55
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen        Score: 0.53
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft         Score: 0.47
                Text: Microsoft         Score: 0.47
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4   Score: 0.25
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC     Score: 0.28
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

> [!NOTE]
> A 2.1-es verzióban az entitásösszekapcsolás szerepel a NER-válaszban.

A dokumentumokat tartalmazó objektumok listájának létrehozása. Hívja meg az ügyfél [entitás()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#entities-models-textanalyticscliententitiesoptionalparams-) metódusát, és vegye le az [EntitiesBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/entitiesbatchresult) objektumot. Végighaladhat az eredmények listáján, és kinyomtathatja az egyes dokumentumok azonosítóját. Minden észlelt entitáshoz nyomtassa ki a wikipédia nevét, a típusát és altípusait (ha van ilyen), valamint az eredeti szövegben lévő helyeket.

[!code-javascript[Entity recognition](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=entityRecognition)]

Futtassa `node index.js` a kódot a konzol ablakában.

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

#### <a name="version-30-preview"></a>[3.0-s verziójú előzetes](#tab/version-3)

Hozzon létre egy karakterlánctömböt, amely az elemezni kívánt dokumentumot tartalmazza. Hívja meg az `extractKeyPhrases()` ügyfél metódusát, és kapja meg a visszaadott `ExtractKeyPhrasesResult` objektumot. Végighaladhat az eredményekközött, és kinyomtathatja az egyes dokumentumok azonosítóját és az észlelt kulcskifejezéseket.

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

Futtassa `node index.js` a kódot a konzol ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Document Key Phrases: cat,veterinarian
```

#### <a name="version-21"></a>[2.1-es verzió](#tab/version-2)

A dokumentumokat tartalmazó objektumok listájának létrehozása. Hívja meg az ügyfél [keyPhrases() metódusát,](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#keyphrases-models-textanalyticsclientkeyphrasesoptionalparams-) és vegye le a visszaadott [KeyPhraseBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/keyphrasebatchresult) objektumot. Végighaladhat az eredményekközött, és kinyomtathatja az egyes dokumentumok azonosítóját és az észlelt kulcskifejezéseket.

[!code-javascript[Key phrase extraction](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=keyPhraseExtraction)]

Futtassa `node index.js` a kódot a konzol ablakában.

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

Futtassa az `node` alkalmazást a parancssegítségével a rövid útmutató fájlban.

```console
node index.js
```
