---
title: Az Azure Active Directory forgatókönyv építőelemei koncepció igazolása |} A Microsoft Docs
description: Ismerje meg, és gyorsan az identitás és hozzáférés-kezelési forgatókönyvek megvalósítása
services: active-directory
keywords: az Azure active directory, a forgatókönyv, a koncepció igazolása, megvalósíthatósági vizsgálat
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
ms.openlocfilehash: c5f3904621dcc4fe992b2c2f8293ad706b01f713
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446775"
---
# <a name="azure-active-directory-proof-of-concept-playbook-building-blocks"></a>Az Azure Active Directory megvalósíthatósági fogalom forgatókönyv: építőelemek

## <a name="catalog-of-roles"></a>A szerepkörök katalógus

| Szerepkör | Leírás | Megvalósíthatósági próbamegoldásoktól feladata |
| --- | --- | --- |
| **Identitás-architektúra és fejlesztői csapat** | Ehhez a csapathoz általában azt, amelyik tervez a megoldás, prototípusok valósítja meg, jóváhagyások-meghajtók és véglegesen átadja az műveletek | A környezeteket nyújtanak, és azokat az eseteket, kezelhetőség szempontjából kipróbálja a |
| **A helyszíni identitás üzemeltetési csapat** | Kezeli a különböző identitás helyszíni adatforrásaihoz: Active Directory-erdők, LDAP-címtárak, HR-rendszerekkel és összevonási identitás-szolgáltatóktól. | A helyszíni hozzáférést biztosítanak a PoC-forgatókönyvhöz szükséges erőforrásokat.<br/>Akkor be kell vonni rendszerműveletekre|
| **Technikai Alkalmazástulajdonosok** | A különböző felhőalapú alkalmazások és szolgáltatások, amelyek az Azure ad-vel integrálni műszaki tulajdonosai | Adja meg az adatokat az SaaS-alkalmazásokra (vélhetően tesztelési példányok) |
| **Az Azure AD globális rendszergazda** | Az Azure ad-ben konfigurációjának kezelése | Adjon meg hitelesítő adatokat konfigurálja a szinkronizálási szolgáltatást. Általában azonos csapat Identitásarchitektúra, megvalósíthatósági vizsgálat során, de a műveletek fázis során külön|
| **Database csapata** | Az adatbázis-infrastruktúra tulajdonosa | SQL-környezet (AD FS vagy az Azure AD Connect) hozzáférést biztosítanak az adott forgatókönyv felkészüléshez.<br/>Akkor be kell vonni rendszerműveletekre |
| **Hálózati** | A hálózati infrastruktúra tulajdonosok | Adja meg a szükséges hozzáférést a hálózati szinten, a szinkronizálási kiszolgálók megfelelően az adatforrást, és felhőalapú szolgáltatásaiba (tűzfalszabályokat, megnyitott portok, IPSec-szabályok stb.) |
| **Biztonsági csapat** | A biztonsági stratégia meghatározása, elemzi a különféle forrásokból származó biztonsági jelentések és a megállapításokat követi. | Adja meg a cél biztonsági értékelés forgatókönyvek |

## <a name="common-prerequisites-for-all-building-blocks"></a>Az összes építőelemeket általános Előfeltételek

Az alábbiakban néhány bármely POC-t az Azure AD Premium szükséges előfeltételeket.

| Előfeltétel | További források |
| --- | --- |
| Az Azure AD bérlő által definiált érvényes Azure-előfizetéssel | [Az Azure Active Directory-bérlő beszerzése](active-directory-howto-tenant.md)<br/>**Megjegyzés:** Ha már rendelkezik prémium szintű Azure AD-licenceket tartalmazó környezet, nulla cap előfizetés beszerezheti az https://aka.ms/accessaad <br/>További információt: https://blogs.technet.microsoft.com/enterprisemobility/2016/02/26/azure-ad-mailbag-azure-subscriptions-and-azure-ad-2/ és https://technet.microsoft.com/library/dn832618.aspx |
| Meghatározott és ellenőrzött tartományok | [Egyéni tartománynév hozzáadása az Azure Active Directoryhoz](active-directory-domains-add-azure-portal.md)<br/>**Megjegyzés:** egyes számítási feladatokhoz, mint a Power BI sikerült kiépítve a takaró alatt egy azure AD-bérlővel. Ellenőrizze, hogy egy bérlő társítva-e egy adott tartományon, navigáljon a https://login.microsoftonline.com/{domain}/v2.0/.well-known/openid-configuration. Ha sikeres válasz lekérése, majd a tartomány már hozzá van rendelve a bérlőhöz, és vegye át lehet szükség. Ha igen, forduljon a Microsofthoz további útmutatást tartalmaz. További információ az átvétel lehetőség is: [Mi az az Azure önkiszolgáló regisztráció?](active-directory-self-service-signup.md) |
| Az Azure AD Premium vagy EMS-próba engedélyezve | [Az Azure Active Directory Premium egy hónapig ingyenesen használható](https://azure.microsoft.com/trial/get-started-active-directory/) |
| Azure AD Premium vagy EMS-licenceket rendelt PoC-felhasználók | [Saját magának, mind pedig a felhasználók az Azure Active Directoryban](active-directory-licensing-get-started-azure-portal.md) |
| Az Azure AD globális rendszergazdai hitelesítő adatait | [Rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](users-groups-roles/directory-assign-admin-roles.md) |
| Nem kötelező, de erősen ajánlott: tartalékként párhuzamos laborkörnyezetben | [Az Azure AD Connect előfeltételei](./connect/active-directory-aadconnect-prerequisites.md) |

## <a name="directory-synchronization---password-hash-sync-phs---new-installation"></a>Címtár szinkronizálási - Jelszókivonat-szinkronizálás (nál) – új telepítés

Hozzávetőleges időt Complete: kevesebb mint 1000 PoC-felhasználók számára egy óra

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Server futtatása az Azure AD Connect | [Az Azure AD Connect előfeltételei](./connect/active-directory-aadconnect-prerequisites.md) |
| POC-felhasználóknak a ugyanabban a tartományban, és a egy biztonsági csoportot, és a szervezeti egység részét | [Az Azure AD Connect testreszabott telepítése](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) |
| Az Azure AD Connect-funkciók a POC szükséges azonosítva. | [Active Directory csatlakoztatása az Azure Active Directory - szinkronizálás beállítása funkciók](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Ön rendelkezik szükséges hitelesítő adatokat a helyszíni és felhőalapú környezetekről  | [Azure AD Connect: fiókok és engedélyek](./connect/active-directory-aadconnect-accounts-permissions.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Az Azure AD Connect legújabb verziójának letöltése | [A Microsoft Azure Active Directory Connect letöltése](https://www.microsoft.com/download/details.aspx?id=47594) |
| Az Azure AD Connect telepítése a legegyszerűbb elérési úttal rendelkező: Express <br/>1. A cél a szinkronizálási ciklus idő minimalizálása érdekében a szervezeti egység szűrése<br/>2. Válassza ki a cél beállítása a felhasználók a helyi csoport.<br/>3. A másik POC témák számára szükséges szolgáltatások telepítése | [Az Azure AD Connect: Testreszabott telepítés: tartomány és szervezeti egységek szűrése](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) <br/>[Az Azure AD Connect: Testreszabott telepítés: csoport alapú szűrés](./connect/active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups)<br/>[Az Azure AD Connect: Helyszíni identitások integrálása az Azure Active Directory: a szinkronizálási funkciók konfigurálása](./connect/active-directory-aadconnect.md#configure-sync-features) |
| Nyissa meg az Azure AD Connect felhasználói felületén, és a futó profilok befejezett (importálás, szinkronizálási és exportálás) | [Az Azure AD Connect szinkronizálása: ütemező](./connect/active-directory-aadconnectsync-feature-scheduler.md) |
| Nyissa meg a [Azure AD felügyeleti portálra](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/), nyissa meg a "Minden felhasználó" panelen, a "Forrás-hitelesítésszolgáltató" oszlopra, és láthatja, hogy a felhasználók jelennek meg, a "Windows Server AD" hamarosan megfelelően megjelölve hozzáadása | [Az Azure AD felügyeleti portálra](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) |

### <a name="considerations"></a>Megfontolandó szempontok

1. Tekintse meg a biztonsági szempontok a Jelszókivonat-szinkronizálás [Itt](./connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).  Ha Jelszókivonat-szinkronizálás próbaüzem üzem felhasználók véglegesen nem egy beállítást, vegye figyelembe a következő lehetőségeket:
   * Tesztfelhasználók létrehozása az éles tartományhoz. Ellenőrizze, hogy ne legyen szinkronizálás a bármelyik más fiókhoz
   * Foglalja annak környezet áthelyezése
2.  Ha vissza szeretné szerezni a összevonási, érdemes a költségek a helyszíni identitásszolgáltató túl a POC egy összevont megoldás társított és mérhetők, amely az Ön által keresett előnyöket:
    * Van a kritikus fontosságú elérési útról így kell magas rendelkezésre állás tervezése
    * Egy kapacitás-csomagot kell a helyszíni szolgáltatás
    * Egy figyelő/karbantartása és javítást kell a helyszíni szolgáltatás

További információ: [ismertetése az Office 365 identitás- és Azure Active Directory - összevont identitás](https://support.office.com/article/Understanding-Office-365-identity-and-Azure-Active-Directory-06a189e7-5ec6-4af2-94bf-a22ea225a7a9#bk_federated)

## <a name="branding"></a>Védjegyzési

Hozzávetőleges teljes ideje: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Objektumok (képek, emblémák, stb.); A legjobb vizualizációt, ellenőrizze, hogy az eszközök rendelkeznek az ajánlott méreteket. | [Vállalati arculat megjelenítése a bejelentkezési oldal az Azure Active Directoryban](active-directory-branding-custom-signon-azure-portal.md) |
| Nem kötelező: Ha a környezetben egy AD FS-kiszolgáló, a hozzáférést a kiszolgáló számára webes témák testreszabása | [AD FS felhasználók bejelentkezésének testreszabása](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/ad-fs-user-sign-in-customization) |
| Hajtsa végre a végfelhasználói bejelentkezési élmény arra a számítógépre |  |
| Választható lehetőség: A mobileszközök számára tapasztalat érvényesítése |  |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Az Azure AD felügyeleti portál megnyitása | [Az Azure AD felügyeleti portál – vállalati arculat megjelenítése](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/LoginTenantBranding) |
| Töltse fel az eszközök számára a bejelentkezési oldal (fő kép embléma, kis méretű embléma, címkék, stb.). Igény szerint, ha az AD FS igazítása ugyanazokat az eszközöket az AD FS bejelentkezési oldalak | [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési Panel oldalon: testreszabható elemek](fundamentals/customize-branding.md) |
| Várjon néhány percig, a módosítás teljes körű érvénybe léptetéséhez |  |
| Jelentkezzen be a POC felhasználói hitelesítő adatok a https://myapps.microsoft.com |  |
| Erősítse meg a böngészőben megjelenését és működését | [Vállalati arculat megjelenítése a bejelentkezési és a hozzáférési Panel oldalon](fundamentals/customize-branding.md) |
| Szükség esetén ellenőrizze a megjelenését és működését az egyéb eszközök |  |

### <a name="considerations"></a>Megfontolandó szempontok

Ha a régi megjelenését és működését a testreszabást követően továbbra is a böngésző ügyfél gyorsítótárának ürítése, és próbálja megismételni a műveletet.

## <a name="group-based-licensing"></a>Csoport alapú licencelés

Hozzávetőleges teljes ideje: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Minden POC-felhasználók biztonsági csoport (felhőbeli vagy helyszíni) részét képezik. | [Hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](fundamentals/active-directory-groups-create-azure-portal.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Licencek az Azure AD felügyeleti portál paneljéről | [Az Azure AD felügyeleti portálra: licencelése](https://portal.azure.com/#blade/Microsoft_AAD_IAM/LicensesMenuBlade/Products) |
| A licencek hozzárendelése a biztonsági csoport a POC-felhasználókkal. | [Licencek hozzárendelése az Azure Active Directory felhasználók egy csoportja](active-directory-licensing-group-assignment-azure-portal.md) |

### <a name="considerations"></a>Megfontolandó szempontok

Bármely felmerülő problémák Ugrás [forgatókönyvek, korlátozások és csoportok használata kezelheti a licencelése az Azure Active Directory szolgáltatással kapcsolatos ismert problémák](active-directory-licensing-group-advanced.md)

## <a name="saas-federated-sso-configuration"></a>SaaS-összevont egyszeri bejelentkezés konfigurálása

Hozzávetőleges időt Complete: 60 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A tesztkörnyezet a SaaS-alkalmazás érhető el. Ez az útmutató a ServiceNow használjuk példaként.<br/>Javasoljuk, hogy minimalizálja a meglévő adatok minőségét és a leképezések irányításához fennakadások nélkül használható egy test-példány használatával. | Lépjen a https://developer.servicenow.com/app.do#! / home egy teszt példány folyamatának elindításához |
| A ServiceNow felügyeleti konzolra a rendszergazdai hozzáférés | [Oktatóanyag: Azure Active Directory-integráció a servicenow segítségével](saas-apps/servicenow-tutorial.md) |
| Cél beállítása a felhasználók az alkalmazást hozzárendelni. A PoC-felhasználókat tartalmazó biztonsági csoport használata javasolt. <br/>Ha a a csoport létrehozásakor nem megvalósítható, hozzárendelheti a felhasználóknak, hogy közvetlenül az alkalmazás a koncepció | [Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Az oktatóanyag összes szereplő Microsoft Documentation megosztása  | [Oktatóanyag: Azure Active Directory-integráció a servicenow segítségével](saas-apps/servicenow-tutorial.md) |
| Egy működő értekezlet beállítása, és kövesse az oktatóanyag lépéseit az egyes színész. | [Oktatóanyag: Azure Active Directory-integráció a servicenow segítségével](saas-apps/servicenow-tutorial.md) |
| Az előfeltételeket a csoporttal rendelni az alkalmazást. A POC feltételes hozzáféréssel rendelkezik a hatókörben, ha újra állítani, hogy később, és adja hozzá a többtényezős hitelesítés, és a hasonló. <br/>Vegye figyelembe, ez lesz pillanattal a kiépítési folyamat (Ha be van állítva) |  [Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md) <br/>[Hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](fundamentals/active-directory-groups-create-azure-portal.md) |
| ServiceNow-alkalmazás hozzáadása a katalógusból az Azure AD felügyeleti portál használatával| [Az Azure AD felügyeleti portálra: vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/Overview) <br/>[Vállalati alkalmazások kezelése az Azure Active Directory újdonságai](active-directory-enterprise-apps-whats-new-azure-portal.md) |
| "ServiceNow-alkalmazás"Egyszeri bejelentkezés"panel SAML-alapú bejelentkezés engedélyezése" |  |
| Töltse ki a "Bejelentkezési URL-cím" és "Azonosító" mezőket a ServiceNow URL-címmel<br/>Az "Új tanúsítvány aktívvá tétele" jelölőnégyzetet<br/>és a beállítások mentése |  |
| Nyissa meg a ServiceNow konfigurálását egyénileg kialakított utasításokat megtekintéséhez a panel alján lévő "Konfigurálás a ServiceNow" panel |  |
| Kövesse az utasításokat a ServiceNow konfigurálása |  |
| ServiceNow-alkalmazás "Kiépítés" panel "Automatikus" kiépítés engedélyezése | [Az új Azure Portalon a vállalati alkalmazások kiépítése a felhasználói fiók kezelése](manage-apps/configure-automatic-user-provisioning-portal.md) |
| Várjon néhány percet, amíg a kiépítés befejezése.  Addig is a kiépítési jelentéseket ellenőrzéséhez |  |
| Jelentkezzen be https://myapps.microsoft.com/ teszt felhasználóként, amely hozzáféréssel rendelkezik | [Mi az a hozzáférési panelen?](active-directory-saas-access-panel-introduction.md) |
| Kattintson a csempére az újonnan létrehozott alkalmazás. Hozzáférés ellenőrzése |  |
| Igény szerint megtekinthető az alkalmazás használati jelentéseket. Vegye figyelembe, hogy van néhány késést, ezért meg kell várnia a forgalmat a jelentések megjelenítéséhez egy kis idő. | [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon: felügyelt alkalmazások használati adatai](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Az Azure Active Directory jelentésmegőrzési házirendje](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Megfontolandó szempontok

1. Fent [oktatóanyag](saas-apps/servicenow-tutorial.md) hivatkozik a régi Azure AD-kezelést. A PoC alapul, de [rövid](active-directory-enterprise-apps-whats-new-azure-portal.md#quickstart-get-going-with-your-new-application-right-away) tapasztalható.
2. Ha a célként megadott alkalmazás nem szerepel a katalógusban, majd használhatja "Használata a saját alkalmazás". További információ: [vállalati alkalmazások kezelése az Azure Active Directory újdonságai: Adja hozzá az egyéni alkalmazások egy helyen](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

## <a name="saas-password-sso-configuration"></a>SaaS-jelszó egyszeri bejelentkezés konfigurálása

Hozzávetőleges teljes ideje: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A tesztkörnyezetben, az SaaS-alkalmazásokhoz. Egy Egyszeri jelszót példája HipChat és a Twitter. Minden olyan alkalmazást szüksége lesz a pontos URL-címe a lap html-bejelentkezési űrlapot. | [Twitter-a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[A Microsoft Azure Marketplace-en HipChat](https://azuremarketplace.microsoft.com/marketplace/apps/aad.hipchat) |
| Tesztelni az alkalmazások számára. | [Feliratkozás a Twitteren](https://twitter.com/signup?lang=en)<br/>[Regisztráljon ingyenesen: HipChat](https://www.hipchat.com/sign_up) |
| Cél beállítása a felhasználók az alkalmazást hozzárendelni. Egy biztonsági csoportot tartalmazza a felhasználóknak ajánlott. | [Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md) |
| A hozzáférési Panel bővítmény telepítését az Internet Explorer, a Chrome és a Firefox egy számítógép helyi rendszergazdai hozzáféréssel | [Hozzáférési Panel az Internet Explorer – bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel kiterjesztése a Chrome-ban](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel kiterjesztése a Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A webböngésző-bővítmény telepítése | [Hozzáférési Panel az Internet Explorer – bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel kiterjesztése a Chrome-ban](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel kiterjesztése a Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Alkalmazás-galériából konfigurálása | [Újdonságok a vállalati alkalmazások kezelése az Azure Active Directory: az új és továbbfejlesztett alkalmazáskatalógusában](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Jelszavas egyszeri Bejelentkezést konfigurálása | [Egyszeri bejelentkezés a vállalati alkalmazásokat az új Azure Portalon kezelése: jelszóalapú bejelentkezés](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Az alkalmazás hozzárendelése az előfeltételeket a csoporttal | [Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md) |
| Jelentkezzen be https://myapps.microsoft.com/ teszt felhasználóként, amely hozzáféréssel rendelkezik |  |
| Kattintson a csempére az újonnan létrehozott alkalmazás. | [Mi az a hozzáférési panelen?: jelszóalapú egyszeri bejelentkezés identitás üzembe helyezése nélkül](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Adja meg az hitelesítő adatai | [Mi az a hozzáférési panelen?: jelszóalapú egyszeri bejelentkezés identitás üzembe helyezése nélkül](active-directory-saas-access-panel-introduction.md#password-based-sso-without-identity-provisioning) |
| Zárja be a böngészőt, majd ismételje meg a bejelentkezés. A felhasználó ezen idő körülbelül kell megjelennie zökkenőmentes hozzáférést az alkalmazáshoz. |  |
| Igény szerint megtekinthető az alkalmazás használati jelentéseket. Vegye figyelembe, hogy van néhány késést, ezért meg kell várnia a forgalmat a jelentések megjelenítéséhez egy kis idő. | [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon: felügyelt alkalmazások használati adatai](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Az Azure Active Directory jelentésmegőrzési házirendje](active-directory-reporting-retention.md) |

### <a name="considerations"></a>Megfontolandó szempontok

Ha a célként megadott alkalmazás nem szerepel a katalógusban, majd használhatja "Használata a saját alkalmazás". További információ: [vállalati alkalmazások kezelése az Azure Active Directory újdonságai: Adja hozzá az egyéni alkalmazások egy helyen](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tartsa szem előtt az alábbi követelményeknek:
   * Alkalmazásoknak rendelkezniük kell egy ismert bejelentkezési URL-címe
   * A bejelentkezési lap HTML-űrlapból az egy vagy több szöveges mező, amely a böngészőbővítmények auto-töltheti fel kell tartalmaznia. Mindenképpen felhasználónevet és jelszót kell tartalmaznia.

## <a name="saas-shared-accounts-configuration"></a>SaaS a megosztott fiókok konfigurációja

Hozzávetőleges teljes ideje: 30 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A lista célalkalmazások és a pontos bejelentkezési URL-CÍMEK előre. Tegyük fel a Twitter is használhatja. | [Twitter-a Microsoft Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps/aad.twitter)<br/>[Feliratkozás a Twitteren](https://twitter.com/signup?lang=en) |
| Az SaaS-alkalmazáshoz tartozó hitelesítő adatok megosztott. | [Az Azure AD-vel fiókok megosztása](active-directory-sharing-accounts.md)<br/>[Az azure AD automatikus jelszó vihetők át a Facebook, a Twitter és a LinkedIn jelenleg előzetes verzióban elérhető! – Enterprise Mobility and Security blogon] (https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/ ) |
| Hitelesítő adatok legalább két, ugyanazzal a fiókkal hozzáférő csapattagok számára. Biztonsági csoport részét kell lenniük. | [Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md) |
| A hozzáférési Panel bővítmény telepítését az Internet Explorer, a Chrome és a Firefox egy számítógép helyi rendszergazdai hozzáféréssel | [Hozzáférési Panel az Internet Explorer – bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel kiterjesztése a Chrome-ban](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel kiterjesztése a Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A webböngésző-bővítmény telepítése | [Hozzáférési Panel az Internet Explorer – bővítmény](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)<br/>[Hozzáférési Panel kiterjesztése a Chrome-ban](https://go.microsoft.com/fwLink/?LinkID=311859&clcid=0x409)<br/>[Hozzáférési Panel kiterjesztése a Firefox](https://go.microsoft.com/fwLink/?LinkID=626998&clcid=0x409) |
| Alkalmazás-galériából konfigurálása | [Újdonságok a vállalati alkalmazások kezelése az Azure Active Directory: az új és továbbfejlesztett alkalmazáskatalógusában](active-directory-enterprise-apps-whats-new-azure-portal.md#improvements-to-the-azure-active-directory-application-gallery) |
| Jelszavas egyszeri Bejelentkezést konfigurálása | [Egyszeri bejelentkezés a vállalati alkalmazásokat az új Azure Portalon kezelése: jelszóalapú bejelentkezés](manage-apps/configure-single-sign-on-portal.md#password-based-sign-on) |
| Rendelje hozzá az alkalmazás a hitelesítő adatokkal való hozzárendelése közben az előfeltételeket csoporttal | [Egy felhasználó vagy csoport hozzárendelése az Azure Active Directory vállalati alkalmazás](manage-apps/assign-user-or-group-access-portal.md) |
| Jelentkezzen be másik felhasználóként, hogy hozzáférési alkalmazást a **ugyanazt a közös fiókot.**  |  |
| Igény szerint megtekinthető az alkalmazás használati jelentéseket. Vegye figyelembe, hogy van néhány késést, ezért meg kell várnia a forgalmat a jelentések megjelenítéséhez egy kis idő. | [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon: felügyelt alkalmazások használati adatai](active-directory-reporting-activity-sign-ins.md#usage-of-managed-applications)<br/>[Az Azure Active Directory jelentésmegőrzési házirendje](active-directory-reporting-retention.md) |


### <a name="considerations"></a>Megfontolandó szempontok

Ha a célként megadott alkalmazás nem szerepel a katalógusban, majd használhatja "Használata a saját alkalmazás". További információ: [vállalati alkalmazások kezelése az Azure Active Directory újdonságai: Adja hozzá az egyéni alkalmazások egy helyen](active-directory-enterprise-apps-whats-new-azure-portal.md#add-custom-applications-from-one-place)

 Tartsa szem előtt az alábbi követelményeknek:
   * Alkalmazásoknak rendelkezniük kell egy ismert bejelentkezési URL-címe
   * A bejelentkezési lap HTML-űrlapból az egy vagy több szöveges mező, amely a böngészőbővítmények auto-töltheti fel kell tartalmaznia. Mindenképpen felhasználónevet és jelszót kell tartalmaznia.

## <a name="app-proxy-configuration"></a>Alkalmazás-Proxy konfigurálása

Hozzávetőleges teljes ideje: 20 perc alatt

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Egy Microsoft Azure AD basic vagy Premium szintű előfizetéssel és Azure AD-címtárral, amelyhez Ön globális rendszergazda | [Az Azure Active Directory-kiadások](fundamentals/active-directory-whatis.md) |
| Egy webalkalmazás üzemeltetett helyben, hogy azt szeretné, hogy a távoli hozzáféréshez |  |
| A Windows Server 2012 R2 vagy Windows 8.1 vagy újabb rendszert futtató kiszolgáló, amelyre telepítheti az alkalmazásproxy-összekötő | [Az Azure AD-alkalmazásproxy-összekötők ismertetése](manage-apps/application-proxy-connectors.md) |
| Ha az elérési út egy tűzfal található, győződjön meg arról, hogy meg nyitva, hogy az összekötő teheti a HTTPS (TCP-) kéréseket az alkalmazásproxynak | [Alkalmazásproxy engedélyezése az Azure Portal: az alkalmazásproxy-Előfeltételek](manage-apps/application-proxy-enable.md#application-proxy-prerequisites) |
| Ha a szervezete proxykiszolgálóval csatlakozik az internethez, hajtsa végre a megfelelő a blog egy pillantást a meglévő helyszíni proxykiszolgálók konfigurálásának őket részletes működő közzététele | [Meglévő helyszíni proxykiszolgálók használata](manage-apps/application-proxy-configure-connectors-with-proxy-servers.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Összekötő telepítése a kiszolgálón | [Alkalmazásproxy engedélyezése az Azure Portal: az összekötő regisztrálása és telepítése](manage-apps/application-proxy-enable.md#install-and-register-a-connector) |
| A helyszíni alkalmazás közzététele az Azure AD alkalmazásproxy-alkalmazásként | [Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával](manage-apps/application-proxy-publish-azure-portal.md) |
| Rendelje hozzá a felhasználók tesztelése | [Alkalmazások közzététele az Azure AD-alkalmazásproxy használatával: egy tesztfelhasználót hozzáadása](manage-apps/application-proxy-publish-azure-portal.md#add-a-test-user) |
| Ha kívánja konfigurálhat egy egyszeri bejelentkezéses felhasználói élmény | [Az Azure AD-alkalmazásproxy egyszeri bejelentkezést biztosít](manage-apps/application-proxy-configure-single-sign-on-password-vaulting.md) |
| A MyApps portálról hozzárendelt felhasználóként való bejelentkezéssel tesztelheti az alkalmazást | https://myapps.microsoft.com |

### <a name="considerations"></a>Megfontolandó szempontok

1. Javasoljuk, hogy az összekötő elhelyezése a vállalati hálózaton, miközben előfordulhatnak olyan esetek, amikor megjelenik a jobb teljesítmény érdekében ennek elhelyezését a felhőben. További információ: [Azure Active Directory Application Proxy használata esetén a hálózati topológiai szempontok](manage-apps/application-proxy-network-topology.md)
2. További biztonsági adatok, és ez hogyan egy különösen biztonságos távoli hozzáférést biztosít a megoldás csak a kimenő kapcsolatok megőrzése lásd: [Azure AD-alkalmazásproxy használatával távolról fér hozzá az alkalmazásokhoz kapcsolódó biztonsági szempontok](manage-apps/application-proxy-security.md)

## <a name="generic-ldap-connector-configuration"></a>Általános LDAP-összekötő konfigurálása

Hozzávetőleges időt Complete: 60 perc

> [!IMPORTANT]
> Ez a FIM vagy MIM bizonyos fokú ismeretét igénylő speciális konfiguráció. Ha éles környezetben használt, azt javasoljuk, hogy ezzel a konfigurációval kapcsolatos kérdések [Premier szintű támogatás](https://support.microsoft.com/premier).

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Az Azure AD Connect telepítése és konfigurálása | Építőelem: [címtár-szinkronizálás – a Jelszókivonat-szinkronizálás](#directory-synchronization--password-hash-sync-phs--new-installation) |
| ADLDS példány értekezlet-követelmények | [Általános LDAP-összekötő – műszaki útmutató: az általános LDAP-összekötő áttekintése](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#overview-of-the-generic-ldap-connector) |
| Számítási feladatok, felhasználók által használt és az ilyen munkaterhelések hozzá kapcsolódó attribútumok közül listája | [Az Azure AD Connect szinkronizálása: az Azure Active Directoryval szinkronizált attribútumok](./connect/active-directory-aadconnectsync-attributes-synchronized.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Általános LDAP-összekötő hozzáadása | [Általános LDAP-összekötő – műszaki útmutató: új összekötő létrehozása](https://docs.microsoft.com/microsoft-identity-manager/reference/microsoft-identity-manager-2016-connector-genericldap#create-a-new-connector) |
| (Teljes importálást, különbözeti importálás, teljes szinkronizálást, a különbözeti szinkronizálás, exportálás) létrehozott összekötőhöz futtatási profilok létrehozása | [Felügyeleti ügynök futtatási profil létrehozása](https://technet.microsoft.com/library/jj590219(v=ws.10).aspx)<br/> [Összekötők használata az Azure AD Connect szinkronizálása a Service Manager rendszerrel](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md)|
| Futtassa a teljes importálást profil, és ellenőrizze, hogy nincsenek-e objektumot az összekötőtérben | [Keresse meg a Összekötőtér objektuma](https://technet.microsoft.com/library/jj590287(v=ws.10).aspx)<br/>[Összekötők használata az Azure AD Connect szinkronizálása a Service Manager rendszerrel: keresési Összekötőterét](./connect/active-directory-aadconnectsync-service-manager-ui-connectors.md#search-connector-space) |
| Szinkronizálási szabályok, létrehozhat, hogy Metaverzumban található objektumok számítási feladatokhoz szükséges attribútumok | [Az Azure AD Connect szinkronizálása: ajánlott eljárások az alapértelmezett konfiguráció módosításának: a szinkronizálási szabályokon változik](./connect/active-directory-aadconnectsync-best-practices-changing-default-configuration.md#changes-to-synchronization-rules)<br/>[Az Azure AD Connect szinkronizálása: a deklaratív kiépítés ismertetése](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning.md)<br/>[Az Azure AD Connect szinkronizálása: deklaratív kiépítés kifejezéseinek ismertetése](./connect/active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |
| Indítsa el a teljes szinkronizálási ciklust | [Az Azure AD Connect szinkronizálása: ütemező: az Ütemező indítása](./connect/active-directory-aadconnectsync-feature-scheduler.md#start-the-scheduler) |
| Problémák esetén tegye a hibaelhárítás | [Az Azure AD-val nem szinkronizálódó objektumok hibaelhárítása](./connect/active-directory-aadconnectsync-troubleshoot-object-not-syncing.md) |
| Ellenőrizze, hogy az LDAP-felhasználó is be- és az alkalmazás eléréséhez | https://myapps.microsoft.com |

### <a name="considerations"></a>Megfontolandó szempontok

> [!IMPORTANT]
> Ez a FIM vagy MIM bizonyos fokú ismeretét igénylő speciális konfiguráció. Ha éles környezetben használt, azt javasoljuk, hogy ezzel a konfigurációval kapcsolatos kérdések [Premier szintű támogatás](https://support.microsoft.com/premier).

## <a name="groups---delegated-ownership"></a>Csoportok – delegált tulajdonjoga

Hozzávetőleges teljes ideje: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| SaaS-alkalmazás (összevont egyszeri bejelentkezés vagy Egyszeri jelszó) már van konfigurálva | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| Felhő az alkalmazást a #1 hozzárendelt hozzáférési csoportokhoz | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) <br/>[Hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](fundamentals/active-directory-groups-create-azure-portal.md) |
| A csoport tulajdonosának hitelesítő adatok | [Erőforrások hozzáférésének kezelése Azure Active Directory-csoportokkal](fundamentals/active-directory-manage-groups.md) |
| Az Infomunkás fér hozzá az alkalmazásokhoz tartozó hitelesítő adatokat talált | [Mi az a hozzáférési panelen?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A csoportnak, hogy kapott hozzáférést az alkalmazáshoz, és konfigurálja az adott tulajdonosa csoport| [Az Azure Active Directory-csoport beállításainak kezelése ](fundamentals/active-directory-groups-settings-azure-portal.md) |
| Jelentkezzen be a csoport tulajdonosa, tekintse meg a csoport tagságát a hozzáférési panel csoportok lapján | [Oldala az Azure Active Directory-csoportok kezelése](https://account.activedirectory.windowsazure.com/r#/groups) |
| Az Infomunkás a vizsgálni kívánt hozzáadása |  |
| Jelentkezzen be az információkkal dolgozó, győződjön meg arról, a csempe akkor érhető el | [Mi az a hozzáférési panelen?](active-directory-saas-access-panel-introduction.md) |

### <a name="considerations"></a>Megfontolandó szempontok

Ha az alkalmazás-kiépítés engedélyezve van, szüksége lehet Várjon néhány percet az üzembe helyezés befejeződik az információkkal dolgozó az alkalmazás elérése előtt.

## <a name="saas-and-identity-lifecycle"></a>SaaS- és identitás-életciklus

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| SaaS-alkalmazás (összevont egyszeri bejelentkezés vagy Egyszeri jelszó) már van konfigurálva | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| Felhő az alkalmazást a #1 hozzárendelt hozzáférési csoportokhoz | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) <br/>[Hozzon létre egy csoportot, és tagokat vehet fel a az Azure Active Directoryban](fundamentals/active-directory-groups-create-azure-portal.md) |
| Az Infomunkás fér hozzá az alkalmazásokhoz tartozó hitelesítő adatokat talált | [Mi az a hozzáférési panelen?](active-directory-saas-access-panel-introduction.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| A felhasználó eltávolítása a csoportból, az alkalmazás hozzá van rendelve. | [A felhasználók számára az Azure Active Directory-bérlőben csoporttagság kezelése](fundamentals/active-directory-groups-members-azure-portal.md) |
| Várjon néhány percet megszüntetést | [Automatikus SaaS-alkalmazás Felhasználókiépítés Azure AD-ben: hogyan működik az automatikus kiépítési?](active-directory-saas-app-provisioning.md#how-does-automatic-provisioning-work) |
| A külön böngésző-munkamenet jelentkezzen be az információkkal dolgozó szakemberek saját alkalmazások portál számára, és győződjön meg arról, hogy a csempe nem található | http://myapps.microsoft.com |


### <a name="considerations"></a>Megfontolandó szempontok

A PoC forgatókönyvnek kilépők és/vagy hagyja távollét forgatókönyvek extrapolálja. Ha a felhasználó lekérdezi az le van tiltva a helyszíni AD vagy eltávolítása után már nem létezik olyan módon, jelentkezzen be a SaaS-alkalmazás.

## <a name="self-service-access-to-application-management"></a>Önkiszolgáló alkalmazás-felügyelet elérésének

Hozzávetőleges teljes ideje: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A biztonsági csoport részeként az alkalmazásokhoz való hozzáférést igényelhet POC-felhasználóinak azonosítása | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| Cél alkalmazása telepítve van. | Építőelem: [SaaS összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Ugrás a vállalati alkalmazások panelen az Azure AD felügyeleti portálon | [Az Azure AD felügyeleti portálra: Vállalati alkalmazások](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) |
| Az önkiszolgáló alkalmazás Előfeltételek konfigurálása | [Újdonságok a vállalati alkalmazások kezelése az Azure Active Directory: az önkiszolgáló alkalmazás-hozzáférés konfigurálása](active-directory-enterprise-apps-whats-new-azure-portal.md#configure-self-service-application-access) |
| Jelentkezzen be az információkkal dolgozó szakemberek saját alkalmazások portál számára | http://myapps.microsoft.com |
| Figyelje meg, hogy a "+ Hozzáadás alkalmazás" gombra a lap op. Annak segítségével kérje le a hozzáférést az alkalmazáshoz |  |

### <a name="considerations"></a>Megfontolandó szempontok

A kiválasztott alkalmazások esetében kiépítési követelményeknek, így azonnal lépjen az alkalmazás egyes hibákat okozhat. Ha a kiválasztott alkalmazás támogatja az azure ad-kiépítés, és úgy van konfigurálva, akkor előfordulhat, hogy használja ezt lehetőséget mutatja a teljes működik a teljes körű. Tekintse meg a építőeleme [SaaS összevont egyszeri bejelentkezési konfiguráció](#saas-federated-sso-configuration) további javaslatok

## <a name="self-service-password-reset"></a>Önkiszolgáló jelszóátállítás

Hozzávetőleges teljes ideje: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A bérlő önkiszolgáló jelszó-kezelés engedélyezése. | [Az Azure Active Directory jelszó-visszaállítás informatikai rendszergazdák számára](active-directory-passwords-update-your-own-password.md) |
| Engedélyezze a jelszóvisszaírást a helyszíni jelszavak kezeléséhez. Megjegyzés: ehhez az adott Azure AD Connect verziója | [Jelszóvisszaírás előfeltételei](authentication/howto-sspr-writeback.md) |
| Ezt a funkciót használja, és ellenőrizze, hogy egy biztonsági csoport tagjai a PoC-felhasználóinak azonosítása. A felhasználó nem rendszergazda teljes mértékben az a funkció bemutatására kell lennie. | [Testreszabása: Az Azure AD-jelszókezelés: hozzáférés korlátozása a jelszó-visszaállítás](authentication/howto-sspr-writeback.md) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Keresse meg az Azure AD felügyeleti portálra: jelszó-visszaállítás | [Az Azure AD felügyeleti portálra: Jelszó-visszaállítás](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) |
| Határozza meg, hogy a jelszó-visszaállítási házirend. POC célokra használhatja a telefonhívásos és a Q & a területen. Javasoljuk, hogy a hozzáférési panelen való bejelentkezéshez szükséges regisztráció engedélyezése |  |
| Jelentkezzen ki, majd jelentkezzen be az információkkal dolgozó |  |
| Adja meg az önkiszolgáló jelszó-visszaállítási adatok konfigurált kiszolgálónként 2. lépés | https://aka.ms/ssprsetup |
| Zárja be a böngészőt |  |
| Az Infomunkás a 4. lépésben használt, a bejelentkezési folyamat Újrakezdés |  |
| A jelszó alaphelyzetbe állítása | [Saját jelszó frissítése: a jelszó alaphelyzetbe állítása](active-directory-passwords-update-your-own-password.md) |
| Próbálja ki a adataikkal jelentkezhetnek be az Azure ad-hez is feltárhatja a helyszíni erőforrásokhoz, hogy új jelszót |  |

### <a name="considerations"></a>Megfontolandó szempontok

1. Okozhat a fennakadások nélkül használható az Azure AD Connect frissítése történik, ha ezután fontolja meg, felhőalapú fiókok ellen, vagy adja meg egy bemutatót egy külön környezetre leselkedő
2. A rendszergazdák egy másik szabályzatot és a jelszót a rendszergazdai fiók használatával lehet, hogy a PoC vágófelülettel és zavart okozhatnak. Normál felhasználói fiókot kell használnia a visszaállítási műveletek tesztelése


## <a name="azure-multi-factor-authentication-with-phone-calls"></a>Az Azure multi-factor Authentication-telefonhívások

Hozzávetőleges teljes ideje: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| MFA-t használó POC-felhasználóinak azonosítása  |  |
| Az MFA-hitelesítést a helyes fogadás telefonszám  | [Mi az az Azure Multi-Factor Authentication?](authentication/multi-factor-authentication.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Keresse meg az Azure AD felügyeleti portálon a "Felhasználók és csoportok" panel | [Az Azure AD felügyeleti portálra: Felhasználók és csoportok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/Overview/menuId/) |
| Válassza a "Minden felhasználó" panelen |  |
| A felső sávon a "Multi-factor Authentication hitelesítés" gomb kiválasztása | Az Azure MFA portál URL-címe közvetlen: https://aka.ms/mfaportal |
| A "User" beállításaiban válassza ki a PoC-felhasználókat, és engedélyezheti őket a multi-factor Authentication | [Azure Multi-Factor Authentication – felhasználói állapotok](authentication/howto-mfa-userstates.md) |
| Jelentkezzen be a PoC-felhasználó, és az útmutató a proof-up folyamat  |  |

### <a name="considerations"></a>Megfontolandó szempontok

1. Ez explicit módon beállítása többtényezős hitelesítés a felhasználó az összes bejelentkezés építőelem PoC lépéseit. Például a feltételes hozzáférés és az Identity Protection egyéb eszközöket, amellyel több MFA forgatókönyvek vonatkozik. Ez lehet érdemes figyelembe venni, amikor POC áthelyezését az éles környezetbe.
2. Az építőelem PoC lépéseit explicit módon használ telefonhívásokat expedience az MFA módszerként. A Váltás a POC az éles környezetben, azt javasoljuk, használjon, például alkalmazások a [Microsoft Authenticator](authentication/end-user/current/microsoft-authenticator-app-how-to.md) a második tényezőként, amikor csak lehetséges.
További információ: [DRAFT NIST Special Publication 800-63B](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="mfa-conditional-access-for-saas-applications"></a>MFA a feltételes hozzáférés a SaaS-alkalmazások

Hozzávetőleges teljes ideje: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| PoC-felhasználók számára a szabályzatok célzásához azonosításához. Ezek a felhasználók a feltételes hozzáférési szabályzat hatóköre egyetlen biztonsági csoportba kell lennie. | [SaaS-összevont egyszeri bejelentkezés konfigurálása](#saas-federated-sso-configuration) |
| SaaS-alkalmazás már konfigurálva |  |
| PoC-felhasználók az alkalmazás már hozzá van rendelve |  |
| A POC-felhasználó hitelesítő adatai érhetők el |  |
| POC-felhasználói MFA-kiszolgáló regisztrálva van. A telefon használata jó fogadása | https://aka.ms/ssprsetup |
| Az eszköz a belső hálózaton. A belső-címtartományban szereplő IP-cím | Keresse meg az ip-címe: https://www.bing.com/search?q=what%27s+my+ip |
| A külső hálózathoz (a szolgáltató mobilhálózat használata telefonon is lehet) eszköz |  |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Nyissa meg az Azure AD felügyeleti portálra: feltételes hozzáférés paneljén | [Az Azure AD felügyeleti portálon: Feltételes hozzáférés](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) |
| Feltételes hozzáférési szabályzat létrehozása:<br/>-Céloldali PoC-felhasználók "Felhasználók és csoportok" területen<br/>-Cél PoC alkalmazást "Felhőalapú alkalmazások" alatt<br/>-Az összes hely cél azzal a különbséggel "Körülmények" megbízható eszközök -> "Hely" **Megjegyzés:** megbízható IP-címek vannak konfigurálva a [MFA portál](https://account.activedirectory.windowsazure.com/UserManagement/MfaSettings.aspx)<br/>-A "Engedélyezés" többtényezős hitelesítés megkövetelése | [Az Azure Active Directory feltételes hozzáférés használatának első lépései: a házirend-konfigurációs lépések](active-directory-conditional-access-azure-portal-get-started.md#policy-configuration-steps) |
| Hozzáférés a vállalati hálózaton belüli alkalmazás | [Az Azure Active Directory feltételes hozzáférés használatának első lépései: a házirend tesztelése](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |
| Hozzáférési kérelem nyilvános hálózat | [Az Azure Active Directory feltételes hozzáférés használatának első lépései: a házirend tesztelése](active-directory-conditional-access-azure-portal-get-started.md#testing-the-policy) |

### <a name="considerations"></a>Megfontolandó szempontok

Összevonási használja, ha a helyszíni identitásszolgáltató (IdP) segítségével a vállalati hálózaton belül/külső állapot jogcímeket a kommunikációhoz. Ezzel a módszerrel lehet, hogy összetett értékeléséhez és kezeléséhez a nagy szervezetek is használnak az IP-címek listájának kezelése nélkül is használhatja. A telepítő a kell a "hálózati központi" forgatókönyv (a felhasználó a belső hálózatról, és közben a kapcsolók naplózás helyeken, például egy kávézóban) fiókot, és győződjön meg arról, hogy megszegéseinek. További információ: [felhőerőforrások védelme Azure multi-factor Authentication és az AD FS: megbízható IP-címek összevont felhasználók esetében](authentication/howto-mfa-adfs.md#trusted-ips-for-federated-users)

## <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

Hozzávetőleges teljes ideje: 15 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A globális rendszergazda, amely a POC a PIM szolgáltatásra része lesz azonosítása | [Az Azure AD Privileged Identity Management használatának megkezdése](active-directory-privileged-identity-management-getting-started.md) |
| Azonosítsa a globális rendszergazdája lesz a biztonsági rendszergazda | [Az Azure AD Privileged Identity Management használatának megkezdése](active-directory-privileged-identity-management-getting-started.md)<br/> [Különböző rendszergazdai szerepkörökkel az Azure Active Directory PIM-ben](active-directory-privileged-identity-management-roles.md) |
| Választható lehetőség: Győződjön meg arról, ha a globális rendszergazdák rendelkeznek-e e-mailek elérését, amelyeket szeretne a PIM e-mail-értesítések | [Mi az Azure AD Privileged Identity Management?: a szerepkör-aktiválási beállítások konfigurálása](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Jelentkezzen be https://portal.azure.com egy globális rendszergazdai (elérhetővé tétel GA) és a rendszerindítási a PIM panel. A globális rendszergazda, amely végrehajtja ezt a lépést a biztonsági rendszergazda van áttöltésekor.  Adjuk a színész GA1 | [Az Azure AD Privileged Identity Management biztonsági varázslója használatával](active-directory-privileged-identity-management-security-wizard.md) |
| Azonosítsa a globális rendszergazda, és át azokat állandó jogosulttá. Ez lehet egy külön rendszergazdai ajánlattartalomnak az 1. lépésben használt. Adjuk a színész GA2 | [Az Azure AD Privileged Identity Management: Hogyan lehet egy felhasználói szerepkör hozzáadása vagy eltávolítása](active-directory-privileged-identity-management-how-to-add-role-to-user.md)<br/>[Mi az Azure AD Privileged Identity Management?: a szerepkör-aktiválási beállítások konfigurálása](active-directory-privileged-identity-management-configure.md#configure-the-role-activation-settings)  |
| Most jelentkezzen be a GA2 https://portal.azure.com , és próbálja meg módosítani a "Felhasználói beállítások". Figyelje meg, hogy néhány lehetőség szürkén jelennek meg. | |
| Egy új lapon, és ugyanabban a munkamenetben, mint 3. lépés, nyissa meg most https://portal.azure.com és a PIM panel hozzáadása az irányítópulthoz. | [Aktiválása vagy inaktiválása szerepkörök az Azure AD Privileged Identity Management: a Privileged Identity Management alkalmazás felvétele](active-directory-privileged-identity-management-how-to-activate-role.md#add-the-privileged-identity-management-application) |
| A globális rendszergazdai szerepkörrel aktiválási kérelmeinek megadása | [Aktiválása vagy inaktiválása szerepkörök az Azure AD Privileged Identity Management: a szerepkör aktiválása](active-directory-privileged-identity-management-how-to-activate-role.md#activate-a-role) |
| Vegye figyelembe, hogy ha GA2 soha nem jelentkezett az MFA-hoz, az Azure MFA regisztrációs szükség lesz |  |
| Lépjen vissza a 3. lépésben az eredeti fülre, és a böngésző Frissítés gombjára. Vegye figyelembe, hogy mostantól hozzáférhet a "Felhasználói beállítások" módosítása | |
| Igény szerint ha a globális rendszergazdák e-mail engedélyezve van, ellenőrizheti GA1 és GA2 a Beérkezett fájlok és az értesítés a szerepkör aktiválása folyamatban |  |
| 8 ellenőrizze a naplózási előzmények, és vizsgálja meg, a jelentés GA2 megszerzését ellenőrizze-e meg. | [Mi az Azure AD Privileged Identity Management?: szerepkör tevékenység áttekintése](active-directory-privileged-identity-management-configure.md#review-role-activity) |

### <a name="considerations"></a>Megfontolandó szempontok

Ez a funkció Azure AD Premium P2 és/vagy az EMS E5 csomag része

## <a name="discovering-risk-events"></a>Kockázati események felderítése

Hozzávetőleges teljes ideje: 20 perc alatt

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Tor böngészővel rendelkező eszköz letöltése és telepítése | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| POC-felhasználó a bejelentkezési annak a hozzáférést | [Az Azure Active Directory Identity Protection-forgatókönyv](active-directory-identityprotection-playbook.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Nyissa meg a tor-böngésző | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Jelentkezzen be https://myapps.microsoft.com a POC felhasználói fiókkal | [Az Azure Active Directory Identity Protection-forgatókönyv: kockázati események szimulálása](active-directory-identityprotection-playbook.md#simulating-risk-events) |
| Várjon 5 – 7 perc |  |
| Jelentkezzen be globális rendszergazdának kell https://portal.azure.com , és nyissa meg az Identity Protection panel | https://aka.ms/aadipgetstarted |
| A kockázati események panel megnyitásához. Megjelenik egy bejegyzés "Bejelentkezések névtelen IP-címekről" alatt  | [Az Azure Active Directory Identity Protection-forgatókönyv: kockázati események szimulálása](active-directory-identityprotection-playbook.md#simulating-risk-events) |

### <a name="considerations"></a>Megfontolandó szempontok

Ez a funkció Azure AD Premium P2 és/vagy az EMS E5 csomag része

## <a name="deploying-sign-in-risk-policies"></a>Bejelentkezési kockázati házirend telepítése

Hozzávetőleges teljes ideje: 10 perc

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| Tor böngészővel rendelkező eszköz letöltése és telepítése | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Hozzáférés a naplót a tesztelés végrehajtásához POC felhasználóként |  |
| POC-felhasználó többtényezős hitelesítéssel regisztrálva. Ügyeljen arra, hogy a telefon használata jó fogadása | Építőelem: [Azure multi-factor Authentication-telefonhívások](#azure-multi-factor-authentication-with-phone-calls) |


### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Jelentkezzen be globális rendszergazdának kell https://portal.azure.com , és nyissa meg az Identity Protection panel | https://aka.ms/aadipgetstarted |
| Engedélyezze a következő bejelentkezési kockázati házirend:<br/>-Hozzárendelve a következőhöz: POC-felhasználó<br/>-Feltételek: Bejelentkezési kockázat közepes vagy nagyobb (bejelentkezési névtelen helyről sikertelennek, közepes méretű kockázati szint)<br/>-Vezérlők: Többtényezős hitelesítés | [Az Azure Active Directory Identity Protection-forgatókönyv: bejelentkezési kockázat](active-directory-identityprotection-playbook.md) |
| Nyissa meg a tor-böngésző | [Töltse le a Tor-böngésző](https://www.torproject.org/projects/torbrowser.html.en#downloads) |
| Jelentkezzen be https://myapps.microsoft.com a PoC felhasználói fiókkal |  |
| Figyelje meg, hogy az MFA-hitelesítést | [Bejelentkezési élmény az Azure AD Identity Protection: kockázatos bejelentkezési helyreállítási](active-directory-identityprotection-flows.md#risky-sign-in-recovery)

### <a name="considerations"></a>Megfontolandó szempontok

Ez a funkció az Azure AD Premium P2 és/vagy az EMS E5 csomag részét képezi. Kockázati események olvashat további: [Azure Active Directory kockázati események](active-directory-reporting-risk-events.md)

## <a name="configuring-certificate-based-authentication"></a>Tanúsítványalapú hitelesítés konfigurálása

Hozzávetőleges időt: 20 perc alatt

### <a name="pre-requisites"></a>Előfeltételek

| Előfeltétel | További források |
| --- | --- |
| A felhasználói tanúsítványt kiépített (Windows, iOS vagy Android) a vállalati nyilvános kulcsú infrastruktúra eszköz | [Felhasználói tanúsítványok telepítése](https://msdn.microsoft.com/library/cc770857.aspx) |
| Az AD FS-összevont Azure AD-tartomány | [Azure AD Connect és összevonás](./connect/active-directory-aadconnectfed-whatis.md)<br/>[Az Active Directory tanúsítványszolgáltatások áttekintése](https://technet.microsoft.com/library/hh831740.aspx)|
| Az iOS-eszközök rendelkeznek a Microsoft Authenticator alkalmazás telepítve van | [A Microsoft Authenticator alkalmazás használatának első lépései](authentication/end-user/current/microsoft-authenticator-app-how-to.md) |

### <a name="steps"></a>Lépések

| Lépés | További források |
| --- | --- |
| Az ADFS "Tanúsítványalapú hitelesítés" engedélyezése | [Hitelesítési házirendek konfigurálása: Az elsődleges hitelesítés globálisan konfigurálása a Windows Server 2012 R2](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-authentication-policies#to-configure-primary-authentication-globally-in-windows-server-2012-r2) |
| Választható lehetőség: Tanúsítványalapú hitelesítés az Azure AD ügyfelei számára engedélyezi az Exchange Active Sync szolgáltatással | [A tanúsítványalapú hitelesítés első lépései az Azure Active Directoryban](active-directory-certificate-based-authentication-get-started.md) |
| Navigáljon a hozzáférési panelen és a hitelesítéshez a felhasználói tanúsítvány használatával | https://myapps.microsoft.com |

### <a name="considerations"></a>Megfontolandó szempontok

További információt talál további információt a központi telepítés figyelmeztetések: [ADFS: Tanúsítványalapú hitelesítés az Azure ad-vel & Office 365-höz](https://blogs.msdn.microsoft.com/samueld/2016/07/19/adfs-certauth-aad-o365/)



> [!NOTE]
> Felhasználói tanúsítvány birtokában kell védeni. Vagy, mivel kezeli az eszközöket, vagy esetén az intelligens kártyák PIN-kóddal.



[!INCLUDE [active-directory-playbook-toc](../../includes/active-directory-playbook-steps.md)]
