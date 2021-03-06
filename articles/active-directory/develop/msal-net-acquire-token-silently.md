---
title: Jogkivonat beszerzése a gyorsítótárból (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szerezheti be csendesen a hozzáférési tokent (a jogkivonat-gyorsítótárból) a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 95cfb937ce236d0acd1a3369068afc6f3b505aed
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88166246"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Jogkivonat beszerzése a jogkivonat-gyorsítótárból a MSAL.NET használatával

Ha a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával szerzi be a hozzáférési jogkivonatot, a rendszer gyorsítótárazza a tokent. Ha az alkalmazásnak tokenre van szüksége, először hívja meg a `AcquireTokenSilent` metódust annak ellenőrzéséhez, hogy elfogadható token van-e a gyorsítótárban. Sok esetben lehetséges, hogy a gyorsítótárban lévő tokenen alapuló további hatókörökkel is beszerezhető egy másik jogkivonat. Az is lehetséges, hogy a jogkivonatot a lejárati idő lezárása után is frissíteni kell (mivel a jogkivonat-gyorsítótár frissítési jogkivonatot is tartalmaz).

A javasolt minta a metódus első meghívása `AcquireTokenSilent` .  Ha `AcquireTokenSilent` a művelet sikertelen, akkor más metódusok használatával szerezzen be egy jogkivonatot.

Az alábbi példában az alkalmazás először a jogkivonat-gyorsítótárból kísérli meg a jogkivonat beszerzését.  Ha kivétel keletkezik `MsalUiRequiredException` , az alkalmazás interaktív módon szerzi be a tokent. 

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
