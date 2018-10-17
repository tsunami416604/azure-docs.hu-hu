---
title: 'Rövid útmutató: Mondathosszok lekérése – Translator Text API, C#'
titleSuffix: Azure Cognitive Services
description: Ebben a rövid útmutatóban kideríti egy szövegben a mondatok hosszát a Translator Text API és C# segítségével.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 4569a580dfdad3a71201e607ae5b7895d0d01099
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364346"
---
# <a name="quickstart-get-sentence-lengths-with-c35"></a>Rövid útmutató: Mondathosszok lekérése a C&#35; használatával

Ebben a rövid útmutatóban kideríti egy szövegben a mondatok hosszát a Translator Text API segítségével.

A minta forráskódja a [GitHubon](https://github.com/MicrosoftTranslator/Text-Translation-API-V3-C-Sharp) érhető el.

## <a name="prerequisites"></a>Előfeltételek

A kód Windows rendszeren történő futtatásához [Visual Studio 2017](https://www.visualstudio.com/downloads/) szükséges. (Az ingyenes Community Edition is elegendő.)

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="breaksentence-request"></a>BreakSentence kérés

A következő kód mondatokra tagolja a forrásszöveget a [BreakSentence](./reference/v3-0-break-sentence.md) metódussal.

1. Hozzon létre egy új C#-projektet a kedvenc IDE-jében.
2. Adja hozzá az alábbi kódot.
3. A `key` értéket cserélje le az előfizetéshez érvényes hozzáférési kulcsra.
4. Futtassa a programot.

```csharp
using System;
using System.Net.Http;
using System.Text;
// NOTE: Install the Newtonsoft.Json NuGet package.
using Newtonsoft.Json;

namespace TranslatorTextQuickStart
{
    class Program
    {
        static string host = "https://api.cognitive.microsofttranslator.com";
        static string path = "/breaksentence?api-version=3.0";

        static string uri = host + path;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        static string text = "How are you? I am fine. What did you do today?";

        async static void Break()
        {
            System.Object[] body = new System.Object[] { new { Text = text } };
            var requestBody = JsonConvert.SerializeObject(body);

            using (var client = new HttpClient())
            using (var request = new HttpRequestMessage())
            {
                request.Method = HttpMethod.Post;
                request.RequestUri = new Uri(uri);
                request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
                request.Headers.Add("Ocp-Apim-Subscription-Key", key);

                var response = await client.SendAsync(request);
                var responseBody = await response.Content.ReadAsStringAsync();
                var result = JsonConvert.SerializeObject(JsonConvert.DeserializeObject(responseBody), Formatting.Indented);

                Console.OutputEncoding = UnicodeEncoding.UTF8;
                Console.WriteLine(result);
            }
        }

        static void Main(string[] args)
        {
            Break();
            Console.ReadLine();
        }
    }
}
```

## <a name="breaksentence-response"></a>BreakSentence válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "detectedLanguage": {
      "language": "en",
      "score": 1.0
    },
    "sentLen": [
      13,
      11,
      22
    ]
  }
]
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és az átírást is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A C#-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=c%23)
