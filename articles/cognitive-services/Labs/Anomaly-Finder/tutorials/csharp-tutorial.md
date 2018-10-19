---
title: 'Oktatóanyag: Anomáliadetektálás a C# használatával'
titlesuffix: Azure Cognitive Services
description: Megismerkedhet egy C#-alkalmazással, amely az Anomaly Detection API-t használja. Ha az eredeti adatpontokat elküldi az API-nak, visszakapja a várt értéket és az attól eltérő pontokat.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.component: anomaly-detection
ms.topic: tutorial
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: f99ce765c1d9417fd5ca88b49214eca8a3b0bf49
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2018
ms.locfileid: "48887649"
---
# <a name="tutorial-anomaly-detection-with-c-application"></a>Oktatóanyag: Anomáliadetektálás C#-alkalmazásokkal

[!INCLUDE [PrivatePreviewNote](../../../../../includes/cognitive-services-anomaly-finder-private-preview-note.md)]

Megismerkedhet egy egyszerű Windows-alkalmazással, amely az Anomaly Detection API használatával észleli a bemenetben található rendellenességeket. A példa elküldi az idősoradatokat az Anomaly Detection API-nak az előfizetési kulccsal, és az API visszaküldi az összes rendellenes pontot és az elvárt értéket.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platformkövetelmények

A példa a [Visual Studio 2017 Community Editiont](https://www.visualstudio.com/products/visual-studio-community-vs) használó .NET keretrendszerhez lett kidolgozva. 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Előfizetés az anomáliadetektálás használatára és előfizetői azonosító beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>A példa letöltése és használata

Az anomáliadetektálási példaalkalmazást klónozhatja a gépére a [GitHubról](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>A példa telepítése

A GitHub Desktopon nyissa meg a Sample\AnomalyDetectionSample.sln fájlt.

<a name="Step2"></a>
### <a name="build-the-example"></a>A példa fordítása

Nyomja le a Ctrl+Shift+B billentyűkombinációt, vagy kattintson a menüszalag Fordítás elemére, majd a Megoldás fordítása elemre.

<a name="Step3"></a>
### <a name="run-the-example"></a>A példa futtatása

1. A fordítást követően nyomja le az **F5** billentyűt, vagy kattintson a menüszalag **Start** elemére a példa futtatásához.
2. Keresse meg a felhasználói felület anomáliadetektálási ablakát, amelynek a szöveges beviteli mezőjében a „{your_subscription_key}” (az ön előfizetési kulcsa) szöveg olvasható.
3. Cserélje le mintaadatokat tartalmazó request.json fájlt a saját adataira, majd kattintson a „Send” (Küldés) gombra. A Microsoft megkapja a feltöltött adatokat és észleli a bennük található rendellenességi pontokat. A feltöltött adatok nem lesznek megőrizve a Microsoft kiszolgálóján. A rendellenességi pont újbóli észleléséhez újra fel kell tölteni az adatokat.
4. Ha az adatok helyesek, a „Response” (Válasz) mezőben megjelenik az anomáliadetektálás eredménye. Ha valamilyen hiba történt, az azzal kapcsolatos információk szintén itt jelennek meg.

<a name="Review"></a>
### <a name="read-the-result"></a>Az eredmények értelmezése

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Áttekintés és tanulás

Most, hogy az alkalmazásunk fut, tekintsük át, hogyan integrálható a példaalkalmazás a Cognitive Services technológiával. Ez a lépés segítséget nyújt ezen alkalmazás továbbfejlesztéséhez, vagy saját alkalmazások összeállításához a Microsoft Anomaly Detection használatával.

A mintaalkalmazás az Anomaly Detection Restful API-végpontot használja.

A Restful API mintaalkalmazásban való használatának áttekintése érdekében nézzük meg közelebbről az **AnomalyDetectionClient.cs** egy kódrészletét. A fájlban a „KEY SAMPLE CODE STARTS HERE” (Itt kezdődik a kulcs mintakódja) és „KEY SAMPLE CODE ENDS HERE” (Itt végződik a kulcs mintakódja) megjegyzések jelzik az alábbiakban bemutatott kódrészletek helyét.

```csharp
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

```
### <a name="request"></a>**Kérés**
Az alábbi kódrészlet bemutatja, hogyan küldhető el a HttpClient segítségével az előfizetési kulcs és az adatpontok az Anomaly Detection API végpontjára.

```csharp
    public async Task<string> Request(string baseAddress, string endpoint, string subscriptionKey, string requestData)
    {
        using (HttpClient client = new HttpClient { BaseAddress = new Uri(baseAddress) })
        {
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Set http request header
            // ---------------------------------------------------------------------- 
            client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
            client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", subscriptionKey);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Build the request content
            // ---------------------------------------------------------------------- 
            var content = new StringContent(requestData, Encoding.UTF8, "application/json");
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------

            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // Send the request content with POST method.
            // ---------------------------------------------------------------------- 
            var res = await client.PostAsync(endpoint, content);
            // ----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------
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
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [REST API – referencia](https://dev.labs.cognitive.microsoft.com/docs/services/anomaly-detection/operations/post-anomalydetection)
