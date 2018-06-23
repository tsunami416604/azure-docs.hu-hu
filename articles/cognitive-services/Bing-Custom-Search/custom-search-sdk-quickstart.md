---
title: Egyéni gyorsindítási keresési SDK C# |} Microsoft Docs
titleSuffix: Cognitive Services
description: A telepítő egyéni keresési SDK C#-konzolalkalmazást.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 01/31/2018
ms.author: rosh
ms.openlocfilehash: 59b208b53bec974433c50c0e2304dc96bd9bd09e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35346899"
---
# <a name="custom-search-sdk-c-quickstart"></a>Egyéni keresési SDK C# első lépések

A Bing egyéni keresési SDK tartalmazza a REST API-t a entitás keresési és az eredmények értelmezése funkcióit.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek

Állítsa be a Konzolalkalmazás, a Bing egyéni keresési SDK használatával, keresse meg a `Manage NuGet Packages` lehetőséget a Visual Studio megoldáskezelőjében. Adja hozzá a `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomag.

Telepíti a [NuGet egyéni keresés](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag is telepíti a függőségek, beleértve a következő szerelvények:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="entity-search-client"></a>Entitás keresési ügyfél

A CustomSearchAPI ügyfél példány létrehozására, irányelvek segítségével hozzáadása:
```
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;

```

Az egyéni keresési ügyfél példányosítható: cserélje le `YOUR-CUSTOM-SEARCH-KEY` és `YOUR-CUSTOM-CONFIG-ID` végpont-konfiguráció API Azonosítót hozzárendelni, és a hozzáférési kulcsot [saját példányok](https://www.customsearch.ai/).
```
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));

```
Az ügyfél segítségével keresést a lekérdezés szövegének:
```
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;

```
## <a name="parse-the-results"></a>Az eredményeket elemezni

A `SearchAsync` metódus értéket ad vissza egy `WebData` tartalmazó objektum `WebPages` Ha a lekérdezés ilyet. Ez a kód első eredménye talál, és lekérdezi a `Name` és `URL`.
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
## <a name="complete-console-application"></a>Teljes Konzolalkalmazás

A következő kódot a lekérdezés "Xbox" keres, és kinyomtatja `Name` és `URL` az első webalkalmazás eredménye.
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

[Kognitív services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
