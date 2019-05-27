---
title: A Microsoft identity platform – Azure evolúciója
description: Ismerje meg a Microsoft identity platform, az Azure Active Directory (Azure AD) identity service és a fejlesztői platform továbbfejlesztett változata.
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
ms.openlocfilehash: 807045089f70e117d46754412d974be7fba5a77a
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65832497"
---
# <a name="evolution-of-microsoft-identity-platform"></a>A Microsoft identitásplatformjának fejlődése

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, amely a felhasználók, a jogkivonatok kiosztva azon hívja az API-k, például a Microsoft Graph, vagy az API-kat a fejlesztők számára készült alkalmazások. Folyamatautomatizálást olyan hitelesítési szolgáltatás, nyílt forráskódú könyvtáraink, alkalmazásregisztráció és konfigurációja (keresztül a fejlesztői portál és a kérelem API-t), teljes körű fejlesztői dokumentáció, a rövid útmutató minták, Kódminták, oktatóanyagok és útmutatók, és más fejlesztői tartalom. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet.

Eddig nagyon a legtöbb fejlesztő dolgozott már az Azure ad-ben 1.0-s verziójú platform hitelesítéséhez a munkahelyi és iskolai fiókok (Azure AD által kiosztott) tokenek kér az Azure AD-1.0-s verziójú végpont által az Azure AD Authentication Library (ADAL), az Azure portal használatával alkalmazás regisztrálása és konfigurációs, és az Azure AD Graph API a programozott alkalmazás konfigurációja.

A Microsoft identity platform (2.0-s verzió) révén ki a horizontját az ilyen típusú felhasználók:

- Munkahelyi és iskolai fiókok (kiépítése az Azure AD-fiókok)
- Személyes fiókok (például Outlook.com-os vagy Hotmail.com)
- Az ügyfelek, akik hozzák saját e-mail vagy éppen közösségi (például Google, Facebook, a LinkedIn) keresztül az Azure AD B2C-ajánlat

Az egyesített Microsoft identity platform kód megírását, és bármely Microsoft identitás hitelesítésére az alkalmazásba. Számos platformra van egy teljes körűen támogatott nyílt forráskódú kódtár, nevű Microsoft-hitelesítési tár (MSAL). Az MSAL könnyen használható, biztosít a felhasználók számára, lehetővé teszi a magas rendelkezésre állást és teljesítményt érhet el, és fejlesztik a Microsoft biztonságos fejlesztési Életciklussal (SDL) használatával találkozik nagyszerű egyszeri bejelentkezés (SSO). API-k hívásakor konfigurálhatja az alkalmazás kihasználásához növekményes jóváhagyás, amely lehetővé teszi, hogy további invazív hatókörök jóváhagyási kérés késleltetés mindaddig, amíg az alkalmazás használati kódhiba Ez futásidőben.

Az Azure portal használatával regisztrálta és konfigurálta az alkalmazást, és használni a Microsoft Graph API-val programozott alkalmazás konfigurációja.

Frissítse az alkalmazást, hogy saját tempójában. ADAL könyvtárakat-val készített alkalmazások továbbra is támogatottak. Vegyes alkalmazás portfóliók, olyan alkalmazások beépített adal-t és a MSAL kódtárak-val készített alkalmazások, is támogatottak. Ez azt jelenti, hogy a legújabb adal-t és a legújabb MSAL használó alkalmazások között a portfólió, ezek a kódtárak között megosztott tokengyorsítótárral által biztosított egyszeri bejelentkezés fog nyújtani. Az ADAL MSAL a frissített alkalmazások megőrzi a felhasználói bejelentkezési állapot frissítése után.

## <a name="microsoft-identity-platform-experience"></a>A Microsoft-identitásplatform környezete

Az alábbi ábra a Microsoft-identitásplatform környezetének áttekintését ábrázolja, beleértve az alkalmazásregisztrációs környezetet, az SDK-kat, a végpontokat és a támogatott identitásokat.

![A Microsoft-identitásplatform ma](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Az alkalmazás regisztrációs felhasználói felülete

Az Azure Portalon **[alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908)** élmény az egyik portáljának felületén az kezelése minden alkalmazást, a Microsoft identity platform már integrálva. Ha már használja az alkalmazásregisztrációs portálon, kezdési használja helyette az Azure-portál alkalmazás regisztrációs felhasználói élményt.

Integráció az Azure AD B2C (Ha a közösségi vagy a helyi identitások hitelesítését) kell regisztrálja az alkalmazást a B2C-bérlőben. Ez a tapasztalat része is az Azure Portalon.

A **Microsoft Graph API-alkalmazás** jelenleg előzetes verzióban érhető el. Az API-val programozott módon konfigurálhatja a Microsoft identitásplatformja bármely Microsoft identitás hitelesítésére az integrált alkalmazásokhoz. Azonban amíg el nem éri az API általános rendelkezésre állás, használjon az Azure AD Graph 1.6-os API és az alkalmazásjegyzék.

### <a name="msal-libraries"></a>Az MSAL kódtárak

Az MSAL library használatával hozhat létre alkalmazásokat, amelyek az összes Microsoft-identitások hitelesítésére. Általánosan elérhetők az MSAL kódtárak a .NET-ben. A JavaScript, iOS és Android az MSAL kódtárak vannak előzetes verzióban érhető el, és az éles környezetben használhatók. Az azonos termelési szintű támogatást biztosítunk MSAL kódtárak előzetes verzióban érhető el, ahogy azt az MSAL és az adal-t azon verzióit, általánosan elérhető.

A MSAL kódtárak használatával az alkalmazás integrálása az Azure AD B2C-t.

Kiszolgálóoldali-kódtárak a webes alkalmazások és a webes API-k általánosan elérhetők: [ASP.NET](https://docs.microsoft.com/aspnet/overview) and [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>A Microsoft identity platform végpont

A Microsoft identity platform (2.0-s verzió) végpont már hitelesített OIDC. A Microsoft-hitelesítési tárak (MSAL) vagy bármely más szabványokkal kompatibilis könyvtár működik. Emberi olvasható hatókörök, iparági szabványoknak megfelelő valósítja meg.

## <a name="next-steps"></a>További lépések

Tudjon meg többet az 1.0-s és 2.0-s verzióról.

* [A Microsoft identity platform (2.0-s verzió) – áttekintés](v2-overview.md)
* [Az Azure Active Directory fejlesztők (1.0-s verzió) – áttekintés](v1-overview.md)
