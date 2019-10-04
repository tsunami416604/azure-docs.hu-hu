---
title: 'Gyors útmutató: A Go-hoz készült ügyféloldali kódtár Text Analytics | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services Text Analytics API használatának gyors megkezdéséhez olvassa el az információk és a kódok mintáit.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 08/28/2019
ms.author: aahi
ms.openlocfilehash: 1ba2ec6b5c0c59be7b7264f7558fbb393adcc2d8
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142788"
---
# <a name="quickstart-text-analytics-client-library-for-go"></a>Gyors útmutató: Text Analytics ügyféloldali kódtár a Go-hoz

Ismerkedjen meg az Text Analytics ügyféloldali kódtáraval a Go-ban. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. 

A következő lépésekhez használja a Text Analytics ügyféloldali függvénytárát:

* Hangulatelemzés
* Nyelvfelismerés
* Entitások felismerése
* A kulcsfontosságú kifejezések kinyerése

[Útmutató](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/textanalytics?view=azure-python) | a dokumentációs[könyvtár forráskód](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-language-textanalytics) | [-csomagjához (GitHub)](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics) | tartozó[mintákhoz](https://github.com/Azure-Samples/cognitive-services-quickstart-code)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
* A [Go](https://golang.org/dl/) legújabb verziója

## <a name="setting-up"></a>Beállítás

### <a name="create-a-text-analytics-azure-resource"></a>Text Analytics Azure-erőforrás létrehozása 

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást Text Analytics a helyi gépen található [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).
* Tekintse meg az erőforrást a [Azure Portalon](https://portal.azure.com).

Miután beolvasott egy kulcsot a próbaverziós előfizetésből vagy erőforrásból, [hozzon létre egy környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) a (z) nevű `TEXT_ANALYTICS_SUBSCRIPTION_KEY`kulcshoz.

### <a name="create-a-new-go-project"></a>Új go-projekt létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, és navigáljon hozzá. A munkaterület három mappát fog tartalmazni: 

* **src** – ez a könyvtár forráskódot és csomagokat tartalmaz. A `go get` paranccsal telepített csomagok itt fognak megjelenni.
* **pkg** – ez a könyvtár tartalmazza a lefordított go csomag objektumait. Ezek a fájlok mindegyike `.a` rendelkezik bővítménnyel.
* **bin** – ez a könyvtár tartalmazza a futtatásakor `go install`létrehozott bináris végrehajtható fájlokat.

> [!TIP]
> További információ a [Go-munkaterület](https://golang.org/doc/code.html#Workspaces)struktúrájáról. Ez az útmutató a és `$GOPATH` `$GOROOT`a beállításával kapcsolatos információkat tartalmaz.

Hozzon létre egy `my-app` nevű munkaterületet és a szükséges `pkg`alkönyvtárakat a, a és `bin`a következőhöz `src`:

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

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
    "log"
    "os"    
)
```

Adja hozzá a következő függvényeket a projekthez, mivel a rövid útmutatóban szereplő paraméterek és tulajdonságok többsége karakterláncot és logikai mutatókat vár.

```golang
// returns a pointer to the string value passed in.
func StringPointer(v string) *string {
    return &v
}

// returns a pointer to the bool value passed in.
func BoolPointer(v bool) *bool {
    return &v
}
```

Az alkalmazás `main` függvényében hozzon létre változókat az erőforrás Azure-végpontja és előfizetési kulcsa számára. Szerezze be ezeket az értékeket a környezeti változók TEXT_ANALYTICS_SUBSCRIPTION_KEY és TEXT_ANALYTICS_ENDPOINT. Ha ezeket a környezeti változókat az alkalmazás szerkesztésének megkezdése után hozta létre, akkor be kell állítania és újra meg kell nyitnia azt a szerkesztőt, IDE vagy rendszerhéjt, amelyet a változók eléréséhez használ.

[!INCLUDE [text-analytics-find-resource-information](../includes/find-azure-resource-info.md)]

```golang
var subscriptionKeyVar string = "TEXT_ANALYTICS_SUBSCRIPTION_KEY"
if "" == os.Getenv(subscriptionKeyVar) {
    log.Fatal("Please set/export the environment variable " + subscriptionKeyVar + ".")
}
var subscriptionKey string = os.Getenv(subscriptionKeyVar)
var endpointVar string = "TEXT_ANALYTICS_ENDPOINT"
if "" == os.Getenv(endpointVar) {
    log.Fatal("Please set/export the environment variable " + endpointVar + ".")
}
var endpoint string = os.Getenv(endpointVar)
```

## <a name="object-model"></a>Objektummodell 

Az Text Analytics-ügyfél egy [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) objektum, amely az Azure-ban hitelesíti magát a kulcs használatával. Az ügyfél számos módszert biztosít a szöveg elemzéséhez, egyetlen sztringként vagy kötegként. 

A rendszer az API-nak `documents`elküldi a szöveget, `dictionary` amely a használt módszertől függően a, `id`a `text`és `language` az attribútumok kombinációját tartalmazó objektumokat tartalmazza. Az `text` attribútum tárolja a forrásban `language`elemezni kívánt szöveget, és a `id` értéke bármilyen lehet. 

A válasz objektum az egyes dokumentumok elemzési információit tartalmazó lista. 

## <a name="code-examples"></a>Példák a kódokra

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következőket a Pythonhoz készült Text Analytics ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Hangulatelemzés](#sentiment-analysis)
* [Nyelvfelismerés](#language-detection)
* [Entitások felismerése](#entity-recognition)
* [Fő kifejezés kibontása](#key-phrase-extraction)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A projekt fő függvényében hozzon létre egy új [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#New) objektumot. Adja át a kulcsát az autorest-nek [. A NewCognitiveServicesAuthorizer ()](https://godoc.org/github.com/Azure/go-autorest/autorest#NewCognitiveServicesAuthorizer) függvény, amelyet a rendszer az ügyfél `authorizer` tulajdonságának továbbít.

```golang
textAnalyticsClient := textanalytics.New(endpoint)
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre egy nevű `SentimentAnalysis()` új függvényt, amely a korábban létrehozott ügyfelet veszi fel. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objektumok listáját. Minden objektum tartalmaz egy `id` `Language` és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanebben a függvényben hívja meg az ügyfél [hangulata ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Sentiment) függvényt, és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint a hangulat pontszámát. Ha a pontszám közelebb van a 0 értékhez, a negatív érzést jelez, míg az 1. számú pontszám pozitív hangulatot jelez.

```golang
result, err := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s\n", *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

A projekt fő függvényében hívja `SentimentAnalysis()`meg a t.

### <a name="output"></a>Output

```console
Document ID: 1 , Sentiment Score: 0.87
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy nevű `LanguageDetection()` új függvényt, amely a korábban létrehozott ügyfelet veszi fel. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [LanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#LanguageInput) -objektumok listáját. Minden objektum tartalmaz egy `id` és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, és a `id` értéke bármilyen lehet. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanebben a függvényben hívja meg az ügyfél [DetectLanguage ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.DetectLanguage) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit, valamint az észlelt nyelvet.

```golang
result, err := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)
if err != nil { log.Fatal(err) }

// Printing language detection results
for _,document := range *result.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Detected Languages with Score: ")
    for _,language := range *document.DetectedLanguages{
        fmt.Printf("%s %f,",*language.Name, *language.Score)
    }
    fmt.Println()
}

// Printing document errors
fmt.Println("Document Errors:")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
fmt.Println()
```

A projekt fő függvényében hívja `LanguageDetection()`meg a t.

### <a name="output"></a>Output

```console
Document ID: 0 Detected Languages with Score: English 1.000000
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre egy nevű `ExtractEntities()` új függvényt, amely a korábban létrehozott ügyfelet veszi fel. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objektumok listáját. Minden objektum tartalmaz egy `id`, `language`és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket. 

```golang
func ExtractEntities(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanebben a függvényben hívja meg az ügyfél [entitásait ()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.Entities) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és a kinyert entitások pontszámát.

```golang
    result, err := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted entities results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Entities:\n")
        for _,entity := range *document.Entities{
            fmt.Printf("\t\tName: %s\tType: %s",*entity.Name, *entity.Type)
            if entity.SubType != nil{
                fmt.Printf("\tSub-Type: %s\n", *entity.SubType)
            }
            fmt.Println()
            for _,match := range *entity.Matches{
                fmt.Printf("\t\t\tOffset: %v\tLength: %v\tScore: %f\n", *match.Offset, *match.Length, *match.EntityTypeScore)
            }
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

A projekt fő függvényében hívja `ExtractEntities()`meg a t.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 0   Length: 9   Score: 1.000000
        Name: Bill Gates    Type: Person
            Offset: 25  Length: 10  Score: 0.999847
        Name: Paul Allen    Type: Person
            Offset: 40  Length: 10  Score: 0.998841
        Name: April 4   Type: Other
            Offset: 54  Length: 7   Score: 0.800000
        Name: April 4, 1975 Type: DateTime  Sub-Type: Date

            Offset: 54  Length: 13  Score: 0.800000
        Name: BASIC Type: Other
            Offset: 89  Length: 5   Score: 0.800000
        Name: Altair 8800   Type: Other
            Offset: 116 Length: 11  Score: 0.800000
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozzon létre egy nevű `ExtractKeyPhrases()` új függvényt, amely a korábban létrehozott ügyfelet veszi fel. Hozza létre az elemezni kívánt dokumentumokat tartalmazó [MultiLanguageInput](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#MultiLanguageBatchInput) -objektumok listáját. Minden objektum tartalmaz egy `id`, `language`és egy `text` attribútumot. Az `text` attribútum tárolja az elemezni kívánt szöveget, `language` a dokumentum nyelvét, a `id` pedig bármely értéket.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanebben a függvényben hívja meg az ügyfél alkifejezéseit [()](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics#BaseClient.KeyPhrases) , és szerezze be az eredményt. Ezután ismételje meg az eredményeket, és nyomtassa ki az egyes dokumentumok AZONOSÍTÓit és a kinyert fő kifejezéseket.

```golang
    result, err := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)
    if err != nil { log.Fatal(err) }

    // Printing extracted key phrases results
    for _,document := range *result.Documents {
        fmt.Printf("Document ID: %s\n" , *document.ID)
        fmt.Printf("\tExtracted Key Phrases:\n")
        for _,keyPhrase := range *document.KeyPhrases{
            fmt.Printf("\t\t%s\n",keyPhrase)
        }
        fmt.Println()
    }

    // Printing document errors
    fmt.Println("Document Errors:")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
    fmt.Println()
```

A projekt fő függvényében hívja `ExtractKeyPhrases()`meg a t.

### <a name="output"></a>Output

```console
Document ID: 0
    Extracted Key Phrases:
        cat
        veterinarian
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével az erőforráscsoporthoz társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések


> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)


* [A Text Analytics áttekintése](../overview.md)
* [Hangulat elemzése](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Entitások felismerése](../how-tos/text-analytics-how-to-entity-linking.md)
* [Nyelv felismerése](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Nyelvi felismerés](../how-tos/text-analytics-how-to-language-detection.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices)található.
