---
title: Gyakori kérdések az Azure Active Directory B2C |} A Microsoft Docs
description: Gyakori kérdések (GYIK) Azure Active Directory B2C-t.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: bccbf5462668913f6f3025e8d2274c115d49457f
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727239"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Az Azure AD B2C: Gyakori kérdések (GYIK) 
Ez az oldal az Azure Active Directory (Azure AD) B2C-vel kapcsolatos gyakori kérdésekre ad választ. Tartsa vissza a frissítések keresése.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>Miért nem fér hozzá az Azure AD B2C-bővítmény az Azure Portalon?
Miért érdemes az Azure AD-bővítmény nem működik az Ön számára a két gyakori oka van.  Az Azure AD B2C a a címtár globális rendszergazdai felhasználói szerepkör szükséges.  Ha úgy véli, hogy hozzáférhet, forduljon a rendszergazdához.  Ha globális rendszergazdai jogosultságokkal rendelkezik, győződjön meg arról, hogy az Azure AD B2C-címtár és a egy Azure Active Directory címtár nem áll.  Láthatja, hogy útmutatást [létrehozása az Azure AD B2C-bérlő](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Használható az Azure AD B2C-funkciók a meglévő, az alkalmazott-alapú Azure AD-bérlőm?
Az Azure AD és az Azure AD B2C-t külön termékajánlatokat, és nem szerepelhet egyszerre ugyanabban a bérlőben.  Az Azure AD-bérlő szervezet jelöli.  Azure AD B2C-bérlő egy függő entitás alkalmazásai a használni kívánt identitások gyűjteményét képviseli.  Egyéni szabályzatok (nyilvános előzetes verzióban elérhető) az Azure AD B2C-vel az Azure AD lehetővé teszi az alkalmazottak hitelesítését egy szervezet is összevonható.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Használható az Azure AD B2C-vel (Facebook és a Google +) közösségi bejelentkezés biztosítható az Office 365-be?
Az Azure AD B2C felhasználók hitelesítése az Office 365 a Microsoft nem használható.  Azure ad-ben az alkalmazottak hozzáférése a SaaS-alkalmazások kezelése a Microsoft-megoldás, és erre a célra például a licencelési és a feltételes hozzáférési funkciók rendelkezik.  Az Azure AD B2C-identitás- és hozzáférés-kezelési platformot biztosít a webes és mobil alkalmazások kialakításához.  Ha Azure AD B2C-vel az Azure AD-bérlő összevonásához van konfigurálva, az Azure AD-bérlő kezeli az alkalmazottak hozzáférését a Azure AD B2C-t használó alkalmazások.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Mik azok a helyi felhasználói fiókok Azure AD B2C-ben? Miben különböznek, a munkahelyi vagy iskolai fiókok Azure AD-ben?
Az Azure AD-bérlő, a bérlőhöz tartozó felhasználók jelentkezzen be egy e-mail címet az űrlap `<xyz>@<tenant domain>`.  A `<tenant domain>` egyik ellenőrzött tartománynak a bérlő vagy az eredeti `<...>.onmicrosoft.com` tartományhoz. Ez a fiók típus egy munkahelyi vagy iskolai fiókjával.

Az Azure AD B2C-bérlő, a legtöbb alkalmazás a felhasználót, hogy jelentkezzen be bármilyen tetszőleges e-mail-címmel szeretne (például joe@comcast.net, bob@gmail.com, sarah@contoso.com, vagy jim@live.com). Ez a fiók típus egy helyi fiókot.  Helyi fiókok (például joe, bob, sarah vagy jim) tetszőleges felhasználói neveket is támogatja. Kiválaszthatja az említett két helyi fiók az Azure Portalon az Azure AD B2C Identitásszolgáltatók konfigurálása során. Kattintson az Azure AD B2C-bérlőben **Identitásszolgáltatók** majd **felhasználónév** a helyi fiókok. 

Alkalmazásokhoz tartozó felhasználói fiókok mindig létre kell hozni egy előfizetési felhasználói folyamatot, a regisztrálási vagy bejelentkezési felhasználói folyamata, vagy az Azure AD Graph API-val. Az Azure Portalon létrehozott felhasználói fiókok felügyelete a bérlő csak szolgálnak.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Melyik közösségi Identitásszolgáltatók támogatja most? Melyik tervezi a jövőben támogatásához?
Jelenleg támogatott Facebook, Google +, LinkedIn, Amazon, a Twitterhez (előzetes verzió), WeChat (előzetes verzió), Weibo (előzetes verzió) és Gyorsműveletek (előzetes verzió). Hozzáadjuk más népszerű közösségi Identitásszolgáltatók az ügyfelek igényei szerint támogatása.

Az Azure AD B2C is hozzáadott támogatása [egyéni szabályzatok](active-directory-b2c-overview-custom.md).  Ezek [egyéni szabályzatok](active-directory-b2c-overview-custom.md) lehetővé teszi a saját szabályzat létrehozása, amely bármely identitásszolgáltatóval, amely támogatja a fejlesztő [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) vagy SAML. 

Ismerkedés az egyéni szabályzatok kiveszi a [egyéni házirendet kezdőcsomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Konfigurálhatja a felhasználók további információkat gyűjthet a különféle közösségi Identitásszolgáltatók hatókörök?
Nem. A támogatott set azon közösségi Identitásszolgáltatók használt alapértelmezett hatóköröknek:

* Facebook: e-mailben
* A Google +: e-mailben
* Microsoft-fiók: openid e-mail-profil
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Az alkalmazásom az Azure-ban futtatni, működni az Azure AD B2C-vel való rendelkezik?
Nem, az alkalmazás bárhol (a felhőbeli vagy helyszíni) is üzemeltethet. Használhatja az Azure AD B2C-vel kell csak küldése és fogadása HTTP-kérelmekre a nyilvánosan hozzáférhető végpontokkal lehetővé teszi.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Több Azure AD B2C-bérlő van. Hogyan kezelhetem őket az Azure Portal webhelyen?
A bal oldali menüben, az Azure Portal megnyitása az "Azure AD-B2C", előtt kell váltania a könyvtárba, amelyet kezelni szeretne.  Az identitás, az Azure portal jobb felső kattintva válthat a címtárak, majd válassza a egy könyvtár, a legördülő lista, amely akkor jelenik meg.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hogyan ellenőrzési e-mailek testre (a tartalmat, és az "a:" mező) Azure AD B2C által küldött?
Használhatja a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/fundamentals/customize-branding.md) ellenőrzési e-mailek tartalmának testreszabása. Ez a két elem az e-mail kifejezetten, testre szabható:

* **A szalagcím emblémája**: jobb alsó látható.
* **Háttérszín**: tetején látható.

    ![Képernyőfelvétel a testre szabott visszaigazoló e-mailt](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

E-mail aláírásának tartalmazza az Azure AD B2C-bérlő neve az Azure AD B2C-bérlő első létrehozásakor megadott. Módosíthatja a nevét, ezen utasításokat követve:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) globális rendszergazdájaként.
1. Nyissa meg a **Azure Active Directory** panelen.
1. Kattintson a **tulajdonságok** fülre.
1. Módosítsa a **neve** mező.
1. Kattintson az oldal tetején lévő **Mentés** elemre.

Jelenleg nem lehet módosítani a "származó:" mezőjében az e-mailt.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hogyan telepíthetek át a meglévő felhasználói neveket, jelszavakat és profilok a saját adatbázis az Azure AD B2C-vel?
Az Azure AD Graph API segítségével írhat az áttelepítési eszköz. Tekintse meg a [áttelepítési útmutatója](active-directory-b2c-user-migration.md) részleteiről.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>Helyi fiókok Azure AD B2C-ben használatos jelszó felhasználói folyamatot?
Az Azure AD B2C-vel jelszó felhasználói folyamat helyi fiókok esetében a házirend alapján az Azure ad-hez. Az Azure AD B2C a regisztrációt, a regisztrálási vagy bejelentkezési és a jelszó alaphelyzetbe állítása felhasználói folyamatok használja "erős" jelszó erőssége, és nem jár le a jelszavakat. Olvassa el a [az Azure AD-jelszóházirendet](https://msdn.microsoft.com/library/azure/jj943764.aspx) további részletekért. További információ a fiók zárolásának és jelszavak: [kezeli az erőforrások és adatok az Azure Active Directory B2C fenyegetések](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Az Azure AD Connect használatával a saját Azure AD B2C-vel a helyszíni Active Directory tárolt felhasználói identitások migrálása?
Nem, az Azure AD Connect nem célja, hogy az Azure AD B2C-vel működik. Fontolja meg a [Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) felhasználói migrálásra.  Tekintse meg a [áttelepítési útmutatója](active-directory-b2c-user-migration.md) részleteiről.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Az alkalmazás megnyitásához is az Azure AD B2C-lapok egy IFRAME?
Nem, biztonsági okokból az Azure AD B2C-lapok nem lehet megnyitni egy IFRAME.  A szolgáltatás a böngésző IFRAME időtartamig kommunikál.  A biztonsági Közösséggel, az általános és az OAUTH2-specifikáció, javasoljuk, identitással kapcsolatos műveletet, kattintson az-emelési kockázata miatt az IFRAME használatával.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Például a Microsoft Dynamics CRM-rendszerekkel való működik az Azure AD B2C-vel?
Integráció a Microsoft Dynamics 365-portál érhető el.  Lásd: [Dynamics 365 Portal konfigurálása az Azure AD B2C-t használja a hitelesítéshez](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Az Azure AD B2C-vel does működnek a helyszíni SharePoint 2016 vagy korábbi?
Az Azure AD B2C nem célja a SharePoint külső partner-megosztási forgatókönyvhöz; Lásd: [az Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/) helyette.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Az Azure AD B2C-vel vagy használjak B2B külső identitások kezelése?
Ez a cikk kapcsolatos [külső identitások](../active-directory/active-directory-b2b-compare-external-identities.md) tudhat meg többet a megfelelő szolgáltatásokat alkalmaz a külső azonosító forgatókönyvekhez.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Milyen jelentéskészítési és a naplózási szolgáltatások az Azure AD B2C-t nyújt? Ugyanazok, mint a prémium szintű Azure AD?
Nem, az Azure AD B2C-vel nem támogatja ugyanazokat a jelentések prémium szintű Azure AD-t. Vannak azonban sok commonalities:

* **Jelentkezzen be a jelentések** adjon meg egy rekord minden egyes bejelentkezés csökkentett adatokkal.
* **Naplózási jelentések** egyaránt rendszergazdai tevékenységhez, valamint alkalmazás-tevékenységet tartalmaznak. 
* **Használati jelentések** felhasználók száma, a bejelentkezések száma, és az MFA mennyisége. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>A felhasználói felület az Azure AD B2C által üzemeltetett oldalak is honosításához? Milyen nyelveket támogat?
Igen!  További információ [nyelvi testreszabás](active-directory-b2c-reference-language-customization.md), amely jelenleg nyilvános előzetes verzióban.  Fordítások 36 nyelvhez biztosítunk, és igény szerint bármilyen karakterlánc felül lehet bírálni.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Használható a saját regisztrációs és bejelentkezési lapok, amelyek az Azure AD B2C által üzemeltetett saját URL-címek? Például válthatok az URL-címet a login.microsoftonline.com login.contoso.com?
Jelenleg nem. Ez a funkció tervbe van. A tartomány az ellenőrzése a **tartományok** lap az Azure Portalon nem ezen cél megvalósításához.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hogyan törölhetek Azure AD B2C-bérlő?
Kövesse az alábbi lépéseket az Azure AD B2C-bérlő törléséhez:

1. A szabályzatokat az Azure AD B2C-bérlő törlése.
1. Most jelentkezzen be a [az Azure portal](https://portal.azure.com/) előfizetés-rendszergazdaként. (Használja a ugyanazzal a munkahelyi vagy iskolai fiók vagy a Microsoft-fiók, amellyel Azure-fiók regisztrálása.)
1. Váltás az Azure AD B2C-bérlőt szeretne törölni.
2. Keresse meg az Active Directory a bal oldali menüben.
3. Válassza a **Felhasználók és csoportok** elemet.
4. Jelölje ki minden egyes felhasználó kapcsolja (a jelenleg bejelentkezett, előfizetés-rendszergazda felhasználó kizárása). Kattintson a **törlése** a lapot, majd kattintson az alsó **Igen** amikor a rendszer kéri.
5. Kattintson a **alkalmazásregisztrációk**.
6. Válassza ki a nevű alkalmazás **b2c-kiterjesztések alkalmazását**. Kattintson a **törlése** kattintson **Igen** amikor a rendszer kéri.
7. Válassza az **Áttekintés** lehetőséget.
8. Kattintson a **Delete directory**. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Az Azure AD B2C-t a nagyvállalati mobilitási csomag részeként is be?
Nem, az Azure AD B2C-t az Azure-szolgáltatás utólagos elszámolású csomagokra és nem a nagyvállalati mobilitási csomag részeként.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Hogyan jelenthetem a problémákat az Azure AD B2C-vel?
Lásd: [fájl támogatási kéréseket az Azure Active Directory B2C](active-directory-b2c-support.md).
