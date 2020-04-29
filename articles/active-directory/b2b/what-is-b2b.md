---
title: Mi a B2B együttműködés a Azure Active Directoryban?
description: Az Azure Active Directory B2B együttműködés támogatja a vendég hozzáférést, így biztonságosan megoszthat erőforrásokat és együttműködhet külső partnerekkel.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: overview
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 167c7f7aa3dbf967575c0ee8905821fb9d3f77ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80050729"
---
# <a name="what-is-guest-user-access-in-azure-active-directory-b2b"></a>Mi az vendég hozzáférés az Azure Active Directory B2B-ben?

Az Azure Active Directory (Azure AD) vállalatok közötti (B2B) együttműködési megoldása lehetővé teszi a vállalati alkalmazások és szolgáltatások bármely más szervezet vendégfelhasználóival való megosztását, miközben megőrzi a saját vállalat adatai feletti irányítást is. Biztonságban, biztosan dolgozhat -nagy vagy kicsi- külső partnerekkel, akkor is, ha nincs is náluk Azure AD vagy IT részleg. Az egyszerű meghívási és érvényesítési folyamat lehetővé teszi, hogy a vállalati erőforrásokhoz a partnerek saját hitelesítő adatokkal férjenek hozzá. A fejlesztők az Azure AD B2B API használatával személyre szabhatják a meghívási folyamatot vagy például önkiszolgáló regisztrációs portál alkalmazásokat írhatnak.

A videóból megtudhatja, hogyan dolgozhat együtt biztonságosan a vendégfelhasználókkal úgy, hogy meghívja őket, hogy jelentkezzenek be a vállalati alkalmazásba és szolgáltatásokba saját azonosítójukkal.

Az alábbi videó hasznos áttekintést nyújt.

>[!VIDEO https://www.youtube.com/embed/AhwrweCBdsc]

   > [!IMPORTANT]
   > **2021. március 31-ig**a Microsoft többé nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure ad-fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. A felkészülés során javasoljuk, hogy az ügyfelek [e-mail-egyszeri jelszavas hitelesítést](one-time-passcode.md)kérjenek. Szívesen fogadjuk visszajelzését ezen a nyilvános előzetes verzióban, és örömmel vesszük, hogy még több módszert is létre lehetne hozni az együttműködésre.

## <a name="collaborate-with-any-partner-using-their-identities"></a>Együttműködés bármely partnerrel saját identitásuk használatával

Az Azure AD B2B esetén a partner saját identitáskezelési megoldását használja, így nem jelent külső adminisztrációs terhelést a szervezet számára.

- A partner saját identitását és hitelesítő adatait használja; Azure AD nem szükséges.
- Nem kell külső fiókokat és jelszavakat kezelnie.
- Nem kell fiókokat szinkronizálni és a fiók életciklust kezelni.  

![A tagok hozzáadása lap képernyőképe](media/what-is-b2b/add-member.png)

## <a name="invite-guest-users-with-a-simple-invitation-and-redemption-process"></a>Vendégfelhasználók meghívása egyszerű meghívási és érvényesítési folyamattal

A vendégfelhasználók a saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkeznek be az alkalmazásokba és szolgáltatásokba. Ha a Vendég felhasználó nem rendelkezik Microsoft-fiókkal vagy Azure AD-fiókkal, akkor a meghívó beváltásakor annak a létrehozása is megtörténik. 

- Hívja meg a vendégfelhasználókat az általuk választott e-mail azonosítóval.
- Küldhet közvetlen az alkalmazásra mutató hivatkozást vagy meghívót a felhasználó saját hozzáférési paneljére.
- A vendégfelhasználók néhány egyszerű érvényesítési lépéssel jelentkezhetnek be.

![A felülvizsgálati engedélyek oldalát ábrázoló képernyőkép](media/what-is-b2b/consentscreen.png)

## <a name="use-policies-to-securely-share-your-apps-and-services"></a>Házirendek használata az alkalmazások és szolgáltatások biztonságos megosztásához

Az engedélyezési házirendek segítségével biztosíthatja a vállalati tartalmak védettségét. A feltételes hozzáférési szabályzatok, például a többtényezős hitelesítés, kényszeríthető:

- A bérlői szinten.
- Az alkalmazási szinten.
- Meghatározott vendégfelhasználók számára a vállalati alkalmazások és adatok védelmére.

![A feltételes hozzáférés beállítását bemutató képernyőkép](media/what-is-b2b/tutorial-mfa-policy-2.png)


## <a name="easily-add-guest-users-in-the-azure-ad-portal"></a>A vendégfelhasználók hozzáadása könnyű az Azure AD Portalon

A rendszergazdák egyszerűen felvehetnek vendégfelhasználókat a szervezetbe az Azure portálon.

- Az új vendégfelhasználó létrehozása az Azure AD-ben hasonló, mint az új felhasználó felvétele.
- A vendégfelhasználó azonnal kap egy testre szabható meghívót, amely lehetővé teszi, hogy bejelentkezzen a hozzáférési panelen.
- A vendégfelhasználók a címtárban hozzárendelhetők alkalmazásokhoz és csoportokhoz.  

![Képernyőfelvétel az új vendég felhasználó Meghívási bejegyzését megjelenítő oldalról](media/what-is-b2b/add-a-b2b-user-to-azure-portal.png)

## <a name="let-application-and-group-owners-manage-their-own-guest-users"></a>Alkalmazás és csoport gazdák számára saját vendégfelhasználóik kezelésének lehetővé tétele

A vendégfelhasználók kezelését rábízhatja a felhasználó tulajdonosokra, hogy közvetlenül ők vehessék fel a vendég felhasználókat a megosztani kívánt alkalmazásokba, akár Microsoft alkalmazásról van szó, akár nem.

- A rendszergazdák önkiszolgáló alkalmazás- és csoportkezelést állíthatnak be.
- A nem rendszergazdák a [Hozzáférési Panel](https://myapps.microsoft.com) használatával adhatnak vendégfelhasználókat az alkalmazásokhoz és csoportokhoz.

![Képernyőfelvétel: a vendég felhasználó hozzáférési paneljének megjelenítése](media/what-is-b2b/access-panel-manage-app.png)

## <a name="customize-the-onboarding-experience-for-b2b-guest-users"></a>A B2B vendég felhasználók bevezetési élményének testreszabása

Külső partnereit saját szervezete igényeinek megfelelően vegye fel.

- A [külső felhasználók hozzáférését kezelő](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-external-users#how-access-works-for-external-users)házirendek konfigurálásához használja az [Azure ad-jogosultságok kezelését](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) .
- A [B2B együttműködés Meghívási API-jai](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) segítségével testre szabhatja a bevezetési élményeket.

## <a name="next-steps"></a>További lépések

- [Azure AD B2B-együttműködés licencelési útmutató](licensing-guidance.md)
- [B2B közreműködő vendégfelhasználók hozzáadása a portálon](add-users-administrator.md)
- [A meghívó érvényesítési folyamat ismertetése](redemption-experience.md)
- És mint mindig, visszajelzésekkel, hozzászólásokkal és javaslatokkal most is fordulhat a termékért felelős csoporthoz a [Microsoft technikai Közösségen](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b) keresztül.
