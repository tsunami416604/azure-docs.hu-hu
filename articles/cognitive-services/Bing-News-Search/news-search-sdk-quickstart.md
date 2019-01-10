---
title: 'Gyors útmutató: Hajtsa végre a hírkeresési – a Bing News Search SDK számáraC#'
titleSuffix: Azure Cognitive Services
description: Ez a rövid útmutató segítségével hírkeresés a Bing News Search SDK a Pythonhoz készült használatával, és a válasz feldolgozása.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: quickstart
ms.date: 01/30/2018
ms.author: v-gedod
ms.custom: seodec2018
ms.openlocfilehash: 5b3e68765fbcff12dcb5337aec38623b8994882c
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54156799"
---
# <a name="quickstart-perform-a-news-search-with-the-bing-news-search-sdk-for-c"></a>Gyors útmutató: Hajtsa végre egy hírkeresés a Bing News Search SDK esetében azC#

A Bing News Search SDK a REST API funkcióit biztosítja a hírek lekérdezéséhez és az eredmények elemzéséhez. 

A [C# Bing News Search SDK-minták forráskódja](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7/BingNewsSearch) elérhető a GitHubon.

## <a name="application-dependencies"></a>Alkalmazásfüggőségek
A [Cognitive Services hozzáférési kulcsát](https://azure.microsoft.com/try/cognitive-services/) a **Keresés** területen kérheti le.  Lásd még: [a Cognitive Services díjszabás – keresési Bing-API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).  

Ha a Bing News Search SDK-val szeretne beállítani egy konzolalkalmazást, keresse meg a `Manage NuGet Packages` lehetőséget a Visual Studióban, a Megoldáskezelőben.  Vegye fel a `Microsoft.Azure.CognitiveServices.Search.NewsSearch` csomagot.

A [NuGet News Search SDK-csomag](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.NewsSearch/1.2.0) telepítésekor függőségeket is telepít, például:
* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

## <a name="news-search-client"></a>News Search-ügyfél
Hozzon létre egy példányt, a `NewsSearchClient`, adja hozzá a direktíva használatával:
```
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

```
Ezután példányosítsa az ügyfelet:
```
var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));


```
Az ügyfél használatával keressen videókat lekérdezési szöveg alapján:
```
var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

```
Elemezze az előző lekérdezés eredményeiben visszaadott híreket:
```
if (newsResults.Value.Count > 0)
{
    var firstNewsResult = newsResults.Value[0];

    Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
    Console.WriteLine($"News result count: {newsResults.Value.Count}");
    Console.WriteLine($"First news name: {firstNewsResult.Name}");
    Console.WriteLine($"First news url: {firstNewsResult.Url}");
    Console.WriteLine($"First news description: {firstNewsResult.Description}");
    Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
    Console.WriteLine($"First news provider: {firstNewsResult.Provider[0].Name}");
}

else
{
    Console.WriteLine("Couldn't find news results!");
}

```
## <a name="complete-console-application"></a>A teljes konzolalkalmazás

A következő konzolalkalmazás végrehajtja az előzőleg meghatározott lekérdezést, és híreket keres a „Quantum Computing” kifejezésre. A kérés a `market` és a `count` paramétert tartalmazza. A kód ellenőrzi a találatok számát, és megjeleníti az első hírtalálat következő értékeit: `totalEstimatedMatches`, `name`, `url`, `description`, `published time` és a `provider` `name` értéke.

```
using System;
using System.Linq;
using Microsoft.Azure.CognitiveServices.Search.NewsSearch;

namespace NewsSrchSDK
{
    class Program
    {
        static void Main(string[] args)
        {
            var client = new NewsSearchClient(new ApiKeyServiceClientCredentials("YOUR-ACCESS-KEY"));

            try
            {
                var newsResults = client.News.SearchAsync(query: "Quantum  Computing", market: "en-us", count: 10).Result;
                Console.WriteLine("Search news for query \"Quantum  Computing\" with market and count");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"TotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }

            // Include these methods to run queries explained under headings following.
            // NewsSearchWithFilters(client);
            // NewsCategory(client);
            // TrendingTopics(client);

            Console.WriteLine("Any key to exit...");
            Console.ReadKey();
        }

    }
}

```
## <a name="recent-news-freshness-and-sortby-parameters"></a>Friss hírek, freshness és sortBy paraméterek
A következő kód megkeresi a legfrissebb híreket az „Artificial Intelligence” kifejezésre a `freshness` és a `sortBy` paraméterekkel. A kód ellenőrzi a találatok számát, és megjeleníti az első hírtalálat következő értékeit: `totalEstimatedMatches`, `name`, `url`, `description`, `published time` és a szolgáltató `name` értéke.
```
        public static void NewsSearchWithFilters(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.SearchAsync(query: "Artificial Intelligence", market: "en-us", freshness: "Week", sortBy: "Date").Result;
                Console.WriteLine("Search most recent news for query \"Artificial Intelligence\" with freshness and sortBy");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nTotalEstimatedMatches value: {newsResults.TotalEstimatedMatches}");
                        Console.WriteLine($"News result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="category-news-safe-search"></a>Kategóriák szerinti hírek, biztonságos keresés
A következő kód kategóriák szerint keres mozis vagy televíziós szórakozással kapcsolatos híreket biztonságos keresés használatával.  A kód ellenőrzi a találatok számát, és megjeleníti az első hírtalálat következő értékeit: `category`, `name`, `url`, `description`, `published time` és a szolgáltató `name` értéke.
```
        public static void NewsCategory(NewsSearchClient client)
        {
            try
            {
                var newsResults = client.News.CategoryAsync(category: "Entertainment_MovieAndTV", market: "en-us", safeSearch: "strict").Result;
                Console.WriteLine("Search category news for movie and TV entertainment with safe search");

                if (newsResults == null)
                {
                    Console.WriteLine("Didn't see any news result data..");
                }
                else
                {
                    if (newsResults.Value.Count > 0)
                    {
                        var firstNewsResult = newsResults.Value.First();

                        Console.WriteLine($"\r\nNews result count: {newsResults.Value.Count}");
                        Console.WriteLine($"First news category: {firstNewsResult.Category}");
                        Console.WriteLine($"First news name: {firstNewsResult.Name}");
                        Console.WriteLine($"First news url: {firstNewsResult.Url}");
                        Console.WriteLine($"First news description: {firstNewsResult.Description}");
                        Console.WriteLine($"First news published time: {firstNewsResult.DatePublished}");
                        Console.WriteLine($"First news provider: {firstNewsResult.Provider.First().Name}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news results!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```
## <a name="trending-topics"></a>Népszerű témakörök
A következő kód népszerű témakörök szerint keres híreket a Bingen. A kód ellenőrzi a találatok számát, és megjeleníti az első hírtalálat `name`, `text of query`, `webSearchUrl`, `newsSearchUrl` és `image.Url` értékét.
```
        public static void TrendingTopics(NewsSearchClient client)
        {
            try
            {
                var trendingTopics = client.News.TrendingAsync(market: "en-us").Result;
                Console.WriteLine("Search news trending topics in Bing");

                if (trendingTopics == null)
                {
                    Console.WriteLine("Didn't see any news trending topics..");
                }
                else
                {
                    if (trendingTopics.Value.Count > 0)
                    {
                        var firstTopic = trendingTopics.Value.First();

                        Console.WriteLine($"\r\nTrending topics count: {trendingTopics.Value.Count}");
                        Console.WriteLine($"First topic name: {firstTopic.Name}");
                        Console.WriteLine($"First topic query: {firstTopic.Query.Text}");
                        Console.WriteLine($"First topic image url: {firstTopic.Image.Url}");
                        Console.WriteLine($"First topic webSearchUrl: {firstTopic.WebSearchUrl}");
                        Console.WriteLine($"First topic newsSearchUrl: {firstTopic.NewsSearchUrl}");
                    }
                    else
                    {
                        Console.WriteLine("Couldn't find news trending topics!");
                    }
                }
            }

            catch (Exception ex)
            {
                Console.WriteLine("Encountered exception. " + ex.Message);
            }
        }

```

## <a name="next-steps"></a>További lépések

[Cognitive services .NET SDK-minták](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
