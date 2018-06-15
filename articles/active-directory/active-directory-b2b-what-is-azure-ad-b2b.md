---
title: Mi az az Azure Active Directory B2B együttműködés? | Microsoft Docs
description: Az Azure Active Directory B2B együttműködés a vállalatokon átívelő kapcsolatok üzleti partnerek szelektíven érhessék el a vállalati alkalmazásokhoz való engedélyezésével támogatja.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/26/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1eff04cf43260a12a50a08b9145e1975b51af0cc
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33928480"
---
# <a name="what-is-azure-ad-b2b-collaboration"></a>Mi az az Azure AD B2B együttműködés?

Az Azure Active Directory (Azure AD) vállalatok (B2B) együttműködési képességek lehetővé bármely szervezet használata az Azure AD biztonságos felhasználók bármilyen más szervezettől származik, minimális és maximális mérete. Azon szervezetek vagy az Azure AD anélkül is lehet, és nem is kell rendelkeznie, hogy az informatikai részleg.

Az Azure AD használatával a szervezetek dokumentumokhoz, erőforrások és a partnerek számára, alkalmazások hozzáférést biztosíthat a teljes felügyeletet gyakorolhat a saját vállalati adatok megőrzésével. A fejlesztők használhatják az Azure AD-vállalatok API-alkalmazások írását, amelyek a két szervezet a kapcsolják össze több biztonságos helyen. Azt is könnyen a végfelhasználók számára, keresse meg.

A következő videó áttekintése hasznos.
>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

## <a name="key-benefits-of-azure-ad-b2b-collaboration"></a>Azure AD B2B együttműködés legfontosabb előnyei

### <a name="work-with-any-user-from-any-partner"></a>Bármely felhasználó bármely partnertől használata

- Partnerek a saját hitelesítő adatok használata
- Nem kötelező használni az Azure AD-partnerek számára
- Nincsenek külső címtárak vagy bonyolult telepítési szükséges

### <a name="simple-and-secure-collaboration"></a>Egyszerű és biztonságos együttműködési

- Minden vállalati alkalmazás vagy adatokat, hozzáférést biztosítanak a kifinomult, az Azure AD által biztosított engedélyezési házirendek alkalmazása közben
- A felhasználók számára könnyen
- Vállalati szintű védelmet biztosít az alkalmazások és adatok

### <a name="no-management-overhead"></a>Egyetlen kezelési terhelés mellett.

- Nincsenek külső fiókjának vagy jelszavának kezelése
- Egyetlen szinkronizálási vagy manuális fiók életciklusának kezelésére
- Nincsenek külső adminisztrációs terhelés

## <a name="easily-add-b2b-collaboration-users"></a>Egyszerűen a B2B együttműködés felhasználók hozzáadása

A rendszergazdák egyszerűen hozzáadhatja B2B együttműködés (vendég) felhasználók a szervezet a [Azure-portálon](https://portal.azure.com).

![vendég felhasználók hozzáadása](media/active-directory-b2b-what-is-azure-ad-b2b/adding-b2b-users-admin.png)

### <a name="enable-your-collaborators-to-bring-their-own-identity"></a>A közreműködők a saját identitás érdekében engedélyezése

B2B közreműködők is jelentkezzen be az általuk választott identitást. Ha a felhasználó nem rendelkezik Microsoft-fiókkal vagy egy Azure AD-fiókot – egy hoz létre őket zökkenőmentesen ajánlat beváltásra időpontjában.

### <a name="delegate-to-application-and-group-owners"></a>Alkalmazás és a csoportházirend-tulajdonosok delegálása

Egy alkalmazás vagy a csoport tulajdonosa adhat hozzá B2B felhasználók közvetlenül bármely alkalmazás, amely a fontos adatokhoz, hogy a Microsoft-alkalmazások vagy a nem. Rendszergazdák számára delegálhatják engedély B2B felhasználók hozzáadása a nem rendszergazda. A rendszergazdák nem használhatják a [az Azure AD alkalmazás-hozzáférési Panel](https://myapps.microsoft.com) B2B együttműködés felhasználók hozzáadása az alkalmazások vagy csoportok számára.

![hozzáférési panel](media/active-directory-b2b-what-is-azure-ad-b2b/access-panel.png)

![tag hozzáadása](media/active-directory-b2b-what-is-azure-ad-b2b/add-member.png)

### <a name="authorization-policies-protect-your-corporate-content"></a>Engedélyezési házirendek a vállalati tartalom védelme

Kényszerítheti a feltételes hozzáférési szabályzatok, például többtényezős hitelesítés:
- A bérlői szintű
- Az alkalmazás szintjén
- Vállalati alkalmazások és adatok védelme érdekében bizonyos felhasználók részére

### <a name="use-apis-and-sample-code-to-easily-build-applications-to-onboard"></a>Az API-kkal és a mintakódot érheti alkalmazások

A külső partnerek a board a szervezet igényeinek megfelelően testre szabott módon kapcsolja.

Használja a [B2B együttműködés meghívó API-k](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) testreszabása a bevezetési észlel, beleértve az előfizetési önkiszolgáló portálokat létrehozása. Az önkiszolgáló portál nyújtunk mintakód [a Githubon](https://github.com/Azure/active-directory-dotnet-graphapi-b2bportal-web).

![regisztrációs portál](media/active-directory-b2b-what-is-azure-ad-b2b/sign-up-portal.png)

Az Azure AD B2B együttműködés a partnerkapcsolatok oly módon, hogy a végfelhasználók számára egyszerű és intuitív található védelméhez az Azure AD a teljes power kérheti le.

## <a name="next-steps"></a>További lépések

- [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
- [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
- [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
- [Az Azure AD B2B együttműködés licencelés](active-directory-b2b-licensing.md)
- És szerint mindig a termékért felelős csoport visszajelzést, beszélgetéseket, és javaslatokat keresztül csatlakozzon a [a Microsoft technikai közösségi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).