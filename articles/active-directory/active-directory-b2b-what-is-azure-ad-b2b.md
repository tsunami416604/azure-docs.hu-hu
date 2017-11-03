---
title: "Mi az az Azure Active Directory B2B együttműködés? | Microsoft Docs"
description: "Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokhoz való engedélyezésével támogatja."
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 1464387b-ee8b-4c7c-94b3-2c5567224c27
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 06/27/2017
ms.author: curtand
ms.custom: aaddev
ms.reviewer: sasubram
ms.openlocfilehash: fbc12a52555b190d43b5e953fd4d19923a25b0ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Mi az az Azure AD B2B együttműködés?

<iframe width="560" height="315" src="https://www.youtube.com/embed/AhwrweCBdsc" frameborder="0" allowfullscreen></iframe>

Az Azure AD-vállalatok (B2B) együttműködési képességek engedélyezése bármely szervezet használata az Azure AD biztonságos felhasználók bármilyen más szervezettől származik, minimális és maximális mérete. Azon szervezetek lehet az Azure ad-vel vagy nélkül, vagy akár egy informatikai szervezet vagy anélkül. 

Az Azure AD használatával a szervezetek dokumentumokhoz, erőforrások és a partnerek számára, alkalmazások hozzáférést biztosíthat a teljes felügyeletet gyakorolhat a saját vállalati adatok megőrzésével. A fejlesztők használhatják az Azure AD-vállalatok API-alkalmazások írását, amelyek a két szervezet a kapcsolják össze több biztonságos helyen. Azt is igen egyszerű, a végfelhasználók számára, keresse meg.

ügyfeleink 97 % jelzik, Azure AD B2B együttműködés nagyon fontos, hogy azokat.

![a tortadiagram](media/active-directory-b2b-what-is-azure-ad-b2b/97-percent-support.png)

Korai április 2017 frissítésétől volt körülbelül 3 millió felhasználó alapján már használja az Azure AD B2B együttműködés képességeit. És az Azure AD a szervezeteknek, amelyek több mint 10 olyan felhasználót 23 százaléka már élvező ezeket a képességeket.

## <a name="the-key-benefits-of-azure-ad-b2b-collaboration-to-your-organization"></a>A szervezet Azure AD B2B együttműködés legfontosabb előnyei

### <a name="work-with-any-user-from-any-partner"></a>Bármely felhasználó bármely partnertől használata

* Partnerek a saját hitelesítő adatok használata

* Nem kötelező használni az Azure AD-partnerek számára

* Nincsenek külső címtárak vagy bonyolult telepítési szükséges

### <a name="simple-and-secure-collaboration"></a>Egyszerű és biztonságos együttműködési

* Minden vállalati alkalmazás vagy adatokat, hozzáférést biztosítanak a kifinomult, az Azure AD által biztosított engedélyezési házirendek alkalmazása közben

* A felhasználók számára könnyen

* Vállalati szintű védelmet biztosít az alkalmazások és adatok

### <a name="no-management-overhead"></a>Egyetlen kezelési terhelés mellett.

* Nincsenek külső fiókjának vagy jelszavának kezelése

* Egyetlen szinkronizálási vagy manuális fiók életciklusának kezelésére

* Nincsenek külső adminisztrációs terhelés

## <a name="you-can-easily-add-b2b-collaboration-users-to-your-organization"></a>Egyszerűen hozzáadhatja B2B együttműködés felhasználók a szervezet

Rendszergazdák B2B együttműködés (vendég) felhasználója hozzáadhatja a [Azure-portálon](https://portal.azure.com).

![vendég felhasználók hozzáadása](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>A közreműködők a saját identitás érdekében engedélyezése

B2B közreműködők is jelentkezzen be az általuk választott identitást. Ha a felhasználó nem rendelkezik Microsoft-fiókkal vagy egy Azure AD-fiókot – egy hoz létre őket zökkenőmentesen ajánlat beváltásra időpontjában.

![bejelentkezési identitás választás](media/active-directory-b2b-what-is-azure-ad-b2b/sign-in-identity-choice.png)

### <a name="delegate-to-application-and-group-owners"></a>Alkalmazás és a csoportházirend-tulajdonosok delegálása 
Alkalmazás és a csoportházirend-tulajdonosok adhat hozzá B2B felhasználók közvetlenül bármely alkalmazás, amely a fontos adatokhoz, hogy a Microsoft-alkalmazások vagy a nem. Rendszergazdák számára delegálhatják engedély B2B felhasználók hozzáadása a nem rendszergazda. A nem rendszergazda használhatja a [az Azure AD alkalmazás-hozzáférési Panel](https://myapps.microsoft.com) B2B együttműködés felhasználók hozzáadása az alkalmazások vagy csoportok számára.

![hozzáférési panel](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![tag hozzáadása](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Engedélyezési házirendek a vállalati tartalom védelme

Kényszerítheti a feltételes hozzáférési szabályzatok, például többtényezős hitelesítés:
- A bérlői szintű
- Az alkalmazás szintjén
- Vállalati alkalmazások és adatok védelme érdekében bizonyos felhasználók részére

![tag hozzáadása](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="use-our-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Az API-k és a mintakódot érheti alkalmazások
A külső partnerek a board a szervezet igényeinek megfelelően testre szabott módon kapcsolja.

Használja a [B2B együttműködés meghívó API-k](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation), testre szabhatja a bevezetési lép, beleértve az előfizetési önkiszolgáló portálokat létrehozása. Az önkiszolgáló portál nyújtunk mintakód [a Githubon](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![regisztrációs portál](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Az Azure AD B2B együttműködés a partnerkapcsolatok oly módon, hogy a végfelhasználók számára egyszerű és intuitív található védelméhez az Azure AD a teljes power kérheti le. Ezért induljon el, a külső együttműködés az Azure AD B2B már használó szervezetek több ezer csatlakozáshoz!

## <a name="next-steps"></a>Következő lépések

* Rendszergazdai élmény találhatók a [Azure-portálon](https://portal.azure.com).

* Információk munkavégző lép érhetők el a [hozzáférési Panel](https://myapps.microsoft.com).

* És szerint mindig a termékért felelős csoport visszajelzést, beszélgetéseket, és javaslatokat keresztül csatlakozzon a [a Microsoft technikai közösségi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
* [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
* [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
* [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
* [Az Azure AD B2B együttműködés licencelés](active-directory-b2b-licensing.md)
* [Hibaelhárítás az Azure Active Directory B2B együttműködés](active-directory-b2b-troubleshooting.md)
* [Az Azure Active Directory B2B együttműködés gyakori kérdések (GYIK)](active-directory-b2b-faq.md)
* [Az Azure Active Directory B2B együttműködés API és a Testreszabás](active-directory-b2b-api.md)
* [Többtényezős hitelesítés a B2B-együttműködés felhasználói számára](active-directory-b2b-mfa-instructions.md)
* [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
* [Naplózási és jelentéskészítési B2B együttműködés felhasználó](active-directory-b2b-auditing-and-reporting.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
