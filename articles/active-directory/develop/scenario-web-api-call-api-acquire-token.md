---
title: A webes API-kat meghívó webes API-k jogkivonatának beolvasása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre olyan webes API-t, amely meghívja az alkalmazás jogkivonatának beszerzését igénylő webes API-kat.
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
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76834110"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívó webes API: az alkalmazás jogkivonatának beszerzése

Miután létrehozott egy ügyfélalkalmazás-objektumot, használja azt egy olyan jogkivonat beszerzéséhez, amely a webes API-k meghívására használható.

## <a name="code-in-the-controller"></a>Kód a vezérlőben

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Íme egy példa az API-vezérlők műveleteiben meghívott kódra. Egy *ToDoList*nevű alsóbb RÉTEGbeli API-t hív meg.

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

`BuildConfidentialClient()` a webes API- [kat meghívó webes API](scenario-web-api-call-api-app-configuration.md)-hoz hasonló forgatókönyvhöz hasonlít: az alkalmazás konfigurációja. `BuildConfidentialClient()` olyan gyorsítótárat hoz létre `IConfidentialClientApplication`, amely csak egy fiókra vonatkozó információt tartalmaz. A fiókot a `GetAccountIdentifier` metódus biztosít.

A `GetAccountIdentifier` metódus a felhasználó identitásához társított jogcímeket használja, akik számára a webes API a JSON Web Tokent (JWT) kapta:

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

# <a name="javatabjava"></a>[Java](#tab/java)
Íme egy példa az API-vezérlők műveleteiben meghívott kódra. Meghívja az alsóbb rétegbeli API-Microsoft Graph.

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

# <a name="pythontabpython"></a>[Python](#tab/python)

Egy Python webes API-nak bizonyos middleware-t kell használnia az ügyféltől kapott tulajdonosi jogkivonat ellenőrzéséhez. A webes API ezután a MSAL Python Library használatával szerezheti be az alsóbb rétegbeli API hozzáférési tokenjét a [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metódus meghívásával. A folyamatot bemutató minta a MSAL Python szolgáltatással még nem érhető el.

---

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: az API meghívása](scenario-web-api-call-api-call-api.md)
