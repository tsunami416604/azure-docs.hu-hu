---
title: Webes API-kat meghívó asztali alkalmazás (áttérés éles környezetbe) – Microsoft Identity platform
description: Megtudhatja, hogyan hozhat létre webes API-kat meghívó asztali alkalmazást (az éles környezetbe)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a353b4577f8cfa9ba279ad2793e1a7ab8b27e55
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268333"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Webes API-kat meghívó asztali alkalmazás – áttérés éles környezetbe

Ez a cikk részletesen ismerteti az alkalmazás további fejlesztését és az éles környezetbe való áthelyezését.

## <a name="handling-errors-in-desktop-applications"></a>Hibák feldolgozása asztali alkalmazásokban

A különböző folyamatokban megtanulta, hogyan kezelheti a csendes folyamatok hibáit (a kódrészletekben látható módon). Azt is láthatta, hogy vannak olyan esetek, amikor interakcióra van szükség (növekményes beleegyezett és feltételes hozzáférés).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>A felhasználó beleegyezett több erőforráshoz

> [!NOTE]
> A Microsoft Identity platform számos erőforrásának beszerzése, de Azure Active Directory (Azure AD) B2C esetében nem. A Azure AD B2C csak a rendszergazdai jogosultságokat támogatja, a felhasználói beleegyezett nem.

A Microsoft Identity platform (v 2.0) végpontja nem teszi lehetővé, hogy egyszerre több erőforráshoz kapjon tokent. Ezért a `scopes` paraméter csak egyetlen erőforrás hatóköreit tartalmazhatja. A `extraScopesToConsent` paraméter használatával biztosíthatja, hogy a felhasználó előre beleegyezett több erőforrásba.

Ha például két erőforrással rendelkezik, amelyek mindegyike két hatókörrel rendelkezik:

- `https://mytenant.onmicrosoft.com/customerapi`-2 hatókörrel `customer.read` és`customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi`-2 hatókörrel `vendor.read` és`vendor.write`

A `.WithAdditionalPromptToConsent` `extraScopesToConsent` paraméterrel rendelkező módosítót kell használnia.

Például:

### <a name="in-msalnet"></a>A MSAL.NET

```CSharp
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

Ez a hívás kap egy hozzáférési jogkivonatot az első webes API-hoz.

Ha meg kell hívnia a második webes API-t, akkor `AcquireTokenSilent` az API-t hívhatja:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>A személyes Microsoft-fiókhoz az alkalmazás minden egyes futtatásakor újra kell egyeznie

A személyes Microsoft-fiókok felhasználói számára a kívánt viselkedést az összes natív ügyfél (asztali/mobil alkalmazás) hívása esetén a rendszer az engedélyezéshez kéri. A natív ügyfél-identitás eredendően nem biztonságos (ellentétben a bizalmas ügyfélalkalmazás, amely a Microsoft Identity platform titkát cseréli a személyazonosságának bizonyítására). A Microsoft Identity platform úgy döntött, hogy a felhasználó hozzájárulásának megadásával csökkenti a fogyasztói szolgáltatások biztonságát.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
