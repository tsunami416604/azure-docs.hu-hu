---
title: A Microsoft identity platform (2.0-s verzió) áttekintése – Azure
description: További tudnivalók a Microsoft identity platform (2.0-s verzió) endpoint és platform.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 058e5962bdf01d02abff8629df42c550fe669852
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110808"
---
# <a name="microsoft-identity-platform-v20-overview"></a>A Microsoft identity platform (2.0-s verzió) – áttekintés

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztőknek, hogy jelentkezzen be az összes Microsoft-identitások és a Microsoft APIs, például a Microsoft Graph vagy fejlesztők számára készült API-k hívásához jogkivonatok megkapásához alkalmazásokat hozhat létre. A Microsoft identity platform áll:

- **OAuth 2.0 és OpenID Connect standard megfelelő hitelesítési szolgáltatás** , amely lehetővé teszi a fejlesztők számára a hitelesítés bármely Microsoft identity többek között:
  - Munkahelyi vagy iskolai fiókkal (Azure AD-n keresztül kiosztott)
  - Személyes Microsoft-fiókok (például a Skype, Xbox és Outlook.com-os)
  - Közösségi vagy helyi fiókot (keresztül az Azure AD B2C-vel)
- **Nyílt forráskódú könyvtáraink**: A Microsoft hitelesítési tárak (MSAL) és az egyéb szabványokkal kompatibilis szalagtárak támogatása
- **Alkalmazás-felügyeleti portálon**: Az Azure Portalon, a többi Azure kezelési lehetőségek mellett a beépített regisztrálása és konfigurálása élményt.
- **Alkalmazás-konfigurációs API-t és a PowerShell**: amely lehetővé teszi az alkalmazások REST API-t (a Microsoft Graph és az Azure Active Directory Graph 1.6-os) és a Powershellen keresztül programozható konfigurációja, a fejlesztési és üzemeltetési feladatok automatizálásához.
- **Fejlesztői tartalom**: elméleti és dokumentáció, a rövid útmutató minták, Kódminták, oktatóanyagok és útmutatók hivatkoznak.

A fejlesztők számára a Microsoft identity platform biztosít az identitás- és biztonsági helyre, például a beállításának hitelesítési step-up hitelesítési és feltételes hozzáférési innovációkat való zökkenőmentes integráció.  Nem kell saját kezűleg funkció végrehajtásához: alkalmazások natív módon integrálva van a Microsoft identity platform kihasználása ilyen.

A Microsoft identity platform révén kód megírását, és minden felhasználót elérhet. Alkalmazás létrehozása után, és nincs sok platformok közötti használathoz, vagy létrehozhat egy alkalmazást, amely egy ügyfelet, valamint az erőforrás-alkalmazás (API) működik.

## <a name="getting-started"></a>Első lépések

Nem kell, hogy az identitások használata nehéz legyen. Válassza ki a forgatókönyv érvényes Önre – minden egyes forgatókönyv elérési útja a gyors üzembe helyezés és áttekintő oldala, percek alatt ütembe helyezheti az első rendelkezik:

- [Egyoldalas alkalmazás készítése](scenario-spa-overview.md)
- [-Webalkalmazás létrehozása, amely képes bejelentkeztetni a felhasználókat](scenario-web-app-sign-user-overview.md)
- [-Webalkalmazás létrehozása, amely meghívja a webes API-k](scenario-web-app-call-api-overview.md)
- [Egy védett webes API készítése](scenario-protected-web-api-overview.md)
- [Webes API, amely meghívja a webes API-k készítése](scenario-web-api-call-api-overview.md)
- [Egy asztali alkalmazás készítése](scenario-desktop-overview.md)
- [Démon alkalmazás készítése](scenario-daemon-overview.md)
- [Egy olyan mobilalkalmazás készítését](scenario-mobile-overview.md)

Az alábbi táblázat ismerteti az általános hitelesítési forgatókönyvek, – használja hivatkozásként van listázva, amikor a Microsoft identity platform integrálják az alkalmazást.

[![Alkalmazás-forgatókönyvek a Microsoft identity platform](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>További lépések

Ha szeretne további kapcsolatos alapfogalmakat hitelesítési, javasoljuk, először a következő témakörökben:

- [Hitelesítési alapok](authentication-scenarios.md)
- [Alkalmazás- és az egyszerű szolgáltatások](app-objects-and-service-principals.md)
- [Célközönség](v2-supported-account-types.md)
- [Engedélyek és jóváhagyás](v2-permissions-and-consent.md)
- [Azonosító-jogkivonatokat](id-tokens.md) és [hozzáférési jogkivonatokat](access-tokens.md)

Meghívó adatokban gazdag-alkalmazás létrehozása [Microsoft Graph](https://docs.microsoft.com/graph/overview).

Amikor készen áll, az alkalmazás indításához egy **éles környezetben**, tekintse át az ajánlott eljárások:

- [Naplózás engedélyezése](msal-logging.md) az alkalmazásban.
- Engedélyezze a telemetriai adatok az alkalmazásban.
- Engedélyezése [proxyk és testre szabhatja a HTTP-ügyfelek](msal-net-provide-httpclient.md).
- Az integráció teszteléséhez a következő a [a Microsoft identity platform integrációs ellenőrzőlista](identity-platform-integration-checklist.md).

## <a name="learn-more"></a>Részletek

Ha egy ügyfél felé irányuló-alkalmazások, amelyeket a közösségi és a helyi identitások, bejelentkezik a [áttekintése az Azure AD B2C-vel](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers).
