---
title: Webes API-k meghívása webes API-k – Microsoft Identity platform | Azure
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó webes API-t.
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
ms.openlocfilehash: 6063d143e2f217426bdf1db217fde46f8542d314
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965211"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Webes API-kat meghívó webes API – egy API meghívása

Ha rendelkezik jogkivonattal, meghívhat egy védett webes API-t. Ezt a ASP.NET/ASP.NET Core webes API vezérlője hajtja végre.

## <a name="controller-code"></a>Vezérlő kódja

Itt látható a [védett webes API-hívásokban](scenario-web-api-call-api-acquire-token.md)megjelenő példa kódja a webes API-k használatával – a jogkivonat beszerzése, amelyet az API-vezérlők műveleteiben, egy alsóbb RÉTEGbeli API meghívásakor (ToDoList) hívnak meg.

Miután megszerezte a tokent, használja tulajdonosi jogkivonatként az alsóbb rétegbeli API meghívásához.

```CSharp
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

// Once the token has been returned by MSAL, add it to the http authorization header, before making the call to access the To Do list service.
_httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the To Do list service.
HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
...
}
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Áthelyezés éles környezetbe](scenario-web-api-call-api-production.md)
