---
title: 'Gyors útmutató: Text Analytics ügyféloldali kódtár a Go-hoz | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban a nyelvet az Azure Cognitive Services go Text Analytics ügyféloldali kódtár használatával ismerheti meg.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 02/26/2020
ms.author: aahi
ms.openlocfilehash: 0b4495616c750b2b3e8431e011d71ae8671af1ef
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77912640"
---
# <a name="quickstart-use-the-text-analytics-client-library-for-go"></a>Gyors útmutató: az Text Analytics ügyféloldali kódtár használata a Go-hoz

[Útmutató a dokumentációs](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | [könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [-csomagjához (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | tartozó[mintákhoz](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

> [!NOTE]
> Ez a rövid útmutató csak az Text Analytics 2,1-es verziójára vonatkozik. Jelenleg nem érhető el a v3-es ügyféloldali kódtár.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="hozzon"  target="_blank">létre egy Text Analytics erőforrást, és hozzon létre egy Text Analytics-erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. 
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Text Analytics APIhoz való összekapcsolásához. Ezt később is megteheti a rövid útmutatóban.
    * Az ingyenes díjszabási csomaggal kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-go-project"></a>Új go-projekt létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, és navigáljon hozzá. A munkaterület három mappát fog tartalmazni: 

* **src** – ez a könyvtár forráskódot és csomagokat tartalmaz. A `go get` paranccsal telepített csomagok itt fognak megjelenni.
* **pkg** – ez a könyvtár tartalmazza a lefordított go csomag objektumait. Ezek a fájlok mindegyike `.a` rendelkezik bővítménnyel.
* **bin** – ez a könyvtár tartalmazza a futtatásakor `go install`létrehozott bináris végrehajtható fájlokat.

> [!TIP]
> További információ a [Go-munkaterület](https://golang.org/doc/code.html#Workspaces)struktúrájáról. Ez az útmutató a és `$GOPATH` `$GOROOT`a beállításával kapcsolatos információkat tartalmaz.

Hozzon létre egy `my-app` nevű munkaterületet és a `src`szükséges `pkg`alkönyvtárakat a, a és `bin`a következőhöz:

```console
$ mkdir -p my-app/{src, bin, pkg}  
$ cd my-app
```

### <a name="install-the-text-analytics-client-library-for-go"></a>Az Text Analytics ügyféloldali kódtár telepítése a Go-hoz

Telepítse az ügyféloldali kódtárat a Go-hoz: 

```console
$ go get -u <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```console
$ dep ensure -add <https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics>
```

### <a name="create-your-go-application"></a>Go-alkalmazás létrehozása

Ezután hozzon létre egy nevű `src/quickstart.go`fájlt:

```bash
$ cd src
$ touch quickstart.go
```

Nyissa meg `quickstart.go` a kedvenc ide-vagy szövegszerkesztőjét. Ezután adja hozzá a csomag nevét, és importálja a következő könyvtárakat:

[!code-go[Import statements](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=imports)]

## <a name="object-model"></a>Objektummodell 

Az Text Analytics-ügyfél egy [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként. 

A rendszer az API- `documents`nak elküldi a szöveget, amely a használt `dictionary` módszertől függően a, `id`a `text`és `language` az attribútumok kombinációját tartalmazó objektumokat tartalmazza. Az `text` attribútum tárolja a forrásban `language`elemezni kívánt szöveget, és a `id` értéke bármilyen lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése


Egy új függvényben hozzon létre változókat az erőforrás Azure-végpontja és előfizetési kulcsa számára.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

Hozzon létre egy új [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) objektumot. Adja át a kulcsát az [autorest-nek. A NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) függvény, amelyet a rendszer az ügyfél `authorizer` tulajdonságának továbbít.

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

Hozzon létre egy nevű `SentimentAnalysis()` új függvényt, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott metódus használatával. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objektumok listáját. Minden objektum tartalmaz egy `id` `Language` és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket. 

Hívja meg az ügyfél [hangulata ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) függvényt, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a hangulat pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

[!code-go[Sentiment analysis sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=sentimentAnalysis)]

hívás `SentimentAnalysis()` a projektben.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy nevű `LanguageDetection()` új függvényt, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott metódus használatával. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) -objektumok listáját. Minden objektum tartalmaz egy `id` és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, és a `id` értéke bármilyen lehet. 

Hívja meg az ügyfél [DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt nyelvet.

[!code-go[Language detection sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=languageDetection)]

Hívás `LanguageDetection()` a projektben.

### <a name="output"></a>Kimenet

```console
Document ID: 0 , Language: English 
Document ID: 1 , Language: Spanish
Document ID: 2 , Language: Chinese_Simplified
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre egy nevű `ExtractEntities()` új függvényt, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott metódus használatával. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objektumok listáját. Minden objektum tartalmaz egy `id`, `language`és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket. 

Hívja meg az ügyfél [entitásait ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és a kinyert entitások pontszámát.

[!code-go[entity recognition sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=entityRecognition)]

hívás `ExtractEntities()` a projektben.

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

Hozzon létre egy nevű `ExtractKeyPhrases()` új függvényt, és hozzon létre egy ügyfelet a `GetTextAnalyticsClient()` korábban létrehozott metódus használatával. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objektumok listáját. Minden objektum tartalmaz egy `id`, `language`és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket.

Hívja meg az ügyfél [Alkifejezéseit ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és a kinyert fő kifejezéseket.

[!code-go[key phrase extraction sample](~/azure-sdk-for-go-samples/cognitiveservices/textanalytics.go?name=keyPhrases)]

Hívás `ExtractKeyPhrases()` a projektben.

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
