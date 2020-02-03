---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: aahi
ms.openlocfilehash: d5197cfe749ae27b9f8807ba62825c8a73371c85
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76748910"
---
<a name="HOLTop"></a>

[Dokumentáció](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics) | [könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesTextAnalytics) | [csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-textanalytics) | [minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples/)

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

Hozzon létre egy `index.js` nevű fájlt, és adja hozzá a következő könyvtárakat:

[!code-javascript[Const statements](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=constStatements)]

Hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának létrehozásához.

[!INCLUDE [text-analytics-find-resource-information](../../find-azure-resource-info-v2.md)]

```javascript
const subscription_key = '<paste-your-text-analytics-key-here>';
const endpoint = '<paste-your-text-analytics-endpoint-here>';
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a `@azure/ms-rest-js` és `@azure/cognitiveservices-textanalytics` NPM csomagokat:

```console
npm install @azure/cognitiveservices-textanalytics @azure/ms-rest-js
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

## <a name="object-model"></a>Objektummodell

Az Text Analytics-ügyfél egy [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként.

A rendszer elküldi a szöveget az API-nak `documents`-listaként, amely a használt módszertől függően `id`, `text`és `language` attribútumok kombinációját tartalmazó objektumokat `dictionary`. A `text` attribútum tárolja a forrás `language`elemezni kívánt szöveget, és a `id` értéke lehet.

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista.

## <a name="code-examples"></a>Példák a kódokra

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Hozzon létre egy új [TextAnalyticsClient](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient) objektumot `credentials` és `endpoint` paraméterként.

[!code-javascript[Authentication and client creation](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=authentication)]


## <a name="sentiment-analysis"></a>Hangulatelemzés

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

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy listát a dokumentumokat tartalmazó szótár-objektumokról. Hívja meg az ügyfél [detectLanguage ()](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/textanalyticsclient#detectlanguage-models-textanalyticsclientdetectlanguageoptionalparams-) metódusát, és szerezze be a visszaadott [LanguageBatchResult](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-textanalytics/languagebatchresult). Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és nyelvét.

[!code-javascript[Language detection](~/cognitive-services-node-sdk-samples/Samples/textAnalytics.js?name=languageDetection)]

Futtassa a kódot `node index.js` a konzol ablakában.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Language: English
Document ID: 2 , Language: Spanish
Document ID: 3 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

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

## <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

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

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési fájlban lévő `node` paranccsal.

```console
node index.js
```
