---
title: A Microsoft Identity platform fejlődése – Azure
description: Ismerje meg a Microsoft Identity platformot, amely a Azure Active Directory (Azure AD) Identity Service és a fejlesztői platform evolúcióját mutatja be.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: overview
ms.workload: identity
ms.date: 06/03/2019
ms.author: ryanwi
ms.reviewer: agirling, saeeda, benv
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca1b6cc6288b75dc7194ead916f2ecb468b053d2
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74845909"
---
# <a name="evolution-of-microsoft-identity-platform"></a>A Microsoft identitásplatformjának fejlődése

A Microsoft identitásplatformja az Azure Active Directory (Azure AD) fejlesztői platform fejlődésének eredménye. Lehetővé teszi a fejlesztők számára, hogy olyan alkalmazásokat hozzanak létre, amelyek bejelentkeznek a felhasználók számára, hogy a fejlesztők által létrehozott API-kat, például Microsoft Graph vagy API-kat hívjanak. Egy hitelesítési szolgáltatásból, nyílt forráskódú könyvtárakból, alkalmazás-regisztrációból és-konfigurációból áll (a fejlesztői portálon és az alkalmazás-API-n keresztül), a teljes fejlesztői dokumentációban, a gyors példákban, a kódokon, az oktatóanyagokon, a útmutatókon és a egyéb fejlesztői tartalmak. A Microsoft identitásplatformja támogatja a nyílt szabványokat, többek között OAuth 2.0-t és az OpenID Connectet.

Eddig a legtöbb fejlesztő dolgozott együtt az Azure AD v 1.0 platformmal a munkahelyi és iskolai fiókok (az Azure AD által kiépített) hitelesítéséhez az Azure AD v 1.0 végpontról származó tokenek igénylésével, az Azure ad Authentication Library (ADAL) használatával, Azure Portal az alkalmazások regisztrálása és konfigurációja, valamint az Azure AD Graph API a programozott alkalmazások konfigurálásához.

A Microsoft Identity platform (v 2.0) használatával bővítheti a következő típusú felhasználók elérését:

- Munkahelyi és iskolai fiókok (Azure AD-kiépített fiókok)
- Személyes fiókok (például Outlook.com vagy Hotmail.com)
- A saját e-mail-címét vagy közösségi identitását (például LinkedIn, Facebook, Google) használó ügyfelei az Azure AD B2C ajánlaton keresztül

Az egységes Microsoft Identity platformmal egyszer is írhat kódot, és hitelesítheti bármely Microsoft-identitását az alkalmazásban. Számos platformon létezik egy teljes körűen támogatott nyílt forráskódú könyvtár a Microsoft Authentication Library (MSAL) néven. A MSAL használata egyszerű, és lehetővé teszi a felhasználók számára a kiváló egyszeri bejelentkezést (SSO), így nagy megbízhatóságot és teljesítményt érhet el, és a Microsoft biztonságos fejlesztési életciklus (SDL) használatával fejleszthető. Az API-k meghívásakor beállíthatja, hogy az alkalmazás kihasználja a növekményes hozzájárulás előnyeit, ami lehetővé teszi, hogy késleltetse a további invazív hatókörökhöz való hozzájárulás iránti kérést, amíg az alkalmazás használata nem indokolja a futtatást.

Az Azure Portal az alkalmazás regisztrálásához és konfigurálásához, valamint a Microsoft Graph API-t használhatja a programozott alkalmazások konfigurálásához.

Saját tempójában frissítheti alkalmazását. A ADAL-könyvtárakkal készített alkalmazások továbbra is támogatottak. A ADAL-mel és a MSAL-tárakkal létrehozott alkalmazásokkal összekevert alkalmazás-portfóliók is támogatottak. Ez azt jelenti, hogy a legújabb ADAL és a legújabb MSAL használó alkalmazások egyszeri bejelentkezést tesznek elérhetővé a portfólióban, amelyet a megosztott jogkivonat gyorsítótára biztosít a különböző könyvtárak között. A ADAL-ről a MSAL-re frissített alkalmazások a felhasználó bejelentkezési állapotát a frissítés után is megőrzik.

## <a name="microsoft-identity-platform-experience"></a>A Microsoft-identitásplatform környezete

Az alábbi ábra a Microsoft-identitásplatform környezetének áttekintését ábrázolja, beleértve az alkalmazásregisztrációs környezetet, az SDK-kat, a végpontokat és a támogatott identitásokat.

![A Microsoft-identitásplatform ma](./media/about-microsoft-identity-platform/about-microsoft-identity-platform.svg)

### <a name="app-registration-experience"></a>Alkalmazás-regisztrálási élmény

A Microsoft Identity platformmal integrált összes alkalmazás kezeléséhez a Azure Portal **[Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908)** élmény az egyetlen portál. Ha már használta az alkalmazás regisztrációs portálját, használja inkább a Azure Portal alkalmazás regisztrációs élményét.

A Azure AD B2C (a közösségi vagy helyi identitások hitelesítése esetén) való integrációhoz regisztrálnia kell az alkalmazást egy Azure AD B2C-bérlőben. Ez a felhasználói élmény a Azure Portal része is.

A **Microsoft Graph alkalmazás API-** je jelenleg előzetes verzióban érhető el. Ezzel az API-val programozott módon konfigurálhatja a Microsoft Identity platformmal integrált alkalmazásokat a Microsoft-identitások hitelesítéséhez. Azonban amíg ez az API nem éri el az általános elérhetőséget, az Azure AD Graph 1,6 API-t és az alkalmazás jegyzékfájlját kell használnia.

### <a name="msal-libraries"></a>MSAL-kódtárak

A MSAL-kódtár használatával olyan alkalmazásokat hozhat létre, amelyek hitelesítik az összes Microsoft-identitást. A .NET és a JavaScript MSAL-kódtára általánosan elérhető. Az iOS és az Android rendszerhez készült MSAL-kódtárak előzetes verzióban érhetők el, és alkalmasak éles környezetben való használatra. Ugyanazt a termelési szintű támogatást nyújtjuk a MSAL-kódtárak számára az előzetes verzióban, mint az általánosan elérhető MSAL-és ADAL-verziók esetében.

A MSAL-kódtárak segítségével integrálhatja az alkalmazást Azure AD B2C használatával.

A webalkalmazások és webes API-k létrehozásához használható kiszolgálóoldali kódtárak általánosan elérhetők: [ASP.net](https://docs.microsoft.com/aspnet/overview) és [ASP.net Core](https://docs.microsoft.com/aspnet/core/?view=aspnetcore-2.2)

### <a name="microsoft-identity-platform-endpoint"></a>Microsoft Identity platform-végpont

A Microsoft Identity platform (v 2.0) végpontja már OIDC minősítéssel rendelkezik. Együttműködik a Microsoft Authentication librarys (MSAL) vagy bármely más szabványnak megfelelő könyvtárral. Az iparági szabványoknak megfelelően megvalósítja az emberi olvasási hatóköröket.

## <a name="next-steps"></a>Következő lépések

Tudjon meg többet az 1.0-s és 2.0-s verzióról.

* [Microsoft Identity platform (v 2.0) – áttekintés](v2-overview.md)
* [Azure Active Directory fejlesztők számára (v 1.0) – áttekintés](v1-overview.md)
