---
title: 'Gyors útmutató: A szövegelemzési API meghívására Node.js használatával'
titleSuffix: Azure Cognitive Services
description: Ezekkel a rövid útmutatókkal és kódmintákkal gyorsan elsajátíthatja a Text Analytics API használatának alapjait.
services: cognitive-services
author: raymondl
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 06/11/2019
ms.author: shthowse
ms.openlocfilehash: 7e43d53c0916cf7fdc684c9e044e632015662c3b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67081518"
---
# <a name="quickstart-using-nodejs-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: A Text Analytics kognitív szolgáltatás hívásához Node.js használatával
<a name="HOLTop"></a>

Ez a rövid útmutató segítségével megkezdheti a Text Analytics SDK-val nyelvi elemzése a node.js-ben. Bár a [Szövegelemzés](//go.microsoft.com/fwlink/?LinkID=759711) REST API-val kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/blob/master/Samples/textAnalytics.js).

Az API-k műszaki dokumentációjáért lásd az [API-definíciókat](//go.microsoft.com/fwlink/?LinkID=759346).

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/)
* A Text Analytics [SDK for Node.js](https://www.npmjs.com/package/azure-cognitiveservices-textanalytics) telepítheti az SDK-val:

    `npm install azure-cognitiveservices-textanalytics`

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../How-tos/text-analytics-how-to-access-key.md) is rendelkeznie kell.

## <a name="create-a-nodejs-application-and-install-the-sdk"></a>Node.js-alkalmazás létrehozása és az SDK telepítése

Miután telepítette a Node.js, a csomópont-projekt létrehozása. Hozzon létre egy új könyvtárat az alkalmazás számára, és lépjen abba a könyvtárba.

```mkdir myapp && cd myapp```

Futtatás ```npm init``` a node-alkalmazás létrehozása a package.json fájllal. Telepítse a `ms-rest-azure` és `azure-cognitiveservices-textanalytics` NPM csomagok:

```npm install azure-cognitiveservices-textanalytics ms-rest-azure```

Az alkalmazás package.json fájlt a függőségeket tartalmazó frissülni fog.

## <a name="authenticate-your-credentials"></a>A hitelesítő adatokat

Hozzon létre egy új fájlt `index.js` a projekt legfelső szintű és a telepített könyvtárak importálása

```javascript
const CognitiveServicesCredentials = require("ms-rest-azure").CognitiveServicesCredentials;
const TextAnalyticsAPIClient = require("azure-cognitiveservices-textanalytics");
```

Hozzon létre egy változót a Szövegelemzés előfizetéshez kulcsot.

```javascript
let credentials = new CognitiveServicesCredentials(
    "enter-your-key-here"
);
```

> [!Tip]
> A titkos kulcsok az éles rendszereket a telepítés biztonságának használatát javasoljuk [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/quick-create-net).
>

## <a name="create-a-text-analytics-client"></a>A Text Analytics-ügyfél létrehozása

Hozzon létre egy új `TextAnalyticsClient` rendelkező objektum `credentials` paraméterként. Használja a megfelelő Azure-régióban a Szövegelemzés előfizetéshez.

```javascript
//Replace 'westus' with the correct region for your Text Analytics subscription
let client = new TextAnalyticsAPIClient(
    credentials,
    "https://westus.api.cognitive.microsoft.com/"
);
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre egy objektumot, amely tartalmazza a dokumentumok elemezni szeretné. Listáját áll, az API-t a hasznos `documents`, amely tartalmaz egy `id`, `language`, és `text` attribútum. A `text` attribútumtárak elemezni, a szöveg `language` nyelve az a dokumentumot, és a `id` bármilyen érték lehet. 

```javascript
const inputDocuments = {documents:[
    {language:"en", id:"1", text:"I had the best day of my life."},
    {language:"en", id:"2", text:"This was a waste of my time. The speaker put me to sleep."},
    {language:"es", id:"3", text:"No tengo dinero ni nada que dar..."},
    {language:"it", id:"4", text:"L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."}
]}
```

Hívás `client.sentiment` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és véleménypontszámot. 0 közelebb pontszámot azt jelzi, hogy egy negatív véleményt jelölnek, míg 1 közelebb pontszámot azt jelzi, hogy egy pozitív véleményt.

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

Az Ön kódjának futtatásához `node index.js` a konzolablakban.

### <a name="output"></a>Kimenet

```console
[ { id: '1', score: 0.8723785877227783 },
  { id: '2', score: 0.1059873104095459 },
  { id: '3', score: 0.43635445833206177 },
  { id: '4', score: 1 } ]
```

## <a name="language-detection"></a>Nyelvfelismerés

A dokumentumok tartalmazó objektumok listájának létrehozásához. Listáját áll, az API-t a hasznos `documents`, amely tartalmaz egy `id` és `text` attribútum. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet.

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

Hívás `client.detectLanguage()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és az első visszaadott nyelv.

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

Az Ön kódjának futtatásához `node index.js` a konzolablakban.

### <a name="output"></a>Kimenet

```console
===== LANGUAGE EXTRACTION ======
ID: 1 Language English
ID: 2 Language Spanish
ID: 3 Language Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre az objektum, amely tartalmazza a dokumentumok listáját. Listáját áll, az API-t a hasznos `documents`, amely tartalmaz egy `id`, `language`, és `text` attribútum. A `text` attribútumtárak elemezni, a szöveg `language` nyelve az a dokumentumot, és a `id` bármilyen érték lehet.

```javascript

    const inputDocuments = {documents:[
        {language:"en", id:"1", text:"Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800"},
        {language:"es", id:"2", text:"La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."},
        ]}

}
```

Hívás `client.entities()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum azonosítója. Az entitás minden egyes észlel, nyomtassa ki a wikipedia neve, típusa és altípusok (ha létezik) valamint a helyeken és az eredeti szöveg.

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

Az Ön kódjának futtatásához `node index.js` a konzolablakban.

### <a name="output"></a>Kimenet

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

Hozzon létre az objektum, amely tartalmazza a dokumentumok listáját. Listáját áll, az API-t a hasznos `documents`, amely tartalmaz egy `id`, `language`, és `text` attribútum. A `text` attribútumtárak elemezni, a szöveg `language` nyelve az a dokumentumot, és a `id` bármilyen érték lehet.

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

Hívás `client.keyPhrases()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki az egyes dokumentumok azonosítója, és bármely felismert kulcskifejezéseket.

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

Az Ön kódjának futtatásához `node index.js` a konzolablakban.

### <a name="output"></a>Kimenet

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

 [Text Analytics áttekintése](../overview.md) [– gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
