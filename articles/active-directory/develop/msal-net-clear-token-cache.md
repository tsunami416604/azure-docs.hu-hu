---
title: A jogkivonat-gyorsítótár kiürítése a MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan törölheti a jogkivonat-gyorsítótárat a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f203bc057482466f6bddc7523c0ec7a7e9404ccc
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915927"
---
# <a name="clear-the-token-cache-using-msalnet"></a>A jogkivonat-gyorsítótár kiürítése a MSAL.NET használatával

Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával szerzi be a [hozzáférési jogkivonatot](msal-acquire-cache-tokens.md) , a rendszer gyorsítótárazza a tokent. Ha az alkalmazásnak tokenre van szüksége, először meg kell hívnia a `AcquireTokenSilent` metódust annak ellenőrzéséhez, hogy elfogadható token van-e a gyorsítótárban. 

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
