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
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98f4724e419e0ad07c7d01f1cfe95bbde001e236
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194588"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?

>Értesítést kaphat arról, hogy a másolás és beillesztés, az URL-cím szerint nyissa meg újra ezt oldal frissítések mikor: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` be a ![RSS ikon](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasó.

Az Azure AD folyamatosan fejlesztései kap. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

Ezen a lapon havonta frissül, így rendszeresen ellenőrizni. Ha a keresett elemek, amelyek hat hónapig régebbiek, megtalálhatja a a [archiválás What's new in Azure Active Directory](whats-new-archive.md).

---
## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható az Azure AD SAML-jogkivonat titkosítása (nyilvános előzetes verzió) 

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO
 
Most már beállíthatja bármely támogatott SAML-alkalmazásba titkosított SAML-jogkivonatokat fogadni. Az alkalmazás használt és konfigurált, Azure ad-ben a kibocsátott SAML helyességi feltételek használatával egy Azure AD-ben tárolt tanúsítvány nyilvános kulcsával titkosítja.

Az SAML-jogkivonat titkosítása konfigurálásával kapcsolatos további információkért lásd: [konfigurálása az Azure AD SAML-jogkivonat titkosítása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>A csoportok és alkalmazások az Azure AD hozzáférési felülvizsgálatok használatával hozzáférési felülvizsgálat létrehozása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék szolgáltatás:** Szabályozás

Most már megadhat több csoportot, vagy egyetlen Azure AD-alkalmazások hozzáférési felülvizsgálat csoporttagság vagy alkalmazás-hozzárendelés. Hozzáférési felülvizsgálatok több csoportokkal vagy alkalmazások beállítása ugyanazokkal a beállításokkal, és az összes belefoglalt véleményezők egy időben értesítést kap.

További információ az Azure AD hozzáférési felülvizsgálatok segítségével a hozzáférési felülvizsgálat létrehozása, lásd: [csoportokat vagy alkalmazásokat a hozzáférési felülvizsgálat létrehozása az Azure AD hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Új összevont alkalmazások érhetők el az Azure AD-alkalmazásgyűjtemény - 2019. február

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
Január 2019 tettünk elérhetővé összevonással új 27-es alkalmazásokról az alkalmazáskatalógusban támogatja:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT UJJÁT](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), [ Engedély kattintson](https://docs.microsoft.com/azure/active-directory/saas-apps/permissionclick-tutorial), [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [ Szeizmikus](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [megosztás egy kicsivel](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [WebMethods integrációs felhőalapú elemében](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Tudásbázis bárhol LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope adatok](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud Genesys szerint ](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp termelékenység Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Továbbfejlesztett kombinált MFA/az SSPR-regisztráció

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Az ügyfelek visszajelzéseire válaszolva ezért továbbfejlesztettük kombinált MFA/az SSPR regisztrációs előzetes felület útmutatás nyújtása a felhasználók gyorsabban MFA és az SSPR a biztonsági adataikat regisztrálhat. 

**Kapcsolja be a továbbfejlesztett élmény a felhasználók számára még ma, kövesse az alábbi lépéseket:**

1. Globális rendszergazda vagy felhasználói rendszergazda, jelentkezzen be az Azure Portalra, és nyissa meg **Azure Active Directory > felhasználói beállítások > hozzáférési panel előzetes verziójú funkciók beállításait kezelheti**. 

2. Az a **felhasználók, akik használhatják az előzetes funkciók regisztrálásához és a biztonsági adatok kezelése – a frissítés** beállítások között válassza ki az a funkciók bekapcsolása egy **felhasználók kiválasztott csoportja** vagy **minden felhasználó** .

A következő néhány hét alatt azt fogjuk kell megszünteti azt a lehetőséget a régi kombinált MFA/az SSPR regisztrációs előzetes felület a bérlők számára, amely még nincs bekapcsolva bekapcsolását.

**Szeretné látni, ha a vezérlőelem távolítja el a bérlő számára, kövesse az alábbi lépéseket:**

1. Globális rendszergazda vagy felhasználói rendszergazda, jelentkezzen be az Azure Portalra, és nyissa meg **Azure Active Directory > felhasználói beállítások > hozzáférési panel előzetes verziójú funkciók beállításait kezelheti**.  

2.  Ha a **felhasználók regisztrálása és biztonsági adatok kezelésére is használhatja az előzetes verziójú funkciók** beállítás **nincs**, a beállítás a bérlőről törlődni fog.

Függetlenül attól, hogy korábban már be van kapcsolva a régi kombinált MFA/az SSPR regisztrációs felhasználói élményét, vagy sem, ha a régi felületet egy későbbi időpontban ki lesz kapcsolva. Ezért erősen javasoljuk, hogy, hogy áthelyezi az új, továbbfejlesztett élmény a lehető leghamarabb.

Továbbfejlesztett regisztrációs funkciókkal kapcsolatos további információkért lásd: a [ritkán használt adatok fejlesztések az Azure ad MFA kombinált és a jelszó-visszaállítási regisztrációs élmény](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Frissített szabályzatot kezelést biztosít a felhasználói folyamatok

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C

Frissítettük a felhasználói folyamatok (korábbi néven, beépített szabályzatokat) könnyebb házirend létrehozásának és felügyeletének folyamatát. Az új felület már az összes az Azure AD-bérlőt az alapértelmezett.

További visszajelzések és javaslatok biztosítása a mosoly használatával, vagy felül konkáv ív ikonjai a **küldjön visszajelzést** területet, a portál képernyő felső részén.

Új szabályzat felügyeleti funkciókkal kapcsolatos további információkért lásd: a [Azure AD B2C-t most már a JavaScript-testreszabást és számos további új funkciókat](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Válassza ki az Azure AD B2C által biztosított adott oldal elem verziók

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C

Most kiválaszthatja az Azure AD B2C által biztosított elemei egy adott verzióját. Ha egy adott verziót, tesztelheti a frissítések lapon jelennek meg, és megjelenik a kiszámítható viselkedés előtt. Ezenkívül mostantól kérheti a kényszerítéséhez, hogy a testreszabások JavaScript adott oldal verziók. Ez a funkció bekapcsolásához nyissa meg a **tulajdonságok** oldalon a felhasználói folyamatokban.

Adott verzióinak oldalelemeihez kiválasztásával kapcsolatban további információkért tekintse meg a [Azure AD B2C-t most már a JavaScript-testreszabást és számos további új funkciókat](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Konfigurálható végfelhasználói jelszókövetelményeinek B2C (GA)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C

Mostantól kifejezetten állíthat be a szervezet jelszó összetettségét, a natív használata helyett a végfelhasználók számára az Azure AD-jelszóházirendet. A a **tulajdonságok** panelt a felhasználói folyamatok (korábbi nevén a beépített szabályzatokat), kiválaszthatja a jelszó bonyolultságát **egyszerű** vagy **erős**, vagy beállíthatja a Hozzon létre egy **egyéni** követelményt ismertet.

Jelszó bonyolultsága követelmény konfigurálásával kapcsolatos további információkért lásd: [bonyolultsági jelszavak konfigurálása az Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új egyéni vállalati arculattal hitelesítési folyamattal alapértelmezett sablonok

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2C - fogyasztói identitások kezelése  
**A termék szolgáltatás:** B2B/B2C

Használhatja az új alapértelmezett sablon, amely található a **elrendezések lapon** panelt a felhasználói folyamatok (korábbi nevén beépített szabályzatokat), hozzon létre egyéni márkás a hitelesítési módszer a felhasználók számára.

A sablonok használatával kapcsolatos további információkért lásd: [Azure AD B2C-t most már a JavaScript-testreszabást és számos további új funkciókat](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Egyszeri jelszó-hitelesítés (nyilvános előzetes verzió) használatával az Active Directory B2B-együttműködés

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék szolgáltatás:** B2B/B2C

Egyszeri jelszó-hitelesítés (OTP) jelentettük B2B vendégfelhasználó, aki nem hitelesíthetők, más módon, például az Azure ad-ben, egy Microsoft-fiókkal (MSA) vagy a Google összevonási. Ez az új hitelesítési módszer azt jelenti, hogy a felhasználóknak nem kell egy új Microsoft-fiók létrehozása a Vendég. Ehelyett váltja be a meghívót, vagy egy megosztott erőforráshoz való hozzáférést, a vendégfelhasználó is kérhető egy e-mail-címre kell küldeni egy ideiglenes kódot. Az ideiglenes kóddal, a vendégfelhasználó továbbra is való bejelentkezéshez.

További információkért lásd: [e-mailben kapott egyszeri jelszót hitelesítés (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blogon [Azure AD-ban megosztási és együttműködési zökkenőmentes bármely felhasználó bármilyen fiókkal](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Application Proxyval kapcsolatos cookie-beállítások az Azure AD-ben

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés

Három új cookie-k, elérhető beállítások az alkalmazásproxyn keresztül közzétett alkalmazások vezettünk be:

- **Csak HTTP cookie-t használja.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy hozzáférési és munkamenet-cookie. Ez a beállítás bekapcsolásával biztosít további biztonsági előnyök, például a megelőzhetők másolása és módosítása a cookie-k segítségével készíthet ügyféloldali parancsfájlt. Javasoljuk, hogy kapcsolja be ezt a jelzőt, (válasszon **Igen**) esetében a előnyökkel.

- **Biztonságos cookie-t használja.** Beállítja a **biztonságos** jelzőt az alkalmazásproxy hozzáférési és munkamenet-cookie. Ez a beállítás bekapcsolásával biztosít további biztonsági előnyök, azáltal, hogy a cookie-k csak továbbításuk a TLS biztonságos csatornát, például a HTTPS. Javasoljuk, hogy kapcsolja be ezt a jelzőt, (válasszon **Igen**) esetében a előnyökkel.

- **Állandó cookie-t használja.** Megakadályozza, hogy a cookie-k hozzáférést lejár, a böngésző bezárásakor. A cookie-kat utolsó a hozzáférési jogkivonat élettartama. A cookie-k azonban a lejárati idő elérésekor, vagy ha a felhasználó nem törli a cookie-k állnak vissza. Azt javasoljuk, hogy megtartani az alapértelmezett beállítást **nem**, csak a régebbi folyamatok közötti cookie-kat nem használó alkalmazások esetében a beállítás bekapcsolásával.

Az új cookie-k kapcsolatos további információkért lásd: [cookie-k beállításairól a helyszíni alkalmazások az Azure Active Directory eléréséhez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2019. január

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
Január 2019 tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 35 új alkalmazásokról:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [szakembereket paletta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco összevonó](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Internet-hozzáférés rendszergazdája](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lejárati emlékeztető](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR megjelenítő](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [művelet](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [zárja be a digitális Pavaso](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice eszközkészlet](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus egyszeri Bejelentkezéses rendszert](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Vállalati ár](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 az Office 365-höz](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn komphajó ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Új Azure AD Identity Protection-funkciók (nyilvános előzetes verzió)

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

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Új alkalmazászárolási funkció az iOS-es és az androidos Microsoft Authenticator alkalmazásban

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** A Microsoft Authenticator alkalmazás  
**A termék szolgáltatás:** Identitásbiztonság és -védelem

Az egyszeri PIN-kódok, alkalmazás- és Alkalmazásbeállítások nagyobb biztonságban, bekapcsolhatja a Microsoft Authenticator alkalmazást az App Lock funkciót. App Lock bekapcsolásával azt jelenti, hogy is ismételt szeretne hitelesítést végezni a PIN-kódot vagy biometrikus, amikor megnyitja a Microsoft Authenticator alkalmazást.

További információkért lásd: a [– gyakori kérdések a Microsoft Authenticator alkalmazás](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett exportálási funkciók az Azure AD Privileged Identity Management (PIM) szolgáltatásban

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management

Privileged Identity Management (PIM) a rendszergazdák most már exportálhatja az összes aktív és a jogosult szerepkör-hozzárendelését egy adott erőforráshoz, amely tartalmazza az összes gyermekerőforrás szerepkör-hozzárendeléseit. Korábban a rendszergazdák teljes listáját az előfizetéshez tartozó szerepkör-hozzárendelések nehéz volt, és kellett arra, hogy minden erőforrás szerepkör-hozzárendeléseit.

További információkért lásd: [PIM az Azure-erőforrások szerepköreihez tartozó tevékenység és a naplózási előzmények megtekintése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>2018. november/December

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem válthatnak felhőalapú fiókokra

**Írja be:** Rögzített  
**Szolgáltatás kategóriája:** Felhasználókezelés  
**A termék szolgáltatás:** Címtár

>[!Important]
>Hogy vezetünk, és megismerheti az as gazdasági válság után a javítás miatt. Ezért azt már vissza a módosítás addig, hogy mi megkönnyítheti a javítás, hogy Megvalósíthat a szervezetben.

Azt is, amelyben egy felhasználó a DirSyncEnabled jelölő lenne próbál állítani hibája kijavítva **hamis** amikor az Active Directory Domain Services (AD DS) objektum zárva a szinkronizálási hatókör és a Lomtárat a majd át Azure ad-ben a következő szinkronizálási ciklusban. A javítás eredményeként a felhasználó adatszinkronizálás hatóköre alól, és ezt követően az Azure AD lomtárának vissza a felhasználói fiók marad, szinkronizálja a helyszíni AD-ben várt módon, és nem felügyelhető a felhőben, mert a forrás hatóság (SoA) marad a helyszíni AD.

A javítás előtt hiba történt a FALSE bekapcsolásakor a rendszer a DirSyncEnabled jelölő. A nem megfelelő benyomást, hogy ezek a fiókok átalakítottunk csak felhőalapú objektumok és a fiókok sikerült felügyelhetők a felhőbeli adott azt. Azonban a fiók továbbra is megfelelően azok SoA érkező, a helyszíni és az összes szinkronizált tulajdonságok (árnyékmásolat attribútum) a helyszíni AD. Ez az állapot az Azure AD-ben több problémákat és egyéb felhőalapú számítási feladatokhoz (például az Exchange online-hoz), hogy kezelje ezeket a fiókokat az AD-ből szinkronizálódni várt, de most is viselkedik például kizárólag felhőalapú fiókok okozza.

Jelenleg az egyetlen valóban egy szinkronizált-a-AD-fiók konvertálása csak felhőalapú fiók módja tiltsa le a DirSync, amely elindítja a háttérrendszer művelet vihetők át a SOA típusú bérlői szinten. Ilyen típusú SoA módosítása igényel (de nem korlátozott a) az összes helyi tisztítás kapcsolódó attribútumok (például LastDirSyncTime és árnyékmásolat-attribútumok) és a egy olyan jelet küld más felhőalapú számítási feladatokhoz, hogy a megfelelő objektum túl alakítani egy kizárólag felhőalapú fiók .

Ez a javítás ennek következtében megakadályozza, hogy az Active Directoryból szinkronizált felhasználói ImmutableID attribútumon közvetlen frissítéseket, amelyek bizonyos esetekben a múltban van szükség a. Szolgáltatásainak kialakítása során az Azure ad-objektum immutableid azonosítója a neve is mutatja, ahogy helyezni nem módosítható. Az ilyen forgatókönyvek megvalósítása az Azure AD Connect Health és az Azure AD Connect szinkronizálási ügyfél új szolgáltatások érhetők el:

- **A legtöbb felhasználó szakaszos megközelítéssel nagyméretű ImmutableID update**
  
  Ha például kell tennie az AD DS hosszadalmas erdők közötti áttelepítés. Megoldás: Az Azure AD Connect használata **Forráshorgony konfigurálása** és, ahogy a felhasználó telepíti át, másolja a meglévő ImmutableID értékeket az Azure ad-ből a helyi Active Directory tartományi szolgáltatások felhasználói ms-DS-konzisztencia-Guid attribútum az új erdő. További információkért lásd: [ms-DS-ConsistencyGuid használata sourceanchorként](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Hány felhasználó egy képernyőkép-készítés a nagy méretű ImmutableID frissítései**

  Például az Azure AD Connect megvalósítása során hibázna, és most már a SourceAnchor attribútum módosítani kell. Megoldás: Tiltsa le a DirSync bérlői szinten, és törölje az összes érvénytelen ImmutableID értékét. További információkért lásd: [kapcsolja ki az Office 365-höz a címtár-szinkronizálás](/office365/enterprise/turn-off-directory-synchronization).

- **Az Azure ad-ben meglévő felhasználóval rematch helyszíni felhasználó** például, hogy újra létre az AD DS-ben a felhasználó hoz létre duplikált rematching azt egy olyan meglévő Azure AD-fiókkal (árva objektumra mutat) helyett az Azure AD-fiókot. Megoldás: Az Azure Portalon az Azure AD Connect Health használatával a forrás kapcsolati alappal/ImmutableID újramegfeleltetése. További információkért lásd: [Orphaned objektum forgatókönyvben](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Kompatibilitástörő változás: A naplózási és a bejelentkezési naplók séma keresztül az Azure Monitor frissítései

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

A naplózási és bejelentkezési napló Streamek keresztül az Azure Monitor, jelenleg azt még közzététele, így zökkenőmentesen integrálhatja a naplófájlokat a SIEM-eszközökkel vagy a Log Analytics használatával. Visszajelzését, és ez a szolgáltatás általános rendelkezésre állás hirdetménye előkészítésekor alapján végzünk az alábbi módosításokat a sémának. Sémaváltások és a kapcsolódó dokumentáció frissítéseiről január első hetében szerint fog történni.

#### <a name="new-fields-in-the-audit-schema"></a>A naplózási sémát az új mezők
Adunk hozzá egy új **művelettípus** az erőforráson végrehajtott mezőben adja meg a művelet típusát. Ha például **Hozzáadás**, **frissítés**, vagy **törlése**.

#### <a name="changed-fields-in-the-audit-schema"></a>A naplózási sémában módosult mezők
A naplózási sémában módosítja a következő mezőket:

|Mező neve|Mi változott|Régi értékek|Új érték|
|----------|------------|----------|----------|
|Kategória|Ez volt a **szolgáltatásnév** mező. Most már a **naplózási kategóriát** mező. **Szolgáltatásnév** kapott a **loggedByService** mező.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Az önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazáskezelés</li></ul>|
|targetResources|Tartalmazza a **TargetResourceType** a legfelső szinten.|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Csoport</li></ul>|
|loggedByService|A szolgáltatás által generált a napló nevét itt.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|Az eredményét, a naplók. Korábban ez volt számba, de most bemutatjuk a tényleges érték.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sikeres</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>A bejelentkezési sémában módosult mezők
A bejelentkezési sémában módosítja a következő mezőket:

|Mező neve|Mi változott|Régi értékek|Új érték|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ez volt a **conditionalaccessPolicies** mező. Most már a **appliedConditionalAccessPolicies** mező.|Nem változott|Nem változott|
|conditionalAccessStatus|Az eredményét, a feltételes hozzáférési szabályzat állapota, jelentkezzen be. Korábban ez volt számba, de most bemutatjuk a tényleges érték.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nem alkalmazott</li><li>Letiltva</li></ul>|
|appliedConditionalAccessPolicies: result|Az eredményét, az egyes feltételes hozzáférési szabályzat állapota, jelentkezzen be. Korábban ez volt számba, de most bemutatjuk a tényleges érték.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nem alkalmazott</li><li>Letiltva</li></ul>|

A séma kapcsolatos további információkért lásd: [értelmezése az Azure AD naplózási sémát naplók az Azure Monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>A felügyelt gépi tanulási modell és a kockázatipontszám-motor új Identity Protectionfunkciókkal bővült

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Identity Protection  
**A termék szolgáltatás:** Kockázati pontszámok

Az Identity Protection kapcsolatos felhasználói, és jelentkezzen be kockázati assessment motor fejlesztései segíthet növelni a felhasználói kockázat pontosság és lefedettségét. A rendszergazdák, hogy felhasználói kockázati szint már nem közvetlenül kapcsolódik az adott észlelések kockázati szintjét, és, hogy nincs-e számát és a kockázatos bejelentkezési események szintjét növekedését tapasztalhatja.

Kockázati észlelések most értékeli ki a felügyelt gépi tanulási modellt, amely a felhasználói kockázat további funkcióit a felhasználói bejelentkezéseket és a egy minta kizárását számítja ki. Ez a modell alapján, a rendszergazda szerkeszt magas kockázati pontszámot, a felhasználók akkor is, ha az adott felhasználóhoz rendelt észlelések alacsony vagy közepes kockázat. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák átállíthatják saját jelszavukat a Microsoft Authenticator alkalmazás használatával (nyilvános előzetes verzió)

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék szolgáltatás:** Felhasználók hitelesítése

Az Azure AD-rendszergazdák mostantól alaphelyzetbe állíthatja a saját jelszót, a Microsoft Authenticator alkalmazás értesítéseket vagy egy hitelesítő mobilalkalmazással igazolják vagy hardver kódot token. Saját jelszó a rendszergazdák mostantól fogja tudni használjon két, az alábbi módszerekkel:

- A Microsoft Authenticator alkalmazásban megjelenő értesítésre

- Egyéb mobil hitelesítő alkalmazás / hardver jogkivonat-kód

- E-mail

- Telefonhívás

- Szöveges üzenet

Új jelszót a Microsoft Authenticator alkalmazás használatával kapcsolatos további információkért lásd: [visszaállítása az Azure AD önkiszolgáló jelszó - mobilalkalmazás és az SSPR (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új szerepkör az Azure AD-ben: Felhőbeli eszközrendszergazda (nyilvános előzetes verzió)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Eszközregisztráció és -kezelés  
**A termék szolgáltatás:** Hozzáférés-vezérlés

A rendszergazdák felhasználókat rendelhet az új Felhőbeli Eszközrendszergazda szerepkör eszközt rendszergazdai feladatok végrehajtásához. A felhő eszköz rendszergazdák szerepéhez rendelt felhasználók engedélyezése, tiltsa le, és eszközök törlése az Azure AD-ben együtt képes arra, hogy (ha van ilyen) olvassa el a Windows 10-es BitLocker-kulcsok az Azure Portalon.

További információ a szerepkörökről és engedélyekről: [rendszergazdai szerepkörök hozzárendelése az Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Az eszközök új tevékenység-időbélyeg használatával való kezelése az Azure AD-ben (nyilvános előzetes verzió)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Eszközregisztráció és -kezelés  
**A termék szolgáltatás:** Eszközéletciklus-kezelés

Tisztában vagyunk vele, hogy idővel kell frissíteni, és a szervezet eszközök kivonása az Azure AD-ben a környezetében függő elavult eszközök ne kelljen. Annak érdekében, a folyamattal, most már az Azure AD frissíti az eszközöket egy új tevékenység időbélyeget, annak megakadályozása, hogy az eszközök életciklusának kezelése.

És az időbélyegző a kapcsolatos további információkért lásd: [Útmutató: Az elavult eszközöket kezelheti az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák minden eszközön megkövetelhetik a felhasználóktól a használati feltételek elfogadását

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Szabályozás
 
A rendszergazdák mostantól bekapcsolhatja a **kérése a felhasználóktól minden eszközön jóváhagyást** a felhasználókat, hogy fogadja el a használati feltételeket minden eszközön, akkor használja a bérlő megkövetelése lehetőséggel.

További információkért lásd: a [eszközszintű feltételek az Azure Active Directory – használati feltételek funkció használata szakaszának](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák ismétlődő ütemezés alapján konfigurálhatják a használati feltételek elévülését

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Szabályozás
 

A rendszergazdák mostantól bekapcsolhatja a **címtárbérlőhöz lejár** beállítás, a használati feltételeket a felhasználók a megadott, ismétlődő ütemezésen alapuló összes lejár. Az ütemezés évente, bi évente, negyedévente, vagy lehet havonta. Miután lejár, a használati feltételeket, a felhasználók hatókörébe kell.

További információkért lásd: a [feltételek hozzáadása az Azure Active Directory – használati feltételek funkció használata szakaszának](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák konfigurálhatják a használati feltételeket a felhasználók ütemezés alapján lejár

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék szolgáltatás:** Szabályozás

A rendszergazdák mostantól megadhatják az időtartamot, hogy a felhasználó a használati feltételeket kell hatókörébe. A rendszergazdák például megadhatja, hogy a felhasználók kell hatókörébe használati feltételeket a 90 naponta.

További információkért lásd: a [feltételek hozzáadása az Azure Active Directory – használati feltételek funkció használata szakaszának](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új, Azure Active Directory-szerepkörökkel kapcsolatos e-mailek az Azure AD Privileged Identity Management (PIM) szolgáltatásban

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Az Azure AD Privileged Identity Management (PIM) használó ügyfelek mostantól fogadni tudják az egy heti kivonat e-mailt, többek között az alábbi adatokat az elmúlt hét napban:

- A felső jogosult és állandó szerepkör-hozzárendelések – áttekintés

- Felhasználók, szerepkörök aktiválása

- A PIM-szerepkörökhöz rendelt felhasználók számát

- A PIM-en kívül szerepkörökhöz rendelt felhasználók számát

- Felhasználók "kezdeményezni állandó" a PIM száma

A PIM és a rendelkezésre álló e-mail-értesítések kapcsolatos további információkért lásd: [E-mail-értesítések a PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>A csoportalapú licencelés már általánosan elérhető

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** Címtár

Csoportalapú licencelés kívül esik a nyilvános előzetes és általánosan elérhető. Az általános kiadás részeként azt jobban skálázható adathalmaza e funkció, és újból feldolgozza a Csoportalapú licencelés hozzárendelését az olyan egyetlen lehetősége és használhatja a Csoportalapú licencelés az Office 365 E3 vagy A3 licencek hozzáadása.

Csoportalapú licenceléssel kapcsolatos további információkért lásd: [Mi az a Csoportalapú licencelés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. november

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
2018 November tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 26 új alkalmazásokról:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [szürkével-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [végtelen Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [ HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [eltéréseket](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy vállalati központi 365](https://accounting.zenegy.com/), [Everbridge tag portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti a Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex alkalmazások – Klasszikus teszt](https://test.plexonline.com/signon), [Plex alkalmazások – klasszikus](https://www.plexonline.com/signon), [Plex alkalmazások – felhasználói felület tesztelési](https://test.cloud.plex.com/sso), [Plex alkalmazások – UX](https://cloud.plex.com/sso), [Plex alkalmazások – IAM](https://accounts.plex.com/), [KÉZMŰVES - gyermekápolási rekordok, jelenlét & pénzügyi követési rendszer](https://getcrafts.ca/craftsregistration) 

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD-naplók már együttműködnek az Azure Log Analytics-szel (nyilvános előzetes verzió)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Örömmel jelentjük, hogy most már továbbítás az Azure AD-naplók az Azure Log Analytics! A várt funkció segítségével még jobb hozzáférést biztosíthat a elemzés az üzleti, műveletek, és a biztonsági, valamint az infrastruktúra figyelése a megoldást. További információkért lásd: a [Azure Active Directory-Tevékenységnaplók naplók az Azure Log Analytics mostantól elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. október

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
2018 október tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 14 új alkalmazásokról:

[A díjjal járó pontokat](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 virtuális környezet](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot vezérlő](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services – e-mail-értesítések

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
**A termék szolgáltatás:** Azure AD Domain Services

Az Azure AD Domain Services konfigurációs hibák vagy problémák a felügyelt tartományhoz az Azure Portalon riasztásokat biztosít. Ezek a riasztások tartalmazzák a részletes útmutatókat, így anélkül, hogy forduljon az ügyfélszolgálathoz a probléma javításához próbálkozhat.

Októbertől az lesz az értesítési beállításokat a felügyelt tartományhoz tartozó, amikor új kapcsolatos riasztások történnek, egy e-mailt küld egy kijelölt csoport tagjainak, így nem kell folyamatosan ellenőrzése a portálon a frissítéseket.

További információkért lásd: [értesítési beállítások az Azure AD tartományi szolgáltatásokban](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD-portál támogatja az egyéni tartományok ForceDelete domain API-val való törlését 

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Címtárkezelés  
**A termék szolgáltatás:** Címtár

Örömmel jelentjük, hogy mostantól használhatja a ForceDelete tartomány API aszinkron módon átnevezésével, mint például a felhasználók, csoportok és alkalmazások az egyéni tartománynevet (contoso.com) a biztonsági mutató hivatkozásokat a kezdeti alapértelmezett tartomány nevét (az egyéni tartománynevek törlése contoso.onmicrosoft.com).

Ez a változás segítségével gyorsabban törölni az egyéni tartománynevek, ha a szervezet már nem használja a nevét, vagy ha szeretné használni a tartománynév egy másik Azure AD-val.

További információkért lásd: [egy egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>2018. szeptember
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>A dinamikus csoportok esetében frissültek a rendszergazdai szerepkör engedélyei

**Írja be:** Rögzített  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés

Azt már megtörtént egy probléma javítása, adott rendszergazdai szerepkörök mostantól létrehozhat és frissíthet a dinamikus tagsági szabályok, anélkül, hogy a csoport tulajdonosa legyen.

A szerepkörök a következők:

- Globális rendszergazda vagy a vállalati író

- Intune szolgáltatás rendszergazdája

- Felhasználóifiók-adminisztrátor

További információkért lásd: [állapotának ellenőrzése és a egy dinamikus csoport létrehozása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Néhány külső gyártótól származó alkalmazás esetében egyszerűsödött az egyszeri bejelentkezés (SSO) konfigurálása

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO

Tisztában vagyunk vele, hogy állítja be egyszeri bejelentkezést (SSO) szoftverfrissítési szolgáltatásként (SaaS) alkalmazások kihívást jelenthet az egyes alkalmazások konfiguráció egyedi jellege miatt. Elkésztettünk egy leegyszerűsített konfigurációs élményt biztosít az egyszeri bejelentkezés beállításai a következő külső SaaS-alkalmazások automatikus feltöltéséhez:

- Zendesk

- Az ArcGis Online

- A Jamf Pro

Az egykattintásos felület használatának megkezdéséhez nyissa meg a **az Azure portal** > **egyszeri bejelentkezési konfiguráció** az alkalmazás lapját. További információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Az Azure Active Directory - Where is your data located? (Azure Active Directory – a Microsoft hol tárolja az adataimat?) lap

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** GoLocal

Válassza ki a régiót a vállalat a **Azure Active Directory - hol található a adatait** oldal nézet, mely Azure-beli adatközpont-környezetet tároló az Azure AD-szolgáltatásokhoz az Azure AD inaktív adatokat. Specifikus szerint szűrheti az adatokat az Azure AD-szolgáltatások számára, a vállalat.

Ez a funkció eléréséhez, és további információkért lásd: [Azure Active Directory - hol található a adatait](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új üzembe helyezési terv érhető el a Saját alkalmazások hozzáférési panelen

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**A termék szolgáltatás:** SSO

Tekintse meg az új központi telepítési csomag, amely a saját alkalmazások hozzáférési panelen érhető el (https://aka.ms/deploymentplans).
A saját alkalmazások hozzáférési panel itt rendelkező felhasználók számára egyetlen helyen megtalálhatja és elérheti a saját alkalmazásokban. Ezen a portálon a felhasználók önkiszolgáló lehetőségeket biztosít, például alkalmazások és a csoportok hozzáférést kér vagy erőforrásokhoz való hozzáférés kezelését ezek mások nevében is biztosít.

További információkért lásd: [Mi az a saját alkalmazások portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új lappal (Hibaelhárítás és támogatás) bővült az Azure Portal Bejelentkezések weblapja

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék szolgáltatás:** Monitorozás és jelentéskészítés

Az új **hibaelhárítás és támogatás** lapján a **bejelentkezések** oldal az Azure portal, ismerteti, hogy a rendszergazdák és a támogatási szakértők az Azure AD bejelentkezési kapcsolatos problémák elhárítása. Ezen a lapon biztosítja a hibakódot, hibaüzenet jelenik meg, és korrigálási javaslatokat tesz (ha van ilyen) a probléma megoldásához. Ha nem sikerül a probléma megoldásához, azt is meg kell adni, hozzon létre egy támogatási jegyet az új módon a **példányt vágólapra** észlel, amely feltölti a **Kérelemazonosító** és **dátuma (UTC)** meg a naplófájlt a támogatási jegy mezőket.  

![Jelentkezzen be az új lap naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozására szolgáló egyénibővítmény-tulajdonságok továbbfejlesztett támogatása

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék szolgáltatás:** Együttműködés

Ez a frissítés most kattintson a **egyéni bővítménytulajdonság lekérése** a dinamikus felhasználói csoport szabály builder a hivatkozásra, adja meg az alkalmazás egyedi azonosítója, és kap egy dinamikus létrehozásakor egyéni bővítménytulajdonságok teljes listája Tagsági szabály a felhasználók számára. Ez a lista is frissíthetők úgy, hogy minden olyan új egyéni bővítmény tulajdonságainak lekérése az adott alkalmazáshoz.

A dinamikus tagsági szabályok egyéni bővítménytulajdonságok használatával kapcsolatos további információkért lásd: [bővítmény és egyéni bővítmény tulajdonságai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Azure AD-beli alkalmazásalapú feltételes hozzáférés – a jóváhagyott ügyfélalkalmazások listája új alkalmazásokkal bővült

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék szolgáltatás:** Identitás-biztonság és védelem

A következő alkalmazások olyan listájában [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Az Azure AD, alkalmazásalapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Már támogatott a Windows 7/8/8.1 zárolási képernyőjéről történő önkiszolgáló jelszómódosítás

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
**A termék szolgáltatás:** Felhasználók hitelesítése

Miután beállította az új funkciót, a felhasználók a jelszavuk egy hivatkozás jelenik-e a **zárolási** egy Windows 7, Windows 8 vagy Windows 8.1 rendszerű eszköz képernyőjén. A hivatkozásra kattintva a felhasználó rendszer végigvezeti a azonos jelszóvisszaállítási folyamatot a webböngészőn keresztül.

További információkért lásd: [a Windows 7, 8 és 8.1 jelszóátállítás engedélyezése](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Módosítási értesítés: Engedélyezési kód már nem érhető el használatra 

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ez és egyéb protokollok-vel kapcsolatos módosításokat, lásd: [teljes listája megtalálható az új hitelesítési](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. szeptember

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
2018 szeptember tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet szoftver felvételi](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS vállalati](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [ JDA felhőalapú](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft egyszeri bejelentkezés az Azure-hoz, használható surveymonkey szolgáltatást [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Bővült a támogatott jogcím-átalakítási módszerek köre

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO

Új jogcím átalakítása módszer ToLower() és ToUpper(), amely is lehet alkalmazni a SAML-jogkivonatok az SAML-alapú vezettünk **egyszeri bejelentkezési konfigurációjának** lapot.

További információkért lásd: [az Azure ad-ben a vállalati alkalmazásokhoz SAML-jogkivonatban kiadott jogcímek testreszabása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissült az SAML-alapú alkalmazáskonfigurációs kezelőfelület (előzetes verzió)

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** SSO

A frissített SAML-alapú alkalmazás-konfigurációs felhasználói Felületet részeként láthatja:

- Egy frissített útmutató felületet, az SAML-alapú alkalmazások konfigurálásához.

- Mi az a hiányzó vagy helytelen a konfiguráció a kapcsolatos láthatóságot.

- Lehetővé teszi, hogy az e-mail címeket lejárati tanúsítványt az értesítésre.

- Új jogcím-átalakítási módszerek, ToLower() ToUpper() és még.

- Töltse fel a saját jogkivonat-aláíró tanúsítvány a vállalati alkalmazások lehetőséget.

- Is be lehet állítani az alkalmazások SAML NameID-formátum, és a NameID értéket állítja be Címtárbővítmények módja.

A frissített nézet bekapcsolásához kattintson a **próbálja ki az új funkciót** hivatkozás tetején a **egyszeri bejelentkezés** lap. További információkért lásd: [oktatóanyag: SAML-alapú egyszeri bejelentkezés az alkalmazáshoz az Azure Active Directory konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Módosultak az Azure Active Directory-beli IP-címtartományok

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Egyéb  
**A termék szolgáltatás:** Platform

Nagyobb IP-címtartományok vezetünk be az Azure ad Szolgáltatásba, ami azt jelenti, ha konfigurálta az Azure AD IP-címtartományok esetében a tűzfalak, útválasztók és hálózati biztonsági csoportok kell azokat. Így nem kell a tűzfalat, útválasztó vagy a hálózati biztonsági csoportok IP-címtartomány konfigurációk újra módosítása esetén az Azure AD új végpontjait hozzáadja ezt a frissítést végzünk. 

Hálózati forgalom ezen új tartományok áthelyezése a következő két hónapban. Szolgáltatás megszakításmentes folytatásához, hozzá kell adnia a frissített értékeket az IP-címek 2018. szeptember 10. előtt:

- 20.190.128.0/18 

- 40.126.0.0/18 

Javasoljuk, hogy nem távolítja a régi IP-címtartományokat, mindaddig, amíg az összes, a hálózati forgalom át lett helyezve az új tartományokat. Frissítések információt, és ismerje meg, amikor eltávolíthatja a régi tartományokat, lásd: [Office 365 URL-címei és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Módosítási értesítés: Engedélyezési kód már nem érhető el használatra 

**Írja be:** Tervezett módosítás  
**Szolgáltatás kategóriája:** Hitelesítések (Bejelentkezések)  
**A termék szolgáltatás:** Felhasználók hitelesítése

2018. November 15., kezdve az Azure AD leáll, az alkalmazások korábban használt hitelesítési kódok elfogadásával. Ez a változás segítséget nyújt ahhoz, hogy az Azure AD az OAuth-specifikációnak megfelelően, és alkalmazza a v1 és v2 végpontokon.

Ha az alkalmazás újból felhasználja a jogkivonatok lekérésére, több erőforrás-engedélyezési kódokat, javasoljuk, hogy a kód használatával egy frissítési jogkivonat lekérése, és a frissítési jogkivonat használatával más erőforrások kiegészítő jogkivonatok beszerzéséhez. Engedélyezési kód csak egyszer használhatók fel, de frissítési biztonsági jogkivonat használható többször több erőforrást. Egy alkalmazás, amely megpróbálja újból felhasználhatja a hitelesítési kódot az OAuth hitelesítésikód-folyamata során invalid_grant hiba lép fel.

Ez és egyéb protokollok-vel kapcsolatos módosításokat, lásd: [teljes listája megtalálható az új hitelesítési](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Önkiszolgáló jelszóregisztráció (SSPR) és többtényezős hitelesítés (MFA) – összevont biztonságiadat-kezelés

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
**A termék szolgáltatás:** Felhasználók hitelesítése
 
Ez az új funkció lehetővé teszi a biztonsági adataikat (mint például a telefonszám, mobilalkalmazás és így tovább) kezelheti az SSPR és a egy egyetlen helyen, és felület; MFA képest a korábban, ahol tette két különböző helyen.

SSPR vagy MFA használatával személyek is használható az átszervezett felületet. Ezenkívül ha a szervezet nem kényszeríti a többtényezős hitelesítés vagy az SSPR regisztrációt, személyek továbbra is regisztrálhatja bármilyen MFA vagy az SSPR biztonsági adatai módszer engedélyezett a munkahelyen a saját alkalmazások portálról.

Egy választható nyilvános előzetes kiadásról. A rendszergazdák bekapcsolhatja az új felhasználói felület (ha szükséges) a kijelölt csoport vagy a bérlő összes felhasználója esetében. Konvergens funkciókkal kapcsolatos további információkért lásd: a [Konvergens élmény blog](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új (csak a HTTP használatát támogató) cookie-beállítás az Azure AD Application Proxy-alkalmazásokban

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés

Van egy új nevű beállítása, **HTTP-Only cookie-k** az alkalmazásproxy-alkalmazásokban. Ez a beállítás biztosítja, beleértve a HTTP-válaszfejléc mindkét alkalmazásproxy hozzáférési és munkamenet-cookie-khoz a HTTPOnly jelző, hozzáférés leállítása a cookie-val ügyféloldali parancsfájl és további megakadályozza a műveleteket, például a Másolás adja meg a további biztonsági vagy a cookie-k módosítását. Bár ez a jelző korábban még nem használt, a cookie-kat mindig lettek titkosítva, és nem megfelelő módosításokat elleni védelem érdekében az SSL-kapcsolat használatával.

Ez a beállítás nem kompatibilis az alkalmazások az ActiveX-vezérlők, például a távoli asztal használatával. Ha ebben a helyzetben, azt javasoljuk, hogy kapcsolja ki ezt a beállítást.

A HTTP-Only cookie-kra vonatkozó beállítással kapcsolatos további információkért lásd: [alkalmazások közzététele az Azure AD-alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>A Privileged Identity Management (PIM) az Azure-erőforrások esetében támogatja a felügyeleti csoport típusú erőforrásokat

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Just-In-Time aktiválási és hozzárendelési beállítások is érvényesek a felügyeleti csoport erőforrástípusok, ugyanúgy, mint az előfizetések, erőforráscsoportok és erőforrások (például virtuális gépek, alkalmazásszolgáltatások, és további) már nincs. Ezenkívül bárki egy szerepkör, amely rendszergazdai hozzáférést biztosít a felügyeleti csoport felderítése és kezelése a PIM erőforrás.

A PIM és az Azure-erőforrásokkal kapcsolatos további információkért lásd: [felderítése és az Azure-erőforrások kezelése a Privileged Identity Management használatával](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz.

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
**A termék szolgáltatás:** Privileged Identity Management
 
Jelenleg a PIM használata szerepkör aktiválásakor is igénybe vehet az engedélyek érvénybe több mint 10 perc. Ha úgy dönt, hogy az alkalmazás-hozzáférést, amely jelenleg nyilvános előzetes verzióban érhető el, használja a rendszergazdák hozzáférhetnek az Azure AD portálra, amint az aktiválási kérelem befejeződött.

Jelenleg alkalmazás elérése csak támogatja az Azure AD-portál felületének és az Azure-erőforrások. A PIM és az alkalmazások kapcsolatos további információkért lásd: [Mi az Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD alkalmazáskatalógusában – 2018. augusztus

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék szolgáltatás:** Külső integráció
 
2018 augusztus tettünk elérhetővé az alkalmazáskatalógusban támogatja az összevonási 16 új alkalmazásokról:

[Szarvascsőrűmadár](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [kötetlen Bridgeline](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [mártás Labs - mobil- és webes tesztelés](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta-összekötő hálózatok](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [úgy tesszük](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (által Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentáció](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - kiadás jelentések](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 élő csevegés](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [ Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív Tableau-támogatás már elérhető az Azure AD Application Proxyban

**Írja be:** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
**A termék szolgáltatás:** Hozzáférés-vezérlés

A frissítés az OpenID Connect, az üzem előtti hitelesítési protokoll az OAuth 2.0-s Kódmegadás protokoll már nem kell az alkalmazásproxy használatával a Tableau használandó további konfigurációt elvégezni. Ez a protokoll változás is segít alkalmazásproxy hatékonyabban támogatják a több modern alkalmazások csak a HTTP átirányítást, gyakran támogatott JavaScript és HTML-címkék használatával.

A natív módon támogatja a Tableau kapcsolatos további információkért lásd: [mostantól natív Tableau támogatásával az Azure AD-alkalmazásproxy](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Már támogatott a Google identitásszolgáltatóként való beállítása a vállalatközi vendégfelhasználók számára (előzetes verzió)

**Írja be:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék szolgáltatás:** B2B/B2C

A szervezet a Google összevonási beállításával engedélyezheti, meghívott Gmail felhasználók bejelentkezési a megosztott alkalmazások és erőforrások a meglévő Google-fiók létrehozása a személyes Microsoft-Account (msa-k) vagy egy Azure AD-fiók nélkül.

Egy választható nyilvános előzetes kiadásról. Google összevonási kapcsolatos további információkért lásd: [Identitásszolgáltatóként B2B vendégfelhasználó hozzáadása Google](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---
