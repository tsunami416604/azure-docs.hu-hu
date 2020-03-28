---
title: 'Rövid útmutató: Text Analytics ügyféltár gohoz | Microsoft dokumentumok'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban észlelheti a nyelvet az Azure Cognitive Services Go Text Analytics ügyfélkönyvtárának használatával.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77912640"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Rövid útmutató: A Text Analytics ügyféltár használata az ugráshoz

[Referenciadokumentációs](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [függvénytár forráskódcsomagjának](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [(GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | [mintái](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Ez a rövid útmutató csak a Text Analytics 2.1-es verziójára vonatkozik. Jelenleg egy v3-as ügyféltár go nem érhető el.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója
* Miután rendelkezik az <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="Azure-előfizetéssel, hozzon <span class="docon docon-navigate-external x-hidden-focus"></span> </a> létre egy Text Analytics-erőforrást"  target="_blank">az Azure Portalon a kulcs és a végpont leéséhez. 
    * Szüksége lesz a kulcs és a végpont a létrehozott erőforrásból az alkalmazás és a Text Analytics API csatlakoztatásához. Ezt később a rövid útmutatóban fogja megtenni.
    * Használhatja az ingyenes tarifacsomag a szolgáltatás kipróbálásához, és frissítse később egy fizetett szint éles környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-go-project"></a>Új Go-projekt létrehozása

Egy konzolablakban (cmd, PowerShell, Terminál, Bash) hozzon létre egy új munkaterületet a Go-projekthez, és keresse meg azt. A munkaterület három mappát tartalmaz: 

* **src** - Ez a könyvtár forráskódot és csomagokat tartalmaz. A `go get` paranccsal telepített csomagok itt fognak elhelyeződni.
* **pkg** - Ez a könyvtár tartalmazza a lefordított Go csomag objektumokat. Ezek a fájlok `.a` mind rendelkeznek kiterjesztéssel.
* **bin** - Ez a könyvtár a futtatáskor `go install`létrehozott bináris végrehajtható fájlokat tartalmazza.

> [!TIP]
> További információ a [Go-munkaterület](https://golang.org/doc/code.html#Workspaces)szerkezetéről. Ez az útmutató `$GOPATH` a `$GOROOT`beállítással és a beállításával kapcsolatos információkat tartalmazza.

Hozzon létre `my-app` egy munkaterületet, amelynek `pkg`neve `bin`és a szükséges alkönyvtárak a `src`hoz létre, és:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>A Text Analytics-ügyféltár telepítése az ugráshoz

Az ügyfélkódtár telepítése az ugráshoz: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

vagy ha dep-t használ, a társzalag-futtatáson belül:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>A Go alkalmazás létrehozása

Ezután hozzon `src/quickstart.go`létre egy fájl neve :

```bash
$ cd src
$ touch quickstart.go
```

Nyissa `quickstart.go` meg kedvenc IDE-jét vagy szövegszerkesztőjét. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objektummodell 

A Text Analytics-ügyfél egy [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) objektum, amely a kulcs használatával hitelesíti magát az Azure-ban. Az ügyfél számos módszert biztosít a szöveg elemzésére, egyetlen karakterláncként vagy kötegként. 

A program a szöveget a `documents`használt `dictionary` módszertől függően `id`a `text`, `language` a kombinációját és attribútumokat tartalmazó objektumok listájaként küldi el az API-nak. Az `text` attribútum az eredeti `language`helyen tárolja az elemzendő szöveget, és az `id` bármilyen érték lehet. 

A válaszobjektum egy lista, amely az egyes dokumentumok elemzési adatait tartalmazza. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek bemutatják, hogyan kell a következőket a Pythonszöveg-elemzési ügyfélkódtárban végezni:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Kulcskifejezés kinyerése](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése


Egy új függvényben hozzon létre változókat az erőforrás Azure-végpontjának és előfizetési kulcsának.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Hozzon létre egy új [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) objektumot. Add át a kulcsot az [autorestnek. NewCognitiveServicesAuthorizer()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) függvény, amely ezután át kerül `authorizer` az ügyfél tulajdonsága.

```go
func GetTextAnalyticsClient() textanalytics.BaseClient {
    var key string = "<paste-your-text-analytics-key-here>"
    var endpoint string = "<paste-your-text-analytics-endpoint-here>"

    textAnalyticsClient := textanalytics.New(endpoint)
    textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(key)

    return textAnalyticsClient
}
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre `SentimentAnalysis()` egy új függvényt, amelyet úgy hívnak, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott módszerrel. Hozzon létre egy listát a [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) objektumok, amely tartalmazza a dokumentumokat szeretne elemezni. Minden objektum tartalmaz `id` `Language` egy `text` t és egy attribútumot. Az `text` attribútum tárolja az elemzendő szöveget, `language` a dokumentum `id` nyelve, és bármilyen érték lehet. 

Hívja meg az ügyfél [Sentiment()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) függvényét, és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját és a hangulatpontszámát. A 0-hoz közelebbi pontszám negatív érzést, míg az 1-hez közelebbi pontszám pozitív véleményt jelez.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

behívásra. `SentimentAnalysis()`

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre `LanguageDetection()` egy új függvényt, amelyet úgy hívnak, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott módszerrel. Hozzon létre egy listát a [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) objektumok, amely tartalmazza a dokumentumokat szeretne elemezni. Minden objektum tartalmaz `id` egy `text` attribútumot és egy attribútumot. Az `text` attribútum tárolja az elemzendő `id` szöveget, és bármilyen érték lehet. 

Hívja meg az ügyfél [DetectLanguage()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) és az eredmény. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját, és észlelte a nyelvet.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Hívja `LanguageDetection()` be a projektet.

### <a name="output"></a>Kimenet

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre `ExtractEntities()` egy új függvényt, amelyet úgy hívnak, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott módszerrel. Hozzon létre egy listát a [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) objektumok, amely tartalmazza a dokumentumokat szeretne elemezni. Minden objektum tartalmaz `id` `language`egy , `text` és egy attribútumot. Az `text` attribútum tárolja az elemzendő szöveget, `language` a dokumentum `id` nyelve, és bármilyen érték lehet. 

Hívja meg az ügyfél [entitásait()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) és kapja meg az eredményt. Ezután végighaladhat az eredményeken, és nyomtassa ki az egyes dokumentumok azonosítóját, és a kinyert entitások pontszám.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

behívásra. `ExtractEntities()`

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

Hozzon létre `ExtractKeyPhrases()` egy új függvényt, amelyet úgy hívnak, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott módszerrel. Hozzon létre egy listát a [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) objektumok, amely tartalmazza a dokumentumokat szeretne elemezni. Minden objektum tartalmaz `id` `language`egy , `text` és egy attribútumot. Az `text` attribútum tárolja az elemzendő szöveget, `language` a dokumentum `id` nyelve, és bármilyen érték lehet.

Hívja fel az ügyfél [KeyPhrases()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) és kap az eredmény. Ezután végighaladhat az eredményeken, és kinyomtathatja az egyes dokumentumok azonosítóját, és kibonthatja a kulcskifejezéseket.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Hívja `ExtractKeyPhrases()` be a projektet.

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
                veterinarian
Document ID: 4
         Key phrases:
                fútbol
```
