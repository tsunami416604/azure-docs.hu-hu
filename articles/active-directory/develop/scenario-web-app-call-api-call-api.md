---
title: Webalkalmazás, hogy a hívások webes API-k (webes API hívása) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely meghívja a webes API-k (webes API hívása)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3624f4e859081e53ee27b6f8415eb3f9b5a2a5fa
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67785464"
---
# <a name="web-app-that-calls-web-apis---call-a-web-api"></a>Webes API-hívás, amely meghívja a webes API - webalkalmazás

Most, hogy megkapta a jogkivonatot, meghívhatja egy védett webes API-t.

## <a name="aspnet-core"></a>ASP.NET-mag

A művelet az egy egyszerűsített kódja a `HomeController`. Ez a kód beolvassa egy tokent a Microsoft Graph meghívásához. Ez alkalommal a kód lett hozzáadva, a REST API-ként Microsoft Graph meghívásához bemutató. A graph API URL-címe megtalálható a `appsettings.json` fájlt, és olvassa el a nevű változóban `webOptions`:

```JSon
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;

 // Calls the web API (here the graph)
 HttpClient httpClient = new HttpClient();
 httpClient.DefaultRequestHeaders.Authorization =
     new AuthenticationHeaderValue(Constants.BearerAuthorizationScheme,accessToken);
 var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

 if (response.StatusCode == HttpStatusCode.OK)
 {
  var content = await response.Content.ReadAsStringAsync();

  dynamic me = JsonConvert.DeserializeObject(content);
  return me;
 }

 ViewData["Me"] = me;
 return View();
}
```

> [!NOTE]
> Használhatja ugyanezt az elvet minden webes API meghívásához.
>
> A legtöbb Azure webes API-k egy SDK-t, amely leegyszerűsíti a hívó azt adja meg. Ez egyben a kis-és a Microsoft Graph. A következő cikkben bemutatjuk, hogy hol található a ezeket a szempontokat bemutató oktatóanyag.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-web-app-call-api-production.md)
