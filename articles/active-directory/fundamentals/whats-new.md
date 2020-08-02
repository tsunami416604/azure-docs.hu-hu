---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft Docs
description: Ismerje meg a Azure Active Directory újdonságait, például a legújabb kibocsátási megjegyzéseket, ismert problémákat, hibajavításokat, elavult funkciókat és a közelgő változásokat.
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc316db0146943113b97b680229bd113f6fc1de5
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494808"
---
# <a name="whats-new-in-azure-active-directory"></a>A Azure Active Directory újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` az ![ RSS-hírcsatorna olvasójának ikonját a ](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasóba.

Az Azure AD folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---

## <a name="july-2020"></a>2020. július

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Rendszergazdaként feltételes hozzáféréssel szeretném megcélozni az ügyfélalkalmazások használatát

**Írja be a következőt:** Tervezze meg a változást   
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
Ha a feltételes hozzáférésben az ügyfélalkalmazások jelennek meg, az új szabályzatok alapértelmezés szerint minden ügyfélalkalmazás számára érvényesek lesznek, beleértve az örökölt hitelesítési ügyfeleket is. A meglévő szabályzatok változatlanok maradnak, de a *configure igen/No* váltógomb el lesz távolítva a meglévő szabályzatokból, így könnyen látható, hogy mely ügyfélalkalmazások lesznek alkalmazva a szabályzat által. 

Új szabályzat létrehozásakor ügyeljen arra, hogy kizárjon egy örökölt hitelesítést használó felhasználókat és szolgáltatásfiókokat; Ha nem, akkor le lesznek tiltva. [További információ](https://aka.ms/caclientapps).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Közelgő SCIM-megfelelőségi javítások

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure AD-kiépítési szolgáltatás a SCIM szabványt használja az alkalmazásokkal való integrációhoz. A SCIM standard implementációja fejlődik, és elvárjuk, hogy módosítjuk a javítási műveleteket, valamint az "Active" tulajdonságot egy erőforráson. [További információ](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>A csoport tulajdonosának beállítása az Azure felügyeleti portálon módosítva lesz

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

A csoportok általános beállítási lapján beállíthatja, hogy a tulajdonos-hozzárendelési jogosultságokat egy korlátozott felhasználói csoportra korlátozza az Azure felügyeleti portál és a hozzáférési panel. Hamarosan lehetőség van arra, hogy a csoport tulajdonosi jogosultságait ne csak a két UX-portálon lehessen hozzárendelni, hanem a háttérbeli szabályzatot is kikényszeríteni, hogy konzisztens viselkedést biztosítson a végpontok, például a PowerShell és a Microsoft Graph között. 

Elkezdjük letiltani a jelenlegi beállítást azon ügyfelek esetében, akik nem használják azt, és a következő néhány hónapban lehetőséget biztosítanak a csoport tulajdonosi jogosultságának hatókörére a felhasználók számára. A csoportok beállításainak frissítésével kapcsolatos útmutatásért lásd: a csoport adatainak szerkesztése [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)használatával.

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory regisztrációs szolgáltatás befejezte a TLS 1,0 és 1,1 támogatását

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Platform

A Transport Layer Security (TLS) 1,2 és a frissítési kiszolgálók és ügyfelek hamarosan Azure Active Directory Eszközregisztráció szolgáltatással fognak kommunikálni. Az Azure AD-eszközök regisztrálási szolgáltatásával folytatott kommunikációhoz használt TLS 1,0 és 1,1 támogatása a következő módon fog kivonulni:
- 2020. augusztus 31-én, minden szuverén felhőkben (GCC High, DoD stb.)
- 2020. október 30-án minden kereskedelmi felhőben

[További](https://docs.microsoft.com/azure/active-directory/devices/reference-device-registration-tls-1-2) információ az Azure ad regisztrációs szolgáltatás TLS 1,2-ről.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>A vállalati Windows Hello-bejelentkezések az Azure AD bejelentkezési naplóiban láthatók

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
A vállalati Windows Hello lehetővé teszi a végfelhasználók számára, hogy egy kézmozdulattal (például PIN-kóddal vagy biometrikus azonosítóval) jelentkezzenek be a Windows rendszerű gépekre. Előfordulhat, hogy az Azure AD-rendszergazdák meg szeretnék különböztetni a vállalati Windows Hello-bejelentkezéseket más Windows-bejelentkezésekhez a szervezetnek a jelszó nélküli hitelesítéshez való hozzáférése részeként. 

A rendszergazdák mostantól láthatják, hogy egy Windows-hitelesítés használta-e a vállalati Windows Hello-t az Azure Portalon az Azure AD-bejelentkezések paneljén egy Windows bejelentkezési esemény hitelesítés részletei lapján. A vállalati Windows Hello hitelesítés a hitelesítési módszer mezőben a "WindowsHelloForBusiness" kifejezést fogja tartalmazni. A bejelentkezési naplók értelmezésével kapcsolatos további információkért tekintse meg a [bejelentkezési naplók dokumentációját](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Javítások a csoportok törlési viselkedésére és a teljesítmény javítására

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Korábban, amikor egy csoport a "hatókörből" a "hatókörön kívülre" módosult, és egy rendszergazda a módosítás befejeződése előtt újraindult, a csoport objektum nem lett törölve. Most a Group objektum törlődik a célalkalmazás területéről, ha a hatókörön kívül esik (letiltva, törölve, nincs hozzárendelve vagy nem adta meg a hatókör-szűrőt). [További információ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Nyilvános előzetes verzió: a rendszergazdák mostantól hozzáadhatnak egyéni tartalmat az e-mailben a felülvizsgálók számára hozzáférési felülvizsgálat létrehozásakor

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás
 
Új hozzáférési felülvizsgálat létrehozásakor a felülvizsgáló e-mailt kap, amely a hozzáférési felülvizsgálat befejezését kéri. Számos ügyfelünk arra kérte a felhasználókat, hogy egyéni tartalmakat adjanak hozzá az e-mailekhez, például kapcsolattartási adatokhoz vagy egyéb kiegészítő tartalomhoz a felülvizsgáló irányításához. 

A nyilvános előzetes verzióban elérhető rendszergazdák megadhatnak egyéni tartalmakat a felülvizsgálók számára az Azure AD hozzáférési felülvizsgálatok "speciális" szakaszában található tartalom hozzáadásával. A hozzáférési felülvizsgálatok létrehozásával kapcsolatos útmutatásért lásd: [csoportok és alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)felülvizsgálatokban.
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Engedélyezési kód folyamatábrája elérhető egyoldalas alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Fejlesztői élmény
 
A modern böngésző harmadik féltől származó cookie-korlátozásai (például a Safari ITP) miatt a fürdőhelyeknek az engedélyezési kód folyamatát kell használniuk az egyszeri bejelentkezés fenntartása helyett, és a MSAL.js v 2. x mostantól támogatja az engedélyezési kód folyamatát. 

A Azure Portal megfelelő frissítései vannak, így frissítheti a SPA-t a "Spa" típusra, és használhatja az Auth Code flow-t. További útmutatásért lásd: [bejelentkezési felhasználók és hozzáférési token beszerzése egy JavaScript Spa-beli hitelesítési kód használatával](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Az Azure AD Application Proxy mostantól támogatja a Távoli asztali szolgáltatások webes ügyfélprogramot

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Az Azure AD Application Proxy mostantól támogatja a Távoli asztali szolgáltatások (RDS) webes ügyfélprogramot. Az RDS webes ügyfélprogram lehetővé teszi, hogy a felhasználók bármilyen HTLM5-kompatibilis böngészővel hozzáférhessenek Távoli asztal infrastruktúrához, például a Microsoft Edge, az Internet Explorer 11, a Google Chrome stb. A felhasználók ugyanúgy kezelhetik a távoli alkalmazásokat vagy asztalokat, mint a helyi eszközökkel. Az Azure AD Application Proxy használatával növelheti az RDS-környezet biztonságát úgy, hogy az előhitelesítést és a feltételes hozzáférési szabályzatokat minden különféle típusú ügyfélalkalmazás számára kikényszeríti. Útmutatásért lásd: [Távoli asztal közzététele az Azure ad Application proxyban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>A következő generációs Azure AD B2C felhasználói folyamatok nyilvános előzetes verziója

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Az egyszerűsített felhasználói folyamat az előzetes verziójú funkciókkal rendelkező funkciók paritását kínálja, és az összes új funkció otthona. A felhasználók az azonos felhasználói folyamaton belül is engedélyezhetik az új szolgáltatásokat, ami csökkenti a több verzió létrehozásának szükségességét minden új szolgáltatás kiadásával. Végül az új, felhasználóbarát UX leegyszerűsíti a felhasználói folyamatok kiválasztását és létrehozását. Próbálja ki most [egy felhasználói folyamat létrehozásával](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows). 

További információ a felhasználók forgalmáról: [felhasználói folyamatok verziói Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-versions#:~:text=%20%20%20%20User%20flow%20%20%2caccount.%20Usi%20...%20%201%20more%20rows%20).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – július 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 júliusában a következő 55 új alkalmazásokat bővítettük az alkalmazás-galériában az összevonási támogatással:

[Taps a kezed](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor](https://inexto.com/inexto-suite/inextor)-tár [, Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepciós](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [Coins Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [reflekt](https://reflekt.konsolute.com/login), [álmodozás](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](https://docs.microsoft.com/azure/active-directory/saas-apps/titanfile-tutorial), [Wootric](https://docs.microsoft.com/azure/active-directory/saas-apps/wootric-tutorial), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), Opentext [Directory Services](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) [, Datasite,](https://docs.microsoft.com/azure/active-directory/saas-apps/datasite-tutorial) [BlogIn](https://docs.microsoft.com/azure/active-directory/saas-apps/blogin-tutorial) [, INTSIGHTS,](https://docs.microsoft.com/azure/active-directory/saas-apps/intsights-tutorial) [Kpifire,](https://docs.microsoft.com/azure/active-directory/saas-apps/kpifire-tutorial) [Textline](https://docs.microsoft.com/azure/active-directory/saas-apps/textline-tutorial), [Cloud Academy-SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-academy-sso-tutorial), [közösségi Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/community-spark-tutorial), [Chatwork](https://docs.microsoft.com/azure/active-directory/saas-apps/chatwork-tutorial) [, CloudSign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudsign-tutorial), [C3M Cloud Control](https://docs.microsoft.com/azure/active-directory/saas-apps/c3m-cloud-control-tutorial), [SmartHR](https://smarthr.jp/), [NumlyEngage™](https://docs.microsoft.com/azure/active-directory/saas-apps/numlyengage-tutorial), [Michigan adatközpont egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/michigan-data-hub-single-sign-on-tutorial), [kimenő](https://docs.microsoft.com/azure/active-directory/saas-apps/egress-tutorial)forgalom [, SendSafely](https://docs.microsoft.com/azure/active-directory/saas-apps/sendsafely-tutorial), [Eletive](https://app.eletive.com/), [jobb oldali kiberbiztonsági ADI](https://right-hand.ai/), [Fyde nagyvállalati hitelesítés](https://enterprise.fyde.com/), verme, [Lenses.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/lensesio-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/verme-tutorial) [Momenta](https://docs.microsoft.com/azure/active-directory/saas-apps/momenta-tutorial), [növekedés](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudcords-tutorial), [mond robot](https://tellme365liteweb.azurewebsites.net/), [INSPIRE](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML-összekötő](https://www.strata.io/identity-fabric/), [Smartschool (iskolai felügyeleti rendszer)](https://smart-schoolapp.com/frmLoginForm), [Zepto-intelligens időmérő](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](https://docs.microsoft.com/azure/active-directory/saas-apps/skedda-tutorial), [WhosOnLocation](https://docs.microsoft.com/azure/active-directory/saas-apps/whos-on-location-tutorial), [Coggle](https://docs.microsoft.com/azure/active-directory/saas-apps/coggle-tutorial), [Kemp Loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/browserstack-single-sign-on-tutorial)

Itt megtalálhatja az összes alkalmazás dokumentációját ishttps://aka.ms/AppsTutorial

Az alkalmazás Azure AD-katalógusban való listázásához tekintse meg a részleteket itt:https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – július 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció

Mostantól automatizálhatja az újonnan integrált alkalmazás [LinkedIn learning](https://docs.microsoft.com/azure/active-directory/saas-apps/linkedin-learning-provisioning-tutorial)felhasználói fiókjainak létrehozását, frissítését és törlését.

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Szerepkör-hozzárendelések megtekintése az összes hatókörben és a fájlok CSV-fájlba való letöltésének lehetősége

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Most már megtekintheti a szerepkör-hozzárendeléseket az összes hatókörben az Azure AD-portál "szerepkörök és rendszergazdák" lapján. Az egyes szerepkörökhöz tartozó szerepkör-hozzárendeléseket egy CSV-fájlba is letöltheti. A szerepkör-hozzárendelések megtekintésével és hozzáadásával kapcsolatos útmutatásért lásd: [rendszergazdai szerepkörök megtekintése és hozzárendelése a Azure Active Directory-ben](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication szoftverfejlesztés (Azure MFA SDK) – elavult

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
Az Azure Multi-Factor Authentication szoftverfejlesztés (Azure MFA SDK) 2018 november 14-én ért véget az élet végére, amely az első, 2017. novemberében jelent meg. A Microsoft az SDK-szolgáltatást a 2020-es szeptember 30-ig érvénybe lép. Az SDK-ra irányuló hívások sikertelenek lesznek.

Ha a szervezete az Azure MFA SDK-t használja, a 2020 szeptember 30-ig kell áttelepítenie:
- Azure MFA SDK a következőhöz: Ha az SDK-t a webplatformon használja, telepítse át az Azure MFA-kiszolgálóra, és aktiválja Privileged Access Management (PAM) műveletet az alábbi [utasítások](https://docs.microsoft.com/microsoft-identity-manager/working-with-mfaserver-for-mim)követésével.   
- Azure MFA SDK testreszabott alkalmazásokhoz: érdemes lehet integrálni az alkalmazást az Azure AD-be, és feltételes hozzáféréssel használni az MFA betartatására. Az első lépésekhez tekintse át ezt a [lapot](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration). 

---

## <a name="june-2020"></a>2020. június 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Felhasználói kockázati feltétel a feltételes hozzáférési házirendben

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 

Az Azure AD feltételes hozzáférési szabályzatának felhasználói kockázati támogatása lehetővé teszi több felhasználói kockázati alapú házirend létrehozását. Különböző felhasználók és alkalmazások esetében különböző minimális felhasználói kockázati szintek szükségesek. A felhasználói kockázat alapján házirendeket hozhat létre a hozzáférés letiltásához, a többtényezős hitelesítés megköveteléséhez, a jelszavak biztonságos módosításához, vagy a munkamenet-szabályzat érvényesítéséhez Microsoft Cloud App Security átirányításához, például további naplózáshoz.

A felhasználói kockázati feltétel prémium szintű Azure AD P2-t igényel, mivel az Azure Identity Protectiont használja, amely P2-ajánlat. a feltételes hozzáféréssel kapcsolatos további információkért tekintse meg az [Azure ad feltételes hozzáférési dokumentációját](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Az SAML SSO mostantól támogatja azokat az alkalmazásokat, amelyekhez szükség van a SPNameQualifier beállítására

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Egyes SAML-alkalmazások esetén SPNameQualifier kell visszaadni az állítás tárgyában, ha erre szükség van. Most az Azure AD megfelelően reagál, ha a kérelem NameID szabályzata SPNameQualifier kér. Ez az SP által kezdeményezett bejelentkezéshez is használható, és a identitásszolgáltató kezdeményezett bejelentkezés a következő lesz:.  Ha többet szeretne megtudni a Azure Active Directory SAML protokollról, tekintse meg az [egyszeri bejelentkezés SAML protokollját](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Az Azure AD B2B együttműködés támogatja a MSA és a Google-felhasználók meghívását Azure Government bérlők számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

A B2B csoportmunka-szolgáltatásokat használó bérlők a Microsoft-vagy Google-fiókkal rendelkező felhasználókat is meghívhatják Azure Government. Annak megállapításához, hogy a bérlő használhatja-e ezeket a képességeket, kövesse az utasításokat, hogyan állapítható meg, hogy a [B2B együttműködés elérhető-e az Azure US government-bérlőben?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Az MS Graph v1 felhasználói objektuma mostantól tartalmazza a externalUserState és a externalUserStateChangedDateTime tulajdonságokat

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

A externalUserState és a externalUserStateChangedDateTime tulajdonsággal megkeresheti azokat a meghívott B2B vendégeket, akik még nem fogadták el a meghívókat, valamint olyan automatizálást is készíthetnek, mint például olyan felhasználók törlése, akik nem fogadták el a meghívókat néhány nap Ezek a tulajdonságok mostantól elérhetők az MS Graph v1-ben. A tulajdonságok használatával kapcsolatos útmutatásért tekintse meg a [felhasználói erőforrás típusát](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Mostantól általánosan elérhető a hitelesítési munkamenetek kezelése az Azure AD feltételes hozzáférésben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
A hitelesítési munkamenet-kezelési funkciók lehetővé teszik annak a konfigurálását, hogy a felhasználóknak milyen gyakran kell megadniuk a bejelentkezési hitelesítő adatokat, és hogy szükségük van-e a hitelesítő adatok megadására a böngészők bezárása és újbóli megnyitása után, hogy nagyobb biztonságot és rugalmasságot nyújtsanak
 
Emellett a hitelesítési munkamenet-kezelés csak az Azure AD-hez csatlakoztatott, a hibrid Azure AD-hez csatlakoztatott és az Azure AD által regisztrált eszközök első tényezős hitelesítésére vonatkozik. A hitelesítési munkamenetek kezelése mostantól az MFA-ra is érvényes lesz. További információ: [a hitelesítési munkamenetek kezelésének beállítása feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – június 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 júniusában a következő 29 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [szegmens](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca-kapcsolat](https://app.pobu.ca/), [proto.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [forgalomirányító](https://www.gatekeeperhq.com/), [hub Planner](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [Ansira-partner piacra](https://ansira.com/technology/channel-engagement)jutási eszközkészlet, [IBM digitális üzleti automatizálás felhőben](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [kisi fizikai biztonság](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [ViewpointOne](https://team.viewpoint.com/), [INTELLIGENCEBANK](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [Pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [nulla](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [edX for Business SAML 2,0 integráció](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [mező azonosítója](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial), [tananyag SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [Perforce Helix Core-Helix hitelesítési szolgáltatás](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial), MyCompliance- [felhő](https://cloud.metacompliance.com/), Smallstep [SSH](https://smallstep.com/sso-ssh/)  

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial . Az alkalmazás Azure AD-katalógusban való listázásához tekintse meg a részleteket itt: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>A külső identitások önkiszolgáló regisztrációjának API-összekötői mostantól nyilvános előzetes verzióban érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
A külső identitások API-összekötői lehetővé teszik a webes API-k használatát a külső felhőalapú rendszerekkel való önkiszolgáló regisztráció integrálásához. Ez azt jelenti, hogy mostantól a webes API-kat egy regisztrációs folyamat adott lépéseként hívhatja meg a felhőalapú egyéni munkafolyamatok elindításához. Az API-összekötők például a következőket használhatják:

- Integrálás egyéni jóváhagyási munkafolyamatokkal.
- Személyazonosság-ellenőrzés végrehajtása
- Felhasználói bemeneti adatok érvényesítése
- Felhasználói attribútumok felülírása
- Egyéni üzleti logika futtatása

További információ az API-összekötők által elérhető összes élményről: [API-összekötők használata az önkiszolgáló regisztráció testreszabásához és kiterjesztéséhez](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview), illetve [külső identitások önkiszolgáló regisztrációjának testreszabása webes API-integrációkkal](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Igény szerinti kiépítés és felhasználók beszerzése az alkalmazásokba másodpercek alatt

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure AD-kiépítési szolgáltatás jelenleg ciklikus alapon működik. A szolgáltatás 40 percenként fut. Az [igény szerinti üzembe](https://aka.ms/provisionondemand) helyezési funkció lehetővé teszi, hogy kiválasszon egy felhasználót, és másodpercek alatt kiépítse őket. Ez a funkció lehetővé teszi a kiépítési problémák gyors elhárítását anélkül, hogy újra kell indítani a kiépítési ciklust, hogy újra lehessen kezdeni. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Új engedély az Azure AD-jogosultságok kezeléséhez gráfban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Jogosultságok kezelése
 
Egy új delegált engedély EntitlementManagement. Read. All már használható a jogosultsági felügyeleti API-hoz a Microsoft Graph Beta-ban. További információ az elérhető API-król: [Az Azure ad jogosultság-kezelési API használata](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>A 1.0-s verzióban elérhető Identity Protection API-k

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme
 
A riskyUsers és a riskDetections Microsoft Graph API-k már általánosan elérhetők. Most, hogy elérhetők a 1.0-s végponton, meghívja Önt, hogy éles környezetben használja őket. További információkért tekintse meg a [Microsoft Graph dokumentációját](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Mostantól általánosan elérhetők a szabályzatok Microsoft 365 csoportokra való alkalmazásának érzékenységi címkéi

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés
 

Mostantól érzékenységi címkéket is létrehozhat, és a címke beállításaival házirendeket alkalmazhat Microsoft 365 csoportokra, beleértve az adatvédelmi (nyilvános vagy magánjellegű) és a külső felhasználói hozzáférési házirendet. Létrehozhat egy címkét az adatvédelmi szabályzattal, hogy magánjellegű legyen, és a külső felhasználói hozzáférési házirend ne engedélyezze a vendég felhasználók hozzáadását. Ha a felhasználó ezt a címkét egy csoportra alkalmazza, a csoport privát lesz, és a csoportba nem vehetők fel vendég felhasználók. 

Az érzékeny címkék fontosak az üzleti szempontból kritikus fontosságú adatmennyiségek védelme érdekében, és lehetővé teszik a csoportok megfelelő és biztonságos módon történő kezelését. Az érzékenységi címkék használatával kapcsolatos útmutatásért lásd [: érzékenységi címkék társítása az Office 365-csoportokhoz Azure Active Directory (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>A prémium szintű Azure AD ügyfelek Microsoft Identity Manager támogatásának frissítései

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Microsoft Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure-támogatás mostantól elérhető a Microsoft Identity Manager 2016 Azure AD integrációs összetevői számára, a Microsoft Identity Manager 2016 kiterjesztett támogatásának végéig. További információ: [prémium szintű Azure ad ügyfelek támogatása Microsoft Identity Manager használatával](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>A csoporttagság feltételeinek használata az SSO-jogcímek konfigurációjában nagyobb

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Korábban a jogcímek a csoporttagság alapján való feltételeit egy adott alkalmazás konfigurációjában legfeljebb 10 értékre lehet használni. Az SSO-jogcímek konfigurációjában a csoporttagság feltételeinek használata már legfeljebb 50 csoportra nőtt. A jogcímek konfigurálásával kapcsolatos további információkért tekintse meg a [vállalati alkalmazások SSO-jogcímek konfigurálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions)című témakört. 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Alapszintű formázás engedélyezése a bejelentkezési oldal Text összetevőjében a vállalati arculatban.

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A vállalati védjegyezési funkció az Azure AD/Microsoft 365 bejelentkezési élményben frissült, így lehetővé teszi az ügyfél számára a hiperhivatkozások és egyszerű formázás hozzáadását, beleértve a félkövér betűtípust, az aláhúzást és a dőlt betűket. A funkció használatáról a következő témakörben talál útmutatást: [branding hozzáadása a szervezet Azure Active Directory bejelentkezési lapjához](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Teljesítménybeli javítások kiépítés

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
A kiépítési szolgáltatás frissítve lett, hogy csökkentse a [növekményes ciklus](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) befejezésének idejét. Ez azt jelenti, hogy a felhasználók és a csoportok gyorsabban lesznek kiépítve az alkalmazásba, mint korábban. Az 6/10/2020-es után létrehozott összes új kiépítési feladat automatikusan kihasználja a teljesítménybeli javításokat. A 6/10/2020 előtti üzembe helyezéshez konfigurált összes alkalmazást újra kell indítani a 6/10/2020 után, hogy kihasználhassa a teljesítmény javításait. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>A ADAL és az MS Graph paritás érvénytelenítésének bejelentése

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Eszközök életciklusának kezelése

Most, hogy elérhetővé vált a Microsoft Authentication librarys (MSAL), már nem fogunk új funkciókat hozzáadni a Azure Active Directory Authentication librarys (ADAL) szolgáltatáshoz, és a biztonsági javítások 2022. június 30-án fognak megjelenni. A MSAL-re való áttelepítéssel kapcsolatos további információkért tekintse át az [alkalmazások áttelepítését a Microsoft Authentication Library (MSAL) webhelyre](https://docs.microsoft.com/azure/active-directory/develop/msal-migration)című témakört.

Emellett elvégezte a munkát, hogy az összes Azure AD Graph-funkciót elérhetővé tegye az MS Graph használatával. Így az Azure AD Graph API-k csak a hibajavítási és biztonsági javításokat kapják meg a 2022. június 30-ig. További információ: [alkalmazások frissítése a Microsoft Authentication Library és a Microsoft Graph API használatára](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>2020. május

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>A bejelentkezések, a riskyUsers és a riskDetections API-k tulajdonságainak kivonása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Jelenleg az enumerált típusok a riskDetections API és a riskyUserHistoryItem (előzetes verzió) riskType tulajdonságának ábrázolására szolgálnak. Az enumerált típusok a bejelentkezések API riskEventTypes tulajdonságához is használatosak. Ezek a tulajdonságok karakterláncként lesznek ábrázolva. 

Az ügyfeleknek a Beta riskDetections és a riskyUserHistoryItem API riskEventType tulajdonságára kell áttérniük, és a Beta bejelentkezések API-ban riskEventTypes_v2 tulajdonságot kell megjelenniük, a 2020. szeptember 9-én. Ezen a napon az aktuális riskType-és riskEventTypes-tulajdonságokat fogjuk kivonásra. További információkért tekintse meg a [kockázati esemény tulajdonságai és az Identity Protection API-k Microsoft Graph-on való módosítását](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)ismertető témakört.

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>A riskEventTypes tulajdonság elavult a bejelentkezések v 1.0 API-n Microsoft Graph

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-biztonsági & védelme

A felsorolt típusok karakterlánc-típusokra válthatnak, amikor a kockázati esemény tulajdonságait jelképezik Microsoft Graph szeptember 2020. Az előzetes verziójú API-k hatása mellett ez a változás is hatással lesz a üzemi bejelentkezések API-ra.

Bevezetünk egy új riskEventsTypes_v2 (string) tulajdonságot a bejelentkezések v 1.0 API-hoz. Az aktuális riskEventTypes (Enum) tulajdonságot 2022 június 11-én kivonják a Microsoft Graph elavult szabályzatnak megfelelően. Az ügyfeleknek a v 1.0 bejelentkezések API riskEventTypes_v2 tulajdonságára kell áttérniük a 2022. június 11-én. További információkért tekintse meg a [riskEventTypes tulajdonság elavulttá tételét a bejelentkezések v 1.0 API-n a Microsoft Graphon](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Az MFA e-mail értesítéseinek közelgő változásai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 

A következő módosításokat hajtjuk végre a Cloud MFA-hoz tartozó e-mail-értesítések esetében:

Az e-mail-értesítések a következő címről lesznek elküldve: azure-noreply@microsoft.com és msonlineservicesteam@microsoftonline.com . Frissítjük a csalások riasztási e-mailek tartalmát, hogy jobban jelezzék a felhasználás feloldásához szükséges lépéseket.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Új önkiszolgáló regisztráció az összevont tartományokban lévő felhasználók számára, akik nem férnek hozzá a Microsoft Teams szolgáltatáshoz, mert nem szinkronizálhatók a Azure Active Directory.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 

Jelenleg az Azure AD-ben összevont tartományokba tartozó, de a bérlőbe nem szinkronizált felhasználók nem férhetnek hozzá a csapatokhoz. Június végétől kezdve ez az új funkció lehetővé teszi számukra, hogy kiterjessze a meglévő e-mailek ellenőrzött regisztrációs funkcióját. Ez lehetővé teszi, hogy a felhasználók egy összevont identitásszolgáltató jelentkezzenek be, de még nem rendelkeznek felhasználói objektummal az Azure ID-ben, hogy a felhasználói objektum automatikusan létrejöjjön, és a csapatok hitelesítve legyenek. A felhasználói objektum "önkiszolgáló regisztráció" állapotúként lesz megjelölve. Ez a meglévő, az e-mailek ellenőrzésére szolgáló lehetőség kiterjesztése, amelyet a felügyelt tartományokban lévő felhasználók megtehetnek, és ugyanazzal a jelzővel vezérelhetők. Ez a változás a következő két hónap során fejeződik be. Tekintse meg a dokumentációs frissítéseket [itt](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Közelgő javítás: a Azure Government-felhő OIDC-felderítési dokumentumának frissítése folyamatban van a megfelelő gráf-végpontokra való hivatkozáshoz.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Szuverén felhők  
A **termék képességei:** Felhasználói hitelesítés
 
Júniusban a OIDC-felderítési dokumentum a [Microsoft Identity platform és az OpenID Connect protokoll](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) a [Azure Government cloud](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) Endpoint (login.microsoftonline.us) alkalmazásban megkezdi a megfelelő [nemzeti Cloud Graph](https://docs.microsoft.com/graph/deployments) -végpont ( https://graph.microsoft.us vagy https://dod-graph.microsoft.us) a megadott bérlő alapján) visszaadását.  Jelenleg a helytelen gráf végpont (graph.microsoft.com) "msgraph_host" mezőt biztosítja.  

Ez a hibajavítás fokozatosan, körülbelül 2 hónapig lesz kivezetve.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government felhasználók többé nem fognak tudni bejelentkezni a login.microsoftonline.com

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Szuverén felhők  
A **termék képességei:** Felhasználói hitelesítés
 
2018. június 1-jén a Azure Government hivatalos Azure Active Directory (HRE) hatósága a verzióról a verzióra változott https://login-us.microsoftonline.com https://login.microsoftonline.us . Ha egy Azure Government bérlőn belül egy alkalmazás tulajdonosa, frissítenie kell az alkalmazást, hogy a. us végponton jelentkezzen be a felhasználókba.

Május 5. után az Azure AD megkezdi a végpontok módosításának kényszerítését, és blokkolja Azure Government a felhasználókat abban, hogy a nyilvános végponttal (microsoftonline.com) Azure Government bérlőben üzemeltetett alkalmazásokba jelentkezzenek be. Az érintett alkalmazások megkezdik a AADSTS900439-USGClientNotSupportedOnPublicEndpoint hibájának megadását. 

Ennek a változásnak a fokozatos bevezetését kell végrehajtania, és a kényszerítést a 2020. június összes alkalmazásában el kell végezni. További részletekért tekintse meg a [Azure Government blogbejegyzést](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>Az SAML egyszeri kijelentkezési kérelem most a megfelelő formátumban küldi el a NameID

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Ha a felhasználó a kijelentkezésre kattint (például a MyApps-portálon), az Azure AD SAML egyszeri kijelentkezési üzenetet küld minden olyan alkalmazásnak, amely aktív a felhasználói munkamenetben, és a kijelentkezési URL-cím konfigurálva van. Ezek az üzenetek állandó formátumú NameID tartalmaznak.

Ha az eredeti SAML-bejelentkezési jogkivonat más formátumot használ a NameID (például e-mail-cím/UPN), akkor az SAML-alkalmazás nem tudja összekapcsolni a kijelentkezési üzenetben szereplő NameID egy meglévő munkamenetben (mivel a mindkét üzenetben használt NameIDs eltér), ami miatt a kijelentkezési üzenetet az SAML-alkalmazás elveti, és a felhasználó bejelentkezett marad. Ez a javítás a kijelentkezési üzenetet az alkalmazáshoz konfigurált NameID összhangban teszi.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>A hibrid identitás-rendszergazdai szerepkör mostantól elérhető a felhőalapú kiépítés során

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD-beli felhőalapú kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
A rendszergazdák az Azure ADConnect Cloud-kiépítés beállításakor az új "hibrid rendszergazda" szerepkört is megkezdhetik a legkevésbé Kiemelt szerepkörbe. Ezzel az új szerepkörrel már nem kell a globális rendszergazdai szerepkört használnia a felhőalapú kiépítés beállításához és konfigurálásához. [További információ](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – május 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
A 2020-es verzióban az alábbi 36 új alkalmazások is elérhetők az alkalmazás-galériában az összevonási támogatással:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [Asc rögzítési ismeretek](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engagement](https://b-engaged.se/), [Competella Contact Center munkacsoportja](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft-identitás](https://identity.imagesoftinc.com/), [saját IBISWorld](https://identity.imagesoftinc.com/), [insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyara CX megbízhatósági platform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [intelligens globális irányítás](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial), [Prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [Mapbox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [Datava Enterprise Service platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [szeszélyes](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [EasySSO for torkolatánál](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO for BitBucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), EasySSO [for Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Torii,](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial) [Axiad felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial), [humán](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial), [ColorTokens ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial), [CCH Tagetik](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial), [ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial), [Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial), [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial), [bárki otthoni CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial), [askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial), [Ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial .

Az alkalmazás Azure AD-katalógusban való listázásához tekintse meg a részleteket itt https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>A feltételes hozzáféréshez már általánosan elérhető a csak jelentés üzemmód

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az [Azure ad feltételes hozzáféréshez csak jelentési mód](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) lehetővé teszi a szabályzatok eredményének kiértékelését a hozzáférés-vezérlés kényszerítése nélkül. A szervezeten belül csak a jelentésekre vonatkozó házirendeket lehet tesztelni, és megismerni a hatásukat, mielőtt engedélyezné őket, így biztonságosabbá és egyszerűbbé teheti a telepítést. Az elmúlt néhány hónapban már láttuk a csak jelentési mód erős bevezetését – a 26M-felhasználókra már csak jelentésekre vonatkozó szabályzatok tartoznak. A bejelentéssel az új Azure AD feltételes hozzáférési szabályzatok alapértelmezés szerint csak jelentési módban lesznek létrehozva. Ez azt jelenti, hogy nyomon követheti a szabályzatok hatását a létrehozásuk pillanatától kezdve. Továbbá azok számára, akik az MS Graph API-kat használják, a [jelentésekre vonatkozó szabályzatokat programozott módon is kezelheti](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Önkiszolgáló regisztráció a vendég felhasználók számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD külső identitásokkal lehetővé teheti, hogy a szervezeten kívüli személyek hozzáférjenek az alkalmazásokhoz és az erőforrásokhoz, miközben a felhasználó bármilyen identitással bejelentkezhetnek. Ha külső felhasználókkal oszt meg egy alkalmazást, előfordulhat, hogy nem mindig tudja előre, hogy kinek van szüksége az alkalmazáshoz való hozzáférésre. Az [önkiszolgáló regisztráció](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)lehetővé teszi a vendég felhasználók számára, hogy regisztráljanak, és vendég fiókot kapjanak az üzletági (LOB) alkalmazások számára. A regisztrációs folyamat létrehozható és testreszabható az Azure AD és a közösségi identitások támogatásához. A regisztráció során további információkat is gyűjthet a felhasználóról.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>A feltételes hozzáférési információ és a jelentéskészítési munkafüzet általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az információk [és a jelentéskészítési munkafüzet](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) a rendszergazdáknak az Azure ad feltételes hozzáférés összefoglaló nézetét adja meg a bérlőn. Az egyes szabályzatok kiválasztásának lehetőségével a rendszergazdák jobban megismerhetik, hogy az egyes szabályzatok milyen módon és valós időben figyelik a módosításokat. A munkafüzet a Azure Monitorban tárolt adatstreameket adatfolyamként tárolja, amelyeket néhány percen belül megadhat az [utasításokat követve](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Az irányítópult könnyebben felderíthetővé tételéhez áthelyezte az Azure AD feltételes hozzáférés menüjének új megállapítások és jelentéskészítés lapjára.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A feltételes hozzáférésre vonatkozó szabályzat részletei panel nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az új [szabályzat részletei](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) panelen a feltételes hozzáférési szabályzat kiértékelése során megjelenő hozzárendelések, feltételek és vezérlőelemek láthatók. A panelt úgy érheti el, ha kiválasztja a feltételes hozzáférés vagy csak jelentési lapokon a bejelentkezési adatokat tartalmazó sort.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>A Microsoft Graph Directory objektumainak új lekérdezési képességei nyilvános előzetes verzióban érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** MS Graph- **termék funkció:** fejlesztői élmény

A rendszer új képességeket vezet be Microsoft Graph Directory Objects API-k, a darabszám, a keresés, a szűrés és a rendezési műveletek engedélyezéséhez. Ez lehetővé teszi a fejlesztők számára, hogy gyorsan lekérdezze a címtár-objektumokat olyan megkerülő megoldások nélkül, mint a memóriában történő szűrés és a rendezés. További információ ebben a [blogbejegyzésben](https://aka.ms/CountFilterMSGraphAAD).

Jelenleg nyilvános előzetes verzióban, visszajelzéseket keresünk. Kérjük, küldje el megjegyzéseit ebben a [rövid kérdőívben](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>SAML-alapú egyszeri bejelentkezés konfigurálása Microsoft Graph API használatával (bétaverzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Mostantól elérhető az Azure AD Galleryben az MS Graph API-k használatával történő alkalmazások létrehozásának és konfigurálásának támogatása. Ha SAML-alapú egyszeri bejelentkezést kell beállítania egy alkalmazás több példányához, időt takaríthat meg a Microsoft Graph API-k használatával, hogy [automatizálja az SAML-alapú egyszeri bejelentkezés konfigurációját](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic a szervezettől](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="saml-token-encryption-is-generally-available"></a>Az SAML-jogkivonat titkosítása általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Az [SAML-jogkivonat titkosítása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) lehetővé teszi, hogy az alkalmazások a titkosított SAML-kijelentések fogadására legyenek konfigurálva. A szolgáltatás mostantól általánosan elérhető minden felhőben.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Általánosan elérhetők a csoportnév jogcímei az alkalmazási jogkivonatokban.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
A jogkivonatban kiadott csoportos jogcímek mostantól csak az alkalmazáshoz hozzárendelt csoportokra korlátozódnak.  Ez különösen akkor fontos, ha a felhasználók nagy számú csoport tagjai, és fennáll a kockázata, hogy túllépte a tokenek méretének korlátozását. Ezzel az új funkcióval általánosan elérhetővé válnak a [csoportok nevei a tokenekhez](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) .
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>A munkanap visszaírási mostantól támogatja a munkahelyi telefonszám-attribútumok beállítását

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Továbbfejlesztettük a munkanap visszaírási kiépítési alkalmazását, hogy mostantól támogassa a munkahelyi telefonszámok és a visszaírási-attribútumok számát. Az e-mailek és a felhasználónevek mellett most már beállíthatja, hogy a munkanap visszaírási kiépítési alkalmazás az Azure AD-ból a munkanapokra is áthaladjon. A telefonszámok visszaírási konfigurálásával kapcsolatos további információkért tekintse meg a [munkanap visszaírási](https://aka.ms/WorkdayWriteback) alkalmazás oktatóanyagát. 

---

### <a name="publisher-verification-preview"></a>Közzétevő ellenőrzése (előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Fejlesztői élmény
 
A kiadói ellenőrzés (előzetes verzió) segítségével a rendszergazdák és a végfelhasználók megismerhetik az alkalmazások fejlesztőinek a Microsoft Identity platformmal való integrálásának eredetiségét. További részletekért tekintse meg a [kiadói ellenőrzés (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview)című témakört.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Engedélyezési kód folyamatábrája egyoldalas alkalmazásokhoz

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** hitelesítési **termék képesség:** fejlesztői felület

A modern böngésző [harmadik féltől származó cookie-korlátozásai (például a Safari itp](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas)) miatt a fürdőhelyeknek az engedélyezési kód folyamatát kell használniuk, nem pedig az SSO fenntartására szolgáló implicit folyamatot. A MSAL.js v 2. x mostantól támogatja az engedélyezési kód folyamatát. A Azure Portalnek megfelelő frissítésekkel rendelkezik, így frissítheti a SPA-t a "Spa" típusra, és használhatja az Auth Code flow-t. Útmutatásért tekintse meg a gyors üzembe helyezési útmutatót [: Jelentkezzen be a felhasználókba, és szerezzen be egy hozzáférési jogkivonatot egy JavaScript Spa-beli hitelesítési kód használatával](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Az eszközök továbbfejlesztett szűrése nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Módosított funkció   
**Szolgáltatás kategóriája:** Eszközkezelés **termék képesség:** eszközök életciklusának kezelése
 
Korábban a használni kívánt szűrők "engedélyezve" és "tevékenység dátuma" volt. Most már [szűrheti az eszközök listáját több tulajdonságra](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), például az operációs rendszer típusára, a csatlakozás típusára, a megfelelőségre és egyéb lehetőségekre. Ezeknek a kiegészítéseknek egyszerűbbé kell tenniük egy adott eszköz megkeresését.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>A Azure AD B2C új Alkalmazásregisztrációk-felülete már általánosan elérhető

**Írja be a következőt:** Módosított funkció   
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** Identitás-életciklus kezelése
 
A Azure AD B2C új Alkalmazásregisztrációk-felülete már általánosan elérhető. 

Korábban a korábbi alkalmazások felhasználói felületén keresztül kellett kezelnie a B2C-vel kapcsolatban álló alkalmazásokat az alkalmazások többi részétől. Ez különböző alkalmazások létrehozását jelentette az Azure különböző helyein.

Az új felhasználói élmény egy helyen jeleníti meg az összes B2C-alkalmazás regisztrációját és az Azure AD-alkalmazások regisztrációját, és egységes módon kezeli őket. Szükség van-e arra, hogy felügyelni lehessen egy olyan ügyfelet vagy alkalmazást, amelynek hozzáférése van a Microsoft Graphhoz, hogy programozott módon kezelhesse Azure AD B2C erőforrásokat, de csak az egyik módszert kell megtanulnia.

Az új élmény eléréséhez navigáljon a Azure AD B2C szolgáltatásra, és válassza a Alkalmazásregisztrációk panelt. A felhasználói élmény a Azure Active Directory szolgáltatásból is elérhető.

A Azure AD B2C Alkalmazásregisztrációk felhasználói élmény az Azure AD-bérlők általános [alkalmazás-regisztrációs felületén](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) alapul, de Azure ad B2Cra van szabva. A korábbi "alkalmazások" felhasználói élmény a jövőben elavulttá válik.

További információért látogasson el [a Azure ad B2C új alkalmazás-regisztrációs felületére](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>2020. április

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>Általánosan elérhető a kombinált biztonsági adatok regisztrációs felülete

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)

A **termék képességei:** Identitás-biztonsági & védelme

A Multi-Factor Authentication (MFA) és az önkiszolgáló jelszó-visszaállítás (SSPR) együttes regisztrációs felülete már általánosan elérhető. Ez az új regisztrációs élmény lehetővé teszi, hogy a felhasználók egyetlen, lépésenkénti folyamattal regisztráljanak az MFA-és SSPR. Amikor üzembe helyezi az új felhasználói élményt a szervezet számára, a felhasználók kevesebb idő alatt regisztrálhatnak, és kevesebb gondot biztosítanak. Tekintse meg a blogbejegyzést [itt](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Folyamatos hozzáférés kiértékelése

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)

A **termék képességei:** Identitás-biztonsági & védelme

A folyamatos hozzáférés kiértékelése egy új biztonsági funkció, amely lehetővé teszi a házirendek közel valós idejű kényszerítését az Azure ad hozzáférési jogkivonatokat használó függő entitások számára, ha az események az Azure AD-ben történnek (például a felhasználói fiókok törlése). Ezt a funkciót először a csapatok és az Outlook-ügyfelek számára tesszük elérhetővé. További részletekért tekintse meg a [blogot](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) és a [dokumentációt](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS-bejelentkezés: a Firstline-feldolgozók bejelentkezhetnek az Azure AD-alapú alkalmazásokba a telefonszámuk és a jelszó nélkül

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)

A **termék képességei:** Felhasználói hitelesítés

Az Office olyan mobil-első üzleti alkalmazásokat indít el, amelyek a nem hagyományos szervezeteknek, illetve a nagyméretű szervezetek alkalmazottainak, akik nem használják az e-maileket elsődleges kommunikációs módszerként. Ezek az alkalmazások megcélozják az alkalmazottakat, az íróasztal nélküli munkavégzőket, a helyszíni ügynököket vagy a kiskereskedelmi alkalmazottakat, akik nem kapnak e-mail-címet a munkaadótól, hozzáférhetnek a számítógéphez vagy a szolgáltatáshoz. Ez a projekt lehetővé teszi, hogy ezek az alkalmazottak bejelentkezzenek az üzleti alkalmazásokba egy telefonszám megadásával és egy kód roundtripping. További részletekért tekintse meg a [rendszergazdai dokumentációt](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) és a [végfelhasználói dokumentációt](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Belső felhasználók meghívása a B2B-együttműködés használatára

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** B2B

**A termék képességei:**

Bővítettük a B2B-Meghívási képességet, amely lehetővé teszi a meglévő belső fiókok meghívását a VÁLLALATKÖZI együttműködési hitelesítő adatok továbbítására. Ezt úgy teheti meg, hogy a meghívott e-mail-címre jellemző paraméterek mellett átadja a felhasználói objektumot a meghívó API-nak. A felhasználó objektumazonosító, UPN, csoporttagság, alkalmazás-hozzárendelés stb. változatlan marad, de a jövőben a B2B használatával hitelesíthető a saját bérlői hitelesítő adataival, nem pedig a meghívást megelőzően használt belső hitelesítő adatokkal. Részletekért tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users).

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>A feltételes hozzáféréshez már általánosan elérhető a csak jelentés üzemmód

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Feltételes hozzáférés

A **termék képességei:** Identitás-biztonsági & védelme

Az [Azure ad feltételes hozzáféréshez csak jelentési mód](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) lehetővé teszi a szabályzatok eredményének kiértékelését a hozzáférés-vezérlés kényszerítése nélkül. A szervezeten belül csak a jelentésekre vonatkozó házirendeket lehet tesztelni, és megismerni a hatásukat, mielőtt engedélyezné őket, így biztonságosabbá és egyszerűbbé teheti a telepítést. Az elmúlt néhány hónapban a csak jelentési mód erős bevezetését láttuk, amely a 26M-felhasználókra már érvényes a csak jelentési szabályzat hatálya alá. Ezzel a bejelentéssel az új Azure AD feltételes hozzáférési szabályzatok alapértelmezés szerint csak jelentési módban lesznek létrehozva. Ez azt jelenti, hogy nyomon követheti a szabályzatok hatását a létrehozásuk pillanatától kezdve. Továbbá azok számára, akik az MS Graph API-kat használják, a [jelentésekre vonatkozó szabályzatokat programozott módon is kezelheti](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>A feltételes hozzáférési információ és a jelentéskészítési munkafüzet általánosan elérhető

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Feltételes hozzáférés

A **termék képességei:** Identitás-biztonsági & védelme

A feltételes hozzáférési információk [és a jelentéskészítési munkafüzet](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) a rendszergazdáknak az Azure ad feltételes hozzáférés összefoglaló nézetét adja meg a bérlőn. Az egyes szabályzatok kiválasztásának lehetőségével a rendszergazdák jobban megismerhetik, hogy az egyes szabályzatok hogyan és valós időben figyelik a módosításokat. A munkafüzet a Azure Monitorban tárolt adatstreameket adatfolyamként tárolja, amelyeket néhány percen belül megadhat az [utasításokat követve](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Az irányítópult könnyebben felderíthetővé tételéhez áthelyezte az Azure AD feltételes hozzáférés menüjének új megállapítások és jelentéskészítés lapjára.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A feltételes hozzáférésre vonatkozó szabályzat részletei panel nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Feltételes hozzáférés

A **termék képességei:** Identitás-biztonsági & védelme

Az új [szabályzat részletei panel](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) megjeleníti, hogy mely hozzárendelések, feltételek és vezérlőelemek teljesültek a feltételes hozzáférési szabályzat kiértékelése során. A panelt úgy érheti el, ha kiválasztja a **feltételes hozzáférés** vagy **csak jelentési** lapokon a bejelentkezési adatokat tartalmazó sort.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – április 2020

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Vállalati alkalmazások

**Termék képesség:** harmadik féltől származó integráció

A 2020. áprilisában a következő 31 új alkalmazást bővítettük az App Gallery összevonási támogatásával: 

[SincroPool-alkalmazások](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [belvízi adatgyűjtési csomag](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO for JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki irányítópult](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365 mozgató](https://app.mover.io/login), [beszélő tevékenység](https://speakerengage.com/login.php), [becsületesen](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial), [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Kata](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [Timetabling megoldások](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), [SynchroNet Click](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [Empower](https://www.made-in-office.com/en/), [Forts Change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [Lakmusz](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [, GroupTalk,](https://recorder.grouptalk.com/) [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial), [MongoDB felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [nitro termelékenység Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>A nyilvános előzetes verzióhoz elérhető oAuth2PermissionGrant-Microsoft Graph különbözeti lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

Nyilvános előzetes verzióban elérhető a oAuth2PermissionGrant különbözeti lekérdezése. Mostantól nyomon követheti a változtatásokat anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. [Részletek](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Általánosan elérhető a Microsoft Graph Delta-lekérdezés támogatása a szervezeti kapcsolattartáshoz

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

A szervezeti kapcsolatok különbözeti lekérdezése általánosan elérhető! Mostantól nyomon követheti az üzemi alkalmazások változásait anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. Cserélje le az összes olyan meglévő kódot, amely folyamatosan lekérdezi a orgContact a különbözeti lekérdezés által, hogy jelentősen javítsa a teljesítményt. [Részletek](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Általánosan elérhető az alkalmazás Microsoft Graph különbözeti lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

Az alkalmazások különbözeti lekérdezése általánosan elérhető! Mostantól nyomon követheti az üzemi alkalmazások változásait anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. Cserélje le az összes olyan meglévő kódot, amely folyamatosan lekérdezi az alkalmazásadatok értékét a különbözeti lekérdezéssel, hogy jelentősen javítsa a teljesítményt. [Részletek](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>A nyilvános előzetes verzióban elérhető felügyeleti egységekre vonatkozó Microsoft Graph különbözeti lekérdezés támogatása

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Nyilvános előzetes verzióban elérhető a fejlesztői élmény változása a felügyeleti egységekhez. Mostantól nyomon követheti a változtatásokat anélkül, hogy folyamatosan le kellene kérdezni Microsoft Graph. [Részletek](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>A hitelesítő telefonszámok és egyebek kezelése az új Microsoft Graph Beta API-kon

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** MS Graph

A **termék képességei:** Fejlesztői élmény

Ezek az API-k kulcsfontosságú eszközt biztosítanak a felhasználók hitelesítési módszereinek kezeléséhez. Mostantól programozott módon előre regisztrálhat és kezelhet az MFA-hoz és az önkiszolgáló jelszó-visszaállításhoz (SSPR) használt hitelesítő. Ez az Azure MFA, a SSPR és a Microsoft Graph Spaces egyik legtöbbször kért funkciója volt. Az ebben a hullámban megjelent új API-k lehetővé teszi a következőket:

- Felhasználó hitelesítési telefonjának olvasása, hozzáadása, frissítése és eltávolítása
- Felhasználó jelszavának alaphelyzetbe állítása
- SMS-bejelentkezés be-és kikapcsolása

További információ: az [Azure ad hitelesítési módszereinek API-áttekintése](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Nyilvános előzetes verziójú felügyeleti egységek

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

A felügyeleti egységek lehetővé teszik olyan rendszergazdai engedélyek megadását, amelyek az Ön által meghatározott szervezeti egységre, régióra vagy más szegmensre korlátozódnak. A felügyeleti egységekkel engedélyeket delegálhat a regionális rendszergazdáknak, vagy megadhatja a szabályzatot egy részletességi szinten. A felhasználói fiók rendszergazdája például frissítheti a profil adatait, alaphelyzetbe állíthatja a jelszavakat, és hozzárendelheti a licenceket a felhasználók számára a felügyeleti egységben.

A felügyeleti egységek használatával a központi rendszergazda:

- Felügyeleti egység létrehozása az erőforrások decentralizált kezeléséhez
- Rendszergazdai engedélyekkel rendelkező szerepkör társítása csak az Azure AD-felhasználók számára egy felügyeleti egységben
- Szükség szerint töltse fel a felügyeleti egységeket a felhasználókkal és a csoportokkal

További információ: [felügyeleti egységek kezelése Azure Active Directory (előzetes verzió)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Nyomtató-rendszergazda és nyomtató-technikus beépített szerepkörei

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

**Nyomtató rendszergazdája**: az ezzel a szerepkörrel rendelkező felhasználók regisztrálhatják a nyomtatókat, és kezelhetik a Microsoft univerzális nyomtatási megoldás összes nyomtatási konfigurációjának minden aspektusát, beleértve az univerzális nyomtatási összekötő beállításait is. Az összes delegált nyomtatási engedély iránti kérelem beleegyezése. A nyomtató rendszergazdái is hozzáférhetnek a nyomtatási jelentésekhez. 

**Nyomtató-technikus**: az ezzel a szerepkörrel rendelkező felhasználók regisztrálhatják a nyomtatókat, és kezelhetik a nyomtató állapotát a Microsoft Universal Print megoldásban. Emellett az összes összekötő adatait is elolvashatják. Legfontosabb feladatok a nyomtató technikusa nem tud felhasználói engedélyeket beállítani a nyomtatók és a nyomtatók megosztásához. [Részletek](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Hibrid Identity admin beépített szerepkör

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

Az ebben a szerepkörben lévő felhasználók engedélyezhetik, konfigurálhatják és kezelhetik a hibrid identitás Azure AD-ben való engedélyezésével kapcsolatos szolgáltatásokat és beállításokat. Ez a szerepkör lehetővé teszi az Azure AD konfigurálását a következő három támogatott hitelesítési módszer egyikére:&#8212;jelszó-kivonatolási szinkronizálás (PHS), átmenő hitelesítés (PTA) vagy összevonás (AD FS vagy harmadik féltől származó összevonási szolgáltató) &#8212;és a kapcsolódó helyszíni infrastruktúra üzembe helyezéséhez. A helyszíni infrastruktúra magában foglalja a létesítést és a PTA-ügynököket. Ez a szerepkör lehetővé teszi a zökkenőmentes egyszeri bejelentkezés (S-SSO) engedélyezését a nem Windows 10-es vagy nem Windows Server 2016 rendszerű számítógépek zökkenőmentes hitelesítésének engedélyezéséhez. Emellett ez a szerepkör lehetővé teszi a bejelentkezési naplók megtekintését, valamint a figyelési és hibaelhárítási célból az állapot és az elemzés elérését. [Részletek](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Hálózati rendszergazda beépített szerepköre

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** RBAC

A **termék képességei:** Access Control

Az ezzel a szerepkörrel rendelkező felhasználók áttekinthetik a Microsoft által a saját felhasználói helyükről érkező hálózati telemetria alapuló hálózati peremhálózati javaslatokat. Az Office 365-hez készült hálózati teljesítmény a megfelelő vállalati ügyfél-hálózati peremhálózati architektúrára támaszkodik, amely általában a felhasználói helyekre jellemző. Ez a szerepkör lehetővé teszi a felderített felhasználói helyszínek szerkesztését és a hálózati paraméterek konfigurálását az adott helyszíneken a továbbfejlesztett telemetria-mérések és kialakítási javaslatok megkönnyítése érdekében. [Részletek](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Tömeges tevékenységek és letöltések az Azure AD felügyeleti portál felületén

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Felhasználói felügyelet

A **termék képességei:** Directory

Az Azure AD-ben a felhasználókra és csoportokra vonatkozó tömeges tevékenységeket is végrehajthat, ha feltölt egy CSV-fájlt az Azure AD felügyeleti portál felületén. Létrehozhat felhasználókat, törölhet felhasználókat, és meghívja a vendég felhasználókat. Hozzáadhat és eltávolíthat tagokat egy csoportból.

Az Azure AD-erőforrások listáját az Azure AD felügyeleti portál felületéről is letöltheti. Letöltheti a címtárban található felhasználók listáját, a címtárban lévő csoportok listáját és egy adott csoport tagjait.

További információkért tekintse meg a következőket:

- [Felhasználók létrehozása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) vagy [vendég felhasználók meghívása](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Felhasználók törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) vagy [törölt felhasználók visszaállítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Felhasználók listájának letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) vagy [a csoportok listájának letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- Tagok [hozzáadása (Importálás)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) vagy [tagok eltávolítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) , vagy a [tagok listájának letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) egy csoport számára

---

### <a name="my-staff-delegated-user-management"></a>Saját munkatársak által delegált felhasználói felügyelet

**Írja be a következőt:** Új funkció

**Szolgáltatás kategóriája:** Felhasználói felügyelet

**A termék képességei:**

A saját munkatársak lehetővé teszik a Firstline-kezelők, például egy áruház kezelője számára, hogy a személyzet tagjai hozzáférhessenek az Azure AD-fiókokhoz. A központi ügyfélszolgálatra való támaszkodás helyett a szervezetek olyan gyakori feladatokat delegálnak, mint például a jelszavak alaphelyzetbe állítása vagy a telefonszámok módosítása a Firstline-kezelőhöz. A munkatársakkal egy olyan felhasználó, aki nem tud hozzáférni a fiókjához, mindössze néhány kattintással elérheti a hozzáférést, és nincs szükség ügyfélszolgálatra vagy informatikai személyzetre. További információ: a [felhasználók kezelése a saját munkatársakkal (előzetes verzió)](https://aka.ms/MyStaffAdminDocs) és a [felhasználói felügyelet delegálása a munkatársakkal (előzetes verzió)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Frissített végfelhasználói élmény a hozzáférési felülvizsgálatokban

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok

A **termék képességei:** Identitás-szabályozás

Frissítettük a véleményezői élményt az Azure AD hozzáférési felülvizsgálatokhoz a saját alkalmazások portálon. Április végén a felülvizsgálók, akik bejelentkeznek az Azure AD-hozzáférési felülvizsgálatok-felülvizsgálati élményre, egy szalagcím jelenik meg, amely lehetővé teszi, hogy a frissített felhasználói élményt kipróbálják a saját hozzáférésben. Vegye figyelembe, hogy a frissített hozzáférési felülvizsgálatok funkciója ugyanazokat a funkciókat kínálja, mint a jelenlegi élmény, de az új funkciókkal rendelkező továbbfejlesztett felhasználói felülettel lehetővé teheti, hogy a felhasználók hatékonyak legyenek. [A frissített felhasználói élményről itt olvashat bővebben](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Ez a nyilvános előzetes verzió a 2020. július végéig tart. Július végén a felülvizsgálók, akik nem választották be az előzetes verziót, automatikusan a hozzáférési felülvizsgálatok végrehajtásához lesznek irányítva. Ha szeretné, hogy a felülvizsgálók véglegesen áttérjenek az előnézeti élményre az alkalmazásom szolgáltatásban, küldjön [itt egy kérést](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>A munkanapon bejövő felhasználók kiépítési és visszaírási alkalmazásai mostantól támogatják a munkanap Web Services API legújabb verzióit

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Alkalmazás kiépítés

**A termék képességei:** 

Az ügyfelek visszajelzései alapján már frissítettük a munkanapon bejövő felhasználók kiépítési és visszaírási alkalmazásait a vállalati alkalmazás-katalógusban, hogy támogassák a munkanap webszolgáltatások (WWS) API legújabb verzióit. Ezzel a módosítással az ügyfelek megadhatják azt a WWS API-verziót, amelyet a kapcsolódási sztringben használni kívánnak. Ez lehetővé teszi az ügyfeleknek, hogy több HR-attribútumot is beolvassák a munkanap kiadásaiban. A munkanap visszaírási alkalmazás mostantól az ajánlott Change_Work_Contact_Info munkanap webszolgáltatást használja a Maintain_Contact_Info korlátainak leküzdéséhez.

Ha nem ad meg verziót a kapcsolódási karakterláncban, alapértelmezés szerint a (z) WWS v 21.1 a beérkező felhasználók üzembe helyezéséhez szükséges legfrissebb munkanapokra vált, a felhasználóknak frissíteniük kell a kapcsolódási karakterláncot az [oktatóanyagban](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) leírtak szerint, és frissíteniük kell a munkanapokhoz használt XPath-értékeket a [munkanap attribútumokra vonatkozó útmutatóban](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)leírtak szerint. 

Az új API-k visszaírási való használatához nincs szükség módosításra a munkanap visszaírási kiépítési alkalmazásban. A munkanap oldalon ellenőrizze, hogy a munkanap-integrációs rendszer felhasználói (ISU) fiókjának van-e engedélye a Change_Work_Contact üzleti folyamat meghívására az oktatóanyag szakaszban leírt módon, az [üzleti folyamat biztonsági házirendjének engedélyeinek konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

Frissítettük az [oktatóanyagra vonatkozó útmutatót](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) , amely az új API-verzió támogatását tükrözi.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók mostantól a kiépítés hatókörében vannak

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Alkalmazás kiépítés

A **termék képességei:** Identitás-életciklus kezelése

Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók a kiépítés hatókörén kívül vannak. Visszajelzésünk szerint az ügyfelek a kiépítés hatókörében szeretnék, hogy az adott szerepkörrel rendelkező felhasználók felhasználják a felhasználókat. 2020. április 16-ától az összes új létesítési konfiguráció lehetővé teszi, hogy a felhasználók az alapértelmezett hozzáférési szerepkörrel legyenek kiépítve. Fokozatosan megváltoztatjuk a meglévő kiépítési konfigurációk viselkedését, hogy támogassák a felhasználók ezzel a szerepkörrel való üzembe helyezését. [Részletek](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Kiépítési felhasználói felület frissítése

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Alkalmazás kiépítés

A **termék képességei:** Identitás-életciklus kezelése

Frissítettük a kiépítési tapasztalatot, hogy egy koncentráltabb felügyeleti nézetet hozzon létre. Ha egy már konfigurált vállalati alkalmazás kiépítési paneljére navigál, könnyedén nyomon követheti a kiépítés előrehaladását, és kezelheti az olyan műveleteket, mint például az indítás, a leállítás és a kiépítés újraindítása. [Részletek](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>A dinamikus csoport szabályának érvényesítése már elérhető a nyilvános előzetes verzióban

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** Csoport kezelése

A **termék képességei:** Együttműködés

A Azure Active Directory (Azure AD) mostantól lehetővé teszi a dinamikus csoportok szabályainak érvényesítését. A **szabályok érvényesítése** lapon ellenőrizheti, hogy a dinamikus szabály a minta csoport tagjaira vonatkozik-e, hogy erősítse meg, hogy a szabály a várt módon működik-e. Dinamikus csoportokra vonatkozó szabályok létrehozásakor vagy frissítésekor a rendszergazdák tudni szeretnék, hogy egy felhasználó vagy egy eszköz tagja lesz-e a csoportnak. Ez segít kiértékelni, hogy egy felhasználó vagy eszköz teljesíti-e a szabályok feltételeit és a hibaelhárítás során a tagság nem várt időpontját.

További információkért lásd: [dinamikus csoporttagság-szabály (előzetes verzió) ellenőrzése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Az identitás biztonságos pontszáma – a biztonsági alapértékek és az MFA-javító művelet frissítései

**Írja be a következőt:** Módosított funkció

**Szolgáltatás kategóriája:** N/A

A **termék képességei:** Identitás-biztonsági & védelme

**Az Azure ad-javító műveletek biztonsági alapértelmezett értékeinek támogatása:** A Microsoft biztonságos pontszáma frissíti a [biztonsági beállítások Azure ad-ben](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)történő támogatásának javítását, amely megkönnyíti a szervezet számára a gyakori támadások előre konfigurált biztonsági beállításainak védelmét. Ez hatással lesz a következő fejlesztési műveletekre:

- Győződjön meg arról, hogy minden felhasználó elvégezheti a többtényezős hitelesítést a biztonságos hozzáféréshez
- MFA megkövetelése rendszergazdai szerepkörökhöz
- Az örökölt hitelesítés blokkolására szolgáló házirend engedélyezése
 
**MFA-javító művelet frissítései:** Annak érdekében, hogy a vállalatok a vállalatnál működő házirendek alkalmazása során biztosítsák a legnagyobb biztonságot, a Microsoft biztonságos pontszáma három, a többtényezős hitelesítés körébe tartozó fejlesztési műveletet eltávolítottan, és kettőt vett fel.

A javítási műveletek el lettek távolítva:

- Az összes felhasználó regisztrálása a többtényezős hitelesítéshez
- MFA megkövetelése minden felhasználótól
- MFA megkövetelése Azure AD-beli Kiemelt szerepkörökhöz

További fejlesztési műveletek:

- Győződjön meg arról, hogy minden felhasználó elvégezheti a többtényezős hitelesítést a biztonságos hozzáféréshez
- MFA megkövetelése rendszergazdai szerepkörökhöz

Ezeknek az új fejlesztési műveleteknek a használatához regisztrálnia kell a felhasználókat vagy a rendszergazdákat a multi-Factor Authentication (MFA) szolgáltatásban a címtárban, és meg kell határoznia a szervezeti igényeihez illeszkedő megfelelő szabályzatokat. A fő cél az, hogy rugalmasságot biztosítson, miközben a felhasználók és a rendszergazdák több tényezővel vagy kockázatalapú identitás-ellenőrzési kérésekkel is hitelesíthetők. Ez olyan formában is megteheti a formáját, amely hatókörön belüli döntéseket alkalmaz, vagy a biztonsági alapértékeket (március 16.) állítja be, amelyek lehetővé teszik a Microsoft számára, hogy mikor kell megkérdőjelezni a felhasználókat az MFA-ban. [Tudjon meg többet a Microsoft biztonságos pontszám újdonságait ismertető cikkről](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>2020. március

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Nem felügyelt Azure Active Directory fiók a B2B-frissítésben március 2021

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
**2021. március 31-ig**a Microsoft többé nem fogja támogatni a meghívások beváltását azáltal, hogy nem felügyelt Azure Active Directory (Azure ad) fiókokat és bérlőket hoz létre vállalatközi együttműködési forgatókönyvek létrehozásához. Ennek előkészítéseként javasoljuk, hogy az [egyszeri jelszavas hitelesítéssel küldje](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)el az e-maileket.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók a kiépítés hatókörében lesznek

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók a kiépítés hatókörén kívül vannak. Visszajelzésünk szerint az ügyfelek a kiépítés hatókörében szeretnék, hogy az adott szerepkörrel rendelkező felhasználók felhasználják a felhasználókat. Dolgozunk a változtatások üzembe helyezésén, így minden új kiépítési konfiguráció lehetővé teszi a felhasználók számára az alapértelmezett hozzáférési szerepkör kiépítését. Fokozatosan megváltoztatjuk a meglévő kiépítési konfigurációk viselkedését, hogy támogassák a felhasználók ezzel a szerepkörrel való üzembe helyezését. Nincs szükség felhasználói beavatkozásra. A módosítás érvénybe lépjen a [dokumentáció](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) frissítése után.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Az Azure AD B2B együttműködés a 21Vianet (Azure China 21Vianet) bérlők által üzemeltetett Microsoft Azureban lesz elérhető.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD B2B együttműködési képességek a 21Vianet (Azure China 21Vianet) bérlők által üzemeltetett Microsoft Azureban lesznek elérhetők, így az Azure China 21Vianet-bérlő felhasználói zökkenőmentesen dolgozhatnak más Azure China 21Vianet-bérlők felhasználóival. [További információ az Azure ad B2B együttműködésről](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B együttműködés meghívása e-mail újratervezése

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD B2B együttműködés Meghívási szolgáltatása által küldött [e-maileket](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) a rendszer újratervezi, hogy meghívja a felhasználókat a címtárba, hogy a Meghívási adatokat és a felhasználó következő lépéseit világosabban hozza meg.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>A HomeRealmDiscovery szabályzat módosítása megjelenik a naplókban.

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Ellenőrzési  
A **termék képességei:** & jelentéskészítés figyelése
 
Rögzítettünk egy hibát, amelyben a [HomeRealmDiscovery szabályzat](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) módosításait nem vették fel a naplókba. Ekkor láthatja, hogy mikor és hogyan módosult a házirend, és hogy kivel. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Új összevont alkalmazások érhetők el a Azure AD alkalmazás Galleryben – március 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Március 2020-én ezeket a 51 új alkalmazásokat bővítettük az alkalmazás-katalógusban az összevonási támogatással: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML-alkalmazás](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [IPoint](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial)- [Aster](https://demo.asterapp.io/login)szolgáltató, [LIFT](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [contexxt.ai szféra](https://contexxt-sphere.com/login), [bölcsesség a Invictus, a](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial) [flare digitális aláírások](https://spark-dev.pixelnebula.com/login), [a Logz.IO-Felhőbeli megfigyelés, a](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial) [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), a BizzContact, a [Node Insight](https://admin.nodeinsight.com/AADLogin.aspx)Elqano, az MarketSignShare, a CrossKnowledge, az net Vision, a Compas, a Byggeweb, az [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial) [GoLinks,](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial) [a](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial)datadoggal [, a Zscaler, a](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)WatchTeams [, Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial)A [,](https://www.devfinition.com/) [a](https://apps.apple.com/us/app/docia/id529058757) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial) [az](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [, a](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [, a](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [IP-platform](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [PIPEDRIVE](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase workshop](https://app.showcaseworkshop.com/) [BizzContact](https://bizzcontact.app/), [Greenlight integrációs platform](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), Greenlight- [kompatibilis hozzáférés-kezelés](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [grokkolom learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Mirador online](https://login.online.miradore.com/), [Khoros Care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [MarketSignShare](http://www.signshare.com/) [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [bizarr Studio a digitális folyamatok automatizálásához](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX,](https://www.insuite.jp/) [sybo](https://www.systexsoftware.com.tw/), [brit](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-nap](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial), [Kollective Sdn](https://portal.kollective.app/login) [, Witivio,](https://app.witivio.com/) [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial), [Catchpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [, Code42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Az Azure AD B2B együttműködés Azure Government bérlők számára érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD B2B együttműködési funkciói mostantól elérhetők a Azure Government bérlők között.  Annak megállapításához, hogy a bérlő tudja-e használni ezeket a képességeket, kövesse az utasításokat, [Hogyan állapítható meg, hogy a B2B együttműködés elérhető-e az Azure US government-bérlőben?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Az Azure-naplók Azure Monitor integrációja már elérhető a Azure Government

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
Az Azure AD-naplók Azure Monitor integrációja már elérhető a Azure Governmentban. Az Azure AD-naplókat (naplózási és bejelentkezési naplókat) átirányíthatja egy Storage-fiókba, az Event hub-ba és a Log Analyticsba. Tekintse meg a [részletes dokumentációt](https://aka.ms/aadlogsinamd) , valamint az Azure ad-forgatókönyvek [jelentéskészítéséhez és monitorozásához szükséges telepítési terveket](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) .

---

### <a name="identity-protection-refresh-in-azure-government"></a>Identity Protection-frissítés Azure Government

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Örömmel vesszük, hogy most már bevezetjük a frissített [Azure ad Identity Protection](https://aka.ms/IdentityProtectionDocs)   élményt a [Microsoft Azure Government portálon](https://portal.azure.us/). További információt a [bejelentési blogbejegyzésben](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)talál.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Vész-helyreállítás: a kiépítési konfiguráció letöltése és tárolása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure AD-kiépítési szolgáltatás a konfigurációs képességek széles körét biztosítja. Az ügyfeleknek el kell tudniuk menteni a konfigurációt, hogy később is hivatkozhatnak rá, vagy visszatérhetnek egy ismert jó verzióra. Lehetőség van arra, hogy a kiépítési konfigurációt JSON-fájlként töltse le, és töltse fel, amikor szüksége van rá. [További információ](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A SSPR (önkiszolgáló jelszó-visszaállítás) mostantól két kaput igényel a rendszergazdák számára a 21Vianet által üzemeltetett Microsoft Azureban (Azure China 21Vianet) 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Identitás-biztonsági & védelme
 
Korábban Microsoft Azure a 21Vianet (Azure China 21Vianet) által működtetett, az önkiszolgáló jelszó-visszaállítást (SSPR) használó rendszergazdák a saját jelszavaik visszaállításához csak egy "kaput" (Challenge) szükségesek az identitásuk bizonyításához. A nyilvános és más országos felhőkben a rendszergazdáknak általában két kaput kell használniuk, hogy igazolják identitásukat a SSPR használatakor. Mivel azonban nem támogatjuk az SMS-t és a telefonhívásokat az Azure China 21Vianet, a rendszergazdák az egykapus jelszó-visszaállítást engedélyezték.

SSPR-szolgáltatás paritást hozunk létre az Azure China 21Vianet és a nyilvános felhő között. A jövőben a rendszergazdáknak két kaput kell használniuk a SSPR használatakor. Az SMS, a telefonhívások és a hitelesítő alkalmazások értesítései és kódjai is támogatottak lesznek. [További információ](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>A jelszó hossza legfeljebb 256 karakter hosszúságú lehet

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD szolgáltatás megbízhatóságának biztosítása érdekében a felhasználói jelszavak már 256 karakterre korlátozódnak. Az ennél hosszabb jelszóval rendelkező felhasználókat a rendszer arra kéri, hogy változtassa meg a jelszavát a következő bejelentkezéskor, vagy forduljon a rendszergazdához, vagy az önkiszolgáló jelszó-visszaállítási funkció használatával.

Ez a változás a 2020. március 13-án, a (18:00 UTC), a AADSTS 50052, a InvalidPasswordExceedsMaxLength. További részletekért tekintse meg a [változtatási értesítéseket](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Az Azure AD bejelentkezési naplói mostantól elérhetők az összes ingyenes bérlő számára a Azure Portal

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
Az ingyenes bérlőket használó ügyfeleink akár 7 napig is hozzáférhetnek az [Azure ad bejelentkezési naplóihoz a Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) . Korábban a bejelentkezési naplók csak prémium szintű Azure Active Directory licenccel rendelkező ügyfelek számára voltak elérhetők. Ezzel a módosítással minden bérlő hozzáférhet a naplókhoz a portálon keresztül.

> [!NOTE]
> Az ügyfeleknek továbbra is prémium szintű licencre (prémium szintű Azure Active Directory P1 vagy P2) van szükségük a bejelentkezési naplók eléréséhez Microsoft Graph API-n és Azure Monitoron keresztül.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>A címtárra kiterjedő csoportok beállításának elavult beállítása a csoportok általános beállításainál Azure Portal

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Annak érdekében, hogy az ügyfelek rugalmasabban hozzanak létre olyan, címtárra kiterjedő csoportokat, amelyek megfelelnek az igényeiknek **, a csoport** **Groups**  >  **általános** beállításai közül a Azure Portal, a [dinamikus csoportokra vonatkozó dokumentációra](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)mutató hivatkozást váltottunk ki. Továbbfejlesztettük a dokumentációt, hogy több útmutatást is tartalmazzon, így a rendszergazdák létrehozhatnak olyan összes felhasználói csoportot, amely tartalmazza vagy kizárják a vendég felhasználókat.

---

## <a name="february-2020"></a>2020. február

### <a name="upcoming-changes-to-custom-controls"></a>Az egyéni vezérlők közelgő módosításai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
Azt tervezzük, hogy lecseréljük a jelenlegi egyéni vezérlők előzetesét egy olyan megközelítéssel, amely lehetővé teszi, hogy a partner által biztosított hitelesítési funkciók zökkenőmentesen működjenek a Azure Active Directory-rendszergazda és a végfelhasználói élményben. Napjainkban a partner MFA-megoldások a következő korlátozásokkal szembesülnek: csak jelszó megadása után működnek. más kulcsfontosságú helyzetekben nem szolgálnak MFA-ként a lépésenkénti hitelesítéshez; és nem integrálják a végfelhasználói vagy a rendszergazdai hitelesítő adatok kezelési funkcióit. Az új implementáció lehetővé teszi, hogy a partner által biztosított hitelesítési tényezők a főbb forgatókönyvek beépített tényezői mellett működjenek, beleértve a regisztrációt, a használatot, az MFA-jogcímeket, a hitelesítés lépéseit, a jelentéskészítést és a naplózást. 

Az egyéni vezérlők továbbra is támogatottak az előzetes verzióban az új terv mellett, amíg az általános rendelkezésre állást nem éri el. Ezen a ponton biztosítjuk az ügyfeleknek az új tervbe való Migrálás idejét. Az aktuális megközelítés korlátai miatt nem fogunk új szolgáltatókat bevezetni, amíg az új terv elérhetővé nem válik. Szorosan együttműködünk az ügyfelekkel és a szolgáltatókkal, és a lehető legszorosabban tájékoztatjuk az ütemtervet. [További információ](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Identitások biztonságos pontszáma – MFA-javító művelet frissítései

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
Annak érdekében, hogy a vállalatok a vállalatnál működő házirendek alkalmazása során biztosítsák a legnagyobb biztonságot, a Microsoft biztonságos pontszáma a többtényezős hitelesítés (MFA) körébe tartozó három fejlesztési művelet eltávolítását és a kettő hozzáadását mutatja be.

A következő javítási műveletek el lesznek távolítva:

- Az összes felhasználó regisztrálása az MFA-hoz
- MFA megkövetelése minden felhasználótól
- MFA megkövetelése Azure AD-beli Kiemelt szerepkörökhöz

A következő javítási műveletek lesznek hozzáadva:

- Győződjön meg arról, hogy minden felhasználó teljes MFA-t tud biztosítani a biztonságos hozzáféréshez
- MFA megkövetelése rendszergazdai szerepkörökhöz

Az új fejlesztési műveletekhez regisztrálnia kell a felhasználókat vagy a rendszergazdákat az MFA-ban a címtárban, és meg kell határoznia a szervezeti igényeknek megfelelő házirend-készletet. A fő cél az, hogy rugalmasságot biztosítson, miközben a felhasználók és a rendszergazdák több tényezővel vagy kockázatalapú identitás-ellenőrzési kérésekkel is hitelesíthetők. Ez olyan biztonsági alapértékek beállítására is lehetőséget nyújt, amelyek lehetővé teszik a Microsoft számára, hogy mikor kell kipróbálnia a felhasználók számára az MFA-t, vagy több, hatókörön belüli döntéseket alkalmazó szabályzatot is A fejlesztési művelet frissítéseinek részeként az alapkonfiguráció-védelmi szabályzatok többé nem fognak szerepelni a pontozási számításokban. [Tudjon meg többet a Microsoft biztonságos pontszámáról](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD Domain Services SKU kiválasztása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services
 
Hallottunk visszajelzést arról, hogy Azure AD Domain Services ügyfeleink nagyobb rugalmasságot kívánnak kijelölni a példányok teljesítményi szintjeit. 2020. február 1-től kezdődően egy dinamikus modellből (ahol az Azure AD meghatározza a teljesítmény-és árképzési szintet az objektumok száma alapján) egy önálló kiválasztási modellre. Az ügyfelek mostantól olyan teljesítményszint közül választhatnak, amelyik megfelel a környezetének. Ez a módosítás azt is lehetővé teszi, hogy új forgatókönyveket, például erőforrás-erdőket és prémium funkciókat, például napi biztonsági mentéseket engedélyezzen. Az objektumok száma mostantól korlátlan az összes SKU esetében, de továbbra is az egyes rétegekhez tartozó objektumokra vonatkozó javaslatokat fogjuk biztosítani.

**Nincs szükség azonnali felhasználói beavatkozásra.** A meglévő ügyfelek esetében a 2020. február 1-én használt dinamikus rétegek az új alapértelmezett szintet határozzák meg. Ennek a változásnak az eredménye nem befolyásolja a díjszabást és a teljesítményt. A továbbiakban az Azure AD DS ügyfeleknek értékelniük kell a teljesítményre vonatkozó követelményeket a címtár méretének és a számítási feladatok jellemzőinek megváltozása után. A szolgáltatási szintek közötti váltás továbbra is leállási művelet marad, és a továbbiakban nem lesz automatikusan áthelyezve az ügyfelek új szintjére a címtáruk növekedése alapján. Emellett az árak nem növekednek, és az új díjszabás az aktuális számlázási modellel fog igazodni. További információkért tekintse meg az [Azure AD DS SKU-dokumentációt](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) és a [Azure ad Domain Services díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/active-directory-ds/).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – február 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Február 2020-án a következő 31 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással: 

[IamIP szabadalmi platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 egyszeri bejelentkezés az Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)-hoz, [Barracuda e-mail-biztonsági szolgáltatás](https://ess.barracudanetworks.com/sso/azure), [ABa-jelentéskészítés](https://myaba.co.uk/client-access/signin/auth/msad) [válság esetén – online portál](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [méhész Azure ad-alapú adatösszekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry-értékelések](https://www.kornferry.com/solutions/kf-digital/kf-assess), Verkada- [parancs](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [Emődi-böngészés](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [új ereklye (korlátozott kiadás)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [túlium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [sablon kiválasztása csapatok számára](https://links.officeatwork.com/templatechooser-download-teams), [méhek](https://www.beesy.me/index.php/site/login), [egészségügyi támogatási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [fali](https://app.mural.co/signup), [kaptár](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink tanárok és iskolák számára](https://www.thinglink.com/), [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WeDo](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [újranyomtatja az íróasztalt-article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – február 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD-támogatás a FIDO2 biztonsági kulcsaihoz hibrid környezetekben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Bejelentjük az Azure AD támogatásának nyilvános előzetes verzióját a FIDO2 biztonsági kulcsaihoz a hibrid környezetekben. A felhasználók mostantól FIDO2 biztonsági kulcsokkal jelentkezhetnek be a hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökre, és zökkenőmentesen jelentkezhetnek be a helyszíni és a Felhőbeli erőforrásaiba. A hibrid környezetek támogatása a legtöbbet kért szolgáltatás volt a jelszóval nem rendelkező ügyfeleinktől, hiszen eredetileg az Azure AD-hez csatlakoztatott eszközök FIDO2-támogatásának nyilvános előzetes verzióját indítottuk el. A védett technológiák, például a biometria és a nyilvános/titkos kulcsos titkosítás használatával a jelszó-alapú hitelesítés kényelmes és egyszerű használatot biztosít. Ezzel a nyilvános előzetes verzióval mostantól modern hitelesítést használhat, például FIDO2 biztonsági kulcsokat a hagyományos Active Directory erőforrások eléréséhez. További információért keresse fel az [SSO-t a helyszíni erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

A kezdéshez látogasson el [a FIDO2 biztonsági kulcsok engedélyezése a bérlőhöz](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) lépésről lépésre. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>Mostantól általánosan elérhető az új fiókom felhasználói felülete

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Saját profil/fiók  
A **termék képességei:** Végfelhasználói élmények
 
A fiókom, az összes végfelhasználói fiók-felügyeleti szükségletet leállító áruház már általánosan elérhető! A végfelhasználók az új webhelyet URL-címen keresztül érhetik el, vagy az új saját alkalmazások felületének fejlécében. További információ az önkiszolgáló funkciókról: az új felhasználói élmény a [saját fiók portálon – áttekintés](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>A fiókom webhely URL-címe frissült a myaccount.microsoft.com

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Saját profil/fiók  
A **termék képességei:** Végfelhasználói élmények
 
Az új fiókom végfelhasználói élménye a következő hónapban frissülni fog az URL-címével `https://myaccount.microsoft.com` . További információk a felhasználói élményről és az összes olyan fiókról, amely a [saját fiók portál súgójában](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)elérhető végfelhasználók számára biztosít segítséget.

