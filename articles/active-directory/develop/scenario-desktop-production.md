---
title: A webes API-k az éles környezetben való hívása a Microsoft Identity platformba | Azure
description: Ismerje meg, hogyan helyezhet át egy olyan asztali alkalmazást, amely webes API-kat hív meg éles környezetben
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: ea564eb69f102d8e548bf8ae9a626598fa264cd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882879"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívó asztali alkalmazás: áthelyezés éles környezetbe

Ebből a cikkből megtudhatja, hogyan helyezheti át az asztali alkalmazást, amely webes API-kat hív meg éles környezetben.

## <a name="handle-errors-in-desktop-applications"></a>Hibák kezelése asztali alkalmazásokban

A különböző folyamatokban megtanulta, hogyan kezelheti a csendes folyamatok hibáit, ahogy azt a kódrészletek is szemléltetik. Azt is láthatta, hogy vannak olyan esetek, amikor beavatkozásra van szükség, a növekményes beleegyező és feltételes hozzáférés esetén.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>A felhasználó beleegyezett több erőforrásra

> [!NOTE]
> A Microsoft Identity platform számos erőforrásának beszerzése, de Azure Active Directory (Azure AD) B2C esetében nem. A Azure AD B2C csak a rendszergazdai jogosultságokat támogatja, a felhasználói beleegyezett nem.

A Microsoft Identity platform (v 2.0) végpontján egyszerre több erőforráshoz nem kaphat tokent. A `scopes` paraméter csak egyetlen erőforráshoz tartalmazhat hatóköröket. A paraméter használatával biztosíthatja, hogy a felhasználó előre beleegyezett több erőforrásba `extraScopesToConsent` .

Előfordulhat például, hogy két erőforrással rendelkezik, amelyek mindegyike két hatókörrel rendelkezik:

- `https://mytenant.onmicrosoft.com/customerapi`a hatókörökkel `customer.read` és`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`a hatókörökkel `vendor.read` és`vendor.write`

Ebben a példában a `.WithAdditionalPromptToConsent` paraméterrel rendelkező módosítót használja `extraScopesToConsent` .

Ilyenek például a következők:

### <a name="in-msalnet"></a>A MSAL.NET

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

### <a name="in-msal-for-ios-and-macos"></a>Az iOS és a macOS rendszerhez készült MSAL

Objective-C:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Ez a hívás egy hozzáférési jogkivonatot kap az első webes API-hoz.

Ha meg kell hívnia a második webes API-t, hívja meg az API-t `AcquireTokenSilent` .

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>A Microsoft személyes fiókjának minden egyes futtatásakor újra kell egyeznie

A személyes Microsoft-fiókok felhasználói számára a kívánt viselkedés az összes natív ügyfél (asztali vagy mobil alkalmazás) hívása, amely engedélyezi a jóváhagyást. A natív ügyfél-identitás eredendően nem biztonságos, ami ellentétben áll a bizalmas ügyfélalkalmazás identitásával. A bizalmas ügyfélalkalmazások a Microsoft Identity platformmal titokban cserélik identitását. A Microsoft Identity platform úgy döntött, hogy csökkenti a fogyasztói szolgáltatások biztonságának kockázatát azáltal, hogy minden alkalommal, amikor az alkalmazás engedélyt kap, a felhasználó hozzájárulását kéri.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
