---
title: 'Translator Text: Szövegátalakítási szkript a C# használatával | Microsoft Docs'
titleSuffix: Microsoft Cognitive Services
description: Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API segítségével és a C# használatával a Cognitive Servicesben.
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: translator-text
ms.topic: quickstart
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: 66d649c0015be8c6a74e9925af68297334bfdb30
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "43769500"
---
# <a name="quickstart-transliterate-text-with-c35"></a>Rövid útmutató: Szöveg átírása a C&#35; használatával

Ebben a rövid útmutatóban szöveget alakít át egy nyelven egy szkriptből egy másikba a Translator Text API segítségével.

## <a name="prerequisites"></a>Előfeltételek

A kód Windows rendszeren történő futtatásához [Visual Studio 2017](https://www.visualstudio.com/downloads/) szükséges. (Az ingyenes Community Edition is elegendő.)

A Translator Text API használatához szüksége van egy előfizetési kulcsra is. Lásd [a Translator Text API regisztrációját](translator-text-how-to-signup.md).

## <a name="transliterate-request"></a>Transliterate kérés

A következő egy szöveget alakít át egy nyelven egy szkriptből egy másik szkriptbe a [Transliterate](./reference/v3-0-transliterate.md) metódussal.

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
        static string path = "/transliterate?api-version=3.0";
        // Transliterate text in Japanese from Japanese script (i.e. Hiragana/Katakana/Kanji) to Latin script.
        static string params_ = "&language=ja&fromScript=jpan&toScript=latn";

        static string uri = host + path + params_;

        // NOTE: Replace this example key with a valid subscription key.
        static string key = "ENTER KEY HERE";

        // Transliterate "good afternoon".
        static string text = "こんにちは";

        async static void Transliterate()
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
            Transliterate();
            Console.ReadLine();
        }
    }
}
```

## <a name="transliterate-response"></a>Transliterate válasz

A rendszer JSON formátumban ad vissza egy sikeres választ a következő példában látható módon:

```json
[
  {
    "text": "konnnichiha",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>További lépések

A GitHubon megismerheti a rövid útmutató és egyebek mintakódját, beleértve a fordítást és a nyelvfelismerést is, valamint más Translator Text-projekteket.

> [!div class="nextstepaction"]
> [A C#-példák megismerése a GitHubon](https://aka.ms/TranslatorGitHub?type=&language=c%23)
