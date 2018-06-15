---
title: C# gyors üzembe helyezés az Azure kognitív szolgáltatások, Szövegelemzések API |} Microsoft Docs
description: Get információkat és a kód minták segítségével gyorsan használatának megkezdésében a szöveg Analytics API-t a Microsoft Azure kognitív Services.
services: cognitive-services
documentationcenter: ''
author: luiscabrer
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: article
ms.date: 09/20/2017
ms.author: ashmaka
ms.openlocfilehash: f46c5179fc245d84a72e038fe3870d2e6c990550
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "35349937"
---
# <a name="quickstart-for-text-analytics-api-with-c"></a>Szövegelemzések a C# API a gyors üzembe helyezés 
<a name="HOLTop"></a>

Ez a cikk bemutatja, hogyan észleli a nyelv, véleményeket elemzése és bontsa ki a legfontosabb kifejezések használata a [szöveg Analytics API-k](//go.microsoft.com/fwlink/?LinkID=759711) a C#. A kód írása volt működik a .net Core alkalmazás minimális hivatkozó külső szalagtárak, Linux vagy MacOS is futtathat.

Tekintse meg a [API-definíciók](//go.microsoft.com/fwlink/?LinkID=759346) az API-k műszaki dokumentációját.

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy [kognitív szolgáltatások API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) rendelkező **szöveg Analytics API**. Használhatja a **5000 tranzakciók/hónapban ingyenes szint** a gyors üzembe helyezés befejeződik.

Rendelkeznie kell a [végpont és a hozzáférési kulcsot](../How-tos/text-analytics-how-to-access-key.md) meg során létrehozott jelentkezzen be. 


## <a name="install-the-nuget-sdk-package"></a>Telepítse az Nuget SDK-csomagot
1. Új konzol megoldás létrehozása a Visual Studióban.
1. A megoldás, majd kattintson a jobb gombbal **NuGet-csomagok kezelése megoldáshoz**
1. Be van jelölve a **tartalmaznak Prerelease** jelölőnégyzetet.
1. Válassza ki a **Tallózás** lapot, és keresse meg **Microsoft.Azure.CognitiveServices.Language**
1. Válassza ki a Nuget-csomagot, és telepítse.

> [!Tip]
>  Amíg hívhatja a [HTTP-végpontokról](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) közvetlenül a C#, a Microsoft.Azure.CognitiveServices.Language SDK sokkal egyszerűbbé teszi a hívó nem kell aggódnia szerializálása és deszerializálása JSON.
>
> Néhány hasznos hivatkozások:
> - [SDK Nuget lap](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language)
> - [SDK-kód ](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Language/Language)


## <a name="call-the-text-analytics-api-using-the-sdk"></a>Az SDK használatával szöveg Analytics API
1. Cserélje le a program.cs fájlt az alábbi kódra. A program az a szöveg Analytics API (nyelvi kinyerési, kulcs-kifejezés kinyerésére és véleményeket elemzés) 3 szakaszokban képességeit mutatja be.
1. Cserélje le a `Ocp-Apim-Subscription-Key` Fejlécérték egy előfizetési érvényes elérési kulcsával.
1. Cserélje le a hely a `client.AzureRegion` (jelenleg `AzureRegions.Westus`) a regisztrált a régióban.
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
            ITextAnalyticsAPI client = new TextAnalyticsAPI(new ApiKeyServiceClientCredentials());
            client.AzureRegion = AzureRegions.Westus;

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
> [A Power BI Szövegelemzések](../tutorials/tutorial-power-bi-key-phrases.md)

## <a name="see-also"></a>Lásd még 

 [Szöveg elemzés áttekintése](../overview.md)  
 [Gyakori kérdések (GYIK)](../text-analytics-resource-faq.md)

