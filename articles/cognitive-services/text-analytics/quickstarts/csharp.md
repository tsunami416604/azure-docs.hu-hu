---
title: Az Azure Cognitive Services Text Analytics API rövid útmutatóban a C# |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan használatának első lépései a szövegelemzési API-t a Microsoft Cognitive Services, Azure-ban.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: 94847adf761652a25fd3e2d594c7169776fefc89
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125125"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Szövegelemzési API-t C# Gyorsútmutató 
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan nyelv felismerése, vélemények elemzése és kinyerheti a kulcsfontosságú kifejezéseket, használja a [Text Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711) a C# használatával. A kód írása volt működik a .net Core-alkalmazást, minimális hivatkozó külső kódtáraiban, így Linux vagy MacOS rendszeren is futhat.

Tekintse meg a [API-definíciók](//go.microsoft.com/fwlink/?LinkID=759346) technikai dokumentációját az API-kat.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) a **Text Analytics API**. Használhatja a **összesen 5 000 tranzakció/hó ingyenes szintet** a rövid útmutató elvégzéséhez.

Rendelkeznie kell a [végpontját és hozzáférési kulcsát](../How-tos/text-analytics-how-to-access-key.md) , amely az Ön számára közben létrehozott jelentkezzen be. 


## <a name="install-the-nuget-sdk-package"></a>Az Nuget SDK-csomag telepítése
1. Hozzon létre egy új konzol megoldást a Visual Studióban.
1. A jobb gombbal a megoldásra, majd kattintson a **NuGet-csomagok kezelése megoldáshoz**
1. Mark az **tartalmaznak Prerelease** jelölőnégyzetet.
1. Válassza ki a **Tallózás** lapra, és keressen rá a **Microsoft.Azure.CognitiveServices.Language**
1. Válassza ki a Nuget-csomagot, és telepítse.

> [!Tip]
>  Miközben hívhatja a [HTTP-végpontokat](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) közvetlenül a C#, a Microsoft.Azure.CognitiveServices.Language SDK megkönnyíti a szolgáltatás hívása nélkül szerializálásához és deszerializálásához JSON aggódnia kellene.
>
> Néhány hasznos hivatkozásokat:
> - [SDK Nuget-oldalon](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.TextAnalytics)
> - [SDK-kód ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/TextAnalytics)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Az SDK-val a szövegelemzési API hívása
1. Cserélje le a program.cs fájlban az alábbi kódra. Ez a program a szövegelemzési API (nyelv kinyerés, kulcs-kifejezések kinyerése és hangulatelemzés) 3 szakaszokban képességeit mutatja be.
1. Cserélje le a `Ocp-Apim-Subscription-Key` Fejlécérték egy hozzáférési kulccsal rendelkező érvényes az előfizetéshez.
1. Cserélje le a hely a `client.BaseUri` a regisztrált a végponthoz. Az Azure Portal resource találja a végpont. A végpont általában a "https://[region].api.cognitive.microsoft.com/text/analytics/v2.0" tűnik.
1. Futtassa a programot.

```csharp
using System;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics;
using Microsoft.Azure.CognitiveServices.Language.TextAnalytics.Models;
using System.Collections.Generic;
using Microsoft.Rest;
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
            ITextAnalyticsClient client = new TextAnalyticsClient(new ApiKeyServiceClientCredentials());
            client.BaseUri = new Uri("https://westus.api.cognitive.microsoft.com/text/analytics/v2.0");

            Console.OutputEncoding = System.Text.Encoding.UTF8;

            // Extracting language
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

            // Getting key-phrases
            Console.WriteLine("\n\n===== KEY-PHRASE EXTRACTION ======");

            KeyPhraseBatchResult result2 = client.KeyPhrasesAsync(new MultiLanguageBatchInput(
                        new List<MultiLanguageInput>()
                        {
                          new MultiLanguageInput("ja", "1", "猫は幸せ"),
                          new MultiLanguageInput("de", "2", "Fahrt nach Stuttgart und dann zum Hotel zu Fu."),
                          new MultiLanguageInput("en", "3", "My cat is stiff as a rock."),
                          new MultiLanguageInput("es", "4", "A mi me encanta el fútbol!")
                        })).Result;

            // Printing keyphrases
            foreach (var document in result2.Documents)
            {
                Console.WriteLine("Document ID: {0} ", document.Id);

                Console.WriteLine("\t Key phrases:");

                foreach (string keyphrase in document.KeyPhrases)
                {
                    Console.WriteLine("\t\t" + keyphrase);
                }
            }

            // Extracting sentiment
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


            // Printing sentiment results
            foreach (var document in result3.Documents)
            {
                Console.WriteLine("Document ID: {0} , Sentiment Score: {1:0.00}", document.Id, document.Score);
            }
        }
    }
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Szövegelemzés a Power bi-ban](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [Text Analytics áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

