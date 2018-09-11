---
title: 'Gyors útmutató: C# használatával a szövegelemzési API meghívására |} A Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Információk és Kódminták a szövegelemzési API-val a Microsoft Cognitive Services, Azure-beli használatának gyors megkezdéséhez segítséget kaphat.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 08/30/2018
ms.author: ashmaka
ms.openlocfilehash: b4d945b7495897caf1f4edd1e909581614798a23
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44303021"
---
# <a name="quickstart-using-c-to-call-the-text-analytics-cognitive-service"></a>Gyors útmutató: C# használatával a Text Analytics kognitív szolgáltatás hívásához
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan nyelv felismerése, vélemények elemzése és kinyerheti a kulcskifejezéseket használatával a [Text Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711) a C# használatával. A kód írása volt működik a .NET Core-alkalmazást, minimális hivatkozó külső kódtáraiban, így a Linux vagy MacOS rendszeren is futtathatja.

Tekintse meg a [API-definíciók](//go.microsoft.com/fwlink/?LinkID=759346) technikai dokumentációját az API-kat.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a Text Analytics API-val. Használhatja a *összesen 5 000 tranzakció/hó ingyenes szintet* a rövid útmutató elvégzéséhez.

Rendelkeznie kell a [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) , amely jött létre az Ön számára a regisztrációhoz. 


## <a name="install-the-nuget-sdk-package"></a>Az NuGet SDK-csomag telepítése
1. Hozzon létre egy új konzol megoldást a Visual Studióban.
1. Kattintson a jobb gombbal a megoldás, és válassza ki **NuGet-csomagok kezelése megoldáshoz**.
1. Válassza ki a **tartalmaznak Prerelease** jelölőnégyzetet.
1. Válassza ki a **Tallózás** lapra, és keressen rá a **Microsoft.Azure.CognitiveServices.Language**.
1. Válassza ki a **Microsoft.Azure.CognitiveServices.Language.TextAnalytics** NuGet csomagot, és telepítse.

> [!Tip]
> Bár a segítségével meghívhatja a [HTTP-végpontokat](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) közvetlenül a C#, a Microsoft.Azure.CognitiveServices.Language SDK megkönnyíti a szolgáltatás hívása nélkül szerializálásához és deszerializálásához JSON aggódnia kellene.
>
> Hasznos hivatkozások a következők:
> - [SDK NuGet-oldalon](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-kód](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-by-using-the-sdk"></a>A szövegelemzési API hívása az SDK-val
1. Cserélje le a program.cs fájlban a következő kóddal. Ez a program a szövegelemzési API három szakaszra (nyelv kinyerés, kulcs-kifejezések kinyerése és hangulatelemzés) képességeit mutatja be.
1. Cserélje le a `Ocp-Apim-Subscription-Key` fejléc értéke érvényes az előfizetéshez tartozó hozzáférési kulccsal.
1. Cserélje le a hely a `Endpoint` a végpontra a bejelentkezéshez. Az Azure-portál erőforrás találja a végpont. A végpont általában kezdődik "https://[region].api.cognitive.microsoft.com." Csak protokoll és a gazdagépcsoport nevét tartalmazza.
1. Futtassa a programot.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
using System.Linq;
using System.Net.Http;
using System.Threading;
using System.Threading.Tasks;

namespace ConsoleApp1
{
    class Program
    {
        /// <summary>
        /// Container for subscription credentials. Make sure to enter your valid key.
        /// </summary>
        class ApiKeyServiceClientCredentials : ServiceClientCredentials
        {
            public override Task ProcessHttpRequestAsync(HttpRequestMessage request, CancellationToken cancellationToken)
            {
                request.Headers.Add("Ocp-Apim-Subscription-Key", "ENTER KEY HERE");
                return base.ProcessHttpRequestAsync(request, cancellationToken);
            }
        }

        static void Main(string[] args)
        {

            // Create a client.
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials())
            {
                Endpoint = "https://westus.api.cognitive.microsoft.com"
            };

            Console.OutputEncoding = System.Text.Encoding.UTF8;
```

## <a name="detect-language"></a>Nyelv felismerése

A nyelvi API-t észleli a szöveg nyelvének dokumentálja, használja a [nyelv észlelése metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7).

```csharp
            // Extracting language.
            Console.WriteLine("===== LANGUAGE EXTRACTION ======");

            var result =  client.DetectLanguageAsync(new BatchInput(
                    new List<Input>()
                        {
                          new Input("1", "This is a document written in English."),
                          new Input("2", "Este es un document escrito en Español."),
                          new Input("3", "这是一个用中文写的文件")
                    })).Result;

            // Printing language results.
            foreach (var document in result.Documents)
            {
                Console.WriteLine("Document ID: {0} , Language: {1}", document.Id, document.DetectedLanguages[0].Name);
            }
```

## <a name="extract-key-phrases"></a>Kulcsszavak kinyerése

A Key kifejezés kinyerése API – a kulcskifejezések kigyűjti a szöveges dokumentum használata a [Kulcskifejezések metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6).

```csharp
            // Getting key phrases.
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing key phrases.
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }
```

## <a name="analyze-sentiment"></a>Vélemények elemzése

Használatával észleli a Sentiment Analysis API szöveg rekordkészlet, jelöli a [vélemények metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9).

```csharp
            // Analyzing sentiment.
            Console.WriteLine("\n\n===== SENTIMENT ANALYSIS ======");

            SentimentBatchResult result3 = client.SentimentAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("en", "0", "I had the best day of my life."),
                          new MultiLanguageInput("en", "1", "This was a waste of my time. The speaker put me to sleep."),
                          new MultiLanguageInput("es", "2", "No tengo dinero ni nada que dar..."),
                          new MultiLanguageInput("it", "3", "L'hotel veneziano era meraviglioso. È un bellissimo pezzo di architettura."),
                        })).Result;


            // Printing sentiment results.
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
```

## <a name="identify-linked-entities"></a>Kapcsolt entitások azonosítása

Az Entitáskapcsolási API azonosítja a jól ismert entitások egy szöveges dokumentum használata a [Entitáskapcsolás metódus](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634).

```csharp
            // Linking entities
            Console.WriteLine("\n\n===== ENTITY LINKING ======");

            EntitiesBatchResult result4 = client.EntitiesAsync(
                    new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                            new MultiLanguageInput("en", "0", "I really enjoy the new XBox One S. It has a clean look, it has 4K/HDR resolution and it is affordable."),
                            new MultiLanguageInput("en", "1", "The Seattle Seahawks won the Super Bowl in 2014."),
                        })).Result;

            // Printing entity results.
            foreach (var document in result4.Documents)
            {
                Console.WriteLine("Document ID: {0} , Entities: {1}", document.Id, String.Join(", ", document.Entities.Select(entity => entity.Name)));
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power BI-jal](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)
