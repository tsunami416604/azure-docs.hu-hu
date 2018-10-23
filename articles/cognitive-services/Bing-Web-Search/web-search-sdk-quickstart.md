---
title: 'Rövid útmutató: A C#-hez készült Bing Web Search SDK használata'
titleSuffix: Azure Cognitive Services
description: A Bing Web Search SDK megkönnyíti a Bing Web Search integrálását a C#-alkalmazásába. Ebben a rövid útmutatóban elsajátíthatja az ügyfél-példányosítás, a kérésküldés és a válaszmegjelenítés módját.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: quickstart
ms.date: 08/16/2018
ms.author: erhopf
ms.openlocfilehash: 04a70c2afbd499d3965e23487cd49540b38e08af
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318656"
---
# <a name="quickstart-use-the-bing-web-search-sdk-for-c"></a>Rövid útmutató: A C#-hez készült Bing Web Search SDK használata

A Bing Web Search SDK megkönnyíti a Bing Web Search integrálását a C#-alkalmazásába. Ebben a rövid útmutatóban elsajátíthatja az ügyfél-példányosítás, a kérésküldés és a válaszmegjelenítés módját.

[!INCLUDE [bing-web-search-quickstart-signup](../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="prerequisites"></a>Előfeltételek

Az alábbi dolgokra szüksége lesz a rövid útmutató futtatásához:

* [Visual Studio](https://visualstudio.microsoft.com/downloads/) vagy
* [Visual Studio Code 2017](https://code.visualstudio.com/download)
  * [C# a Visual Studio Code-hoz](https://visualstudio.microsoft.com/downloads/)
  * [NuGet-csomagkezelő](https://github.com/jmrog/vscode-nuget-package-manager)
* [.NET Core SDK](https://www.microsoft.com/net/download)

## <a name="create-a-project-and-install-dependencies"></a>Projekt létrehozása és a függőségek telepítése

> [!TIP]
> Szerezze be a legújabb kódot Visual Studio-megoldásként a [GitHubról](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/).

Az első lépés egy új konzolprojekt létrehozása. Ha segítségre van szüksége a konzolprojekt létrehozásához, tekintse meg a [Hello World – Az első saját programom](https://docs.microsoft.com/dotnet/csharp/programming-guide/inside-a-program/hello-world-your-first-program) C#-programozási útmutatót. Ha a Bing Web Search SDK-t használná az alkalmazásában, a NuGet-csomagkezelővel telepítse a következőt: `Microsoft.Azure.CognitiveServices.Search.WebSearch`.

A [Web Search SDK-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0) a követezőket is telepíti:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="declare-dependencies"></a>Függőségek deklarálása

Nyissa meg a projektet a Visual Studióban vagy a Visual Studio Code-ban, és importálja a következő függőségeket:

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.CognitiveServices.Search.WebSearch;
using Microsoft.Azure.CognitiveServices.Search.WebSearch.Models;
using System.Linq;
```

## <a name="create-project-scaffolding"></a>Projektszerkezet létrehozása

Amikor létrehozta az új konzolprojektet, létre kellett jönnie egy névtérnek és egy osztálynak az alkalmazásához. A programnak a következőképp kell kinéznie:

```csharp
namespace WebSearchSDK
{
    class YOUR_PROGRAM
    {

        // The code in the following sections goes here.

    }
}
```

A következő szakaszban ebben az osztályban hozzuk létre a mintaalkalmazásunkat.

## <a name="construct-a-request"></a>Kérés létrehozása

Ez a kód hozza létre a keresési lekérdezést.

```csharp
public static void WebResults(WebSearchAPI client)
{
    try
    {
        var webData = client.Web.Search(query: "Yosemite National Park");
        Console.WriteLine("Searching for \"Yosemite National Park\"");

        // Code for handling responses is provided in the next section...

    }
    catch (Exception ex)
    {
        Console.WriteLine("Encountered exception. " + ex.Message);
    }
}
```

## <a name="handle-the-response"></a>A válasz kezelése

Most adjunk hozzá némi kódot a válasz elemzéséhez és az eredmények megjelenítéséhez. Az első weblap, kép, cikk és videó `name` és `url` értékét a rendszer megjeleníti, ha szerepel a válaszobjektumban.

```csharp
if (webData?.WebPages?.Value?.Count > 0)
{
    // find the first web page
    var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

    if (firstWebPagesResult != null)
    {
        Console.WriteLine("Webpage Results # {0}", webData.WebPages.Value.Count);
        Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
        Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any web pages...");
    }
}
else
{
    Console.WriteLine("Didn't find any web pages...");
}

/*
 * Images
 * If the search response contains images, the first result's name
 * and url are printed.
 */
if (webData?.Images?.Value?.Count > 0)
{
    // find the first image result
    var firstImageResult = webData.Images.Value.FirstOrDefault();

    if (firstImageResult != null)
    {
        Console.WriteLine("Image Results # {0}", webData.Images.Value.Count);
        Console.WriteLine("First Image result name: {0} ", firstImageResult.Name);
        Console.WriteLine("First Image result URL: {0} ", firstImageResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any images...");
    }
}
else
{
    Console.WriteLine("Didn't find any images...");
}

/*
 * News
 * If the search response contains news articles, the first result's name
 * and url are printed.
 */
if (webData?.News?.Value?.Count > 0)
{
    // find the first news result
    var firstNewsResult = webData.News.Value.FirstOrDefault();

    if (firstNewsResult != null)
    {
        Console.WriteLine("\r\nNews Results # {0}", webData.News.Value.Count);
        Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
        Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
    }
    else
    {
        Console.WriteLine("Didn't find any news articles...");
    }
}
else
{
    Console.WriteLine("Didn't find any news articles...");
}

/*
 * Videos
 * If the search response contains videos, the first result's name
 * and url are printed.
 */
if (webData?.Videos?.Value?.Count > 0)
{
    // find the first video result
    var firstVideoResult = webData.Videos.Value.FirstOrDefault();

    if (firstVideoResult != null)
    {
        Console.WriteLine("\r\nVideo Results # {0}", webData.Videos.Value.Count);
        Console.WriteLine("First Video result name: {0} ", firstVideoResult.Name);
        Console.WriteLine("First Video result URL: {0} ", firstVideoResult.ContentUrl);
    }
    else
    {
        Console.WriteLine("Didn't find any videos...");
    }
}
else
{
    Console.WriteLine("Didn't find any videos...");
}
```

## <a name="declare-the-main-method"></a>A fő metódus deklarálása

Ebben az alkalmazásban a fő metódusban szerepel az ügyfelet példányosító, a `subscriptionKey` azonosítót érvényesítő és a `WebResults` metódust meghívő kód. Folytatás előtt győződjön meg arról, hogy érvényes előfizetői azonosítót adott meg az Azure-fiókjához.

```csharp
static void Main(string[] args)
{
    var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

    WebResults(client);

    Console.WriteLine("Press any key to exit...");
    Console.ReadKey();
}
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást.

```console
dotnet run
```

## <a name="define-functions-and-filter-results"></a>Függvények definiálása és az eredmények szűrése

Most, hogy létrehozta az első hívást a Bing Web Search API-ra, tekintsünk meg néhány függvényt, amelyek jól példázzák az SDK lekérdezéseket pontosító és eredményeket szűrő funkcióját. Minden függvény hozzáadható az előző szakaszban létrehozott C#-alkalmazásához.

### <a name="limit-the-number-of-results-returned-by-bing"></a>A Bing által visszaadott eredmények számának korlátozása

Ebben a példában a `count` és az `offset` paramétert használjuk a „Best restaurants in Seattle” (Seattle legjobb éttermei) keresésre visszaadott eredmények számának korlátozására. Az első eredményhez tartozó `name` és `URL` értékét a rendszer megjeleníti.

1. Adja hozzá ezt a kódot a konzolprojekthez:
    ```csharp
    public static void WebResultsWithCountAndOffset(WebSearchAPI client)
    {
        try
        {
            var webData = client.Web.SearchAsync(query: "Best restaurants in Seattle", offset: 10, count: 20).Result;
            Console.WriteLine("\r\nSearching for \" Best restaurants in Seattle \"");

            if (webData?.WebPages?.Value?.Count > 0)
            {
                var firstWebPagesResult = webData.WebPages.Value.FirstOrDefault();

                if (firstWebPagesResult != null)
                {
                    Console.WriteLine("Web Results #{0}", webData.WebPages.Value.Count);
                    Console.WriteLine("First web page name: {0} ", firstWebPagesResult.Name);
                    Console.WriteLine("First web page URL: {0} ", firstWebPagesResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first web result!");
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
    }
    ```
2. A `main` metódushoz adja hozzá a következőt: `WebResultsWithCountAndOffset`.
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Futtassa az alkalmazást.

### <a name="filter-for-news"></a>Hírek szűrése

Ez a minta a `response_filter` paraméter segítségével szűri a keresési eredményeket. A visszaadott keresési eredmények a „Microsoft” kifejezésre visszaadott hírekre vannak korlátozva. Az első eredményhez tartozó `name` és `URL` értékét a rendszer megjeleníti.

1. Adja hozzá ezt a kódot a konzolprojekthez:
    ```csharp
    public static void WebSearchWithResponseFilter(WebSearchAPI client)
    {
        try
        {
            IList<string> responseFilterstrings = new List<string>() { "news" };
            var webData = client.Web.SearchAsync(query: "Microsoft", responseFilter: responseFilterstrings).Result;
            Console.WriteLine("\r\nSearching for \" Microsoft \" with response filter \"news\"");

            if (webData?.News?.Value?.Count > 0)
            {
                var firstNewsResult = webData.News.Value.FirstOrDefault();

                if (firstNewsResult != null)
                {
                    Console.WriteLine("News Results #{0}", webData.News.Value.Count);
                    Console.WriteLine("First news result name: {0} ", firstNewsResult.Name);
                    Console.WriteLine("First news result URL: {0} ", firstNewsResult.Url);
                }
                else
                {
                    Console.WriteLine("Couldn't find first News results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any News data..");
            }

        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. A `main` metódushoz adja hozzá a következőt: `WebResultsWithCountAndOffset`.
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Futtassa az alkalmazást.

### <a name="use-safe-search-answer-count-and-the-promote-filter"></a>A biztonságos keresés, a válaszszám és az előléptetés szűrő használata

Ez a példa az `answer_count`, a `promote` és a `safe_search` paraméter segítségével szűri a „Music Videos” (Zenei videók) kifejezés keresési eredményeit. A kód megjeleníti az első eredmény `name` és `URL` értékét.

1. Adja hozzá ezt a kódot a konzolprojekthez:
    ```csharp
    public static void WebSearchWithAnswerCountPromoteAndSafeSearch(WebSearchAPI client)
    {
        try
        {
            IList<string> promoteAnswertypeStrings = new List<string>() { "videos" };
            var webData = client.Web.SearchAsync(query: "Music Videos", answerCount: 2, promote: promoteAnswertypeStrings, safeSearch: SafeSearch.Strict).Result;
            Console.WriteLine("\r\nSearching for \"Music Videos\"");

            if (webData?.Videos?.Value?.Count > 0)
            {
                var firstVideosResult = webData.Videos.Value.FirstOrDefault();

                if (firstVideosResult != null)
                {
                    Console.WriteLine("Video Results # {0}", webData.Videos.Value.Count);
                    Console.WriteLine("First Video result name: {0} ", firstVideosResult.Name);
                    Console.WriteLine("First Video result URL: {0} ", firstVideosResult.ContentUrl);
                }
                else
                {
                    Console.WriteLine("Couldn't find videos results!");
                }
            }
            else
            {
                Console.WriteLine("Didn't see any data..");
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine("Encountered exception. " + ex.Message);
        }
    }
    ```
2. A `main` metódushoz adja hozzá a következőt: `WebResultsWithCountAndOffset`.
    ```csharp
    static void Main(string[] args)
    {
        var client = new WebSearchAPI(new ApiKeyServiceClientCredentials("YOUR_SUBSCRIPTION_KEY"));

        WebResults(client);
        // Search with count and offset...
        WebResultsWithCountAndOffset(client);  
        // Search with news filter...
        WebSearchWithResponseFilter(client);
        // Search with answer count, promote, and safe search
        WebSearchWithAnswerCountPromoteAndSafeSearch(client);

        Console.WriteLine("Press any key to exit...");
        Console.ReadKey();
    }
    ```
3. Futtassa az alkalmazást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett ezzel a projekttel, ne felejtse el eltávolítani az előfizetői azonosítót az alkalmazás kódjából.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Cognitive Services Node.js SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/)
