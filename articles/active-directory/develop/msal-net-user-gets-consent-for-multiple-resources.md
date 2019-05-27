---
title: Jóváhagyás lekérése több erőforrás (Microsoft-hitelesítési tár .NET) |} Az Azure
description: Ismerje meg, hogy a felhasználók miként juthatnak előzetes beleegyezés kérése a Microsoft-hitelesítési tár .NET (MSAL.NET) használatával több erőforrás.
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
ms.openlocfilehash: e8bd9a86d5ec0d39a7f1c26adac52f41e6420283
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121989"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>Felhasználói jóváhagyás az MSAL.NET használatával több erőforrás beolvasása
A Microsoft identity platform végpont nem engedélyezi, hogy egyszerre több erőforrás lekérése egy token. A Microsoft-hitelesítési tár .NET (MSAL.NET) használata esetén a beolvasási token módszer a hatókörök paramétere csak tartalmaznia kell egy adott erőforrás hatókörök. Azonban meg előre engedélyezhetik, hogy előre a számos erőforrás használatával további hatókörökkel megadásával a `.WithExtraScopeToConsent` builder metódust.

> [!NOTE]
> Jóváhagyás lekérdezése több erőforrások működését a Microsoft identity platform, de nem Azure AD B2C-t. Az Azure AD B2C csak rendszergazdai jóváhagyás, nem a felhasználói beleegyezés támogatja.

Ha például két olyan erőforrásokat, ha a 2 hatókörök minden:

- https:\//mytenant.onmicrosoft.com/customerapi (2 hatókörökkel `customer.read` és `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (2 hatókörökkel `vendor.read` és `vendor.write`)

Használjon a `.WithExtraScopeToConsent` módosító, amelyen a *extraScopesToConsent* paramétert az alábbi példában látható módon:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Ez lesz, hozzáférési jogkivonatot kapjon az első webes API-hoz. Majd ha a második webes API-k beavatkozás nélkül is el kell beszerezni a jogkivonatot a jogkivonat-gyorsítótárból:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
