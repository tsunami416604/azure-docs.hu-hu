---
title: Jogkivonat beszerzése csendesen (Microsoft Authentication Library for .NET)
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogyan szerezheti be csendesen a hozzáférési tokent (a jogkivonat-gyorsítótárból) a .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatával.
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1dc573c37aab95006f647509080fb9883d8650c7
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802940"
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
