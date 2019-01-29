---
title: Rang használatával a keresési eredmények megjelenítése
titleSuffix: Azure Cognitive Services
description: Bemutatja, hogyan használhatja a Bing RankingResponse választ sorrend a keresési eredmények megjelenítéséhez.
services: cognitive-services
author: bradumbaugh
manager: cgronlun
ms.assetid: 2575A80C-FC74-4631-AE5D-8101CF2591D3
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 05/08/2017
ms.author: bking
ms.openlocfilehash: 90ad2ff87e9541c892262edb2e48366826b2a388
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55188439"
---
# <a name="build-a-console-app-search-client-in-c"></a>Hozhat létre egy konzol alkalmazás keresési ügyfélalkalmazást a C#-ban

Ez az oktatóanyag bemutatja, hogyan hozhat létre egyszerű .NET Core-konzolalkalmazást, amely lehetővé teszi a felhasználóknak a Bing Web Search API lekérdezéséhez és rangsorolt eredmények megjelenítéséhez.

Ez az oktatóanyag bemutatja, hogyan lehet:

- Hozzon létre egy egyszerű lekérdezést, a Bing Web Search API
- Lekérdezési eredmények megjelenítése a rangsorolt sorrendben

## <a name="prerequisites"></a>Előfeltételek

Követheti az oktatóanyagot, az alábbiak szükségesek:

- Visual Studio. Ha nem rendelkezik, [töltse le és telepítse az ingyenes Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/).
- Egy előfizetési kulcsot, a Bing Web Search API számára. Ha nem rendelkezik ilyennel, regisztrálhat az [ingyenes próbaverzióra](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).

## <a name="create-a-new-console-app-project"></a>Hozzon létre egy új Konzolalkalmazás-projektet

Hozzon létre egy projektet a Visual Studióban a `Ctrl`+`Shift`+`N` billentyűparancs lenyomásával.

Az a **új projekt** párbeszédpanelen kattintson a **Visual C# > Windows klasszikus Asztalialkalmazás > Console App (.NET Framework)**.

Adjon nevet az alkalmazásnak **MyConsoleSearchApp**, és kattintson a **OK**.

## <a name="add-the-jsonnet-nuget-package-to-the-project"></a>A JSON.net Nuget csomagjának hozzáadása a projekthez

JSON.net lehetővé teszi, hogy az API által visszaadott JSON-válaszok. Adja hozzá a NuGet-csomagot a projekthez:

- A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza ki a **NuGet-csomagok kezelése...** .
- Az a **Tallózás** fülre, keressen rá a `Newtonsoft.Json`. Válassza ki a legújabb verziót, és kattintson **telepítése**.
- Kattintson a **OK** gombot a **változások áttekintése** ablak.
- Zárja be a Visual Studio lap címe **NuGet: MyConsoleSearchApp**.

## <a name="add-a-reference-to-systemweb"></a>Vegyen fel egy hivatkozást System.Web

Ebben az oktatóanyagban támaszkodik a `System.Web` sestavení. Vegyen fel egy hivatkozást a szerelvény a projekthez:

- A **Megoldáskezelőben**, kattintson a jobb gombbal a **hivatkozások** válassza **hivatkozás hozzáadása...**
- Válassza ki **Sestavení > keretrendszer**, majd görgessen lefelé, és ellenőrizze **System.Web**
- Kattintson az **OK** gombra.

## <a name="add-some-necessary-using-statements"></a>Néhány szükséges utasításokkal hozzáadása

A jelen oktatóanyagban szereplő kód szükséges három további utasításokkal. Adja hozzá a következő utasításokat a meglévő alábbi `using` utasítást a elejéhez **Program.cs**:

```csharp
using System.Web;
using System.Net.Http;
```

## <a name="ask-the-user-for-a-query"></a>Kérje meg a felhasználót egy lekérdezés

A **Megoldáskezelőben**, nyissa meg **Program.cs**. Frissítés a `Main()` módszer:

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

- Arra kéri a felhasználót egy lekérdezés
- Hívások `RunQueryAndDisplayResults(userQuery)` hajtsa végre a lekérdezést, és az eredmények megjelenítése
- Felhasználói bevitel megvárja, amíg annak érdekében, hogy megakadályozza, hogy a konzolablakban azonnal bezárása.

## <a name="search-for-query-results-using-the-bing-web-search-api"></a>Keresse meg a Bing Web Search API a lekérdezés eredménye

Ezután adjon hozzá egy metódus lekérdezi az API-t, és megjeleníti az eredményeket:

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

- Létrehoz egy `HttpClient` lekérdezni a Web Search API
- Beállítja a `Ocp-Apim-Subscription-Key` HTTP-fejléc, mely a Bing segítségével hitelesíteni a kérelmet
- A kérelem végrehajtása, és az eredmények deszerializálása a JSON.net használatával
- Hívások `DisplayAllRankedResults(responseObjects)` rangsorolt sorrendben összes eredmény megjelenítése

Ügyeljen arra, hogy az értékét állítsa be, hogy `Ocp-Apim-Subscription-Key` az előfizetési kulccsal.

## <a name="display-ranked-results"></a>Az első megjelenített eredmények

Mielőtt bemutató rangsorolt sorrendben jelenjenek meg az eredmények, mintául szolgáló webes keresés válasz tekintse meg:

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

A `rankingResponse` JSON-objektum ([dokumentáció](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#rankingresponse)) ismerteti a keresési eredmények kívánt megjelenítési sorrendjét. Legalább egy, a következő, a rangsorolt csoportokat tartalmazza:

- `pole`: A keresési eredményeket a legfeltűnőbb kezelés lekérése (például a fent a által látható és az oldalsávot).
- `mainline`: A keresési eredmények megjelennek a által.
- `sidebar`: A keresési eredmények megjelennek az oldalsávon. Ha nincs Oldalsáv, jelenítjük meg alább a által.

Ennek a területnek válasz JSON tartalmazhat egy vagy több, a csoportok.

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

- Keresztül hurkokat a `rankingResponse` csoportokat, amelyek a válasz tartalmazza
- Az elemek megjelenítése az egyes csoportokban meghívásával `DisplaySpecificResults(...)`

A **Program.cs**, adja hozzá az alábbi két módszer:

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

Ezek a metódusok együttműködve a keresési eredményeket a konzolon.

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

Tudjon meg többet [eredmények megjelenítéséhez használja a ranglistán megjelenő](rank-results.md).
