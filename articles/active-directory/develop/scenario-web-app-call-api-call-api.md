---
title: Webes API meghívása egy webalkalmazásból – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan hozhat létre olyan webalkalmazást, amely webes API-kat hív meg (védett webes API-t hív meg)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1e448f52f4e8c24dd8552cae873edac841e57fc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058442"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Webes API-kat meghívó webalkalmazás: webes API meghívása

Most, hogy rendelkezik egy jogkivonattal, meghívhat egy védett webes API-t.

## <a name="call-a-protected-web-api"></a>Védett webes API meghívása

A védett webes API-k meghívása a választott nyelvtől és keretrendszertől függ:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Íme a művelethez tartozó egyszerűsített kód `HomeController` . Ez a kód lekéri a Microsoft Graph meghívására szolgáló tokent. A kód hozzá lett adva, hogy megmutassa, hogyan hívhatja meg Microsoft Graph REST APIként. A Microsoft Graph API URL-címe megtalálható a fájl appsettings.jsjában, és a következő nevű változóban olvasható `webOptions` :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```

> [!NOTE]
> Ugyanezt az elvet használhatja bármely webes API meghívásához.
>
> A legtöbb Azure-beli webes API egy SDK-t biztosít, amely leegyszerűsíti az API meghívását. Ez Microsoft Graph is igaz. A következő cikkben megtudhatja, hol találhat egy olyan oktatóanyagot, amely bemutatja az API-használatot.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Átállás éles üzemre](scenario-web-app-call-api-production.md)
