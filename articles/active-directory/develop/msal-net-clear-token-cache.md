---
title: A token gyorsítótár ürítése a .NET-hez - Azure Microsoft-hitelesítési tár használatával
description: 'Útmutató: a jogkivonat gyorsítótár használatával a Microsoft-hitelesítési tár .NET (MSAL.NET).'
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: celested
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1604d2833387b105fc7897a89f96ebcf9486d6a8
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65468469"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Az MSAL.NET használatával token gyorsítótár ürítése

Ha Ön [hozzáférési jogkivonat beszerzése](msal-acquire-cache-tokens.md) használja Microsoft-hitelesítési tár .NET (MSAL.NET), a jogkivonatot a rendszer gyorsítótárazza. Ha az alkalmazás egy jogkivonatot, azt először meghívja a `AcquireTokenSilent` metódust annak ellenőrzéséhez, ha egy elfogadható jogkivonat a gyorsítótárban. 

A gyorsítótár kiürítése érik el a fiókokat eltávolítása a gyorsítótárból. A munkamenetcookie-t, amely a böngészőben, azonban ez nem távolítja el.  Az alábbi példa példányosítja az ügyfélalkalmazások nyilvános, az alkalmazás lekéri a fiókokat, és eltávolítja a fiókok.

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

További információ beszerzéséhez és a jogkivonatok gyorsítótárazását, olvassa el [hozzáférési jogkivonat beszerzése](msal-acquire-cache-tokens.md).
