---
title: Webalkalmazás, hogy a hívások webes API-k (bejelentkezés) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy webalkalmazást, amely meghívja a webes API-k (bejelentkezés)
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
ms.openlocfilehash: 663cea72eb620217ad5fa8925d3bb00eedbf890c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074560"
---
# <a name="web-app-that-calls-web-apis---sign-in"></a>Webes alkalmazás, amely meghívja a webes API-k – bejelentkezés

Már ismeri a webalkalmazás bejelentkezési hozzáadása. Ismerje meg, hogy a [, hogy a felhasználók jelentkezik be – bejelentkezés felvétele webalkalmazás](scenario-web-app-sign-user-sign-in.md).

Milyen különböző itt, a, hogy a felhasználó bejelentkezett, el, az alkalmazástól vagy bármely olyan alkalmazásból, amikor el kívánja távolítani a tokent gyorsítótárból, a tokenek a felhasználóhoz társított.

## <a name="intercepting-the-callback-after-sign-out---single-sign-out"></a>A visszahívás elfogja után jelentkezzen ki – egyszeri kijelentkezés

Az alkalmazás tudja fogni a után `logout` esemény, például a bejegyzés a jogkivonat-kijelentkeztetése fiókhoz tartozó gyorsítótár törléséhez. Láthatjuk, hogy ez az oktatóanyag (a webalkalmazás, webes API meghívása) második részében, az, hogy a webalkalmazás a gyorsítótárban tárolja a felhasználó vonatkozó hozzáférési jogkivonatait. Elfogja a után `logout` visszahívási lehetővé teszi, hogy a webes alkalmazásba a felhasználói jogkivonat gyorsítótárból való eltávolításához. Ez a mechanizmus mutatja be a `AddMsal()` metódusa [StartupHelper.cs L137-143](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/b87a1d859ff9f9a4a98eb7b701e6a1128d802ec5/Microsoft.Identity.Web/StartupHelpers.cs#L137-L143)

A **kijelentkezési URL-címe** , hogy az alkalmazás egyszeri kijelentkezési megvalósítását teszi regisztrálta. A Microsoft identity platform `logout` végpont hívni fogja a **kijelentkezési URL-címe** az alkalmazás regisztrálva. Ez a hívás akkor fordul elő, ha a Kijelentkezés kezdeményezte a webalkalmazásból, illetve egy másik webes alkalmazást vagy a böngésző. További információkért lásd: [egyszeri kijelentkezéshez](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc#single-sign-out) a fogalmi dokumentációjában.

```CSharp
public static IServiceCollection AddMsal(this IServiceCollection services, IEnumerable<string> initialScopes)
{
    services.AddTokenAcquisition();

    services.Configure<OpenIdConnectOptions>(AzureADDefaults.OpenIdScheme, options =>
    {
     ...
        // Handling the sign-out: removing the account from MSAL.NET cache
        options.Events.OnRedirectToIdentityProviderForSignOut = async context =>
        {
            // Remove the account from MSAL.NET token cache
            var _tokenAcquisition = context.HttpContext.RequestServices.GetRequiredService<ITokenAcquisition>();
            await _tokenAcquisition.RemoveAccount(context);
        };
    });
    return services;
}
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A webalkalmazás egy token beszerzése](scenario-web-app-call-api-acquire-token.md)
