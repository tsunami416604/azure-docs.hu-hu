---
title: Az Azure Active Directory forgatókönyv építőelemei a koncepció igazolása |} Microsoft Docs
description: Vizsgálatát, és gyorsan végrehajthatja az identitás- és kezelési helyzetek
services: active-directory
keywords: az Azure active directory, a forgatókönyv, a koncepció igazolása, PoC
documentationcenter: ''
author: dstefanMSFT
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: dstefan
ms.openlocfilehash: ed64c9df6fcca8f85b200c5f738c2009ea7ae0a5
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35293109"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Az Azure Active Directory alkalmazástervezési a koncepció igazolása: építőelemek

## <a name="catalog-of-roles"></a>A szerepkörök katalógus

| Szerepkör | Leírás | Megvalósíthatósági felelősségi igazolása |
| --- | --- | --- |
| **Identitás-architektúra / fejlesztői csapat** | A csapat az általában azt, amelyik a megoldás tervez, prototípusok megvalósítja, jóváhagyások meghajtók és végül átadja az műveletek | Ezek biztosítják, hogy a környezetben, és kezelhetőség szempontjából a különböző alkalmazási helyzetek értékelése a meglévők közül |
| **A helyszíni identitás műveleti csapata** | Kezeli a különböző identitás források helyszíni: Active Directory-erdők, LDAP-könyvtárak, HR rendszerek és összevonási identitás-szolgáltatóktól. | A helyszíni hozzáférést biztosítanak a koncepció forgatókönyvhöz szükséges erőforrásokat.<br/>Akkor be kell vonni lehető|
| **Műszaki Alkalmazástulajdonosok** | A különböző felhőalkalmazások és szolgáltatásokról, amelyek az Azure ad-vel integrálni műszaki tulajdonosai | Adja meg az adatokat a SaaS-alkalmazások (esetleg tesztelési példány) |
| **Az Azure AD globális rendszergazda** | Kezeli az Azure AD konfigurálása | Adjon meg hitelesítő adatokat konfigurálja a szinkronizálási szolgáltatást. Általában azonos Identity architektúra, vonja össze az koncepció során, de a műveletek fázis során külön|
| **Adatbázis-csoport** | Az adatbázis-infrastruktúra tulajdonosainak | SQL-környezetben (AD FS vagy az Azure AD Connect) hozzáférést biztosítanak az adott forgatókönyv műveleteket.<br/>Akkor be kell vonni lehető |
| **Hálózati adapterek** | A hálózati infrastruktúra tulajdonosainak | Adja meg a szinkronizálási kiszolgálók megfelelően az adatforrást, és a felhőalapú szolgáltatások (tűzfalszabályok, megnyitott portok, IPSec-szabályok stb.) a hálózati szintű hozzáférési |
| **Biztonsági csapat** | A biztonsági stratégia meghatározása, elemzi a különböző forrásokból származó biztonsági jelentések és megállapítások következik. | Adja meg a célként megadott biztonsági értékelési forgatókönyvek |

## <a name="common-prerequisites-for-all-building-blocks"></a>Minden egyéb építőelemekből közös előfeltételei

Az alábbiakban néhány szükséges előfeltételek bármely Koncepció az Azure AD Premium szükséges.

| Előfeltétel | További források |
| --- | --- |
| Az Azure AD-bérlő definiált egy érvényes Azure-előfizetés | [Az Azure Active Directory-bérlő beszerzése](active-directory-howto-tenant.md)<br/>**Megjegyzés:** Ha már rendelkezik Azure AD Premium licenccel rendelkező környezetek, nulla cap előfizetés beszerezheti útvonalon https://aka.ms/accessaad <br/>További tudnivalókért olvassa el: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ és https://technet.microsoft.com/library/dn832618.aspx |
| Meghatározott és ellenőrzött tartományok | [Egy egyéni tartománynév hozzáadása az Azure Active Directoryban](active-directory-domains-add-azure-portal.md)<br/>**Megjegyzés:** bizonyos munkaterhelések esetén, mint a Power bi-ban az azure AD-bérlő a színfalak sikerült létrehozta. Ellenőrizze, hogy a bérlő társítva-e egy adott tartományban, lépjen https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Ha tarozik sikeres, akkor a tartomány már hozzá van rendelve egy bérlő, és vegye át lehet szükség. Ha igen, lépjen kapcsolatba a Microsofttal kapcsolatos további iránymutatásért. További információ a felvásárlási beállításokat érintő: [Mi az az Azure önkiszolgáló regisztráció?](active-directory-self-service-signup.md) |
| Prémium szintű Azure AD- vagy EMS próba engedélyezve | [Az Azure Active Directory prémium ingyenes egy hónapos](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Prémium szintű Azure AD- vagy EMS-licenceket rendelt koncepció felhasználók | [Licenc saját maga és a felhasználók az Azure Active Directoryban](active-directory-licensing-get-started-azure-portal.md) |
| Az Azure AD globális rendszergazda hitelesítő adataival | [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directoryban](active-directory-assign-admin-roles-azure-portal.md) |
| Nem kötelező, de erősen ajánlott: tartalékként párhuzamos laborkörnyezetben | [Az Azure AD Connect előfeltételei](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Directory szinkronizálási - Jelszókivonat-szinkronizálás (PHS-ben) – új telepítése

Hozzávetőleges időt Complete: kisebb, mint 1000 koncepció felhasználó egy óra

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Futtatására az Azure AD Connect | [Az Azure AD Connect előfeltételei](./connect/active-directory-aadconnect-prerequisites.md) |
| Koncepció felhasználók, azonos tartományban és része egy biztonsági csoport és szervezeti egység | [Az Azure AD Connect testreszabott telepítése](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Az Azure AD Connect-funkciókat a koncepció szükséges azonosítják. | [Az Azure Active Directoryval az Active Directory Connect - szinkronizálás konfigurálása szolgáltatások](./connect/active-directory-aadconnect.md#configure-sync-features) |
| A helyszíni hitelesítő adatok kellett, és a felhőalapú környezetben  | [Azure AD Connect: fiókok és engedélyek](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Az Azure AD Connect legújabb verziójának letöltése | [A Microsoft Azure Active Directory Connect letöltése](https://www.microsoft.com/download/details.aspx?id=47594) |
| Azure AD Connect telepítése a legegyszerűbb elérési úttal rendelkező: Express <br/>1. A cél a szinkronizálási ciklusban idő minimalizálása érdekében OU szűrése<br/>2. Válasszon cél felhasználók a helyi csoport.<br/>3. Ha a Koncepció témák számára szükséges szolgáltatásokat | [Az Azure AD Connect: Testreszabott telepítés: tartomány és szervezeti egységek szűrése](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Az Azure AD Connect: Testreszabott telepítés: csoport alapú szűrése](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Az Azure AD Connect: A helyszíni identitások integrálása az Azure Active Directoryval: a szinkronizálási funkciók konfigurálása](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Nyissa meg az Azure AD Connect felhasználói felületén, és a futó profilok befejezett (importálás, szinkronizálási és exportálás) | [Az Azure AD Connect szinkronizálása: ütemező](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Nyissa meg a [Azure AD felügyeleti portál](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), nyissa meg a "Minden felhasználó" paneljét, "hatóság" forrásoszlop és ellenőrizze, hogy a felhasználók jelennek meg, "Windows Server AD" érkező megfelelően jelölésű hozzáadása | [Az Azure AD felügyeleti portál](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Megfontolandó szempontok

1. Nézze meg a biztonsági szempontok a Jelszókivonat-szinkronizálás [Itt](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Ha kísérleti felhasználóknak Jelszókivonat-szinkronizálás nincs véglegesen lehetőség, fontolja meg a következő lehetőségeket:
   * Tesztfelhasználók létrehozása az éles tartományhoz. Ellenőrizze, hogy bármely más fiók nincs szinkronizálás
   * Helyezze át a UAT környezethez
2.  Ha azt szeretné, hogy összevonási, érdemes a költségek egy összevont megoldás társított helyszíni identitásszolgáltató túl a Koncepció ismertetése és mérjük, amely a keresett előnyöket szemben:
    * A kritikus elérési utat úgy, hogy a magas rendelkezésre állás kialakítása van
    * Egy kapacitás terv kell a helyi szolgáltatás
    * Egy figyelő/karbantartása/javítás kell a helyi szolgáltatás

További: [az Office 365 identitás- és Azure Active Directory - összevont identitás](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Védjegyek

Hozzávetőleges időt Complete: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Eszközök (képek, emblémák, stb.); A legjobb képi megjelenítés győződjön meg arról, hogy az eszközök rendelkezik az ajánlott méreteket. | [Vállalati arculat megjelenítése a bejelentkezési oldal az Azure Active Directoryban](active-directory-branding-custom-signon-azure-portal.md) |
| Nem kötelező: Ha a környezetben az AD FS-kiszolgáló, a hozzáférést a kiszolgáló, webes témák testreszabása | [AD FS felhasználói bejelentkezési testreszabása](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Ügyfélszámítógép végfelhasználói bejelentkezési élmény végrehajtásához |  |
| Választható lehetőség: Mobileszközök élmény ellenőrzése |  |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Ugrás az Azure AD felügyeleti portál | [Azure AD felügyeleti portál - vállalati arculat megjelenítése](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Töltse fel az eszközök a bejelentkezési oldal (kiemelt embléma kis embléma, címkék, stb.). Nem kötelező, ha az AD FS igazítása az AD FS bejelentkezési oldalak ugyanazokat az eszközöket | [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési Panel oldalon: testreszabható elemek](customize-branding.md) |
| Várjon néhány percig, amíg a módosítás teljesen érvénybe |  |
| Jelentkezzen be a Koncepció felhasználói hitelesítő adatok https://myapps.microsoft.com |  |
| Erősítse meg a böngészőben megjelenését és működését | [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési Panel oldalon](customize-branding.md) |
| Szükség esetén ellenőrizze az egyéb eszközök megjelenését és működését |  |

### <a name="considerations"></a>Megfontolandó szempontok

Ha régi megjelenését és működését marad, a testreszabást követően a böngésző ügyfél gyorsítótárának ürítése, és próbálja megismételni a műveletet.

## <a name="group-based-licensing"></a>Csoport alapú licencelés

Hozzávetőleges időt Complete: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Senki Koncepció részei egy biztonsági csoportot (helyszíni vagy felhőalapú) | [Hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban](active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Nyissa meg az Azure AD felügyeleti portál licencek paneljét | [Azure AD felügyeleti portál: Licencelés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| A licenceket rendelhet a biztonsági csoport Koncepció felhasználókkal. | [Az Azure Active Directory felhasználók csoportja számára licencek hozzárendelése](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Megfontolandó szempontok

Probléma merül fel, ha Ugrás [forgatókönyvek, korlátozások és a csoportok használatával felügyeli a licencelés az Azure Active Directory szolgáltatással kapcsolatos ismert problémák](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>SaaS összevont egyszeri bejelentkezés konfigurálása

Hozzávetőleges időt Complete: 60 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A rendelkezésre álló SaaS-alkalmazás tesztkörnyezetben. Ez az útmutató a ServiceNow használjuk példaként.<br/>Erősen ajánlott a test-példányt használ a Navigálás a meglévő adatok minőségét és hozzárendelések súrlódás minimalizálása érdekében. | Ugrás a https://developer.servicenow.com/app.do#! / otthoni egy teszt példány folyamatának elindításához. |
| A ServiceNow felügyeleti konzol rendszergazdai hozzáféréssel | [Oktatóanyag: Azure Active Directoryval integrált ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Célja állítja be a felhasználók az alkalmazást. A koncepció felhasználókat tartalmazó biztonsági csoport használata ajánlott. <br/>Ha a csoport létrehozása esetén nem valósítható meg, majd kiosztása a felhasználók közvetlenül az alkalmazás a koncepció | [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](manage-apps/assign-user-or-group-access-portal.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Az oktatóanyag összes szereplő Microsoft Documentation megosztása  | [Oktatóanyag: Azure Active Directoryval integrált ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Egy működő értekezlet beállítása, és minden résztvevővel Útmutató lépéseit kövesse. | [Oktatóanyag: Azure Active Directoryval integrált ServiceNow](active-directory-saas-servicenow-tutorial.md) |
| Az alkalmazás rendelhet hozzá a csoporthoz, az Előfeltételek azonosított. Van-e a Koncepció feltételes hozzáférése a hatókörben, le újra, hogy később, és adja hozzá a többtényezős Hitelesítést, és hasonló. <br/>Megjegyzés: Ez a telepítési folyamatot a rendszer indítsa (Ha be van állítva) |  [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](manage-apps/assign-user-or-group-access-portal.md) <br/>[Hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban](active-directory-groups-create-azure-portal.md) |
| Az Azure AD felügyeleti portál használatával adja hozzá az alkalmazás a ServiceNow gyűjteményből| [Azure AD felügyeleti portál: vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Vállalati alkalmazások kezelése az Azure Active Directory újdonságai](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| "Egyszeri bejelentkezés" panelen ServiceNow alkalmazás SAML-alapú bejelentkezés"engedélyezése" |  |
| Töltse ki a "Bejelentkezési URL-cím" és "Azonosítója" mezőket a ServiceNow URL-címmel<br/>A jelölőnégyzet bejelölésével "Aktívvá új tanúsítvány"<br/>és a beállítások mentése |  |
| A panel alsó részén a ServiceNow konfigurálhatók egyénileg kialakított utasításokat megtekintéséhez nyissa meg "Konfigurálása ServiceNow" paneljét |  |
| Kövesse az utasításokat a ServiceNow konfigurálása |  |
| A ServiceNow alkalmazás "Kiépítés" panelen "Automatikus" kiépítés engedélyezése | [Az új Azure-portálon a vállalati alkalmazások telepítése a felhasználói fiók kezelése](manage-apps/configure-automatic-user-provisioning-portal.md) |
| Várjon néhány percig, amíg kiépítése befejeződött.  Addig is akkor is ellenőrizhesse a létesítési jelentések |  |
| Jelentkezzen be https://myapps.microsoft.com/ jogosultságokkal rendelkező felhasználóként teszt | [Mi az a hozzáférési Panel?](active-directory-saas-access-panel-introduction.md) |
| Kattintson a csempére az újonnan létrehozott alkalmazás. Hozzáférés megerősítése |  |
| Másik lehetőségként az alkalmazás használati jelentések ellenőrizheti. Megjegyzés: egyes késést, ezért meg kell várnia kell néhány a forgalmat a jelentések megtekintéséhez. | [Az Azure Active Directory portálon bejelentkezési tevékenység jelentések: felügyelt alkalmazások használata](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Az Azure Active Directory jelentésmegőrzési házirendje](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Megfontolandó szempontok

1. Fent [oktatóanyag](active-directory-saas-servicenow-tutorial.md) hivatkozik a régi Azure AD felügyeleti élmény. A koncepció alapul, de [gyors üzembe helyezés](active-directory-enterprise-apps-whats-new-azure-portal.md#quickstart-get-going-with-your-new-application-right-away) tapasztalhat.
2. Ha a cél alkalmazás nem található a katalógusban, majd használhatja "Állapotba hozása a saját alkalmazás". További: [What's new in Azure Active Directory vállalati Alkalmazáskezelés: egyetlen helyről egyéni alkalmazás hozzáadása](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS-jelszó egyszeri bejelentkezés konfigurálása

Hozzávetőleges időt Complete: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A tesztkörnyezet az SaaS-alkalmazásokhoz. Egy Egyszeri jelszót példája HipChat és a Twitter. Egyetlen más alkalmazáshoz van szüksége az oldal HTML-bejelentkezési űrlap pontos URL-CÍMÉT. | [A Microsoft Azure piactérről Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[A Microsoft Azure piactérről HipChat](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Tesztelje a fiókok az alkalmazásokhoz. | [Feliratkozás a Twitteren](https://twitter.com/signup?lang=en)<br/>[Regisztráljon az ingyenes: HipChat](https://www.hipchat.com/sign_up) |
| Célja állítja be a felhasználók az alkalmazást. Egy biztonsági csoportot a felhasználók tartalmazott ajánlott. | [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](manage-apps/assign-user-or-group-access-portal.md) |
| Egy számítógépet az Internet Explorer, a Chrome vagy a Firefox a hozzáférési Panel bővítmény telepítéséhez helyi rendszergazdai hozzáféréssel | [Az Internet Explorer hozzáférési Panel bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel bővítményével Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel bővítményével Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A bővítmény telepítése | [Az Internet Explorer hozzáférési Panel bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel bővítményével Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel bővítményével Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Állítsa be alkalmazását a gyűjteményből | [What's new in Azure Active Directory vállalati Alkalmazáskezelés: az új és továbbfejlesztett alkalmazáskatalógusában](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Egyszeri jelszó konfigurálása | [Egyszeri bejelentkezés a vállalati alkalmazásokat az új Azure-portálon kezelése: jelszó alapú bejelentkezés](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Az alkalmazás rendelhet hozzá a csoporthoz, az Előfeltételek azonosított | [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](manage-apps/assign-user-or-group-access-portal.md) |
| Jelentkezzen be https://myapps.microsoft.com/ jogosultságokkal rendelkező felhasználóként teszt |  |
| Kattintson a csempére az újonnan létrehozott alkalmazás. | [Mi az, hogy a hozzáférési Panel?: jelszó alapú egyszeri bejelentkezési identitás kiépítés nélkül](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Adja meg az alkalmazás hitelesítő adatai | [Mi az, hogy a hozzáférési Panel?: jelszó alapú egyszeri bejelentkezési identitás kiépítés nélkül](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zárja be a böngészőt, és ismételje meg a bejelentkezés. A felhasználó ezen időre körülbelül zökkenőmentes hozzáférést biztosít az alkalmazás kell látnia. |  |
| Másik lehetőségként az alkalmazás használati jelentések ellenőrizheti. Megjegyzés: egyes késést, ezért meg kell várnia kell néhány a forgalmat a jelentések megtekintéséhez. | [Az Azure Active Directory portálon bejelentkezési tevékenység jelentések: felügyelt alkalmazások használata](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Az Azure Active Directory jelentésmegőrzési házirendje](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Megfontolandó szempontok

Ha a cél alkalmazás nem található a katalógusban, majd használhatja "Állapotba hozása a saját alkalmazás". További: [What's new in Azure Active Directory vállalati Alkalmazáskezelés: egyetlen helyről egyéni alkalmazás hozzáadása](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Vegye figyelembe az alábbi követelményeknek:
   * Alkalmazás egy ismert bejelentkezési URL-címet kell rendelkeznie.
   * A bejelentkezési oldal tartalmaznia kell egy HTML-űrlaphoz, amely a böngészőbővítmények automatikus-töltheti fel egy további szöveg mezőben. A minimum felhasználónevet és jelszót kell tartalmaznia.

## <a name="saas-shared-accounts-configuration"></a>Megosztott SaaS konfigurációja

Hozzávetőleges időt Complete: 30 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A pontos bejelentkezési URL-CÍMEK időben és célalkalmazások listáját. Tegyük fel a Twitter is használhatja. | [A Microsoft Azure piactérről Twitter](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Feliratkozás a Twitteren](https://twitter.com/signup?lang=en) |
| Megosztott az SaaS-alkalmazáshoz tartozó hitelesítő adatok. | [Az Azure AD fiókok megosztása](active-directory-sharing-accounts.md)<br/>[Az azure AD automatikus jelszó során a Facebook, a Twitter és a LinkedIn előzetes verzióként elérhető! -Nagyvállalati mobilitással és biztonsággal foglalkozó blogban] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Legalább két csoport tagjai ugyanazt a fiókot hozzáférő hitelesítő adatait. Azok a biztonsági csoport részének kell lennie. | [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](manage-apps/assign-user-or-group-access-portal.md) |
| Egy számítógépet az Internet Explorer, a Chrome vagy a Firefox a hozzáférési Panel bővítmény telepítéséhez helyi rendszergazdai hozzáféréssel | [Az Internet Explorer hozzáférési Panel bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel bővítményével Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel bővítményével Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A bővítmény telepítése | [Az Internet Explorer hozzáférési Panel bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel bővítményével Chrome](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel bővítményével Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Állítsa be alkalmazását a gyűjteményből | [What's new in Azure Active Directory vállalati Alkalmazáskezelés: az új és továbbfejlesztett alkalmazáskatalógusában](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Egyszeri jelszó konfigurálása | [Egyszeri bejelentkezés a vállalati alkalmazásokat az új Azure-portálon kezelése: jelszó alapú bejelentkezés](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Az alkalmazás rendelhet hozzá a csoporthoz, az Előfeltételek hitelesítő adatok hozzárendelése során azonosított | [Egy felhasználó vagy csoport hozzárendelése egy vállalati alkalmazás Azure Active Directoryban](manage-apps/assign-user-or-group-access-portal.md) |
| Jelentkezzen be másik felhasználóként, hogy hozzáférési alkalmazást a **ugyanazt a közös fiókot.**  |  |
| Másik lehetőségként az alkalmazás használati jelentések ellenőrizheti. Megjegyzés: egyes késést, ezért meg kell várnia kell néhány a forgalmat a jelentések megtekintéséhez. | [Az Azure Active Directory portálon bejelentkezési tevékenység jelentések: felügyelt alkalmazások használata](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Az Azure Active Directory jelentésmegőrzési házirendje](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Megfontolandó szempontok

Ha a cél alkalmazás nem található a katalógusban, majd használhatja "Állapotba hozása a saját alkalmazás". További: [What's new in Azure Active Directory vállalati Alkalmazáskezelés: egyetlen helyről egyéni alkalmazás hozzáadása](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Vegye figyelembe az alábbi követelményeknek:
   * Alkalmazás egy ismert bejelentkezési URL-címet kell rendelkeznie.
   * A bejelentkezési oldal tartalmaznia kell egy HTML-űrlaphoz, amely a böngészőbővítmények automatikus-töltheti fel egy további szöveg mezőben. A minimum felhasználónevet és jelszót kell tartalmaznia.

## <a name="app-proxy-configuration"></a>Alkalmazás proxykonfigurációt

Hozzávetőleges időt Complete: 20 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A Microsoft Azure AD alapszintű vagy premium előfizetéssel és, amelynek egy globális rendszergazda Azure AD-címtár | [Az Azure Active Directory-kiadások](active-directory-whatis.md) |
| A webes alkalmazás üzemeltetett a helyszínen, amelyet a távelérés konfigurálása |  |
| A Windows Server 2012 R2 vagy Windows 8.1 vagy újabb rendszert futtató kiszolgáló, amelyre telepítheti az alkalmazásproxy-összekötő | [Az Azure AD-alkalmazásproxy összekötők ismertetése](manage-apps/application-proxy-connectors.md) |
| Ha az elérési út egy tűzfal található, győződjön meg arról, hogy meg nyitva, hogy az összekötő el tudja küldeni az alkalmazásproxynak HTTPS (TCP) kérések | [Alkalmazásproxy engedélyezése az Azure-portálon: alkalmazásproxy Előfeltételek](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Ha a szervezet proxykiszolgálót használ az internetkapcsolat működését, hajtsa végre a megfelelő egy pillantást a blog működő utáni proxykiszolgálókkal meglévő helyszíni őket konfigurálásával kapcsolatos részletekért | [A meglévő helyszíni proxykiszolgálókkal működik](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Összekötő telepítése a kiszolgálón | [Alkalmazásproxy engedélyezése az Azure-portálon: az összekötő regisztrálása és telepítése](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| Az Azure AD alkalmazásproxy alkalmazásként a helyszíni alkalmazás közzététele | [Az Azure AD-alkalmazásproxy használó alkalmazások közzététele](manage-apps/application-proxy-publish-azure-portal.md) |
| Teszt felhasználók hozzárendelése | [Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával: teszt felhasználó hozzáadása](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Nem kötelező lépésként beállíthatja egy egyszeri bejelentkezéses felhasználói élmény | [Adja meg az egyszeri bejelentkezés az Azure AD-alkalmazásproxyval](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| Alkalmazás tesztelése a hozzárendelt felhasználó MyApps portálra való bejelentkezéskor | https://myapps.microsoft.com |

### <a name="considerations"></a>Megfontolandó szempontok

1. Javasoljuk, hogy az összekötő és a vállalati hálózaton, miközben vannak esetek, amikor megjelenik a felhő helyezés jobb teljesítményt. További: [Azure Active Directory Application Proxy használata esetén a hálózati topológia szempontjai](manage-apps/application-proxy-network-topology.md)
2. További biztonsági részletek és hogyan Ez a különösen biztonságos távoli hozzáférést biztosít a megoldás csak a kimenő kapcsolatok fölött:: [alkalmazásokhoz távolról az Azure AD-alkalmazásproxy használatával fér hozzá biztonsági szempontjai](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Általános LDAP-összekötő konfigurálása

Hozzávetőleges időt Complete: 60 perc

> [!IMPORTANT]
> Ez a beállítás egy speciális igénylő FIM vagy MIM bizonyos fokú ismeretét. Ha használja termelési, javasoljuk, hogy ez a konfiguráció kapcsolatos kérdéseket halad át [Premier támogatási](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Az Azure AD Connect telepítése és konfigurálása | Építőelem: [a címtár-szinkronizálás - Jelszókivonat-szinkronizálás](#directory-synchronization--password-hash-sync-phs--new-installation) |
| ADLDS példány értekezlet követelmények | [Általános LDAP-összekötő technikai útmutató: az általános LDAP-összekötő áttekintése](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Felhasználók által használt, munkaterhelések és az ilyen terhelések tartozó attribútumok listája | [Azure AD Connect szinkronizálása: az Azure Active Directoryval szinkronizált attribútumok](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Általános LDAP-összekötő hozzáadása | [Általános LDAP-összekötő műszaki útmutatója: hozzon létre egy új összekötőt](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| (A teljes importálás, különbözeti importálás, teljes szinkronizálást, a különbözeti szinkronizálás, exportálás) létrehozott összekötőhöz futtatási profilok létrehozása | [A felügyeleti ügynök futtatási profil létrehozásához](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Az az Azure AD Connect szinkronizálási Service Manager-összekötők használata](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Teljes importálás profil futtatni, és ellenőrizze, hogy nincsenek-e objektumokat a kapcsolódási térbe | [Egy összekötő terület objektum keresése](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Összekötők használata a az Azure AD Connect Sync Service Manager: Összekötőtér keresése](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Szinkronizálási szabályok létrehozása, hogy a Metaverzumban található objektumok munkaterhelések szükséges attribútumokkal rendelkeznek | [Azure AD Connect szinkronizálása: gyakorlati tanácsok az alapértelmezett konfiguráció módosításának: szinkronizálási szabályok módosításai](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Azure AD Connect szinkronizálása: Understanding deklaratív kiépítés](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Azure AD Connect szinkronizálása: deklaratív kiépítés kifejezések ismertetése](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Indítsa el a teljes szinkronizálási ciklust | [Azure AD Connect szinkronizálása: a Feladatütemező: a Feladatütemező indítása](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| Problémák esetén hajtsa végre a hibaelhárítási | [Az Azure AD-val nem szinkronizálódó objektumok hibaelhárítása](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Győződjön meg arról, hogy LDAP felhasználó bejelentkezési és hozzáférni az alkalmazáshoz | https://myapps.microsoft.com |

### <a name="considerations"></a>Megfontolandó szempontok

> [!IMPORTANT]
> Ez a beállítás egy speciális igénylő FIM vagy MIM bizonyos fokú ismeretét. Ha használja termelési, javasoljuk, hogy ez a konfiguráció kapcsolatos kérdéseket halad át [Premier támogatási](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Csoportok - delegált tulajdonosa

Hozzávetőleges időt Complete: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| SaaS-alkalmazáshoz (összevont egyszeri Bejelentkezéses vagy Egyszeri jelszó) már konfigurálva | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| Felhő csoporthoz rendelt hozzáférési # az 1. az alkalmazás, amelynél | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) <br/>[Hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban](active-directory-groups-create-azure-portal.md) |
| A csoport tulajdonosa a hitelesítő adatok | [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](active-directory-manage-groups.md) |
| Az Infomunkás a alkalmazásokhoz fér hozzá a hitelesítő adatok azonosítása | [Mi az a hozzáférési Panel?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A csoportnak, amely rendelkezik hozzáférni az alkalmazáshoz, és konfigurálja a megadott tulajdonos csoport| [Az Azure Active Directory csoport beállításainak kezelése ](active-directory-groups-settings-azure-portal.md) |
| Jelentkezzen be a csoport tulajdonosa, lásd: a hozzáférési panel csoportok lapján a csoporttagságot | [Az Azure Active Directory-csoportok kezelése lap](https://account.activedirectory.windowsazure.com/r/#/groups) |
| A vizsgálni kívánt Infomunkás hozzáadása |  |
| Jelentkezzen be az információkkal dolgozó szakember, győződjön meg arról, a csempe érhető el | [Mi az a hozzáférési Panel?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Megfontolandó szempontok

Ha az alkalmazás kiépítés engedélyezve van, szükség lehet Várjon néhány percet, amíg az üzembe helyezés befejeződik az információkkal dolgozó az alkalmazás elérése előtt.

## <a name="saas-and-identity-lifecycle"></a>SaaS és identitás életciklusa

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| SaaS-alkalmazáshoz (összevont egyszeri Bejelentkezéses vagy Egyszeri jelszó) már konfigurálva | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| Felhő csoporthoz rendelt hozzáférési # az 1. az alkalmazás, amelynél | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) <br/>[Hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban](active-directory-groups-create-azure-portal.md) |
| Az Infomunkás a alkalmazásokhoz fér hozzá a hitelesítő adatok azonosítása | [Mi az a hozzáférési Panel?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Távolítsa el a felhasználót a csoporthoz, az alkalmazás hozzá van rendelve. | [Az Azure Active Directory-bérlő felhasználók csoport tagságának kezelésére](active-directory-groups-members-azure-portal.md) |
| Várjon néhány percig, megszüntetést | [Az Azure AD SaaS-alkalmazás a felhasználók átadása az automatikus: hogyan működik az automatikus létesítési munkahelyi?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| A külön böngésző-munkamenet jelentkezzen be az információkkal dolgozó szakemberek számára az alkalmazások portálon, és győződjön meg arról, hogy a csempe nem található | http://myapps.microsoft.com |


### <a name="considerations"></a>Megfontolandó szempontok

A koncepció forgatókönyvnek kilépők és/vagy hagyja az hiányában forgatókönyvek extrapolálják. Ha a felhasználó lekérdezi az le van tiltva a helyszíni AD vagy eltávolítani, már nincs meg tudja-e jelentkezni a SaaS-alkalmazáshoz.

## <a name="self-service-access-to-application-management"></a>Alkalmazás-felügyelet elérésének önkiszolgáló

Hozzávetőleges időt Complete: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Amely hozzáférést biztosít az alkalmazásokhoz fog igényelni a biztonsági csoport részeként Koncepció felhasználók azonosítása | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| Cél alkalmazása telepítve van. | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Ugrás a vállalati alkalmazások panel az Azure AD felügyeleti portál | [Az Azure AD felügyeleti portál: Vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Alkalmazás eltávolítása a szükséges Előfeltételek konfigurálása az önkiszolgáló szolgáltatással | [What's new in Azure Active Directory vállalati Alkalmazáskezelés: önkiszolgáló alkalmazás-hozzáférés konfigurálása](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Jelentkezzen be az információkkal dolgozó szakemberek számára az alkalmazások portál | http://myapps.microsoft.com |
| Figyelje meg, "+ alkalmazás hozzáadása" gombra az oldal op. Az alkalmazás elérésének segítségével |  |

### <a name="considerations"></a>Megfontolandó szempontok

A választott alkalmazások esetében kiépítési követelményeknek, ezért azonnal Ugrás az alkalmazás egyes hibákat okozhat. Ha a kiválasztott alkalmazás támogatja a létesítést az azure ad-val, és van-e konfigurálva, akkor előfordulhat, hogy használja ezt lehetőséget megjelenítése az egész folyamat végpont működik. Tekintse meg a építőeleme [SaaS összevont egyszeri Bejelentkezéses konfigurációs](#saas-federated-sso-configuration) további ajánlások

## <a name="self-service-password-reset"></a>Önkiszolgáló jelszóátállítás

Hozzávetőleges időt Complete: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A bérlői önkiszolgáló jelszó-kezelésen. | [Az Azure Active Directory-jelszó alaphelyzetbe állítása a informatikai rendszergazdák](active-directory-passwords-update-your-own-password.md) |
| Engedélyezze a jelszóvisszaírást a helyszíni jelszavak kezeléséhez. Megjegyzés: ehhez az adott Azure AD Connect verziók | [Jelszóvisszaírás előfeltételei](authentication/howto-sspr-writeback.md) |
| Ez a funkció, és győződjön meg arról, hogy egy biztonsági csoport tagjai koncepció felhasználók azonosítására. A felhasználók nem rendszergazdák számára, hogy a funkció teljes mértékben kihasználhatják kell lennie. | [Testreszabása: Az Azure AD a jelszókezelés: hozzáférés korlátozása a jelszó alaphelyzetbe állítása](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Navigáljon az Azure AD felügyeleti portálra: jelszó alaphelyzetbe állítása | [Azure AD felügyeleti portál: Jelszó alaphelyzetbe állítása](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Határozza meg, hogy a jelszó-visszaállítási házirend. Koncepció célokra használhatja a telefonhívás és a Q & a területen. Javasoljuk, hogy a hozzáférési panel való bejelentkezéshez szükséges regisztráció engedélyezése |  |
| Jelentkezzen ki, majd jelentkezzen be az Infomunkás |  |
| Adja meg az önkiszolgáló jelszó-változtatási adatok konfigurált / 2. lépés | https://aka.ms/ssprsetup |
| Zárja be a böngészőt. |  |
| Az Infomunkás a 4. lépésben használt, a bejelentkezési folyamat elindítása |  |
| A jelszó alaphelyzetbe állítása | [Frissítse a saját jelszavát: a jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) |
| Próbálja meg az új jelszót az Azure ad Szolgáltatásba, valamint hogy a helyszíni erőforrások bejelentkezik |  |

### <a name="considerations"></a>Megfontolandó szempontok

1. Súrlódás okozhatja az Azure AD Connect frissítése lesz, ha majd esetleg felhő fiókok ellen, vagy tegye a bemutató egy külön környezet ellen
2. A rendszergazdák egy másik házirendet és a jelszó alaphelyzetbe állításához a rendszergazda fiók használatával lehet, hogy a koncepció vágófelülettel és zavart okozhat. Ellenőrizze, hogy szabályos felhasználói fiók használata a visszaállítási műveletek tesztelése


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Az Azure multi-factor Authentication telefonhívást

Hozzávetőleges időt Complete: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Többtényezős Hitelesítést használó Koncepció felhasználók azonosítása  |  |
| A jó fogadása az MFA-kérdést Phone  | [Mi az az Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Navigáljon a "Felhasználók és csoportok" panel az Azure AD felügyeleti portál | [Az Azure AD felügyeleti portál: Felhasználók és csoportok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Válassza ki a "Minden felhasználó" panel |  |
| A felső menüsoron válasszon "Többtényezős hitelesítés" gombra a | Közvetlen URL-címe Azure MFA-portálon: https://aka.ms/mfaportal |
| A "User" beállításainál jelölje ki a koncepció felhasználókat, és lehetővé teszi a multi-factor Authentication | [Azure Multi-Factor Authentication – felhasználói állapotok](authentication/howto-mfa-userstates.md) |
| Jelentkezzen be a koncepció felhasználó, és a lépésein végighaladva a igazolása létrehozása folyamatban  |  |

### <a name="considerations"></a>Megfontolandó szempontok

1. Ez az explicit módon beállítása MFA egy felhasználó összes bejelentkezések építőelem koncepció lépéseit. Például a feltételes hozzáférés és Identity Protection egyéb eszközöket, amelyek több többtényezős Hitelesítést végezhetnek megcélzott forgatókönyvek. Ez lehet valami kell figyelembe venni, amikor Koncepció üzemi környezetben.
2. Ez építőelem koncepció lépéseit explicit módon használ telefonhívásokat expedience a többtényezős hitelesítési módszerként. Áttérés POC üzemi környezetben, mert azt javasoljuk, alkalmazások, mint a [Microsoft Authenticator](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) a második tényezőként amikor csak lehetséges.
További: [VÁZLAT NIST különleges közlemény 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>Többtényezős hitelesítés feltételes hozzáférés a Szolgáltatottszoftver-alkalmazáshoz

Hozzávetőleges időt Complete: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Azonosítsa a koncepció felhasználók számára a szabályzatok célzásához. Ezek a felhasználók egy biztonsági csoportot a feltételes hozzáférési szabályzat hatóköre kell lennie | [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| SaaS-alkalmazás már konfigurálva |  |
| Koncepció felhasználók már hozzá van rendelve az alkalmazáshoz |  |
| A Koncepció felhasználói hitelesítő adatok érhetők el |  |
| Ez a felhasználó a multi-factor Authentication regisztrálva van. A telefon használata jó fogadása | https://aka.ms/ssprsetup |
| A belső hálózati eszköz. A belső címtartomány konfigurált IP-cím | Keresse meg az IP-címe: https://www.bing.com/search?q=what%27s+my+ip |
| A külső hálózathoz (a telefon, a vivőjel mobil hálózat használatával lehet) eszköz |  |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Nyissa meg az Azure AD felügyeleti portálra: feltételes hozzáférési panel | [Az Azure AD felügyeleti portál: Feltételes hozzáférés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Feltételes hozzáférési szabályzat létrehozása:<br/>-A cél koncepció felhasználók "Felhasználók és csoportok"<br/>-Koncepció célalkalmazás "Felhőalapú alkalmazások" alatt<br/>-Az összes hely cél kivételével megbízható ők "Feltételek" a "Helyek" -> **Megjegyzés:** megbízható IP-címek vannak konfigurálva a [MFA-portálon](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-"Grant" a többtényezős hitelesítés megkövetelése | [Feltételes hozzáférés az Azure Active Directoryban az első lépései: a házirend-konfigurációs lépések](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Hozzáférés a vállalati hálózaton belüli alkalmazás | [Feltételes hozzáférés az Azure Active Directoryban az első lépései: a házirend tesztelése](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Nyilvános hálózat hozzáférés alkalmazás | [Feltételes hozzáférés az Azure Active Directoryban az első lépései: a házirend tesztelése](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Megfontolandó szempontok

Összevonási használ, ha a helyszíni identitásszolgáltató (IdP) segítségével JOGCÍMEKKEL rendelkező és a vállalati hálózat belső/külső állapot kommunikációhoz. Ezzel a módszerrel nem kell az IP-címek, elképzelhető, hogy értékeléséhez és kezeléséhez a nagy méretű szervezeteknek összetett kezelését is használhatja. A telepítő által kell a "hálózati központi" forgatókönyv (olyan felhasználó, a belső hálózatról, és közben a kapcsolók naplózás helyeken, például egy kávézóban) fiókot, és győződjön meg arról, hogy a megvalósítását. További: [Securing felhőalapú erőforrásokat az Azure többtényezős hitelesítés és az AD FS: megbízható IP-címeinek összevont felhasználók](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Hozzávetőleges időt Complete: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A globális rendszergazda, amely a PIM szolgáltatásra a Koncepció része lesz azonosítása | [Azure AD Privileged Identity Management használatának megkezdése](active-directory-privileged-identity-management-getting-started.md) |
| A globális rendszergazdai lesz a biztonsági rendszergazda azonosítása | [Azure AD Privileged Identity Management használatának megkezdése](active-directory-privileged-identity-management-getting-started.md)<br/> [Az Azure Active Directory PIM különböző rendszergazdai szerepkörei](active-directory-privileged-identity-management-roles.md) |
| Választható lehetőség: Győződjön meg róla, ha a globális rendszergazdák rendelkezik-e e-mailek elérését a PIM értesítő e-mailek megadásával | [Mi az Azure AD Privileged Identity Management?: a szerepkör-aktiválási beállításainak konfigurálása](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Jelentkezzen be https://portal.azure.com egy globális rendszergazda (GA) és a rendszerindítási a PIM panelen. A globális rendszergazda hajtja végre ezt a lépést a biztonsági rendszergazda van rendezés.  Az aktor GA1 most hívása | [Az Azure AD Privileged Identity Management biztonsági varázslóval](active-directory-privileged-identity-management-security-wizard.md) |
| Azonosítsa a globális rendszergazda, és helyezze azokat a végleges jogosult. Egy különálló rendszergazdai az egyértelműség érdekében az 1. lépésben használt legyen. Az aktor GA2 most hívása | [Az Azure AD Privileged Identity Management: Hogyan lehet hozzáadni vagy eltávolítani egy felhasználói szerepkör](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Mi az Azure AD Privileged Identity Management?: a szerepkör-aktiválási beállításainak konfigurálása](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Most, jelentkezzen be a GA2 https://portal.azure.com , és próbálja meg módosítani a "Felhasználói beállítások". Figyelje meg, több lehetősége is használhatók. | |
| Új lapon, és ugyanabban a munkamenetben, mint 3. lépés, keresse meg most https://portal.azure.com , és adja hozzá a PIM panelen az irányítópult megnyitásához. | [Aktiválása vagy inaktiválása szerepkörök az Azure AD Privileged Identity Management: a Privileged Identity Management alkalmazás felvétele](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| A globális rendszergazdai szerepkörrel aktiválási kérelmeinek megadása | [Aktiválása vagy inaktiválása szerepkörök az Azure AD Privileged Identity Management: a szerepkör aktiválása](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Vegye figyelembe, hogy ha GA2 soha nem iratkozott fel az MFA szolgáltatásra, az Azure MFA regisztrációs szükség lesz |  |
| Lépjen vissza a 3. lépésben az eredeti fülre, és a böngésző Frissítés gombjára. Vegye figyelembe, hogy most már hozzáférhet "Felhasználói beállítások" módosítása | |
| Nem kötelező Ha a globális rendszergazdák e-mailek engedélyezve van, ellenőrizheti GA1 és GA2 a Beérkezett fájlok és az értesítés a szerepkör aktiválása folyamatban |  |
| 8 ellenőrizze a naplózási előzmények, és figyelje meg a jelentés a jogok kiterjesztésének GA2 megerősítéséhez. | [Mi az Azure AD Privileged Identity Management?: tekintse át a szerepkör-tevékenység](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Megfontolandó szempontok

Ez a funkció az Azure AD Premium P2 és/vagy az EMS E5 része

## <a name="discovering-risk-events"></a>Kockázati események felderítéséhez

Hozzávetőleges időt Complete: 20 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Tor böngészővel rendelkező eszköz letöltése és telepítése | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Koncepció felhasználó annak a bejelentkezési elérésére | [Az Azure Active Directory Identity Protection-forgatókönyv](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Nyissa meg a tor-böngésző | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Jelentkezzen be https://myapps.microsoft.com Koncepció a felhasználói fiókkal | [Az Azure Active Directory Identity Protection-forgatókönyv: szimulálva kockázati események](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Várjon 5-7 percet |  |
| Jelentkezzen be egy globális rendszergazdának kell https://portal.azure.com és az Identity Protection panel megnyitása | https://aka.ms/aadipgetstarted |
| A kockázati események panel megnyitásához. Egy "Bejelentkezéseket névtelen IP-címekről" bejegyzést kell megjelennie  | [Az Azure Active Directory Identity Protection-forgatókönyv: szimulálva kockázati események](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Megfontolandó szempontok

Ez a funkció az Azure AD Premium P2 és/vagy az EMS E5 része

## <a name="deploying-sign-in-risk-policies"></a>Bejelentkezés a kockázat házirendek

Hozzávetőleges időt Complete: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Tor böngészővel rendelkező eszköz letöltése és telepítése | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Hozzáférés a bejelentkezést a tesztelés végrehajtásához Koncepció felhasználóként |  |
| Ez a felhasználó többtényezős hitelesítéssel regisztrálva van. Ügyeljen arra, hogy a telefon használata jó fogadása | Építőelem: [telefonhívásokat az Azure többtényezős hitelesítés](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Jelentkezzen be egy globális rendszergazdának kell https://portal.azure.com és az Identity Protection paneljének megnyitásához | https://aka.ms/aadipgetstarted |
| A kockázat bejelentkezési házirend engedélyezése az alábbiak szerint:<br/>-Rendelt: Koncepció felhasználó<br/>– Feltételek: Bejelentkezési kockázat közepes vagy újabb rendszer (bejelentkezés névtelen helyről akkor számít elértnek, egy közepes méretű kockázati szint)<br/>-Vezérlők: Többtényezős hitelesítés megkövetelése | [Az Azure Active Directory Identity Protection-forgatókönyv: bejelentkezési kockázata](active-directory-identityprotection-playbook.md) |
| Nyissa meg a tor-böngésző | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Jelentkezzen be https://myapps.microsoft.com koncepció a felhasználói fiókkal |  |
| Figyelje meg, az MFA-kérdést | [Bejelentkezés során lép az Azure AD Identity Protection: kockázatos bejelentkezési helyreállítási](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Megfontolandó szempontok

Ez a funkció az Azure AD Premium P2 és/vagy az EMS E5 részét képezi. Kockázati események olvashat további: [Azure Active Directory kockázati események](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>A Tanúsítványalapú hitelesítés konfigurálása

Hozzávetőleges időt igényel: 20 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Eszköz felhasználói tanúsítvánnyal kiépítve (Windows, iOS vagy Android) a vállalati nyilvános kulcsú infrastruktúra | [Felhasználói tanúsítványok telepítése](https://msdn.microsoft.com/library/cc770857.aspx) |
| Az AD FS összevont Azure AD-tartomány | [Azure AD Connect és összevonás](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Active Directory tanúsítványszolgáltatások áttekintése](https://technet.microsoft.com/library/hh831740.aspx)|
| Az iOS-eszközök telepítve a Microsoft Authenticator alkalmazás | [Ismerkedés a Microsoft Authenticator alkalmazással](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| "Tanúsítvány-hitelesítés" az AD FS engedélyezése | [Hitelesítési házirendek konfigurálása: Az elsődleges hitelesítés globálisan konfigurálása a Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Választható: Engedélyezze a Tanúsítványalapú hitelesítés az Azure AD-ban az Exchange ActiveSync-ügyfelek | [A tanúsítványalapú hitelesítés első lépései az Azure Active Directoryban](active-directory-certificate-based-authentication-get-started.md) |
| Navigáljon a hozzáférési panelre, és a hitelesítés a felhasználói tanúsítvány használatával | https://myapps.microsoft.com |

### <a name="considerations"></a>Megfontolandó szempontok

A központi telepítés figyelmeztetések olvashat további: [az AD FS: Azure AD-val tanúsítványhitelesítés & Office 365](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Felhasználói tanúsítvány birtokában kell védeni. Vagy eszközök kezelése esetén az intelligens kártyák PIN-kóddal.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
