---
title: Jogkivonat-beszerzési csendes (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Ismerje meg, hogyan használja a Microsoft-hitelesítési tár .NET (MSAL.NET) csendes módban (a gyorsítótárból token) hozzáférési jogkivonat beszerzése.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/30/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6331407067a39550d866d7c293a92fac9184b54e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544236"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>A token gyorsítótárból MSAL.NET használatával egy token beszerzése

Használatával a Microsoft-hitelesítési tár .NET (MSAL.NET) hozzáférési jogkivonatot szerez be, amikor a rendszer gyorsítótárazza a jogkivonat. Ha az alkalmazás egy jogkivonatot, azt először meghívja a `AcquireTokenSilent` metódust annak ellenőrzéséhez, ha egy elfogadható jogkivonat a gyorsítótárban. Sok esetben egy másik jogkivonatot beszerezni egy jogkivonat a gyorsítótárban alapján további hatókörökkel. Akkor is egy jogkivonat frissítése, ha azt hamarosan lejár az első (mivel a jogkivonatok gyorsítótárának is tartalmaz egy frissítési jogkivonat).

A javasolt egyik hívja a `AcquireTokenSilent` metódus első.  Ha `AcquireTokenSilent` meghiúsul, majd más módszerekkel jogkivonat beszerzése.

Az alkalmazás a következő példában először megkísérli a token a gyorsítótárból egy token beszerzéséhez.  Ha egy `MsalUiRequiredException` kivétel történt, az alkalmazás tokenbeolvasás interaktív módon. 

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
 // A MsalUiRequiredException happened on AcquireTokenSilentAsync.
 // This indicates you need to call AcquireTokenAsync to acquire a token
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