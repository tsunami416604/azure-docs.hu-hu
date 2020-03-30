---
title: Webes API, amely webes API-kat hív meg - Microsoft identity platform | Azure
description: Ismerje meg, hogyan hozhat létre webes API-t, amely webes API-kat hív meg.
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
ms.openlocfilehash: d66a08d4e84a3771d6c3fa46b96c975869435452
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76833379"
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
