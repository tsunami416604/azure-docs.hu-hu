---
title: "Index a következő cikket: az Azure Active Directoryban Alkalmazáskezelés |} A Microsoft Azure"
description: "Ismerje meg, hogyan szabhatja testre a összevonási tanúsítványok lejárati dátuma és rövidesen lejáró tanúsítvány megújítása."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 5321b8e4-2afa-4dfe-8d53-4add7abb5ec8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/21/2017
ms.author: markvi
ms.reviewer: asteen
ms.openlocfilehash: 276997bb6800fda9cb432384ebaaaa717b8426ca
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="article-index-for-application-management-in-azure-active-directory"></a>Article Index for Application Management in Azure Active Directory (Az Azure Active Directory segítségével végzett alkalmazásfelügyeletre vonatkozó cikkek jegyzéke)
Ezen a lapon minden egyes dokumentum írása az alkalmazással kapcsolatos szolgáltatásokra vonatkozó az Azure Active Directory (Azure AD) átfogó listáját tartalmazza.

Nincs minden fő szolgáltatásterület, valamint a cikkek elolvasására attól függően, hogy milyen információkat keres útmutatást rövid áttekintést.

## <a name="overview-articles"></a>Cikkek áttekintése
Az alábbi cikkek jó kezdőpont rendelkező egyszerűen szeretné az Azure AD alkalmazás-felügyeleti funkciókat rövid leírása.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Az alkalmazás felügyelettel kapcsolatos problémák, amelyek az Azure AD megoldja bemutatása |[Alkalmazások kezelése az Azure Active Directory (AD)](active-directory-enable-sso-scenario.md) |
| Kik férhetnek hozzá az alkalmazásokhoz, és hogyan indítsa el a felhasználók az alkalmazások engedélyezése egyszeri bejelentkezéshez, kapcsolódik az Azure AD a különféle funkcióinak áttekintése |[Alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban](active-directory-appssoaccess-whatis.md) |
| A különböző szükséges lépések során alkalmazások integrálása az Azure AD meg |[Az Azure Active Directory integrálása alkalmazások](active-directory-integrating-applications-getting-started.md)<br /><br />[Egyszeri bejelentkezés az SaaS-alkalmazások engedélyezése](active-directory-enterprise-apps-manage-sso.md)<br /><br />[Az alkalmazásokhoz való hozzáférés kezelése](active-directory-managing-access-to-apps.md) |
| Hogyan vannak megadva az alkalmazások az Azure AD technikai magyarázata |[Miért és hogyan lehet az Azure AD-alkalmazások felvétele](active-directory-how-applications-are-added.md) |

## <a name="troubleshooting-articles"></a>Hibaelhárítási cikkek
Ez a szakasz vonatkozó hibaelhárítási útmutatók gyors hozzáférést biztosít. További információ az egyes szolgáltatási terület a többi ezen a lapon található.

| Szolgáltatási terület |  |
|:---:| --- |
| Összevont egyszeri bejelentkezést. |[Hibaelhárítási SAML-alapú egyszeri bejelentkezést.](active-directory-saml-debugging.md) |
| Jelszó-alapú egyszeri bejelentkezést. |[A hozzáférési Panel bővítményét az Internet Explorer hibaelhárítása](active-directory-saas-ie-troubleshooting.md) |
| Application Proxy |[Alkalmazás Proxy hibaelhárítási útmutatója](active-directory-application-proxy-troubleshoot.md) |
| Egyszeri bejelentkezés közötti helyszíni AD és az Azure AD |[A jelszó-szinkronizálás hibaelhárítása](connect/active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization)<br /><br />[A Jelszóvisszaírás hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Dinamikus csoporttagság |[Dinamikus csoporttagság hibaelhárítása](active-directory-accessmanagement-troubleshooting.md) |

## <a name="single-sign-on-sso"></a>Egyszeri bejelentkezés (SSO)
### <a name="federated-single-sign-on-sign-into-many-apps-using-one-identity"></a>Összevont egyszeri bejelentkezés: Jelentkezzen be a egy identitásával sok alkalmazás
Egyszeri bejelentkezés lehetővé teszi a különböző alkalmazások és szolgáltatások csak egy hitelesítő adatainak használatával. Összevonási a, amelyekkel engedélyezheti az egyszeri bejelentkezés módszerrel. Amikor a felhasználók próbálnak bejelentkezni az összevont alkalmazásokba, azokat rendszer átirányítja az szervezetük hivatalos bejelentkezési oldal Azure Active Directory által nyújtott, és a rendszer majd visszairányítja a sikeres hitelesítés után az alkalmazás.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Összevonási és más bejelentkezési bemutatása |[Egyszeri bejelentkezés az Azure ad szolgáltatással](active-directory-appssoaccess-whatis.md) |
| Az Azure AD-val előre integrált Szolgáltatottszoftver-alkalmazásoknál ezer egyszerűsített egyszeri bejelentkezés konfigurációs lépések |[Ismerkedés az Azure AD application gallery a](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Összevonási támogató előre integrált alkalmazások teljes listája](http://aka.ms/aadfederatedapps)<br /><br />[Az alkalmazás felvétele az Azure AD-Alkalmazásgyűjtemény](active-directory-app-gallery-listing.md) |
| Több mint 150 app oktatóanyagok konfigurálásával egyszeri bejelentkezés alkalmazásokhoz, mint [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md), és sok más |[Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md) |
| Manuálisan állítsa be, és az egyszeri bejelentkezés konfigurációs testreszabása |[Hogyan való konfigurálása összevont egyszeri bejelentkezést az alkalmazásokat, amelyek nem találhatók az Azure Active Directory Alkalmazáskatalógusában](application-config-sso-how-to-configure-federated-sso-non-gallery.md)<br /><br />[A SAML-jogkivonat előzetesen beépített alkalmazások számára kiállított jogcímek testreszabása](active-directory-saml-claims-customization.md) |
| A összevont alkalmazásokat, az SAML-protokollt használó hibaelhárítási útmutatója |[Hibaelhárítási SAML-alapú egyszeri bejelentkezést.](active-directory-saml-debugging.md) |
| Az alkalmazás tanúsítvány lejárati dátuma konfigurálása és a tanúsítványok megújítása |[Tanúsítványok kezelése az összevont egyszeri bejelentkezés az Azure Active Directoryban](active-directory-sso-certs.md) |

Összevont egyszeri bejelentkezést érhető el minden kiadása felhasználónként legfeljebb tíz alkalmazások Azure ad-val. [Prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [Azure AD alapvető](https://azure.microsoft.com/pricing/details/active-directory/) vagy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), akkor [csoportoknak a segítségével összevont alkalmazásokhoz történő hozzáférés hozzárendelése](#managing-access-to-applications).

### <a name="password-based-single-sign-on-account-sharing-and-sso-for-non-federated-apps"></a>Jelszó-alapú egyszeri bejelentkezés: fiók megosztása és az egyszeri bejelentkezés nem összevont alkalmazásokhoz
Egyszeri bejelentkezés alkalmazásokhoz, amelyek nem támogatják az összevonás engedélyezéséhez az Azure AD jelszó felügyeleti szolgáltatásokat nyújt, jelszavak SaaS-alkalmazásokhoz való biztonságos tárolására és a felhasználók automatikus bejelentkezés az alkalmazások. Könnyen terjesztése az újonnan létrehozott fiókhoz tartozó hitelesítő adatok, és a csoporthoz megosztása több személy. Felhasználók nem feltétlenül szükséges a hitelesítő adatokat a fiókok, azok hozzáférést kap.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Hogyan jelszóalapú SSO works bemutatása és egy rövid műszaki áttekintése |[Jelszó-alapú egyszeri bejelentkezés az Azure ad szolgáltatással](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| A fiók megosztása és hogyan lehet a problémák megoldani az Azure ad kapcsolatos forgatókönyvek összefoglalása |[Fiókok megosztása az Azure ad szolgáltatással](active-directory-sharing-accounts.md) |
| Automatikusan rendszeres időközönként az egyes alkalmazások jelszavának módosítása |[Automatikus jelszó váltása (előzetes verzió)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Üzembe helyezési és hibaelhárítási útmutatók az Azure AD-jelszó management bővítmény Internet Explorer verziója |[A hozzáférési Panel bővítmény telepítése az Internet Explorer csoportházirend használatával](active-directory-saas-ie-group-policy.md)<br /><br />[A hozzáférési Panel bővítményét az Internet Explorer hibaelhárítása](active-directory-saas-ie-troubleshooting.md) |

Jelszó-alapú egyszeri bejelentkezést érhető el minden kiadása felhasználónként legfeljebb tíz alkalmazások Azure ad-val. [Prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [Azure AD alapvető](https://azure.microsoft.com/pricing/details/active-directory/) vagy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), akkor [csoportoknak a segítségével hozzáférés hozzárendelése alkalmazásokhoz](#managing-access-to-applications). Automatikus jelszó váltási van egy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) szolgáltatás.

### <a name="app-proxy-single-sign-on-and-remote-access-to-on-premises-applications"></a>Alkalmazás-Proxy: Az egyszeri bejelentkezés és a távoli hozzáférést a helyszíni alkalmazások
Ha vannak olyan alkalmazások, a magánhálózaton, amely a felhasználók és eszközök a hálózaton kívülről kell hozzáférniük, majd használhatja az Azure AD-alkalmazásproxy ahhoz, hogy ezek az alkalmazások biztonságos távoli elérést.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Az Azure AD-alkalmazásproxy és működésének áttekintése |[A helyszíni alkalmazások biztonságos távoli hozzáférést biztosító](active-directory-application-proxy-get-started.md) |
| A szolgáltatásalkalmazás-Proxy konfigurálása és közzététele az első alkalmazás oktatóprogramok |[Az Azure AD alkalmazás Proxy beállítása](active-directory-application-proxy-enable.md)<br /><br />[Az alkalmazás alkalmazásproxy-összekötő csendes telepítése](active-directory-application-proxy-silent-installation.md)<br /><br />[Alkalmazás Proxy használó alkalmazások közzétételének módját](active-directory-application-proxy-publish.md)<br /><br />[Saját tartománynév használatával](active-directory-application-proxy-custom-domains.md) |
| Alkalmazás Proxy közzétett alkalmazások egyszeri bejelentkezés és a feltételes hozzáférés engedélyezése |[Single-sign-on a Proxy](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Feltételes hozzáférés és a Proxy](application-proxy-enable-remote-access-sharepoint.md) |
| Alkalmazásproxy használata a következő forgatókönyvek esetén útmutatást |[Natív ügyfél-alkalmazások támogatásának módja](active-directory-application-proxy-native-client.md)<br /><br />[Hogyan támogatja a jogcímbarát alkalmazásokhoz](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Külön hálózatok és helyek közzétett alkalmazások támogatása](active-directory-application-proxy-connectors-azure-portal.md) |
| Alkalmazásproxy hibaelhárítási útmutatója |[Alkalmazás Proxy hibaelhárítási útmutatója](active-directory-application-proxy-troubleshoot.md) |

Minden kiadása felhasználónként legfeljebb tíz alkalmazásokhoz az Azure AD alkalmazásproxy érhető el. [Prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [Azure AD alapvető](https://azure.microsoft.com/pricing/details/active-directory/) vagy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), akkor [csoportoknak a segítségével hozzáférés hozzárendelése alkalmazásokhoz](#managing-access-to-applications).

Bizonyos is érdeklődik [Azure AD tartományi szolgáltatások](../active-directory-domain-services/active-directory-ds-overview.md), amely lehetővé teszi, hogy a helyszíni alkalmazások az Azure-bA áttelepíteni ugyanakkor továbbra is meg ezeknek az alkalmazásoknak identitás igényeinek.

### <a name="enabling-single-sign-on-between-azure-ad-and-on-premises-ad"></a>Egyszeri bejelentkezés az Azure AD között engedélyezése és a helyszíni AD
Ha a szervezet egy Windows Server Active Directory a helyszíni és a felhőben az Azure Active Directoryban tárolja, akkor érdemes engedélyezése egyszeri bejelentkezéshez a két rendszer közötti. Az Azure AD Connect (az eszköz, amely együtt egyesíti a két rendszer) egyszeri bejelentkezés beállítása több lehetőséget biztosít: az AD FS vagy egy másik összevonási szolgáltató összevonási létesíthető, vagy a jelszó-szinkronizálás engedélyezése.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Az egyszeri bejelentkezésre vonatkozó beállításokat a áttekintése érhető el az Azure AD Connect, valamint a hibrid környezetek kezeléséről |[A felhasználói bejelentkezési beállítások az Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Általános útmutatást mindkét környezetek kezelése a helyszíni Active Directory és az Azure Active Directory |[Az Azure AD hibrid identitáskezelési elrendezésével kapcsolatos szempontok](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[A helyszíni identitások integrálása az Azure Active Directoryval](active-directory-aadconnect.md) |
| Útmutatás a jelszó-szinkronizálás engedélyezése az egyszeri bejelentkezés használatával |[Jelszó-szinkronizálás megvalósítása az Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)<br /><br />[Jelszó-szinkronizálás hibaelhárítása](https://support.microsoft.com/en-us/kb/2855271) |
| Útmutatás a Jelszóvisszaírás engedélyezése az egyszeri bejelentkezés használatával |[Az Azure AD-jelszókezelés első lépések](active-directory-passwords-getting-started.md)<br /><br />[A jelszóvisszaíró hibaelhárítása](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Útmutatás az egyszeri bejelentkezés engedélyezése harmadik fél Identitásszolgáltatók használatával |[Kompatibilis harmadik fél Identitásszolgáltatók engedélyezése egyszeri bejelentkezéshez használható listája](https://aka.ms/ssoproviders) |
| Hogyan Windows 10-felhasználók számára is teszik az egyszeri bejelentkezés az Azure AD Join keresztül |[Csatlakozás a felhő szolgáltatásainak kiterjesztése Windows 10 eszközökön az Azure Active Directory használatával](active-directory-azureadjoin-overview.md) |

Az Azure AD Connect érhető el [Azure Active Directory minden kiadása](https://azure.microsoft.com/pricing/details/active-directory/). Az Azure AD önkiszolgáló jelszó-változtatási érhető el [Azure AD alapvető](https://azure.microsoft.com/pricing/details/active-directory/) és [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Jelszavak visszaírását a helyszíni AD: egy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) szolgáltatás.

### <a name="conditional-access-enforce-additional-security-requirements-for-high-risk-apps"></a>Feltételes hozzáférés: Kényszerítése magas kockázatú alkalmazások további biztonsági követelmények
Beállítása után az egyszeri bejelentkezés az alkalmazások és erőforrások, majd további biztonságát érzékeny alkalmazások meghatározott biztonsági követelmények minden bejelentkezés az alkalmazást a tartat be. Például az Azure AD-be, hogy egy adott alkalmazás hozzáférésének mindig megkövetelik a többtényezős hitelesítést, függetlenül attól, hogy támogatja-e az alkalmazás innately funkció igény szerint is használhatja. A feltételes hozzáférés egy másik közös példa egy szükséges, hogy felhasználók kapcsolódnak a szervezet megbízható hálózathoz különösen bizalmas alkalmazás eléréséhez.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Bevezetés a feltételes hozzáférési funkciókat kínál az Azure AD között Office365, és az Intune-ban |[Kockázatkezelés feltételes hozzáférés](active-directory-conditional-access-azure-portal.md) |
| A következő típusú erőforrásokat a feltételes hozzáférés engedélyezése |[Feltételes hozzáférés a Szolgáltatottszoftver-alkalmazásoknál](active-directory-conditional-access-azure-portal-get-started.md)<br /><br />[Feltételes hozzáférés az Office 365-szolgáltatásokhoz](active-directory-conditional-access-device-policies.md)<br /><br />[Feltételes hozzáférés a helyszíni alkalmazások](active-directory-conditional-access-azure-portal.md)<br /><br />[Feltételes hozzáférés a helyszíni alkalmazások közzététele az Azure AD alkalmazás-proxyn keresztül](application-proxy-enable-remote-access-sharepoint.md) |
| Eszközök regisztrálása az Azure Active Directoryban ahhoz, hogy az eszközalapú feltételes hozzáférési házirend engedélyezése |[Az Azure Active Directory Eszközregisztráció – áttekintés](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Automatikus Eszközregisztráció engedélyezése a tartományhoz csatlakoztatott Windows-eszközök](active-directory-conditional-access-automatic-device-registration.md)<br />– [Lépéseket a Windows 8.1-eszközök](active-directory-conditional-access-automatic-device-registration-setup.md)<br />– [Lépéseket a Windows 7-eszközök](active-directory-conditional-access-automatic-device-registration-setup.md) |

| A Microsoft Authenticator alkalmazás használata a kétlépéses ellenőrzéshez |} [Microsoft hitelesítő](../multi-factor-authentication/end-user/microsoft-authenticator-app-how-to.md) |

Feltételes hozzáférés egy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) szolgáltatás.

## <a name="apps--azure-ad"></a>Alkalmazások és az Azure AD
### <a name="cloud-app-discovery-find-which-saas-apps-are-being-used-in-your-organization"></a>A cloud App Discovery: Található mely SaaS-alkalmazások van használatban a szervezet
A cloud App Discovery segítségével informatikai részlegek ismerje meg, melyik SaaS-alkalmazások vannak használatban a szervezetben. Azt is mérheti az alkalmazások használatát és a népszerűségét úgy, hogy az informatikai megállapíthatja, hogy mely alkalmazások előnyösek a legjobban alatt informatikai vezérlő és integrálva van az Azure AD.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Egy általános működésének áttekintése |[A Cloud App Discovery megállapítás nem engedélyezett a felhőalapú alkalmazásokhoz](active-directory-cloudappdiscovery-whatis.md) |
| A mélyebb bemutatója ismerteti, hogyan működik, az adatvédelmi a kérdésekre adott válaszok |[Biztonsági és adatvédelmi megfontolások](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Gyakori kérdések |[Gyakori kérdések a Cloud App Discovery modulra](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| A Cloud App Discovery telepítéséhez oktatóprogramok |[Csoport házirendjének telepítési útmutatója](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[A System Center telepítési útmutatója](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[A Proxy kiszolgálókon egyéni portok telepítése](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| A módosítási napló a Cloud App Discovery-ügynök frissítések |[Változásnapló](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

A cloud App Discovery van egy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) szolgáltatás.

### <a name="automatically-provision-and-deprovision-user-accounts-in-saas-apps"></a>Automatikusan kiépítése és felhasználói fiókok a Szolgáltatottszoftver-alkalmazásoknál kiosztásának megszüntetése
A létrehozási, karbantartási és SaaS-alkalmazásokhoz, például a Dropbox, Salesforce, a ServiceNow és több felhasználói identitások eltávolítása automatizálását. Azonos, meglévő identitások az Azure AD közötti szinkronizálása és a Szolgáltatottszoftver-alkalmazásoknál, és automatikusan letiltja a fiókokat, amikor a felhasználók elhagyják a munkahelyet elérés.

| Útmutató a következő cikket: |  |
|:---:| --- |
| További tudnivalók: hogyan működik, és a gyakori kérdésekre adott válaszok |[Kiépítés és megszüntetés SaaS-alkalmazásokhoz való felhasználói automatizálásához](active-directory-saas-app-provisioning.md) |
| Hogyan van rendelve az információk között az Azure AD konfigurálása és az SaaS-alkalmazás |[Attribútum-leképezésekhez testreszabása](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Attribútum-leképezésekhez kifejezések írása](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Bármely alkalmazás, amely támogatja a SCIM protokoll automatikus kiépítés engedélyezése |[Automatizált Felhasználókiépítése bármely SCIM-Enabled alkalmazás beállítása](active-directory-scim-provisioning.md) |
| Jelentés és a felhasználók átadása hibaelhárítása |[A felhasználók automatikus átadása jelentések](active-directory-saas-provisioning-reporting.md)<br><br>[Üzembe helyezési értesítések](active-directory-saas-account-provisioning-notifications.md)<br><br>[Hibaelhárítás a felhasználók átadása](active-directory-application-provisioning-content-map.md) |
| Korlát, akik lekérdezi létrehozni, hogy az alkalmazást a saját attribútumértékek alapján |[Helyezése Hatókörszűrőkkel](active-directory-saas-scoping-filters.md) |

Automatizált felhasználókiépítése felhasználónként legfeljebb tíz alkalmazásokhoz az Azure AD összes verziója érhető el. [Prémium szintű Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) korlátlan alkalmazásokat támogatja. Ha a szervezete [Azure AD alapvető](https://azure.microsoft.com/pricing/details/active-directory/) vagy [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), akkor [csoportoknak a segítségével kezelheti a felhasználókat első üzembe](#managing-access-to-applications).

### <a name="building-applications-that-integrate-with-azure-ad"></a>Épület alkalmazásokat, amelyekbe beépül az Azure ad szolgáltatással
Ha a szervezet fejleszt, vagy az üzletági (LoB) alkalmazások karbantartása, vagy ha az alkalmazás fejlesztőjének az ügyfelek, akik az Azure Active Directory használatával, az alábbi oktatóanyagok segít az alkalmazások integrálása az Azure ad-val.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Útmutató mind az informatikai szakemberek, valamint az alkalmazások integrálása az Azure AD az alkalmazásfejlesztők számára |[Az informatikai Pro meg az Azure AD-alkalmazások fejlesztésével útmutatója](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[A fejlesztői útmutató az Azure Active Directory](active-directory-developers-guide.md) |
| Hogyan alkalmazás szállítók adhat hozzá az alkalmazások az Azure AD-Alkalmazásgyűjtemény |[Az alkalmazást az Azure Active Directory Alkalmazáskatalógusában listázása](active-directory-app-gallery-listing.md) |
| Az Azure Active Directoryval fejlett alkalmazás-hozzáférés kezelése |[Felhasználó-hozzárendelése fejlesztésű alkalmazások engedélyezése](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Felhasználók hozzárendelése az alkalmazáshoz](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Csoportok átjáróalhálózathoz való hozzárendelése az alkalmazáshoz](active-directory-applications-guiding-developers-assigning-groups.md) |

Ha az alkalmazások a felhasználók felé néző, előfordulhat, hogy el szeretné használni [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) , hogy nincs kezelheti a felhasználók a saját identitásrendszere fejlesztéséhez. [További információk](../active-directory-b2c/active-directory-b2c-overview.md).

## <a name="managing-access-to-applications"></a>Alkalmazás-hozzáférés kezelése
### <a name="using-groups-and-self-service-to-manage-who-has-access-to-which-apps"></a>Csoportok és mely alkalmazások hozzáféréssel rendelkező önkiszolgáló kezelése
Segítséget a kezelése, akik hozzáférhetnek a erőforrásokat, Azure Active Directory teszi hozzárendelések és az engedélyek beállítása a csoportok léptékű. Informatikai dönthetnek úgy, hogy új önkiszolgálói funkció engedélyezése, hogy a felhasználók egyszerűen kérhetnek engedély amikor kell azt.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Az Azure AD hozzáférési felügyeleti funkciókat áttekintése |[Bevezetés az alkalmazásokhoz való hozzáférés kezelése](active-directory-managing-access-to-apps.md)<br /><br />[Hozzáférés-kezelés az Azure AD működése](active-directory-manage-groups.md)<br /><br />[Csoportok használata az SaaS-alkalmazásokhoz való hozzáférés kezelése](active-directory-accessmanagement-group-saasapps.md) |
| Alkalmazások és a csoportok a önkiszolgáló felügyeletének lehetővé tételéhez |[Önkiszolgáló alkalmazások kezelése](active-directory-self-service-application-access.md)<br /><br />[Önkiszolgáló csoportkezelés](active-directory-accessmanagement-self-service-group-management.md) |
| Az Azure ad-ben a csoportok beállításával kapcsolatos útmutatás |[Biztonsági csoportok létrehozása](active-directory-groups-create-azure-portal.md)<br /><br />[Egy csoport tulajdonosainak kijelölése](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[A "Minden felhasználók" csoport használata](active-directory-accessmanagement-dedicated-groups.md) |
| Dinamikus csoportok segítségével automatikusan az attribútumalapú tagsági szabályokkal csoporttagság feltöltéséhez |[Dinamikus csoporttagság: Speciális szabályok](active-directory-groups-dynamic-membership-azure-portal.md)<br /><br />[Dinamikus csoporttagság hibaelhárítása](active-directory-accessmanagement-troubleshooting.md) |

Csoportalapú hozzáférés Alkalmazáskezelés érhető el [Azure AD alapvető](https://azure.microsoft.com/pricing/details/active-directory/) és [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Önkiszolgáló csoportfelügyelet, önkiszolgáló Alkalmazáskezelés és a dinamikus csoportok [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) szolgáltatásokat.

### <a name="b2b-collaboration-enable-partner-access-to-applications"></a>B2B együttműködés: Alkalmazások partner elérésének lehetővé tétele a
Ha a vállalata rendelkezik közösen kötünk más vállalatokkal, valószínű, hogy szeretné-e a vállalati alkalmazásokat a partner hozzáférésének kezelése. Az Azure Active Directory B2B együttműködés megoldást egy egyszerű és biztonságos megosztása az alkalmazások partnerekkel.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Egy másik Azure AD áttekintést funkciókat, hogy is, mint a külső felhasználók kezelése súgó partnerek, ügyfelek stb. |[Az Azure AD külső identitások kezelésére szolgáló képességeket összehasonlítása](active-directory-b2b-compare-external-identities.md) |
| B2B együttműködés és az első lépések bemutatása |[Egyszerű, biztonságos, az Azure AD Cloud Partner integráció](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Az Azure Active Directory B2B együttműködés](active-directory-b2b-collaboration-overview.md) |
| Egy Azure AD B2B együttműködés, és hogy miképpen lehet vele mélyebb bemutatója |[B2B együttműködés: Hogyan működik?](active-directory-b2b-how-it-works.md)<br /><br />[Az Azure AD B2B együttműködés aktuális korlátozásai](active-directory-b2b-current-limitations.md)<br /><br />[Azure AD B2B együttműködés részletes bemutatója](active-directory-b2b-detailed-walkthrough.md) |
| Hivatkozás cikkek az Azure AD B2B együttműködés működéséről technikai részletei |[A Partner felhasználók hozzáadása a CSV fájlformátum](active-directory-b2b-references-csv-file-format.md)<br /><br />[Az Azure AD B2B együttműködés által érintett felhasználói attribútumok](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[A Partner felhasználók a felhasználói Token formátuma](active-directory-b2b-references-external-user-token-format.md) |

B2B együttműködés érhető el jelenleg [Azure Active Directory minden kiadása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="access-panel-a-portal-for-accessing-apps-and-self-service-features"></a>Hozzáférési Panel: Portál alkalmazások és az önkiszolgáló funkciók eléréséhez
Az Azure AD hozzáférési Panel, ahol a végfelhasználók alkalmazások elindítása és az önkiszolgáló funkciók, amelyek lehetővé teszik az alkalmazások és a csoporttagságok kezelése eléréséhez. A hozzáférési Panel mellett más beállításokat a SSO-kompatibilis alkalmazásokhoz fér hozzá az alábbi lista szerepelnek.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Az egyszeri bejelentkezés alkalmazások központi telepítéséhez a felhasználók számára elérhető különböző lehetőségek összehasonlítása |[Üzembe helyezése az Azure AD integrált alkalmazások felhasználók számára](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| A hozzáférési Panel és a mobil egyenértékű MyApps áttekintése |[Bevezetés a hozzáférési Panel és MyApps](active-directory-saas-access-panel-introduction.md)<br />– [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Hogyan érhetők el az Azure AD alkalmazásaiban az Office 365 webhelyről |[Az Office 365 alkalmazás indító használatával](https://support.office.com/en-us/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Hogyan érhetők el az Intune Managed Browser mobilalkalmazás által az Azure AD alkalmazásaiban |[Intune által felügyelt böngészőben](https://technet.microsoft.com/en-us/library/dn878029.aspx)<br />– [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br />– [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Hogyan érhetők el az Azure AD alkalmazások mélyhivatkozással kezdeményezése egyszeri bejelentkezést. |[Közvetlen bejelentkezés az alkalmazásokra mutató beolvasása](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Hozzáférési Panel érhető el [Azure Active Directory minden kiadása](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="reports-easily-audit-app-access-changes-and-monitor-sign-ins-to-apps"></a>Jelentések: Egyszerűen app változásokat naplózási és bejelentkezések alkalmazások figyelése
Az Azure Active Directory biztosít több jelentései és figyelmeztetései segítséget nyújtanak a szervezet hozzáférés alkalmazások figyelésére. Az alkalmazásokhoz is fogadhatja az értesítéseket a rendellenes bejelentkezések, és nyomon követheti az mikor, és miért változott a felhasználók hozzáférést egy alkalmazáshoz.

| Útmutató a következő cikket: |  |
|:---:| --- |
| Azure Active Directory jelentéskészítési szolgáltatásainak áttekintése |[Ismerkedés az Azure AD jelentéskészítési](active-directory-reporting-getting-started.md) |
| A bejelentkezéseket és a felhasználók Alkalmazáshasználat figyelése |[A hozzáférési és használati jelentések megtekintése](active-directory-view-access-usage-reports.md) |
| Egy adott alkalmazás elérésére jogosult végrehajtott módosítások nyomon követése |[Az Azure Active Directory Auditnaplójának jelentési eseményei](active-directory-reporting-audit-events.md) |
| Ezek a jelentések adatainak exportálása a kívánt eszközök a Reporting API használatával |[Bevezetés az Azure AD Reporting API használatába](active-directory-reporting-api-getting-started.md) |

Mely jelentések érhetők el az Azure Active Directory különböző kiadásait megjelenítéséhez [ide](active-directory-view-access-usage-reports.md).

## <a name="see-also"></a>Lásd még:
[Mi az az Azure Active Directory?](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Az Azure Active Directory tartományi szolgáltatások](https://azure.microsoft.com/services/active-directory-ds/)

[Az Azure többtényezős hitelesítés](https://azure.microsoft.com/services/multi-factor-authentication/)
