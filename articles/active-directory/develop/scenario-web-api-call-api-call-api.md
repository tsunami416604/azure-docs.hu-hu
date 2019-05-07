---
title: Webes API-t, amely meghívja a webes API-k (hívás API-k) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre a webes API-k a hívások alsóbb rétegbeli webes API-k (webes API hívása).
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
ms.openlocfilehash: 1cd093cc68a21558dc326221eeaa8c034c24f1c2
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074725"
---
# <a name="web-api-that-calls-web-apis---call-an-api"></a>Webes API-t, amely meghívja a webes API-k – az API meghívása

Miután megkapta a jogkivonatot, meghívhatja egy védett webes API-t. Ez a vezérlő a ASP.NET/ASP.NET Core webes API végezheti el.

## <a name="controller-code"></a>Vezérlő kód

Íme a látható példa kód az fenntartása [védett webes API-hívások webes API-k – egy token beszerzése a](scenario-web-api-call-api-acquire-token.md)néven (nevű todolist) alsóbb rétegbeli API meghívása a műveletek az API-vezérlők,.

Miután a token szerezte be, használhatja tulajdonosi jogkivonattal, az alsóbb rétegbeli API-t.

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Helyezze át az éles környezetbe](scenario-web-api-call-api-production.md)
