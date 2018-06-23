---
title: Az anomáliadetektálási észlelési C# alkalmazás – Microsoft kognitív szolgáltatások |} Microsoft Docs
description: Megismerkedhet a C# alkalmazás, amely az Anomáliadetektálási észlelési API kognitív Microsoft-szolgáltatásokban. Eredeti adatpontok küldeni API, és a várt érték és anomáliadetektálási pontok.
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 05/01/2018
ms.author: chliang
ms.openlocfilehash: 2e4100fd7d8e85a6b103c31000176aaaeb3d7151
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348191"
---
# <a name="anomaly-detection-c-application"></a>Az anomáliadetektálási észlelési C#-alkalmazás

Fedezze fel egy alapvető Windows-alkalmazás, amely Anomáliadetektálási észlelési API bemeneti rendellenességek észlelését. A példa az Anomáliadetektálási észlelési API adatsorozat időadatok elküldi az előfizetés kulccsal, majd az anomáliadetektálási pontok és a várt érték az egyes adatpontokban lekérdezi az API-n.

## <a name="prerequisites"></a>Előfeltételek

### <a name="platform-requirements"></a>Platform-követelmények

A példa kidolgozott a .NET-keretrendszer használatával [Visual Studio 2017, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs). 

### <a name="subscribe-to-anomaly-detection-and-get-a-subscription-key"></a>Az anomáliadetektálás szolgáltatásra, és egy előfizetés kulcs beszerzése 

[!INCLUDE [GetSubscriptionKey](../includes/get-subscription-key.md)]

## <a name="get-and-use-the-example"></a>Kérheti le és használja a példát

Átmásolhatja az Anomáliadetektálás a számítógépre a mintaalkalmazás [Github](https://github.com/MicrosoftAnomalyDetection/csharp-sample.git). 
<a name="Step1"></a>
### <a name="install-the-example"></a>A példa telepítése

Nyissa meg a GitHub asztali Sample\AnomalyDetectionSample.sln.

<a name="Step2"></a>
### <a name="build-the-example"></a>A példa összeállítása

Nyomja le a Ctrl + Shift + B, vagy a menüszalag menü Build, majd válassza ki a megoldás felépítéséhez.

<a name="Step3"></a>
### <a name="run-the-example"></a>A példa futtatásához

1. A rendszer a létrehozás után nyomja le az **F5** , vagy kattintson a **Start** a menüszalag menü a példa futtatásához.
2. Keresse meg az Anomáliadetektálás felhasználói felület ablakban az "{your_subscription_key}" olvasása szövegmezőben.
3. A request.json fájl, amely tartalmazza a mintaadatok lecserélése a saját adatait, majd kattintson a "Küldési" gombra. Microsoft megkapja az adatok feltöltése és bármely anomáliadetektálási pontok között ezután használhatja őket. Az adatok frissítése nem maradnak meg a Microsoft Server. Az anomáliadetektálási pont észleléséhez ebben az esetben meg kell feltölteni az adatokat még egyszer.
4. Ha az adatok helyes, az anomáliadetektálási észlelésének eredménye "Válasz" mezőben található. A hiba akkor fordul elő, ha a hiba adatait, valamint a válasz mezőben jelenik meg.

<a name="Review"></a>
### <a name="read-the-result"></a>Az eredmény olvasása

[!INCLUDE [diagrams](../includes/diagrams.md)]

<a name="Review"></a>
### <a name="review-and-learn"></a>Tekintse át, és ismerje meg

Most, hogy a futó alkalmazások, tekintsük át, hogyan a példa az alkalmazás integrálja az kognitív szolgáltatások technológiával. Ez a lépés könnyebben épület ezt az alkalmazást, vagy a saját Microsoft Anomáliadetektálás használó alkalmazás fejlesztése.

Ez például az alkalmazás lehetővé teszi az Anomáliadetektálási észlelési Restful API használata végpont.

Tekintse át a Restful API lekérdezi használatáról a mintaalkalmazás, vizsgáljuk meg egy kódrészletet a **AnomalyDetectionClient.cs**. A fájl "Kulcs minta kód KEZDŐDIK itt" és "Kulcs minta kód karakterlánccal végződik-e itt" hogy könnyebben megtalálja az alábbiakban bemutatott kódtöredékek kód jelző kód megjegyzéseket tartalmaz.

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
**Request(...)**  Kód alábbi kódrészletben láthatja, hogyan használható a HttlClient küldje el a Előfizetés kulcs és adatpontok a végpontnak a Anomáliadetektálási észlelési API.

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
