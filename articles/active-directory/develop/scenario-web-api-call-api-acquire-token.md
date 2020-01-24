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
ms.openlocfilehash: 1c3d6f8d47152d70bdeaabbbc6d7b81187291857
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701807"
---
# <a name="a-web-api-that-calls-web-apis-acquire-a-token-for-the-app"></a>Webes API-kat meghívó webes API: az alkalmazás jogkivonatának beszerzése

Miután létrehozott egy ügyfélalkalmazás-objektumot, használja azt egy olyan jogkivonat beszerzéséhez, amely a webes API-k meghívására használható.

## <a name="code-in-the-controller"></a>Kód a vezérlőben

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

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Webes API-kat meghívó webes API: az API meghívása](scenario-web-api-call-api-call-api.md)
