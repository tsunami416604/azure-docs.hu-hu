---
title: A Microsoft identitásplatform (2.0-s verzió) áttekintése – Azure
description: Ismerje meg a Microsoft identity platform (v2.0) végpontját és platformját.
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
ms.date: 05/08/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f6953fb5d016c802db260c55bc4970c3f029ab1a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240827"
---
# <a name="microsoft-identity-platform-v20-overview"></a>Microsoft identity platform (2.0-s verzió) – áttekintés

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek minden Microsoft-identitást bejelentkeznek, és jogkivonatokat kapnak a Microsoft API-k hívásához, például a Microsoft Graph vagy a fejlesztők által készített API-k hívására. A Microsoft identity platform a következőkből áll:

- **OAuth 2.0 és OpenID Connect szabványnak megfelelő hitelesítési szolgáltatás,** amely lehetővé teszi a fejlesztők számára bármely Microsoft-identitás hitelesítését, beleértve a következőket:
  - Munkahelyi vagy iskolai fiókok (az Azure AD-n keresztül kiépítve)
  - Személyes Microsoft-fiókok (például Skype, Xbox és Outlook.com)
  - Közösségi vagy helyi fiókok (az Azure AD B2C-n keresztül)
- **Nyílt forráskódú tárak**: Microsoft authentication libraries (MSAL) és egyéb szabványoknak megfelelő tárak támogatása
- **Alkalmazáskezelési portál:** Az Azure Portalon beépített regisztrációs és konfigurációs élmény, valamint az összes többi Azure felügyeleti képesség.
- **Alkalmazáskonfigurációs API és PowerShell:** amely lehetővé teszi az alkalmazások programozott konfigurálását a Microsoft Graph API-n és a PowerShellen keresztül, így automatizálhatja a DevOps-feladatokat.
- **Fejlesztői tartalom:** koncepcionális és referenciadokumentáció, rövid útmutatóminták, kódminták, oktatóanyagok és útmutatók.

A fejlesztők számára a Microsoft identity platform zökkenőmentes integrációt kínál az identitás- és biztonsági tér újításaiba, például a jelszó nélküli hitelesítésbe, a nagyobb hitelesítésbe és a feltételes hozzáférésbe.  Nem kell saját maga megvalósítania ezt a funkciót: a Microsoft identitásplatformba integrált alkalmazások natívmódon kihasználják az ilyen újításokat.

A Microsoft identity platform, akkor írjon kódot egyszer, és elérheti bármely felhasználó. Egyszer létrehozhat egy alkalmazást, és számos platformon is működhet, vagy létrehozhat egy olyan alkalmazást, amely ügyfélként és erőforrás-alkalmazásként (API) is működik.

## <a name="getting-started"></a>Első lépések

Nem kell, hogy az identitások használata nehéz legyen. 

Tekintse meg a [Microsoft identity platform videóját,](identity-videos.md) amelyből megtudhatja az alapokat. 

Válasszon önre vonatkozó [forgatókönyvet](authentication-flows-app-scenarios.md) – minden forgatókönyv elérési útja rövid útmutatóval és egy áttekintő lappal rendelkezik, amely percek alatt üzembe hoz:

- [Egyoldalas alkalmazás készítése](scenario-spa-overview.md)
- [Felhasználót bejelentkeztető webalkalmazás készítése](scenario-web-app-sign-user-overview.md)
- [Webes API-kat hívó webalkalmazás készítése](scenario-web-app-call-api-overview.md)
- [Védett webes API készítése](scenario-protected-web-api-overview.md)
- [Webes API-kat meghívjaó webes API létrehozása](scenario-web-api-call-api-overview.md)
- [Asztali alkalmazás létrehozása](scenario-desktop-overview.md)
- [Démonalkalmazás létrehozása](scenario-daemon-overview.md)
- [Mobilalkalmazás létrehozása](scenario-mobile-overview.md)

A következő diagram ismerteti a közös hitelesítési alkalmazás forgatókönyvek – használja azt referenciaként, amikor integrálja a Microsoft identity platform ot az alkalmazásba.

[![Alkalmazási forgatókönyvek a Microsoft identitásplatformján](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az alapvető hitelesítési fogalmakról, javasoljuk, hogy kezdje az alábbi témakörökkel:

- [Hitelesítési folyamatok és alkalmazási forgatókönyvek](authentication-flows-app-scenarios.md)
- [Hitelesítési alapok](authentication-scenarios.md)
- [Alkalmazás- és szolgáltatástagok](app-objects-and-service-principals.md)
- [Közönség](v2-supported-account-types.md)
- [Engedélyek és jóváhagyás](v2-permissions-and-consent.md)
- [Azonosító jogkivonatok](id-tokens.md) és [hozzáférési jogkivonatok](access-tokens.md)

A [Microsoft Graph](https://docs.microsoft.com/graph/overview)-nak megnevezett, adatgazdag alkalmazást hozhat létre.

Ha készen áll arra, hogy éles **környezetbe**indítsa az alkalmazást, tekintse át az alábbi gyakorlati tanácsokat:

- [Engedélyezze](msal-logging.md) a naplózást az alkalmazásban.
- Engedélyezze a telemetriai adatokat az alkalmazásban.
- Proxyk engedélyezése [és http-ügyfelek testreszabása.](msal-net-provide-httpclient.md)
- Tesztelje az integrációt a [Microsoft identitásplatform-integrációs ellenőrzőlistájának](identity-platform-integration-checklist.md)követésével.

## <a name="learn-more"></a>Részletek

Ha azt tervezi, hogy hozzon létre egy ügyfél felé néző alkalmazás, amely aláírja a közösségi és helyi identitások, olvassa el az [Azure AD B2C áttekintést.](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-add-identity-providers)
