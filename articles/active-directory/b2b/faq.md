---
title: B2B-együttműködés – gyakori kérdések – Azure Active Directory |} A Microsoft Docs
description: Válaszok az Azure Active Directory B2B-együttműködés – gyakori kérdések.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 04/10/2019
ms.author: mimart
author: msmimart
manager: celested
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d361a40e2efa80a677255c02c7e9cf155af09ffb
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59496301"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Az Azure Active Directory B2B-együttműködés – gyakori kérdések

Ezek az Azure Active Directory (Azure AD)-vállalatközi (B2B) együttműködés kapcsolatos gyakori kérdések (GYIK) rendszeres időközönként frissülnek az új témaköröket tartalmazza.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Hogy testre a bejelentkezési oldalát, hogy a B2B együttműködési vendégfelhasználók számára intuitívabb legyen?
Feltétlenül! Tekintse meg a [funkcióról szóló blogbejegyzést](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/). A munkahelyi bejelentkezési lap testreszabásával kapcsolatos további információkért lásd: [adja hozzá a vállalati arculat megjelenítése a bejelentkezés és a hozzáférési Panel oldalakon](../fundamentals/customize-branding.md).

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>A SharePoint Online és onedrive vállalati verzió hozzáférhet B2B együttműködési felhasználókat?
Igen. Van azonban arra, hogy keresse meg a SharePoint online-ban meglévő vendégfelhasználókat a személyválasztója segítségével **ki** alapértelmezés szerint. Kapcsolja be a beállítást meglévő vendég felhasználók kereséséhez, állítsa **ShowPeoplePickerSuggestionsForGuestUsers** való **a**. Ez a beállítás bekapcsolhatja vagy bérlői szinten vagy a gyűjtemény szintjén. Ez a beállítás a Set-SPOTenant és Set-SPOSite parancsmagok használatával módosíthatja. Ezeket a parancsmagokat a tagok kereshet az összes meglévő vendégfelhasználókat a címtárban. A bérlői hatókörben módosítása nem érinti a SharePoint Online-webhelyekkel már kiépített.

### <a name="is-the-csv-upload-feature-still-supported"></a>A fürt megosztott kötetei szolgáltatás feltöltési funkció továbbra is támogatott?
Igen. A .csv fájl feltöltési funkció használatával kapcsolatos további információkért lásd: [a PowerShell-mintát](code-samples.md).

### <a name="how-can-i-customize-my-invitation-emails"></a>Hogyan szabhatja testre a meghívó e-mailt?
Testre szabhatja a meghívó folyamatra vonatkozó szinte mindent használatával a [B2B meghívó API-k](customize-invitation-api.md).

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>Vendégfelhasználók alaphelyzetbe állíthatja a multi-factor Authentication hitelesítési módszert?
Igen. Vendégfelhasználók alaphelyzetbe állíthatja a multi-factor authentication módszer ugyanúgy, ahogy a normál felhasználók hogyan használják.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Melyik szervezetben felelős a multi-factor authentication licencek?
A meghívó szervezetet a multi-factor Authentication hitelesítést hajt végre. A meghívó szervezetet ellenőrizze, hogy a szervezete rendelkezik-e elegendő licenccel a B2B-felhasználók számára a többtényezős hitelesítés használata esetén.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Mi történik, ha egy fiókpartner-szervezet már rendelkezik multi-factor authentication szolgáltatás beállítása? Is azt a multi-factor authentication megbízható, és nem a saját multi-factor authentication használata?
Ez a funkció egy későbbi kiadásban tervezünk, akkor választhatja ki kizárandó konkrét partnerek, a többtényezős hitelesítés (a meghívó szervezetet meg).

### <a name="how-can-i-use-delayed-invitations"></a>Hogyan használható a késleltetett felkérések?
Egy szervezet hozzáadása a B2B-együttműködés felhasználók, alkalmazások szükség szerint építse és majd a Meghívók küldése kíván. A B2B-együttműködés meghívó API segítségével testre szabhatja a regisztrációs munkafolyamat.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Készíthetek vendégfelhasználók látható az Exchange globális címlista?
Igen. Vendég objektumok nem látható a szervezet globális címlista (GAL) alapértelmezés szerint, de használhatja az Azure Active Directory PowerShell láthatóvá tegye őket. Lásd: [lehet Vendég objektumok a globális címlista látható?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#can-i-make-guest-objects-visible-in-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>Tehetők a vendégfelhasználó korlátozott rendszergazda?
Abszolút. További információkért lásd: [vendég felhasználók hozzáadása szerepkörökhöz](add-guest-to-role.md).

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Azure AD B2B együttműködés lehetővé teszi a B2B-felhasználók az Azure portal eléréséhez?
Hacsak egy felhasználó a korlátozott rendszergazdai szerepkör van hozzárendelve, B2B együttműködési felhasználókat az Azure Portalon való hozzáférés nem szükséges. Azonban a B2B együttműködés korlátozott rendszergazda szerepe hozzárendelt felhasználók hozzáférhessenek a portálhoz. Ezenkívül ha vendégfelhasználó, aki nincs hozzárendelve egyik rendszergazdai szerepkör hozzáfér a portálon, a felhasználó lehet érhessék el az egyes részeit a felhasználói élményt. A Vendég felhasználói szerep néhány engedélyt a címtárban.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Letilthatom az Azure Portalon a vendégfelhasználók számára a hozzáférést?
Igen! Ez a szabályzat konfigurálásakor kell arra, hogy elkerülje a tagjának és rendszergazdájának véletlenül letiltja a hozzáférést.
A vendégfelhasználó való hozzáférés letiltását a [az Azure portal](https://portal.azure.com), használja a feltételes hozzáférési szabályzat a Windows Azure klasszikus üzemi modell API-ban:
1. Módosítsa a **minden felhasználó** csoportot, hogy csak a tagok tartalmaz.
   ![Ha UserType nem egyenlő Vendég minden felhasználó csoport ábrázoló képernyőfelvétel](media/faq/modify-all-users-group.png)
2. Vendégfelhasználók tartalmazó dinamikus csoport létrehozása.
   ![Minden vendégfelhasználó új csoportot ábrázoló képernyőfelvétel](media/faq/group-with-guest-users.png)
3. Állítsa be feltételes hozzáférési szabályzat vendég felhasználók számára a portál hozzáférjen az alábbi videóban látható módon:
  
   > [!VIDEO https://channel9.msdn.com/Blogs/Azure/b2b-block-guest-user/Player] 

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>A multi-factor authentication és a fogyasztói e-mail-fiókokat támogatja az Azure AD B2B együttműködés?
Igen. Többtényezős hitelesítés és a fogyasztói e-mail-fiókokat az Azure AD B2B együttműködés támogatottak.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Új jelszó kérését az Azure AD B2B együttműködés felhasználók támogatja?
Ha egy felhasználó a kezdőkönyvtárba, az Azure AD-bérlővel is [a felhasználó jelszavának alaphelyzetbe állítása](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) az Azure Portalról. De nem közvetlenül állítható be új vendégfelhasználó, aki egy másik Azure AD directory és a külső identitásszolgáltató által felügyelt fiókkal jelentkezik be egy jelszót. Csak a meghívott felhasználónak vagy a felhasználó kezdőkönyvtárához a rendszergazda alaphelyzetbe állíthatja a jelszót. Íme néhány példa a jelszó-visszaállítás módja a vendégfelhasználók számára:
 
* Vendég felhasználók, akik a Microsoft-fiókkal (például guestuser@live.com) is alaphelyzetbe állíthatja saját jelszavait, önkiszolgáló jelszó-visszaállítási (SSPR) Microsoft-fiókjával. Lásd: [Microsoft-fiók jelszavának alaphelyzetbe állítása](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password).
* Jelentkezzen be Google-fiók vagy egy másik külső identitásszolgáltató vendég felhasználók is alaphelyzetbe állíthatja saját jelszavait, azok identitásszolgáltató SSPR metódus használatával. Ha például egy Google-fiókkal vendégfelhasználó guestuser@gmail.com is alaphelyzetbe állíthatja a jelszavát a következő témakör utasításait követve [módosítása vagy a jelszó alaphelyzetbe](https://support.google.com/accounts/answer/41078).
* Ha a szolgáltatásidentitás bérlője egy – igény (szerinti JIT) vagy "vírusos" bérlői (azaz egy különálló, nem felügyelt Azure-bérlőhöz), csak a meghívott felhasználónak alaphelyzetbe állíthatja a jelszavát. Egyes esetekben egy szervezet fog [átveszi a vírusos bérlő](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) , amely jönnek létre, ha az alkalmazottak saját munkahelyi e-mail-címek használatával szolgáltatások regisztrálhat. A szervezetnek szüksége van a vírusos bérlő felett, miután csak egy adott szervezet rendszergazdája alaphelyzetbe a jelszót, vagy SSPR engedélyezése. Ha szükséges, mint a meghívó szervezetet, a Vendég felhasználói fiókhoz eltávolítása a címtárból, és egy meghívó újraküldése.
* Ha a vendégfelhasználó saját címtárral az Azure AD-bérlővel, alaphelyzetbe állíthatja a jelszót. Például, előfordulhat, hogy létrehozott egy felhasználó vagy a felhasználó a helyszíni Active Directoryból szinkronizált és állítsa be a UserType Vendég. Mivel ezt a felhasználót a címtárban az előbbi, alaphelyzetbe állíthatja a jelszavát, az Azure Portalról.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>Nem a Microsoft Dynamics 365 online-támogatást nyújt az Azure AD B2B együttműködés?
Igen, a Dynamics 365 (online) támogatja az Azure AD B2B együttműködés. További információkért tekintse meg a Dynamics 365 cikket [meghívása a felhasználók az Azure AD B2B együttműködés](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration).

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Mi az, hogy egy újonnan létrehozott B2B együttműködés felhasználó kezdeti jelszavát élettartama?
Az Azure AD rendelkezik egy rögzített karakter, a jelszó erősségét és a fiók zárolása követelmények egyaránt érvényesek minden Azure ad felhőalapú felhasználói fiókok. Felhőalapú felhasználói fiókok olyan fiókok, nem összevont más identitásszolgáltatóval, például 
* Microsoft-fiók
* Facebook
* Active Directory összevonási szolgáltatások
* Egy másik felhőalapú bérlőre (a B2B-együttműködés)

Összevont fiókok esetében jelszóházirendet a házirendet, amely a helyszíni bérlős és a felhasználó Microsoft-fiók beállításainak alkalmazása függ.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Egy szervezet szüksége lehet a különböző környezeteket bérlői és vendég felhasználók az alkalmazásokba. A standard szintű útmutatást van? Az identitásszolgáltató jelenléte jogcím a megfelelő modellt?
Vendégfelhasználó használhatja bármely identitásszolgáltató hitelesítésére. További információkért lásd: [B2B együttműködés felhasználói tulajdonságok](user-properties.md). Használja a **UserType** tulajdonság határozza meg a felhasználói élmény. A **UserType** jogcím jelenleg nem szerepel a jogkivonatban. A felhasználó számára a címtár lekérdezésére, és a UserType beolvasásához, alkalmazások a Graph API-t kell használnia.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Hol található a B2B együttműködés közösségi megoldások megosztása és ötleteket szeretne elküldeni?
Folyamatosan figyelünk a B2B-együttműködés javítása érdekében a visszajelzés. Ossza meg a felhasználó forgatókönyvek, ajánlott eljárások és mi tetszik Önnek az Azure AD B2B együttműködés. Csatlakozzon a beszélgetéshez a [a Microsoft technikai Közösség](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b).
 
Azt is meghívhat, akkor küldje be az ötletek és a későbbi funkciókkal, szavazzon [B2B együttműködés ötleteket](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Is küldünk egy meghívást arra, hogy automatikusan beváltott, úgy, hogy a felhasználó csak "készen áll"? Vagy a felhasználó mindig rendelkezik, kattintással lépjen a beváltási URL-cím?
A felhasználói felület, a PowerShell-parancsfájlok használatával meghívhatja munkatársait, a a fiókpartner-szervezet többi felhasználója vagy API-k. Ezután elküldheti a vendégfelhasználó közvetlen hivatkozás egy megosztott alkalmazás. A legtöbb esetben már nem látható az e-mailes meghívót megnyitásához, majd kattintson a beváltási URL-cím szükséges. Lásd: [Azure Active Directory B2B együttműködés vendégmeghívás beváltása](redemption-experience.md).

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hogyan működik a B2B-együttműködés a Ha a meghívott partner összevonási segítségével adja hozzá a saját helyszíni hitelesítéssel?
Ha a partner van összevonva a helyszíni hitelesítési infrastruktúráját az Azure AD-bérlő, a helyszíni egyszeri bejelentkezés (SSO) automatikusan érhető el. Ha a partner Azure AD-bérlő nem rendelkezik, az Azure AD-fiók jön létre az új felhasználók számára. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>E úgy Gondoltuk, az Azure AD B2B nem fogadja el a gmail.com és az Outlook.com-os e-mail-címeket, és a B2C használatát az ilyen típusú fiókokat?
B2B és üzleti – fogyasztói (B2C) együttműködés szempontjából, amely az identitások támogatottak közötti különbségekről megszüntetjük. Az identitásnak nem választhat B2B használatával, vagy pedig a B2C jó oka. Az együttműködési lehetőség kiválasztásával kapcsolatban további információkért lásd: [összehasonlítása B2B-együttműködés és az Azure Active Directory B2C](compare-with-b2c.md).

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Milyen alkalmazásokat és szolgáltatásokat támogatják az Azure B2B vendégfelhasználók?
Minden Azure AD-val integrált alkalmazásokat Azure B2B vendégfelhasználókat is támogatja, de a vendégfelhasználók hitelesítést kell használniuk a bérlőként beállítani a végpont. Előfordulhat, hogy szükség [testre szabhatja a jogcímek](claims-mapping.md) az SAML-jogkivonatban kiadott, ha a Vendég felhasználó hitelesíti magát az alkalmazást. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Hogy kényszerítheti a többtényezős hitelesítés B2B vendégfelhasználók Ha partnerei nem rendelkeznek a multi-factor authentication?
Igen. További információkért lásd: [feltételes hozzáférés B2B-együttműködés felhasználók](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>A SharePoint megadhatja a külső felhasználók számára egy "engedélyezheti" vagy "Elutasítás" listában. Hogy ehhez az Azure-ban?
Igen. Az Azure AD B2B együttműködés támogatja engedélyezési listák, és letiltási listáját. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Milyen licencek szükségünk van az Azure AD B2B használata?
Milyen licencek, a szervezet kell használni az Azure AD B2B kapcsolatos információkért lásd: [licencelési útmutató Azure Active Directory B2B együttműködés](licensing-guidance.md).

### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)

