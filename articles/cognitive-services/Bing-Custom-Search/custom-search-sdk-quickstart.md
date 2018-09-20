---
title: A rövid útmutatóban egyéni keresési SDK C# |} A Microsoft Docs
titleSuffix: Cognitive Services
description: A telepítő egyéni keresési SDK C# konzolalkalmazást.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 6b41dfbde0c2af776ee2c35220f731e40de334a0
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46367848"
---
# <a name="custom-search-sdk-c-quickstart"></a>Custom Search SDK C# – rövid útmutató

A Bing Custom Search SDK tartalmazza a REST API entitáskeresés és elemzési eredmények funkcióit.

Az ehhez a mintához forráskódja elérhető a [Github](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).
## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Szeretne beállítani egy konzolalkalmazást, a Bing Custom Search SDK használatával, keresse meg a `Manage NuGet Packages` lehetőség a Visual Studio Megoldáskezelőjében. Vegye fel a `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot.

Telepíti a [NuGet egyéni keresés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag is telepíti a függőségeket, többek között az alábbi szerelvények:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Entity Search ügyfél

Hozzon létre egy példányt a CustomSearchAPI ügyfél, a Hozzáadás irányelvekkel:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Hozza létre az egyéni keresési ügyfél: csere `YOUR-CUSTOM-SEARCH-KEY` és `YOUR-CUSTOM-CONFIG-ID` a hozzáférési kulccsal és a jelenleg hozzárendelt azonosító API végpont-konfiguráció [saját példányok](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Az ügyfél használja a keresést a lekérdezés szövege:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Az eredmények elemzése

A `SearchAsync` metódus adja vissza egy `WebData` tartalmazó objektum `WebPages` találat a lekérdezéshez. Ez a kód megkeresi az első eredményt, és megszerzi az `Name` és `URL`.
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
 
Console.WriteLine("Searched for Query# \" Xbox \"");

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
## <a name="complete-console-application"></a>A teljes konzolalkalmazás

Az alábbi kód "Xbox" lekérdezés átvizsgálja, és kiírja `Name` és `URL` első webes eredmény.
```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

namespace CustomSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {

            var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

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
            }
            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}


```

## <a name="next-steps"></a>További lépések

[Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
