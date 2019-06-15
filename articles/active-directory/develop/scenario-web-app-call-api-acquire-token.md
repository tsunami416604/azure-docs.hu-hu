---
title: Webes alkalmazás, amely meghívja a webes API-kat (az alkalmazás szükséges jogkivonat beszerzése) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely meghívja a webes API-k (tokenbeolvasás az alkalmazás)
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
ms.openlocfilehash: 653db995000308bb3ef78a9183696cd9d8ed1056
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074800"
---
# <a name="web-app-that-calls-web-apis---acquire-a-token-for-the-app"></a>Webes alkalmazás, amely meghívja a webes API-k – az alkalmazás szükséges jogkivonat beszerzése

Most létrehozott, ügyfél alkalmazásobjektum, majd egy webes API-k hívása a használni kívánt jogkivonat-beszerzési fogja használni. Az ASP.NET- vagy ASP.NET Core webes API-vezérlőben levő majd történik meghívásakor. Szól:

- A webes API-t a tokengyorsítótárral jogkivonatának beolvasása. Ennek a hívás `AcquireTokenSilent`
- A hozzáférési jogkivonat védett API meghívása

## <a name="aspnet-core"></a>ASP.NET-mag

A vezérlő módszerek által védett egy `[Authorize]` attribútum, amely arra kényszeríti a felhasználók a webalkalmazás használatához hitelesítésre. A Microsoft Graph meghívásához kódja

```CSharp
[Authorize]
public class HomeController : Controller
{
 ...
}
```

Íme egy egyszerűsített kódot a HomeController, amely kér le egy tokent a Microsoft Graph meghívásához művelet.

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

Ha szeretné az ehhez a forgatókönyvhöz szükséges kódot az összes részletes ismertetése, tekintse meg a 2. fázis [2-1-Web App hívások a Microsoft Graph](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-1-Call-MSGraph) lépésében a [ms-identity-aspnetcore-webapp-tutorial](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) oktatóanyag

Van például számos további hagyhatják:

- a Web App (az oktatóanyag jelen számos implementáció) token gyorsítótár megvalósítása
- A fiók eltávolítása a gyorsítótárból, ha a felhasználó jelentkezik, kimenő
- Több API-t, beleértve a növekményes hozzájárulási kellene hívása

## <a name="aspnet"></a>ASP.NET

Az ASP.NET hasonló a következők:

- Egy tartományvezérlő művelet [engedélyezés] attribútum által védett fogja kinyerni a bérlő Azonosítóját és felhasználói azonosítója, a `ClaimsPrincipal` a tartományvezérlő tagja (az ASP.NET `HttpContext.User`)
- innen az MSAL.NET buildek `IConfidentialClientApplication`
- IT-hívás a `AcquireTokenSilent` metódus az bizalmas ügyfélalkalmazás 

a kód hasonlít az ASP.NET Core látható kód

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Webes API-hívás](scenario-web-app-call-api-call-api.md)
