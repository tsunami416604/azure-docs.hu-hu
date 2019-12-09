---
title: Token csendes beszerzése (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szerezheti be csendesen a hozzáférési tokent (a jogkivonat-gyorsítótárból) a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9c988eba278e184d0e60a4ede456a340c258e71
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915976"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Jogkivonat beszerzése a jogkivonat-gyorsítótárból a MSAL.NET használatával

Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával szerzi be a hozzáférési jogkivonatot, a rendszer gyorsítótárazza a tokent. Ha az alkalmazásnak tokenre van szüksége, először meg kell hívnia a `AcquireTokenSilent` metódust annak ellenőrzéséhez, hogy elfogadható token van-e a gyorsítótárban. Sok esetben lehetséges, hogy a gyorsítótárban lévő tokenen alapuló további hatókörökkel is beszerezhető egy másik jogkivonat. Az is lehetséges, hogy a jogkivonatot a lejárati idő lezárása után is frissíteni kell (mivel a jogkivonat-gyorsítótár frissítési jogkivonatot is tartalmaz).

A javasolt minta az `AcquireTokenSilent` metódus első meghívása.  Ha `AcquireTokenSilent` meghibásodik, akkor más metódusok használatával szerezzen be egy jogkivonatot.

Az alábbi példában az alkalmazás először a jogkivonat-gyorsítótárból kísérli meg a jogkivonat beszerzését.  Ha `MsalUiRequiredException` kivétel keletkezik, az alkalmazás interaktív módon szerzi be a tokent. 

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
