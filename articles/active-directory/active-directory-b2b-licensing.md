---
title: "Útmutatás licencelése az Azure Active Directory B2B együttműködés |} Microsoft Docs"
description: "Az Azure Active Directory B2B együttműködés nem igényel fizetős Azure AD-licenccel, de is is beolvasása fizetett funkciók a B2B vendégfelhasználók számára"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: sasubram
ms.custom: it-pro
ms.openlocfilehash: dfef32c05af157ae8d3a5434016f87f488a35051
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Útmutató az Azure Active Directory vállalatközi együttműködés licenceléséhez

Azure AD B2B együttműködés képességek segítségével hívhat meg a vendégfelhasználók be az Azure AD-bérlőn, hogy azok képesek legyenek elérni az Azure AD-szolgáltatások és egyéb erőforrásokat a szervezetben. Ha lehetővé szeretné tenni a hozzáférést a fizetős Azure AD-funkciókat, a B2B együttműködés vendégfelhasználók licenccel kell rendelkezniük a megfelelő Azure AD licencet. 

Konkrétan:
* Az Azure AD ingyenes képességek érhetők el a vendégfelhasználók külön engedélye nélkül.
* Ha lehetővé szeretné tenni a hozzáférést fizetős Azure AD-funkciókat B2B felhasználók számára, hogy elegendő licenccel B2B vendég felhasználók támogatására kell rendelkezik.
* Az hívja fel egy licencet a fizetős Azure ad-bérlő tartozik B2B együttműködés jogosultsága ahhoz, hogy egy további, meghívót, hogy a bérlő öt B2B vendégfelhasználók használja.
* Az ügyfél hívja fel a bérlő birtokló annak meghatározásához, hány B2B együttműködés a felhasználóknak kell fizetni az Azure AD-lehetőségek kell lennie. Attól függően, hogy a fizetős Azure AD szolgáltatásokra van szükség a vendég felhasználók rendelkeznie kell ahhoz az Azure AD fizetős abba B2B együttműködés felhasználók 5:1 arányban licenceket.

A B2B együttműködés Vendég felhasználó meg van adva egy felhasználó egy partnervállalatnak, nem egy alkalmazott a szervezet vagy egy alkalmazott a konglomerátum különböző üzleti. A B2B Vendég felhasználó is jelentkezzen be a külső hitelesítő adatokat vagy a hitelesítő adatokat a szervezet által birtokolt, ebben a cikkben leírtak szerint. 

Más szóval B2B licencelési értéke nem a felhasználók hitelesítése hogyan történjen, hanem inkább a kapcsolatot a felhasználó a szervezetben. Ha ezek a felhasználók nem partnerek, ezeket kell kezelni eltérően a licencelési időszakonként. Nem tekinthetők kell lennie a B2B együttműködés felhasználó licenckezelési célokból, akkor is, ha azok UserType attribútuma "Vendég". Ezek engedéllyel kell rendelkeznie általában, minden felhasználóhoz egy-egy licencet. Ezek a felhasználók a következők:
* Az alkalmazottak
* A személyzet külső identitásokkal bejelentkezés
* Egy alkalmazott a konglomerátum különböző üzleti


## <a name="licensing-examples"></a>Licencelési példák
- Egy ügyfél szeretne az Azure AD-bérlő 100 B2B együttműködés felhasználók meghívása. Az ügyfél hozzárendeli a hozzáférés-kezelés és a kiépítés az összes felhasználó számára, azonban 50 felhasználóinak is kell-e többtényezős hitelesítés és a feltételes hozzáférés. Ez az ügyfél 10 Azure AD alapvető licencek és 10 Azure AD Premium P1 licencek fedik le a B2B felhasználók megfelelően kell vásárolnia. Ha az ügyfél B2B felhasználóival Identity Protection használatát, az Azure AD Premium P2 licenceket, amelyek a meghívott felhasználók 5:1 arányban kell rendelkezniük.
- Az ügyfél 10 alkalmazottak, akik az Azure AD Premium P1 jelenleg licencét rendelkezik. Most szeretne meghívott 60 B2B felhasználóknak, akik többtényezős hitelesítést (MFA). 5:1-engedélyezési szabály alapján az ügyfél rendelkeznie kell legalább 12 Azure AD Premium P1 licencek 60 B2B együttműködés felhasználók fedik le. Már rendelkeznek 10 Premium P1 licencek 10 az alkalmazottak, mert a meghívott Premium P1 szolgáltatások, mint az MFA 50 B2B felhasználó engedéllyel rendelkeznek. Ebben a példában, majd azok kell 2 további Premium P1 licencek vásárlása fedik le a fennmaradó 10 B2B együttműködés felhasználók.

> [!NOTE]
> Nincs még rendelhet hozzá licenceket közvetlenül ezek B2B együttműködés a felhasználói jogosultságok engedélyezése a B2B felhasználók mód.

Az ügyfél hívja fel a bérlő birtokló annak meghatározásához, hány B2B együttműködés a felhasználóknak kell fizetni az Azure AD-lehetőségek kell lennie. Attól függően, amelyek fizetős Azure AD-funkciókat a vendégfelhasználók használni szeretne elég az Azure AD licencet fizetős abba B2B együttműködés felhasználók az 5:1 arányban kell rendelkeznie. 

## <a name="additional-licensing-details"></a>További licencelési részletei
- Nincs szükség az ténylegesen licencek hozzárendelése B2B felhasználói fiókokat. A 5:1 arányban alapján, licencelési automatikusan kiszámítása és jelenteni.
- Van-e nem fizetős Azure AD licencet a bérlő minden a meghívott felhasználó lekérdezi, hogy az Azure AD ingyenes kiadás kínál.
- Ha a B2B együttműködés a felhasználónak már van egy fizetős Azure AD a szervezet licencszerződés, nem használják ki a B2B együttműködés licencek hívja fel a bérlő egyikét.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Az ismertető speciális: Ha azt egy konglomerátum szervezetben az API-k "tagok" felhasználók felvétele a licencelési szempontok Mik?
A B2B Vendég felhasználó egy, a fiókpartner-szervezet meghívót, hogy a gazdagép szervezet dolgozni. Általában minden más esetben nem minősül B2B még azt B2B funkciókat használja. Nézzük két esetben különösen:

1. Ha egy állomás meghívja az alkalmazott fogyasztói címet használ
  * Ez a forgatókönyv nem megfelel az engedélyezési házirendeket, és nem ajánlott.

2. Ha egy állomás szervezet hozzáad egy felhasználót egy másik konglomerátum szervezet
  1. Ebben az esetben a felhasználó felkérik B2B API-k használatával, de ebben az esetben nincs hagyományosan B2B. Ideális esetben azt kell rendelkeznie a más szervezethez felhasználók meghívása (az API felületen lehetővé teszi, hogy) tagként szervezetek számára. Ebben az esetben licencek kell hozzárendelni ezekről a tagokról számukra hívja fel a szervezet erőforrásaihoz.

  2. Egyes szervezetek adja hozzá a szervezeti felhasználók hozzáadandó "Vendég" házirend-érdemes. Két olyan eset létezik itt:
      * A konglomerátum munkahelyén már működik a Azure AD és a meghívott felhasználók licencét a másik szervezet: Ebben az esetben kövesse a jelen dokumentum korábbi leírva 1:5 képlet meghívott felhasználók nem várhatóan. 

      * A konglomerátum szervezete nem használja az Azure AD, vagy nem rendelkezik megfelelő licencek: Ebben az esetben, hajtsa végre az 1:5 képlet a jelen dokumentum korábbi leírva.

## <a name="next-steps"></a>Következő lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hogyan rendszergazdák Azure Active Directory B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
* [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
* [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
* [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
* [Hibaelhárítás az Azure Active Directory B2B együttműködés](active-directory-b2b-troubleshooting.md)
* [Az Azure Active Directory B2B együttműködés gyakori kérdések (GYIK)](active-directory-b2b-faq.md)
* [Az Azure Active Directory B2B együttműködés API és a Testreszabás](active-directory-b2b-api.md)
* [Többtényezős hitelesítés a B2B-együttműködés felhasználói számára](active-directory-b2b-mfa-instructions.md)
* [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
* [Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke](active-directory-apps-index.md)
