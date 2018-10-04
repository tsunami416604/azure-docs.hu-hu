---
title: Az Anomáliadetektálási kereső API használata a C# - a Microsoft Cognitive Services |} A Microsoft Docs
description: Get information és kód minták segítségével gyorsan használatának első lépései a C# és Anomáliadetektálási kereső API-val a Cognitive Services.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 3ae3d85e8e37fbf896405948f9bc7042bcdaca7b
ms.sourcegitcommit: 609c85e433150e7c27abd3b373d56ee9cf95179a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2018
ms.locfileid: "48246448"
---
# <a name="use-the-anomaly-finder-api-with-c"></a>A Anomáliaészlelő API használata a C# használatával

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Ez a cikk bemutatja, és kódminták segítségével gyorsan az Anomáliadetektálási kereső API-használatának C# a feladatnak az anomáliadetektálási eredményét idősorozat-adatok beolvasása.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="getting-anomaly-points-with-anomaly-finder-api-using-c"></a>Az Anomáliadetektálási kereső API-jával a C# anomáliadetektálási pontok beolvasása

[!INCLUDE [DataContract](../includes/datacontract.md)]

### <a name="example-of-time-series-data-points"></a>Adatsorozat adatpontjainak idő – példa

A példában az adatsorozat adatpontjainak idő a következőképpen történik.
[!INCLUDE [Request](../includes/request.md)]

### <a name="analyze-data-and-get-anomaly-points-c-example"></a>Adatok elemzése és anomáliadetektálási pontok C#-példa beolvasása

A példa lépései a következők.

1. Hozzon létre egy új konzol megoldást a Visual Studióban.
2. Cserélje le a program.cs fájlban a következő kódra, és adja hozzá a System.Net.Http mutató hivatkozást.
3. Cserélje le `[YOUR_SUBSCRIPTION_KEY]` értéke az érvényes előfizetési kulccsal végzett.
4. Cserélje le `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` az adatpontok együtt.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;

namespace Console
{
    class Program
    {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        // Replace the subscriptionKey string value with your valid subscription key.
        const string subscriptionKey = "[YOUR_SUBSCRIPTION_KEY]";

        const string endpoint = "https://api.labs.cognitive.microsoft.com/anomalyfinder/v1.0/anomalydetection";

        // Replace the request data with your real data.
        const string requestData = "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]";
        static void Main(string[] args)
        {
            var match = Regex.Match(endpoint, "(?<BaseAddress>https://[^/]+)(?<Url>/*.+)");
            if (match.Success)
            {
                var res = Request(
                    match.Groups["BaseAddress"].Value,
                    match.Groups["Url"].Value,
                    subscriptionKey,
                    requestData).Result;
                System.Console.Write(res);
            }
            else
            {
                System.Console.Write("Incorrect endpoint.");
            }
        }

        /// <summary>
        /// Call API to detect the anomaly points
        /// </summary>
        /// <param name="baseAddress">Base address of the API endpoint.</param>
        /// <param name="endpoint">The endpoint of the API</param>
        /// <param name="subscriptionKey">The subscription key applied  </param>
        /// <param name="requestData">The JSON string for requet data points</param>
        /// <returns>The JSON string for anomaly points and expected values.</returns>
        static async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
        {
            using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
            {
                client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
                client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);

                var content = new StringContent(requestData, Encoding.UTF8, "application/json");
                var res = await client.PostAsync(endpoint, content);
                if (res.IsSuccessStatusCode)
                {
                    return await res.Content.ReadAsStringAsync();
                }
                else
                {
                    return $"ErrorCode: {res.StatusCode}";
                }
            }
        }
    }
}

```

### <a name="example-response"></a>Példaválasz

A rendszer JSON formátumban adja vissza a sikeres választ. A példa a válasz a következőképpen történik.
[!INCLUDE [Response](../includes/response.md)]

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [C#-alkalmazás](../tutorials/csharp-tutorial.md)
