---
title: A tokengyorsítótár törlése (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként törölheti a tokengyorsítótárat a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: a10efb5ff0a2c6a3ced3631dfe82c86e3e8a72fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084770"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Törölje a tokengyorsítótárat MSAL.NET használatával

Amikor a Microsoft Authentication Library for .NET (MSAL.NET) segítségével [szerez be hozzáférési jogkivonatot,](msal-acquire-cache-tokens.md) a rendszer gyorsítótárba helyezi a tokenet. Ha az alkalmazásnak szüksége van egy `AcquireTokenSilent` jogkivonatra, először meg kell hívnia a metódust, hogy ellenőrizze, hogy egy elfogadható jogkivonat van-e a gyorsítótárban. 

A gyorsítótár törlése a fiókok gyorsítótárból való eltávolításával érhető el. Ez azonban nem távolítja el a munkamenet-cookie-t, amely a böngészőben található.  A következő példa egy nyilvános ügyfélalkalmazást hoz végre, lekéri az alkalmazás fiókjait, és eltávolítja a fiókokat.

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

Ha többet szeretne megtudni a jogkivonatok beszerzéséről és gyorsítótárazásáról, olvassa el [a hozzáférési jogkivonat beszerzése](msal-acquire-cache-tokens.md)olvasását.
