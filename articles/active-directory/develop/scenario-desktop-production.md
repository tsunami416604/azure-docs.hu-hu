---
title: Asztali alkalmazás, hogy hívások webes API-kat (Váltás az éles környezetben) – a Microsoft identity platform
description: Ismerje meg, hogyan hozhat létre egy asztali alkalmazás, hogy a hívások webes API-k (Váltás az éles környezetben)
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
ms.openlocfilehash: 2343a416bd810792e7267b94395f953aa4f880a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111190"
---
# <a name="desktop-app-that-calls-web-apis---move-to-production"></a>Asztali alkalmazás, amely meghívja a webes API-k – helyezze át az éles környezetbe

Ez a cikk részletesen, az alkalmazás további javítása, és helyezze át az éles környezetbe.

## <a name="handling-errors-in-desktop-applications"></a>Az asztali alkalmazások hibák kezelése

A különböző folyamatokban bemutattuk, hogyan kezelje a hibákat a beavatkozás nélküli folyamatok (amint kódrészletek). Azt is láttuk, hogy nincsenek-e esetekben, amikor beavatkozás szükséges (növekményes beleegyezése és a feltételes hozzáférés).

## <a name="how-to-have--the-user-consent-upfront-for-several-resources"></a>Hogyan lehet a felhasználói beleegyezés előre a számos erőforrás

> [!NOTE]
> Jóváhagyás beolvasása a Microsoft identity platform, de nem az Azure Active Directory (Azure AD) B2C számos erőforrások működését. Az Azure AD B2C csak rendszergazdai jóváhagyás, nem a felhasználói beleegyezés támogatja.

A Microsoft identity platform (2.0-s verzió) végpont nem teszik lehetővé a több erőforrás egyszerre egy token beszerzéséhez. Ezért a `scopes` paraméter csak egy adott erőforrás hatókörök tartalmazhat. Biztosíthatja, hogy a felhasználó előre jóváhagy több erőforrás használatával a `extraScopesToConsent` paraméter.

Például ha két erőforrás, rendelkező két hatókörök minden:

- `https://mytenant.onmicrosoft.com/customerapi` – 2 hatókörökkel `customer.read` és `customer.write`
- `https://mytenant.onmicrosoft.com/vendorapi` – 2 hatókörökkel `vendor.read` és `vendor.write`

Használjon a `.WithAdditionalPromptToConsent` módosító, amelyen a `extraScopesToConsent` paraméter.

Például:

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

Ez a hívás elsajátíthatja az első egy hozzáférési jogkivonatot az első webes API-hoz.

A második webes API-k meghívásához szükséges, ha hívása:

```CSharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```

### <a name="microsoft-personal-account-requires-reconsenting-each-time-the-app-is-run"></a>Személyes Microsoft-fiók szükséges reconsenting minden alkalommal, amikor az alkalmazás fut

A Microsoft a személyes fiókok felhasználóknak reprompting beleegyezést minden natív ügyfél (asztalon vagy mobilalkalmazás) hívás hitelesítéséhez az a célja. Natív ügyfél-azonosító eredendően nem biztonságos (ellentétesen bizalmas-ügyfélalkalmazást egy titkos kulcsot, a Microsoft Identity platform kell igazolnia az identitását az exchange). Úgy döntött, a Microsoft identity platform mérséklésére a bizonytalanság fogyasztói szolgáltatások által kéri a felhasználót a hozzájárulásra, minden alkalommal, amikor az alkalmazás engedélyezett.

## <a name="next-steps"></a>További lépések

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]
