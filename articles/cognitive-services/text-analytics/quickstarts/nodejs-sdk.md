---
title: 'Gyors útmutató: A Node. js használata a Text Analytics API meghívásához'
titleSuffix: Azure Cognitive Services
description: Ezekkel a rövid útmutatókkal és kódmintákkal gyorsan elsajátíthatja a Text Analytics API használatának alapjait.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/30/2019
ms.author: shthowse
ms.openlocfilehash: 9b8a713d58d5753e04de050e0bc961b5e8388123
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697483"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Node. js használata a Text Analytics kognitív szolgáltatás meghívásához
<a name="HOLTop"></a>

Ezzel a rövid útmutatóval megkezdheti a nyelv elemzését a Node. js-hez készült Text Analytics SDK-val. Míg a [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API a legtöbb programozási nyelvvel kompatibilis, az SDK egyszerű módszert kínál a szolgáltatás alkalmazásba való integrálására. A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js)található.

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/)
* A [Node. js-hez](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) készült Text Analytics SDK az SDK-t a következő használatával telepítheti:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) is rendelkeznie kell.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Node. js-alkalmazás létrehozása és az SDK telepítése

A Node. js telepítése után hozzon létre egy csomópont-projektet. Hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon a címtárhoz.

```mkdir myapp && cd myapp```

Futtassa ```npm init``` a parancsot egy Node-alkalmazás egy Package. JSON fájllal való létrehozásához. Telepítse a `ms-rest-azure` és `azure-cognitiveservices-textanalytics` a NPM csomagokat:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Az alkalmazás Package. JSON fájlja a függőségekkel lesz frissítve.

## <a name="authenticate-your-credentials"></a>Hitelesítő adatok hitelesítése

Hozzon létre egy `index.js` új fájlt a projekt gyökerében, és importálja a telepített kódtárakat

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Hozzon létre egy változót a Text Analytics előfizetési kulcshoz.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> A titkok biztonságos üzembe helyezéséhez az éles rendszerekben javasolt a [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net)használata.
>

## <a name="create-a-text-analytics-client"></a>Text Analytics-ügyfél létrehozása

Hozzon létre `TextAnalyticsClient` egy új `credentials` objektumot paraméterként. A Text Analytics-előfizetéséhez használja a megfelelő Azure-régiót.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre egy listát az objektumok listájáról, amely tartalmazza az elemezni kívánt dokumentumokat. Az API hasznos adatai a, a, a és `documents` `language` `text` az `id`attribútumot tartalmazó listákból állnak. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

A `client.sentiment` hívás és az eredmény beolvasása. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a hangulat pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

```javascript
const operation = client.sentiment({multiLanguageBatchInput: inputDocuments})
operation
.then(result => {
    console.log(result.documents);
})
.catch(err => {
    throw err;
});
```

Futtassa a kódot `node index.js` a konzoljának ablakában.

### <a name="output"></a>Output

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Az API-ban található hasznos adatok a `documents` `id` és `text` a attribútumot tartalmazó listából állnak. Az `text` attribútum tárolja az elemezni kívánt szöveget, és a `id` értéke bármilyen lehet.

```javascript
// The documents to be submitted for language detection. The ID can be any value.
const inputDocuments = {
    documents: [
        { id: "1", text: "This is a document written in English." },
        { id: "2", text: "Este es un document escrito en Español." },
        { id: "3", text: "这是一个用中文写的文件" }
    ]
    };
```

A `client.detectLanguage()` hívás és az eredmény beolvasása. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az első visszaadott nyelvet.

```javascript
const operation = client.detectLanguage({
    languageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`ID: ${document.id}`);
        document.detectedLanguages.forEach(language =>
        console.log(`\tLanguage: ${language.name}`)
        );
    });
    })
    .catch(err => {
    throw err;
    });
```

Futtassa a kódot `node index.js` a konzoljának ablakában.

### <a name="output"></a>Output

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Az API hasznos adatai a, a, a és `documents` `language` `text` az `id`attribútumot tartalmazó listákból állnak. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

A `client.entities()` hívás és az eredmény beolvasása. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit. Minden észlelt entitás esetében nyomtassa ki a wikipedia nevét, típusát és altípusait (ha van), valamint az eredeti szöveg helyét.

```javascript
const operation = client.entities({
    multiLanguageBatchInput: inputDocuments
});
operation
    .then(result => {
    result.documents.forEach(document => {
        console.log(`Document ID: ${document.id}`)
        document.entities.forEach(e =>{
        console.log(`\tName: ${e.name} Type: ${e.type} Sub Type: ${e.type}`)
        e.matches.forEach(match => (
            console.log(`\t\tOffset: ${match.offset} Length: ${match.length} Score: ${match.entityTypeScore}`)
        ))
        })
    });
    })
    .catch(err => {
    throw err;
    });
```

Futtassa a kódot `node index.js` a konzoljának ablakában.

### <a name="output"></a>Output

```console
Document ID: 1
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 0 Length: 9 Score: 1
    Name: Bill Gates Type: Person Sub Type: Person
            Offset: 25 Length: 10 Score: 0.999786376953125
    Name: Paul Allen Type: Person Sub Type: Person
            Offset: 40 Length: 10 Score: 0.9988105297088623
    Name: April 4 Type: Other Sub Type: Other
            Offset: 54 Length: 7 Score: 0.8
    Name: April 4, 1975 Type: DateTime Sub Type: DateTime
            Offset: 54 Length: 13 Score: 0.8
    Name: BASIC Type: Other Sub Type: Other
            Offset: 89 Length: 5 Score: 0.8
    Name: Altair 8800 Type: Other Sub Type: Other
            Offset: 116 Length: 11 Score: 0.8
Document ID: 2
    Name: Microsoft Type: Organization Sub Type: Organization
            Offset: 21 Length: 9 Score: 0.999755859375
    Name: Redmond (Washington) Type: Location Sub Type: Location
            Offset: 60 Length: 7 Score: 0.9911284446716309
    Name: 21 kilómetros Type: Quantity Sub Type: Quantity
            Offset: 71 Length: 13 Score: 0.8
    Name: Seattle Type: Location Sub Type: Location
            Offset: 88 Length: 7 Score: 0.9998779296875
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozza létre a dokumentumokat tartalmazó objektumok listáját. Az API hasznos adatai a, a, a és `documents` `language` `text` az `id`attribútumot tartalmazó listákból állnak. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket.

```javascript
    let inputLanguage = {
    documents: [
        {language:"ja", id:"1", text:"猫は幸せ"},
        {language:"de", id:"2", text:"Fahrt nach Stuttgart und dann zum Hotel zu Fu."},
        {language:"en", id:"3", text:"My cat might need to see a veterinarian."},
        {language:"es", id:"4", text:"A mi me encanta el fútbol!"}
    ]
    };
```

A `client.keyPhrases()` hívás és az eredmény beolvasása. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt legfontosabb kifejezéseket.

```javascript
    let operation = client.keyPhrases({
    multiLanguageBatchInput: inputLanguage
    });
    operation
    .then(result => {
        console.log(result.documents);
    })
    .catch(err => {
        throw err;
    });
```

Futtassa a kódot `node index.js` a konzoljának ablakában.

### <a name="output"></a>Output

```console
[ 
    { id: '1', keyPhrases: [ '幸せ' ] },
    { id: '2', keyPhrases: [ 'Stuttgart', 'Hotel', 'Fahrt', 'Fu' ] },
    { id: '3', keyPhrases: [ 'cat', 'veterinarian' ] },
    { id: '4', keyPhrases: [ 'fútbol' ] } 
]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

 [Text Analytics áttekintése](../overview.md) [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
