---
title: Get egy token egy webes API-k, amely meghívja a webes API-k | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webes API-t, amely meghívja a webes API-kat, amelyek megkövetelik az alkalmazás jogkivonatának beszerzését.
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
ms.openlocfilehash: 2721837459af24f39bb15ee17d394345cbb37eb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76834110"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívja: Jogkivonat beszerzése az alkalmazáshoz

Miután elkészítette az ügyfélalkalmazás-objektumot, használja azt egy olyan jogkivonat beszerzésére, amely segítségével meghívhat egy webes API-t.

## <a name="code-in-the-controller"></a>Kód a vezérlőben

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Íme egy példa az API-vezérlők műveletek ben megnevezett kódra. Meghívja a todolista nevű alsóbb rétegbeli *API-t.*

```csharp
private async Task GetTodoList(bool isAppStarting)
{
 ...
 //
 // Get an access token to call the To Do service.
 //
 AuthenticationResult result = null;
 try
 {
  app = BuildConfidentialClient(HttpContext, HttpContext.User);
  result = await app.AcquireTokenSilent(Scopes, account)
                     .ExecuteAsync()
                     .ConfigureAwait(false);
 }
...
}
```

`BuildConfidentialClient()`hasonló a forgatókönyv [egy webes API-k, amely meghívja a webes API-k: App konfiguráció](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()`olyan gyorsítótárral `IConfidentialClientApplication` rendelkezik, amely csak egy fiók adatait tartalmazza. A fiókot a `GetAccountIdentifier` metódus biztosítja.

A `GetAccountIdentifier` metódus a json-webtoken (JWT) azonosítójával társított jogcímeket használja:

```csharp
public static string GetMsalAccountId(this ClaimsPrincipal claimsPrincipal)
{
 string userObjectId = GetObjectId(claimsPrincipal);
 string tenantId = GetTenantId(claimsPrincipal);

 if (    !string.IsNullOrWhiteSpace(userObjectId)
      && !string.IsNullOrWhiteSpace(tenantId))
 {
  return $"{userObjectId}.{tenantId}";
 }

 return null;
}
```

# <a name="java"></a>[Java](#tab/java)
Íme egy példa az API-vezérlők műveletek ben megnevezett kódra. Meghívja az alsóbb rétegbeli API-t - a Microsoft Graph.

```java
@RestController
public class ApiController {

    @Autowired
    MsalAuthHelper msalAuthHelper;

    @RequestMapping("/graphMeApi")
    public String graphMeApi() throws MalformedURLException {

        String oboAccessToken = msalAuthHelper.getOboToken("https://graph.microsoft.com/.default");

        return callMicrosoftGraphMeEndpoint(oboAccessToken);
    }

}
```

# <a name="python"></a>[Python](#tab/python)

A Python webes API-t kell használninéhány köztes az ügyféltől kapott tulajdonosi jogkivonat érvényesítéséhez. A webes API ezután beszerezheti a hozzáférési jogkivonatot [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) az alsóbb rétegbeli API-hoz az MSAL Python-kódtár használatával a metódus hívásával. A folyamatot az MSAL Python-nal bemutató minta még nem érhető el.

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API:API hívása](scenario-web-api-call-api-call-api.md)
