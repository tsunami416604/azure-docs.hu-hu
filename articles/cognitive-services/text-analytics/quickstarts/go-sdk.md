---
title: 'Gyors útmutató: Meghívja a Text Analytics szolgáltatást, a Go SDK-val'
titleSuffix: Azure Cognitive Services
description: Get information és kód minták segítségével gyorsan első lépései a Microsoft Cognitive Services Text Analytics API használatával.
services: cognitive-services
author: laramume
manager: assafi
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 05/23/2019
ms.author: aahi
ms.openlocfilehash: 44def29292bc882fdaa08ff76667742756f178b8
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299610"
---
# <a name="quickstart-call-the-text-analytics-service-using-the-go-sdk"></a>Gyors útmutató: Meghívja a Text Analytics szolgáltatást, a Go SDK-val 
<a name="HOLTop"></a>

Ez a rövid útmutató segítségével megkezdheti a Text Analytics Góhoz készült SDK-val nyelvi elemzése. Ez a cikk bemutatja, hogyan nyelv, vélemények elemzése, kinyerheti a kulcsfontosságú kifejezéseket, és azonosítja a társított entitásokat. Bár a REST API kompatibilis szinte bármelyik programozási nyelvével, az SDK biztosít egy egyszerű módja annak, hogy a szolgáltatás integrálása az alkalmazásokba. Ez a minta forráskódja találhatók [GitHub](https://github.com/Azure-Samples/azure-sdk-for-go-samples/tree/master/cognitiveservices).

## <a name="prerequisites"></a>Előfeltételek

* A Text Analytics [Góhoz készült SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/textanalytics)

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

A regisztráció során létrejött [végponttal és hozzáférési kulccsal](../How-tos/text-analytics-how-to-access-key.md) is rendelkeznie kell.

## <a name="set-up-a-new-project"></a>Új projekt beállítása

Új Go-projekt létrehozása a kedvenc Kódszerkesztő, vagy IDE. Ezután adja hozzá a következő importálási utasítást a Go fájlhoz.

```golang
import (
    "context"
    "encoding/json"
    "fmt"
    "github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/textanalytics"
    "github.com/Azure/go-autorest/autorest"
)
```

Az alábbi funkciókat a projekthez hozzáadni a paramétereknek és tulajdonságoknak a ebben a rövid útmutatóban a legtöbb várt karakterlánc és logikai mutatók.

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

## <a name="create-text-analytics-client-and-authenticate-credentials"></a>Hozzon létre Text Analytics ügyfél és a hitelesítő adatok hitelesítése

A projekt fő funkcióját, hozzon létre egy új `TextAnalytics` objektum. Használja a megfelelő Azure-régióban a Szövegelemzés előfizetéshez. Például: `https://eastus.api.cognitive.microsoft.com`. Ha egy próbaverziós kulcsot használ, nem kell a hely frissítése.

```golang
//Replace 'eastus' with the correct region for your Text Analytics subscription
textAnalyticsClient := textanalytics.New("https://eastus.api.cognitive.microsoft.com")
```

Hozzon létre egy változót a kulcsot, és adja át azt a függvény `autorest.NewCognitiveServicesAuthorizer` amely majd átkerülnek az ügyfél `authorizer` tulajdonság.

```golang
subscriptionKey := "<<subscriptionKey>>"
textAnalyticsClient.Authorizer = autorest.NewCognitiveServicesAuthorizer(subscriptionKey)
```

## <a name="sentiment-analysis"></a>Hangulatelemzés

Hozzon létre egy új függvényt nevű `SentimentAnalysis()` az ügyfél korábban létrehozott paraméterrel. Hozzon létre egy `MultiLanguageInput` objektumokat, az elemezni kívánt dokumentumokat tartalmazó. Minden objektumot tartalmazza egy `id`, `Language` és a egy `text` attribútum. A `text` attribútumtárak elemezni, a szöveg `language` nyelve az a dokumentumot, és a `id` bármilyen érték lehet. 

```golang
func SentimentAnalysis(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("I had the best day of my life."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("1"),
            Text:StringPointer("This was a waste of my time. The speaker put me to sleep."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("2"),
            Text:StringPointer("No tengo dinero ni nada que dar..."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("it"),
            ID:StringPointer("3"),
            Text:StringPointer("L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanabban a függvényben hívja `textAnalyticsclient.Sentiment()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum-Azonosítót és véleménypontszámot. 0 közelebb pontszámot azt jelzi, hogy egy negatív véleményt jelölnek, míg 1 közelebb pontszámot azt jelzi, hogy egy pozitív véleményt.

```golang
result, _ := textAnalyticsclient.Sentiment(ctx, BoolPointer(false), &batchInput)
batchResult := textanalytics.SentimentBatchResult{}
jsonString, _ := json.Marshal(result.Value)
json.Unmarshal(jsonString, &batchResult)

// Printing sentiment results
for _,document := range *batchResult.Documents {
    fmt.Printf("Document ID: %s " , *document.ID)
    fmt.Printf("Sentiment Score: %f\n",*document.Score)
}

// Printing document errors
fmt.Println("Document Errors")
for _,error := range *batchResult.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

A projekt fő függvényben hívja `SentimentAnalysis()`.

### <a name="output"></a>Kimenet

```console
Document ID: 1 , Sentiment Score: 0.87
Document ID: 2 , Sentiment Score: 0.11
Document ID: 3 , Sentiment Score: 0.44
Document ID: 4 , Sentiment Score: 1.00
```

## <a name="language-detection"></a>Nyelvfelismerés

Hozzon létre egy új függvényt nevű `LanguageDetection()` az ügyfél korábban létrehozott paraméterrel. Hozzon létre egy `LanguageInput` objektumokat, az elemezni kívánt dokumentumokat tartalmazó. Minden objektumot tartalmazza egy `id` és a egy `text` attribútum. A `text` attribútumtárak elemezni, a szöveg és a `id` bármilyen érték lehet. 

```golang
func LanguageDetection(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.LanguageInput {
        textanalytics.LanguageInput {
            ID:StringPointer("0"),
            Text:StringPointer("This is a document written in English."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("1"),
            Text:StringPointer("Este es un document escrito en Español."),
        },
        textanalytics.LanguageInput {
            ID:StringPointer("2"),
            Text:StringPointer("这是一个用中文写的文件"),
        },
    }

    batchInput := textanalytics.LanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanabban a függvényben hívja `textAnalyticsclient.DetectLanguage()` és az eredményt kapja. Ezután Iterál végig az eredményeket, és kinyomtatja az egyes dokumentumok azonosítója és felismert nyelv.

```golang
result, _ := textAnalyticsclient.DetectLanguage(ctx, BoolPointer(false), &batchInput)

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
fmt.Println("Document Errors")
for _,error := range *result.Errors {
    fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
}
```

A projekt fő függvényben hívja `LanguageDetection()`.

### <a name="output"></a>Kimenet

```console
Document ID: 0 Detected Languages with Score: English 1.000000,
Document ID: 1 Detected Languages with Score: Spanish 1.000000,
Document ID: 2 Detected Languages with Score: Chinese_Simplified 1.000000,
```

## <a name="entity-recognition"></a>Entitások felismerése

Hozzon létre egy új függvényt nevű `ExtractEntities()` az ügyfél korábban létrehozott paraméterrel. Hozzon létre egy `MultiLanguageInput` objektumokat, az elemezni kívánt dokumentumokat tartalmazó. Minden objektumot tartalmazza egy `id`, `language`, és a egy `text` attribútum. A `text` attribútumtárak elemezni, a szöveg `language` nyelve az a dokumentumot, és a `id` bármilyen érték lehet. 

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("0"),
            Text:StringPointer("Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("1"),
            Text:StringPointer("La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanabban a függvényben `call textAnalyticsclient.Entities()` és az eredményt kapja. Majd újrafuttathatja – a eredményeket és nyomtatási, egyes dokumentumok azonosítója, és ki kell olvasni pontozása entitásokat.

```golang
    result, _ := textAnalyticsclient.Entities(ctx, BoolPointer(false), &batchInput)

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
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

A projekt fő függvényben hívja `ExtractEntities()`.

### <a name="output"></a>Kimenet

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

Document ID: 1
    Extracted Entities:
        Name: Microsoft Type: Organization
            Offset: 21  Length: 9   Score: 0.999756
        Name: Redmond (Washington)  Type: Location
            Offset: 60  Length: 7   Score: 0.991128
        Name: 21 kilómetros Type: Quantity  Sub-Type: Dimension

            Offset: 71  Length: 13  Score: 0.800000
        Name: Seattle   Type: Location
            Offset: 88  Length: 7   Score: 0.999878
```

## <a name="key-phrase-extraction"></a>A kulcsfontosságú kifejezések kinyerése

Hozzon létre egy új függvényt nevű `ExtractKeyPhrases()` az ügyfél korábban létrehozott paraméterrel. Hozzon létre egy `MultiLanguageInput` objektumokat, az elemezni kívánt dokumentumokat tartalmazó. Minden objektumot tartalmazza egy `id`, `language`, és a egy `text` attribútum. A `text` attribútumtárak elemezni, a szöveg `language` nyelve az a dokumentumot, és a `id` bármilyen érték lehet.

```golang
func ExtractKeyPhrases(textAnalyticsclient textanalytics.BaseClient) {

    ctx := context.Background()
    inputDocuments := []textanalytics.MultiLanguageInput {
        textanalytics.MultiLanguageInput {
            Language: StringPointer("ja"),
            ID:StringPointer("0"),
            Text:StringPointer("猫は幸せ"),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("de"),
            ID:StringPointer("1"),
            Text:StringPointer("Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("en"),
            ID:StringPointer("2"),
            Text:StringPointer("My cat might need to see a veterinarian."),
        },
        textanalytics.MultiLanguageInput {
            Language: StringPointer("es"),
            ID:StringPointer("3"),
            Text:StringPointer("A mi me encanta el fútbol!"),
        },
    }

    batchInput := textanalytics.MultiLanguageBatchInput{Documents:&inputDocuments}
}
```

Ugyanabban a függvényben textAnalyticsclient.KeyPhrases() hívja, és az eredményt kapja. Ezután Iterál végig az eredményeket, és nyomtassa ki minden egyes dokumentum azonosítója és kinyert kulcskifejezéseket.

```golang
    result, _ := textAnalyticsclient.KeyPhrases(ctx, BoolPointer(false), &batchInput)

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
    fmt.Println("Document Errors")
    for _,error := range *result.Errors {
        fmt.Printf("Document ID: %s Message : %s\n" ,*error.ID, *error.Message)
    }
```

A projekt fő függvényben hívja `ExtractKeyPhrases()`.

### <a name="output"></a>Kimenet

```console
Document ID: 0
    Extracted Key Phrases:
        幸せ

Document ID: 1
    Extracted Key Phrases:
        Stuttgart
        Hotel
        Fahrt
        Fu

Document ID: 2
    Extracted Key Phrases:
        cat
        veterinarian

Document ID: 3
    Extracted Key Phrases:
        fútbol
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még

 [Text Analytics áttekintése](../overview.md) [– gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
