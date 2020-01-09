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
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc92fb7bc5ddf451279e6c157f9e93aa7fe9a12a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423623"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webes API-k meghívása webes API-k – az alkalmazás jogkivonatának beszerzése

Miután létrehozott egy ügyfélalkalmazás-objektumot, használja azt egy olyan token beszerzéséhez, amelyet használhat a webes API-k meghívásához.

## <a name="code-in-the-controller"></a>Kód a vezérlőben

Az alábbi példa egy olyan kódot mutat be, amely az API-vezérlők műveleteiben lesz meghívva, egy alsóbb rétegbeli API meghívása (ToDoList).

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

a `BuildConfidentialClient()` a [webes API-kat meghívó webes API-t meghívja a web API-k – alkalmazás konfigurálása](scenario-web-api-call-api-app-configuration.md)című cikkben láthatóhoz hasonló. `BuildConfidentialClient()` olyan gyorsítótárat hoz létre `IConfidentialClientApplication`, amely csak egy fiók adatait tartalmazza. A fiókot a `GetAccountIdentifier` metódus biztosít.

A `GetAccountIdentifier` metódus a felhasználó identitásához társított jogcímeket használja, amelyekhez a webes API a JWT kapta:

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
> [Webes API meghívása](scenario-web-api-call-api-call-api.md)
