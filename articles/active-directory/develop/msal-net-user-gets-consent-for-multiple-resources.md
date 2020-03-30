---
title: Több erőforráshoz (MSAL.NET) vonatkozó hozzájárulás beszerezése | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg, hogyan kaphat a felhasználó előzetes hozzájárulást több erőforráshoz a Microsoft Authentication Library for .NET (MSAL.NET) segítségével.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 94c9a2b6a46262ad293da9ca3ba493d6f898c870
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085834"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>A felhasználó több erőforráshoz is engedélyt kap MSAL.NET
A Microsoft identity platform végpont jattot, hogy egyszerre több erőforrás jogkivonatot. A Microsoft Authentication Library for .NET (MSAL.NET) használatakor a beszerzési jogkivonat metódusban lévő hatókörparaméter csak egyetlen erőforrás hatóköreit tartalmazhatja. Azonban előzetesen is hozzájárulhat több erőforráshoz előre, ha `.WithExtraScopeToConsent` további hatóköröket aszerkesztő i.

> [!NOTE]
> Több erőforrás beleegyezése a Microsoft identity platformhoz, de az Azure AD B2C-hez nem működik. Az Azure AD B2C csak a rendszergazdai jóváhagyást támogatja, a felhasználó beleegyezését nem.

Ha például két erőforrással rendelkezik, amelyek egyenként 2 hatókörrel rendelkeznek:

- https:\//mytenant.onmicrosoft.com/customerapi (2 `customer.read` hatókörökkel és `customer.write`)
- https:\//mytenant.onmicrosoft.com/vendorapi (2 `vendor.read` hatókörökkel és `vendor.write`)

Az `.WithExtraScopeToConsent` *extraScopesToConsent* paraméterrel rendelkező módosítót kell használnia a következő példában látható módon:

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

Ez kap egy hozzáférési jogkivonatot az első webes API-t. Ezután, amikor hozzá kell férned a második webes API-hoz, csendben beszerezheti a jogkivonatot a jogkivonat-gyorsítótárból:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
