---
title: Anomáliadetektálási észlelési C#-alkalmazás – a Microsoft Cognitive Services |} A Microsoft Docs
description: A Microsoft Cognitive Services az Anomáliadetektáló API-t használó C# alkalmazás megismerése. Küldés eredeti adatpontok API-hoz és a várt érték és anomáliadetektálási pontokat.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 7d4f6a12c94620f447b5d6df4d7715d32eac2d98
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "41988792"
---
# <a name="anomaly-detection-c-application"></a>Rendellenességek észlelése C#-alkalmazás

Fedezze fel egy egyszerű Windows-alkalmazás, amely a bemeneti rendellenességek észlelése Anomáliadetektáló API-t használja. A példa az idősoros adatainak az Anomáliadetektáló API-t, elküldi az előfizetési kulccsal végzett, majd az anomáliadetektálási pontok és a várt érték az egyes lekérdezi az API-ból.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

A példában kidolgozott a .NET-keretrendszer használatával [Visual Studio 2017, a Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Fizessen elő a rendellenességek észlelése és a egy előfizetési kulcs lekérése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>És a példában a

Az Anomáliadetektálás mintaalkalmazás számítógépre klónozhat [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>A példában telepítése

Nyissa meg a GitHub Desktop Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>A példában létrehozása

Nyomja le a Ctrl + Shift + B, vagy a menüszalag menü hozhat létre, majd válassza ki a megoldás fordítása.

<a name="Step3"></a>
### <a name="run-the-example"></a>A példa Futtatás

1. A létrehozás befejezése után nyomja le az ENTER **F5** , vagy kattintson **Start** a menüszalag menü a példa futtatásához.
2. Keresse meg a rendellenességek észlelése felhasználói felület ablakot az a szöveges beviteli mezőbe olvasása "{your_subscription_key}".
3. A request.json fájl, amely tartalmazza a mintaadatokat, cserélje le a saját adataival, majd kattintson a "Küldés" gombra. A Microsoft kap az adatok feltöltéséhez, és ezek segítségével bármely anomáliadetektálási pontok között, majd észleléséhez. Töltse be az adatokat nem maradnak meg a Microsoft server. Észleli a anomáliadetektálási pont újra, kell feltöltheti az adatokat még egyszer.
4. Ha az adatok jó, az anomáliadetektálási az észlelés eredménye a "Response" mezőben található. Bármilyen hiba akkor fordul elő, ha a hiba adatait, valamint a válasz mező jelenik meg.

<a name="Review"></a>
### <a name="read-the-result"></a>Olvassa el az eredmény

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Tekintse át, és ismerje meg

Most, hogy a futó alkalmazást, tekintse át a PéldaAlkalmazás együttműködéséről a Cognitive Services-technológiát. Ebben a lépésben könnyebben továbbra is használhatja ezt az alkalmazást az alakzatot vagy a saját Microsoft Anomáliadetektálás használatával alkalmazás fejlesztése.

Ez a példa az alkalmazás lehetővé teszi, hogy használja az Anomáliadetektáló Restful API-végpont.

Tekintse át, hogyan lekérdezi a Restful API-t használja a mintaalkalmazás, nézzük meg a kódtöredék **AnomalyDetectionClient.cs**. A kód megjegyzéseket jelző "Kulcs minta kód ELINDÍTJA itt" és "Kulcs minta kódja ér VÉGET itt" hogy könnyebben megtalálja kódtöredékek az alábbiakban találja a kódot tartalmaz.

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
Az alábbi kódrészlet bemutatja, hogyan nyújt az előfizetési kulcs és az adatok pontok a végponthoz való az Anomáliadetektáló API-t, a HttpClient használatával.

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
