---
title: Fiókok eltávolítása a gyorsítótárból a kijelentkezéskor – Microsoft Identity platform | Azure
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 375382b1245186197657c5067e3c5b4ec2b15655
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74961658"
---
# <a name="remove-accounts-from-the-cache-on-global-sign-out"></a>Fiókok eltávolítása a gyorsítótárból a globális kijelentkezéskor

Már tudja, hogyan adhat hozzá bejelentkezést a webalkalmazáshoz. Megtudhatja, hogy a [webalkalmazásban, amely bejelentkezik a felhasználóknak – bejelentkezés hozzáadása](scenario-web-app-sign-user-sign-in.md).

Mi a különbség itt, hogy amikor a felhasználó kijelentkezett, ebből az alkalmazásból vagy bármely alkalmazásból, el szeretné távolítani a jogkivonat-gyorsítótárból a felhasználóhoz társított jogkivonatokat.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>A visszahívás elfogása a kijelentkezés után – egyszeri kijelentkezés

Az alkalmazás elfogja a `logout` eseményt, így például törli a kijelentkezett fiókhoz társított jogkivonat-gyorsítótár bejegyzését. A webalkalmazás a gyorsítótárban tárolja a felhasználó hozzáférési jogkivonatait. A `logout` visszahívás utáni elfogása lehetővé teszi, hogy a webalkalmazás eltávolítsa a felhasználót a jogkivonat-gyorsítótárból.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ez `AddMsal()` a mechanizmus a [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157) metódusban látható.

Az alkalmazáshoz regisztrált **kijelentkezési URL-cím** lehetővé teszi az egyszeri kijelentkezés megvalósítását. A Microsoft Identity platform `logout` végpontja hívja meg az alkalmazásban regisztrált **kijelentkezési URL-címet** . Ez a hívás akkor fordul elő, ha a kijelentkezést a webalkalmazásból vagy egy másik webalkalmazásból vagy a böngészőből kezdeményezték. További információ: [egyszeri kijelentkezés](v2-protocols-oidc.md#single-sign-out).

```CSharp
public static class WebAppServiceCollectionExtensions
{
 public static IServiceCollection AddMsal(this IServiceCollection services, IConfiguration configuration, IEnumerable<string> initialScopes, string configSectionName = "AzureAd")
 {
  // Code omitted here

  services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
  {
   // Code omitted here

   // Handling the sign-out: removing the account from MSAL.NET cache
   options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
   {
    // Remove the account from MSAL.NET token cache
    var tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
    await tokenAcquisition.RemoveAccountAsync(context).ConfigureAwait(false);
   };
  });
  return services;
 }
}
```

A RemoveAccountAsync kódja elérhető a [Microsoft. Identity. Web/TokenAcquisition. cs # L264-L288](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/TokenAcquisition.cs#L264-L288).

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

A ASP.NET minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor

# <a name="javatabjava"></a>[Java](#tab/java)

A Java-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor

# <a name="pythontabpython"></a>[Python](#tab/python)

A Python-minta nem távolítja el a fiókokat a gyorsítótárból a globális kijelentkezéskor

---

## <a name="next-steps"></a>Következő lépések

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnettabaspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="javatabjava"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="pythontabpython"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
