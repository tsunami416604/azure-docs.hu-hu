---
title: Az Azure Active Directory B2B-együttműködés licencelése útmutató |} A Microsoft Docs
description: A fizetős Azure AD-licenceket az Azure Active Directory B2B együttműködés nem igényel, de Ön is is első díjkötelessé B2B vendégfelhasználó
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: mimart
author: msmimart
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1d92f68bbb5e8c001594e4f78f90cb10496aaf29
ms.sourcegitcommit: 776b450b73db66469cb63130c6cf9696f9152b6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2018
ms.locfileid: "45984492"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Útmutató az Azure Active Directory vállalatközi együttműködés licenceléséhez

Az Azure AD B2B együttműködési lehetőségekhez segítségével vendégfelhasználók meghívása az Azure AD-bérlője, hogy hozzáférjenek az Azure AD-szolgáltatások és egyéb erőforrásokat a szervezetben. Ha azt szeretné, így biztosítva hozzáférést a fizetős Azure AD-funkciók, B2B együttműködési vendégfelhasználók licencelni kell a megfelelő Azure AD-licenceket. 

Konkrétan:
* Az Azure AD ingyenes képességek érhetők el a vendégfelhasználók számára külön engedélye nélkül.
* Ha azt szeretné, így biztosítva hozzáférést a fizetős Azure AD-funkciók a B2B-felhasználók számára, B2B vendég felhasználók támogatására elegendő licenccel kell rendelkeznie.
* Egy meghívó egy fizetett licenc Azure AD-bérlőnek B2B-együttműködés egy további, a bérlőbe meghívott öt B2B vendégfelhasználók engedélyei segítségével.
* Az ügyfél a meghívó bérlőben birtokló azt határozza meg, hogy hány B2B-együttműködés a felhasználóknak kell fizetős Azure AD-képességekhez, kell lennie. Attól függően, a fizetős Azure ad-ben használni szeretne a vendég felhasználók kell rendelkeznie ahhoz az Azure AD szolgáltatások fizetős B2B együttműködési felhasználókat 5:1 arányban szintű licenceknek.

B2B együttműködés vendégfelhasználó egy partnervállalatnak, nem a szervezet egy alkalmazott vagy egy alkalmazott a konglomerátum különböző üzleti felhasználói egészül ki. B2B-vendégként felhasználó bejelentkezhet a külső vagy a vállalat tulajdonában ebben a cikkben leírtak szerint. 

Más szóval B2B-licencelés értéke nem a felhasználó hitelesítése hogyan történjen a, hanem a kapcsolatot a felhasználó a szervezet számára. Ha ezek a felhasználók nem partnerek, ezeket kell kezelni eltérően a licencelési időszakonként. Nem tekinthetők a B2B-együttműködés felhasználói kell licenckezelési célokból, akkor is, ha azok UserType van megjelölve a "Vendég". Ezek engedéllyel kell rendelkeznie általában, felhasználónként egy-egy licencet. Ezek a felhasználók a következők:
* Az alkalmazottak
* Bejelentkezés külső identitások használatával személyzet
* Egy alkalmazott a konglomerátum különböző üzleti


## <a name="licensing-examples"></a>Licencelési példák
- Szeretne meghívni a B2B együttműködés 100 felhasználó, az Azure AD-bérlővel. Az ügyfél rendeli hozzá a hozzáférés-kezelés és üzembe helyezés az összes felhasználó számára, de 50 felhasználó is megkövetelheti a többtényezős hitelesítés és a feltételes hozzáférés. Az ügyfél 10 alapszintű Azure AD-licenceket, és 10 Azure AD Premium P1 szintű licenceknek a B2B-felhasználók megfelelően kell vásárolnia. Ha az ügyfél-csomagok Identitásvédelmi funkciók használata a B2B-felhasználók, az Azure AD Premium P2 szintű licenceknek a meghívott felhasználók 5:1 arányban kell rendelkezniük.
- Egy ügyfél rendelkezik az összes jelenleg az Azure AD Premium P1 licenccel rendelkező 10 alkalmazottal. Most szeretné meghívni 60 B2B-felhasználók, akik többtényezős hitelesítést (MFA). 5:1-engedélyezési szabály alapján az ügyfél legalább 12 Azure AD Premium P1 szintű licenceknek 60 B2B együttműködési felhasználókat kell rendelkeznie. Prémium P1 szintű 10 licencből 10 az alkalmazottak már van, mert 50 prémium P1 szintű szolgáltatásai, például a többtényezős hitelesítés a B2B-felhasználók meghívni engedéllyel rendelkeznek. Ebben a példában, majd azok kell vásárolnia 2 további prémium P1 szintű licenceknek a fennmaradó 10 B2B együttműködési felhasználókat.

> [!NOTE]
> Nincs még nem lehet közvetlenül a B2B-felhasználók ezeket B2B-együttműködés felhasználói jogosultsággal ahhoz, hogy a licencek hozzárendelése.

Az ügyfél a meghívó bérlőben birtokló azt határozza meg, hogy hány B2B-együttműködés a felhasználóknak kell fizetős Azure AD-képességekhez, kell lennie. Attól függően, amelyek fizetős Azure AD-funkciók esetében a vendégfelhasználók szeretné elég fizetett Azure AD-licenccel ahhoz, hogy biztosítsák az 5:1 arányt a B2B-együttműködés felhasználók kell rendelkeznie. 

## <a name="additional-licensing-details"></a>További licencelési részletei
- Hiba esetén nem kell ténylegesen a B2B-felhasználói fiókok licencet rendelni. A 5:1 arány alapján a licencelési automatikusan kiszámítása és jelentett.
- Ha fizetős nem létezik-e az Azure AD-licencre a bérlő minden meghívott felhasználó lekérdezi, hogy az Azure AD ingyenes kiadásának kínál.
- A B2B-együttműködés felhasználó már rendelkezik egy fizetős Azure AD licencet a saját szervezet, ezek nem használnak fel a meghívó bérlőben a B2B együttműködés licencek egyikét.

## <a name="advanced-discussion-what-are-the-licensing-considerations-when-we-add-users-from-a-conglomerate-organization-as-members-using-your-apis"></a>Speciális vitafórum: Mik azok a licencelési kapcsolatos szempontokat, amikor hozzáadjuk a felhasználókat tagokként"" API-k használatával egy konglomerátum szervezet?
B2B vendégfelhasználó, amelyik a fiókpartner-szervezet felkérik, hogy a gazdagép szervezettel működik. Általában minden más esetben nem minősül B2B még akkor is, használja a B2B-funkciói. Nézzük meg két esetek különösen:

1. Ha egy gazdagép egy-egy fogyasztó címmel alkalmazott szóló meghívásról
  * Ebben a forgatókönyvben nem felelnek meg a licencelési szabályokat, és nem ajánlott.

2. Ha egy gazdagép szervezet hozzáad egy felhasználót egy másik konglomerátum szervezet
  1. Ebben az esetben a felhasználót a cégbe B2B API-k használatával, de ebben az esetben nem hagyományosan B2B. Ideális esetben ezek a szervezetek a szervezetek számára más felhasználók meghívása (az API lehetővé teszi, hogy) tagként kell állnia. Ebben az esetben licenceket kell ezekről a tagokról azok esetében a meghívó szervezetet erőforrásaihoz kell hozzárendelni.

  2. Egyes szervezetek szeretné hozzáadni a szervezeti felhasználók "Vendég" házirend-hozzáadni. Van itt két esetet:
      * A konglomerátum szervezet már használja az Azure AD és a meghívott felhasználók-licencek, a másik szervezet: Ebben az esetben várhatóan nem lesz szüksége, kövesse a jelen dokumentum korábbi elrendezve 1:5 képlet meghívott felhasználók. 

      * A konglomerátum szervezet nem használ Azure ad-ben, vagy nem rendelkezik a megfelelő licencek: Ebben az esetben kövesse a jelen dokumentum korábbi elrendezve 1:5 képlet.

## <a name="next-steps"></a>További lépések

Az Azure AD B2B együttműködés a következő cikkekben talál:

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az Azure Active Directory B2B-együttműködés – gyakori kérdések (GYIK)](faq.md)
