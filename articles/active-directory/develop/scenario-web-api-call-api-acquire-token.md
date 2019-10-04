---
title: Webes API-t, hogy más hívások webes API-kat (az alkalmazás szükséges jogkivonat beszerzése) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webes API-t, hogy a hívások más webes API-k (tokenbeolvasás az alkalmazás).
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
ms.openlocfilehash: 986e2e0f8a481d61dc870af2548290658b44d2d3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65231108"
---
# <a name="web-api-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webes API-t, amely meghívja a webes API-k – az alkalmazás szükséges jogkivonat beszerzése

Miután létrehozott egy ügyfélalkalmazás objektumot, hogyan használhatja a webes API-hívás jogkivonat-beszerzési.

## <a name="code-in-the-controller"></a>A vezérlő a Code

Íme egy példa a műveletek az API-vezérlők, (nevű todolist) alsóbb rétegbeli API meghívása a meghívni kívánt kódra.

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
}
```

`BuildConfidentialClient()` a cikkben megtudhatta hasonló [webes API, amely meghívja a webes API - alkalmazás konfigurációja](scenario-web-api-call-api-app-configuration.md). `BuildConfidentialClient()` példányosítja `IConfidentialClientApplication` az egy cache-ben, amely csak egy fiók adatait tartalmazza. A fiók által biztosított a `GetAccountIdentifier` metódust.

A `GetAccountIdentifier` módszer a jogcímeket a felhasználó számára, amely a webes API a JWT kapott az identitáshoz tartozó használja:

```CSharp
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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A webes API meghívása](scenario-web-api-call-api-call-api.md)
