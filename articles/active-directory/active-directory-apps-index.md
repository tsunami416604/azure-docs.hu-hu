---
title: Article Index-felügyelet az Azure Active Directoryban |} A Microsoft Azure
description: Ismerje meg, hogyan szabhatja testre az összevonási tanúsítványok lejáratának dátumát, és a közeljövőben lejáró tanúsítványok megújítása.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 9d398d810a2d43b3754fd8950376c605d4654f38
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621531"
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Article Index for Application Management in Azure Active Directory (Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke)
Ezen a lapon minden dokumentumnak Azure Active Directory (Azure AD) különböző-alkalmazásokkal kapcsolatos lehetőségei írt átfogó listáját tartalmazza.

Nincs minden jelentős területeket, valamint a mely cikkek olvasni, attól függően, milyen információt keresett útmutatást röviden bemutatja.

## <a name="overview-articles"></a>Áttekintő cikkek
Az alábbi cikkeket is jó kiindulási pontként azok számára, akik csupán azt szeretnénk, amely röviden elmagyarázza, Azure AD alkalmazás-felügyeleti funkciók.

| A cikk az útmutató |  |
|:---:| --- |
| Bevezetés az Azure AD ezért application felügyeleti problémák |[Alkalmazások kezelése az Azure Active Directory (AD)](manage-apps/what-is-application-management.md) |
| Kik férhetnek hozzá az alkalmazásokhoz, és hogyan indítsa el a felhasználók az alkalmazások egyszeri bejelentkezéshez, amely lehetővé teszi a kapcsolódó Azure AD-ben a különböző funkcióinak áttekintése |[Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban](manage-apps/what-is-single-sign-on.md) |
| Tekintse meg a különböző szükséges lépések eltérőek, amikor az alkalmazások integrálása az Azure AD |[Alkalmazások az Azure Active Directory integrálása](manage-apps/plan-an-application-integration.md)<br /><br />[Egyszeri bejelentkezés SaaS-alkalmazások engedélyezése](manage-apps/configure-single-sign-on-portal.md)<br /><br />[Alkalmazásokhoz való hozzáférés kezelése](manage-apps/what-is-access-management.md) |
| Amely technikai elmagyarázza, hogyan szerepelnek az alkalmazások az Azure ad-ben |[Hogyan és miért érdemes az Azure AD-alkalmazások felvétele](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Hibaelhárítási cikkek
Ez a szakasz gyors hozzáférést biztosít a vonatkozó hibaelhárítási útmutatókat. További információ az egyes szolgáltatási terület a többi ezen a lapon található.

| Szolgáltatási terület |  |
|:---:| --- |
| Összevont egyszeri bejelentkezés |[SAML-alapú egyszeri bejelentkezés hibaelhárítása](develop/howto-v1-debug-saml-sso-issues.md) |
| Jelszavas egyszeri bejelentkezés |[Az Internet Explorer a hozzáférési Panel bővítmény hibaelhárítása](active-directory-saas-ie-troubleshooting.md) |
| Alkalmazásproxy |[Alkalmazásproxy hibaelhárítási útmutatója](manage-apps/application-proxy-troubleshoot.md) |
| Egyszeri bejelentkezés között a helyszíni AD és az Azure ad-ben |[A Jelszókivonat-szinkronizálás hibáinak elhárítása](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md#troubleshoot-password-hash-synchronization)<br /><br />[A Jelszóvisszaíró hibaelhárítása](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dinamikus csoporttagság |[Dinamikus csoporttagságok hibaelhárítása](users-groups-roles/groups-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Egyszeri bejelentkezés (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Összevont egyszeri bejelentkezés: Bejelentkezés számos alkalmazás egyetlen identitás használatával
Egyszeri bejelentkezés lehetővé teszi a felhasználók férhetnek hozzá a különböző alkalmazások és szolgáltatások csak egyetlen hitelesítő adatainak használatával. Összevonási több módszert, amelyekkel engedélyezheti az egyszeri bejelentkezés. Amikor a felhasználók megpróbálnak bejelentkezni összevont alkalmazásokat, azok átirányítja lekérése a szervezeti hivatalos bejelentkezési oldala az Azure Active Directory által nyújtott, és átirányítja vissza az alkalmazás sikeres hitelesítést követően.

| A cikk az útmutató |  |
|:---:| --- |
| Összevonási és a bejelentkezés egyéb bemutatása |[Egyszeri bejelentkezés az Azure ad-vel](manage-apps/what-is-single-sign-on.md) |
| Több ezer SaaS-alkalmazások az Azure AD-vel előre integrált, egyszerűsített egyszeri bejelentkezéses konfigurációs lépések |[Ismerkedés az Azure AD alkalmazáskatalógusában](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Összevonási támogató előre integrált alkalmazások teljes listája](saas-apps/tutorial-list.md)<br /><br />[Az alkalmazás hozzáadása az Azure AD Alkalmazáskatalógusában](develop/howto-app-gallery-listing.md) |
| Több mint 150 alkalmazás oktatóanyagok konfigurálásának egyszeri bejelentkezési alkalmazások például [Salesforce](saas-apps/salesforce-tutorial.md), [ServiceNow](saas-apps/servicenow-tutorial.md), [Google Apps](saas-apps/google-apps-tutorial.md), [Workday](saas-apps/workday-tutorial.md), és még sok más |[SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](saas-apps/tutorial-list.md) |
| Manuálisan állítsa be, és az egyszeri bejelentkezés beállításainak testreszabása |[Hogyan való konfigurálása összevont egyszeri bejelentkezés az alkalmazásokhoz, amelyek nem szerepelnek az Azure Active Directory Alkalmazáskatalógusában](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[Az előre integrált alkalmazások SAML-jogkivonatban kiadott jogcímek testreszabása](active-directory-saml-claims-customization.md) |
| Az összevont alkalmazások az SAML protokollt használó hibaelhárítási útmutató |[SAML-alapú egyszeri bejelentkezés hibaelhárítása](develop/howto-v1-debug-saml-sso-issues.md) |
| Az alkalmazás tanúsítvány lejárati dátuma konfigurálása és a tanúsítványok megújítása |[Tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](manage-apps/manage-certificates-for-federated-single-sign-on.md) |

Összevont egyszeri bejelentkezés az Azure AD-hez felhasználónként legfeljebb tíz alkalmazások minden kiadása érhető el. [Az Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [alapszintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) vagy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/), akkor [összevont alkalmazásokhoz való hozzáférés hozzárendelése csoportok használatával](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Jelszavas egyszeri bejelentkezés: fiók megosztása és egyszeri Bejelentkezést, nem összevont alkalmazások
Egyszeri bejelentkezés alkalmazásokhoz, amelyek nem támogatják az összevonás engedélyezéséhez az Azure AD jelszó felügyeleti szolgáltatásokat nyújt, amelyek biztonságosan tárolhatja az SaaS-alkalmazások a jelszavakat, és automatikusan bejelentkezhetnek a felhasználók ezeknek az alkalmazásoknak. Könnyedén terjesztheti az újonnan létrehozott fiókok hitelesítő adatait, és csapat fiókok több emberekkel osztanak. Felhasználók nem feltétlenül szükséges tudnia, a hitelesítő adatokat, felhőszolgáltatására, a hozzáférést a fiókokhoz.

| A cikk az útmutató |  |
|:---:| --- |
| Hogyan jelszavas egyszeri Bejelentkezést működését bemutató és a egy rövid technikai áttekintése |[Jelszavas egyszeri bejelentkezés az Azure ad-vel](manage-apps/what-is-single-sign-on.md#password-based-single-sign-on) |
| A fiók megosztása, és hogyan a ezeket a problémákat telepítésével lehet megoldani az Azure AD által kapcsolódó forgatókönyveket összefoglalása |[Az Azure AD-fiókok megosztása](active-directory-sharing-accounts.md) |
| Automatikusan módosítsa a jelszót, az egyes alkalmazások rendszeres időközönként |[Automatizált Jelszóváltás (előzetes verzió)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Üzembe helyezése és hibaelhárítása az Azure AD-jelszó felügyeleti bővítmény, az Internet Explorer verziójára vonatkozó útmutatók |[A hozzáférési Panel bővítmény telepítése csoportházirend használatával az Internet Explorer](active-directory-saas-ie-group-policy.md)<br /><br />[Az Internet Explorer a hozzáférési Panel bővítmény hibaelhárítása](active-directory-saas-ie-troubleshooting.md) |

Jelszavas egyszeri bejelentkezéssel felhasználónként legfeljebb tíz alkalmazások az Azure AD minden kiadása érhető el. [Az Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [alapszintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) vagy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/), akkor [alkalmazásokhoz való hozzáférés hozzárendelése csoportok használatával](#managing-access-to-applications). Automatizált jelszóváltás van egy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) funkció.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Alkalmazás-Proxy: Az egyszeri bejelentkezést és távoli hozzáférést a helyszíni alkalmazások
Ha a magánhálózaton, amely a felhasználók és eszközök a hálózaton kívülről kell hozzáférniük az alkalmazásokat, majd használhatja az Azure AD-alkalmazásproxy alkalmazásokra való biztonságos és távoli hozzáférés engedélyezése.

| A cikk az útmutató |  |
|:---:| --- |
| Azure AD-alkalmazásproxy és a működésének áttekintése |[A helyszíni alkalmazások biztonságos távoli hozzáférést biztosító](manage-apps/application-proxy.md) |
| Az oktatóanyagok az alkalmazásproxy konfigurálásáról és az első alkalmazás közzététele |[Az Azure AD-alkalmazásproxyn beállítása](manage-apps/application-proxy-enable.md)<br /><br />[Az alkalmazásproxy-összekötő csendes telepítése](manage-apps/application-proxy-register-connector-powershell.md)<br /><br />[Alkalmazásproxy használó alkalmazások közzétételének módját](manage-apps/application-proxy-publish-azure-portal.md)<br /><br />[A saját tartománynév használatával](manage-apps/application-proxy-configure-custom-domain.md) |
| Az App proxyval közzétett alkalmazások egyszeri bejelentkezést és feltételes hozzáférés engedélyezése |[Single-sign-on az alkalmazásproxy használatával](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)<br /><br />[Feltételes hozzáférés és -Proxy](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Útmutatás az alkalmazásproxy használatával a következő célokra |[Hogyan támogatja a natív ügyfélalkalmazások](manage-apps/application-proxy-configure-native-client-application.md)<br /><br />[Hogyan támogatják a jogcímeket figyelembe vevő alkalmazásokkal](manage-apps/application-proxy-configure-for-claims-aware-applications.md)<br /><br />[Hogyan támogatja a különálló hálózattal és a helyek a közzétett alkalmazások](manage-apps/application-proxy-connector-groups.md) |
| Az Application Proxy hibaelhárítási útmutató |[Alkalmazásproxy hibaelhárítási útmutatója](manage-apps/application-proxy-troubleshoot.md) |

Az alkalmazásproxy felhasználónként legfeljebb tíz alkalmazások az Azure AD összes kiadása esetén érhető el. [Az Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [alapszintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) vagy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/), akkor [alkalmazásokhoz való hozzáférés hozzárendelése csoportok használatával](#managing-access-to-applications).

Akkor is hasznos lehet a [Azure AD tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md), amely lehetővé teszi, hogy az Azure-bA a helyszíni alkalmazások migrálása ugyanakkor továbbra is meg ezeket az alkalmazásokat identitás igényeit.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Egyszeri bejelentkezés az Azure AD között engedélyezése és a helyszíni AD
Ha a szervezet egy Windows Server Active Directoryval a helyszínen és az Azure Active Directory, a felhőben, akkor valószínűleg szeretné majd egyszeri bejelentkezést a két rendszer közötti engedélyezéséhez. Az Azure AD Connect (az eszköz, amely a két rendszer szolgáltatásainak együtt) beállítása az egyszeri bejelentkezés több lehetőséget biztosít: az AD FS vagy egy másik összevonási szolgáltató összevonási létesíthet vagy a jelszó-szinkronizálás engedélyezése.

| A cikk az útmutató |  |
|:---:| --- |
| Az egyszeri bejelentkezési lehetőségek áttekintését az Azure AD Connect, valamint a hibrid környezetek kezeléséről érhető el |[Felhasználói bejelentkezési beállításai az Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Általános útmutatást tartalmazó környezetek kezelése a helyszíni Active Directory és az Azure Active Directory |[Az Azure AD hibrid identitás kialakításával kapcsolatos szempontok](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[A helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md) |
| Útmutató a jelszó-szinkronizálás használatával való egyszeri bejelentkezés engedélyezése |[Csatlakozás az Azure AD jelszó-szinkronizálás megvalósítása](connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md)<br /><br />[Jelszó-szinkronizálás hibaelhárítása](https://support.microsoft.com/en-us/kb/2855271) |
| Útmutató a Jelszóvisszaíró használatával való egyszeri bejelentkezés engedélyezése |[Ismerkedés a jelszókezelés az Azure ad-ben](authentication/quickstart-sspr.md)<br /><br />[A jelszóvisszaíró hibaelhárítása](authentication/active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Útmutató az harmadik féltől származó identitásszolgáltató használatával való egyszeri bejelentkezés engedélyezése |[Kompatibilis külső Identitásszolgáltatók listájából, amely használható az egyszeri bejelentkezés engedélyezése](https://aka.ms/ssoproviders) |
| Hogyan a Windows 10-es egyszeri bejelentkezéssel keresztül az Azure AD Join előnyeit élvezheti |[Csatlakozzon a felhőalapú képességek kiterjesztése a Windows 10-eszközökre az Azure Active Directory keresztül](active-directory-azureadjoin-overview.md) |

Az Azure AD Connect érhető el a [Azure Active Directory minden kiadása](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure AD önkiszolgáló jelszó-visszaállítás érhető el [alapszintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) és [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/). Jelszavak visszaírását a helyszíni AD egy egy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) funkció.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Feltételes hozzáférés: Kényszerítése további biztonsági követelményeket magas kockázatú alkalmazások
Ha beállít egyszeri bejelentkezést az alkalmazások és erőforrások, majd további gondoskodhat érzékeny alkalmazások kényszerítése minden jelentkezzen be, amelyet az alkalmazás az adott biztonsági követelményeknek. Például használhatja az Azure ad-ben, igény szerint, hogy egy adott alkalmazás összes hozzáférést a multi-factor authentication alkalmazás innately támogatja-e a funkció függetlenül mindig szükség van. Egy másik gyakori példa a feltételes hozzáférés, akkor kötelező, hogy felhasználók a szervezet megbízható hálózathoz csatlakozni különösen bizalmas alkalmazásokhoz férhet hozzá.

| A cikk az útmutató |  |
|:---:| --- |
| A feltételes hozzáférési képességeit bemutató érhető el az Azure ad-ben, Office 365 és Intune-ban |[Kockázatkezelés feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) |
| A következő típusú erőforrásokat a feltételes hozzáférés engedélyezése |[Feltételes hozzáférés a SaaS-alkalmazások](conditional-access/app-based-conditional-access.md)<br /><br />[Feltételes hozzáférés az Office 365-szolgáltatásokhoz](active-directory-conditional-access-device-policies.md)<br /><br />[Feltételes hozzáférés a helyszíni alkalmazások](active-directory-conditional-access-azure-portal.md)<br /><br />[Feltételes hozzáférés a helyszíni alkalmazások az Azure AD-alkalmazásproxyn keresztül közzétett](manage-apps/application-proxy-integrate-with-sharepoint-server.md) |
| Eszközök regisztrálása az Azure Active Directoryban annak érdekében, hogy eszközalapú feltételes hozzáférési szabályzat engedélyezése |[Az Azure Active Directory Eszközregisztráció – áttekintés](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Automatikus Eszközregisztráció engedélyezése a tartományhoz csatlakoztatott Windows-eszközök](active-directory-conditional-access-automatic-device-registration.md)<br />– [Lépéseket a Windows 8.1-eszközök](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Lépéseket a Windows 7-eszközökön](active-directory-conditional-access-automatic-device-registration-setup.md) |

| A Microsoft Authenticator alkalmazás használata a kétlépéses ellenőrzéshez |} [Microsoft Authenticator](user-help/microsoft-authenticator-app-how-to.md) |

Feltételes hozzáférés egy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) funkció.

## <a name="apps--azure-ad"></a>Alkalmazások és az Azure ad-ben
### <a name="cloud-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>A cloud Discovery: Keresse meg, melyik SaaS-alkalmazásokat használ a szervezetben
A cloud Discovery összeveti a forgalmi naplóit a Microsoft Cloud App Security felhőalapú alkalmazáskatalógusával a több mint 16000 felhőalapú alkalmazásokat, amelyek szerint rangsorolunk és pontozunk több mint 70 kockázati tényezők alapján, adja meg a folyamatban lévő bepillantást nyerhetnek a felhőalapú használja, informatikai árnyék-infrastruktúrára, és a kockázat Árnyék be a szervezet informatikai kockázatot.

| A cikk az útmutató |  |
|:---:| --- |
| Hogyan működik az általános áttekintése |[Cloud Discovery beállítása](/cloud-app-security/set-up-cloud-discovery) |


### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatikus kiépítésének és felhasználói fiókok a SaaS-alkalmazások megszüntetése
A létrehozás, a karbantartással és a SaaS-alkalmazások, például a Dropbox, a Salesforce, ServiceNow vagy további felhasználói identitásokat eltávolításának automatizálása. Megfelelő és a már meglévő identitásokat az Azure AD között szinkronizálja, és a SaaS-alkalmazások, és a fiókok automatikusan letiltja, amikor a felhasználók elhagyják a szervezet hozzáférés vezérlése.

| A cikk az útmutató |  |
|:---:| --- |
| Ismerje meg annak működését és a gyakori kérdésekre adott válaszok |[Kiépítés & megszüntetés SaaS-alkalmazások felhasználói automatizálása](active-directory-saas-app-provisioning.md) |
| Hogyan van leképezve az információk között az Azure AD konfigurálása és az SaaS-alkalmazás |[Attribútumleképezések testreszabása](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Attribútum-leképezéshez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Amely támogatja az SCIM-protokollt minden alkalmazásba tiltott az Automatikus kiépítés engedélyezése |[Automatizált Felhasználókiépítése bármely SCIM-Enabled alkalmazás beállítása](manage-apps/use-scim-to-provision-users-and-groups.md) |
| A jelentést, és a felhasználók átadásának hibaelhárítása |[A felhasználók automatikus átadásáról Reporting](active-directory-saas-provisioning-reporting.md)<br><br>[Felhasználók átadásának hibaelhárítása](active-directory-application-provisioning-content-map.md) |
| Korlátozzák, hogy kihez lekérdezi létrehozni, hogy egy alkalmazás az attribútumértékek alapján |[Hatókörkezeléshez kapcsolódó szűrők](active-directory-saas-scoping-filters.md) |

Automatizált felhasználókiépítése felhasználónként legfeljebb tíz alkalmazások az Azure AD összes kiadása esetén érhető el. [Az Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [alapszintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) vagy [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/), akkor [csoportok használata kezelheti, hogy mely felhasználók kiépítve](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Alkalmazások létrehozása, amelyek integrálhatók az Azure ad-ben
Ha a szervezet fejlesztéséhez vagy karbantartása – üzletági (LoB) alkalmazásokat, vagy ha Ön az alkalmazás fejlesztője az ügyfeleink, akik az Azure Active Directory, az alábbi oktatóanyagok segítségével az alkalmazások integrálása az Azure ad-ben.

| A cikk az útmutató |  |
|:---:| --- |
| Informatikai szakemberek és az alkalmazások fejlesztői az Azure AD-alkalmazások integrálását ismertető útmutató |[Az IT Pro az útmutató az Azure ad-alkalmazások fejlesztéséhez](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Az Azure Active Directory fejlesztői útmutatója](develop/azure-ad-developers-guide.md) |
| Hogyan alkalmazáshoz szállítók adhat hozzá alkalmazásaikat az Azure AD Alkalmazáskatalógusában |[Az alkalmazás szerepeltetése az Azure Active Directory Alkalmazáskatalógusában listázása](develop/howto-app-gallery-listing.md) |
| Az Azure Active Directoryval fejlett alkalmazásokhoz való hozzáférés kezelése |[Fejlett alkalmazások felhasználó-hozzárendelés engedélyezése](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Felhasználók hozzárendelése az alkalmazáshoz](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Csoport hozzárendelése az alkalmazáshoz](active-directory-applications-guiding-developers-assigning-groups.md) |

Ha a felhasználók felé néző alkalmazásokat fejleszt, érdekelheti az [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , hogy nem kell fejlesztése kezelheti a felhasználók a saját identitáskezelő rendszerbe. [További információk](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Alkalmazásokhoz való hozzáférés kezelése
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Csoportok és önkiszolgáló kezelése, ki férhet hozzá, hogy mely alkalmazások használata
Akik kell, hogy mely erőforrásokhoz való hozzáférés kezelése érdekében az Azure Active Directory lehetővé teszi hozzárendelések és az engedélyek beállítása a méretezési csoportok használatával. Informatikai dönthet úgy, hogy új önkiszolgálói funkció engedélyezése, hogy a felhasználók egyszerűen igényelhetnek engedély regisztrálnak, szükség esetén is.

| A cikk az útmutató |  |
|:---:| --- |
| Az Azure AD hozzáférési felügyeleti funkcióinak áttekintése |[Bevezetés az alkalmazásokhoz való hozzáférés kezelése](manage-apps/what-is-access-management.md)<br /><br />[Hozzáférés-kezelés működését az Azure ad-ben](fundamentals/active-directory-manage-groups.md)<br /><br />[Csoportok használata a SaaS-alkalmazásokhoz való hozzáférés kezelése](users-groups-roles/groups-saasapps.md) |
| Az alkalmazások és a csoportok önkiszolgáló felügyeletének engedélyezése |[Önkiszolgáló kezelés](active-directory-self-service-application-access.md)<br /><br />[Önkiszolgáló csoportkezelés](users-groups-roles/groups-self-service-management.md) |
| Az Azure ad-ben a csoportok beállításával kapcsolatos útmutatás |[Biztonsági csoportok létrehozása](fundamentals/active-directory-groups-create-azure-portal.md)<br /><br />[Hogyan-tulajdonosok megadását egy csoportot kijelölje](fundamentals/active-directory-accessmanagement-managing-group-owners.md)<br /><br />[A "Minden felhasználó" csoport használata](active-directory-accessmanagement-dedicated-groups.md) |
| Dinamikus csoportok használatával automatikusan a tagsági Attribútumalapú szabályok használatával csoporttagság feltöltéséhez |[Dinamikus csoporttagság: Speciális szabályok](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Dinamikus csoporttagságok hibaelhárítása](users-groups-roles/groups-troubleshooting.md) |

Csoportalapú alkalmazáshozzáférés-felügyeletet érhető el a [alapszintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) és [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/). Önkiszolgáló csoportfelügyelet, az önkiszolgáló alkalmazás-felügyeleti és a dinamikus csoportok [prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) funkciókat.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B-együttműködés: Partner alkalmazásokhoz való hozzáférés engedélyezése
Az üzleti más cégekkel alakított ki, valószínű, hogy külső partnerek hozzáférését a vállalati alkalmazások kezeléséhez szükséges. Az Azure Active Directory B2B együttműködés alkalmazások megosztása a partnerekkel való könnyű és biztonságos módot kínál.

| A cikk az útmutató |  |
|:---:| --- |
| Másik Azure AD áttekintést funkciókat, hogy is segítséget, mint például a külső felhasználók kezelése partnerek, ügyfelek stb. |[Az Azure AD külső identitások kezelésére szolgáló képességek összehasonlítása](active-directory-b2b-compare-external-identities.md) |
| B2B-együttműködés és első lépések bemutatása |[Egyszerű, biztonságos, felhőalapú Partnerintegráció az Azure ad-vel](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Az Azure Active Directory B2B-együttműködés](active-directory-b2b-collaboration-overview.md) |
| Részletesebben megismerni az Azure AD B2B együttműködés, és hogyan használható a |[B2B-együttműködés: Működési elv](active-directory-b2b-how-it-works.md)<br /><br />[Aktuális korlátozások az Azure AD B2B együttműködés](active-directory-b2b-current-limitations.md)<br /><br />[Részletes útmutató az Azure AD B2B együttműködés segítségével](active-directory-b2b-detailed-walkthrough.md) |
| Útmutató cikkek az Azure AD B2B együttműködés működésével kapcsolatos technikai részletei |[A Partner felhasználók hozzáadása a CSV fájlformátum](active-directory-b2b-references-csv-file-format.md)<br /><br />[Az Azure AD B2B együttműködés által érintett felhasználói attribútumok](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[A Partner felhasználók számára a felhasználói Token formátuma](active-directory-b2b-references-external-user-token-format.md) |

B2B-együttműködés érhető el jelenleg [Azure Active Directory minden kiadása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Hozzáférési Panel: A portál az alkalmazások és az önkiszolgáló funkciók eléréséhez
Az Azure AD hozzáférési Panel a végfelhasználók hol indítsa el az alkalmazások és, amelyek lehetővé teszik az alkalmazások és a csoporttagságok kezelése önkiszolgáló funkcióhoz férhet hozzá. SSO-kompatibilis alkalmazások eléréséhez szükséges egyéb lehetőségek mellett a hozzáférési panelen, az alábbi lista megtalálhatók.

| A cikk az útmutató |  |
|:---:| --- |
| Az egyszeri bejelentkezéses alkalmazások telepítése a felhasználók számára elérhető különböző lehetőségek összehasonlítása |[Üzembe helyezése az Azure AD integrált alkalmazások felhasználók számára](manage-apps/what-is-single-sign-on.md#deploying-azure-ad-integrated-applications-to-users) |
| A hozzáférési panelen és a mobil egyenértékű MyApps áttekintése |[Bevezetés a hozzáférési panelen és a MyApps](user-help/active-directory-saas-access-panel-introduction.md)<br />— [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Az Azure AD-alkalmazások elérése az Office 365 webhelyről |[Használja az Office 365 Appindítója](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Az Azure AD-alkalmazások elérése az Intune Managed Browser mobilalkalmazás |[Az Intune Managed Browser](https://technet.microsoft.com/library/dn878029.aspx)<br />— [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Mélyhivatkozások használata kezdeményezni az egyszeri bejelentkezés az Azure AD-alkalmazások elérése |[Közvetlen bejelentkezés az alkalmazásokra mutató beolvasása](manage-apps/what-is-single-sign-on.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Hozzáférési Panel érhető el az [Azure Active Directory minden kiadása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Jelentések: Egyszerűen alkalmazás hozzáférés-módosítások naplózása és alkalmazásokhoz való bejelentkezések figyelése
Az Azure Active Directory számos jelentéseket és riasztásokat monitorozhatja a munkahelyi alkalmazásokhoz való hozzáférést biztosít. Rendellenes bejelentkezések riasztásokat fogadhat az alkalmazásokat, és mikor és miért változott meg egy felhasználó hozzáférést egy alkalmazáshoz követheti nyomon.

| A cikk az útmutató |  |
|:---:| --- |
| Az Azure Active Directory jelentéskészítési funkcióinak áttekintése |[Ismerkedés az Azure AD-jelentéskészítés](active-directory-reporting-getting-started.md) |
| A bejelentkezések és felhasználók Alkalmazáshasználat figyelése |[A hozzáférési és használati jelentések megtekintése](active-directory-view-access-usage-reports.md) |
| Egy adott alkalmazás elérésére jogosult végrehajtott módosítások nyomon követése |[Az Azure Active Directory Auditnaplójának jelentési eseményei](active-directory-reporting-audit-events.md) |
| Exportálja az adatokat az alábbi jelentések az Ön által választott eszközökkel, a Reporting API-val |[Bevezetés az Azure AD Reporting API használatába](active-directory-reporting-api-getting-started.md) |

Megtekintheti, mely a jelentések megtalálhatók az Azure Active Directory különböző kiadásait [ide](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Lásd még
[Mi az az Azure Active Directory?](fundamentals/active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Az Azure Active Directory Domain Servicesben](https://azure.microsoft.com/services/active-directory-ds/)

[Az Azure multi-factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)
