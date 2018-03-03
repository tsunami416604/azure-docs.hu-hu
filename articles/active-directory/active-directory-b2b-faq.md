---
title: "Az Azure Active Directory B2B együttműködés – gyakori kérdések |} Microsoft Docs"
description: "Azure Active Directory B2B együttműködés gyakran feltett kérdésekre adott válaszok."
services: active-directory
documentationcenter: 
author: twooley
manager: mtillman
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 01/08/2018
ms.author: twooley
ms.reviewer: sasubram
ms.openlocfilehash: 21511b37eba2cd2e87d8720f3e9cfe453b50d88c
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Az Azure Active Directory B2B együttműködés – gyakori kérdések

Gyakori kérdések (GYIK) kapcsolatos Azure Active Directory (Azure AD) üzleti vállalatközi (B2B) együttműködés rendszeresen frissülnek új témaköröket tartalmazza.

### <a name="can-we-customize-our-sign-in-page-so-it-is-more-intuitive-for-our-b2b-collaboration-guest-users"></a>A Microsoft testre az bejelentkezési oldalra, hogy a a B2B együttműködés vendégfelhasználók intuitívabb?
Feltétlenül! Tekintse meg a [erről a szolgáltatásról blogbejegyzés](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). A szervezet bejelentkezési lap testreszabásával kapcsolatos további információkért lásd: [adja hozzá a vállalati arculat megjelenítése a bejelentkezéshez és a hozzáférési Panel oldalakon](customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>SharePoint Online és onedrive vállalati verzió hozzáférhet B2B együttműködés felhasználók?
Igen. Keresse meg a SharePoint Online meglévő vendégfelhasználók a személyek kiválasztása segítségével képes azonban **ki** alapértelmezés szerint. Kapcsolja be a meglévő vendégfelhasználók kereséséhez, állítsa **ShowPeoplePickerSuggestionsForGuestUsers** való **a**. Ha bekapcsolja ezt a beállítást a bérlői szintjén vagy a gyűjtemény szintjén. Ez a beállítás a Set-SPOTenant és a Set-SPOSite-parancsmagok használatával módosíthatja. Ezeket a parancsmagokat a tagok összes meglévő vendégfelhasználók kereshet a címtárban. A bérlői hatókört változásai nem befolyásolják a SharePoint Online-webhelyhez, amely már megtörtént.

### <a name="is-the-csv-upload-feature-still-supported"></a>A CSV-feltöltési szolgáltatás továbbra is támogatott?
Igen. A .csv fájl feltöltési szolgáltatás használatával kapcsolatos további információkért lásd: [a PowerShell-példa](active-directory-b2b-code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hogyan szabható testre a meghívó e-mailt?
A meghívó folyamattal kapcsolatos szinte minden segítségével szabhatja testre a [B2B meghívó API-k](active-directory-b2b-api.md).

### <a name="can-an-invited-external-user-leave-the-organization-after-being-invited"></a>A meghívott külső felhasználó elhagyja a szervezet meghívásának után?
Hívja fel a szervezet rendszergazdája B2B együttműködés Vendég felhasználó törlése a könyvtárból, de a Vendég felhasználó nem léphet ki hívja fel a szervezet címtárához önmagában. 

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Visszaállíthatja a vendégfelhasználók a többtényezős hitelesítést?
Igen. Vendégfelhasználók azonos módon rendszeres felhasználók visszaállíthatják a multi-factor authentication módszer.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Melyik szervezetben felelős a multi-factor authentication licenceket?
Hívja fel a szervezet a multi-factor Authentication hitelesítést hajt végre. Hívja fel a szervezet kell győződjön meg arról, hogy a szervezet rendelkezik-e elegendő licenccel a B2B többtényezős hitelesítést használó felhasználók számára.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Mi történik, ha egy fiókpartner-szervezet már rendelkezik állítson be többtényezős hitelesítést? Is azt a többtényezős hitelesítés megbízható, és nem a saját többtényezős hitelesítés használata?
Ez a szolgáltatás egy későbbi kiadásban tervezett, akkor egyes partnerek kizárását is kiválaszthat, a többtényezős hitelesítést (a hívja fel szervezet).

### <a name="how-can-i-use-delayed-invitations"></a>Hogyan használhatók késleltetett meghívókat?
Egy szervezet B2B együttműködés felhasználók hozzáadása az alkalmazások szükség szerint oszthatják és elküldheti-e meghívókat kíván. A B2B együttműködés meghívót API segítségével testre szabhatja a bevezetési munkafolyamat.

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Tehetők a Vendég felhasználó korlátozott jogokkal rendelkező rendszergazda?
Abszolút. További információkért lásd: [vendég felhasználók hozzáadása az adott szerepkörhöz](active-directory-users-assign-role-azure-portal.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B együttműködés lehetővé teszi a B2B felhasználók Azure-portál eléréséhez?
Ha a felhasználó a korlátozott rendszergazdai vagy globális rendszergazdai szerepkör van hozzárendelve, B2B együttműködés felhasználók nem csak az Azure-portálon. Azonban B2B együttműködés korlátozott jogokkal rendelkező rendszergazda vagy a globális rendszergazdai szerepkörhöz hozzárendelt felhasználók a portál eléréséhez. Emellett, ha nincs hozzárendelve a rendszergazdai szerepkörök valamelyikét egy Vendég felhasználó hozzáfér a portálon, a felhasználó lehet érhessék el az egyes részeit a felhasználói élmény. A Vendég felhasználói szerepkör egyes jogosult a címtárban.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Letilthatom az Azure portálra a vendégfelhasználók hozzáférés?
Igen! Ez a házirend konfigurálásakor ügyeljen arra, hogy elkerülése érdekében a tagok és rendszergazdái véletlenül blokkoló elérésére.
A Vendég felhasználó hozzáférését blokkolja a [Azure-portálon](https://portal.azure.com), a feltételes hozzáférési házirend a Windows Azure klasszikus telepítési modell API használata:
1. Módosítsa a **minden felhasználó** csoportot, hogy csak a tagot tartalmaz.
  ![módosíthatja a csoport képernyőképe](media/active-directory-b2b-faq/modify-all-users-group.png)
2. Hozzon létre egy dinamikus csoportot, amely tartalmazza a vendégfelhasználók számára.
  ![képernyőfelvétel a csoport létrehozása](media/active-directory-b2b-faq/group-with-guest-users.png)
3. Beállítása egy feltételes hozzáférési házirend vendég felhasználók blokkolása nem férhet hozzá a portálon, ahogy az az alábbi videó:
  
  > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Többtényezős hitelesítés és a fogyasztói e-mail fiókok támogatja Azure AD B2B együttműködés?
Igen. Többtényezős hitelesítés és a fogyasztói e-mail fiókok is támogatott az Azure AD B2B együttműködés.

### <a name="do-you-plan-to-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Biztosítani kívánja a támogatja a jelszó alaphelyzetbe állítása a felhasználók a Azure AD B2B együttműködés?
Igen. Az alábbiakban az önkiszolgáló jelszó-visszaállítási (SSPR) B2B felhasználó felkérik, a fiókpartner-szervezet számára fontos részleteket:
 
* SSPR csak a B2B felhasználói identitás bérlő történik.
* Ha az identitás bérlői Microsoft-fiókkal, a Microsoft-fiók SSPR mechanizmus szolgál.
* Ha az identitás-bérlő a közvetlenül az igény (szerinti JIT) vagy a "ugrásszerű" bérlői, a jelszó alaphelyzetbe állítása e-mailt küld el.
* Más bérlők számára a standard SSPR folyamat B2B felhasználók követi. Tag SSPR B2B felhasználók esetében az a környezetben, az erőforrás, például a vállalat kiszolgálása le van tiltva. 

### <a name="is-password-reset-available-for-guest-users-in-a-just-in-time-jit-or-viral-tenant-who-accepted-invitations-with-a-work-or-school-email-address-but-who-didnt-have-a-pre-existing-azure-ad-account"></a>Jelszó alaphelyzetbe áll rendelkezésre a vendégfelhasználók a közvetlenül az igény (szerinti JIT) a, vagy "ugrásszerű" bérlői elfogadó meghívókat munkahelyi vagy iskolai e-mail címet, de nem volt, akik egy már meglévő Azure AD-fiókot?
Igen. A jelszó alaphelyzetbe állítása e-mail küldhető, amely lehetővé teszi a felhasználó az igény szerinti bérleti jelszavuk.

### <a name="does-microsoft-dynamics-crm-provide-online-support-for-azure-ad-b2b-collaboration"></a>Nem Microsoft Dynamics CRM online támogatást nyújthat olyan Azure AD B2B együttműködés?
Jelenleg Microsoft Dynamics CRM nem online támogatást nyújt az Azure AD B2B együttműködés. Azonban tervezzük támogatja ezt a jövőben.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Mi az, hogy egy újonnan létrehozott B2B együttműködés felhasználó egy kezdeti jelszó élettartama?
Meghatározott karakter, a jelszó erősségét és a fiók zárolása vonatkozó követelmények egyaránt minden Azure ad cloud felhasználói fiókok Azure AD is rendelkezik. Felhő felhasználói fiókok azok a fiókok, a rendszer nem összevont egy másik identitásszolgáltatóval, például a 
* Microsoft-fiók
* Facebook
* Active Directory összevonási szolgáltatások
* Egy másik felhőben bérlőt (B2B együttműködés)

Jelszóházirend összevont fiókok a helyszíni bérlet és a felhasználó Microsoft-fiók beállításai alkalmazott házirend függ.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Egy szervezet előfordulhat, hogy szeretné különböző élmények az alkalmazásokban a bérlő felhasználók és a vendégfelhasználók. A szokásos útmutató van? Az identitásszolgáltató jelenléte jogcímek a megfelelő modellt használja?
 A Vendég felhasználó bármely identitásszolgáltató segítségével hitelesíteni. További információkért lásd: [B2B együttműködés felhasználó tulajdonságainak](active-directory-b2b-user-properties.md). Használja a **UserType** felhasználói felületére vonatkozó tulajdonság. A **UserType** jogcím jelenleg nem része a token. Alkalmazások használjon a Graph API lekérdezi a felhasználó a directory, valamint hogy a UserType.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Hol találok nyújt megoldások megosztása és ötleteket a B2B együttműködés közösségi?
Jelenleg folyamatban folyamatosan figyel a visszajelzést B2B együttműködés javítása érdekében. Szeretnénk felhívni a megosztásához a felhasználó forgatókönyvek, ajánlott eljárások és például Azure AD B2B együttműködés kapcsolatban. Az ismertető csatlakozott a [a Microsoft technikai közösségi](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Azt is hívhat meg, hogy a ötleteket és a jövőbeli szolgáltatásokat szavazzon [B2B együttműködés ötleteket](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>A Microsoft küldhet, amely automatikusan beváltott, meghívót, hogy a felhasználó csak "készen áll"? Vagy nem a felhasználó mindig kell kattintania a visszaváltási URL-címet?
A felhasználó a hívja fel őt is a tagja a fiókpartner-szervezet által küldött meghívót nem igényelnek visszaváltás a B2B felhasználó.

Azt javasoljuk, hogy egy felhasználó a partnerszervezet hívja fel a szervezet csatlakozni meghívott. [Adja hozzá a felhasználót a Vendég meghívó szerepkörhöz az erőforrás-szervezetben](active-directory-b2b-add-guest-to-role.md). Ez a felhasználó a fiókpartner-szervezet belüli más felhasználók is meghívhatja a bejelentkezési felhasználói felület, PowerShell-parancsfájlok használatával vagy API-t. Ezt követően B2B együttműködés felhasználóit, hogy a szervezet a meghívást beváltani megnyitásáig nem szükséges.

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hogyan működik a B2B együttműködés a Ha a meghívott partner összevonási segítségével adja hozzá a saját helyszíni hitelesítéssel?
Ha a partner, amely a helyszíni hitelesítési infrastruktúra össze van vonva az Azure AD-bérlő, a helyszíni egyszeri bejelentkezés (SSO) automatikusan érhető el. Ha a partner az Azure AD-bérlő nem rendelkezik, az Azure AD-fiókot hoz létre az új felhasználók. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>I-re Azure AD B2B nem fogadta el a gmail.com és Outlook.com-os e-mail címet, és a B2C használatát az ilyen típusú fiókok?
Megszüntetjük a B2B és üzleti-fogyasztói (B2C) együttműködés szempontjából identitások támogatottak közötti különbséget. A használt azonosító nem B2B vagy B2C használatával választhat jó oka. Az együttműködési lehetőség kiválasztásával kapcsolatos információkért lásd: [összehasonlítása B2B együttműködés és az Azure Active Directory B2C](active-directory-b2b-compare-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Milyen alkalmazások és szolgáltatások támogatják az Azure B2B vendégfelhasználók?
Minden Azure Active Directoryba integrált alkalmazások Azure B2B vendég felhasználók támogatására. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>A Microsoft kényszerítheti a többtényezős hitelesítést a B2B vendégfelhasználók Ha partnereink nem rendelkezik a multi-factor authentication?
Igen. További információkért lásd: [feltételes hozzáférés a B2B együttműködés felhasználók](active-directory-b2b-mfa-instructions.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>A SharePoint definiálhat egy külső felhasználók "engedélyezése" vagy "Elutasítás" listája. A Microsoft ehhez az Azure-ban?
Igen. Az Azure AD B2B együttműködés támogatja engedélyezési listák, és letiltási listáját. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Milyen licencek szükséges a Azure AD B2B használni?
További információ a mi licencek, a szervezeti igényeknek használata az Azure AD B2B: [útmutatást licencelési Azure Active Directory B2B együttműködés](active-directory-b2b-licensing.md).

### <a name="next-steps"></a>További lépések

Ismerje meg az Azure AD B2B együttműködés további cikkeit:

* [Mi az az Azure AD B2B együttműködés?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [Hogyan rendszergazdák az Azure AD B2B együttműködés felhasználók hozzá?](active-directory-b2b-admin-add-users.md)
* [Hogyan hozzá az információkkal dolgozó szakemberek B2B együttműködés felhasználók?](active-directory-b2b-iw-add-users.md)
* [A B2B együttműködés meghívó e-mail elemei](active-directory-b2b-invitation-email.md)
* [B2B együttműködés meghívó érvényesítési](active-directory-b2b-redemption-experience.md)
* [Az Azure AD B2B együttműködés licencelés](active-directory-b2b-licensing.md)
* [Hibaelhárítás az Azure AD B2B együttműködés](active-directory-b2b-troubleshooting.md)
* [Az Azure AD B2B együttműködés API és a Testreszabás](active-directory-b2b-api.md)
* [Többtényezős hitelesítés a B2B-együttműködés felhasználói számára](active-directory-b2b-mfa-instructions.md)
* [Adja hozzá a B2B együttműködés felhasználók nélkül](active-directory-b2b-add-user-without-invite.md)
* [Alkalmazások kezelése az Azure AD-cikk indexe](active-directory-apps-index.md)
