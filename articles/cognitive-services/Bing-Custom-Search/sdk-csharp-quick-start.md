---
title: A rövid útmutatóban egyéni keresési SDK C# |} A Microsoft Docs
titleSuffix: Cognitive Services
description: A telepítő egyéni keresési SDK C# konzolalkalmazást.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6c9917e3a63515f36b386e444edcc53de07799fc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46949927"
---
# <a name="c-sdk-quickstart"></a>C# SDK-t a rövid útmutató

A Bing Custom Search SDK biztosít egy egyszerűbb programozási modellhez, a Bing Custom Search REST API-nál. Ez a szakasz végigvezeti az első egyéni keresés hívások, a C# SDK-val.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész egyéni keresési példány. Lásd: [az első Bing Custom Search-példány létrehozása](quick-start.md).  
  
- Egy előfizetési kulcsot. Is kap egy előfizetési kulcsot, aktiválásakor a [az ingyenes próbaidőszak](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), vagy használhat egy díjköteles előfizetési kulcsot az Azure irányítópultján (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- A Visual Studio 2017 telepítése. Ha még nem rendelkezik azt, akkor letöltheti a **ingyenes** [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).  
  
- A [NuGet egyéni keresés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag telepítve van. A Visual Studio Megoldáskezelőjében, kattintson a jobb gombbal a projekt, és válassza a `Manage NuGet Packages` a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot.

Az alábbi szerelvényeket is az egyéni keresés NuGet-csomag telepítése telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>A kód futtatása

Ez a példa futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio 2017-ben.
  
2. Kattintson a **fájl** menüben kattintson a **új**, **projekt**, majd a **Visual C# Konzolalkalmazást** sablont.
  
3. Nevezze el a megoldás CustomSearchSolution, és keresse meg a mappa, akkor.
  
4. Kattintson az OK gombra a megoldás létrehozásához.  
  
4. A Megoldáskezelőben, kattintson a jobb gombbal a projektre (melynek neve megegyezik a megoldás), és válassza ki `Manage NuGet Packages`. Kattintson a **Tallózás** a NuGet Package Manager. Adja meg a Microsoft.Azure.CognitiveServices.Search.CustomSearch a keresési mezőbe, majd nyomja le az enter. Válassza ki a csomagot, és kattintson a telepítés gombra.  
  
4. Másolja az alábbi kódot a Program.cs fájlba. Cserélje le **YOUR-SUBSCRIPTION-KEY** és **YOUR-CUSTOM-CONFIG-ID** az előfizetési kulcs és a konfiguráció azonosítója.  
  
    ```csharp
    using System;
    using System.Linq;
    using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

    namespace CustomSrchSDK
    {
        class Program
        {
            static void Main(string[] args)
            {

                var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-SUBSCRIPTION-KEY"));

                try
                {
                    // This will look up a single query (Xbox).
                    var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
                    Console.WriteLine("Searched for Query# \" Xbox \"");

                    //WebPages
                    if (webData?.WebPages?.Value?.Count > 0)
                    {
                        // find the first web page
                        var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                        if (firstWebPagesResult != null)
                        {
                            Console.WriteLine("Number of webpage results {0}", webData.WebPages.Value.Count);
                            Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                            Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                        }
                        else
                        {
                            Console.WriteLine("Couldn't find web results!");
                        }
                    }
                    else
                    {
                        Console.WriteLine("Didn't see any Web data..");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine("Encountered exception. " + ex.Message);
                }

                Console.WriteLine("Press any key to exit...");
                Console.ReadKey();
            }

        }
    }
    ```  
  
5. Összeállítását és futtatását (Hibakeresés) a megoldás. 




## <a name="breaking-it-down"></a>Parancsban történt használhatatlanná

A NuGet Custom Search-csomag telepítése után kell hozzáadnia egy azt direktívát.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Következő lépésként hozza létre az egyéni keresési ügyfelet, amely használja, hogy a keresési kérésekben. Ne felejtse el `YOUR-SUBSCRIPTION-KEY` a kulccsal.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Most már használhatja az ügyfél egy keresési kérelmet küldeni. Ne felejtse el a `YOUR-CUSTOM-CONFIG-ID` a példány konfigurációs azonosítóval (az azonosítója annak az [Custom Search portál](https://www.customsearch.ai/)). Ebben a példában az Xbox keres. Frissítés szükséges.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

A `SearchAsync` metódus adja vissza egy `WebData` objektum. Használat `WebData` keresztül bármely újrafuttathatja `WebPages` találhatók, amelyek. Ez a kód az első weblap eredmény megkeresi és jelenít meg a képernyőn látható weblapon `Name` és `URL`.

```csharp
//WebPages
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results#{0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Couldn't find web results!");
    }
}
else
{
    Console.WriteLine("Didn't see any Web data..");
}

```


## <a name="next-steps"></a>További lépések

Tekintse meg az SDK minták. Mindegyik minta egy információs fájl, az Előfeltételek és a minták telepítése/futó részleteit tartalmazza.

* Ismerkedés a [.NET-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * Lásd még: [.NET-kódtárakra](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch) definíciókat és függőségeit.
* Ismerkedés a [NodeJS-minták](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * Lásd még: [NodeJS-kódtárak](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch) definíciókat és függőségeit.
* Ismerkedés a [Java-példák](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * Lásd még: [Java-kódtárak](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch) definíciókat és függőségeit.
* Ismerkedés a [példák Python programnyelvhez](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * Lásd még: [Python-kódtárakat](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch) definíciókat és függőségeit.

