---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, mi az új Azure Active Directoryval, például a legújabb kibocsátási megjegyzések, az ismert problémák, hibajavításokat tartalmaz, elavult funkció, és a közelgő változásokat.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 833a22635c1d53dce9086aa3e11220d5868a302c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67471994"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?

>Értesítést kaphat arról, hogy a másolás és beillesztés, az URL-cím szerint nyissa meg újra ezt oldal frissítések mikor: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` be a ![RSS-hírcsatorna olvasó ikon](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasó.

Az Azure AD folyamatosan fejlesztései kap. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

Ezen a lapon havonta frissül, így rendszeresen ellenőrizni. Ha a keresett elemek, amelyek hat hónapig régebbiek, megtalálhatja a a [archiválás What's new in Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2019"></a>Június 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Új riskDetections API a Microsoft Graph (nyilvános előzetes verzió)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Identity Protection  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Örömmel jelentjük be, a Microsoft Graph API új riskDetections jelenleg nyilvános előzetes verzióban érhető el. Az új API segítségével a szervezet felhasználói Identity Protection és bejelentkezési kockázat észlelések listájának megtekintéséhez. Ez az API segítségével hatékonyabban lekérdezése a kockázati észlelések, beleértve az észlelés típusa, állapot, szint és további részleteit.

További információkért lásd: a [észlelési API referenciadokumentációra kockázati](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény – június 2019

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** 3. integráció

Június 2019 tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 22 új alkalmazásokról:

[Az Azure AD SAML-eszközkészlet](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN-ügyfél](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [ Segítő segítő](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes Benz autós Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [ Oracle Felhőbeli infrastruktúra konzolon](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx ](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise operációs rendszer](https://proptimise.co.uk/property-software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle hozzáférés-kezelési Oracle kiskereskedelmi kereskedelmi, az Oracle E-Business Suite, Oracle IDCS Oracle Access Manager számára E-Business Suite, Oracle PeopleSoft, Oracle, a JD Edwards IDCS a IDCS

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiók kiépítése ezek újonnan támogatott SaaS-alkalmazások automatizálása

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Most már automatizálhatja létrehozása, frissítése és törlése a felhasználói fiókok ezen újonnan integrált alkalmazások:

- [Nagyítás](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Az envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Hatékonyabb védelmet biztosítson szervezetének automatizált felhasználói fiók kiépítése használatával kapcsolatos további információkért lásd: [SaaS-alkalmazásokhoz az Azure AD-felhasználók létrehozásának automatizálása](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Az Azure AD létesítési szolgáltatás valós idejű állapotának megtekintése

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazások kiépítése  
**A termék szolgáltatás:** Identitás-életciklus-felügyelet

Frissítettük az Azure AD üzembe helyezési környezetének tartalmaz egy új folyamatjelző sávot, amely bemutatja, milyen távolságban áll a folyamat a felhasználók számára. A frissített felhasználói felületet vonatkozó információkat is tartalmaz az aktuális ciklus során létesített felhasználók száma, valamint, hogy hogyan sok felhasználó kiépített dátumhoz.

További információkért lásd: [felhasználókiépítés állapotának ellenőrzéséhez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Most már megjelenik a vállalati arculat a jelentkezzen ki, és hiba képernyővel

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése

Frissítettük az Azure ad-ben, hogy a vállalat márkajelzésének megjelenítésével most kijelentkezési és a hiba képernyők, valamint a bejelentkezési oldal jelenik meg. Ez a funkció bekapcsolásához semmit nem kell, az Azure AD egyszerűen használja a már beállította az eszközöket a **vállalati védjegyezés** az Azure portal területén.

Saját céges védjegy megadásával beállításával kapcsolatos további információkért lásd: [arculat megjelenítése a szervezet Azure Active Directory-lapok hozzáadása](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Az Azure multi-factor Authentication (MFA) kiszolgáló már nem érhető el az új központi telepítéseknél

**Írja be:** Elavult  
**Szolgáltatás kategóriája:** MFA  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

2019. július 1-től a Microsoft már nem szavatolnak MFA-kiszolgáló az új üzembe helyezésekhez. Új ügyfelek számára a szervezetben való többtényezős hitelesítést szeretne most kell használnia a felhőalapú Azure multi-factor Authentication. Ügyfelek, akik aktiválták az MFA-kiszolgáló előtt július 1 módosítás nem jelenik meg. Ön továbbra is képes lesz a legújabb verzió letöltéséhez, a jövőbeli frissítések és az aktiváló hitelesítő adatok előállítása.

További információkért lásd: [első lépései az Azure multi-factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). További információ a felhőalapú Azure multi-factor Authentication: [felhőalapú Azure multi-factor Authentication szolgáltatás üzembe helyezésének megtervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>2019. május

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>A szolgáltatás módosítása: Az alkalmazásproxy-szolgáltatás csak a TLS 1.2 protokoll jövőbeli támogatása

**Írja be:** Tervezett változtatás  
**Szolgáltatás kategóriája:** App Proxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés

Ügyfeleink kategóriaelső a titkosítás biztosítása érdekében, hogy Ön korlátozza a hozzáférést az alkalmazásproxy-szolgáltatás csak a TLS 1.2 protokoll. Ez a változás fokozatosan vezetjük be, akiknek már csak a TLS 1.2 protokoll használata, így nem látható változásait.

A TLS 1.0 és a TLS 1.1 elavulása 2019. augusztus 31-ig történik, de biztosítunk, amellyel további speciális figyelje meg, így lesz ideje a változás előkészítéséhez. Fel a változásra győződjön meg arról, hogy az ügyfél-kiszolgáló és a böngésző-kiszolgáló kombinációit, beleértve az ügyfelek alkalmazásproxyn keresztül közzétett alkalmazások eléréséhez használja a felhasználók, frissítve lett, hogy a TLS 1.2 protokoll használatát az alkalmazás a kapcsolat fenntartását Proxy szolgáltatás. További információkért lásd: [távelérés alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>A használati és insights jelentés segítségével az alkalmazáshoz kapcsolódó bejelentkezési adatok megtekintése

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Mostantól használhatja a használati és insights jelentést, a található a **vállalati alkalmazások** alkalmazásközpontú megtekintheti a bejelentkezési adatait, többek között az adatok kapcsolatos, az Azure portal területén:

- Felső használt alkalmazások a szervezet számára

- A legtöbb sikertelen bejelentkezést-alkalmazások

- Legfontosabb bejelentkezési hibák az egyes alkalmazások

Ezen szolgáltatásáról kapcsolatos további információkért lásd: [használati és elemzések jelentések az Azure Active Directory portál](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>A felhasználókiépítés Azure AD-vel a felhőalkalmazások automatizálása

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Kövesse ezeket az új oktatóanyagok az Azure AD-kiépítés szolgáltatás használatával automatizálható létrehozása, törlés, és a következő felhőalapú alkalmazások felhasználói fiókok frissítése:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Is kövesse ezt új [Dropbox oktatóanyag](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), amely biztosítja, hogy hogyan építheti ki a csoportobjektumokhoz kapcsolatos.

Hatékonyabb védelmet biztosítson szervezetének keresztül automatizált felhasználói fiók kiépítése kapcsolatos további információkért lásd: [SaaS-alkalmazásokhoz az Azure AD-felhasználók létrehozásának automatizálása](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Biztonságos identitás-pontszám már elérhető az Azure ad-ben (általánosan elérhető)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** –  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Most már figyelheti és javíthatja az identitás biztonsági állapotáról, az identitás használatával biztonságos pontszám a szolgáltatás az Azure ad-ben. Az identitás biztonságos pontszám funkciót használ segítségével egyetlen irányítópulton:

- Az identitás biztonsági állapotáról, 1 és 223 közötti pontszámot alapján objektív mérjük.

- Az identitás biztonsági fejlesztések tervezése

- Tekintse át a biztonsági fejlesztésekkel sikeres

Az identitás biztonsági pontszám funkcióival kapcsolatos további információkért lásd: [Mi az az identitás biztonságos pontszám, az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Új felhasználói regisztrációk élményt már elérhető (általánosan elérhető)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Fejlesztői élmény

Az új [alkalmazásregisztrációk](https://aka.ms/appregistrations) élmény mostantól az általános rendelkezésre állás. Az új felületet tartalmaz ismeri az Azure portal és a az alkalmazásregisztrációs portálon minden lényeges szolgáltatását, és javítja a ezekre épül, keresztül:

- **Jobb Alkalmazáskezelés.** Helyett jelenik meg az alkalmazások között a különböző portálok, most már megtekintheti az alkalmazásokat egy olyan hellyel.

- **Egyszerűbb regisztrációs.** A továbbfejlesztett navigációs élményt, az engedély technológiájú termékválasztó felhasználói felület, az mostantól egyszerűbb legyen a regisztrálja, és kezelheti az alkalmazásokat.

- **Részletes információkat.** Az alkalmazás, például gyors útmutatók és egyéb kapcsolatos további részleteket talál.

További információkért lásd: [Microsoft identitásplatformja](https://docs.microsoft.com/azure/active-directory/develop/) és a [már általánosan elérhető az alkalmazás regisztrációs felhasználói felülete!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) bejelentést a blogon.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Az Identity Protection kockázatos felhasználók API-ban elérhető új funkciók

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Identity Protection  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Örömmel jelentjük, hogy most már használhatja a kockázatos felhasználók API lekérdezni felhasználói kockázat előzményeit, hagyja figyelmen kívül a kockázatos felhasználók, és felhasználók megerősítéséhez, mert sérült a biztonsága. Ez a változás segítségével hatékonyabban a felhasználó kockázati állapotának frissítése és a kockázati előzmények ismertetése.

További információkért lásd: a [kockázatos felhasználók API-referenciadokumentáció](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény - 2019. május

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** 3. integráció

2019. május tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 21-én új alkalmazásokról:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [valós hivatkozások](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [egyszerű bejelentkezési](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo értékesítési léphet](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globális HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum munkahelyi](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [WebMethods API felhőalapú elemében](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Vezérlő](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>A továbbfejlesztett csoportok létrehozása és kezelése során lép fel az Azure AD portálon

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés

A csoportok kapcsolatos funkciókat a fejlesztéseket végeztünk az Azure AD portálon. Ezek a fejlesztések lehetővé teszik a rendszergazdák hatékonyabban tudják kezelni a csoportok listája, a tagok listája, és adja meg a további létrehozási lehetőségek.

Fejlesztések a következők:

- Alapszintű tagság típusa és a csoport típusa alapján történő szűrést.

- Új oszlop, például a forrás- és E-mail-cím hozzáadását.

- Lehetővé teszi több válassza ki a csoportokat, a tagok és a tulajdonos egyszerűen törlésre sorolja fel.

- Válasszon egy e-mail-címet, és adja hozzá a tulajdonosok csoport létrehozása során lehetősége.

További információkért lásd: [létrehozásához, és adja hozzá az Azure Active Directoryval tagok](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Office 365-csoportokra vonatkozó elnevezési szabályzat konfigurálása az Azure AD portálon (általánosan elérhető)

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés

A rendszergazdák most már konfigurálhat az Azure AD Portalon Office 365-csoportokra vonatkozó elnevezési szabályzat. Ezen módosítás révén az Office 365-csoportokat a szervezet felhasználói által létrehozott vagy módosított egységes elnevezési konvenciók kényszerítése.

Office 365-csoportokra vonatkozó elnevezési szabályzat kétféleképpen konfigurálhatja:

- Adja meg az előtagok vagy utótagok, amely a rendszer automatikusan hozzáadja a csoport nevét.

- Töltse fel a csoportjának letiltott szavakat a szervezete számára, amely a csoport nevét (például "CEO, Bérszámfejtés, HR") nem engedélyezett.

További információkért lásd: [egy Office 365-csoportokra vonatkozó elnevezési szabályzat kényszerítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>A Microsoft Graph API-végpontok számára érhető el az Azure AD-tevékenységnaplókat (általánosan elérhető)

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Örömmel jelentjük be az általános rendelkezésre állás a Microsoft Graph API végpontok támogatása az Azure Active Directory naplói. Ezzel a kiadással, így naplózása az Azure AD 1.0-s verziójának használatát naplózza, valamint a bejelentkezési naplók API-k.

További információkért lásd: [az Azure AD naplózási log API – áttekintés](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>A rendszergazdák most már használhatja a feltételes hozzáférés az egyesített regisztrációs folyamathoz (nyilvános előzetes verzió)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitásbiztonság és -védelem  

A rendszergazdák most már létrehozhat használható a feltételes hozzáférési szabályzatok szerint a kombinált regisztrációs oldalhoz. Ez tartalmazza a regisztráció engedélyezése, ha a házirendek:

- A megbízható hálózat, a felhasználók.

- A felhasználók egy kis bejelentkezési kockázat.

- A felügyelt eszközön, a felhasználók.

- Felhasználók vállalja, hogy a szervezet azon használati feltételeket (feltételek).

További információ a feltételes hozzáférés és a jelszó-visszaállítás láthatja a [feltételes hozzáférés az Azure ad MFA kombinált és a jelszó-visszaállítási regisztrációs élmény blogbejegyzés](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). További információ a feltételes hozzáférési szabályzatokat az egyesített regisztrációs folyamathoz: [kombinált regisztrációs vonatkozó feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). A funkció használatát az Azure AD használati kapcsolatos további információkért lásd: [Azure Active Directory – használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Április 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>Új Azure ad-ben fenyegetésészlelési intelligencia már elérhető a frissített Azure AD Identity Protection

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Azure AD Identity Protection  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Az Azure AD intelligens fenyegetésészlelés most már a frissített Azure AD Identity Protection érhető el. Ez az új funkció segíti jelezze a felhasználói tevékenység, amely egy adott felhasználó szokatlan vagy, amely összhangban az ismert támadási mintákat a Microsoft külső és belső fenyegetések felderítése alapján.

Az Azure AD Identity Protection frissített verziója kapcsolatos további információkért lásd: a [négy fő Azure AD Identity Protection fejlesztések, mostantól nyilvános előzetes verzióban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) blog és a [Mi az Azure Active Directory Identity Protection (frissítve)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) a cikk. Az Azure AD fenyegetésészlelési intelligencia kapcsolatos további információkért lásd: a [Azure Active Directory Identity Protection kockázati események](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) cikk.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Az Azure AD jogosultságot kezelés pedig mostantól elérhető (nyilvános előzetes verzió)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Identity Governance  
**A termék szolgáltatás:** Identity Governance

Az Azure AD tagjogosultság-kezelés, mostantól nyilvános előzetes verzióban elérhető segítségével az ügyfelek és delegálja a felügyeleti csomagok hozzáférést, amely meghatározza, hogy alkalmazottai és partnerei üzleti kérhet a hozzáférést, aki jóvá kell hagynia és mennyi ideig hozzáféréssel rendelkeznek. Hozzáférési csomagok kezelheti az Azure AD-tagság és a Office 365-csoportok, a szerepkör-hozzárendeléseit a vállalati alkalmazásokban és a szerepkör-hozzárendeléseit a SharePoint Online-webhelyekkel. További információ: tagjogosultság-kezelés a [tagjogosultság-kezelés az Azure AD áttekintést](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). További információ a technológiai spektrumunk kihasználtságának növelését az Azure AD Identity Cégirányítási szolgáltatásait, beleértve a Privileged Identity Management, a hozzáférési felülvizsgálatok és a használati feltételeket, [Mi az Azure AD Identity Cégirányítási?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Office 365-csoportokra vonatkozó elnevezési szabályzat konfigurálása az Azure AD Portalon (nyilvános előzetes verzió)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés

A rendszergazdák most már konfigurálhat az Azure AD Portalon Office 365-csoportokra vonatkozó elnevezési szabályzat. Ezen módosítás révén az Office 365-csoportokat a szervezet felhasználói által létrehozott vagy módosított egységes elnevezési konvenciók kényszerítése.

Office 365-csoportokra vonatkozó elnevezési szabályzat kétféleképpen konfigurálhatja:

- Adja meg az előtagok vagy utótagok, amely a rendszer automatikusan hozzáadja a csoport nevét.

- Töltse fel a csoportjának letiltott szavakat a szervezete számára, amely a csoport nevét (például "CEO, Bérszámfejtés, HR") nem engedélyezett.

További információkért lásd: [egy Office 365-csoportokra vonatkozó elnevezési szabályzat kényszerítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-Tevékenységnaplók már elérhetők az Azure Monitor (általánosan elérhető)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Oldja meg az Azure AD-Tevékenységnaplók rendelkező Vizualizációk visszajelzést érdekében vezetünk be egy új elemzés szolgáltatás a Log Analyticsben. Ez a szolgáltatás segít az interaktív sablonokat, munkafüzetek nevű használatával betekintés nyerhető az Azure AD-erőforrások. Ezeket az előzetesen összeállított munkafüzetek alkalmazások vagy felhasználók adja meg adatait, és a következők:

- **Bejelentkezések.** Részletes adatokat biztosít az alkalmazások és a felhasználók számára, beleértve a bejelentkezés helyét, a használatban lévő operációs rendszer vagy böngészőügyfél és verziója és a sikeres vagy sikertelen bejelentkezések száma.

- **Az örökölt hitelesítési és feltételes hozzáférés.** Részletes adatokat biztosít a alkalmazás- és felhasználói örökölt hitelesítéssel, többek között a feltételes hozzáférési szabályzatokat a feltételes hozzáférési szabályzatok, alkalmazások által aktivált multi-factor Authentication használati és így tovább.

- **Sikertelen bejelentkezési elemzése.** Segít meghatározni, ha a bejelentkezési hiba fordul elő egy felhasználói művelet, a házirend problémák vagy az infrastruktúra miatt.

- **Egyéni jelentések.** Új létrehozása vagy szerkesztése a létező munkafüzeteken segítségével testre szabhatja az elemzés szolgáltatás a szervezet számára.

További információkért lásd: [használata az Azure Monitor-munkafüzetek a jelentések az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény - április 2019

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** 3. integráció

Április 2019 tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 21-én új alkalmazásokról:

[Az SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Szűrjön át](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [ Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Csatlakozás](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel csatlakozás](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba felhő (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent tőke felügyeleti](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Tanúsítványkezelő](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [ Surveymonkey-beli vállalati](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakorisága beállítás és több szerepkör kiválasztása

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék szolgáltatás:** Identity Governance

Új frissítések az Azure AD hozzáférési felülvizsgálatokkal teszi lehetővé:

- Módosítsa a gyakoriságát, a hozzáférési felülvizsgálat **félig annually**, heti, havi, negyedéves és éves, korábban létező lehetőségek mellett.

- Válassza ki a több Azure ad-ben, és Azure-erőforrások szerepköreihez egyetlen elérhető létrehozásakor tekintse át. Ebben a helyzetben minden szerepkör beállítása ugyanazokkal a beállításokkal, és minden véleményezők egy időben értesítést kap.

Hozzáférési felülvizsgálat létrehozásával kapcsolatos további információkért lásd: [csoportok a hozzáférési felülvizsgálat létrehozása és alkalmazások az Azure AD hozzáférési felülvizsgálatokkal](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Az Azure AD Connect e-mail értesítési rendszer(ek) transitioning, egyes ügyfelek számára az új e-mailt küldő adatokat küld

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** AD-Szinkronizálóról  
**A termék szolgáltatás:** Platform

Az Azure AD Connect az e-mail értesítési rendszer(ek) transitioning folyamatban van a potenciálisan megjelenítése egy új e-mailt küldő az egyes ügyfelek. Ennek fel kell vennie `azure-noreply@microsoft.com` , a szervezet engedélyezi a listát, vagy nem tudja folytatni a fontos riasztások fogadása az Office 365, Azure vagy a szinkronizálási szolgáltatások.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Egyszerű felhasználónév utótagja módosítások sikeresen végre lettek most az összevont tartományok az Azure AD Connect között

**Írja be:** Rögzített  
**Szolgáltatás kategóriája:** AD-Szinkronizálóról  
**A termék szolgáltatás:** Platform

Most már sikeresen módosíthatja a felhasználói UPN-utótagot az összevont tartományok az Azure AD Connect egy másik az összevont tartományhoz. Javítás: már nem kell a szinkronizálási ciklus során a FederatedDomainChangeError hibaüzenetet tapasztal, vagy kap egy értesítést e-mailt arról "nem sikerült frissíteni az objektumot az Azure Active Directoryban, mert az attribútum [ FederatedUser.UserPrincipalName], nem érvényes. Frissítse az értéket a helyi címtárszolgáltatásban".

További információkért lásd: [szinkronizálási hibák elhárítását](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Nagyobb biztonság az app protection-alapú feltételes hozzáférési szabályzat segítségével az Azure ad-ben (nyilvános előzetes verzió)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

App protection-alapú feltételes hozzáférés már elérhető használatával a **megkövetelése alkalmazásvédelmi** házirend. Az új szabályzat segít növelni a szervezet biztonsági segít elkerülése érdekében:

- A felhasználók férjenek hozzá az alkalmazások Microsoft Intune-licenc nélkül.

- A felhasználó nem a Microsoft Intune alkalmazásvédelmi szabályzat lekérése.

- A felhasználók férjenek hozzá a konfigurált a Microsoft Intune alkalmazásvédelmi szabályzat nélküli alkalmazások.

További információkért lásd: [kötelezővé tétele az alkalmazásvédelmi szabályzatot a cloud app hozzáféréshez a feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Az Azure AD egyszeri bejelentkezést és feltételes hozzáférés a Microsoft Edge (nyilvános előzetes verzió) az új támogatása

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Ezért továbbfejlesztettük az Azure AD-támogatás a Microsoft Edge, beleértve az új Azure AD egyszeri bejelentkezést és feltételes hozzáférés gondoskodik. Ha korábban már használta a Microsoft Intune Managed Browser, most már használhatja a Microsoft Edge helyett.

Beállítását és kezelését, az eszközök és alkalmazások feltételes hozzáférés használatával kapcsolatos további információkért lásd: [megkövetelése eszközök feltételes hozzáféréssel felhőalapú alkalmazás-hozzáférés felügyelt](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) és [igényel jóváhagyott ügyfélalkalmazások felhő a feltételes hozzáférést biztosító alkalmazás-hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). -Hozzáférés kezelése a Microsoft Edge a Microsoft Intune-házirendek használatával kapcsolatos további információkért lásd: [Internet-hozzáférés kezelése a Microsoft Intune a házirend által védett böngészővel](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>2019. március

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identitás-kezelőfelületi keretrendszer és az egyéni házirend támogatása az Azure Active Directory B2C jelenleg elérhető (GA)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** B2C - felhasználói identitás kezelése  
**A termék szolgáltatás:** B2B/B2C

Mostantól létrehozhat egyéni szabályzatok Azure AD B2C-vel, és többek között az alábbi feladatokat, amelyek támogatottak ipari méretekben és az Azure SLA mellett:

- Hozzon létre, és töltse fel az egyéni hitelesítési felhasználói utak egyéni szabályzatok használatával.

- Felhasználói utak cseréje, részletes közötti Jogcímszolgáltatók ismertetik.

- Adja meg a felhasználói utak feltételes elágaztatás.

- Átalakíthatja és képezi le a jogcímeket, valós idejű döntéseket hozhat, és kommunikációhoz használható.

- Az egyéni hitelesítési felhasználói utak REST API-kompatibilis szolgáltatásokat használni. Ha például az e-mail-szolgáltatók, CRM és engedélyezési saját fejlesztésű rendszerekhez.

- Identitás-szolgáltatóktól, akik megfelelnek a OpenIDConnect protokoll-összevonják. Ha például a több-bérlős Azure ad-ben, a közösségi fiók szolgáltatók, vagy kétfaktoros hitelesítési szolgáltatót.

Egyéni szabályzatok létrehozásával kapcsolatos további információkért lásd: [az Azure Active Directory B2C-vel egyéni szabályzatok megjegyzések fejlesztők számára](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) és olvasási [Alex Simon blogbejegyzésében találhat, többek között az esettanulmányok](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény - 2019. március

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** 3. integráció

Március 2019 tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 14 új alkalmazásokról:

[ISEC7 mobileszköz Exchange delegált](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Magyarázata naplózási rendszerbe](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [lean típusú](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool teljesítmény kérdések](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [osztályozása Intranetes](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit szintek](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [feladat](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler és kiépítése az Azure AD katalógusából származó – március 2019 összekötők Atlassian

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Alkalmazások kiépítése  
**A termék szolgáltatás:** 3. integráció

Automatizálás létrehozása, frissítése és törlése a következő alkalmazások felhasználói fiókok:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler béta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler egy](https://aka.ms/ZscalerOneProvisioning), [Zscaler két](https://aka.ms/ZscalerTwoProvisioning), [Zscaler három](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian felhő](https://aka.ms/atlassianCloudProvisioning)

Hatékonyabb védelmet biztosítson szervezetének keresztül automatizált felhasználói fiók kiépítése kapcsolatos további információkért lásd: [SaaS-alkalmazásokhoz az Azure AD-felhasználók létrehozásának automatizálása](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Visszaállítása és az Azure AD portálon a törölt Office 365-csoportok kezelése

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés

Most már megtekintheti, és a törölt Office 365-csoportok az Azure AD Portalon lehet kezelni. Ezen módosítás révén láthatja, hogy mely csoportok visszaállítása, együtt, ami lehetővé teszi, hogy véglegesen törölni minden olyan csoportok, amelyek nem szükségesek a szervezet által érhetők el.

További információkért lásd: [visszaállítási lejárt vagy törölt csoportok](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Egyszeri bejelentkezés már elérhető az Azure AD SAML biztonságos helyszíni alkalmazások proxyn keresztül történő alkalmazás (nyilvános előzetes verzió)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** App Proxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés

Most már megadhat egy egyszeri bejelentkezéssel (SSO) felhasználói élményt a helyszínen, SAML-hitelesítésű alkalmazások együtt alkalmazásproxyn keresztül ezek az alkalmazások távoli elérését. SAML egyszeri bejelentkezés beállítása a helyszíni alkalmazásokkal kapcsolatos további információkért lásd: [SAML egyszeri bejelentkezést az alkalmazásproxy (előzetes verzió) használatával a helyszíni alkalmazások](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A kérelem hurkok ügyfélalkalmazások megszakad, megbízhatóság és a felhasználói élmény javítása érdekében

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése

Ügyfélalkalmazások rövid időn át nem megfelelően azonos bejelentkezési kéréseket több száz bocsát ki. Ezeket a kérelmeket azok sikeresek-e, hogy az összes közreműködés és a gyenge felhasználói élmény fokozása számítási feladatok az Identitásszolgáltató, növelje a késés az összes felhasználó számára, és csökkenti az Identitásszolgáltató rendelkezésre állását a.

Ezt a frissítést küld egy `invalid_grant` hiba: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` , egy rövid idő alatt, az túlmutat a normál működés során többször ismétlődő kérelmeket kibocsátó ügyfélalkalmazások. A probléma előforduló ügyfélalkalmazások meg kell jelennie egy interaktív kérdés, hogy a felhasználónak újra be kell jelentkezni. Erről a változásról, és javítsa ki az alkalmazást, ha ezt a hibát tapasztal kapcsolatos további információkért lásd: [hitelesítés újdonságai?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Új Auditnaplók felhasználói élmény már elérhető

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Létrehoztunk egy új Azure AD **Auditnaplók** lap olvashatóságát és az adatok kereséséhez hogyan javítása érdekében. Megtekintheti az új **Auditnaplók** lapon jelölje be **Auditnaplók** a a **tevékenység** szakaszban az Azure AD.

![Új naplók az oldalon minta információval](media/whats-new/audit-logs-page.png)

További információ az új **Auditnaplók** lapon találhat [naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatást a helytelenül konfigurált feltételes hozzáférési szabályzatok véletlen rendszergazda fiókzárolási megelőzése érdekében

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Megakadályozza, hogy a rendszergazdák véletlenül zárolás maguk ki saját bérlők helytelenül konfigurált feltételes hozzáférési házirendek érdekében létrehoztuk az Azure portal új figyelmeztetések és a frissített útmutatást. Az új útmutató kapcsolatos további információkért lásd: [Mik az Azure Active Directory feltételes hozzáférés függőségei](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Továbbfejlesztett végfelhasználói használati funkciók használatának meggátolása a mobil eszközök használata

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Szabályozás

Frissítettük a meglévő használati használata élményt, tekintse át, és hogyan a használati feltételeket a mobileszközökön való hozzájárulás javítása érdekében. Ezután nagyíthat és, lépjen vissza, töltse le az adatokat, és válassza ki a hivatkozások. A frissített használati feltételek kapcsolatos további információkért lásd: [Azure Active Directory – használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Új Azure AD-Tevékenységnaplók elérhető letöltése

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Letöltheti tevékenységeket tartalmazó naplók nagy mennyiségű most már közvetlenül az Azure Portalról. A frissítés lehetővé teszi:

- Töltse le a legfeljebb 250 000 sort.

- A letöltés befejezése után értesítést kaphat.

- Testre szabhatja a fájl nevét.

- A kimeneti formátum, a JSON és a fürt megosztott kötetei szolgáltatás határozza meg.

Ez a funkció kapcsolatos további információkért lásd: [a rövid útmutató: Töltse le az auditnaplókat, az Azure portal használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Kompatibilitástörő változás: A feltétel kiértékelése Exchange ActiveSync (EAS) által frissítései

**Írja be:** Tervezett változtatás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Hozzáférés-vezérlés

Hogy dokumentációnk frissítés a hogyan értékeli ki az Exchange ActiveSync (EAS) a következő feltétel:

- Felhasználó helye, ország, régió vagy IP-cím alapján

- Bejelentkezési kockázat

- Eszközplatform

Ha a feltételes hozzáférési szabályzatokat a korábban már használta az ezeket a feltételeket, vegye figyelembe, hogy a feltétel viselkedés változhatnak. Például ha korábban már használta a felhasználói helyfeltétel egy házirendet, Észreveheti most kihagyja a szabályzatot a felhasználó helye alapján.

---

## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható az Azure AD SAML-jogkivonat titkosítása (nyilvános előzetes verzió) 

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO

Most már beállíthatja bármely támogatott SAML-alkalmazásba titkosított SAML-jogkivonatokat fogadni. Az alkalmazás használt és konfigurált, Azure ad-ben a kibocsátott SAML helyességi feltételek használatával egy Azure AD-ben tárolt tanúsítvány nyilvános kulcsával titkosítja.

Az SAML-jogkivonat titkosítása konfigurálásával kapcsolatos további információkért lásd: [konfigurálása az Azure AD SAML-jogkivonat titkosítása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>A csoportok és alkalmazások az Azure AD hozzáférési felülvizsgálatok használatával hozzáférési felülvizsgálat létrehozása

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék szolgáltatás:** Szabályozás

Most már megadhat több csoportot, vagy egyetlen Azure AD-alkalmazások hozzáférési felülvizsgálat csoporttagság vagy alkalmazás-hozzárendelés. Hozzáférési felülvizsgálatok több csoportokkal vagy alkalmazások beállítása ugyanazokkal a beállításokkal, és az összes belefoglalt véleményezők egy időben értesítést kap.

További információ az Azure AD hozzáférési felülvizsgálatok segítségével a hozzáférési felülvizsgálat létrehozása, lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása az Azure AD hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény - 2019. február

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** 3. integráció
 
A 2019. február tettünk elérhetővé összevonással új 27-es alkalmazásokról az alkalmazáskatalógusban támogatja:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT UJJÁT](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Engedély kattintással [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [szeizmikus ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Megosztás egy kicsivel](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [WebMethods integrációs felhőalapú elemében](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Tudásbázis bárhol LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope adatok](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud Genesysszerint](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp termelékenység Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Továbbfejlesztett kombinált MFA/az SSPR-regisztráció

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóváltoztatás  
**A termék szolgáltatás:** Felhasználók hitelesítése

Az ügyfelek visszajelzéseire válaszolva ezért továbbfejlesztettük kombinált MFA/az SSPR regisztrációs előzetes felület útmutatás nyújtása a felhasználók gyorsabban MFA és az SSPR a biztonsági adataikat regisztrálhat. 

**Kapcsolja be a továbbfejlesztett élmény a felhasználók számára a ma, kövesse az alábbi lépéseket:**

1. Globális rendszergazda vagy felhasználói rendszergazda, jelentkezzen be az Azure Portalra, és nyissa meg **Azure Active Directory > felhasználói beállítások > hozzáférési panel előzetes verziójú funkciók beállításait kezelheti**. 

2. Az a **felhasználók, akik használhatják az előzetes funkciók regisztrálásához és a biztonsági adatok kezelése – a frissítés** beállítások között válassza ki az a funkciók bekapcsolása egy **felhasználók kiválasztott csoportja** vagy **minden felhasználó** .

A következő néhány hét alatt azt fogjuk kell megszünteti azt a lehetőséget a régi kombinált MFA/az SSPR regisztrációs előzetes felület a bérlők számára, amely még nincs bekapcsolva bekapcsolását.

**Szeretné látni, ha a vezérlőelem távolítja el a bérlő számára, kövesse az alábbi lépéseket:**

1. Globális rendszergazda vagy felhasználói rendszergazda, jelentkezzen be az Azure Portalra, és nyissa meg **Azure Active Directory > felhasználói beállítások > hozzáférési panel előzetes verziójú funkciók beállításait kezelheti**.  

2. Ha a **felhasználók regisztrálása és biztonsági adatok kezelésére is használhatja az előzetes verziójú funkciók** beállítás **nincs**, a beállítás a bérlőről törlődni fog.

Függetlenül attól, hogy korábban már be van kapcsolva a régi kombinált MFA/az SSPR regisztrációs felhasználói élményét, vagy sem, ha a régi felületet egy későbbi időpontban ki lesz kapcsolva. Ezért erősen javasoljuk, hogy, hogy áthelyezi az új, továbbfejlesztett élmény a lehető leghamarabb.

Továbbfejlesztett regisztrációs funkciókkal kapcsolatos további információkért lásd: a [ritkán használt adatok fejlesztések az Azure ad MFA kombinált és a jelszó-visszaállítási regisztrációs élmény](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Frissített szabályzatot kezelést biztosít a felhasználói folyamatok

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** B2C - felhasználói identitás kezelése  
**A termék szolgáltatás:** B2B/B2C

Frissítettük a felhasználói folyamatok (korábbi néven, beépített szabályzatokat) könnyebb házirend létrehozásának és felügyeletének folyamatát. Az új felület már az összes az Azure AD-bérlőt az alapértelmezett.

További visszajelzések és javaslatok biztosítása a mosoly használatával, vagy felül konkáv ív ikonjai a **küldjön visszajelzést** területet, a portál képernyő felső részén.

Új szabályzat felügyeleti funkciókkal kapcsolatos további információkért lásd: a [Azure AD B2C-t most már a JavaScript-testreszabást és számos további új funkciókat](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Válassza ki az Azure AD B2C által biztosított adott oldal elem verziók

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** B2C - felhasználói identitás kezelése  
**A termék szolgáltatás:** B2B/B2C

Most kiválaszthatja az Azure AD B2C által biztosított elemei egy adott verzióját. Ha egy adott verziót, tesztelheti a frissítések lapon jelennek meg, és megjelenik a kiszámítható viselkedés előtt. Ezenkívül mostantól kérheti a kényszerítéséhez, hogy a testreszabások JavaScript adott oldal verziók. Ez a funkció bekapcsolásához nyissa meg a **tulajdonságok** oldalon a felhasználói folyamatokban.

Adott verzióinak oldalelemeihez kiválasztásával kapcsolatban további információkért tekintse meg a [Azure AD B2C-t most már a JavaScript-testreszabást és számos további új funkciókat](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurálható végfelhasználói jelszókövetelményeinek B2C (GA)

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** B2C - felhasználói identitás kezelése  
**A termék szolgáltatás:** B2B/B2C

Most már beállíthatja a szervezet jelszó erőssége a natív használata helyett a végfelhasználók számára az Azure AD-jelszóházirendet. A a **tulajdonságok** panelt a felhasználói folyamatok (korábbi nevén a beépített szabályzatokat), kiválaszthatja a jelszó bonyolultságát **egyszerű** vagy **erős**, vagy beállíthatja a Hozzon létre egy **egyéni** követelményt ismertet.

Jelszó bonyolultsága követelmény konfigurálásával kapcsolatos további információkért lásd: [bonyolultsági jelszavak konfigurálása az Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új egyéni vállalati arculattal hitelesítési folyamattal alapértelmezett sablonok

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** B2C - felhasználói identitás kezelése  
**A termék szolgáltatás:** B2B/B2C

Használhatja az új alapértelmezett sablon, amely található a **elrendezések lapon** panelt a felhasználói folyamatok (korábbi nevén beépített szabályzatokat), hozzon létre egyéni márkás a hitelesítési módszer a felhasználók számára.

A sablonok használatával kapcsolatos további információkért lásd: [Azure AD B2C-t most már a JavaScript-testreszabást és számos további új funkciókat](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Egyszeri jelszó-hitelesítés (nyilvános előzetes verzió) használatával az Active Directory B2B-együttműködés

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** B2B  
**A termék szolgáltatás:** B2B/B2C

Egyszeri jelszó-hitelesítés (OTP) jelentettük B2B vendégfelhasználó, aki nem hitelesíthetők, más módon, például az Azure ad-ben, egy Microsoft-fiókkal (MSA) vagy a Google összevonási. Ez az új hitelesítési módszer azt jelenti, hogy a felhasználóknak nem kell egy új Microsoft-fiók létrehozása a Vendég. Ehelyett váltja be a meghívót, vagy egy megosztott erőforráshoz való hozzáférést, a vendégfelhasználó is kérhető egy e-mail-címre kell küldeni egy ideiglenes kódot. Az ideiglenes kóddal, a vendégfelhasználó továbbra is való bejelentkezéshez.

További információkért lásd: [e-mailben kapott egyszeri jelszót hitelesítés (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blogon [Azure AD-ban megosztási és együttműködési zökkenőmentes bármely felhasználó bármilyen fiókkal](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD-alkalmazásproxy cookie beállításai

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** App Proxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés

Három új cookie-k, elérhető beállítások az alkalmazásproxyn keresztül közzétett alkalmazások vezettünk be:

- **Csak HTTP cookie-t használja.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy hozzáférési és munkamenet-cookie. Ez a beállítás bekapcsolásával biztosít további biztonsági előnyök, például a megelőzhetők másolása és módosítása a cookie-k segítségével készíthet ügyféloldali parancsfájlt. Javasoljuk, hogy kapcsolja be ezt a jelzőt, (válasszon **Igen**) esetében a előnyökkel.

- **Biztonságos cookie-t használja.** Beállítja a **biztonságos** jelzőt az alkalmazásproxy hozzáférési és munkamenet-cookie. Ez a beállítás bekapcsolásával biztosít további biztonsági előnyök, azáltal, hogy a cookie-k csak továbbításuk a TLS biztonságos csatornát, például a HTTPS. Javasoljuk, hogy kapcsolja be ezt a jelzőt, (válasszon **Igen**) esetében a előnyökkel.

- **Állandó cookie-t használja.** Megakadályozza, hogy a cookie-k hozzáférést lejár, a böngésző bezárásakor. A cookie-kat utolsó a hozzáférési jogkivonat élettartama. A cookie-k azonban a lejárati idő elérésekor, vagy ha a felhasználó nem törli a cookie-k állnak vissza. Azt javasoljuk, hogy megtartani az alapértelmezett beállítást **nem**, csak a régebbi folyamatok közötti cookie-kat nem használó alkalmazások esetében a beállítás bekapcsolásával.

Az új cookie-k kapcsolatos további információkért lásd: [cookie-k beállításairól a helyszíni alkalmazások az Azure Active Directory eléréséhez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény - 2019. január

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** 3. integráció
 
Január 2019 tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 35 új alkalmazásokról:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [szakembereket paletta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco összevonó](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet-hozzáférés rendszergazdája](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lejárati emlékeztető](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR megjelenítő](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [művelet](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [zárja be a digitális Pavaso](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice eszközkészlet](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus egyszeri Bejelentkezéses rendszert](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Vállalati ár](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 az Office 365-höz](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn komphajó ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Az új Azure AD Identity Protection fejlesztések (nyilvános előzetes verzió)

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Identity Protection  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Örömmel jelentjük, hogy lehetőségekkel bővült az alábbi fejlesztéseket tartalmazzák az Azure AD Identity Protection nyilvános előzetes ajánlat, többek között:

- Egy frissített és több integrált felhasználói felület

- További API-k

- Továbbfejlesztett kockázatértékelés gépi tanuláshoz

- A termék egészére igazítás kockázatos felhasználók és a kockázatos bejelentkezések

A fejlesztések kapcsolatos további információkért lásd: [Mi az Azure Active Directory Identity Protection (frissíthetők)?](https://aka.ms/IdentityProtectionDocs) További információkért, és megoszthatja a gondolatait a terméken belüli utasításokat keresztül.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Új alkalmazás-zárolási funkció az iOS és Android rendszerű eszközökön a Microsoft Authenticator alkalmazás

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** A Microsoft Authenticator alkalmazás  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Az egyszeri PIN-kódok, alkalmazás- és Alkalmazásbeállítások nagyobb biztonságban, bekapcsolhatja a Microsoft Authenticator alkalmazást az App Lock funkciót. App Lock bekapcsolásával azt jelenti, hogy is ismételt szeretne hitelesítést végezni a PIN-kódot vagy biometrikus, amikor megnyitja a Microsoft Authenticator alkalmazást.

További információkért lásd: a [– gyakori kérdések a Microsoft Authenticator alkalmazás](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett Azure AD Privileged Identity Management (PIM) képességeket exportálása

**Írja be:** Új szolgáltatás  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management

Privileged Identity Management (PIM) a rendszergazdák most már exportálhatja az összes aktív és a jogosult szerepkör-hozzárendelését egy adott erőforráshoz, amely tartalmazza az összes gyermekerőforrás szerepkör-hozzárendeléseit. Korábban a rendszergazdák teljes listáját az előfizetéshez tartozó szerepkör-hozzárendelések nehéz volt, és kellett arra, hogy minden erőforrás szerepkör-hozzárendeléseit.

További információkért lásd: [PIM az Azure-erőforrások szerepköreihez tartozó tevékenység és a naplózási előzmények megtekintése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
