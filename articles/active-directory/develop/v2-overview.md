---
title: A Microsoft Identity platform áttekintése – Azure
description: Ismerje meg a Microsoft Identity platform összetevőit, valamint azt, hogy miként segíthetnek az alkalmazásokban az identitás-és hozzáférés-kezelési (IAM) támogatás létrehozásában.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 07/09/2020
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: identityplatformtop40
ms.openlocfilehash: 566893a9babc3f771e3198970e0a5ae97592e322
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231675"
---
# <a name="microsoft-identity-platform-overview"></a>A Microsoft Identity platform áttekintése

A Microsoft Identity platform segítségével olyan alkalmazásokat hozhat létre, amelyeket a felhasználók és az ügyfelek bejelentkezhetnek a Microsoft-identitásuk vagy közösségi fiókjaik használatára, valamint jogosult hozzáférést biztosítanak a saját API-khoz vagy a Microsoft API-khoz, például a Microsoft Graphhoz.

A Microsoft Identity platform számos összetevőből áll:

- **OAuth 2,0 és OpenID Connect standard-kompatibilis hitelesítési szolgáltatás** , amely lehetővé teszi a fejlesztők számára a különböző identitások hitelesítését, beleértve a következőket:
  - Az Azure AD-n keresztül kiépített munkahelyi vagy iskolai fiókok
  - Személyes Microsoft-fiók, például Skype, Xbox és Outlook.com
  - Közösségi vagy helyi fiókok Azure AD B2C használatával
- **Nyílt forráskódú kódtárak**: Microsoft Authentication librarys (MSAL) és más szabványoknak megfelelő kódtárak támogatása
- **Application Management Portal**: a Azure Portal regisztrációs és konfigurációs felhasználói felülete, valamint a többi Azure felügyeleti lehetőség.
- **Alkalmazás-konfigurációs API és PowerShell**: az alkalmazások programozott konfigurációja a Microsoft Graph API-val és a PowerShell-lel, hogy automatizálható legyen a DevOps-feladatok.
- **Fejlesztői tartalom**: technikai dokumentáció, beleértve a gyors útmutatókat, oktatóanyagokat, útmutatók és a kód mintáit.

A fejlesztők számára a Microsoft Identity platform a modern innovációk integrációját kínálja az identitás és a biztonság terén, például a jelszó-alapú hitelesítéshez, a lépésenkénti hitelesítéshez és a feltételes hozzáféréshez. Nincs szükség ilyen funkciók megvalósítására: a Microsoft Identity platformmal integrált alkalmazások natív módon kihasználhatják ezeket az innovációkat.

A Microsoft Identity platformmal egyszer is írhat kódot, és elérheti a felhasználókat. Egyszerre létrehozhat egy alkalmazást, és több platformon is dolgozhat, vagy olyan alkalmazást hozhat létre, amely ügyfélként, valamint erőforrás-alkalmazásként (API) működik.

## <a name="getting-started"></a>Első lépések

Válassza ki a létrehozni kívánt [alkalmazási forgatókönyvet](authentication-flows-app-scenarios.md) . A forgatókönyvek mindegyikének elérési útja áttekintést nyújt, és egy rövid útmutatóra mutató hivatkozásokat tartalmaz, amelyekkel megkezdheti a működést:

- [Egyoldalas alkalmazás (SPA)](scenario-spa-overview.md)
- [Felhasználót bejelentkeztető webalkalmazás](scenario-web-app-sign-user-overview.md)
- [Webes API-kat hívó webalkalmazás](scenario-web-app-call-api-overview.md)
- [Védett webes API](scenario-protected-web-api-overview.md)
- [Webes API-kat hívó webes API](scenario-web-api-call-api-overview.md)
- [Asztali alkalmazás](scenario-desktop-overview.md)
- [Démonalkalmazások](scenario-daemon-overview.md)
- [Mobilalkalmazás](scenario-mobile-overview.md)

Amikor együttműködik a Microsoft Identity platformmal az alkalmazások hitelesítésének és engedélyezésének integrálásához, tekintse át ezt a rendszerképet, amely az alkalmazás leggyakoribb forgatókönyveit és identitás-összetevőit ismerteti. Válassza ki a képet a teljes méret megtekintéséhez.

[![A Microsoft Identity platform számos alkalmazási forgatókönyvét bemutató Metro Map](./media/v2-overview/application-scenarios-identity-platform.png)](./media/v2-overview/application-scenarios-identity-platform.svg#lightbox)

## <a name="learn-authentication-concepts"></a>A hitelesítési fogalmak megismerése

Ismerje meg, hogyan vonatkoznak az alapszintű hitelesítés és az Azure AD-fogalmak a Microsoft Identity platformra ebben az ajánlott cikkben:

- [Hitelesítési alapok](authentication-scenarios.md)
- [Alkalmazás-és egyszerű szolgáltatások](app-objects-and-service-principals.md)
- [Közönség](v2-supported-account-types.md)
- [Engedélyek és jóváhagyás](v2-permissions-and-consent.md)
- [Azonosító jogkivonatok](id-tokens.md)
- [Hozzáférési jogkivonatok](access-tokens.md)
- [Hitelesítési folyamatok és alkalmazási helyzetek](authentication-flows-app-scenarios.md)

## <a name="more-identity-and-access-management-options"></a>További identitás-és hozzáférés-kezelési lehetőségek

[Azure ad B2C](../../active-directory-b2c/overview.md) – ügyfelek által elérhető alkalmazások létrehozása a felhasználók bejelentkezhetnek a közösségi fiókjaik, például a Facebook vagy a Google használatával, vagy e-mail-címmel és jelszóval.

[Azure ad B2B](../b2b/what-is-b2b.md) – külső felhasználók meghívása az Azure ad-bérlőbe "vendég" felhasználóként, valamint engedélyek kiosztása a hitelesítéshez a meglévő hitelesítő adataik használata közben.

> [!TIP]
> *Azure Active Directory fejlesztői platform (v 1.0)* dokumentációját keresi? Tekintse [meg a fejlesztőknek szóló Azure Active Directory (v 1.0) – áttekintés](../azuread-dev/v1-overview.md).

## <a name="next-steps"></a>További lépések

Ha van olyan Azure-fiókja, amelyhez már van hozzáférése egy Azure Active Directory bérlőhöz, de a Microsoft Identity platform fejlesztőinek saját Azure AD-bérlőre van szüksége az alkalmazások fejlesztéséhez, a "dev-bérlő".

Megtudhatja, hogyan hozhat létre saját bérlőt az alkalmazások összeállítása során való használatra:

[Gyors útmutató: Azure AD-bérlő beállítása](quickstart-create-new-tenant.md)
