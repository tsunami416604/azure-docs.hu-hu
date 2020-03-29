---
title: Fiókok eltávolítása a tokengyorsítótárból a kijelentkezéskor – Microsoft identity platform | Azure
description: További információ arról, hogyan távolíthat el egy fiókot a tokengyorsítótárból a kijelentkezéskor
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
ms.date: 09/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea18538662dc63876a50f52e9e6a8b3fffb3b35a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76758870"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webes API-kat meghívjaó webalkalmazás: Fiókok eltávolítása a tokengyorsítótárból globális kijelentkezéskor

Megtanulta, hogyan vehet fel bejelentkezést a webalkalmazásba a [felhasználók: Bejelentkezés és kijelentkezés a webalkalmazásban.](scenario-web-app-sign-user-sign-in.md)

A kijelentkezés más, mint egy webalkalmazás, amely meghívja a webes api-kat. Amikor a felhasználó kijelentkezik az alkalmazásból, vagy bármely alkalmazásból, el kell távolítania a jogkivonat-gyorsítótárból a felhasználóhoz társított jogkivonatokat.

## <a name="intercept-the-callback-after-single-sign-out"></a>A visszahívás elfogása egyszeri kijelentkezés után

A kijelentkező fiókhoz társított token-cache bejegyzés törléséhez az `logout` alkalmazás elfogja az esemény utáni eseményt. A webalkalmazások minden felhasználó számára hozzáférési jogkivonatokat tárolnak egy jogkivonat-gyorsítótárban. A visszahívás utáni `logout` lehallgatással a webalkalmazás eltávolíthatja a felhasználót a gyorsítótárból.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

A ASP.NET Core esetében az elfogási mechanizmust a `AddMsal()` [WebAppServiceCollectionExtensions.cs#L151-L157 metódus szemlélteti.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)

A kijelentkezési URL-cím, amely korábban regisztrált az alkalmazás lehetővé teszi, hogy egyetlen kijelentkezés megvalósítása. A Microsoft identity platform `logout` végpontja meghívja a kijelentkezési URL-címet. Ez a hívás akkor történik, ha a kijelentkezés a webalkalmazásból, egy másik webalkalmazásból vagy a böngészőből indult. További információ: [Single-sign-out](v2-protocols-oidc.md#single-sign-out).

```csharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: Remove the account from MSAL.NET cache.
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache.
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

A kód `RemoveAccountAsync` a [Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)webhelyen érhető el.

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="java"></a>[Java](#tab/java)

A Java minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="python"></a>[Python](#tab/python)

A Python-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

---

## <a name="next-steps"></a>További lépések

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
