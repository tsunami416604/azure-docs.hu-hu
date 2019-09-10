---
title: Webes API-kat meghívó webalkalmazás (token beszerzése az alkalmazáshoz) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó webalkalmazást (token beszerzése az alkalmazáshoz)
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
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3aa144c76fb0a8e479658efdb5d43361fbbc085c
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70860622"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webes API-kat meghívó webalkalmazás – jogkivonat beszerzése az alkalmazáshoz

Most, hogy létrehozta az ügyfélalkalmazás-objektumot, felhasználhatja egy, a webes API-t meghívó token beszerzéséhez. A ASP.NET vagy ASP.NET Coreban a webes API-k meghívása a vezérlőn történik. Az alábbiakról szól:

- Jogkivonat-gyorsítótár beszerzése a webes API-hoz. A jogkivonat beszerzéséhez hívja `AcquireTokenSilent`a következőt:.
- A védett API meghívása a hozzáférési jogkivonattal.

## <a name="aspnet-core"></a>ASP.NET-mag

A vezérlő módszereit olyan `[Authorize]` attribútum védi, amely arra kényszeríti a felhasználókat, hogy a webalkalmazás használatára legyenek hitelesítve. Itt látható a Microsoft Graph meghívására szolgáló kód.

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Itt látható a HomeController műveletének egyszerűsített kódja, amely lekéri a tokent a Microsoft Graph meghívásához.

```CSharp
public async Task<IActionResult> Profile()
{
 var application = BuildConfidentialClientApplication(HttpContext, HttpContext.User);
 string accountIdentifier = claimsPrincipal.GetMsalAccountId();
 string loginHint = claimsPrincipal.GetLoginHint();

 // Get the account
 IAccount account = await application.GetAccountAsync(accountIdentifier);

 // Special case for guest users as the Guest iod / tenant id are not surfaced.
 if (account == null)
 {
  var accounts = await application.GetAccountsAsync();
  account = accounts.FirstOrDefault(a => a.Username == loginHint);
 }

 AuthenticationResult result;
 result = await application.AcquireTokenSilent(new []{"user.read"}, account)
                            .ExecuteAsync();
 var accessToken = result.AccessToken;
 ...
 // use the access token to call a web API
}
```

A forgatókönyvhöz szükséges kód alaposabb megismeréséhez tekintse meg a 2. fázis ([2-1-Web App calls Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph)) lépést az [MS-Identity-aspnetcore-WebApp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) oktatóanyagban.

Számos további bonyolultság van, például:

- Jogkivonat-gyorsítótár implementálása a webalkalmazáshoz (az oktatóanyag számos implementációt mutat be)
- A fiók eltávolítása a gyorsítótárból, ha a felhasználó kijelentkezik
- Több API meghívása, beleértve a növekményes hozzájárulást is

## <a name="aspnet"></a>ASP.NET

A ASP.NET hasonló dolgok:

- Az [engedélyezés] attribútum által védett vezérlő művelet kibontja a vezérlő `ClaimsPrincipal` tagjának bérlői azonosítóját és felhasználói azonosítóját. (A ASP.NET `HttpContext.User`használja.)
- Ettől kezdve létrehoz egy MSAL.NET `IConfidentialClientApplication`.
- Végezetül meghívja a `AcquireTokenSilent` bizalmas ügyfélalkalmazás metódusát.

A kód hasonló a ASP.NET Corehoz megjelenített kódhoz.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-hívás](scenario-web-app-call-api-call-api.md)
