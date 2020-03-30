---
title: A Microsoft identitásplatformjának fejlődése - Azure
description: Ismerje meg a Microsoft identity platformot, az Azure Active Directory (Azure AD) identitásszolgáltatás és fejlesztői platform fejlődését.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 12/09/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.openlocfilehash: 8714b7a96197cb4a59b29bada31b5559961bf8e3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "78300213"
---
# <a name="evolution-of-microsoft-identity-platform"></a>A Microsoft identitásplatformjának fejlődése

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek bejelentkeznek a felhasználókba, jogkivonatokat kapnak az API-k hívásához, például a Microsoft Graph vagy a fejlesztők által készített API-k. Ez egy hitelesítési szolgáltatásból, nyílt forráskódú kódtárakból, alkalmazásregisztrációból és konfigurációból áll (fejlesztői portálon és alkalmazásAPI-n keresztül), teljes fejlesztői dokumentációból, gyorsindítási mintákból, kódmintákból, oktatóanyagokból, útmutatókból és egyéb fejlesztői tartalom. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között az OAuth 2.0-t és az OpenID Connectet.

Eddig a legtöbb fejlesztő az Azure AD v1.0 platformmal együttműködve hitelesítette a munkahelyi és iskolai fiókokat (az Azure AD által kiépítve) az Azure AD 1.0-s végpontról származó jogkivonatok kérésével, az Azure AD authentication library (ADAL) használatával, az Azure AD hitelesítési könyvtár (ADAL) használatával az alkalmazás regisztrációja és konfigurálása, valamint a Microsoft Graph API az automatizált alkalmazások konfigurálásához.

Az egyesített Microsoft-identitásplatform (2.0-s verzió) segítségével egyszer írhat kódot, és hitelesítheti a Microsoft-identitást az alkalmazásban. Több platformon a teljes mértékben támogatott nyílt forráskódú Microsoft authentication library (MSAL) használata az identitásplatform végpontok ellen ajánlott. Az MSAL használata egyszerű, nagyszerű egyszeri bejelentkezési (SSO) élményt nyújt a felhasználók számára, segít a magas megbízhatóság és teljesítmény elérésében, és a Microsoft Secure Development Lifecycle (SDL) használatával lett kifejlesztve. API-k hívásakor konfigurálhatja az alkalmazást, hogy kihasználja a növekményes hozzájárulás, amely lehetővé teszi, hogy késleltesse a hozzájárulás iránti kérelem több invazív hatókörök, amíg az alkalmazás használata indokolja ezt futásidőben.  Az MSAL támogatja az Azure Active Directory B2C-t is, így az ügyfelek az előnyben részesített közösségi, nagyvállalati vagy helyi fiókidentitások használatával egyszeri bejelentkezési hozzáférést kapnak az alkalmazásokhoz és API-khoz.

A Microsoft identity platform segítségével az alábbi típusú felhasználókra bonthatja az elérést:

- Munkahelyi és iskolai fiókok (Azure AD-vel kiépített fiókok)
- Személyes fiókok (például Outlook.com vagy Hotmail.com)
- Azok az ügyfelek, akik saját e-mail-címüket vagy közösségi identitásukat (például LinkedIn, Facebook, Google) hozzák az MSAL-on és az Azure AD B2C-n keresztül

Az Azure Portal segítségével regisztrálhatja és konfigurálhatja az alkalmazást, és használhatja a Microsoft Graph API-t az automatizált alkalmazások konfigurálásához.

Frissítse alkalmazását a saját tempójában. Az ADAL-kódtárakkal létrehozott alkalmazások továbbra is támogatottak. Az ADAL-szal és MSAL-kódtárakkal készült alkalmazásokból álló vegyes alkalmazásportfóliók is támogatottak. Ez azt jelenti, hogy a legújabb ADAL és a legújabb MSAL használatával készült alkalmazások sso-t biztosítanak a portfólióban, amelyet a könyvtárak közötti megosztott jogkivonat-gyorsítótár biztosít. Az ADAL-ról MSAL-ra frissített alkalmazások a frissítéskor megőrzik a felhasználói bejelentkezési állapotot.

## <a name="microsoft-identity-platform-experience"></a>A Microsoft-identitásplatform környezete

Az alábbi ábra a Microsoft-identitásplatform környezetének áttekintését ábrázolja, beleértve az alkalmazásregisztrációs környezetet, az SDK-kat, a végpontokat és a támogatott identitásokat.

![A Microsoft-identitásplatform ma](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Alkalmazásregisztrációs élmény

Az Azure Portal **[alkalmazásregisztrációs](https://go.microsoft.com/fwlink/?linkid=2083908)** élménye az egyetlen portálos élmény a Microsoft identity platformmal integrált összes alkalmazás kezeléséhez. Ha az alkalmazásregisztrációs portált használja, kezdje el használni az Azure Portal alkalmazásregisztrációs felületét.

Az Azure AD B2C-vel való integrációhoz (közösségi vagy helyi identitások hitelesítésekor) regisztrálnia kell az alkalmazást egy Azure AD B2C-bérlőben. Ez a tapasztalat is része az Azure Portalon.

Az [Application API segítségével](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0) programozott módon konfigurálhatja a Microsoft identity platformmal integrált alkalmazásokat bármely Microsoft-identitás hitelesítéséhez.

### <a name="msal-libraries"></a>MSAL-könyvtárak

Az MSAL-tár segítségével olyan alkalmazásokat hozhat létre, amelyek hitelesítik az összes Microsoft-identitást. A .NET és a JavaScript MSAL-kódtárai általában elérhetők. Az iOS és androidos MSAL-könyvtárak előzetes verzióban vannak, és éles környezetben való használatra alkalmasak. Az MSAL-kódtárak hoz ugyanazt a termelési szintű támogatást biztosítjuk előzetes verzióban, mint az MSAL és az ADAL általánosan elérhető verzióihoz.

Az MSAL-kódtárak segítségével is integrálhatja az alkalmazást az Azure AD B2C-vel.

A webalkalmazások és webes API-k készítéséhez használt kiszolgálóoldali tárak általában elérhetők: [ASP.NET](https://docs.microsoft.com/aspnet/overview) és [ASP.NET Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft-identitásplatform-végpont

A Microsoft identity platform (2.0-s verzió) végpontja mostantól OIDC-tanúsítvánnyal rendelkezik. Együttműködik a Microsoft authentication libraries (MSAL) vagy bármely más szabványnak megfelelő könyvtárral. Emberi legolvashatóbb hatóköröket valósít meg, az ipari szabványoknak megfelelően.

## <a name="next-steps"></a>További lépések

Tudjon meg többet az 1.0-s és 2.0-s verzióról.

* [Microsoft identity platform (2.0-s verzió) – áttekintés](v2-overview.md)
* [Az Azure Active Directory fejlesztőknek (1.0-s v1.0) – áttekintés](../azuread-dev/v1-overview.md)
