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
ms.openlocfilehash: 69899f521e73cb5af1af145a0915dbe1a017f307
ms.sourcegitcommit: d9ec6e731e7508d02850c9e05d98d26c4b6f13e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2020
ms.locfileid: "76281160"
---
<a name="HOLTop"></a>

[Dokumentáció](https://aka.ms/azsdk-js-textanalytics-ref-docs) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [csomag (NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics) | [minták](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics/samples)

> [!NOTE]
> * Ez a rövid útmutató a Text Analytics ügyféloldali kódtár `3.0-preview` verzióját használja, amely egy nyilvános előzetes verziót tartalmaz a továbbfejlesztett [Hangulatelemzés](../../../how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features) és az [elnevezett entitások felismeréséhez](../../../how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features).
> * A cikkben szereplő kód a nem biztonságos hitelesítő adatok tárolását használja az egyszerűség kedvéért. A termelési forgatókönyvek esetében javasoljuk, hogy a kötegekben a teljesítmény és a méretezhetőség érdekében karakterláncokat küldjön. Például a `SentimentBatchAsync()` hívása `Sentiment()`helyett.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Node. js](https://nodejs.org/)jelenlegi verziója.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása

[!INCLUDE [text-analytics-resource-creation](../resource-creation.md)]

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

A `npm init` parancs futtatásával hozzon létre egy csomópont-alkalmazást egy `package.json`-fájllal. 

```console
npm init
```
### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `@azure/cognitiveservices-textanalytics` NPM csomagokat:

```console
npm install --save @azure/cognitiveservices-textanalytics
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

Hozzon létre egy `index.js` nevű fájlt, és adja hozzá a következő könyvtárakat:

```javascript
"use strict";

const { TextAnalyticsClient, CognitiveServicesCredential } = require("@azure/cognitiveservices-textanalytics");
```

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info.md)]

```javascript
const key = '<paste-your-text-analytics-key-here>';
const endpoint = `<paste-your-text-analytics-endpoint-here>`;
```

## <a name="object-model"></a>Objektummodell

A Text Analytics ügyfél egy `TextAnalyticsClient`-objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként.

A rendszer elküldi a szöveget az API-nak `documents`-listaként, amely a használt módszertől függően `id`, `text`és `language` attribútumok kombinációját tartalmazó objektumokat `dictionary`. A `text` attribútum tárolja a forrás `language`elemezni kívánt szöveget, és a `id` értéke lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Kódpéldák

* [Ügyfél-hitelesítés](#client-authentication)
* [Hangulatelemzés](#sentiment-analysis) (nyilvános előzetes verzió)
* [Nyelvfelismerés](#language-detection)
* [Elnevezett entitások felismerése](#named-entity-recognition-public-preview) (nyilvános előzetes verzió)
* [Nevesített entitások felismerése – személyes adatok](#named-entity-recognition---personal-information-public-preview) (nyilvános előzetes verzió)
* [Entitás összekapcsolása](#entity-linking)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="client-authentication"></a>Ügyfél-hitelesítés

Hozzon létre egy új `TextAnalyticsClient` objektumot a kulcs és a végpont paraméterként.

```javascript
const client = new TextAnalyticsClient(endpoint,  new CognitiveServicesCredential(key));
```

## <a name="sentiment-analysis-public-preview"></a>Hangulat elemzése (nyilvános előzetes verzió)

> [!NOTE]
> Az alábbi kód az érzelmek elemzése v3, amely nyilvános előzetes verzióban érhető el.

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `analyzeSentiment()` metódusát, és szerezze be a visszaadott `SentimentBatchResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, a dokumentum szintjének véleményét megbízhatósági pontszámokkal. Az eredmény az egyes dokumentumokhoz tartozó mondatok szintjének, valamint az eltolások, a hossz és a megbízhatósági pontszámok értékeit tartalmazza.

```javascript
async function sentimentAnalysis(client){

    const sentimentInput = [
        "I had the best day of my life. I wish you were there with me."
    ]

    const sentimentResult = await client.analyzeSentiment(sentimentInput);
    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        console.log(`\tDocument Sentiment: ${document.sentiment}`);
        console.log(`\tDocument Scores:`);
        console.log(`\t\tPositive: ${document.documentScores.positive.toFixed(2)} \tNegative: ${document.documentScores.negative.toFixed(2)} \tNeutral: ${document.documentScores.neutral.toFixed(2)}`);
        console.log(`\tSentences Sentiment(${document.sentences.length}):`);
        document.sentences.forEach(sentence => {
            console.log(`\t\tSentence sentiment: ${sentence.sentiment}`)
            console.log(`\t\tSentences Scores:`);
            console.log(`\t\tPositive: ${sentence.sentenceScores.positive.toFixed(2)} \tNegative: ${sentence.sentenceScores.negative.toFixed(2)} \tNeutral: ${sentence.sentenceScores.neutral.toFixed(2)}`);
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
                Positive: 0.61  Negative: 0.01  Neutral: 0.39
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

## <a name="language-detection"></a>Nyelvfelismerés

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `detectLanguages()` metódusát, és szerezze be a visszaadott `DetectLanguageResult`. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit a megfelelő elsődleges és észlelt nyelven.

```javascript
async function languageDetection(client) {

    const languageInputArray = [
        "Ce document est rédigé en Français."
    ]

    const languageResult = await client.detectLanguages(languageInputArray);

    result.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tDetected Language ${language.name}`)
        );
        console.log(`\tPrimary Language ${document.primaryLanguage.name}`)
    });
}
languageDetection(textAnalyticsClient);
```

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
ID: 0
        Detected Language French
        Primary Language French
```

## <a name="named-entity-recognition"></a>Nevesített entitások felismerése

> [!NOTE]
> Az alábbi kód a megnevezett Entity Recognition v3, amely nyilvános előzetes verzióban érhető el.

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
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
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
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 0, Length: 9    Score: 1
        Name: Bill Gates        Type: Person    Sub Type: N/A
        Offset: 25, Length: 10  Score: 0.999786376953125
        Name: Paul Allen        Type: Person    Sub Type: N/A
        Offset: 40, Length: 10  Score: 0.9988105297088623
        Name: April 4, 1975     Type: DateTime  Sub Type: Date
        Offset: 54, Length: 13  Score: 0.8
        Name: Altair    Type: Organization      Sub Type: N/A
        Offset: 116, Length: 6  Score: 0.7996330857276917
        Name: 8800      Type: Quantity  Sub Type: Number
        Offset: 123, Length: 4  Score: 0.8
Document ID: 1
        Name: Microsoft         Type: Organization      Sub Type: N/A
        Offset: 21, Length: 9   Score: 0.9837456345558167
        Name: 21        Type: Quantity  Sub Type: Number
        Offset: 71, Length: 2   Score: 0.8
```

## <a name="named-entity-recognition---personal-information-public-preview"></a>Nevesített entitások felismerése – személyes adatok (nyilvános előzetes verzió)

> [!NOTE]
> Az alábbi kód a személyes adatok észlelésére szolgál az elnevezett Entity Recognition v3 használatával, amely nyilvános előzetes verzióban érhető el.

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizePiiEntities()` metódusát, és szerezze be a `EntitiesBatchResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, típusát, altípusát, eltolását, hosszát és pontszámát.


```javascript
async function entityPiiRecognition(client){

    const entityPiiInput = [
        "Insurance policy for SSN on file 123-12-1234 is here by approved."
    ]
    const entityResults = await client.recognizePiiEntities(entityPiiInput);

    result.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tType: ${entity.type} \tSub Type: ${entity.subtype != "" ? entity.subtype : "N/A"}`);
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
        Name: 123-12-1234       Type: U.S. Social Security Number (SSN)         Sub Type: N/A
        Offset: 33, Length: 11  Score: 0.85
```

## <a name="entity-linking"></a>Entitáskapcsolás

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `recognizeLinkedEntities()` metódusát, és szerezze be a `RecognizeLinkedEntitiesResult` objektumot. Ismételje meg az eredmények listáját, és nyomtassa ki az entitás nevét, AZONOSÍTÓját, forrását, URL-címét és egyezéseit. `matches` tömb minden objektuma az adott egyezés eltolását, hosszát és pontszámát fogja tartalmazni.

```javascript
async function linkedEntityRecognition(client){

    const linkedEntityInput = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800. During his career at Microsoft, Gates held the positions of chairman, chief executive officer, president and chief software architect, while also being the largest individual shareholder until May 2014."
    ]
    const entityResults = await client.recognizeLinkedEntities(linkedEntityInput);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.name} \tID: ${entity.id} \tURL: ${entity.url} \tData Source: ${entity.dataSource}`);
            console.log(`\tMatches:`)
            entity.matches.forEach(match => {
                console.log(`\t\tText: ${match.text}`);
                console.log(`\t\tOffset: ${match.offset}, Length: ${match.length} \tScore: ${match.score.toFixed(3)}`);
            })
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
                Offset: 116, Length: 11         Score: 0.650
        Name: Bill Gates        ID: Bill Gates  URL: https://en.wikipedia.org/wiki/Bill_Gates   Data Source: Wikipedia
        Matches:
                Text: Bill Gates
                Offset: 25, Length: 10  Score: 0.243
                Text: Gates
                Offset: 161, Length: 5  Score: 0.243
        Name: Paul Allen        ID: Paul Allen  URL: https://en.wikipedia.org/wiki/Paul_Allen   Data Source: Wikipedia
        Matches:
                Text: Paul Allen
                Offset: 40, Length: 10  Score: 0.174
        Name: Microsoft         ID: Microsoft   URL: https://en.wikipedia.org/wiki/Microsoft    Data Source: Wikipedia
        Matches:
                Text: Microsoft
                Offset: 0, Length: 9    Score: 0.196
                Text: Microsoft
                Offset: 150, Length: 9  Score: 0.196
        Name: April 4   ID: April 4     URL: https://en.wikipedia.org/wiki/April_4      Data Source: Wikipedia
        Matches:
                Text: April 4
                Offset: 54, Length: 7   Score: 0.137
        Name: BASIC     ID: BASIC       URL: https://en.wikipedia.org/wiki/BASIC        Data Source: Wikipedia
        Matches:
                Text: BASIC
                Offset: 89, Length: 5   Score: 0.052
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Az elemezni kívánt dokumentumot tartalmazó karakterláncok tömbjét hozza létre. Hívja meg az ügyfél `extractKeyPhrases()` metódusát, és szerezze be a visszaadott `ExtractKeyPhrasesResult` objektumot. Ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

```javascript
async function keyPhraseExtraction(client){

    const keyPhrasesInput = [
        "My cat might need to see a veterinarian.",
    ]

    const result = await client.extractKeyPhrases(keyPhrasesInput)


    result.forEach(document => {
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

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési fájlban lévő `node` paranccsal.

```console
node index.js
```
