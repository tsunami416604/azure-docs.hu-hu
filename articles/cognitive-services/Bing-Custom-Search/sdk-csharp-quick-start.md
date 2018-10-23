---
title: 'Rövid útmutató: Custom Search SDK, C#'
titleSuffix: Azure Cognitive Services
description: Telepítse a Custom Search SDK C#-konzolalkalmazást.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 09/06/2018
ms.author: scottwhi
ms.openlocfilehash: 6dc10bc2dedfe99573b5ad646461e66827c6df9e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49320169"
---
# <a name="quickstart-using-the-bing-custom-search-sdk-with-c"></a>Első lépések: A Bing Custom Search SDK használata C#-pal

A Bing Custom Search SDK a Bing Custom Search REST API-nál egyszerűbb programozási modellt biztosít. Ebből a szakaszból megtudhatja, hogyan készítse el első Custom Search hívásait a C# SDK-val.

## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez a következőkre lesz szüksége:

- Egy használatra kész Custom Search-példány. Lásd: [Az első Bing Custom Search-példánya létrehozása](quick-start.md).  
  
- Egy előfizetői kulcs. Használhatja az [ingyenes próbaverzió](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) aktiválásakor kapott előfizetői azonosítót, vagy egy fizetős előfizetői azonosítót az Azure-irányítópultról (lásd: [Cognitive Services API-fiók](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).  
  
- Telepített Visual Studio 2017 telepítve. Ha még nincs telepítve, letöltheti az **ingyenes** [Visual Studio 2017 Community Editiont](https://www.visualstudio.com/downloads/).  
  
- Telepített [NuGet Custom Search](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0) csomag. A Visual Studio Megoldáskezelőjében kattintson a jobb gombbal a projektre, és válassza a `Manage NuGet Packages` lehetőséget a menüből. Telepítse az `Microsoft.Azure.CognitiveServices.Search.CustomSearch` csomagot.

A NuGet Custom Search csomag telepítésekor a következő szerelvények is települnek:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json



## <a name="run-the-code"></a>A kód futtatása

> [!TIP]
> Szerezze be a legújabb kódot Visual Studio-megoldásként a [GitHubról](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingCustomWebSearch).

A példa futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a Visual Studio 2017-et.
  
2. Kattintson a **File** (Fájl) menüre, a **New** (Új), a **Project** (Projekt) elemre, majd a **Visual C# Console Application** (Visual C#-konzolalkalmazás) sablonra.
  
3. A megoldásnak adja CustomSearchSolution nevet, majd tallózzon ahhoz a mappához, amelyben tárolni szeretné.
  
4. A megoldás létrehozásához kattintson az OK gombra.  
  
4. A Megoldáskezelőben kattintson a jobb gombbal a projektre (ugyanaz a neve, mint a megoldásnak), és válassza a `Manage NuGet Packages` lehetőséget. A NuGet-csomagkezelőben kattintson a **Browse** (Tallózás) elemre. A keresőmezőbe írja be a Microsoft.Azure.CognitiveServices.Search.CustomSearch kifejezést, majd nyomja le az Enter billentyűt. Válassza ki a csomagot, és kattintson az Install (Telepítés) elemre.  
  
4. Másolja a következő kódot a Program.cs fájlba. A **YOUR-SUBSCRIPTION-KEY** (előfizetői azonosító) és a **YOUR-CUSTOM-CONFIG-ID** (konfigurációs azonosító) helyére írja be saját adatait.  
  
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
  
5. Állítsa össze és futtassa az alkalmazást (indítsa el a hibakeresést). 




## <a name="breaking-it-down"></a>Részletes áttekintés

A NuGet Custom Search-csomag telepítése után hozzá kell adnia egy használati direktívát.

```csharp
using Microsoft.Azure.CognitiveServices.Search.CustomSearch;
```

Következő lépésként példányosítsa az egyéni keresési ügyfelet, amelyet a keresési kérésekhez használ. Ne felejtse el lecserélni a `YOUR-SUBSCRIPTION-KEY` sztringet a kulcsára.

```csharp
var client = new CustomSearchAPI(new ApiKeyServiceClientCredentials("YOUR-CUSTOM-SEARCH-KEY"));
```

Most már keresési kérést küldhet az ügyféllel. Ne felejtse el lecserélni a `YOUR-CUSTOM-CONFIG-ID` sztringet a példánya konfigurációazonosítójára (az azonosítót a [Custom Search portálon](https://www.customsearch.ai/) találja). A példában az Xbox szóra kerestünk rá. Módosítsa szükség szerint.

```csharp
var webData = client.CustomInstance.SearchAsync(query: "Xbox", customConfig: Int32.Parse("YOUR-CUSTOM-CONFIG-ID")).Result;
```

A `SearchAsync` metódus egy `WebData` objektumot ad vissza. A `WebData` segítségével lépkedjen végig az összes talált `WebPages` elemen. Ez a kód megtalálja az első weboldal eredményt, és megjeleníti a weboldal `Name` és `URL` tulajdonságát.

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

Nézze át az SDK-mintákat. Minden mintában található egy ReadMe-fájl, amely a minták előfeltételeivel és a telepítésükkel/futtatásukkal kapcsolatos információkat tartalmaz.

* Ismerkedés a [.NET-mintákkal](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7) 
    * [NuGet-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.CustomSearch/1.2.0)
    * További definíciókat és függőségeket találhat a [.NET-kódtárakban](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/CognitiveServices/dataPlane/Search/BingCustomSearch).
* Ismerkedés a [NodeJS-mintákkal](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples) 
    * További definíciókat és függőségeket találhat a [NodeJS-kódtárakban](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/customSearch).
* Ismerkedés a [Java-mintákkal](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples) 
    * További definíciókat és függőségeket találhat a [Java-kódtárakban](https://github.com/Azure/azure-sdk-for-java/tree/master/cognitiveservices/azure-customsearch).
* Ismerkedés a [Python-mintákkal](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples) 
    * További definíciókat és függőségeket találhat a [Python-kódtárakban](https://github.com/Azure/azure-sdk-for-python/tree/master/azure-cognitiveservices-search-customsearch).

