---
title: Rangsorolás használata a keresési eredmények megjelenítéséhez
titleSuffix: Azure Cognitive Services
description: Bemutatja, hogyan jelenítheti meg a keresési eredmények rangsorolási sorrendjében a Bing RankingResponse választ.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: tutorial
ms.date: 12/19/2019
ms.author: aahi
ms.openlocfilehash: 1c8e0bb136fddeb84dc991e63a761378b38cc470
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75382327"
---
# <a name="build-a-console-app-search-client-in-c"></a>Konzolalkalmazás-kereső ügyfél létrehozása C-ben #

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy egyszerű .NET Core konzolalkalmazást, amely lehetővé teszi a felhasználók számára a Bing Web Search API lekérdezését és a rangsorolt eredmények megjelenítését.

Ez az oktatóanyag bemutatja, hogyan:

- Egyszerű lekérdezés a Bing Web Search API-ra
- Lekérdezési eredmények megjelenítése rangsorolt sorrendben

## <a name="prerequisites"></a>Előfeltételek

Követni együtt a bemutató, meg kell:

- Visual Studio. Ha még nincs meg, [töltse le és telepítse az ingyenes Visual Studio 2017 Community Edition alkalmazást.](https://www.visualstudio.com/downloads/)
- A Bing Web Search API előfizetési kulcsa. Ha nem rendelkezik ilyennel, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Új konzolalkalmazás-projekt létrehozása

Hozzon létre egy projektet a Visual Studióban a `Ctrl`+`Shift`+`N` billentyűparancs lenyomásával.

Az **Új projekt** párbeszédpanelen kattintson a **Visual C# > Windows Classic Desktop > Console App (.NET Framework)** elemre.

Nevezze el az alkalmazást **MyConsoleSearchApp**néven, majd kattintson **az OK gombra.**

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>A JSON.net Nuget csomag hozzáadása a projekthez

JSON.net lehetővé teszi az API által visszaadott JSON-válaszok. Adja hozzá NuGet csomagját a projekthez:

- A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza **a NuGet csomagok kezelése...** lehetőséget.
- A **Tallózás** lapon `Newtonsoft.Json`keresse meg a keresett gombot. Válassza ki a legújabb verziót, majd kattintson a **Telepítés gombra.**
- Kattintson az **OK** gombra a **Változások áttekintése** ablakban.
- Zárja be a Visual Studio **nuget: MyConsoleSearchApp**című lapját.

## <a name="add-a-reference-to-systemweb"></a>Hivatkozás hozzáadása a System.Web fájlhoz

Ez az oktatóanyag `System.Web` a szerelvényre támaszkodik. Hivatkozás hozzáadása ehhez a szerelvényhez a projekthez:

- A **Megoldáskezelőben**kattintson a jobb gombbal a **Hivatkozások** elemre, és válassza a Hivatkozás **hozzáadása parancsot...**
- Válassza **az Összeállítások > Framework**lehetőséget, majd görgessen le, és ellenőrizze a **System.Web**
- Kattintson az **OK** gombra.

## <a name="add-some-necessary-using-statements"></a>Néhány szükséges hozzáadása utasítások használatával

Az oktatóanyagban szereplő kód három további utasítást igényel. Adja hozzá ezeket `using` a nyilatkozatokat a meglévő utasítások alá **a Program.cs**tetején:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Lekérdezés lekérdezésének lekérdezése a felhasználótól

A **Megoldáskezelőben**nyissa meg **a Program.cs.** Frissítse `Main()` a módszert:

```csharp
static void Main()
{
    // Get the user's query
    Console.Write("Enter Bing query: ");
    string userQuery = Console.ReadLine();
    Console.WriteLine();

    // Run the query and display the results
    RunQueryAndDisplayResults(userQuery);

    // Prevent the console window from closing immediately
    Console.WriteLine("\nHit ENTER to exit...");
    Console.ReadLine();
}
```

Ez a módszer:

- Lekérdezést kér a felhasználótól
- A `RunQueryAndDisplayResults(userQuery)` lekérdezés végrehajtására és az eredmények megjelenítésére irányuló hívások
- Megvárja a felhasználói bevitelt, hogy megakadályozza a konzolablak azonnali bezárását.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Lekérdezési eredmények keresése a Bing Web Search API használatával

Ezután adjon hozzá egy metódust, amely lekérdezi az API-t, és megjeleníti az eredményeket:

```csharp
static void RunQueryAndDisplayResults(string userQuery)
{
    try
    {
        // Create a query
        var client = new HttpClient();
        client.DefaultRequestHeaders.Add("Ocp-Apim-Subscription-Key", "<YOUR_SUBSCRIPTION_KEY_GOES_HERE>");
        var queryString = HttpUtility.ParseQueryString(string.Empty);
        queryString["q"] = userQuery;
        var query = "https://api.cognitive.microsoft.com/bing/v7.0/search?" + queryString;

        // Run the query
        HttpResponseMessage httpResponseMessage = client.GetAsync(query).Result;

        // Deserialize the response content
        var responseContentString = httpResponseMessage.Content.ReadAsStringAsync().Result;
        Newtonsoft.Json.Linq.JObject responseObjects = Newtonsoft.Json.Linq.JObject.Parse(responseContentString);

        // Handle success and error codes
        if (httpResponseMessage.IsSuccessStatusCode)
        {
            DisplayAllRankedResults(responseObjects);
        }
        else
        {
            Console.WriteLine($"HTTP error status code: {httpResponseMessage.StatusCode.ToString()}");
        }
    }
    catch (Exception e)
    {
        Console.WriteLine(e.Message);
    }
}
```

Ez a módszer:

- Létrehoz `HttpClient` egy lekérdezést a Web Search API-t
- Beállítja a HTTP-fejlécet, amelyet a `Ocp-Apim-Subscription-Key` Bing a kérelem hitelesítésére használ
- Végrehajtja a kérést, és JSON.net az eredmények deszerializálására használja.
- Hívások `DisplayAllRankedResults(responseObjects)` az összes eredmény rangsorolt sorrendben való megjelenítéséhez

Győződjön meg arról, hogy az előfizetési kulcs értékét állítja `Ocp-Apim-Subscription-Key` be.

## <a name="display-ranked-results"></a>Rangsorolt eredmények megjelenítése

Mielőtt megmutatna, hogyan jelenítheti meg az eredményeket rangsorolt sorrendben, tekintse meg a mintainternetes keresési válaszát:

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346...",
        "totalEstimatedMatches" : 982000,
        "value" : [{
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
            "name" : "Contoso Sailing Club - Seattle",
            "url" : "https:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/contososailingsea...",
            "snippet" : "Come sail with Contoso in Seattle...",
            "dateLastCrawled" : "2017-04-07T02:25:00"
        },
        {
            "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#WebPages.6",
            "name" : "Contoso Sailing Lessons - Official Site",
            "url" : "http:\/\/www.bing.com\/cr?IG=70BE289346ED4594874FE...",
            "displayUrl" : "https:\/\/www.constososailinglessonsseat...",
            "snippet" : "Contoso sailing lessons in Seattle...",
            "dateLastCrawled" : "2017-04-09T14:30:00"
        },

        ...

        ],
        "someResultsRemoved" : true
    },
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/7\/#RelatedSearches",
        "value" : [{
            "text" : "sailing lessons",
            "displayText" : "sailing lessons",
            "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=70BE289346E..."
        }

        ...

        ]
    },
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            }

            ...

            ]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}
```

A `rankingResponse` JSON objektum ([dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)) leírja a keresési eredmények megfelelő megjelenítési sorrendjét. Ez magában foglalja egy vagy több, a következő, kiemelt csoportok:

- `pole`: A keresési eredmények, hogy a leginkább látható kezelés (például felett jelenik meg a fővonal és az oldalsáv).
- `mainline`: A fővonalon megjelenítendő keresési eredmények.
- `sidebar`: Az oldalsávon megjelenítendő keresési eredmények. Ha nincs oldalsáv, jelenítse meg az eredményeket a fővonal alatt.

A JSON rangsorolási válasza egy vagy több csoportot tartalmazhat.

A **Program.cs**adja hozzá a következő módszert az eredmények megfelelő en rangsorolási sorrendjének megjelenítéséhez:

```csharp
static void DisplayAllRankedResults(Newtonsoft.Json.Linq.JObject responseObjects)
{
    string[] rankingGroups = new string[] { "pole", "mainline", "sidebar" };

    // Loop through the ranking groups in priority order
    foreach (string rankingName in rankingGroups)
    {
        Newtonsoft.Json.Linq.JToken rankingResponseItems = responseObjects.SelectToken($"rankingResponse.{rankingName}.items");
        if (rankingResponseItems != null)
        {
            foreach (Newtonsoft.Json.Linq.JObject rankingResponseItem in rankingResponseItems)
            {
                Newtonsoft.Json.Linq.JToken resultIndex;
                rankingResponseItem.TryGetValue("resultIndex", out resultIndex);
                var answerType = rankingResponseItem.Value<string>("answerType");
                switch (answerType)
                {
                    case "WebPages":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("webPages.value"), "WebPage", "name", "url", "displayUrl", "snippet");
                        break;
                    case "News":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("news.value"), "News", "name", "url", "description");
                        break;
                    case "Images":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("images.value"), "Image", "thumbnailUrl");
                        break;
                    case "Videos":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("videos.value"), "Video", "embedHtml");
                        break;
                    case "RelatedSearches":
                        DisplaySpecificResults(resultIndex, responseObjects.SelectToken("relatedSearches.value"), "RelatedSearch", "displayText", "webSearchUrl");
                        break;
                }
            }
        }
    }
}
```

Ez a módszer:

- Hurkok a `rankingResponse` válaszok által tartalmazott csoportok felett
- Az egyes csoportok elemeinek megjelenítése hívással`DisplaySpecificResults(...)`

A **Program.cs**adja hozzá a következő két módszert:

```csharp
static void DisplaySpecificResults(Newtonsoft.Json.Linq.JToken resultIndex, Newtonsoft.Json.Linq.JToken items, string title, params string[] fields)
{
    if (resultIndex == null)
    {
        foreach (Newtonsoft.Json.Linq.JToken item in items)
        {
            DisplayItem(item, title, fields);
        }
    }
    else
    {
        DisplayItem(items.ElementAt((int)resultIndex), title, fields);
    }
}

static void DisplayItem(Newtonsoft.Json.Linq.JToken item, string title, string[] fields)
{
    Console.WriteLine($"{title}: ");
    foreach( string field in fields )
    {
        Console.WriteLine($"- {field}: {item[field]}");
    }
    Console.WriteLine();
}
```

Ezek a módszerek együttműködnek a keresési eredmények konzolra való kimenetével.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást. A kimenetnek a következőképpen kell kinéznie:

```
Enter Bing query: sailing lessons seattle

WebPage:
- name: Contoso Sailing Club - Seattle
- url: https://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://contososailingsea....
- snippet: Come sail with Contoso in Seattle...

WebPage:
- name: Contoso Sailing Lessons Seattle - Official Site
- url: http://www.bing.com/cr?IG=70BE289346ED4594874FE...
- displayUrl: https://www.constososailinglessonsseat...
- snippet: Contoso sailing lessons in Seattle...

...
```

## <a name="next-steps"></a>További lépések

További információ a [rangsorolás használatáról az eredmények megjelenítéséhez.](rank-results.md)
