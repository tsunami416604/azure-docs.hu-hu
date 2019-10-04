---
title: Beleegyezik több erőforráshoz (Microsoft Authentication Library for .NET) | Azure
description: Megtudhatja, hogy a felhasználók hogyan kaphatnak előzetes beleegyezett több erőforrásra a Microsoft Authentication Library for .NET (MSAL.NET) használatával.
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
ms.date: 04/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ded7a6fc465b4cfc98d26f65195f89de8381ac6
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532372"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>A felhasználó beleegyezik több erőforráshoz a MSAL.NET használatával
A Microsoft Identity platform végpontja nem teszi lehetővé, hogy egyszerre több erőforráshoz kapjon tokent. A .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatakor a beszerzési jogkivonat metódus Scopes paraméterének csak egyetlen erőforráshoz tartozó hatóköröket kell tartalmaznia. A `.WithExtraScopeToConsent` Builder metódussal azonban további hatóköröket is megadhat, ha előre beleegyezett több erőforráshoz.

> [!NOTE]
> A Microsoft Identity platform számos erőforrásának beszerzése, de nem Azure AD B2C. A Azure AD B2C csak a rendszergazdai jogosultságokat támogatja, a felhasználói beleegyezett nem.

Ha például két olyan erőforrással rendelkezik, amelyek mindegyike 2 hatókörrel rendelkezik:

- https:\//mytenant.onmicrosoft.com/customerapi (2 hatókörrel `customer.read` és `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (2 hatókörrel `vendor.read` és `vendor.write`)

Az `.WithExtraScopeToConsent` alábbi példában látható módon a *extraScopesToConsent* paraméterrel rendelkező módosítót kell használnia:

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

Ez egy hozzáférési jogkivonatot kap az első webes API-hoz. Ezután, amikor el kell érnie a második webes API-t, a jogkivonat-gyorsítótárból csendesen is beszerezheti a tokent:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
