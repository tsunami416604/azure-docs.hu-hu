---
title: Gyakori kérdések az Azure Active Directory B2C-vel kapcsolatban
description: Válaszok az Azure Active Directory B2C-vel kapcsolatos gyakori kérdésekre.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/14/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 40285c811cd6f407c20c40bf3a90ec5b779a9c18
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264400"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: gyakori kérdések (GYAKORI KÉRDÉSEK)

Ez az oldal választ ad az Azure Active Directory B2C (Azure AD B2C) kapcsolatos gyakori kérdésekre. Nézz vissza a frissítéseket.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Miért nem tudom elérni az Azure AD B2C bővítményt az Azure Portalon?

Két gyakori oka annak, hogy az Azure AD-bővítmény nem működik az Ön számára. Az Azure AD B2C megköveteli, hogy a címtárban lévő felhasználói szerepkör globális rendszergazda legyen. Kérjük, forduljon a rendszergazdához, ha úgy gondolja, hogy hozzáféréssel kell rendelkeznie. Ha globális rendszergazdai jogosultságokkal rendelkezik, győződjön meg arról, hogy egy Azure AD B2C könyvtárban van, és nem egy Azure Active Directory-címtárban. Az [Azure AD B2C-bérlő létrehozásához](tutorial-create-tenant.md)vonatkozó utasításokat láthatja.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Használhatom az Azure AD B2C funkciókat a meglévő, alkalmazott-alapú Azure AD-bérlőben?

Az Azure AD és az Azure AD B2C különálló termékajánlatok, és nem létezhetnek együtt ugyanabban a bérlőben. Az Azure AD-bérlő egy szervezetet jelöl. Az Azure AD B2C-bérlő a függő entitásalkalmazásokhoz használandó identitások gyűjteményét jelöli. Az **Azure AD B2C > identity-szolgáltatók** alatt vagy egyéni szabályzatokkal új **OpenID Connect-szolgáltató** hozzáadásával az Azure AD B2C összetudja egyeztetni az Azure AD-t, lehetővé téve a szervezet alkalmazottainak hitelesítését.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Használhatom az Azure AD B2C-t közösségi bejelentkezéshez (Facebook és Google+) az Office 365-be?

Az Azure AD B2C nem használható a Microsoft Office 365 felhasználóinak hitelesítésére. Az Azure AD a Microsoft megoldása az alkalmazottak SaaS-alkalmazásokhoz való hozzáférésének kezelésére, és rendelkezik az erre a célra tervezett funkciókkal, például a licenceléssel és a feltételes hozzáféréssel. Az Azure AD B2C identitás- és hozzáférés-kezelési platformot biztosít webes és mobilalkalmazások létrehozásához. Ha az Azure AD B2C úgy van konfigurálva, hogy egy Azure AD-bérlőhöz egyenlítsen, az Azure AD-bérlő kezeli az alkalmazottak hozzáférését az Azure AD B2C-re támaszkodó alkalmazásokhoz.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Mik azok a helyi fiókok az Azure AD B2C-ben? Miben különböznek a munkahelyi vagy iskolai fiókoktól az Azure AD-ben?

Egy Azure AD-bérlőben a bérlőhöz tartozó felhasználók az űrlap `<xyz>@<tenant domain>`e-mail-címével. Az `<tenant domain>` egyik ellenőrzött tartományok a bérlőben vagy `<...>.onmicrosoft.com` a kezdeti tartományban. Az ilyen típusú fiók munkahelyi vagy iskolai fiók.

Az Azure AD B2C-bérlőknél a legtöbb alkalmazás azt szeretné, hogy a joe@comcast.net bob@gmail.comfelhasználó sarah@contoso.comtetszőleges jim@live.come-mail címmel jelentkezzen be (például , , vagy ). Az ilyen típusú fiók egy helyi fiók. Tetszőleges felhasználói neveket is támogatunk helyi fiókként (például joe, bob, sarah vagy jim). A két helyi fióktípus közül választhat, amikor az Azure AD B2C identitásszolgáltatóit konfigurálja az Azure Portalon. Az Azure AD B2C-bérlőben válassza **az Identitásszolgáltatók**lehetőséget, válassza a **Helyi fiók**lehetőséget, majd a **Felhasználónév**lehetőséget.

Az alkalmazások felhasználói fiókjai létrehozhatók egy regisztrációs felhasználói folyamat, a regisztráció vagy a bejelentkezés felhasználói folyamat, a Microsoft Graph API vagy az Azure Portalon keresztül.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Mely társadalmi identitásszolgáltatókat támogatja most? Melyiket tervezi támogatni a jövőben?

Jelenleg számos közösségi identitásszolgáltatót támogatunk, köztük az Amazont, a Facebookot, a GitHubot (előzetes verzió), a Google-t, a LinkedIn-t, a Microsoft-fiókot (MSA), a QQ-t (preview), a Twittert, a WeChatet (előnézet) és a Weibo-t (előzetes verzió). A többi népszerű közösségi identitásszolgáltató támogatásának hozzáadását az ügyfelek igényei alapján értékeljük.

Az Azure AD B2C egyéni [szabályzatokat](custom-policy-overview.md)is támogat. Az egyéni házirendek lehetővé teszik, hogy saját szabályzatot hozzon létre minden olyan identitásszolgáltató számára, amely támogatja az [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) vagy az SAML-t. Ismerkedés az egyéni szabályzatokkal az [egyéni szabályzatkezdőcsomaggal.](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Konfigurálhatom a hatóköröket úgy, hogy több információt gyűjtsenek a különböző közösségi identitásszolgáltatók fogyasztóiról?

Nem. A támogatott közösségi identitásszolgáltatók alapértelmezett hatókörei a következők:

* Facebook: e-mail
* Google+: e-mail
* Microsoft-fiók: openid e-mail profil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Az azure-beli AD B2C-vel való működéséhez az alkalmazásomat az Azure AD B2C-vel kell futtatni?

Nem, az alkalmazást bárhol (a felhőben vagy a helyszínen) üzemeltetheti. Az Azure AD B2C-vel való interakcióhoz mindössze annyit kell tennie, hogy http-kérelmeket küldjön és fogadjon nyilvánosan elérhető végpontokon.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Több Azure AD B2C-bérlővel rendelkezem. Hogyan kezelhetem őket az Azure Portalon?

Az "Azure AD B2C" megnyitása előtt az Azure Portal bal oldali menüjében át kell váltania a kezelni kívánt könyvtárba. Váltson könyvtárakat az Azure Portal jobb felső részén található identitásra kattintva, majd válasszon egy könyvtárat a megjelenő legördülő menüben.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hogyan szabhatom testre az Azure AD B2C által küldött ellenőrző e-maileket (a tartalmat és a "From:" mezőt).

A vállalati [márkajelzési funkcióval](../active-directory/fundamentals/customize-branding.md) testreszabhatja az ellenőrző e-mailek tartalmát. Pontosabban, ez a két elem az e-mail testreszabható:

* **Banner embléma**: A jobb alsó sarokban látható.
* **Háttérszín**: Felül látható.

    ![Képernyőkép egy személyre szabott ellenőrző e-mailről](./media/faq/company-branded-verification-email.png)

Az e-mail aláírás tartalmazza az Azure AD B2C bérlő nevét, amely az Azure AD B2C-bérlő első létrehozásakor megadott. A nevet az alábbi utasítások segítségével módosíthatja:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) globális rendszergazdaként.
1. Nyissa meg az **Azure Active Directory** panelt.
1. Kattintson a **Tulajdonságok** fülre.
1. Módosítsa a **Név** mezőt.
1. Kattintson az oldal tetején lévő **Mentés** elemre.

Jelenleg nincs mód az e-mail "From:" mezőjének módosítására.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hogyan telepíthetem át a meglévő felhasználóneveket, jelszavakat és profilokat az adatbázisból az Azure AD B2C-be?

A Microsoft Graph API segítségével írhatja az áttelepítési eszközt. A részleteket a [Felhasználói áttelepítési útmutatóban](user-migration.md) találja.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Milyen jelszó felhasználói folyamat ot használnak a helyi fiókok az Azure AD B2C?

Az Azure AD B2C jelszó felhasználói folyamat a helyi fiókok alapja az Azure AD szabályzata. Az Azure AD B2C regisztrációs, bejelentkezési vagy bejelentkezési és jelszó-alaphelyzetbe állítási felhasználói folyamatai az "erős" jelszóerősséget használják, és nem járnak le jelszavakat. További információt az [Azure Active Directory jelszóházirendjei és korlátozásai című témakörben talál.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

A fiókzárolásokról és -jelszavakról az [Erőforrásokkal és adatokkal kapcsolatos fenyegetések kezelése az Azure Active Directory B2C-ben](threat-management.md)című témakörben talál.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Használhatom az Azure AD Connectet a helyszíni Active Directoryban tárolt fogyasztói identitások átáttelepítéséhez az Azure AD B2C-be?

Nem, az Azure AD Connect nem az Azure AD B2C-vel való együttműködésre készült. Fontolja meg a [Microsoft Graph API](manage-user-accounts-graph-api.md) használatát a felhasználók áttelepítéséhez. A részleteket a [Felhasználói áttelepítési útmutatóban](user-migration.md) találja.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Megnyithatja az alkalmazásom az Azure AD B2C-lapokat egy iFrame-en belül?

Nem, biztonsági okokból az Azure AD B2C-lapok nem nyithatók meg egy iFrame-en belül. Szolgáltatásunk kommunikál a böngészővel, hogy megtiltsa az iFrame-eket. A biztonsági közösség általában és az OAUTH2 specifikáció javallata a kattintás-illetékelés veszélye miatt azt javasolja, hogy az iFrame-eket ne használják az identitásélményekhez.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Működik az Azure AD B2C olyan CRM-rendszerekkel, mint a Microsoft Dynamics?

A Microsoft Dynamics 365 Portállal való integráció elérhető. Lásd: [A Dynamics 365 Portal konfigurálása az Azure AD B2C hitelesítéshez.](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c)

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Működik az Azure AD B2C a helyszíni 2016-os vagy korábbi SharePoint-tal?

Az Azure AD B2C nem a SharePoint külső partnermegosztási forgatókönyvéhez való; lásd: [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) helyett.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Az Azure AD B2C vagy a B2B használatával kezeljem a külső identitásokat?

[A B2B-együttműködés és a B2C összehasonlítása az Azure AD-ben](../active-directory/b2b/compare-with-b2c.md) című, a megfelelő funkciók külső identitásforgatókönyvekre való alkalmazásáról olvashat bővebben.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Milyen jelentéskészítési és naplózási funkciókat biztosít az Azure AD B2C? Ugyanazok, mint az Azure AD Premium?

Nem, az Azure AD B2C nem támogatja ugyanazokat a jelentéseket, mint az Azure AD Premium. Azonban sok közös vonások:

* **A bejelentkezési jelentések** minden egyes bejelentkezési rekordról biztosítanak, csökkentett részletekkel.
* **A naplózási jelentések** mind a rendszergazdai tevékenységet, mind az alkalmazástevékenységet tartalmazzák.
* **A használati jelentések** tartalmazzák a felhasználók számát, a bejelentkezések számát és az MFA mennyiségét.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>Honosíthatom az Azure AD B2C által kiszolgált oldalak felhasználói felületét? Milyen nyelvek támogatottak?

Igen, lásd: [nyelvi testreszabás](user-flow-language-customization.md). 36 nyelv fordítását biztosítjuk, és bármilyen karakterláncot felülírhat az igényeinek megfelelően.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-contosob2clogincom-to-logincontosocom"></a>Használhatom a saját URL-címemet az Azure AD B2C által kiszolgált regisztrációs és bejelentkezési oldalaimon? Módosíthatom például az URL-címet contoso.b2clogin.com-ról login.contoso.com?

Jelenleg nincs. Ez a funkció szerepel az ütemtervünkben. A tartomány ellenőrzése az Azure Portal **Tartományok** lapján nem éri el ezt a célt. Azonban a b2clogin.com, kínálunk egy [semleges legfelső szintű domain](b2clogin.md), és így a külső megjelenés lehet végrehajtani említése nélkül a Microsoft.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hogyan törölhetem az Azure AD B2C-bérlőmet?

Kövesse az alábbi lépéseket az Azure AD B2C-bérlő törléséhez.

Használhatja az aktuális **alkalmazások** vagy az új, egységes **alkalmazásregisztrációk (előzetes verzió)** használatát. [További információ az új felületről](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Alkalmazások](#tab/applications/)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) *előfizetési rendszergazdaként.* Használja ugyanazt a munkahelyi vagy iskolai fiókot, vagy ugyanazt a Microsoft-fiókot, amelyet az Azure-ra való regisztrációhoz használt.
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Törölje az összes **felhasználói folyamatok (szabályzatok)** az Azure AD B2C bérlőben.
1. Törölje az Azure AD B2C-bérlőben regisztrált **összes alkalmazást.**
1. Válassza az **Azure Active Directory** a bal oldali menüben.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. Válassza ki egymás után az egyes felhasználókat (zárja ki azt az *Előfizetés-rendszergazda* felhasználót, akire éppen be van jelentkezve). Válassza a Lap alján a **Törlés** lehetőséget, és amikor a rendszer kéri, válassza az **IGEN** lehetőséget.
1. A **Kezelés csoportban**válassza **az Alkalmazásregisztrációk** (vagy **az Alkalmazásregisztrációk (Örökölt) lehetőséget).**
1. Az **összes alkalmazás megtekintése** jelölőnégyzet bejelölése
1. Jelölje ki a **b2c-extensions-app**nevű alkalmazást, válassza **a Törlés**lehetőséget, majd a rákérdezéskor válassza az **Igen** lehetőséget.
1. A **Kezelés csoportban**válassza a **Felhasználói beállítások lehetőséget.**
1. Ha van ilyen, a **LinkedIn-fiókkapcsolatok**csoportban válassza a **Nem**lehetőséget, majd a **Mentés**lehetőséget.
1. A **Kezelés csoportban**válassza a **Tulajdonságok lehetőséget.**
1. Az **Azure-erőforrásokhoz való hozzáférés kezelése** területen válassza az **Igen**, majd a **Mentés** lehetőséget.
1. Jelentkezzen ki az Azure Portalon, majd jelentkezzen be újra a hozzáférés frissítéséhez.
1. Válassza az **Azure Active Directory** a bal oldali menüben.
1. Az **Áttekintés** lapon válassza a **Könyvtár törlése**lehetőséget. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

#### <a name="app-registrations-preview"></a>[Alkalmazásregisztrációk (előzetes verzió)](#tab/app-reg-preview/)

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) *előfizetési rendszergazdaként.* Használja ugyanazt a munkahelyi vagy iskolai fiókot, vagy ugyanazt a Microsoft-fiókot, amelyet az Azure-ra való regisztrációhoz használt.
1. Válassza ki a **Könyvtár + előfizetés** szűrőa felső menüben, majd válassza ki az Azure AD B2C bérlőt tartalmazó könyvtárat.
1. A bal oldali menüben válassza az **Azure AD B2C**lehetőséget. Vagy válassza a **Minden szolgáltatás** lehetőséget, és keresse meg az **Azure AD B2C elemet.**
1. Törölje az összes **felhasználói folyamatot (szabályzatot)** az Azure AD B2C-bérlőben.
1. Válassza **az Alkalmazásregisztrációk (Előnézet)** lehetőséget, majd a **Minden alkalmazás** lapot.
1. Törölje az összes regisztrált alkalmazást.
1. Törölje a **b2c-extensions-app**.
1. A **Kezelés** alatt válassza a **Felhasználókat**.
1. Válassza ki egymás után az egyes felhasználókat (zárja ki azt az *Előfizetés-rendszergazda* felhasználót, akire éppen be van jelentkezve). Válassza a Lap alján a **Törlés** lehetőséget, és a rákérdezéskor válassza az **Igen** lehetőséget.
1. Válassza az **Azure Active Directory** a bal oldali menüben.
1. A **Kezelés csoportban**válassza a **Felhasználói beállítások lehetőséget.**
1. Ha van ilyen, a **LinkedIn-fiókkapcsolatok**csoportban válassza a **Nem**lehetőséget, majd a **Mentés**lehetőséget.
1. A **Kezelés csoportban**válassza a **Tulajdonságok lehetőséget.**
1. Az **Azure-erőforrásokhoz való hozzáférés kezelése** területen válassza az **Igen**, majd a **Mentés** lehetőséget.
1. Jelentkezzen ki az Azure Portalon, majd jelentkezzen be újra a hozzáférés frissítéséhez.
1. Válassza az **Azure Active Directory** a bal oldali menüben.
1. Az **Áttekintés** lapon válassza a **Könyvtár törlése**lehetőséget. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

* * *

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Beszerezhetem az Azure AD B2C-t az Enterprise Mobility Suite részeként?

Nem, az Azure AD B2C egy használatra szóló Azure-szolgáltatás, és nem része az Enterprise Mobility Suite szolgáltatásnak.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hogyan jelenthetem az Azure AD B2C-vel kapcsolatos problémákat?

Lásd: [Az Azure Active Directory B2C fájltámogatási kérelmei.](support-options.md)
