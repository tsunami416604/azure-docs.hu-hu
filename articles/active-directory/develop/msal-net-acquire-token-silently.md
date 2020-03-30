---
title: Token beszerzése a gyorsítótárból (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy miként szerezhet be egy hozzáférési jogkivonatot csendben (a tokengyorsítótárból) a Microsoft Authentication Library for .NET (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 90189a1d7fd6421b7a24940e8c6ed615fa0df6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77084835"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Token beolvasása a tokengyorsítótárból MSAL.NET

Amikor a Microsoft Authentication Library for .NET (MSAL.NET) segítségével szerez be hozzáférési jogkivonatot, a rendszer gyorsítótárba helyezi a tokenet. Ha az alkalmazásnak szüksége van egy `AcquireTokenSilent` jogkivonatra, először meg kell hívnia a metódust, hogy ellenőrizze, hogy egy elfogadható jogkivonat van-e a gyorsítótárban. Sok esetben lehetséges egy másik jogkivonat beszerzése több hatókört a gyorsítótárban lévő jogkivonat alapján. A jogkivonat frissítése is lehetséges, amikor a lejárati idő höz közeledik (mivel a jogkivonat-gyorsítótár egy frissítési jogkivonatot is tartalmaz).

Az ajánlott minta `AcquireTokenSilent` az, hogy hívja meg a metódust először.  Ha `AcquireTokenSilent` nem sikerül, majd szerezzen be egy jogkivonatot más módszerekkel.

A következő példában az alkalmazás először megkísérli a jogkivonat beszerzése a jogkivonat-gyorsítótárból.  Ha `MsalUiRequiredException` kivétel történik, az alkalmazás interaktív jogkivonatot szerez be. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
