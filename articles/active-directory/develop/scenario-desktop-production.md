---
title: Asztali alkalmazás hívásának webes API-jainak áthelyezése éles környezetbe – Microsoft identity platform | Azure
description: Megtudhatja, hogy miként helyezhet át webes API-kat éles környezetbe hívó asztali alkalmazásokat
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882879"
---
# <a name="desktop-app-that-calls-web-apis-move-to-production"></a>Webes API-kat meghívjaó asztali alkalmazás: Ugrás éles környezetbe

Ebből a cikkből megtudhatja, hogyan helyezheti át az asztali alkalmazást, amely webes API-kat hív éles környezetbe.

## <a name="handle-errors-in-desktop-applications"></a>Az asztali alkalmazások hibáinak kezelése

A különböző folyamatok, megtanulta, hogyan kell kezelni a hibákat a csendes folyamatok, ahogy az a kódkódrészletek. Azt is látta, hogy vannak olyan esetek, ahol interakcióra van szükség, mint a növekményes hozzájárulás és a feltételes hozzáférés.

## <a name="have-the-user-consent-upfront-for-several-resources"></a>A felhasználó előzetes hozzájárulása több erőforráshoz

> [!NOTE]
> Több erőforrás beleegyezésének beszerzése a Microsoft Identity platformhoz működik, de az Azure Active Directory (Azure AD) B2C-hez nem. Az Azure AD B2C csak a rendszergazdai jóváhagyást támogatja, a felhasználó beleegyezését nem.

A Microsoft identity platform (2.0- as verzió) végpontjával nem kaphat jogkivonatot egyszerre több erőforráshoz. A `scopes` paraméter csak egyetlen erőforrás hatóköreit tartalmazhatja. A paraméter használatával biztosíthatja, hogy a felhasználó `extraScopesToConsent` a paraméter használatával előzetesen beleegyezik több erőforráshoz.

Előfordulhat például, hogy két erőforrással rendelkezik, amelyek mindegyike két hatókörrel rendelkezik:

- `https://mytenant.onmicrosoft.com/customerapi`a hatályokkal `customer.read` és a`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`a hatályokkal `vendor.read` és a`vendor.write`

Ebben a példában `.WithAdditionalPromptToConsent` használja a `extraScopesToConsent` paramétert tartalmazó módosítót.

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

### <a name="in-msal-for-ios-and-macos"></a>Az MSAL-ban iOS és macOS rendszeren

C célkitűzés:

```objc
NSArray *scopesForCustomerApi = @[@"https://mytenant.onmicrosoft.com/customerapi/customer.read",
                                @"https://mytenant.onmicrosoft.com/customerapi/customer.write"];

NSArray *scopesForVendorApi = @[@"https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                              @"https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

MSALInteractiveTokenParameters *interactiveParams = [[MSALInteractiveTokenParameters alloc] initWithScopes:scopesForCustomerApi webviewParameters:[MSALWebviewParameters new]];
interactiveParams.extraScopesToConsent = scopesForVendorApi;
[application acquireTokenWithParameters:interactiveParams completionBlock:^(MSALResult *result, NSError *error) { /* handle result */ }];
```

Swift:

```swift
let scopesForCustomerApi = ["https://mytenant.onmicrosoft.com/customerapi/customer.read",
                            "https://mytenant.onmicrosoft.com/customerapi/customer.write"]

let scopesForVendorApi = ["https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
                          "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"]

let interactiveParameters = MSALInteractiveTokenParameters(scopes: scopesForCustomerApi, webviewParameters: MSALWebviewParameters())
interactiveParameters.extraScopesToConsent = scopesForVendorApi
application.acquireToken(with: interactiveParameters, completionBlock: { (result, error) in /* handle result */ })
```

Ez a hívás kap egy hozzáférési jogkivonatot az első webes API-t.

Ha meg kell hívnia a második `AcquireTokenSilent` webes API-t, hívja meg az API-t.

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsent-each-time-the-app-runs"></a>A Microsoft személyes fiókjának újbóli beleegyezése szükséges az alkalmazás minden futtatásakor

A Microsoft személyes fiók felhasználói számára a szándékolt viselkedés az, ha minden natív ügyfél (asztali vagy mobilalkalmazás) hívás engedélyezéséhez minden natív ügyfél (asztali vagy mobilalkalmazás) hívásengedélyezéséhez újra kéri a beleegyezést. A natív ügyfélidentitás eredendően nem biztonságos, ami ellentétes a bizalmas ügyfélalkalmazás-identitással. A bizalmas ügyfélalkalmazások titkos kapcsolatot cserélnek a Microsoft Identity platformmal személyazonosságuk igazolására. A Microsoft identity platform úgy döntött, hogy enyhíti ezt a bizonytalanságot a fogyasztói szolgáltatások által kéri a felhasználó beleegyezését minden egyes alkalommal, amikor az alkalmazás engedélyezett.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
