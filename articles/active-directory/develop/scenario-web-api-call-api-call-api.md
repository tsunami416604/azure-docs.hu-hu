---
title: Webes API-k meghívása webes API-k – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó webes API-t.
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
ms.openlocfilehash: b756d7df03bd3c06b703617dbf84a194d716f1e3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026373"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Webes API-kat meghívó webes API: az API meghívása

A jogkivonat meghívása után meghívhat egy védett webes API-t. Ezt a webes API vezérlője hajtja végre.

## <a name="controller-code"></a>Vezérlő kódja

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A következő kód folytatja a webes API-kat meghívó webes API-kon megjelenő példa kódját [: az alkalmazás jogkivonatának beszerzése](scenario-web-api-call-api-acquire-token.md). A kódot az API-vezérlők műveleteiben hívják meg. Egy *ToDoList*nevű alsóbb RÉTEGbeli API-t hív meg.

Miután megszerezte a tokent, használja tulajdonosi jogkivonatként az alsóbb rétegbeli API meghívásához.

```csharp
private async Task CallTodoListService(string accessToken)
{

// After the token has been returned by Microsoft Identity Web, add it to the HTTP authorization header before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

# <a name="java"></a>[Java](#tab/java)

A következő kód folytatja a webes API-kat meghívó webes API-kon megjelenő példa kódját [: az alkalmazás jogkivonatának beszerzése](scenario-web-api-call-api-acquire-token.md). A kódot az API-vezérlők műveleteiben hívják meg. Meghívja az alsóbb rétegbeli API MS Graphot.

Miután megszerezte a tokent, használja tulajdonosi jogkivonatként az alsóbb rétegbeli API meghívásához.

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
A folyamatot bemutató minta a MSAL Python szolgáltatással még nem érhető el.

---

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: áthelyezés éles környezetbe](scenario-web-api-call-api-production.md)
