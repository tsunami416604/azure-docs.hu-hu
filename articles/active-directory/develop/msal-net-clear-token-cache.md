---
title: Jogkivonat-gyorsítótár törlése (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan törölheti a jogkivonat-gyorsítótárat a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 83c1dd43235dc7bccb322a484362b08544d54d11
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85477515"
---
# <a name="clear-the-token-cache-using-msalnet"></a>A jogkivonat-gyorsítótár kiürítése a MSAL.NET használatával

Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával szerzi be a [hozzáférési jogkivonatot](msal-acquire-cache-tokens.md) , a rendszer gyorsítótárazza a tokent. Ha az alkalmazásnak tokenre van szüksége, először hívja meg a `AcquireTokenSilent` metódust annak ellenőrzéséhez, hogy elfogadható token van-e a gyorsítótárban. 

A gyorsítótár kiürítésével a fiókok a gyorsítótárból való eltávolításával érhetők el. Ez nem távolítja el a böngészőben található munkamenet-cookie-t, de.  A következő példa egy nyilvános ügyfélalkalmazás létrehozását, beolvassa az alkalmazás fiókját, és eltávolítja a fiókokat.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

A tokenek beszerzésével és gyorsítótárazásával kapcsolatos további tudnivalókért olvassa el a [hozzáférési token beszerzése](msal-acquire-cache-tokens.md)című témakört.
