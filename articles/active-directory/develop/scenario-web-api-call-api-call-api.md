---
title: Webes API, amely webes API-kat hív meg - Microsoft identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-t, amely webes API-kat hív meg.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 6bbd24978891efd147b0c317c1746d13961ce5e9
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885089"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Webes API-kat meghívó webes API:API hívása

Miután rendelkezik egy jogkivonatot, meghívhat egy védett webes API-t. Ezt a webes API vezérlőjétől kell megtennie.

## <a name="controller-code"></a>Vezérlő kódja

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A következő kód folytatja a [webes API-kat meghívjaó webes API-kban megjelenő példakódot: Az alkalmazás jogkivonatának beszerzése.](scenario-web-api-call-api-acquire-token.md) A kód neve az API-vezérlők műveleteket. Meghívja a todolista nevű alsóbb rétegbeli *API-t.*

Miután megszerezte a jogkivonatot, használja azt tulajdonosi jogkivonatként az alsóbb rétegbeli API-hoz.

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

// After the token has been returned by Microsoft Authentication Library (MSAL), add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

A következő kód folytatja a [webes API-kat meghívjaó webes API-kban megjelenő példakódot: Az alkalmazás jogkivonatának beszerzése.](scenario-web-api-call-api-acquire-token.md) A kód neve az API-vezérlők műveleteket. Meghívja az alsóbb rétegbeli API MS Graph.It calls the downstream API MS Graph.

Miután megszerezte a jogkivonatot, használja azt tulajdonosi jogkivonatként az alsóbb rétegbeli API-hoz.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
A folyamatot az MSAL Python-nal bemutató minta még nem érhető el.

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API:Ugrás éles környezetbe](scenario-web-api-call-api-production.md)
