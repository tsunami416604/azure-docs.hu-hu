---
title: "Gyakori kérdések (GYIK) – az Azure AD B2C |} Microsoft Docs"
description: "Azure Active Directory B2C-vel kapcsolatos gyakori kérdések"
services: active-directory-b2c
documentationcenter: 
author: saeeda
manager: krassk
editor: bryanla
ms.assetid: ed33c2ca-76d0-442a-abb1-8b7b7bb92d6a
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: saeeda
ms.openlocfilehash: 45ae4ab4c832e7537e6ee78c32603734fa64ad86
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2017
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Az Azure AD B2C: Gyakori kérdések (GYIK) 
Ezen a lapon az Azure Active Directory (Azure AD) B2C kapcsolatos gyakori kérdésekre ad választ. Tartsa biztonsági frissítések keresése.

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>Használható az Azure AD B2C funkciók a a meglévő, alkalmazott-alapú Azure AD-bérlő?
Az Azure AD és az Azure AD B2C külön termékajánlatokat, és nem szerepelhet egyszerre a(z) ugyanannak a bérlőnek.  Az Azure AD-bérlő szervezet jelöli.  Azure AD B2C-bérlő a függő entitások alkalmazásainak használni kívánt identitások gyűjteményét képviseli.  Egyéni házirendek segítségével (a nyilvános előzetes verzió) lehetővé téve az alkalmazottak hitelesítését egy szervezet Azure ad az Azure AD B2C is összevonni.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>Használhatók az Azure AD B2C adja meg a közösségi bejelentkezés (Facebook és Google +) Office 365-be?
Az Azure AD B2C nem használható a Microsoft Office 365 felhasználók hitelesítéséhez.  Az Azure AD SaaS-alkalmazásokhoz való alkalmazott hozzáférés kezelése a Microsoft megoldás, és erre a célra, például a licencelési és a feltételes hozzáférések tervezett szolgáltatások rendelkezik.  Az Azure AD B2C egy identitás- és hozzáférés-kezelési platformot kínál webes és mobilalkalmazásokhoz készítéséhez.  Ha az Azure AD B2C az Azure AD-bérlő összevonásához van konfigurálva, az Azure AD-bérlő kezeli alkalmazott alkalmazások az Azure AD B2C eléréséhez.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>Mik azok a helyi fiókok az Azure AD B2C? Hogyan vannak különbözik a munkahelyi vagy iskolai fiókok Azure AD-ben?
Az Azure AD-bérlő, a bérlőhöz tartozó felhasználók jelentkezzen be egy e-mail címet az űrlap `<xyz>@<tenant domain>`.  A `<tenant domain>` a bérlő vagy az eredeti ellenőrzött tartomány egyik `<...>.onmicrosoft.com` tartomány. Ilyen típusú fiókok munkahelyi vagy iskolai fiókkal.

Azure AD B2C-bérlő, a legtöbb alkalmazást szeretné, hogy a felhasználó bejelentkezhet bármely tetszőleges e-mail címmel (például joe@comcast.net, bob@gmail.com, sarah@contoso.com, vagy jim@live.com). Ez a fiók típus egy helyi fiókot.  Tetszőleges felhasználónevek helyi fiókok (például joe, bob, sarah vagy jim) is támogatja. Az említett két helyi fiók az Azure AD B2C konfigurálása az Azure portálon szerint is választhat.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>Mely közösségi identitás-szolgáltatóktól támogatják a most? Melyik tervezi a jövőben támogatásához?
Jelenleg támogatott Facebook, Google +, LinkedIn, Amazon, Twitter (előzetes verzió), WeChat (előzetes verzió), Weibo (előzetes verzió) és Gyorsműveletek (előzetes verzió). A Microsoft támogatni fogják a többi ügyfél igény szerint népszerű közösségi identitás-szolgáltatóktól.

Az Azure AD B2C is hozzá van adva támogatása [egyéni házirendek](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom).  Ezek [egyéni házirendek](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-overview-custom) lehetővé teszi a fejlesztők saját házirend létrehozásához, amely bármely identitásszolgáltató, amely támogatja a [OpenID Connect](http://openid.net/specs/openid-connect-core-1_0.html) vagy SAML. 

Ismerkedés az egyéni házirendek kiveszi a [egyéni házirend alapszintű csomag](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>Konfigurálhatja a fogyasztók további információkat gyűjt a különböző közösségi identitás-szolgáltatóktól származó hatóköröket?
Nem, de ez a funkció megtalálható a terv. A közösségi identitás-szolgáltatóktól támogatott számú használt alapértelmezett-hatókörök a következők:

* Facebook: e-mailek
* Google +: e-mailek
* Microsoft-fiókot: e-mail-profil openid
* Amazon: profil
* LinkedIn: r_emailaddress, r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>Az alkalmazás futtatható Azure, az Azure AD B2C működik rendelkezik?
Nem, az alkalmazás bárhol (a felhőbeli vagy helyszíni) tárolhatja. Minden Azure AD B2C-vel kommunikál kell azt, hogy a küldési és fogadási HTTP-kérelmek nyilvánosan elérhető végponton képességet.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Van több Azure AD B2C-bérlő. Hogyan lehet kezelni a őket az Azure portálon?
"Az Azure AD B2C" megnyitása az Azure-portálon a bal oldali menüjében, előtt át kell váltania a kezelni kívánt könyvtárba.  Váltson a könyvtárak kattintson a jobb felső sarkában az Azure-portálon identitásra, majd válasszon egy könyvtárat, a legördülő lista, amely akkor jelenik meg.  A részletes lemezképekkel, lásd: [és az Azure AD B2C beállítások lép](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>Hogyan ellenőrző e-mailek testre (a tartalom és a "származó:" mező) az Azure AD B2C által küldött?
Használhatja a [vállalati arculat megjelenítése a szolgáltatás](../active-directory/customize-branding.md) a tartalom az ellenőrző e-mailek testreszabásához. Pontosabban a két elem az e-mailek testre szabható:

* **A szalagcím emblémája**: az alsó sarokban látható.
* **Háttérszín**: felső részén látható.

    ![Képernyőfelvétel a testreszabott ellenőrző e-mailt](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

Az e-mailek aláírás a B2C-bérlő nevét tartalmazza, amelyet a B2C-bérlő kezdeti létrehozásakor megadott. Módosíthatja a nevét, a jelen utasításokat:

1. Jelentkezzen be a [Azure-portálon](https://portal.azure.com/) előfizetés-rendszergazdaként.
1. Nyissa meg a **Azure Active Directory** panelen.
1. Kattintson a **tulajdonságok** fülre.
1. Módosítsa a **neve** mező.
1. Kattintson a **mentése** az oldal tetején.

Jelenleg nincs semmilyen módon nem lehet módosítani a "származó:" az e-mailben található. Szavazhatnak [feedback.azure.com](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/15334335-fully-customizable-verification-emails) érdekli testreszabása a megerősítési e-mailt törzsét.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>Hogyan tudom áttelepíthetők a meglévő felhasználóneveket, jelszavakat és profilokat az adatbázis Azure AD B2C?
Az Azure AD Graph API-t használhatja az áttelepítési eszköz írni. Tekintse meg a [Graph API-mintában](active-directory-b2c-devquickstarts-graph-dotnet.md) részleteiről.

### <a name="what-password-policy-is-used-for-local-accounts-in-azure-ad-b2c"></a>Milyen jelszóházirend az Azure AD B2C helyi fiókok esetében használatos?
A helyi felhasználói fiókok Azure AD B2C jelszóházirend az Azure AD a házirend alapul. Az Azure AD B2C-előfizetés, a regisztráció vagy bejelentkezés és a jelszó alaphelyzetbe állítása házirendeket használ a "erős" jelszó erőssége, és nem jár le a jelszavakat. Olvassa el a [az Azure AD-jelszóházirendet](https://msdn.microsoft.com/library/azure/jj943764.aspx) további részleteket.

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>Az Azure AD Connect használatával telepítse át a felhasználói identitásokat a helyszíni Active Directory Azure AD B2C tárolt?
Nem, az Azure AD Connect nem célja, hogy az Azure AD B2C dolgozni. Érdemes lehet a [Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md) felhasználói áttelepítés.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>Saját alkalmazás megnyitása is az Azure AD B2C lapok iFrame belül?
Nem, biztonsági okokból az Azure AD B2C-lap nem nyitható iFrame belül.  A szolgáltatás IFRAME letiltja a böngésző kommunikál.  A biztonsági Közösség általános és a OAUTH2 specifikációját, javasoljuk, szemben az identitással kapcsolatos műveletet, kattintson az-emelési kockázata miatt IFRAME használatával.

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>Az Azure AD B2C például a Microsoft Dynamics CRM rendszerrel működik?
Integráció a Microsoft Dynamics 365 Portal érhető el.  Lásd: [Dynamics 365 portál konfigurálása az Azure AD B2C hitelesítéshez használandó](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>Az Azure AD B2C does munkahelyi SharePoint helyszíni 2016 vagy korábbi?
Az Azure AD B2C nem célja a SharePoint külső partner-megosztási forgatókönyvhöz; Lásd: [Azure AD B2B](http://blogs.technet.com/b/ad/archive/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview.aspx) helyette.

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>Érdemes használni az Azure AD B2C vagy B2B külső identitások kezeléséhez?
Olvassa el ebben a cikkben [külső identitások](../active-directory/active-directory-b2b-compare-external-identities.md) tudhat meg többet a megfelelő szolgáltatásokat alkalmaz a külső identitások forgatókönyvek.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>Milyen jelentéskészítési és a naplózási szolgáltatások az Azure AD B2C nyújt? Vannak ugyanaz, mint a prémium szintű Azure AD?
Az Azure AD B2C nem, nem támogatja ugyanazokat a jelentések szerint az Azure AD Premium. Vannak azonban sok commonalities:

* **Bejelentkezési jelentések** csak érhetők el az Azure portálon (Azure Active Directory > tevékenység > bejelentkezések), és nem érhetők el a Graph API-n keresztül. Minden egyes bejelentkezés rekordja csökkentett adatokkal rendelkeznek.
* **Naplózási jelentések** csak érhetők el az Azure portálon (Azure Active Directory > tevékenység > Naplók), és nem érhetők el a Graph API-n keresztül. A felügyeleti tevékenység, valamint alkalmazástevékenységhez tartalmaznak. 
* **Használati jelentések** érhetők el csak a [használati Reporting API](active-directory-b2c-reference-usage-reporting-api.md) és nem érhetők el az Azure-portálon. Azon felhasználók számát, a bejelentkezések száma és a kötet MFA tartalmaznak. 

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>A felhasználói felület az Azure AD B2C által kiszolgált lapok is localize? Milyen nyelveket támogatja?
Igen!  További információ a [nyelvi testreszabási](active-directory-b2c-reference-language-customization.md), amely nyilvános előzetes verzió van.  Azt a fordítások 36 nyelvet a, és az igényeinek bármilyen karakterlánc lehet felülbírálni.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>Használható önállóan URL-címeket a regisztrációs és a bejelentkezési oldalon, az Azure AD B2C által szolgáltatott? Például módosítható az URL-címet a login.microsoftonline.com való login.contoso.com?
Jelenleg nem. Ez a funkció a terv van. A tartomány ellenőrzése a **tartományok** a klasszikus Azure portálra a lap nem ezen cél megvalósításához.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>Hogyan törli a saját Azure AD B2C-bérlő?
Kövesse az alábbi lépéseket az Azure AD B2C-bérlő törlése:

1. Az alábbi lépéseket követve [navigáljon az Azure AD B2C beállítások](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) az Azure portálon.
1. Keresse meg a **alkalmazások**, **identitás-szolgáltatóktól**, és **házirend** és azok az összes bejegyzést törölni.
1. Most már bejelentkezhet a [a klasszikus Azure portálon](https://manage.windowsazure.com/) előfizetés-rendszergazdaként. (Használja a ugyanaz a munkahelyi vagy iskolai fiók vagy a Microsoft-fiók, amellyel regisztráció az Azure.)
1. Lépjen a bal oldalon található Active Directory bővítményre, majd kattintson a B2C bérlőre.
1. Kattintson a **felhasználók** fülre.
1. Jelölje ki minden egyes felhasználó bekapcsolása (zárja ki a előfizetési rendszergazda felhasználó, akkor jelenleg felhasználóként van bejelentkezve). Kattintson a **törlése** a lapon, majd kattintson az alsó **Igen** megjelenésekor.
1. Kattintson a **alkalmazások** fülre.
1. Válassza ki **a vállalat tulajdonában lévő alkalmazások** a a **megjelenítése** legördülő mezőben, majd kattintson a pipa.
1. Egy alkalmazást, **b2c-bővítmények-alkalmazás**. Kattintson a **törlése** a lapon, majd kattintson az alsó **Igen** megjelenésekor.
1. Keresse meg az Active Directory-bővítményt újra, és válassza ki a B2C-bérlő.
1. Kattintson a **törlése** az oldal alján. A folyamat befejezéséhez kövesse a képernyőn megjelenő utasításokat.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>Az Azure AD B2C nagyvállalati mobilitási csomag részeként szerezhető?
Az Azure AD B2C nem, a használatalapú fizetés Azure-szolgáltatás, és nem része a nagyvállalati mobilitási csomag.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>Azure AD B2C-vel kapcsolatos problémák jelentése?
Lásd: [fájl támogatási kérelmek az Azure Active Directory B2C](active-directory-b2c-support.md).
