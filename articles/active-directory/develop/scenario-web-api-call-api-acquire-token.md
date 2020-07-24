---
title: A webes API-kat meghívó webes API-k jogkivonatának beolvasása | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre olyan webes API-t, amely meghívja az alkalmazás jogkivonatának beszerzését igénylő webes API-kat.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7e0701cc5a9bb14800a48e2281dba1eb6ea0cf72
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026458"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívó webes API: az alkalmazás jogkivonatának beszerzése

Miután létrehozott egy ügyfélalkalmazás-objektumot, használja azt egy olyan jogkivonat beszerzéséhez, amely a webes API-k meghívására használható.

## <a name="code-in-the-controller"></a>Kód a vezérlőben

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Íme egy példa a kód használatára a Microsoft. Identity. Web néven, amely az API-vezérlők műveleteiben található. Egy *ToDoList*nevű alsóbb RÉTEGbeli API-t hív meg. Az alárendelt API meghívására szolgáló jogkivonat lekéréséhez a `ITokenAcquisition` szolgáltatás a vezérlő konstruktorában (vagy az oldal konstruktorában, ha a Blazer használatakor) befecskendezi a szolgáltatást, és a vezérlő műveleteiben használja, a felhasználó ( `GetAccessTokenForUserAsync` ) vagy az alkalmazás () jogkivonatának beszerzése `GetAccessTokenForAppAsync` egy démoni forgatókönyv esetén.

```csharp
[Authorize]
public class MyApiController : Controller
{
    /// <summary>
    /// The web API will accept only tokens 1) for users, 2) that have the `access_as_user` scope for
    /// this API.
    /// </summary>
    static readonly string[] scopeRequiredByApi = new string[] { "access_as_user" };

     static readonly string[] scopesToAccessDownstreamApi = new string[] { "api://MyTodolistService/access_as_user" };

    private readonly ITokenAcquisition _tokenAcquisition;

    public MyApiController(ITokenAcquisition tokenAcquisition)
    {
        _tokenAcquisition = tokenAcquisition;
    }

    public IActionResult Index()
    {
        HttpContext.VerifyUserHasAnyAcceptedScope(scopeRequiredByApi);

        string accessToken = _tokenAcquisition.GetAccessTokenForUserAsync(scopesToAccessDownstreamApi);
        return await callTodoListService(accessToken);
    }
}
```

A metódussal kapcsolatos részletekért `callTodoListService` tekintse meg [a webes API-kat meghívó webes API-t: az API meghívása](scenario-web-api-call-api-call-api.md)című témakört.

# <a name="java"></a>[Java](#tab/java)
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

# <a name="python"></a>[Python](#tab/python)

Egy Python webes API-nak bizonyos middleware-t kell használnia az ügyféltől kapott tulajdonosi jogkivonat ellenőrzéséhez. A webes API ezt követően a metódus meghívásával lekérheti az alsóbb rétegbeli API hozzáférési tokenjét a MSAL Python Library használatával [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) . A folyamatot bemutató minta a MSAL Python szolgáltatással még nem érhető el.

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: az API meghívása](scenario-web-api-call-api-call-api.md)
