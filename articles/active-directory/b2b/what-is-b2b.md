---
title: Mi az Azure Active Directory B2B együttműködés? | Microsoft Docs
description: Az Azure Active Directory B2B-együttműködés a átívelő kapcsolatok üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokhoz való engedélyezésével támogatja.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 51a969ae215583a0be8d75ff1de11173e0696a22
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35645681"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Mi az az Azure AD B2B együttműködés?

Az Azure Active Directory (Azure AD)-vállalatközi (B2B) együttműködési lehetőségekhez engedélyezése bármely szervezet az Azure AD-vel történő biztonságos együttműködve szervezet felhasználóitól származó bármely más, kisebb vagy nagyobb. Azon szervezetek vagy az Azure AD nélkül is lehet, és nem rendelkezik az IT-részlegek is szükséges.

Az Azure AD a szervezetek dokumentumok, erőforrások és alkalmazások számára azok partnerei hozzáférést biztosíthat a teljes körű irányítást a saját vállalati adatokat. A fejlesztők használhatják az Azure AD-vállalatok, amelyek egyesítik két szervezet több biztonságos alkalmazások írására, API-k. Azt is egyszerűen navigálhat a végfelhasználók számára.

Az alábbi videó hasznos áttekintést nyújt.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Az Azure AD B2B-együttműködés fő előnye

### <a name="work-with-any-user-from-any-partner"></a>Bármely felhasználóval bármely partnertől működik

- Partnerek a saját hitelesítő adatok használata
- Nem követelmény, a partnerek számára Azure AD használata
- Nincsenek külső címtárak vagy szükséges összetett beállítás

### <a name="simple-and-secure-collaboration"></a>Egyszerű és biztonságos együttműködési

- Minden vállalati alkalmazás vagy az adatok, hozzáférést biztosítanak a kifinomult, az Azure AD-alapú engedélyezési házirendek alkalmazása közben
- A felhasználók számára könnyen
- Nagyvállalati szintű biztonság az alkalmazások és adatok

### <a name="no-management-overhead"></a>Nincs munkaterhelést

- Nincsenek külső fiók vagy a jelszó felügyeleti
- Nem szinkronizálás vagy manuális fiókok életciklusának kezelése
- Nincsenek külső adminisztrációs terhelés

## <a name="easily-add-b2b-collaboration-users"></a>Bővítse a B2B-együttműködés felhasználók

A rendszergazdák egyszerűen hozzáadhat B2B-együttműködés (vendég) felhasználók a szervezet a [az Azure portal](https://portal.azure.com).

![vendégfelhasználók hozzáadása](media/what-is-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>A közreműködők ahhoz, hogy a saját identitás engedélyezése

B2B közreműködő bejelentkezhet az identitás a választott eszközükön. Ha a felhasználó nem rendelkezik Microsoft-fiókkal vagy egy Azure AD-fiók – a rendszer létrehoz egyet azokat zökkenőmentesen ajánlat beváltásra időpontjában.

### <a name="delegate-to-application-and-group-owners"></a>Az alkalmazás és a csoportok tulajdonosainak delegálása

Egy alkalmazás vagy a csoport tulajdonosa adhat hozzá B2B-felhasználók közvetlenül minden olyan alkalmazás, amely az Ön számára, hogy egy Microsoft-alkalmazásba vagy nem. A rendszergazdák delegálhatja a B2B-felhasználók hozzáadása a nem rendszergazdák számára. A rendszergazdák nem használhatják a [az Azure AD alkalmazás-hozzáférési Panel](https://myapps.microsoft.com) B2B együttműködési felhasználókat hozzá alkalmazásokat vagy a csoportok.

![Hozzáférési panel](media/what-is-b2b/access-panel.png)

![tag hozzáadása](media/what-is-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Engedélyezési házirendek a vállalati tartalom védelme

Feltételes hozzáférési szabályzatok, például a többtényezős hitelesítés, a következőkkel lehet érvényesíteni:
- Bérlői szinten
- Az alkalmazás szintjén
- Megadott felhasználók számára a vállalati alkalmazások és adatok védelme

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Az API-kkal és a mintakódot üzembe helyezni alkalmazásokat készíthet

A külső partnerekkel hajón a szervezet igényeinek megfelelően testre szabott módon használata.

Használja a [B2B együttműködés meghívó API-k](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) szabhatja testre a bevezetési során lép fel, mint például az önkiszolgáló regisztrációs portál létrehozása. Önkiszolgáló portál kínálunk mintakód [a Githubon](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![regisztrációs portál](media/what-is-b2b/sign-up-portal.png)

Az Azure AD B2B együttműködés kérheti le úgy, hogy a végfelhasználók számára egyszerű és intuitív keresése a partneri kapcsolatok védelme érdekében az Azure AD teljes hatékonyságát.

## <a name="next-steps"></a>További lépések

- [Hogyan hozzá az Azure Active Directory-rendszergazdák B2B együttműködési felhasználókat?](add-users-administrator.md)
- [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködési felhasználókat?](add-users-information-worker.md)
- [B2B együttműködés vendégmeghívás beváltása](redemption-experience.md)
- [Az Azure AD B2B-együttműködés licencelése](licensing-guidance.md)
- És, mint mindig, minden olyan visszajelzést, hozzászólások és keretében javaslat a termékért felelős csoport összekapcsolása a [a Microsoft technikai Közösség](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).