---
title: Fiókok eltávolítása a jogkivonat-gyorsítótárból a kijelentkezéskor – Microsoft Identity platform | Azure
description: Ismerje meg, hogyan távolíthat el egy fiókot a jogkivonat-gyorsítótárból a kijelentkezéskor
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2020
ms.locfileid: "76758870"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Webes API-kat meghívó webalkalmazás: fiókok eltávolítása a jogkivonat-gyorsítótárból a globális kijelentkezéskor

Megtanulta, hogyan adhat hozzá bejelentkezést a webalkalmazáshoz a webalkalmazásban, amely bejelentkezik [a felhasználókba: bejelentkezés és](scenario-web-app-sign-user-sign-in.md)kijelentkezés.

A webes API-kat meghívó webalkalmazások esetében a kijelentkezés nem egyezik. Ha a felhasználó kijelentkezik az alkalmazásból, vagy bármely alkalmazásból, el kell távolítania az adott felhasználóhoz társított jogkivonatokat a jogkivonat-gyorsítótárból.

## <a name="intercept-the-callback-after-single-sign-out"></a>Visszahívás elfogása az egyszeri kijelentkezés után

A kijelentkezett fiókhoz társított jogkivonat-gyorsítótár-bejegyzés törléséhez az alkalmazás a következő `logout` eseményt fogja feltartóztatni. A Web Apps tárolja a hozzáférési jogkivonatokat a jogkivonat-gyorsítótárban lévő egyes felhasználók számára. Az `logout` visszahívás utáni elfogásával a webalkalmazás el tudja távolítani a felhasználót a gyorsítótárból.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

ASP.NET Core esetében az elfogó mechanizmus a [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157)`AddMsal()` metódusában látható.

Az alkalmazáshoz korábban regisztrált kijelentkezési URL-cím lehetővé teszi az egyszeri kijelentkezés megvalósítását. A Microsoft Identity platform `logout` végpontja meghívja a kijelentkezési URL-címet. Ez a hívás akkor fordul elő, ha a kijelentkezést a webalkalmazásból vagy egy másik webalkalmazásból vagy a böngészőből indította el. További információ: [egyszeri kijelentkezés](v2-protocols-oidc.md#single-sign-out).

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

A `RemoveAccountAsync` kódja a [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288)címen érhető el.

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor.

---

## <a name="next-steps"></a>Következő lépések

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Jogkivonat beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
