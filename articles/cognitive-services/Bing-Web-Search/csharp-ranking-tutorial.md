---
title: Dimenziószáma használja a keresési eredmények megtekintése céljából |} Microsoft Docs
description: Bemutatja, hogyan használja a Bing RankingResponse válasz sorrend a keresési eredmények megtekintése céljából.
services: cognitive-services
author: bradumbaugh
manager: bking
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 05/08/2017
ms.author: brumbaug
ms.openlocfilehash: ec47b8448c0c39cc54e4c79434ce7a2d926df341
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347578"
---
# <a name="build-a-console-app-search-client-in-c"></a>C# konzol app keresési ügyfél létrehozása

Ez az oktatóanyag bemutatja, hogyan hozhat létre egy egyszerű .NET Core console-alkalmazást, amely lehetővé teszi a felhasználóknak a webes Bing keresési API lekérdezhetik és megjeleníthetik a rangsorolt eredmények.

Ez az oktatóanyag bemutatja, hogyan:

- Egy egyszerű lekérdezést teremtsen webes a Bing keresési API
- Lekérdezési eredmények megjelenítése rangsorolt sorrendben

## <a name="prerequisites"></a>Előfeltételek

Kövesse az oktatóanyag együtt, a következők szükségesek:

- Visual Studio. Ha még nem rendelkezik, [töltse le és telepítse az ingyenes Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- A webes Bing keresési API előfizetés kulcsot. Ha még nincs fiókja, [regisztráljon egy ingyenes próbaverzióra](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Új Konzolalkalmazás-projekt létrehozása

Hozzon létre egy projektet a Visual Studióban a `Ctrl`+`Shift`+`N` billentyűparancs lenyomásával.

Az a **új projekt** párbeszédpanel, kattintson a **Visual C# > Klasszikus Windows asztal > Konzolalkalmazás (.NET-keretrendszer)**.

Adjon nevet az alkalmazásnak **MyConsoleSearchApp**, és kattintson a **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>A JSON.net Nuget-csomag hozzáadása a projekthez

JSON.net lehetővé teszi a JSON-választ az API által visszaadott használatát. A NuGet-csomag hozzáadása a projekthez:

- A **Megoldáskezelőben** kattintson a jobb gombbal a projekt, és válassza ki a **NuGet-csomagok kezelése...** . 
- Az a **Tallózás** lap, a keresési keresése `Newtonsoft.Json`. Válassza ki a legújabb verziót, és kattintson **telepítése**. 
- Kattintson a **OK** gombra a **változások áttekintése** ablak.
- Zárja be a Visual Studio lapon című **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Adjon hozzá egy hivatkozást, System.Web

Ez az oktatóanyag támaszkodik a `System.Web` szerelvény. Erre a szerelvényre mutató hivatkozás hozzáadása a projekthez:

- A **Megoldáskezelőben**, kattintson a jobb gombbal a **hivatkozások** válassza **hivatkozás hozzáadása...**
- Válassza ki **szerelvények > keretrendszer**, majd görgessen lefelé, és ellenőrizze **System.Web**
- Kattintson az **OK** gombra.

## <a name="add-some-necessary-using-statements"></a>Néhány szükséges utasítások segítségével hozzáadása

A jelen oktatóanyagban található kód szükséges három további utasítások segítségével. Adja hozzá a meglévő alatt ezekről az utasításokról `using` tetején lévő utasítások **Program.cs**: 

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Kérje meg, egy lekérdezés

A **Megoldáskezelőben**, nyissa meg **Program.cs**. Frissítés a `Main()` módszert:

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

- Megkérdezi a felhasználót, egy lekérdezés
- Hívások `RunQueryAndDisplayResults(userQuery)` végrehajtsák a lekérdezést, és az eredmények megtekintése
- Megvárja-e a felhasználói bevitel leállítja, nehogy a konzolablakban azonnal bezárása.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Keresse meg a Bing webes API-jával keresési lekérdezés eredményei

Ezután adja hozzá egy metódust, amely lekérdezi az API-t, és megjeleníti az eredményeket:

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

- Létrehoz egy `HttpClient` a webes keresés API lekérdezése
- Beállítja a `Ocp-Apim-Subscription-Key` HTTP-fejléc, a Bing használó hitelesíteni a kérelmet
- A kérelem végrehajtása és az eredmények deszerializálása JSON.net használ
- Hívások `DisplayAllRankedResults(responseObjects)` összes eredmény megjelenítéséhez rangsorolt sorrendben

Ügyeljen arra, hogy állítsa be a `Ocp-Apim-Subscription-Key` az előfizetés kulccsal.

## <a name="display-ranked-results"></a>Rangsorolt eredmények megtekintése

Bemutatja, hogyan jelenjenek meg az eredmények rangsorolt sorrendben, előtt tekintse meg a webes keresés mintaválasz: 

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

A `rankingResponse` JSON-objektum ([dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) a megfelelő megjelenítési sorrendje a keresési eredmények ismerteti. Legalább egy, a következő, a rangsorolt csoportokat tartalmazza: 

- `pole`: A keresési eredmények eléréséhez a legjobban látható kezelése (például a a mainline fölött megjelenő szöveg és az oldalsó sáv).
- `mainline`: A keresési eredmények a mainline megjelennek.
- `sidebar`: A keresési eredmények megjelenítéséhez az oldalsávon. Ha nincs oldalsávon, jeleníti meg az eredményeket a mainline alatt.

A rangsorolási válasz JSON legalább egy, a csoportok tartalmazhatják.

A **Program.cs**, adja hozzá a következő metódust eredmények megjelenítéséhez megfelelően rangsorolt sorrendben:

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

- Fut a hurokban keresztül a `rankingResponse` csoportokat, amelyek a válasz tartalmazza
- Az elemek megjelenítése minden csoport meghívásával `DisplaySpecificResults(...)` 

A **Program.cs**, adja hozzá az alábbi két módszerrel:

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

Ezek a módszerek működnek együtt a keresési eredmények a konzol kimeneti.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást. A kimenet az alábbihoz hasonló kell kinéznie:

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

Tudjon meg többet az [eredmények megjelenítéséhez használja a prioritás](rank-results.md).