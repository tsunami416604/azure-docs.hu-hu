---
title: Webes API-kat meghívó webalkalmazás (bejelentkezés) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó webalkalmazást (bejelentkezés)
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
ms.openlocfilehash: 3036f8cb72f2a07673743a77e8be37614002563f
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720201"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Webes API-kat meghívó webalkalmazás – bejelentkezés

Már tudja, hogyan adhat hozzá bejelentkezést a webalkalmazáshoz. Megtudhatja, hogy a [webalkalmazásban, amely bejelentkezik a felhasználóknak – bejelentkezés hozzáadása](scenario-web-app-sign-user-sign-in.md).

Mi a különbség itt, hogy amikor a felhasználó kijelentkezett, ebből az alkalmazásból vagy bármely alkalmazásból, el szeretné távolítani a jogkivonat-gyorsítótárból a felhasználóhoz társított jogkivonatokat.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>A visszahívás elfogása a kijelentkezés után – egyszeri kijelentkezés

Az alkalmazás a `logout` eseményt követően képes feltartóztatni, például törli a kijelentkezett fiókhoz társított jogkivonat-gyorsítótár bejegyzését. A webalkalmazás a gyorsítótárban tárolja a felhasználó hozzáférési jogkivonatait. A `logout` visszahívást követően a webalkalmazás eltávolítja a felhasználót a jogkivonat-gyorsítótárból.

# <a name="aspnet-coretabaspnetcore"></a>[ASP.NET Core](#tab/aspnetcore)

Ez @no__t a mechanizmus a [WebAppServiceCollectionExtensions. cs # L151-L157](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/db7f74fd7e65bab9d21092ac1b98a00803e5ceb2/Microsoft.Identity.Web/WebAppServiceCollectionExtensions.cs#L151-L157) -0 metódusban látható.

Az alkalmazáshoz regisztrált **kijelentkezési URL-cím** lehetővé teszi az egyszeri kijelentkezés megvalósítását. A Microsoft Identity platform `logout` végpont hívja meg az alkalmazásban regisztrált **kijelentkezési URL-címet** . Ez a hívás akkor fordul elő, ha a kijelentkezést a webalkalmazásból vagy egy másik webalkalmazásból vagy a böngészőből kezdeményezték. További információ: [egyszeri kijelentkezés](v2-protocols-oidc.md#single-sign-out).

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

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Token beszerzése a webalkalmazáshoz](scenario-web-app-call-api-acquire-token.md)
