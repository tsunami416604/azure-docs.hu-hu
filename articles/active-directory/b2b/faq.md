---
title: B2B együttműködés – gyakori kérdések – Azure Active Directory | Microsoft dokumentumok
description: Válaszok az Azure Active Directory B2B-együttműködéssel kapcsolatos gyakori kérdésekre.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: reference
ms.date: 03/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92c3e0d77a26db406e24d6d2fa07e96349613634
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050822"
---
# <a name="azure-active-directory-b2b-collaboration-faqs"></a>Az Azure Active Directory B2B együttműködésének gyakori kérdések

Az Azure Active Directory (Azure AD) vállalkozások közötti (B2B) együttműködésével kapcsolatos gyakori kérdések (GYIK) rendszeres időközönként frissülnek, hogy új témaköröket tartalmazzanak.

   > [!IMPORTANT]
   > **2021. március 31-től a**Microsoft már nem támogatja a meghívók beváltását nem felügyelt Azure AD-fiókok és bérlők létrehozásával a B2B együttműködési forgatókönyvekhez. Az előkészítés során arra bátorítjuk ügyfeleinket, hogy válasszák az [e-mailes egyszeri jelkód-hitelesítést.](one-time-passcode.md) Örömmel fogadjuk a nyilvános előnézeti funkcióval kapcsolatos visszajelzéseit, és izgatottan várjuk, hogy még több együttműködési módot hozzon létre.

### <a name="can-we-customize-our-sign-in-page-so-its-more-intuitive-for-our-b2b-collaboration-guest-users"></a>Testreszabhatjuk a bejelentkezési oldalunkat, hogy intuitívabb legyen a B2B együttműködési vendégfelhasználók számára?
Teljesen! Tekintse meg [blogbejegyzésünket erről a funkcióról.](https://blogs.technet.microsoft.com/enterprisemobility/2017/04/07/improving-the-branding-logic-of-azure-ad-login-pages/) A szervezet bejelentkezési lapjának testreszabásáról a Vállalati [márkajelzés hozzáadása a bejelentkezéshez és a Hozzáférési panel lapjai](../fundamentals/customize-branding.md)című témakörben talál további információt.

### <a name="can-b2b-collaboration-users-access-sharepoint-online-and-onedrive"></a>A B2B-alapú együttműködési felhasználók hozzáférhetnek a SharePoint Online-hoz és a OneDrive-hoz?
Igen. A SharePoint Online-ban a személyek választójának használatával meglévő vendégfelhasználók keresésének lehetősége azonban alapértelmezés szerint ki van **kapcsolva.** A meglévő vendégfelhasználók keresésének bekapcsolásához állítsa a **ShowPeoplePickerSuggestionsForGuestUsers beállítást** **Be beállításra.** Ezt a beállítást bérlői vagy webhelycsoport szinten is bekapcsolhatja. Ezt a beállítást a Set-SPOTenant és a Set-SPOSite parancsmagokkal módosíthatja. Ezekkel a parancsmagokkal a tagok a címtárban lévő összes vendégfelhasználóközött kereshetnek. A bérlői hatókör módosításai nincsenek hatással a már kiépített SharePoint Online-webhelyekre.

### <a name="is-the-csv-upload-feature-still-supported"></a>Továbbra is támogatott a CSV-feltöltési funkció?
Igen. A .csv fájlfeltöltési funkció használatáról további információt ebben a [PowerShell-mintában](code-samples.md)talál.

### <a name="how-can-i-customize-my-invitation-emails"></a>Hogyan szabhatom testre a meghívó e-mailjeimet?
A [B2B meghívási API-k](customize-invitation-api.md)segítségével szinte mindent testreszabhat a meghívófolyamatról.

### <a name="can-guest-users-reset-their-multi-factor-authentication-method"></a>A vendégfelhasználók alaphelyzetbe állíthatják a többtényezős hitelesítési módszert?
Igen. A vendégfelhasználók ugyanúgy állíthatják alaphelyzetbe a többtényezős hitelesítési módszerüket, mint a normál felhasználók.

### <a name="which-organization-is-responsible-for-multi-factor-authentication-licenses"></a>Melyik szervezet felelős a többtényezős hitelesítési licencekért?
A meghívó szervezet többtényezős hitelesítést hajt végre. A meghívó szervezetnek meg kell győződnie arról, hogy a szervezet elegendő licenccel rendelkezik a többtényezős hitelesítést használó B2B-felhasználók számára.

### <a name="what-if-a-partner-organization-already-has-multi-factor-authentication-set-up-can-we-trust-their-multi-factor-authentication-and-not-use-our-own-multi-factor-authentication"></a>Mi a teendő, ha egy partnerszervezet már beállítotttöbbtényezős hitelesítést? Bízhatunk a többtényezős hitelesítésben, és nem használhatjuk a saját többtényezős hitelesítésünket?
Ez a szolgáltatás jelenleg nem támogatott. Ha a szervezet erőforrásaihoz való hozzáférés többtényezős hitelesítést igényel, a partnerszervezetnek regisztrálnia kell a többtényezős hitelesítéshez a (meghívó) szervezetben.

### <a name="how-can-i-use-delayed-invitations"></a>Hogyan használhatom a késleltetett meghívókat?
Előfordulhat, hogy egy szervezet b2B együttműködési felhasználókat szeretne hozzáadni, szükség szerint kiépíteni őket az alkalmazásokba, majd meghívókat küldeni. A B2B együttműködési meghívásAPI segítségével testreszabhatja a bevezetési munkafolyamatot.

### <a name="can-i-make-guest-users-visible-in-the-exchange-global-address-list"></a>Láthatóvá tehetem a vendégfelhasználókat az Exchange globális címlistájában?
Igen. A vendégobjektumok alapértelmezés szerint nem láthatók a szervezet globális címlistájában (GAL), de az Azure Active Directory PowerShell használatával láthatóvá teheti őket. Lásd: [Láthatóvá tehetem a vendégobjektumokat a globális címlistában?](https://docs.microsoft.com/office365/admin/create-groups/manage-guest-access-in-groups#add-guests-to-the-global-address-list)

### <a name="can-i-make-a-guest-user-a-limited-administrator"></a>A vendégfelhasználót korlátozott rendszergazdaként tudom eltitenni?
Abszolút. További információt a [Vendégfelhasználók hozzáadása szerepkörhöz című témakörben talál.](add-guest-to-role.md)

### <a name="does-azure-ad-b2b-collaboration-allow-b2b-users-to-access-the-azure-portal"></a>Az Azure AD B2B együttműködés lehetővé teszi a B2B-felhasználók számára az Azure Portal elérését?
Ha csak egy felhasználó van rendelve a korlátozott rendszergazda, B2B együttműködési felhasználók nem lesz szükségük az Azure Portalhoz való hozzáférést. A korlátozott rendszergazdai szerepkörrel rendelkező B2B együttműködési felhasználók azonban hozzáférhetnek a portálhoz. Továbbá, ha egy vendégfelhasználó, aki nem rendelkezik ezen rendszergazdai szerepkörök egyikével, hozzáfér a portálhoz, a felhasználó hozzáférhet a felhasználói élmény bizonyos részeihez. A vendégfelhasználói szerepkör rendelkezik bizonyos engedélyekkel a címtárban.

### <a name="can-i-block-access-to-the-azure-portal-for-guest-users"></a>Letilthatom a hozzáférést az Azure Portalhoz a vendégfelhasználók számára?

Igen! Feltételes hozzáférési szabályzatot hozhat létre, amely megakadályozza, hogy az összes vendég- és külső felhasználó hozzáférjen az Azure Portalhoz. A házirend konfigurálásakor ügyeljen arra, hogy véletlenül ne tiltsa le a tagok és a rendszergazdák hozzáférését.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) biztonsági rendszergazdaként vagy feltételes hozzáférés-rendszergazdaként.
2. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget. 
3. A **Kezelés csoportban**válassza a **Biztonság**lehetőséget.
4. A **Védelem csoportban**válassza a **Feltételes hozzáférés**lehetőséget. Válassza az **Új házirend lehetőséget.**
5. Az **Új** lap **Név** mezőjébe írja be a házirend nevét (például "A vendégek hozzáférésének letiltása").
6. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
7. A **Belefoglalás** lapon válassza **a Felhasználók és csoportok kiválasztása**lehetőséget, majd válassza az Összes **vendég- és külső felhasználó (Előnézet) lehetőséget.**
9. Válassza a **Done** (Kész) lehetőséget.
10. Az **Új** lap **Hozzárendelések** szakaszában válassza a **Felhőalapú alkalmazások vagy műveletek**lehetőséget.
11. A **Felhőalapú alkalmazások vagy műveletek** lapon válassza az **Alkalmazások kiválasztása**lehetőséget, majd a **Kijelölés**lehetőséget.
12. A **Kiválasztás** lapon válassza a **Microsoft Azure-felügyelet** elemet, majd a **Kiválaszt** lehetőséget.
13. A **Felhőalapú alkalmazások vagy műveletek** lapon válassza a **Kész**lehetőséget.

### <a name="does-azure-ad-b2b-collaboration-support-multi-factor-authentication-and-consumer-email-accounts"></a>Támogatja az Azure AD B2B együttműködésa a többtényezős hitelesítést és a fogyasztói e-mail fiókokat?
Igen. A többtényezős hitelesítés és a fogyasztói e-mail fiókok egyaránt támogatottak az Azure AD B2B együttműködéshez.

### <a name="do-you-support-password-reset-for-azure-ad-b2b-collaboration-users"></a>Támogatja a jelszó-visszaállítást az Azure AD B2B együttműködési felhasználók számára?
Ha az Azure AD-bérlő egy felhasználó kezdőkönyvtára, [alaphelyzetbe állíthatja a felhasználó jelszavát](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-reset-password-azure-portal) az Azure Portalon. De nem lehet közvetlenül alaphelyzetbe állítani egy vendégfelhasználó jelszavát, aki egy másik Azure AD-címtár vagy külső identitásszolgáltató által kezelt fiókkal jelentkezik be. Csak a vendégfelhasználó vagy a felhasználó kezdőkönyvtárának rendszergazdája állíthatja alaphelyzetbe a jelszót. Íme néhány példa arra, hogyan működik a jelszó-visszaállítás a vendégfelhasználók számára:
 
* Azok a vendégfelhasználók, akik Microsoft-fiókkal jelentkeznek be (például) guestuser@live.coma Microsoft-fiók önkiszolgáló jelszó-visszaállításával (SSPR) visszaállíthatják saját jelszavukat. Olvassa [el A Microsoft-fiók jelszavának alaphelyzetbe állítása című témakört.](https://support.microsoft.com/help/4026971/microsoft-account-how-to-reset-your-password)
* Azok a vendégfelhasználók, akik Google-fiókkal vagy más külső identitásszolgáltatóval jelentkeznek be, visszaállíthatják saját jelszavukat az identitásszolgáltatójuk SSPR-módszerével. A Google-fiókkal guestuser@gmail.com rendelkező vendégfelhasználó például a Jelszó [módosítása vagy alaphelyzetbe állítása](https://support.google.com/accounts/answer/41078)című részben található utasításokat követve állíthatja vissza jelszavát.
* Ha az identitás-bérlő egy just-in-time (JIT) vagy "vírusos" bérlő (azaz egy különálló, nem felügyelt Azure-bérlő), csak a vendég felhasználó alaphelyzetbe állíthatja a jelszavát. Előfordulhat, hogy egy szervezet [átveszi a vírusos bérlők felügyeletét,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-admin-takeover) amelyek akkor jönnek létre, amikor az alkalmazottak a munkahelyi e-mail-címükkel regisztrálnak a szolgáltatásokra. Miután a szervezet átveszi a vírusos bérlőt, csak a szervezet rendszergazdája állíthatja vissza a felhasználó jelszavát, vagy engedélyezheti az SSPR-t. Szükség esetén meghívó szervezetként eltávolíthatja a vendégfelhasználói fiókot a címtárból, és újra elküldheti a meghívót.

* Ha a vendégfelhasználó kezdőkönyvtára az Azure AD-bérlő, alaphelyzetbe állíthatja a felhasználó jelszavát. Előfordulhat például, hogy létrehozott egy felhasználót, vagy szinkronizált egy felhasználót a helyszíni Active Directoryból, és a UserType beállítását vendégre állította. Mivel ez a felhasználó a címtárban található, alaphelyzetbe állíthatja a jelszavát az Azure Portalon.

### <a name="does-microsoft-dynamics-365-provide-online-support-for-azure-ad-b2b-collaboration"></a>A Microsoft Dynamics 365 online támogatást nyújt az Azure AD B2B együttműködéshez?
Igen, a Dynamics 365 (online) támogatja az Azure AD B2B együttműködést. További információt a Dynamics 365 [cikkBen A felhasználók meghívása az Azure AD B2B együttműködéssel](https://docs.microsoft.com/dynamics365/customer-engagement/admin/invite-users-azure-active-directory-b2b-collaboration)című témakörben talál.

### <a name="what-is-the-lifetime-of-an-initial-password-for-a-newly-created-b2b-collaboration-user"></a>Mi az újonnan létrehozott B2B együttműködési felhasználó kezdeti jelszavának élettartama?
Az Azure AD egy rögzített karakterkészlet, jelszó erőssége, és a fiók zárolási követelmények, amelyek egyaránt vonatkoznak az összes Azure AD felhőbeli felhasználói fiókok. A felhőalapú felhasználói fiókok olyan fiókok, amelyek nincsenek összeegyezve egy másik identitásszolgáltatóval, például 
* Microsoft-fiók
* Facebook
* Active Directory összevonási szolgáltatások
* Egy másik felhőbérlő (B2B együttműködéshez)

Összevont fiókok esetén a jelszóházirend a helyszíni bérleti szerződésben alkalmazott házirendtől és a felhasználó Microsoft-fiókbeállításaitól függ.

### <a name="an-organization-might-want-to-have-different-experiences-in-their-applications-for-tenant-users-and-guest-users-is-there-standard-guidance-for-this-is-the-presence-of-the-identity-provider-claim-the-correct-model-to-use"></a>Előfordulhat, hogy egy szervezet szeretné, hogy a különböző felhasználói élmények az alkalmazások a bérlői felhasználók és a vendég felhasználók számára. Van-e szabványos útmutatást erre? Az identitásszolgáltató jelenléte a megfelelő modell használata?
A vendégfelhasználó bármilyen identitásszolgáltatót használhat a hitelesítéshez. További információ: [Tulajdonságok a B2B együttműködési felhasználó](user-properties.md). A **UserType** tulajdonsággal határozza meg a felhasználói élményt. A **UserType** jogcím jelenleg nem szerepel a jogkivonatban. Az alkalmazásoknak a Microsoft Graph API-t kell használniuk a címtár lekérdezéséhez a felhasználó számára, és a UserType bek és a UserType bek és a UserType lekérni.

### <a name="where-can-i-find-a-b2b-collaboration-community-to-share-solutions-and-to-submit-ideas"></a>Hol találok b2b együttműködési közösséget a megoldások megosztására és az ötletek benyújtására?
Folyamatosan hallgatjuk visszajelzéseit, hogy javítsuk a B2B együttműködést. Kérjük, ossza meg a felhasználói forgatókönyvek, gyakorlati tanácsok, és mit szeret az Azure AD B2B együttműködés. Csatlakozzon a [microsoftos technikai közösséghez.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B/bd-p/AzureAD_B2b)
 
Arra is meghívjuk Önt, hogy küldje el ötleteit, és szavazzon a jövőbeli funkciók [B2B Collaboration Ötletek](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-B2B-Ideas/idb-p/AzureAD_B2B_Ideas).

### <a name="can-we-send-an-invitation-that-is-automatically-redeemed-so-that-the-user-is-just-ready-to-go-or-does-the-user-always-have-to-click-through-to-the-redemption-url"></a>Küldhetünk egy meghívót, amely automatikusan beváltott, hogy a felhasználó csak "készen áll"? Vagy a felhasználónak mindig át kell kattintania a beváltási URL-re?
A partnerszervezet más felhasználóit is meghívhat a felhasználói felület, a PowerShell-parancsfájlok vagy az API-k használatával. Ezután közvetlen hivatkozást küldhet a vendégfelhasználónak egy megosztott alkalmazásra. A legtöbb esetben már nincs szükség az e-mail meghívó megnyitására és a beváltási URL-re való kattintásra. Lásd: [Azure Active Directory B2B együttműködési meghívók beváltása.](redemption-experience.md)

### <a name="how-does-b2b-collaboration-work-when-the-invited-partner-is-using-federation-to-add-their-own-on-premises-authentication"></a>Hogyan működik a B2B-együttműködés, ha a meghívott partner összevonással adja hozzá saját helyszíni hitelesítését?
Ha a partner rendelkezik egy Azure AD-bérlő, amely a helyszíni hitelesítési infrastruktúra, a helyszíni egyszeri bejelentkezés (SSO) automatikusan megvalósul. Ha a partner nem rendelkezik Egy Azure AD-bérlő, egy Azure AD-fiók jön létre az új felhasználók számára. 

### <a name="i-thought-azure-ad-b2b-didnt-accept-gmailcom-and-outlookcom-email-addresses-and-that-b2c-was-used-for-those-kinds-of-accounts"></a>Azt hittem, az Azure AD B2B nem fogadja el gmail.com és outlook.com e-mail címeket, és hogy a B2C használták az ilyen típusú számlák?
Megszüntetjük a B2B és a vállalkozások közötti (B2C) együttműködés közötti különbségeket, amelyek tekintetében az identitások támogatottak. A használt identitás nem jó ok választani a B2B vagy a B2C használata között. Az együttműködési beállítás kiválasztásáról a [B2B együttműködés és a B2C összehasonlítása az Azure Active Directoryban című témakörben talál.](compare-with-b2c.md)

### <a name="what-applications-and-services-support-azure-b2b-guest-users"></a>Milyen alkalmazások és szolgáltatások támogatják az Azure B2B vendégfelhasználókat?
Minden Azure AD-integrált alkalmazás támogatja az Azure B2B vendégfelhasználók, de kell használni a végpont bérlőként beállított a vendégfelhasználók hitelesítéséhez. Előfordulhat, hogy [testre kell szabnia az](claims-mapping.md) SAML-jogkivonatban lévő jogcímeket is, amelyek akkor jelennek meg, amikor egy vendégfelhasználó hitelesíti magát az alkalmazásban. 

### <a name="can-we-force-multi-factor-authentication-for-b2b-guest-users-if-our-partners-dont-have-multi-factor-authentication"></a>Kényszeríthetjük a többtényezős hitelesítést a B2B vendégfelhasználók számára, ha partnereink nem rendelkeznek többtényezős hitelesítéssel?
Igen. További információ: [Conditional Access for B2B collaboration users](conditional-access.md).

### <a name="in-sharepoint-you-can-define-an-allow-or-deny-list-for-external-users-can-we-do-this-in-azure"></a>A SharePointban külső felhasználók számára definiálhat "engedélyezés" vagy "megtagadás" listát. Meg tudjuk csinálni ezt az Azure-ban?
Igen. Az Azure AD B2B együttműködés támogatja az engedélyezési listák és a megtagadási listák. 

### <a name="what-licenses-do-we-need-to-use-azure-ad-b2b"></a>Milyen licenceket kell használnunk az Azure AD B2B használatához?
Ha többet szeretne tudni arról, hogy a szervezetnek milyen licenceket kell használnia az Azure AD B2B-t, olvassa el az [Azure Active Directory B2B együttműködési licencelési útmutatóját.](licensing-guidance.md)

### <a name="next-steps"></a>További lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)

