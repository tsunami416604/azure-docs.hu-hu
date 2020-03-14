---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory |} A Microsoft Docs
description: Ismerje meg, mi az új Azure Active Directoryval, például a legújabb kibocsátási megjegyzések, az ismert problémák, hibajavításokat tartalmaz, elavult funkció, és a közelgő változásokat.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262099"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` a ![RSS-hírcsatorna olvasó ikonja](./media/whats-new/feed-icon-16x16.png) a hírcsatorna-olvasó.

Az Azure AD folyamatosan fejlesztései kap. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

Ezen a lapon havonta frissül, így rendszeresen ellenőrizni. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---

## <a name="february-2020"></a>2020. február
 
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
 
Február 2020-én a következő 31 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással: 

[IamIP szabadalmi platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Cloud Experience](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [ns1 SSO az Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)-hoz, [Barracuda e-mail-biztonsági szolgáltatás](https://ess.barracudanetworks.com/sso/azure), [ABa-jelentéskészítés](https://myaba.co.uk/client-access/signin/auth/msad), [válság-online portál](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC Cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [méhész Azure ad Adatösszekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada parancs](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [Emődi navigál](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [új ereklye (korlátozott kiadás)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [túlium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [sablon Csapatok](https://links.officeatwork.com/templatechooser-download-teams), [méhek](https://www.beesy.me/index.php/site/login), [egészségügyi támogatási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [fali](https://app.mural.co/signup), [kaptár](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink oktatóknak és iskoláknak](https://www.thinglink.com/), [CODA](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WeDo](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [újranyomtatni az íróasztal-cikk galaxis](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
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
- [PureCloud Genesys szerint](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
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
 
Az új fiókom végfelhasználói élmény a következő hónapban frissíti a https://myaccount.microsoft.com URL-címét. További információk a felhasználói élményről és az összes olyan fiókról, amely a [saját fiók portál súgójában](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)elérhető végfelhasználók számára biztosít segítséget.

---
 
## <a name="january-2020"></a>2020. január
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Az új My apps portál már általánosan elérhető

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Végfelhasználói élmények
 
Frissítse szervezetét az új My apps portálra, amely már általánosan elérhető! További információ az új portálról és gyűjteményekről: [gyűjtemények létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Az Azure AD-munkaterületek a gyűjteményeknek lettek átnevezve

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások   
A **termék képességei:** Végfelhasználói élmények
 
A munkaterületek, a szűrők rendszergazdák konfigurálhatják a felhasználói alkalmazásaikat, most gyűjteményekként lesznek hivatkozva. További információ arról, hogyan konfigurálhatja őket a [gyűjtemények létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)című témakörben.

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C telefonos regisztráció és bejelentkezés egyéni szabályzattal (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A telefonos regisztrációval és bejelentkezéssel a fejlesztők és a vállalatok lehetővé tehetik ügyfeleiknek, hogy regisztráljanak, és jelentkezzenek be a felhasználó telefonszámára SMS-ben eljuttatott egyszeri jelszó használatával. Ez a funkció azt is lehetővé teszi, hogy az ügyfél megváltoztassa a telefonszámát, ha elvesztik a telefonjára való hozzáférést. Az egyéni szabályzatok erejével a telefonos regisztráció és bejelentkezés lehetővé teszi a fejlesztők és a vállalatok számára, hogy az oldal testreszabásával kommunikáljanak a márkájuk használatával. Megtudhatja, hogyan [állíthatja be a telefonos regisztrációt, és hogyan jelentkezhet be a Azure ad B2C egyéni házirendjeivel](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – január 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler privát hozzáférés](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – január 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Január 2020-én ezeket a 33 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük: 

[Josa](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [gyors Edge-felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick Felhőbeli biztonsági mentés az Office 365](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)-es verzióhoz [,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LeaveBot](https://leavebot.io/#home) [, DataCamp, TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [, SmartWork,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [dotcom,](https://www.intumit.com/english/SmartWork.html)SSOGEN [-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [JDE-Azure ad SSO-átjáró az Oracle E-Business Suite-EBS, PeopleSoft és MyCirqa](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [üzemeltetett Yuhu SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [LumApps Property Management platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Worker Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe egy saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient-kezelő szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [Áttekintés](https://portal.coreview.com/), [leverni Cloud Office 365 Connector](https://laxmi.squelch.io/login), [PingFlow hitelesítés](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize csatlakozás](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai](https://app.sandwai.com/), EZRentOut, [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [, akari](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [ Virtuális asszisztens](https://akari.io/akari-virtual-assistant/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="two-new-identity-protection-detections"></a>Két új Identity Protection-észlelés

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme
 
Két új bejelentkezési típust adtunk hozzá a személyazonosság védelme érdekében: a gyanús beérkezett fájlok manipulációs szabályait és a lehetetlen utazást. Ezeket az offline észleléseket Microsoft Cloud App Security (MCAS) észleli, és a felhasználók és a bejelentkezési kockázat befolyásolása az Identity Protectionben. További információ ezekről az észlelésekről: [bejelentkezési kockázati típusok](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Megszakítási változás: az URI-töredékek nem lesznek végrehajtva a bejelentkezési átirányítás során

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
2020. február 8-án kezdődően, amikor egy felhasználó egy kérést küld a login.microsoftonline.com egy felhasználónak való bejelentkezéshez, a szolgáltatás hozzáfűz egy üres töredéket a kéréshez.  Ezzel megakadályozza, hogy a böngésző kitörölje a kérésben szereplő összes töredéket. Ehhez a viselkedéshez egyetlen alkalmazásnak sincs függősége. További [információkért lásd: a Microsoft](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) Identity platform dokumentációjában.

---

## <a name="december-2019"></a>2019. december

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>SAP-SuccessFactors üzembe helyezése az Azure AD-ben és a helyszíni AD-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

Mostantól integrálhatja az SAP SuccessFactors-t mérvadó identitási forrásként az Azure AD-ben. Ez az integráció segít automatizálni a teljes körű identitás-életciklust, beleértve a HR-alapú események, például az új bérletek vagy a megszűnések használatát az Azure AD-fiókok üzembe helyezésének vezérléséhez.

Az SAP-SuccessFactors bejövő üzembe helyezésének az Azure AD-be való beállításával kapcsolatos további információkért tekintse meg az [SAP SuccessFactors automatikus létesítésének konfigurálása](https://aka.ms/SAPSuccessFactorsInboundTutorial) oktatóanyagot.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Testreszabott e-mailek támogatása a Azure AD B2Cban (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Mostantól a Azure AD B2C használatával testreszabott e-maileket hozhat létre, amikor a felhasználók az alkalmazások használatára jelentkeznek. A DisplayControls (jelenleg előzetes verzióban) és egy külső gyártótól származó e-mail-szolgáltató (például a [SendGrid](https://sendgrid.com/), a [SparkPost](https://sparkpost.com/)vagy egy egyéni REST API) használatával saját e-mail-sablont használhat a cím és a tárgy **szövege alapján,** valamint támogathatja a honosítási és egyéni egyszeri jelszavas (OTP) beállításokat is.

További információ: [egyéni e-mail-ellenőrzés Azure Active Directory B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Alapkonfiguráció-házirendek cseréje biztonsági alapértékekkel

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás biztonsága és védelme

A hitelesítéshez a biztonságos, alapértelmezett modell részeként minden bérlőről eltávolítjuk a meglévő alapkonfiguráció-védelmi szabályzatokat. Ez az Eltávolítás a februári befejezést célozza meg. Ezeknek az alapkonfiguráció-védelmi házirendeknek a pótlása biztonsági alapértékek. Ha alapkonfiguráció-védelmi házirendeket használ, meg kell terveznie az új biztonsági alapértelmezett házirendre vagy a feltételes hozzáférésre való áttérést. Ha még nem használta ezeket a szabályzatokat, nem kell végrehajtania a műveletet.

További információ az új biztonsági alapbeállításokról: [Mik a biztonsági alapértékek?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) A feltételes hozzáférési szabályzatokkal kapcsolatos további információkért lásd: [általános feltételes hozzáférési szabályzatok](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>2019. november

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>A SameSite attribútum és a Chrome 80 támogatása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

A cookie-k számára a biztonságos, alapértelmezett modell részeként a Chrome 80 böngésző megváltoztatja, hogyan kezeli a cookie-kat a `SameSite` attribútum nélkül. Minden olyan cookie, amely nem adja meg a `SameSite` attribútumot, a rendszer úgy kezeli, mintha `SameSite=Lax`re lett állítva, ami azt eredményezi, hogy a Chrome blokkolja a több tartományon belüli cookie-megosztási forgatókönyveket, amelyektől az alkalmazás függ. A korábbi Chrome-viselkedés fenntartása érdekében használhatja a `SameSite=None` attribútumot, és hozzáadhat egy további `Secure` attribútumot, így a helyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. A Chrome a módosítás befejezését ütemezi a 2020. február 4-én.

Azt javasoljuk, hogy a fejlesztők az alábbi útmutató segítségével tesztelik alkalmazásaikat:

- Állítsa az alapértelmezett értéket a **biztonságos cookie használata** beállításnál az **Igen**értékre.

- Állítsa a **SameSite** attribútum alapértelmezett értékét **none**értékre.

- Adjon hozzá egy további, **biztonságos**`SameSite` attribútumot.

További információ: a [közelgő SameSite cookie-változások a ASP.net és a ASP.net Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) , valamint az [ügyfelek webhelyeire és a Microsoft termékeire és szolgáltatásaira vonatkozó lehetséges fennakadások a Chrome 79-os és újabb verzióiban](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Új gyorsjavítás a Microsoft Identity Manager (rendszerhez) 2016 Service Pack 2 (SP2) rendszerhez

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Microsoft Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése

A gyorsjavítások kumulatív csomagja (Build 4.6.34.0) elérhető a következőhöz: Microsoft Identity Manager (2016 Service Pack 2 (SP2). Ez a frissítőcsomag elhárítja a problémákat, és olyan javításokat ad hozzá, amelyek a jelen frissítés által javított problémák és javításokkal foglalkozó szakaszban olvashatók.

További információ és a gyorsjavító csomag letöltése: [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0) kumulatív frissítés elérhető](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Új AD FS alkalmazás-tevékenység jelentés az alkalmazások Azure AD-be való átmigrálása érdekében (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Használja az új Active Directory összevonási szolgáltatások (AD FS) (AD FS) alkalmazás-tevékenység jelentését a Azure Portalban annak azonosításához, hogy mely alkalmazások képesek áttelepíteni az Azure AD-be. A jelentés az Azure AD-vel való kompatibilitás érdekében az összes AD FS alkalmazást felméri, ellenőrzi a problémákat, és útmutatást nyújt az egyes alkalmazások áttelepítésre való előkészítéséhez.

További információ: [az alkalmazások Azure ad-ba való áttelepítésére szolgáló AD FS alkalmazás-tevékenységi jelentés használata](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Új munkafolyamat a felhasználók számára rendszergazdai engedély kérése (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** Access Control

Az új rendszergazdai hozzájárulási munkafolyamat lehetővé teszi a rendszergazdáknak, hogy hozzáférést biztosítanak a rendszergazdai jóváhagyást igénylő alkalmazásokhoz. Ha egy felhasználó megpróbál hozzáférni egy alkalmazáshoz, de nem tud beleegyezést adni, akkor a rendszergazda jóváhagyására vonatkozó kérést küldhet. A rendszer e-mailben küldi el a kérést, és egy, a Azure Portal által elérhető várólistába helyezi az összes olyan rendszergazdához, aki felülvizsgáló lett megjelölve. Ha egy felülvizsgáló egy függőben lévő kérelemre vonatkozó műveletet hajt végre, a kérelmező felhasználók értesítést kapnak a műveletről.

További információ: [a rendszergazdai engedélyekkel kapcsolatos munkafolyamat (előzetes verzió) konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>A választható jogcímek (nyilvános előzetes verzió) kezelésére vonatkozó új Azure AD alkalmazás regisztrációs jogkivonat konfigurációs felülete

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Fejlesztői élmény

A Azure Portal új **Azure ad alkalmazás regisztrációs jogkivonat-konfiguráció** panelje mostantól megjeleníti az alkalmazás-fejlesztőknek az alkalmazásokhoz választható jogcímek dinamikus listáját. Ez az új felület megkönnyíti az Azure AD-alkalmazások áttelepítésének egyszerűsítését, és minimálisra csökkenti a választható jogcímek hibás konfigurációit.

További információ: [opcionális jogcímek megadása az Azure ad-alkalmazáshoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Új kétfázisú jóváhagyási munkafolyamat az Azure AD-beli jogosultságok kezelésében (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Jogosultságok kezelése

Új kétlépcsős jóváhagyási munkafolyamatot vezettünk be, amely lehetővé teszi, hogy két jóváhagyót igényeljen a felhasználói kérések egy hozzáférési csomagba való jóváhagyásához. Beállíthatja például úgy, hogy a kérelmező felhasználó felettesének először jóvá kell hagynia, majd azt is megkövetelheti, hogy az erőforrás-tulajdonos jóváhagyja a jóváhagyást. Ha az egyik jóváhagyó nem hagyja jóvá, a hozzáférés nem engedélyezett.

További információ: [hozzáférési csomagra vonatkozó kérelem-és jóváhagyási beállítások módosítása az Azure ad-jogosultságok kezelésében](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>A saját alkalmazások oldal frissítései az új munkaterületekkel együtt (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Mostantól testre szabhatja, hogy a szervezet felhasználói hogyan tekinthetik meg és érhetik el a frissített saját alkalmazások felületét. Ez az új felület az új munkaterületeket is tartalmazza, ami megkönnyíti a felhasználók számára az alkalmazások keresését és rendszerezését.

További információ az alkalmazások új felületéről és a munkaterületek létrehozásáról: [munkaterületek létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>A Google közösségi AZONOSÍTÓjának támogatása az Azure AD B2B együttműködéshez (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** Felhasználói hitelesítés

A Google közösségi azonosítók (Gmail-fiókok) Azure AD-ben való használatának új támogatása lehetővé teszi, hogy egyszerűbben működjenek együtt a felhasználók és partnerek számára. A partnerek számára már nem szükséges új, Microsoft-specifikus fiókot létrehozni és felügyelni. A Microsoft Teams mostantól teljes mértékben támogatja a Google-felhasználókat minden ügyfélen, valamint a közös és a Bérlővel kapcsolatos hitelesítési végpontokon.

További információ: a [Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)számára.

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge Mobile-támogatás feltételes hozzáféréshez és egyszeri bejelentkezéshez (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az iOS és Android rendszerhez készült Microsoft Edge Azure AD mostantól támogatja az Azure AD egyszeri bejelentkezést és a feltételes hozzáférést:

- **Microsoft Edge egyszeri bejelentkezés (SSO):** Az egyszeri bejelentkezés mostantól a natív ügyfeleken (például a Microsoft Outlookban és a Microsoft Edge-ben) is elérhető az összes Azure AD-hez kapcsolódó alkalmazáshoz.

- **Microsoft Edge feltételes hozzáférés:** Az alkalmazás-alapú feltételes hozzáférési szabályzatok segítségével a felhasználóknak Microsoft Intune által védett böngészőket kell használniuk, például a Microsoft Edge-t.

További információk a Microsoft Edge feltételes hozzáférésről és egyszeri bejelentkezésről: a [Microsoft Edge Mobile támogatása a feltételes hozzáféréshez és az egyszeri bejelentkezéshez már általánosan elérhető](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) blogbejegyzés. További információ arról, hogyan állíthatja be az alkalmazásokat az [alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) vagy az [eszközökön alapuló feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)használatával: [webes elérés kezelése Microsoft Intune szabályzattal védett böngésző használatával](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD-jogosultságok kezelése (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Jogosultságok kezelése

Az Azure AD-jogosultságok kezelése egy új identitás-irányítási szolgáltatás, amely segít a szervezeteknek az identitások és a hozzáférések életciklusának nagy léptékű kezelésében. Ez az új funkció a hozzáférési kérelmek munkafolyamatainak automatizálásával, a hozzárendelések, a felülvizsgálatok és a lejárati csoportok, alkalmazások és a SharePoint Online-webhelyeken való lejárattal segít.

Az Azure AD-jogosultságok kezelése révén hatékonyabban kezelheti a hozzáférést az alkalmazottak és a szervezeten kívüli felhasználók számára is, akiknek szükségük van az erőforrásokhoz való hozzáférésre.

További információ: [Mi az az Azure ad-jogosultságok kezelése?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció  

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

[SAP Cloud platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [prioritási mátrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – november 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

November 2019-én a következő 21 új alkalmazást adtuk hozzá az App Gallery összevonási támogatásával:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly címkéjű](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife portál](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal egyszeri bejelentkezés (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko kiskereskedelmi](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & innováció kezelése](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope felhasználói hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), uniFLOW [online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [Jisc tanuló szavazói regisztráció](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Új és továbbfejlesztett Azure AD Application Gallery

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Frissítettük az Azure AD-alkalmazás katalógusát, így könnyebben megtalálhatja azokat az előre integrált alkalmazásokat, amelyek támogatják a kiépítés, az OpenID Connect és az SAML használatát a Azure Active Directory bérlőn.

További információ: [alkalmazás hozzáadása a Azure Active Directory bérlőhöz](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Megnövelt alkalmazás-szerepkör definíciójának hossza 120 és 240 karakter között

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Meghallottuk ügyfeleinktől, hogy egyes alkalmazások és szolgáltatások alkalmazás-szerepkör-definíciós értékének hossza túl rövid 120 karakternél. Válaszként növelte a szerepkör-érték definíciójának maximális hosszát 240 karakterre.

Az alkalmazás-specifikus szerepkör-definíciók használatával kapcsolatos további információkért lásd: az [alkalmazás szerepköreinek hozzáadása az alkalmazásban, és fogadása a jogkivonatban](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>2019. október

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>A identityRiskEvent API elavult változata Azure AD Identity Protection kockázati észlelések esetén  

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

A fejlesztői visszajelzésekre adott válaszként prémium szintű Azure AD P2 előfizetők mostantól összetett lekérdezéseket végezhetnek a Azure AD Identity Protection kockázati észlelési adatain az új riskDetection API-val Microsoft Graph. A meglévő [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API bétaverziója leállítja az adatvisszaadás **2020. január 10**. után. Ha a szervezete a identityRiskEvent API-t használja, térjen át az új riskDetection API-ra.

További információ az új riskDetection API-ról: [kockázatkezelési API-dokumentáció](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Alkalmazásproxy-támogatás a SameSite attribútumhoz és a Chrome 80

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Néhány héttel a Chrome 80 böngésző kiadása előtt azt tervezzük, hogy az alkalmazásproxy-cookie-k hogyan kezelik az **SameSite** attribútumot. A Chrome 80-as kiadásával a **SameSite** attribútumot nem megadó cookie-ket a rendszer úgy kezeli, mintha az `SameSite=Lax`értékre lett állítva.

A változás miatt potenciálisan negatív hatások elkerülése érdekében a következő lépésekkel frissítjük az alkalmazásproxy-hozzáférési és munkamenet-cookie-kat:

- A **Secure cookie használata** beállítás alapértelmezett értékének beállítása **Igen**.

- A **SameSite** attribútum alapértelmezett értékének beállítása **none**.

    >[!NOTE]
    > Az alkalmazásproxy-hozzáférési cookie-k továbbítása mindig kizárólag biztonságos csatornákon keresztül történt. Ezek a módosítások csak a munkamenet-cookie-kra vonatkoznak.

Az alkalmazásproxy cookie-beállításaival kapcsolatos további információkért lásd: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Az alkalmazás regisztrációs portálján (apps.dev.microsoft.com) Alkalmazásregisztrációk (örökölt) és az átszervezett alkalmazások felügyelete többé nem lesz elérhető.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Fejlesztői élmény

A közeljövőben az Azure AD-fiókkal rendelkező felhasználók többé nem tudják regisztrálni és kezelni az átszervezett alkalmazásokat az Application Registration Portal (apps.dev.microsoft.com) használatával, illetve a Alkalmazásregisztrációk (örökölt) alkalmazások regisztrálását és kezelését. élmény a Azure Portalban.

Ha többet szeretne megtudni az új Alkalmazásregisztrációk-élményről, tekintse meg a [Alkalmazásregisztrációk a Azure Portal betanítási útmutatójában](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>A felhasználókra már nincs szükség a felhasználónkénti MFA-ról a feltételes hozzáférés-alapú MFA-re való Migrálás során történő újraregisztráláshoz

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

Kijavítottunk egy ismert problémát, amely során a felhasználóknak újra regisztrálniuk kell, ha le voltak tiltva a felhasználónkénti Multi-Factor Authentication (MFA), majd feltételes hozzáférési szabályzattal engedélyezték az MFA-t.

Ha szeretné megkövetelni, hogy a felhasználók újra regisztráljanak, az Azure AD-portálon a felhasználó hitelesítési módszerei közül kiválaszthatja a **szükséges újbóli regisztrálási MFA** -beállítást. A felhasználók felhasználónkénti MFA-ról feltételes hozzáférés-alapú MFA-re való áttelepítésével kapcsolatos további információkért lásd: [felhasználók konvertálása FELHASZNÁLÓNKÉNTI MFA-ből feltételes hozzáférés-alapú MFA-ra](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Új funkciók a jogcímek átalakításához és küldéséhez az SAML-jogkivonatban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

További képességeket adtunk hozzá, amelyek segítségével testre szabhatja és elküldheti a jogcímeket az SAML-jogkivonatban. Ezek az új képességek a következők:

- További jogcím-átalakítási függvények, amelyek segítenek a jogcímben elküldött értékek módosításában.

- Több átalakítás alkalmazása egyetlen jogcímre.

- A jogcím forrásának megadása a felhasználó típusa és azon csoport alapján, amelyhez a felhasználó tartozik.

További információ ezekről az új képességekről, beleértve a használatuk módját is: [a SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Új bejelentkezési oldal a végfelhasználók számára az Azure AD-ben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** & Jelentéskészítés figyelése

Felvettünk egy új **saját bejelentkezési** oldalt (https://mysignins.microsoft.com), hogy a szervezet felhasználóinak megtekintsék a legutóbbi bejelentkezési Előzményeiket, hogy megnézzék a szokatlan tevékenységeket. Ez az új oldal lehetővé teszi, hogy a felhasználók lássák a következőket:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha egy támadó sikeresen bejelentkezett a fiókjába és a helyről.

- Milyen alkalmazások próbáltak hozzáférni a támadóhoz.

További információ: a felhasználók mostantól megtekinthetik a [szokatlan tevékenységek blogjának bejelentkezési előzményeit](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD Domain Services (Azure AD DS) áttelepítése klasszikusról Azure Resource Manager virtuális hálózatokra

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Ügyfeleinknek, akik klasszikus virtuális hálózatokon ragadtak, nagyszerű hírekkel rendelkezünk! Most már elvégezheti a klasszikus virtuális hálózatról egy meglévő Resource Manager-alapú virtuális hálózatra történő egyszeri áttelepítést. A Resource Manager-alapú virtuális hálózatra való áttérés után kihasználhatja a további és frissített funkciókat, például a részletes jelszóházirendek, az e-mailes értesítések és a naplózási naplók előnyeit.

További információ: [előnézet – Azure ad Domain Services migrálása a klasszikus virtuális hálózati modellből a Resource Managerbe](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Az Azure AD B2C oldal-szerződés elrendezésének frissítései

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Bevezetünk néhány új módosítást a Azure AD B2C 1.2.0. Ebben a frissített verzióban mostantól szabályozhatja az elemek betöltési sorrendjét, ami segíthet a stíluslap (CSS) betöltését követően megjelenő vibrálás leállításában is.

A lap szerződésében történt módosítások teljes listáját a [verzió módosítása naplóban](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)tekintheti meg.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Frissítés a saját alkalmazások lapra az új munkaterületekkel együtt (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Access Control

Mostantól testre szabhatja, hogy a szervezet felhasználói hogyan tekinthetik meg és érhetik el a vadonatúj saját alkalmazások felületét, beleértve az új munkaterületek funkció használatát is, hogy könnyebb legyen számukra az alkalmazások keresése. Az új munkaterületek funkció szűrőként működik azon alkalmazások esetében, amelyekhez a szervezet felhasználói már hozzáférnek.

További információ az alkalmazások új felhasználói élményének és a munkaterületek létrehozásáról: [munkaterületek létrehozása a saját alkalmazások (előzetes verzió) portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>A havi aktív felhasználó-alapú számlázási modell támogatása (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

A Azure AD B2C mostantól támogatja a havi aktív felhasználók (MAU) számlázását. A MAU-számlázás a naptári hónap során a hitelesítési tevékenységgel rendelkező egyedi felhasználók számától függ. A meglévő ügyfelek bármikor válthatnak erre az új számlázási módra.

A 2019. november 1-től kezdődően az összes új ügyfél számlázása ezzel a módszerrel történik. Ez a számlázási módszer előnyökkel jár, és lehetővé teszi, hogy előre tervezze meg az ügyfeleket.

További információ: [frissítés a havi aktív felhasználói számlázási modellre](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – október 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

A 2019-es verzióban az alábbi 35 új alkalmazásokat bővítettük az App Gallery összevonási támogatásával:

[Válság esetén – mobil](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), Juno- [utazás](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [tapintat](https://tact.ai/assistant/), [OpusCapita](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [SHIPHAZMAT](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [tartalom](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue koordináta – EU](https://www.hirevue.com/), [HireVue koordináta-USOnly](https://www.hirevue.com/), [HireVue koordináta – USA](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass portál](https://login.xirrus.com/azure-signup), [paylocityben](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamer](https://theteamie.com/), [sebesség a csapatok számára](https://velocity.peakup.org/teams/login), [signl4 használatával](https://account.signl4.com/manage), [Emődi Navigálás IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega pont](https://pi.ompnt.com/), [Speaking mail for Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [beszéd e-mailek az Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care navigációs rendszer](https://ihealthnav.com/account/signin), [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konszolidált biztonsági menüpont az Azure AD-portálon

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Most már hozzáférhet az összes elérhető Azure AD-biztonsági szolgáltatáshoz az új **biztonsági** menüpontból, illetve a **keresősáv** Azure Portal. Emellett az új **biztonsági** Kezdőlap, az úgynevezett **Biztonság – első lépések**, a nyilvános dokumentációra, a biztonsági útmutatásra és az üzembe helyezési útmutatókra mutató hivatkozásokat is tartalmaz.

Az új **biztonsági** menü a következőket tartalmazza:

- Feltételes hozzáférés
- Identity Protection
- Security Center
- Identitás biztonságos pontszáma
- Hitelesítési módszerek
- MFA
- Kockázati jelentések – kockázatos felhasználók, kockázatos bejelentkezések, kockázati észlelések
- És még sok minden más...

További információ: [Biztonság – első lépések](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Az Office 365-csoportok elévülési szabályzatának továbbfejlesztése az automatikus megújítás révén

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Identitás-életciklus kezelése

Az Office 365-csoportok elévülési szabályzata a tagjai által aktívan használt csoportok automatikus megújítására lett kibővítve. A csoportok az Office 365-alkalmazások, például az Outlook, a SharePoint és a Teams felhasználói tevékenysége alapján automatikusan megújulnak.

Ez a fejlesztés segít csökkenteni a csoport lejárati értesítéseit, és segít biztosítani, hogy az aktív csoportok továbbra is elérhetők legyenek. Ha már rendelkezik aktív lejárati házirenddel az Office 365-csoportok számára, akkor semmit nem kell tennie az új funkció bekapcsolásához.

További információ: [az Office 365-csoportok elévülési szabályzatának konfigurálása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Frissített Azure AD Domain Services (Azure AD DS) létrehozási élmény

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

Frissítettük Azure AD Domain Services (Azure AD DS) új és továbbfejlesztett létrehozási élményét, amely segít a felügyelt tartományok létrehozásában mindössze három kattintással! Emellett az Azure AD DS is feltöltheti és üzembe helyezheti egy sablonból.

További információ: [oktatóanyag: Azure Active Directory Domain Services példány létrehozása és konfigurálása](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>2019. szeptember

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Tervezze meg a változást: a Power BIi csomagok elavulttá váltása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

A 2019. október 1-től kezdődően a Power BI megkezdi az összes tartalomforrás érvénytelenítését, beleértve az Azure AD Power BI Content Pack csomagot is. A Content Pack alternatívájaként az Azure AD-munkafüzetek használatával betekintést nyerhet az Azure AD-hez kapcsolódó szolgáltatásaiba. További munkafüzetek érkeznek, beleértve a feltételes hozzáférési szabályzatokkal kapcsolatos munkafüzeteket a csak jelentés módban, az alkalmazások belefoglalási és egyéb információk alapján.

További információ a munkafüzetekről: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). További információ a tartalmi csomagok érvénytelenítéséről: a [Bejelentési Power bi sablon alkalmazásai általános elérhetősége](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blogbejegyzés.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>A profil átnevezése és integrálása a Microsoft Office Account (fiók) oldallal

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Saját profil/fiók  
A **termék képességei:** Együttműködés

Októbertől kezdve a saját profilom felhasználói élmény lesz. Ennek a változásnak a részeként, ahol jelenleg is azt mondja, **a profilom** **a fiókomra változik.** Az elnevezési változás és az egyes tervezési változások miatt a frissített élmény további integrációt tesz lehetővé a Microsoft Office Account (fiók) oldalával. Pontosabban hozzáférhet az Office-telepítésekhez és-előfizetésekhez az **áttekintő fiók** lapról, valamint az Office-hoz kapcsolódó kapcsolattartási beállítások az **Adatvédelem** lapról.

A saját profil (előzetes verzió) felülettel kapcsolatos további információkért tekintse meg a [profil (előzetes verzió) portál áttekintését](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Csoportok és tagok tömeges kezelése az Azure AD-portálon található CSV-fájlok használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon nyilvánosan elérhető a tömeges csoportok felügyeletének előzetes verziója. Mostantól egy CSV-fájlt és az Azure AD-portált is használhat a csoportok és a tagok listáinak kezeléséhez, többek között a következőket:

- Tagok hozzáadása vagy eltávolítása egy csoportból.

- A csoportok listájának letöltése a címtárból.

- A csoporttagok listájának letöltése egy adott csoportra vonatkozóan.

További információ: [tömeges Hozzáadás](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), tagok tömeges [eltávolítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), tagok [tömeges letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)és csoportos [letöltési csoportok listája](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>A dinamikus beleegyezikés mostantól egy új rendszergazdai engedélyezési végponton keresztül támogatott

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Létrehoztunk egy új rendszergazdai engedélyezési végpontot a dinamikus beleegyezett támogatáshoz, amely hasznos lehet olyan alkalmazásokhoz, amelyek a dinamikus engedélyezési modellt szeretnék használni a Microsoft Identity platformon.

További információ az új végpont használatáról: [a rendszergazdai engedélyezési végpont használata](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Új összevont alkalmazások érhetők el a Azure AD alkalmazás Galleryben – szeptember 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2019 szeptemberében a következő 29 új alkalmazást adtuk hozzá az App Gallery összevonási támogatásával:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO-hozzáférés a Ethidex megfelelőségi irodához™-egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer-portál](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), az [utazás és a költségek](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial) [, a WorkBoard, a](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial) [YeeFlow,](https://apps.yeeflow.com/)az [arc létesítmények](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), a [Luware Stratus csapata](https://stratus.emea.luware.cloud/login), a [széles körű ötletek](https://wideideas.online/wideideas/), a [Prisma-felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial) [, JDLT](https://clients.jdlt.co.uk/login), [RENRAKU, SealPath,](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [biztonságos böngésző](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisma felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [Penneo](https://app.penneo.com/), [Hiretual](https://app.testhtm.com/settings/email-integration), [Cintoo-felhő](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [üzemeltetett örökség online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai csapat létrehozása](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [felderítési előnyök SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [iTask](https://itask.yipinapp.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-azure-ad-global-reader-role"></a>Új Azure AD globális olvasói szerepkör

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control

A 2019. szeptember 24-én kezdődően a globális olvasó nevű új Azure Active Directory (AD) szerepkört fogjuk elkezdeni. Ez a bevezetés az éles és a globális felhőalapú ügyfelekkel (GCC) kezdődik, és a világ minden részén elérhetővé válik.

A globális olvasó szerepkör a globális rendszergazda csak olvasási jogosultsággal rendelkező partnere. Az ebben a szerepkörben lévő felhasználók beolvashatják a beállításokat és a felügyeleti információkat Microsoft 365 szolgáltatások között, de nem végezhetnek felügyeleti műveleteket. Létrehoztuk a globális olvasói szerepkört a szervezet globális rendszergazdáinak számának csökkentése érdekében. Mivel a globális rendszergazdai fiókok hatékonyak és sebezhetők a támadásokkal szemben, javasoljuk, hogy kevesebb mint öt globális rendszergazdával rendelkezzen. Javasoljuk, hogy a globális olvasó szerepkört használja a tervezéshez, a naplózáshoz és a vizsgálatokhoz. Javasoljuk továbbá, hogy a globális olvasó szerepkört más korlátozott rendszergazdai szerepkörökkel, például az Exchange Administrator szolgáltatással együtt használja, hogy a globális rendszergazdai szerepkör nélkül is segítséget nyújtson a munkához.

A globális olvasó szerepkör az új Microsoft 365 felügyeleti központtal, az Exchange felügyeleti központtal, a Teams felügyeleti központtal, a Security Center, a megfelelőségi központtal, az Azure AD felügyeleti központtal és az Eszközkezelő felügyeleti központtal működik

>[!NOTE]
> A nyilvános előzetes verzió elején a globális olvasó szerepkör nem fog működni a következővel: SharePoint, Privileged Access Management, Ügyfélszéf, érzékenységi címkék, csapatok életciklusa, csapatoknak szóló jelentés & a Call Analytics, a Teams IP Phone-eszközök kezelése és a Teams alkalmazás Katalógus. Ezeknek a szolgáltatásoknak a célja, hogy a jövőben is működjenek együtt a szerepkörrel.

További információ: [rendszergazdai szerepkör engedélyei Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Helyszíni jelentéskészítő kiszolgáló elérése a Power BI Mobile alkalmazásból a Azure Active Directory Application Proxy használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

A Power BI Mobile App és az Azure AD Application Proxy közötti új integráció lehetővé teszi, hogy biztonságosan bejelentkezzen a Power BI Mobile alkalmazásba, és megtekintse a szervezete által a helyszíni Power BI jelentéskészítő kiszolgáló üzemeltetett jelentések bármelyikét.

A Power BI Mobile alkalmazással kapcsolatos információkért, beleértve az alkalmazás letöltésének helyét, tekintse meg a [Power bi webhelyet](https://powerbi.microsoft.com/mobile/). A Power BI Mobile App Azure AD Application Proxy használatával történő beállításával kapcsolatos további információkért lásd: [a távoli hozzáférés engedélyezése Power bi Mobile az azure ad Application proxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A AzureADPreview PowerShell-modul új verziója érhető el

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

Új parancsmagok lettek hozzáadva az AzureADPreview modulhoz, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, beleértve a következőket:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect új verziója

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

A Azure AD Connect frissített verzióját közzétettük az ügyfelek automatikus frissítéséhez. Ez az új verzió számos új funkciót, javítást és hibajavítást tartalmaz. További információ erről az új verzióról [: Azure ad Connect: verzió kiadásának előzményei](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Az Azure Multi-Factor Authentication-(MFA-) kiszolgáló, a 8.0.2 verziója már elérhető

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme

Ha Ön egy meglévő ügyfél, aki az MFA-kiszolgálót 2019. július 1. előtt aktiválta, most már letöltheti az MFA-kiszolgáló legújabb verzióját (8.0.2-verzió). Ebben az új verzióban:

- Javítva lett egy probléma, így amikor az Azure AD Sync megváltoztat egy felhasználót a letiltotttól az engedélyezett értékre, a rendszer e-mailt küld a felhasználónak.

- Javítva a probléma, hogy az ügyfelek sikeresen frissíthetnek, miközben továbbra is használhatják a címkék funkciót.

- A koszovói (+ 383) országkód hozzáadva.

- Az egyszeri Mellőzés naplózása a MultiFactorAuthSvc. log naplófájlba lett hozzáadva.

- Továbbfejlesztett teljesítmény a Web Service SDK-hoz.

- Egyéb kisebb hibák kijavítása.

2019. július 1-től kezdődően a Microsoft az MFA-kiszolgáló új üzemelő példányokhoz való felajánlását állította le. A többtényezős hitelesítést igénylő új ügyfeleknek felhőalapú Azure-Multi-Factor Authentication kell használniuk. További információ: [felhőalapú Azure multi-Factor Authentication-telepítés tervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
