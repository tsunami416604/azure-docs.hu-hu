---
title: Archiválja a Azure Active Directory újdonságait? | Microsoft Docs
description: A jelen tartalomforrás Áttekintés szakaszában található Újdonságok kibocsátási megjegyzései 6 hónapos tevékenységet tartalmaznak. 6 hónap elteltével az elemek el lesznek távolítva a főcikkből, és ebbe az archív cikkbe kerül.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: 887bc083a35a065c4b54590c284bfbc794da18e5
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267819"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>Archiválja a Azure Active Directory újdonságait?

A [Azure Active Directory legfontosabb újdonságai? a kiadási megjegyzések](whats-new.md) cikke az elmúlt hat hónapban tartalmaz frissítéseket, míg ez a cikk az összes régebbi információt tartalmazza.

A Azure Active Directory újdonságai a kibocsátási megjegyzések a alábbiakról nyújtanak információt:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

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
 
A munkaterületek, a szűrők rendszergazdák konfigurálhatják a felhasználók alkalmazásai rendszerezését, mostantól gyűjteményekként lesznek hivatkozva. További információ arról, hogyan konfigurálhatja őket a [gyűjtemények létrehozása a saját alkalmazások portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)című témakörben.

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
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – január 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Január 2020-én ezeket a 33 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük: 

[Josa](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [gyors Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LEAVEBOT](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial) [, TripActions,](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial) [SmartWork,](https://www.intumit.com/english/SmartWork.html) [dotcom-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Azure ad SSO Gateway for Oracle E-Business Suite-EBS, PeopleSoft és JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [üzemeltetett MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), munkavégző [vállalat](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB a Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe egy saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), Maxient- [kezelő szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [Áttekintés](https://portal.coreview.com/), [leverni Cloud Office 365 Connector](https://laxmi.squelch.io/login), [PingFlow hitelesítés](https://app-staging.pingview.io/), PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), Taskize [Csatlakozás](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), Sandwai, EZRentOut [, AssetSonar](https://app.sandwai.com/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [akari virtuális asszisztens](https://akari.io/akari-virtual-assistant/) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)

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

A cookie-k számára a biztonságos alapértelmezett modell részeként a Chrome 80 böngésző megváltoztatja a cookie-k attribútum nélküli kezelésének módját `SameSite` . Minden olyan cookie, amely nem adja meg az `SameSite` attribútumot, a rendszer úgy kezeli, mintha a értékre lett állítva `SameSite=Lax` , ami azt eredményezi, hogy a Chrome blokkolja a több tartományon belüli cookie-megosztási forgatókönyveket, amelyektől az alkalmazás függ. A korábbi Chrome-viselkedés fenntartása érdekében használhatja az `SameSite=None` attribútumot, és hozzáadhat egy további `Secure` attribútumot, így a helyek közötti cookie-k csak HTTPS-kapcsolatokon keresztül érhetők el. A Chrome a módosítás befejezését ütemezi a 2020. február 4-én.

Azt javasoljuk, hogy a fejlesztők az alábbi útmutató segítségével tesztelik alkalmazásaikat:

- Állítsa az alapértelmezett értéket a **biztonságos cookie használata** beállításnál az **Igen**értékre.

- Állítsa a **SameSite** attribútum alapértelmezett értékét **none**értékre.

- Adjon hozzá egy további `SameSite` **biztonságos**attribútumot.

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

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [bitly címkéjű](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife portál](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal egyszeri bejelentkezés (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [foko kiskereskedelmi](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & innováció kezelése](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), Netskope [felhasználói hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), uniFLOW [online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis, Jisc](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [Student választópolgár regisztráció](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

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

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: Identity Protection- **termék funkció:** Identity Security & Protection

A fejlesztői visszajelzésekre adott válaszként prémium szintű Azure AD P2 előfizetők mostantól összetett lekérdezéseket végezhetnek a Azure AD Identity Protection kockázati észlelési adatain az új riskDetection API-val Microsoft Graph. A meglévő [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API bétaverziója leállítja az adatvisszaadás **2020. január 10**. után. Ha a szervezete a identityRiskEvent API-t használja, térjen át az új riskDetection API-ra.

További információ az új riskDetection API-ról: [kockázatkezelési API-dokumentáció](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Alkalmazásproxy-támogatás a SameSite attribútumhoz és a Chrome 80

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: app proxy- **termék képesség:** Access Control

Néhány héttel a Chrome 80 böngésző kiadása előtt azt tervezzük, hogy az alkalmazásproxy-cookie-k hogyan kezelik az **SameSite** attribútumot. A Chrome 80 kiadásával a **SameSite** attribútumot nem megadó cookie-ket a rendszer úgy kezeli, mintha a értékre lett állítva `SameSite=Lax` .

A változás miatt potenciálisan negatív hatások elkerülése érdekében a következő lépésekkel frissítjük az alkalmazásproxy-hozzáférési és munkamenet-cookie-kat:

- A **Secure cookie használata** beállítás alapértelmezett értékének beállítása **Igen**.

- A **SameSite** attribútum alapértelmezett értékének beállítása **none**.

    >[!NOTE]
    > Az alkalmazásproxy-hozzáférési cookie-k továbbítása mindig kizárólag biztonságos csatornákon keresztül történt. Ezek a módosítások csak a munkamenet-cookie-kra vonatkoznak.

Az alkalmazásproxy cookie-beállításaival kapcsolatos további információkért lásd: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>A Alkalmazásregisztrációk (örökölt) és az alkalmazások kezelése az alkalmazás regisztrációs portálján (apps.dev.microsoft.com) már nem érhető el

**Írja be a következőt:** A változási **szolgáltatás kategóriájának tervezése:** N/A **termék képesség:** fejlesztői élmény

Az Azure AD-fiókkal rendelkező felhasználók már nem regisztrálhatják és kezelhetik az alkalmazásokat az Application Registration Portal (apps.dev.microsoft.com) használatával, vagy a Azure Portal Alkalmazásregisztrációk (örökölt) felhasználói felületén regisztrálhatnak és kezelhetnek alkalmazásokat.

Ha többet szeretne megtudni az új Alkalmazásregisztrációk-élményről, tekintse meg a [Alkalmazásregisztrációk a Azure Portal betanítási útmutatójában](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>A felhasználókra már nincs szükség a felhasználónkénti MFA-ról a feltételes hozzáférés-alapú MFA-re való Migrálás során történő újraregisztráláshoz

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** MFA- **termék képesség:** Identitáskezelés biztonsági & védelme

Kijavítottunk egy ismert problémát, amely során a felhasználóknak újra regisztrálniuk kell, ha le voltak tiltva a felhasználónkénti Multi-Factor Authentication (MFA), majd feltételes hozzáférési szabályzattal engedélyezték az MFA-t.

Ha szeretné megkövetelni, hogy a felhasználók újra regisztráljanak, az Azure AD-portálon a felhasználó hitelesítési módszerei közül kiválaszthatja a **szükséges újbóli regisztrálási MFA** -beállítást. A felhasználók felhasználónkénti MFA-ról feltételes hozzáférés-alapú MFA-re való áttelepítésével kapcsolatos további információkért lásd: [felhasználók konvertálása FELHASZNÁLÓNKÉNTI MFA-ből feltételes hozzáférés-alapú MFA-ra](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Új funkciók a jogcímek átalakításához és küldéséhez az SAML-jogkivonatban

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

További képességeket adtunk hozzá, amelyek segítségével testre szabhatja és elküldheti a jogcímeket az SAML-jogkivonatban. Ezek az új képességek a következők:

- További jogcím-átalakítási függvények, amelyek segítenek a jogcímben elküldött értékek módosításában.

- Több átalakítás alkalmazása egyetlen jogcímre.

- A jogcím forrásának megadása a felhasználó típusa és azon csoport alapján, amelyhez a felhasználó tartozik.

További információ ezekről az új képességekről, beleértve a használatuk módját is: [a SAML-jogkivonatban kiadott jogcímek testreszabása nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Új bejelentkezési oldal a végfelhasználók számára az Azure AD-ben

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** monitoring & Reporting

Új **bejelentkezési** oldalunk lett hozzáadva ( https://mysignins.microsoft.com) így a szervezet felhasználóinak megtekinthetik a legutóbbi bejelentkezési Előzményeiket, hogy megnézzék a szokatlan tevékenységeket. Ez az új oldal lehetővé teszi, hogy a felhasználók lássák a következőket:

- Ha valaki megpróbálja kitalálni a jelszavát.

- Ha egy támadó sikeresen bejelentkezett a fiókjába és a helyről.

- Milyen alkalmazások próbáltak hozzáférni a támadóhoz.

További információ: a felhasználók mostantól megtekinthetik a [szokatlan tevékenységek blogjának bejelentkezési előzményeit](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD Domain Services (Azure AD DS) áttelepítése klasszikusról Azure Resource Manager virtuális hálózatokra

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Domain Services **termék képesség:** Azure ad Domain Services

Ügyfeleinknek, akik klasszikus virtuális hálózatokon ragadtak, nagyszerű hírekkel rendelkezünk! Most már elvégezheti a klasszikus virtuális hálózatról egy meglévő Resource Manager-alapú virtuális hálózatra történő egyszeri áttelepítést. A Resource Manager-alapú virtuális hálózatra való áttérés után kihasználhatja a további és frissített funkciókat, például a részletes jelszóházirendek, az e-mailes értesítések és a naplózási naplók előnyeit.

További információ: [előnézet – Azure ad Domain Services migrálása a klasszikus virtuális hálózati modellből a Resource Managerbe](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Az Azure AD B2C oldal-szerződés elrendezésének frissítései

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Bevezetünk néhány új módosítást a Azure AD B2C 1.2.0. Ebben a frissített verzióban mostantól szabályozhatja az elemek betöltési sorrendjét, ami segíthet a stíluslap (CSS) betöltését követően megjelenő vibrálás leállításában is.

A lap szerződésében történt módosítások teljes listáját a [verzió módosítása naplóban](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)tekintheti meg.

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Frissítés a saját alkalmazások lapra az új munkaterületekkel együtt (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** saját alkalmazások **termék képesség:** Access Control

Mostantól testre szabhatja, hogy a szervezet felhasználói hogyan tekinthetik meg és érhetik el a vadonatúj saját alkalmazások felületét, beleértve az új munkaterületek funkció használatát is, hogy könnyebb legyen számukra az alkalmazások keresése. Az új munkaterületek funkció szűrőként működik azon alkalmazások esetében, amelyekhez a szervezet felhasználói már hozzáférnek.

További információ az alkalmazások új felhasználói élményének és a munkaterületek létrehozásáról: [munkaterületek létrehozása a saját alkalmazások (előzetes verzió) portálon](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>A havi aktív felhasználó-alapú számlázási modell támogatása (általánosan elérhető)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

A Azure AD B2C mostantól támogatja a havi aktív felhasználók (MAU) számlázását. A MAU-számlázás a naptári hónap során a hitelesítési tevékenységgel rendelkező egyedi felhasználók számától függ. A meglévő ügyfelek bármikor válthatnak erre az új számlázási módra.

A 2019. november 1-től kezdődően az összes új ügyfél számlázása ezzel a módszerrel történik. Ez a számlázási módszer előnyökkel jár, és lehetővé teszi, hogy előre tervezze meg az ügyfeleket.

További információ: [frissítés a havi aktív felhasználói számlázási modellre](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – október 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

A 2019-es verzióban az alábbi 35 új alkalmazásokat bővítettük az App Gallery összevonási támogatásával:

[Válság esetén – mobil](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [tapintat](https://tact.ai/assistant/), [OpusCapita cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), HunchBuzz, [Freshworks](https://login.hunchbuzz.com/integrations/azure/process), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial) [SHIPHAZMAT](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [tartalom](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue koordináta – Európa](https://www.hirevue.com/), [HireVue koordináta-USOnly](https://www.hirevue.com/), [HIREVUE koordináta-USA](https://www.hirevue.com/), [WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass portál](https://login.xirrus.com/azure-signup), [paylocityben](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mail LUCK!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamer](https://theteamie.com/), [a csapatok sebessége](https://velocity.peakup.org/teams/login), [signl4 használatával](https://account.signl4.com/manage), [Emődi Navigálás IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega pont](https://pi.ompnt.com/), [beszélő e-mail az Intune-hoz (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [beszéd e-mailek az Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [,](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [iHealthHome](https://ihealthnav.com/account/signin)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Konszolidált biztonsági menüpont az Azure AD-portálon

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** Identity Protection **termék képesség:** Identity Security & Protection

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

**Írja be a következőt:** A szolgáltatás **szolgáltatásának kategóriája** módosítva: csoport kezelése **termék képesség:** Identity Lifecycle Management

Az Office 365-csoportok elévülési szabályzata a tagjai által aktívan használt csoportok automatikus megújítására lett kibővítve. A csoportok az Office 365-alkalmazások, például az Outlook, a SharePoint és a Teams felhasználói tevékenysége alapján automatikusan megújulnak.

Ez a fejlesztés segít csökkenteni a csoport lejárati értesítéseit, és segít biztosítani, hogy az aktív csoportok továbbra is elérhetők legyenek. Ha már rendelkezik aktív lejárati házirenddel az Office 365-csoportok számára, akkor semmit nem kell tennie az új funkció bekapcsolásához.

További információ: [az Office 365-csoportok elévülési szabályzatának konfigurálása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Frissített Azure AD Domain Services (Azure AD DS) létrehozási élmény

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** Azure ad Domain Services a **termék képességei:** Azure ad Domain Services

Frissítettük Azure AD Domain Services (Azure AD DS) új és továbbfejlesztett létrehozási élményét, amely segít a felügyelt tartományok létrehozásában mindössze három kattintással! Emellett az Azure AD DS is feltöltheti és üzembe helyezheti egy sablonból.

További információ: [oktatóanyag: Azure Active Directory Domain Services példány létrehozása és konfigurálása](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>2019. szeptember

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Tervezze meg a változást: a Power BIi csomagok elavulttá váltása

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: jelentéskészítési **termék képesség:** & jelentéskészítés figyelése

A 2019. október 1-től kezdődően a Power BI megkezdi az összes tartalomforrás érvénytelenítését, beleértve az Azure AD Power BI Content Pack csomagot is. A Content Pack alternatívájaként az Azure AD-munkafüzetek használatával betekintést nyerhet az Azure AD-hez kapcsolódó szolgáltatásaiba. További munkafüzetek érkeznek, beleértve a feltételes hozzáférési szabályzatokkal kapcsolatos munkafüzeteket a csak jelentés módban, az alkalmazások belefoglalási és egyéb információk alapján.

További információ a munkafüzetekről: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). További információ a tartalmi csomagok érvénytelenítéséről: a [Bejelentési Power bi sablon alkalmazásai általános elérhetősége](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blogbejegyzés.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>A profil átnevezése és integrálása a Microsoft Office Account (fiók) oldallal

**Írja be a következőt:** A változási **szolgáltatás kategóriájának tervezése:** saját profil/fiók **termék képesség:** együttműködés

Októbertől kezdve a saját profilom felhasználói élmény lesz. Ennek a változásnak a részeként, ahol jelenleg is azt mondja, **a profilom** **a fiókomra változik.** Az elnevezési változás és az egyes tervezési változások miatt a frissített élmény további integrációt tesz lehetővé a Microsoft Office Account (fiók) oldalával. Pontosabban hozzáférhet az Office-telepítésekhez és-előfizetésekhez az **áttekintő fiók** lapról, valamint az Office-hoz kapcsolódó kapcsolattartási beállítások az **Adatvédelem** lapról.

A saját profil (előzetes verzió) felülettel kapcsolatos további információkért tekintse meg a [profil (előzetes verzió) portál áttekintését](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Csoportok és tagok tömeges kezelése az Azure AD-portálon található CSV-fájlok használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon nyilvánosan elérhető a tömeges csoportok felügyeletének előzetes verziója. Mostantól egy CSV-fájlt és az Azure AD-portált is használhat a csoportok és a tagok listáinak kezeléséhez, többek között a következőket:

- Tagok hozzáadása vagy eltávolítása egy csoportból.

- A csoportok listájának letöltése a címtárból.

- A csoporttagok listájának letöltése egy adott csoportra vonatkozóan.

További információ: [tömeges Hozzáadás](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), tagok tömeges [eltávolítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), tagok [tömeges letöltése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)és csoportos [letöltési csoportok listája](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>A dinamikus beleegyezikés mostantól egy új rendszergazdai engedélyezési végponton keresztül támogatott

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Létrehoztunk egy új rendszergazdai engedélyezési végpontot a dinamikus beleegyezett támogatáshoz, amely hasznos lehet olyan alkalmazásokhoz, amelyek a dinamikus engedélyezési modellt szeretnék használni a Microsoft Identity platformon.

További információ az új végpont használatáról: [a rendszergazdai engedélyezési végpont használata](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Új összevont alkalmazások érhetők el a Azure AD alkalmazás Galleryben – szeptember 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2019 szeptemberében a következő 29 új alkalmazást adtuk hozzá az App Gallery összevonási támogatásával:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [MS Azure SSO hozzáférés a Ethidex megfelelőségi irodához &trade; – egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [iServer-portál](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), az [utazás és a költségek](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), a [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), `https://apps.yeeflow.com/` az [arc létesítmények](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), a [Luware Stratus csapata](https://stratus.emea.luware.cloud/login), a [széles körű ötletek](https://wideideas.online/wideideas/), a [Prisma-felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), a JDLT- [ügyfél központja](https://clients.jdlt.co.uk/login), [a RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), a [SealPath biztonságos böngésző](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), a [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), `https://app.penneo.com/` a, `https://app.testhtm.com/settings/email-integration` a [Cintoo felhő](https://aec.cintoo.com/login), [Discovery Benefits SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial)a Whitesource [,](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial) [a](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial) [szolgáltatott örökség online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [Amelio](https://app.amelio.co/) [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [COO Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial)`https://itask.yipinapp.com/`

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-azure-ad-global-reader-role"></a>Új Azure AD globális olvasói szerepkör

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad-szerepkörök a **termék képességei:** Access Control

A 2019. szeptember 24-én kezdődően a globális olvasó nevű új Azure Active Directory (AD) szerepkört fogjuk elkezdeni. Ez a bevezetés az éles és a globális felhőalapú ügyfelekkel (GCC) kezdődik, és a világ minden részén elérhetővé válik.

A globális olvasó szerepkör a globális rendszergazda csak olvasási jogosultsággal rendelkező partnere. Az ebben a szerepkörben lévő felhasználók beolvashatják a beállításokat és a felügyeleti információkat Microsoft 365 szolgáltatások között, de nem végezhetnek felügyeleti műveleteket. Létrehoztuk a globális olvasói szerepkört a szervezet globális rendszergazdáinak számának csökkentése érdekében. Mivel a globális rendszergazdai fiókok hatékonyak és sebezhetők a támadásokkal szemben, javasoljuk, hogy kevesebb mint öt globális rendszergazdával rendelkezzen. Javasoljuk, hogy a globális olvasó szerepkört használja a tervezéshez, a naplózáshoz és a vizsgálatokhoz. Javasoljuk továbbá, hogy a globális olvasó szerepkört más korlátozott rendszergazdai szerepkörökkel, például az Exchange Administrator szolgáltatással együtt használja, hogy a globális rendszergazdai szerepkör nélkül is segítséget nyújtson a munkához.

A globális olvasó szerepkör az új Microsoft 365 felügyeleti központtal, az Exchange felügyeleti központtal, a Teams felügyeleti központtal, a Security Center, a megfelelőségi központtal, az Azure AD felügyeleti központtal és az Eszközkezelő felügyeleti központtal működik

>[!NOTE]
> A nyilvános előzetes verzió elején a globális olvasó szerepkör nem fog működni a következővel: SharePoint, Privileged Access Management, Ügyfélszéf, érzékenységi címkék, csapatok életciklusa, csapatok jelentése & a Call Analytics, a Teams IP Phone-eszközök kezelése és a Teams app Catalog.

További információ: [rendszergazdai szerepkör engedélyei Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Helyszíni jelentéskészítő kiszolgáló elérése a Power BI Mobile alkalmazásból a Azure Active Directory Application Proxy használatával

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** Access Control

A Power BI Mobile App és az Azure AD Application Proxy közötti új integráció lehetővé teszi, hogy biztonságosan bejelentkezzen a Power BI Mobile alkalmazásba, és megtekintse a szervezete által a helyszíni Power BI jelentéskészítő kiszolgáló üzemeltetett jelentések bármelyikét.

A Power BI Mobile alkalmazással kapcsolatos információkért, beleértve az alkalmazás letöltésének helyét, tekintse meg a [Power bi webhelyet](https://powerbi.microsoft.com/mobile/). A Power BI Mobile App Azure AD Application Proxy használatával történő beállításával kapcsolatos további információkért lásd: [a távoli hozzáférés engedélyezése Power bi Mobile az azure ad Application proxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>A AzureADPreview PowerShell-modul új verziója érhető el

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** egyéb **termék funkció:** könyvtár

Új parancsmagok lettek hozzáadva az AzureADPreview modulhoz, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, beleértve a következőket:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect új verziója

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** egyéb **termék funkció:** könyvtár

A Azure AD Connect frissített verzióját közzétettük az ügyfelek automatikus frissítéséhez. Ez az új verzió számos új funkciót, javítást és hibajavítást tartalmaz. További információ erről az új verzióról [: Azure ad Connect: verzió kiadásának előzményei](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Az Azure Multi-Factor Authentication-(MFA-) kiszolgáló, a 8.0.2 verziója már elérhető

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** MFA- **termék képesség:** Identitáskezelés biztonsági & védelme

Ha Ön egy meglévő ügyfél, aki az MFA-kiszolgálót 2019. július 1. előtt aktiválta, most már letöltheti az MFA-kiszolgáló legújabb verzióját (8.0.2-verzió). Ebben az új verzióban:

- Javítva lett egy probléma, így amikor az Azure AD Sync megváltoztat egy felhasználót a letiltotttól az engedélyezett értékre, a rendszer e-mailt küld a felhasználónak.

- Javítva a probléma, hogy az ügyfelek sikeresen frissíthetnek, miközben továbbra is használhatják a címkék funkciót.

- A koszovói (+ 383) országkód hozzáadva.

- Az egyszeri Mellőzés naplózása a MultiFactorAuthSvc. log naplófájlba lett hozzáadva.

- Továbbfejlesztett teljesítmény a Web Service SDK-hoz.

- Egyéb kisebb hibák kijavítása.

2019. július 1-től kezdődően a Microsoft az MFA-kiszolgáló új üzemelő példányokhoz való felajánlását állította le. A többtényezős hitelesítést igénylő új ügyfeleknek felhőalapú Azure-Multi-Factor Authentication kell használniuk. További információ: [felhőalapú Azure multi-Factor Authentication-telepítés tervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="august-2019"></a>2019. augusztus

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A csoportok továbbfejlesztett keresése, szűrése és rendezése az Azure AD portálon érhető el (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon elérhetővé tettük a bővített csoportok kapcsolódó felhasználói élményének nyilvános előzetes verzióját. Ezek a fejlesztések megkönnyítik a csoportok és a tagok névsorának jobb kezelését a következő biztosításával:

- Speciális keresési funkciók, például alsztringek keresése a csoportok listájára.
- Speciális szűrési és rendezési beállítások a tag és a tulajdonos listán.
- Új keresési funkciók a tagok és a tulajdonosok listájához.
- A nagyméretű csoportok pontosabb csoportosítási száma.

További információ: [csoportok kezelése a Azure Portalban](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Új egyéni szerepkörök érhetők el az alkalmazás-regisztrálási felügyelethez (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad-szerepkörök a **termék képességei:** Access Control

Az Azure AD P1 vagy P2 előfizetéssel elérhető egyéni szerepkörök mostantól részletes hozzáférést biztosíthatnak, ha a szerepkör-definíciókat meghatározott engedélyekkel hozza létre, majd hozzárendeli a szerepköröket adott erőforrásokhoz. Jelenleg egyéni szerepköröket hozhat létre az alkalmazások regisztrálásának kezeléséhez szükséges engedélyekkel, majd hozzárendelheti a szerepkört egy adott alkalmazáshoz. További információ az egyéni szerepkörökről: [Egyéni rendszergazdai szerepkörök Azure Active Directoryban (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Ha további engedélyekre vagy erőforrásokra van szüksége, amelyek jelenleg nem láthatók, küldjön visszajelzést az [Azure feedback-webhelyre](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) , és felvesszük a kérést az Update Road-térképre.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Az új kiépítési naplók segítségével figyelheti és elháríthatja az alkalmazások üzembe helyezésének központi telepítését (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app kiépítési **termék képesség:** az identitás életciklusának kezelése

Az új kiépítési naplók segítenek a felhasználók és csoportok üzembe helyezésének figyelésében és hibakeresésében. Ezek az új naplófájlok a következőkkel kapcsolatos információkat tartalmazzák:

- A [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) sikeresen létrehozott csoportok
- Milyen szerepkörök lettek importálva a [Amazon Web Servicesból (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Milyen alkalmazottak lettek importálva a [munkanapokból](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

További információ: [jelentések kiépítési jelentései a Azure Active Directory portálon (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Új biztonsági jelentések az összes Azure AD-rendszergazda számára (általánosan elérhető)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity Protection- **termék funkció:** Identitáskezelés biztonsági & védelme

Alapértelmezés szerint az összes Azure AD-rendszergazda hamarosan hozzáférhet a modern biztonsági jelentésekhez az Azure AD-n belül. Szeptember végéig a modern biztonsági jelentések tetején található szalagcím használatával visszatérhet a régi jelentésekhez.

A modern biztonsági jelentések további képességeket biztosítanak a régebbi verziókról, többek között a következőkről:

- Speciális szűrés és rendezés
- Tömeges műveletek, például a felhasználói kockázat elvetése
- Sérült vagy biztonságos entitások megerősítése
- Kockázati állapot, amely a következőkre terjed ki: veszélyeztetett, elvetett, szervizelt és megerősített sérült
- Új kockázattal kapcsolatos észlelések (prémium szintű Azure AD előfizetők számára elérhető)

További információ: [kockázatos felhasználók](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users), [kockázatos bejelentkezések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)és [kockázati észlelések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A felhasználó által hozzárendelt felügyelt identitás elérhető Virtual Machines és Virtual Machine Scale Sets (általánosan elérhető)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** felügyelt identitások az Azure-erőforrások **termék képességeihez:** fejlesztői élmény

A felhasználó által hozzárendelt felügyelt identitások mostantól általánosan elérhetők Virtual Machines és Virtual Machine Scale Sets számára. Ennek részeként az Azure olyan identitást hozhat létre az Azure AD-bérlőben, amelyet a használatban lévő előfizetés megbízhatónak tekint, és hozzárendelhető egy vagy több Azure-szolgáltatáshoz. A felhasználó által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](https://aka.ms/azuremanagedidentity)című témakört.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>A felhasználók a Mobile App vagy a Hardware token (általánosan elérhető) használatával állíthatják vissza a jelszavukat

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** önkiszolgáló jelszó-visszaállítási **termék funkció:** felhasználói hitelesítés

Azok a felhasználók, akik regisztráltak egy mobil alkalmazást a szervezettel, most visszaállíthatják a saját jelszavukat, ha jóváhagyják a Microsoft Authenticator alkalmazásból érkező értesítést, vagy megadhatnak egy kódot a Mobile App vagy a Hardware token használatával.

További információkért lásd [: Hogyan működik az Azure ad önkiszolgáló jelszó-visszaállítás](https://aka.ms/authappsspr). További információ a felhasználói élményről: [saját munkahelyi vagy iskolai jelszó átállítása – áttekintés](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>A ADAL.NET figyelmen kívül hagyja a MSAL.NET megosztott gyorsítótárat a következő esetekben:

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az Azure AD Authentication Library (ADAL.NET) verziójának 5.0.0-előzetes verziójától kezdődően az alkalmazások fejlesztőinek [a Web Apps és a webes API-k esetében egy gyorsítótárat kell szerializálni](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Ellenkező esetben előfordulhat, hogy egyes forgatókönyvek, amelyek a (z) [-](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)t használja, és bizonyos használati esetekkel rendelkeznek, a `UserAssertion` jogosultság megemelését eredményezhetik. A biztonsági rés elkerülése érdekében a ADAL.NET mostantól figyelmen kívül hagyja a Microsoft hitelesítési függvénytárát a DotNet (MSAL.NET) megosztott gyorsítótárához a következő esetekben:.

További információ erről a hibáról: [Azure Active Directory a hitelesítési függvénytár jogosultságszint-emelését érintő biztonsági rés](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – augusztus 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2019 augusztusában a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Civic platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), a-ben az [Ops Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial), [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport 's Inativ portál (Európa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy részvétel](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritási mátrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [kézbesítés. &trade; Media Portal](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat ad-kapcsolat](https://www.stashcat.com), [Blink](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial) [, Sigstr,](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)Darwinbox [,](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial) [Emődi](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [karóra színek](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), [hám](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), [navigáljon stratégiai Care](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>A AzureAD PowerShell és a AzureADPreview PowerShell-modulok új verziói érhetők el

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** egyéb **termék funkció:** könyvtár

A AzureAD és a AzureAD előzetes verziójának PowerShell-moduljainak új frissítései elérhetők:

- Új `-Filter` paraméter lett hozzáadva a `Get-AzureADDirectoryRole` paraméterhez a AzureAD modulban. Ez a paraméter segít a parancsmag által visszaadott címtárbeli szerepkörök szűrésében.
- Új parancsmagok lettek hozzáadva az AzureADPreview modulhoz, amely segít az egyéni szerepkörök definiálásában és hozzárendelésében az Azure AD-ben, beleértve a következőket:

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>A dinamikus csoportházirend-szerkesztő felhasználói felületének fejlesztései a Azure Portal

**Írja be a következőt:** A szolgáltatás **szolgáltatásának kategóriája** módosítva: csoport felügyelete – **termékcsoport képesség:** együttműködés

A Azure Portal elérhetővé tettük a dinamikus csoportházirend-szerkesztőt, amely megkönnyíti az új szabályok egyszerűbb beállítását, vagy a meglévő szabályok módosítását. Ez a kialakítás lehetővé teszi, hogy csak egy legfeljebb öt kifejezéssel hozzon létre szabályokat. Az eszköz tulajdonságainak listáját is frissítettük, hogy eltávolítsa az elavult eszköz tulajdonságait.

További információ: a [dinamikus tagsági szabályok kezelése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>A hozzáférési felülvizsgálatokkal használható új Microsoft Graph alkalmazás engedélyezése

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosult: a hozzáférési felülvizsgálatok **termék képesség:** identitás-szabályozás

Bevezetünk egy új Microsoft Graph alkalmazás engedélyt, `AccessReview.ReadWrite.Membership` amely lehetővé teszi, hogy az alkalmazások automatikusan hozzanak létre és kérjenek hozzáférési felülvizsgálatokat a csoporttagságok és az alkalmazás-hozzárendelések számára. Ezt az engedélyt az ütemezett feladatok vagy az automatizálás részeként használhatja, anélkül, hogy bejelentkezett felhasználói környezetre lenne szükség.

További információkért tekintse meg a következő [példát: Azure ad hozzáférési felülvizsgálatok létrehozása Microsoft Graph app permissions with PowerShell blog használatával](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Az Azure AD-Tevékenységnaplók mostantól elérhetők a kormányzati Felhőbeli példányok számára Azure Monitor

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Örömmel jelentjük be, hogy az Azure AD-beli adatnaplók mostantól elérhetők a Azure Monitor kormányzati Felhőbeli példányaihoz. Most már küldhet Azure AD-naplókat a Storage-fiókjába vagy egy Event hub-ba az SIEM-eszközökkel, például a [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), a [splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)és a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)való integráláshoz.

A Azure Monitor beállításával kapcsolatos további információkért lásd: [Az Azure ad-tevékenység naplófájljai Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Frissítse a felhasználókat az új, fokozott biztonsági információkkal kapcsolatos felhasználói élményre

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:**  hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

2019. szeptember 25-én kikapcsoljuk a régi, nem továbbfejlesztett biztonsági információkat a felhasználói biztonsági adatok regisztrálásához és kezeléséhez, és csak az új, [továbbfejlesztett verzió](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)bekapcsolását. Ez azt jelenti, hogy a felhasználók többé nem fogják tudni használni a régi felhasználói élményt.

A fokozott biztonsági információkkal kapcsolatos további információkért tekintse meg a [rendszergazdai dokumentációt](https://aka.ms/securityinfodocs) és a [felhasználói dokumentációt](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Az új felhasználói élmény bekapcsolásához a következőket kell tennie:

1. Jelentkezzen be a Azure Portal globális rendszergazdaként vagy felhasználói rendszergazdaként.

2. Nyissa meg **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése lehetőséget**.

3. A **felhasználók használhatják az előzetes verziójú funkciókat a biztonsági adatok regisztrálásához és kezeléséhez – bővített** terület, válassza a **kijelölt**lehetőséget, majd válasszon ki egy felhasználói csoportot, vagy válassza az **összes** lehetőséget a szolgáltatás bekapcsolásához a bérlő összes felhasználója számára.

4. A * * felhasználók használhatják az előzetes verziójú funkciókat a Security * * info * * * * terület regisztrálásához és kezeléséhez, válassza a **nincs**lehetőséget.

5. Mentse a beállításokat.

    A beállítások mentése után már nem fog tudni hozzáférni a régi biztonsági információkhoz.

>[!Important]
>Ha nem hajtja végre ezeket a lépéseket a 2019. szeptember 25. előtt, a Azure Active Directory bérlője automatikusan engedélyezve lesz a továbbfejlesztett felhasználói élményhez. Ha kérdése van, kérjük, lépjen kapcsolatba velünk a következő címen: registrationpreview@microsoft.com .

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>A POST bejelentkezéseket használó hitelesítési kérelmeket a rendszer szigorúan érvényesíti

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** hitelesítések (bejelentkezések) a **termék képességei:** szabványok

2019. szeptember 2-ától kezdődően a POST metódust használó hitelesítési kérelmeket a rendszer szigorúan érvényesíti a HTTP-szabványokkal szemben. A szóközök és a kettős idézőjelek (") nem lesznek eltávolítva a kérelmek űrlapjának értékeiből. Ezeknek a változásoknak nem kell megszüntetniük a meglévő ügyfeleket, és meg kell győződniük arról, hogy az Azure AD-ba küldött kérelmek minden alkalommal megbízhatóan kezelhetők.

További információ: az [Azure ad Breaking Changes – megjegyzések](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>2019. július

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Tervezze meg a változást: az Application proxy szolgáltatás frissítése csak a TLS 1,2-et támogatja

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: app proxy- **termék képesség:** Access Control

Ha segítségre van szüksége a legerősebb titkosításhoz, megkezdjük az alkalmazásproxy szolgáltatás elérésének korlátozását csak a TLS 1,2 protokollokhoz. Ez a korlátozás kezdetben a TLS 1,2 protokollokat használó ügyfelek számára lesz bevezetve, így a hatás nem fog megjelenni. A TLS 1,0 és a TLS 1,1 protokollok teljes elavulása a 2019. augusztus 31-én fejeződik be. A TLS 1,0-et és a TLS 1,1-t használó ügyfeleink speciális értesítést kapnak a változás előkészítéséhez.

Annak érdekében, hogy az alkalmazásproxy szolgáltatáshoz való csatlakozás megmaradjon a változás során, javasoljuk, hogy az ügyfél-kiszolgáló és a böngészőalapú kiszolgálók kombinációit a TLS 1,2 használatára frissítse. Azt is javasoljuk, hogy minden olyan ügyfélszoftvert tartalmazzon, amelyet az alkalmazottak az alkalmazásproxy szolgáltatáson keresztül közzétett alkalmazások eléréséhez használnak.

További információ: helyszíni [alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Tervezze meg a változást: a terv frissítései az alkalmazás-katalógushoz jönnek

**Írja be a következőt:** A Change **szolgáltatás kategóriájának** megtervezése: vállalati alkalmazások **termék képesség:** SSO

Új felhasználói felületi változások jönnek létre a **Hozzáadás** az **alkalmazás hozzáadása** panel katalógus területéről. Ezek a változások könnyebben megtalálják az alkalmazásokat, amelyek támogatják az automatikus kiépítés, az OpenID Connect, a Security Assertion Markup Language (SAML) és a jelszavas egyszeri bejelentkezést (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Tervezze meg a változást: az MFA-kiszolgáló IP-címének eltávolítása az Office 365 IP-címről

**Írja be a következőt:** A Change **Service kategóriájának** megtervezése: MFA- **termék képesség:** Identitáskezelés biztonsági & védelme

A rendszer eltávolítja az MFA-kiszolgáló IP-címét az [Office 365 IP-címe és az URL-webszolgáltatás webszolgáltatásból](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Ha ezekkel az oldalakkal frissíti a tűzfalbeállítások frissítését, meg kell győződnie arról, hogy az Azure Multi-Factor Authentication-kiszolgáló című cikk [első lépéseiben](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) az **Azure multi-Factor Authentication-kiszolgáló tűzfal követelményei** című részben ismertetett IP-címek listáját is tartalmazza.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Az alkalmazáshoz tartozó tokenek mostantól megkövetelik az ügyfélalkalmazás létezését az erőforrás-bérlőben

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

2019. július 26-án módosítjuk, hogy az [ügyfél hitelesítő adataival](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)hogyan biztosítjuk az alkalmazáshoz tartozó jogkivonatokat. Korábban az alkalmazások jogkivonatokat kérhetnek más alkalmazások meghívásához, függetlenül attól, hogy az ügyfélalkalmazás a bérlőben volt-e. Frissítettük ezt a viselkedést, így az egybérlős erőforrásokat – más néven webes API-kat – csak az erőforrás-bérlőben található ügyfélalkalmazások hívhatják meg.

Ha az alkalmazás nem az erőforrás-bérlőben található, hibaüzenet jelenik meg, amely szerint a `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` probléma megoldásához létre kell hoznia az ügyfélalkalmazás szolgáltatást a bérlőben, a [rendszergazdai hozzájárulási végpont](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) vagy a [PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)használatával, amely biztosítja, hogy a bérlő a bérlőn belül működjön az alkalmazás engedélyével.

További információ: [Újdonságok a hitelesítéshez](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Az ügyfél és az API közötti meglévő beleegyezés továbbra sem szükséges. Az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Új jelszó nélküli bejelentkezés az Azure AD-be az FIDO2 biztonsági kulcsainak használatával

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az Azure AD-ügyfelek mostantól szabályzatokat állíthatnak be a szervezet felhasználóinak és csoportjainak FIDO2 biztonsági kulcsainak kezeléséhez. A végfelhasználók emellett saját maguk is regisztrálhatják biztonsági kulcsaikat, a kulcsokkal bejelentkezhetnek a Microsoft-fiókjaikat a webhelyekre, miközben a rendszerre képes eszközökön, valamint az Azure AD-hez csatlakoztatott Windows 10-es eszközökön is bejelentkezhetnek.

További információkért lásd: a [jelszó nélküli bejelentkezés engedélyezése az Azure ad-ben (előzetes verzió)](/azure/active-directory/authentication/concept-authentication-passwordless) a rendszergazda által kapcsolatos információkhoz, valamint biztonsági adatok beállítása a végfelhasználókkal kapcsolatos információk [biztonsági kulcsának (előzetes verzió) használatához](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – július 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2019 júliusában az alábbi 18 új alkalmazást bővítettük az alkalmazás-katalógusban található összevonási támogatással:

[Ungerboeck szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [világos minta Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO hozzáférés Ethidex megfelelőségi iroda &trade; ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [absztrakt](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [átfedés](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wanda](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://pharmid.com/), i2B- [kapcsolat](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [beli jfrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **Product képesség:** monitoring & Reporting

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont címtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használatával című témakört.

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>A hálózati biztonsági csoport új Azure AD Domain Services szolgáltatásának címkéje

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Domain Services **termék képesség:** Azure ad Domain Services

Ha nem tudja kezelni az IP-címek és tartományok hosszú listáját, az Azure hálózati biztonsági csoportjában található új **AzureActiveDirectoryDomainServices** hálózati szolgáltatás címkével biztonságossá teheti a Azure ad Domain Services virtuális hálózati alhálózatra irányuló bejövő adatforgalmat.

További információ erről az új szolgáltatási címkéről: [hálózati biztonsági csoportok Azure ad domain Serviceshoz](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás a Azure AD Domain Serviceshoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Domain Services **termék képesség:** Azure ad Domain Services

Örömmel jelentjük be, hogy megjelent az Azure AD tartományi szolgáltatások biztonsági naplózásának nyilvános előzetes verziója. A biztonsági naplózás segítségével az Azure AD tartományi szolgáltatás-portálon keresztül biztosíthatja a hitelesítési szolgáltatások számára a biztonsági naplózási események továbbítását, például az Azure Storage, az Azure Log Analytics-munkaterületek és az Azure Event hub elérését.

További információ: [Azure ad Domain Services biztonsági naplózásának engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Új hitelesítési módszerek a használat & a bepillantást (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** önkiszolgáló jelszó-visszaállítási **termék funkció:** figyelés & jelentéskészítés

Az új hitelesítési módszerek használati & az adatvizsgálati jelentések segítségével megismerheti, hogy az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítás hogyan legyen regisztrálva és használatban a szervezetében, beleértve a regisztrált felhasználók számát az egyes szolgáltatásokhoz, milyen gyakran használják az önkiszolgáló jelszó-visszaállítást a jelszavak alaphelyzetbe állítására, és hogy az Alaphelyzetbe állítás milyen módon történik.

További információ: [hitelesítési módszerek használata & információk (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Új biztonsági jelentések érhetők el az összes Azure AD-rendszergazda számára (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity Protection- **termék funkció:** Identitáskezelés biztonsági & védelme

Az Azure AD-rendszergazdák mostantól kiválaszthatják a meglévő biztonsági jelentések (például a kockázati jelentésre **megjelölt felhasználók** ) felső részén látható szalagcímet, hogy az új biztonsági élményt használják a **kockázatos felhasználók** és a **kockázatos bejelentkezések** jelentéseiben. Az idő múlásával az összes biztonsági jelentés a régebbi verziókról az új verzióra kerül, és az új jelentések a következő további képességeket biztosítják:

- Speciális szűrés és rendezés

- Tömeges műveletek, például a felhasználói kockázat elvetése

- Sérült vagy biztonságos entitások megerősítése

- Kockázati állapot, amely a következőkre terjed ki: veszélyeztetett, elvetett, szervizelt és megerősített sérült

További információ: [kockázatos felhasználók jelentés](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users) és [kockázatos bejelentkezések jelentés](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás a Azure AD Domain Serviceshoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Domain Services **termék képesség:** Azure ad Domain Services

Örömmel jelentjük be, hogy megjelent az Azure AD tartományi szolgáltatások biztonsági naplózásának nyilvános előzetes verziója. A biztonsági naplózás segítségével az Azure AD tartományi szolgáltatás-portálon keresztül biztosíthatja a hitelesítési szolgáltatások számára a biztonsági naplózási események továbbítását, például az Azure Storage, az Azure Log Analytics-munkaterületek és az Azure Event hub elérését.

További információ: [Azure ad Domain Services biztonsági naplózásának engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Új B2B közvetlen összevonás SAML/WS-fed használatával (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2B- **termék funkció:** B2B/B2C

A közvetlen összevonás segít megkönnyíteni az olyan partnerekkel való munkavégzést, akik felügyelt identitási megoldását nem az Azure AD, az SAML-vagy WS-fed szabványokat támogató Identity Systems használatával. Miután létrehozott egy közvetlen összevonási kapcsolatot egy partnerrel, az adott tartományból meghívó új vendég-felhasználók a meglévő szervezeti fiókkal dolgozhatnak, így a vendégek zökkenőmentesen használhatják a felhasználói élményt.

További információ: [közvetlen összevonás AD FS és külső szolgáltatókkal a vendég felhasználók számára (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **Product képesség:** monitoring & Reporting

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont címtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ismétlődő csoportok nevének új keresése az Azure AD-portálon

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

Most, amikor létrehoz vagy frissít egy csoportnevet az Azure AD-portálról, akkor ellenőrizze, hogy van-e duplikálva egy meglévő csoportnév az erőforrásban. Ha azt állapítjuk meg, hogy a nevet már egy másik csoport használja, a rendszer megkéri, hogy módosítsa a nevét.

További információ: [csoportok kezelése az Azure ad-portálon](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Az Azure AD mostantól támogatja a statikus lekérdezési paramétereket a válasz (átirányítás) URI-k között.

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az Azure AD-alkalmazások most már regisztrálhatják és használhatják a válasz-(átirányítási) URI-ket statikus lekérdezési paraméterekkel (például `https://contoso.com/oauth2?idp=microsoft` ) a OAuth 2,0-kérelmek esetében. A statikus lekérdezési paraméter a válasz URI azonosítójának megfelelő karakterláncra vonatkozik, ugyanúgy, mint a válasz URI más része. Ha nincs olyan regisztrált karakterlánc, amely megfelel az URL-cím-dekódolású átirányítás-URI azonosítónak, a rendszer elutasítja a kérelmet. Ha a válasz URI-ja megtalálható, a rendszer a teljes karakterláncot használja a felhasználó átirányítására, beleértve a statikus lekérdezési paramétert is.

A dinamikus válasz URI-k továbbra is tiltottak, mert biztonsági kockázatot jelentenek, és nem használhatók az állapotadatok megőrzésére a hitelesítési kérelem során. Erre a célra használja a `state` paramétert.

Jelenleg a Azure Portal alkalmazás-regisztrációs képernyői továbbra is letiltják a lekérdezési paramétereket. Az alkalmazás jegyzékfájlját azonban manuálisan is szerkesztheti, ha lekérdezési paramétereket ad hozzá az alkalmazáshoz. További információ: [Újdonságok a hitelesítéshez](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Az Azure AD-hez készült Tevékenységnaplók (MS Graph API-k) mostantól a PowerShell-parancsmagok használatával érhetők el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Örömmel jelentjük be, hogy az Azure AD-tevékenységek naplói (naplózási és bejelentkezési jelentések) mostantól elérhetők az Azure AD PowerShell-modulon keresztül. Korábban létrehozhat saját parancsfájlokat MS Graph API-végpontok használatával, és most már kiterjesztjük ezt a képességet a PowerShell-parancsmagokra.

További információ a parancsmagok használatáról: [Azure ad PowerShell-parancsmagok jelentéskészítéshez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Frissített szűrési vezérlők az Azure AD naplózási és bejelentkezési naplóihoz

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Frissítettük a naplózási és bejelentkezési naplózási jelentéseket, így mostantól különböző szűrőket alkalmazhat anélkül, hogy azokat oszlopként kellene hozzáadni a jelentés képernyőjén. Emellett most már eldöntheti, hogy hány szűrőt szeretne megjeleníteni a képernyőn. Ezek a frissítések mind együtt működnek, hogy a jelentések könnyebben olvashatók legyenek, és több hatókört is igénybe lehessen venni.

További információ ezekről a frissítésekről: a [naplózási naplók szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) és [a bejelentkezési tevékenységek szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>2019. június

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Új riskDetections API a Microsoft Graphhoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity Protection- **termék funkció:** Identitáskezelés biztonsági & védelme

Örömmel jelentjük be, hogy a Microsoft Graph új riskDetections API már nyilvános előzetes verzióban érhető el. Ezzel az új API-val megtekintheti a szervezete személyazonosságának védelmével kapcsolatos felhasználói és bejelentkezési kockázati észlelések listáját. Ezzel az API-val hatékonyabban kérdezheti le a kockázati észleléseket, beleértve az észlelési típussal, az állapottal, a szinttel és egyebekkel kapcsolatos részleteket is.

További információ: [kockázatkezelési API-dokumentáció](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – június 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2019 júniusában a következő 22 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Azure ad SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle felhő-infrastruktúra konzol](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise os](https://proptimise.co.uk/software/), [VTIGER CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle kiskereskedelmi merchandising, Oracle Access Manager for Oracle e-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **Product képesség:** monitoring & Reporting

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használatával című témakört.

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Az Azure AD-kiépítési szolgáltatás valós idejű előrehaladásának megtekintése

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** app kiépítési **termék funkció:** az identitás életciklusának kezelése

Frissítettük az Azure AD üzembe helyezési élményét, hogy szerepeljen egy új folyamatjelző sáv, amely megmutatja, hogy meddig tart a felhasználó kiépítési folyamata. Ez a frissített élmény az aktuális ciklusban kiosztott felhasználók számával, valamint a dátummal rendelkező felhasználók számával kapcsolatos információkat is tartalmazza.

További információ: [a felhasználó kiépítési állapotának ellenõrzése](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A céges arculat mostantól megjelenik a kijelentkezés és a hibaüzenetek képernyőjén

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Frissítettük az Azure AD-t, hogy a cég arculata mostantól megjelenjen a kijelentkezés és a hibák képernyőjén, valamint a bejelentkezési oldalon. A funkció bekapcsolásához semmit nem kell tennie, az Azure AD egyszerűen azokat az eszközöket használja, amelyeket már beállított a Azure Portal **vállalati védjegyezés** területén.

A vállalat arculatának beállításával kapcsolatos további információkért lásd: [branding hozzáadása a szervezet Azure Active Directory oldalaihoz](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Az Azure Multi-Factor Authentication-(MFA-) kiszolgáló már nem érhető el az új üzemelő példányokhoz

**Írja be a következőt:** Elavult **szolgáltatáskategória:** MFA- **termék képesség:** Identitáskezelés biztonsági & védelme

2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. A többtényezős hitelesítést a szervezetében megkövetelő új ügyfeleknek most már a felhőalapú Azure-Multi-Factor Authentication kell használniuk. Az MFA-kiszolgálót július 1. előtt aktivált ügyfelek nem fogják látni a változást. Továbbra is letöltheti a legújabb verziót, lekérheti a jövőbeli frissítéseket, és előállíthatja az aktiválási hitelesítő adatokat.

További információ: [Bevezetés az Azure multi-Factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)használatába. További információ a felhőalapú Azure-Multi-Factor Authenticationről: [felhőalapú Azure-beli multi-Factor Authentication üzembe helyezésének tervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>2019. május

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Szolgáltatás módosítása: az alkalmazásproxy szolgáltatásban csak a TLS 1,2 protokollok jövőbeli támogatása

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: app proxy- **termék képesség:** Access Control

A legjobb titkosítás biztosítása érdekében ügyfeleinknek biztosítjuk, hogy csak a TLS 1,2 protokollokra korlátozza a hozzáférést az alkalmazásproxy szolgáltatásban. Ezt a változást fokozatosan a TLS 1,2 protokollokat használó ügyfeleknek kell bevezetni, így nem kell semmilyen változást látnia.

A TLS 1,0 és a TLS 1,1 elavult változata a 2019-as augusztus 31-én történik, de további speciális értesítéseket is biztosítunk, így a változás előkészítéséhez időt kell igénybe vennie. Ennek a módosításnak az előkészítéséhez győződjön meg arról, hogy az ügyfél és a böngésző kiszolgálói kombinációja, beleértve azokat az ügyfeleket, amelyeket a felhasználók az alkalmazásproxy használatával közzétett alkalmazások elérésére használnak, frissülnek a TLS 1,2 protokoll használatával az alkalmazásproxy szolgáltatással létesített kapcsolat fenntartásához. További információ: helyszíni [alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Az alkalmazással kapcsolatos bejelentkezési adatok megtekintéséhez használja a használati és elemzési jelentést.

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **Product képesség:** monitoring & Reporting

Mostantól használhatja a Azure Portal **vállalati alkalmazások** területén található használati és elemzési jelentést a bejelentkezési adatok alkalmazás-központú nézetének beszerzéséhez, beleértve a következőkkel kapcsolatos információkat:

- A szervezet leggyakrabban használt alkalmazásai

- A legtöbb sikertelen bejelentkezést tartalmazó alkalmazások

- Leggyakoribb bejelentkezési hibák az egyes alkalmazásokhoz

A szolgáltatással kapcsolatos további információkért tekintse [meg a használati és adatáttekintési jelentést a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>A felhasználók üzembe helyezésének automatizálása a Cloud apps szolgáltatásban az Azure AD használatával

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **Product képesség:** monitoring & Reporting

Ezeket az új oktatóanyagokat követve az Azure AD kiépítési szolgáltatásával automatizálhatja a következő felhőalapú alkalmazások felhasználói fiókjainak létrehozását, törlését és frissítését:

- [Megfelelés](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Ezt az új [Dropbox-oktatóanyagot](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)is követheti, amely a csoport objektumainak kiépítésével kapcsolatos információkat tartalmaz.

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Az identitások biztonságos pontszáma mostantól elérhető az Azure AD-ben (általános elérhetőség)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** N/A **termék képesség:** Identity Security & Protection

Most már nyomon követheti és javíthatja az identitás biztonsági állapotát az Azure AD biztonságos pontszám funkciója segítségével. Az Identitáskezelés biztonságos pontszáma funkció egyetlen irányítópultot használ, amely a következőket nyújtja:

- Az azonosító biztonsági helyzet tárgyilagos mérése az 1 és 223 közötti pontszám alapján.

- Tervezze meg az identitás biztonsági frissítéseit

- Tekintse át a biztonsági tökéletesítések sikerességét

További információ a személyazonossági biztonsági pontszám funkcióról: [Mi az Identity Secure pontszám a Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Mostantól elérhető az új Alkalmazásregisztrációki élmény (általános rendelkezésre állás)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** fejlesztői élmény

Az új [Alkalmazásregisztrációk](https://aka.ms/appregistrations) élmény már általánosan elérhető. Ez az új felhasználói élmény magában foglalja a Azure Portal és az alkalmazás regisztrációs portáljának összes fontos funkcióját, és a következőkön keresztül fejleszti őket:

- **Hatékonyabb alkalmazások kezelése.** Az alkalmazások különböző portálokon való megjelenítése helyett mostantól egyetlen helyen láthatja az összes alkalmazást.

- **Egyszerűsített alkalmazás regisztrálása.** A továbbfejlesztett navigációs felületről a kijavított engedélyek kiválasztásának folyamata révén könnyebben regisztrálhatók és kezelhetők az alkalmazások.

- **Részletesebb információ.** Az alkalmazással kapcsolatos további részleteket, például a gyors útmutatókat és egyebeket is megtalálhatja.

További információ: a [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) és a [Alkalmazásregisztrációk-élmény már általánosan elérhető!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog bejelentése.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>A kockázatos felhasználók API-ban a személyazonosság védelme érdekében elérhető új képességek

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity Protection- **termék funkció:** Identitáskezelés biztonsági & védelme

Örömmel jelentjük, hogy mostantól a kockázatos felhasználók API-val lekérheti a felhasználók kockázati előzményeit, elhagyhatja a kockázatos felhasználókat, és megerősítheti a felhasználókat a feltörtek szerint. Ezzel a módosítással hatékonyabban frissítheti a felhasználók kockázati állapotát, és megismerheti a kockázati Előzményeiket.

További információkért tekintse meg a [kockázatos felhasználók API-referenciájának dokumentációját](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások – 2019. május

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

A 2019-es verzióban a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [valódi hivatkozások](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [egyszerű aláírás](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [forrasztás](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [lejátszó](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo értékesítések](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [rendszerek](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globális HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum munkahely](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API-felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [vezérlés](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Továbbfejlesztett csoportok létrehozása és kezelése az Azure AD-portálon

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

Továbbfejlesztettük az Azure AD-portálon a csoportokkal kapcsolatos tapasztalatokat. Ezen fejlesztésekkel a rendszergazdák hatékonyabban kezelhetik a csoportok listáját, a tagok listáját, és további létrehozási lehetőségeket biztosíthatnak.

A tökéletesítések a következők:

- Alapszintű szűrés a tagság típusa és a csoport típusa alapján.

- Új oszlopok, például forrás-és e-mail-cím hozzáadása.

- A csoportok, a tagok és a tulajdonosi listák többszörös kiválasztásának lehetősége az egyszerű törléshez.

- Lehetőség van e-mail-cím kiválasztására és a tulajdonosok hozzáadására a csoportok létrehozása során.

További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (általánosan elérhető)

**Írja be a következőt:** A szolgáltatás **szolgáltatásának kategóriája** módosítva: csoport felügyelete – **termékcsoport képesség:** együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>A Microsoft Graph API-végpontok mostantól elérhetők az Azure AD-tevékenységek naplóihoz (általános elérhetőség)

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Örömmel jelentjük be, hogy Microsoft Graph API-végpontok támogatásának általános elérhetősége az Azure AD-tevékenységek naplóiban. Ebben a kiadásban mostantól az Azure AD-naplók 1,0-es verzióját, valamint a bejelentkezési naplók API-jait is használhatja.

További információ: az [Azure ad naplózási naplójának API áttekintése](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>A rendszergazdák mostantól feltételes hozzáférést is használhatnak a kombinált regisztrációs folyamathoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** feltételes hozzáférésű **termék funkció:** Identity Security & Protection

A rendszergazdák mostantól feltételes hozzáférési szabályzatokat hozhatnak létre a kombinált regisztrációs lap általi használatra. Ez magában foglalja a szabályzatok alkalmazását a regisztráció engedélyezéséhez, ha:

- A felhasználók megbízható hálózaton vannak.

- A felhasználók alacsony bejelentkezési kockázatot jelentenek.

- A felhasználók felügyelt eszközön találhatók.

- A felhasználók elfogadják a szervezet használati feltételeit (felhasználási feltételek).

A feltételes hozzáféréssel és a jelszó-visszaállítással kapcsolatos további információkért tekintse meg az [Azure ad kombinált MFA és a jelszó-változtatási regisztrációs élmény blogbejegyzésének feltételes hozzáférését](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). A kombinált regisztrációs folyamat feltételes hozzáférési házirendjeivel kapcsolatos további információkért lásd a [feltételes hozzáférési szabályzatok a kombinált regisztrációhoz](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)című témakört. További információ az Azure AD használati feltételeiről: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>2019. április

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Az Azure AD veszélyforrások felderítésének új észlelése mostantól a Azure AD Identity Protection részeként érhető el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Identity Protection **termék funkció:** Identity Security & Protection

Az Azure AD Threat Intelligence észlelése mostantól a frissített Azure AD Identity Protection funkció részeként érhető el. Ez az új funkció lehetővé teszi, hogy szokatlan felhasználói tevékenységet jelezzen egy adott felhasználó vagy tevékenység számára, amely konzisztens a Microsoft belső és külső veszélyforrások elleni hírszerzési forrásai alapján ismert támadási mintákkal.

A Azure AD Identity Protection frissített verziójával kapcsolatos további információkért tekintse meg a [négy jelentős Azure ad Identity Protection fejlesztést a nyilvános előzetes verzióban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a mi az [Azure Active Directory Identity Protection (frissítve)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) cikket. Az Azure AD veszélyforrások észlelésével kapcsolatos további információkért tekintse meg a [Azure Active Directory Identity Protection kockázati észlelésekkel](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks) foglalkozó cikket.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Az Azure AD-jogosultságok kezelése mostantól elérhető (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity irányítási **termék képesség:** Identity-irányítás

A nyilvános előzetes verzióban elérhető Azure AD-jogosultsági felügyelet segít az ügyfeleknek a hozzáférési csomagok felügyeletének delegálásában, amely meghatározza, hogy az alkalmazottak és az üzleti partnerek hogyan igényelhetnek hozzáférést, jóvá kell hagynia, és hogy mennyi ideig férhetnek hozzá. A hozzáférési csomagok kezelhetik az Azure AD és az Office 365-csoportok tagságát, a vállalati alkalmazásokban lévő szerepkör-hozzárendeléseket, valamint a SharePoint Online-webhelyek szerepkör-hozzárendeléseit. További információ a jogosultságok kezeléséről az [Azure ad-jogosultságok kezelésének áttekintése című](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)témakörben olvasható. Ha többet szeretne megtudni a Azure AD Identity Governance-funkciók széles köréről, beleértve a Privileged Identity Management, a hozzáférési felülvizsgálatokat és a használati feltételeket, tekintse meg a [Mi az Azure ad Identity Governance?](../governance/identity-governance-overview.md)című témakört.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-Tevékenységnaplók mostantól Azure Monitorban érhetők el (általánosan elérhető)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Az Azure AD-tevékenység naplóival kapcsolatos vizualizációk visszajelzésének megkönnyítése érdekében a Log Analytics új elemzési funkciót vezetünk be. Ez a funkció segít az Azure AD-erőforrásokkal kapcsolatos elemzések megszerzésében interaktív sablonjaink használatával, az úgynevezett munkafüzeteknél. Ezek az előre elkészített munkafüzetek részletes adatokat biztosíthatnak az alkalmazásokhoz és a felhasználókhoz, és a következőket tartalmazzák:

- **Bejelentkezések.** Részletes információkat nyújt az alkalmazásokról és a felhasználókról, beleértve a bejelentkezési helyet, a használatban lévő operációs rendszert vagy böngésző-ügyfelet és-verziót, valamint a sikeres vagy sikertelen bejelentkezések számát.

- **Örökölt hitelesítés és feltételes hozzáférés.** A régi hitelesítést használó alkalmazások és felhasználók részletes adatait tartalmazza, beleértve a feltételes hozzáférési házirendek által aktivált, feltételes hozzáférési házirendeket használó alkalmazások és így tovább Multi-Factor Authentication használatát.

- **Sikertelen bejelentkezések elemzése.** Segít megállapítani, hogy a bejelentkezési hibák egy felhasználói művelet, házirend-problémák vagy az infrastruktúra miatt történnek-e.

- **Egyéni jelentések.** Létrehozhat új, vagy szerkesztheti a meglévő munkafüzeteket, amelyek segítségével testre szabhatja a szervezete által használt adatellenőrzési funkciót.

További információ: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – április 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Április 2019-én a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [átszivárog](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [padok](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085) [, PageDNA, EDUBRITE](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), RStudio- [kapcsolat](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS,](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)Mitel- [kapcsolat](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (szerepköralapú SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [surveymonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakoriságának beállítása és több szerepkör kiválasztása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hozzáférési felülvizsgálatok **termék képesség:** identitás szabályozása

Az Azure AD hozzáférési felülvizsgálatok új frissítései a következőket teszik lehetővé:

- A korábbi, heti, havi, negyedéves és évente meglévő lehetőségeken felül a hozzáférési felülvizsgálatok gyakoriságát **is módosíthatja**félévente.

- Egyetlen hozzáférési felülvizsgálat létrehozásakor több Azure AD-és Azure-erőforrás-szerepkört is kijelölhet. Ebben az esetben minden szerepkör ugyanazokkal a beállításokkal van beállítva, és minden felülvizsgáló értesítést kap egyszerre.

A hozzáférési felülvizsgálat létrehozásával kapcsolatos további információkért lásd: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)felülvizsgálatokban.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail-riasztási rendszer (ek) át lettek irányítva, és minden ügyfél számára új e-mail feladói adatokat küldenek

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** ad-szinkronizáló **termék funkció:** platform

Azure AD Connect az e-mail-riasztási rendszer (ek) átváltásának folyamata, amely egy új e-mail-feladót mutat be. Ennek megoldásához hozzá kell adnia a `azure-noreply@microsoft.com` szervezet engedélyezési listájához, vagy nem fogja tudni fogadni az Office 365, az Azure vagy a Sync Services fontos riasztásait.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Az UPN-utótag módosításai mostantól sikeresek az összevont tartományok között Azure AD Connect

**Írja be a következőt:** Rögzített **szolgáltatás kategóriája:** ad-szinkronizáló **termék funkció:** platform

Most már sikeresen módosíthatja egy felhasználó UPN-utótagját az egyik összevont tartományból egy másik összevont tartományba Azure AD Connectban. Ez a javítás azt jelenti, hogy a szinkronizálási ciklusban többé nem tapasztalja a FederatedDomainChangeError hibaüzenetet, vagy értesítést kap a következőről: "nem lehet frissíteni ezt az objektumot Azure Active Directory, mert a (z) [FederatedUser. UserPrincipalName] attribútum érvénytelen. Frissítse az értéket a helyi címtárszolgáltatások szolgáltatásban.

További információ: [hibaelhárítási hibák a szinkronizálás során](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Fokozott biztonság az App Protection-alapú feltételes hozzáférési szabályzattal az Azure AD-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** feltételes hozzáférésű **termék funkció:** Identity Security & Protection

Az App Protection-alapú feltételes hozzáférés mostantól az alkalmazás- **védelmi házirend megkövetelése** eszközzel érhető el. Ez az új szabályzat segít a szervezet biztonságának növelésében azáltal, hogy megakadályozza a következőket:

- A felhasználók Microsoft Intune licenc nélkül férhetnek hozzá az alkalmazásokhoz.

- A felhasználók nem tudnak Microsoft Intune app Protection-szabályzatot beolvasni.

- A felhasználók a konfigurált Microsoft Intune app Protection-házirend nélkül szereznek hozzáférést az alkalmazásokhoz.

További információkért lásd: [alkalmazás-védelmi szabályzat megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Új támogatás az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez a Microsoft Edge-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** feltételes hozzáférésű **termék funkció:** Identity Security & Protection

Bővítettük a Microsoft Edge-hez készült Azure AD-támogatást, amely új támogatást nyújt az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez. Ha korábban már használta Microsoft Intune Managed Browser, a Microsoft Edge-t használhatja helyette.

Az eszközök és alkalmazások feltételes hozzáférés használatával történő beállításával és kezelésével kapcsolatos további információkért lásd: [felügyelt eszközök megkövetelése a felhőalapú alkalmazásokhoz való hozzáféréshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) , valamint [jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). További információ arról, hogyan kezelhető a hozzáférés a Microsoft Edge használatával Microsoft Intune házirendekkel: az [Internet-hozzáférés kezelése Microsoft Intune házirenddel védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>2019. március

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Az identitási élmény keretrendszere és az egyéni szabályzatok támogatása Azure Active Directory B2C már elérhető (GA)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Mostantól létrehozhat egyéni házirendeket Azure AD B2Cban, beleértve a következő feladatokat, amelyek az Azure SLA-ban támogatottak:

- Egyéni hitelesítési felhasználói útvonalak létrehozása és feltöltése egyéni szabályzatok használatával.

- A felhasználói útvonalak lépésenkénti ismertetése a jogcímek szolgáltatói közötti cseréjeként.

- Feltételes elágazás definiálása felhasználói útvonalakon.

- A jogcímek átalakítása és leképezése valós idejű döntésekben és kommunikációkban való használatra.

- Az egyéni hitelesítési felhasználói útvonalakon REST API-kompatibilis szolgáltatásokat használhat. Például az e-mail-szolgáltatókkal, a CRM és a saját engedélyezési rendszerekkel.

- A összevonása az OpenIDConnect protokollnak megfelelő identitás-szolgáltatókkal rendelkeznek. Például több-bérlős Azure AD-vel, közösségi fiókkal vagy kétfaktoros ellenőrző szolgáltatókkal.

Az egyéni szabályzatok létrehozásával kapcsolatos további információkért tekintse meg az [Egyéni házirendek fejlesztői megjegyzéseit Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) és olvassa el [Alex Simon blogbejegyzését, beleértve az esettanulmányokat is](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – március 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Március 2019-án a következő 14 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással:

[ISEC7 Mobile Exchange-delegált](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [magyarázat-alapú naplózási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [sovány](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool teljesítményű ügyek](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [írisz intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit horizont](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [Tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler-és Atlassian-létesítési összekötők az Azure AD-galériában – március 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app kiépítési **termék képesség:** harmadik féltől származó integráció

A következő alkalmazásokhoz tartozó felhasználói fiókok létrehozásának, frissítésének és törlésének automatizálása:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler három](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://aka.ms/ProvisioningDocumentation)című témakört.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Törölt Office 365-csoportok visszaállítása és kezelése az Azure AD-portálon

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

Most már megtekintheti és kezelheti a törölt Office 365-csoportokat az Azure AD-portálon. Ez a módosítás segít megtekinteni, hogy mely csoportok állíthatók vissza a visszaállításhoz, és hogy véglegesen törli azokat a csoportokat, amelyek nem szükségesek a szervezet számára.

További információ: [lejárt vagy törölt csoportok visszaállítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Az egyszeri bejelentkezés mostantól elérhető az Azure AD SAML-védelemmel ellátott helyszíni alkalmazásokhoz az Application proxyn keresztül (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** Access Control

Mostantól egyszeri bejelentkezéses (SSO) élményt biztosíthat a helyszíni, SAML-hitelesítésen alapuló alkalmazásokhoz, valamint az alkalmazások távoli elérését az Application proxyn keresztül. Az SAML SSO helyszíni alkalmazásokkal való beállításával kapcsolatos további információkért lásd: [SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)használatával.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A megbízhatóság és a felhasználói élmény fokozása érdekében a kérelemben szereplő ügyfélalkalmazások megszakadnak.

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az ügyfélalkalmazások nem tudják megfelelően kiadni több száz azonos bejelentkezési kérést rövid idő alatt. Ezek a kérések, akár sikeresek, akár nem, mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.

Ez a frissítés `invalid_grant` hibaüzenetet küld: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` olyan ügyfélalkalmazások számára, amelyek rövid idő alatt többször is kiadják a duplikált kérelmeket, a normál működés hatókörén kívül. A problémát tapasztaló ügyfélalkalmazások interaktív üzenetet jelenítenek meg, amely megköveteli, hogy a felhasználó újra bejelentkezzen. További információ erről a változásról, valamint arról, hogy az alkalmazás Hogyan oldható meg, ha ez a hiba ütközik: Újdonságok [a hitelesítéshez?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Új naplózási naplók – felhasználói élmény most elérhető

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Létrehoztunk egy új Azure AD- **naplókat** tartalmazó oldalt, amely segít az olvashatóság és az információk megkeresésének javításában. Ha meg szeretné tekinteni **az új naplók** lapot, válassza a **naplók** lehetőséget az Azure ad **tevékenység** szakaszában.

![Új naplózási naplók oldal, a minta adataival](media/whats-new/audit-logs-page.png)

További információ az új **naplók** lapról: [naplózási tevékenység jelentései a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatás a nem megfelelően konfigurált feltételes hozzáférési szabályzatok véletlen rendszergazdai zárolásának megakadályozásához

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** feltételes hozzáférésű **termék funkció:** Identity Security & Protection

Annak megakadályozása érdekében, hogy a rendszergazdák véletlenül zárolják magukat a saját bérlőik nem megfelelően konfigurált feltételes hozzáférési szabályzatokkal, új figyelmeztetéseket hoztunk létre, és frissítettük az útmutatót a Azure Portal. Az új útmutatással kapcsolatos további információkért lásd: [Mi a szolgáltatás függőségei a Azure Active Directory feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Jobb végfelhasználói használati feltételek a mobileszközök esetében

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** használati feltételek a **termék képességei:** irányítás

Frissítettük a meglévő használati feltételeket, amelyek segítségével javítható a mobileszközök használati feltételeinek áttekintése és belefoglalása. Most nagyíthatja és kibővítheti, visszatérhet, letöltheti az adatokat, és kiválaszthatja a hiperhivatkozásokat. A frissített használati feltételekkel kapcsolatos további információkért lásd: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Az Azure AD-beli új tevékenység naplóinak letöltési felülete elérhető

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Most már nagy mennyiségű tevékenységet is letölthet közvetlenül a Azure Portalból. Ez a frissítés a következőket teszi lehetővé:

- Legfeljebb 250 000 sort tölthet le.

- Értesítést kap a letöltés befejeződése után.

- Szabja testre a fájlnevet.

- Határozza meg a kimeneti formátumot JSON-ként vagy CSV-ként.

További információ erről a szolgáltatásról: gyors útmutató [: naplózási jelentés letöltése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Változás megszakítása: az Exchange ActiveSync (EAS) által a feltételek kiértékelésének frissítései

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: feltételes hozzáférésű **termék képesség:** Access Control

Folyamatban van az Exchange ActiveSync (EAS) az alábbi feltételek kiértékelésének frissítése:

- Felhasználói hely, ország, régió vagy IP-cím alapján

- Bejelentkezési kockázat

- Eszközplatform

Ha korábban már használta ezeket a feltételeket a feltételes hozzáférési házirendekben, vegye figyelembe, hogy a feltétel viselkedése változhat. Ha például korábban a felhasználói hely feltételét használta egy házirendben, akkor a rendszer a felhasználó helye alapján kihagyhatja a házirendet.

---

## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható Azure AD SAML-jogkivonat titkosítása (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

Mostantól a támogatott SAML-alkalmazások is konfigurálhatók titkosított SAML-tokenek fogadására. Az Azure AD az Azure AD-ben tárolt tanúsítványból beszerzett nyilvános kulccsal titkosítja a kibocsátott SAML-kijelentéseket, ha az alkalmazás konfigurálva van és használatban van.

Az SAML-jogkivonat titkosításának konfigurálásával kapcsolatos további információkért lásd: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Hozzáférési felülvizsgálat létrehozása csoportok vagy alkalmazások számára az Azure AD hozzáférési felülvizsgálatok használatával

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hozzáférési felülvizsgálatok **termék képesség:** irányítás

Mostantól több csoportot vagy alkalmazást is hozzáadhat egyetlen Azure AD-hozzáférési felülvizsgálatban csoporttagság vagy alkalmazás-hozzárendelés esetén. A különböző csoportokkal vagy alkalmazásokkal való hozzáférési felülvizsgálatok ugyanazokkal a beállításokkal vannak beállítva, és az összes belefoglalt felülvizsgálók egyszerre vannak bejelentve.

További információ az Azure AD hozzáférési felülvizsgálatok használatával történő hozzáférési felülvizsgálat létrehozásáról: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) felülvizsgálatokban

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – február 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Február 2019-án az alábbi 27 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [ég felé Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), engedély kattintás, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip, Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [szeizmikus](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [megoszthat egy álom](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial) [webmódszerek integrációs felhő](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [ismeretek bárhová LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [ou Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [periszkóp adat](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), NetOp [portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), Genesys, [ClickUp hatékonyságnövelő platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Bővített összetett MFA/SSPR-regisztráció

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** önkiszolgáló jelszó-visszaállítási **termék funkció:** felhasználói hitelesítés

Az ügyfelek visszajelzései alapján továbbfejlesztettük a kombinált MFA/SSPR regisztráció előzetes verzióját, így a felhasználók gyorsabban regisztrálhatják biztonsági adataikat mind az MFA-, mind a SSPR.

**Az alábbi lépéseket követve kapcsolja be a felhasználók számára a továbbfejlesztett felhasználói élményt:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be a Azure Portalba, és lépjen **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése**lehetőségre.

2. Azokon a **felhasználóknál, akik használhatják az előzetes verziójú funkciókat a biztonsági adatok regisztrálásához és kezeléséhez – frissítés** lehetőség, válassza a **kiválasztott felhasználók** vagy az **összes felhasználó**funkcióinak bekapcsolását.

A következő hetekben eltávolítjuk a régi, kombinált MFA/SSPR regisztráció előzetes verzióját azon bérlők esetében, amelyeknek még nincs bekapcsolva.

**Az alábbi lépéseket követve ellenőrizheti, hogy a vezérlő el lesz-e távolítva a bérlőhöz:**

1. Globális rendszergazdaként vagy felhasználói rendszergazdaként jelentkezzen be a Azure Portalba, és lépjen **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése**lehetőségre.

2. Ha a **biztonsági adatok regisztrálására és kezelésére szolgáló előzetes verziójú funkciókat használó felhasználók** **nem értékre vannak állítva, akkor**a rendszer eltávolítja a lehetőséget a bérlőről.

Függetlenül attól, hogy korábban bekapcsolta-e a régi, kombinált MFA/SSPR regisztráció előzetes verzióját a felhasználók számára, vagy sem, a régi élmény jövőbeli időpontban ki lesz kapcsolva. Ezért azt javasoljuk, hogy a lehető leghamarabb váltson az új, továbbfejlesztett élményre.

A továbbfejlesztett regisztrációs felülettel kapcsolatos további információkért tekintse [meg az Azure ad kombinált MFA-és jelszó-visszaállítási regisztrációs felületének gyakori fejlesztéseit](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Frissített házirend-kezelési élmény a felhasználói folyamatokhoz

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

A szabályzat létrehozásának és felügyeletének folyamatát frissítettük a felhasználói folyamatok (korábbi nevén a beépített házirendek) egyszerűbbé tétele érdekében. Ez az új felhasználói élmény mostantól az összes Azure AD-bérlő alapértelmezett alapszolgáltatása.

A portál képernyő felső részén található a **visszajelzés küldése** terület a mosoly vagy a komor ikon használatával további visszajelzést és javaslatokat is megadhat.

További információ az új házirend-kezelési feladatokról: a [Azure ad B2C mostantól JavaScript-testreszabást és számos további új funkciót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogot tartalmaz.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Válassza ki a Azure AD B2C által biztosított adott oldal elemeinek verzióját.

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Mostantól kiválaszthatja a Azure AD B2C által biztosított oldal elemeinek egy adott verzióját. Egy adott verzió kiválasztásával tesztelheti a frissítéseket, mielőtt megjelennek az oldalon, és kiszámítható működést érhet el. Emellett a JavaScript-testreszabások engedélyezéséhez is engedélyezheti, hogy bizonyos lapokat is kikényszerítse. A funkció bekapcsolásához nyissa meg a felhasználói folyamatok **Tulajdonságok** lapját.

További információ az oldalelemek adott verzióinak kiválasztásáról: a [Azure ad B2C mostantól JavaScript-testreszabást és számos további új funkciót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogot tartalmaz.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>A B2C (GA) konfigurálható végfelhasználói jelszavának követelményei

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Most már beállíthatja a szervezete jelszavának összetettségét a végfelhasználók számára, ahelyett, hogy a natív Azure AD-beli jelszavas szabályzatot kellene használnia. A felhasználói folyamatok (korábbi nevén beépített szabályzatok) **Tulajdonságok** paneljén kiválaszthatja az **egyszerű** vagy **erős**jelszó bonyolultságát, vagy létrehozhat egy **Egyéni** követelményt is.

A jelszó-összetettségi követelmények konfigurálásával kapcsolatos további információkért lásd: az [összetettségi követelmények konfigurálása a jelszavakhoz a Azure Active Directory B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új alapértelmezett sablonok az egyéni márkás hitelesítési élményekhez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Az új alapértelmezett sablonokat a felhasználói folyamatok (korábbi nevén beépített szabályzatok) **lap elrendezések** paneljén is használhatja, hogy egyéni márkás hitelesítési élményt hozzon létre a felhasználók számára.

További információ a sablonok használatáról: [Azure ad B2C most már rendelkezik JavaScript-testreszabással és számos további új funkcióval](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>VÁLLALATKÖZI együttműködés Active Directory egyszeri jelszó-hitelesítéssel (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2B- **termék funkció:** B2B/B2C

Bevezetünk egy egyszeri jelszavas hitelesítést (OTP) a B2B vendég felhasználói számára, akik nem hitelesíthetők más módon (például Azure AD, Microsoft-fiók (MSA) vagy Google Federation). Ez az új hitelesítési módszer azt jelenti, hogy a vendég felhasználóknak nem kell új Microsoft-fiók létrehozniuk. Ehelyett a meghívások beváltásakor vagy egy megosztott erőforrás elérésekor a vendég felhasználó egy ideiglenes kódot kérhet az e-mail-címre történő küldéshez. Ezt az ideiglenes kódot használva a vendég felhasználó továbbra is bejelentkezhet.

További információ: az [e-mailek egyszeri jelszavas hitelesítése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blog, az [Azure ad lehetővé teszi a megosztást és együttműködést bármilyen fiókkal rendelkező felhasználó számára](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD Application Proxy cookie-beállítások

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** Access Control

Három új cookie-beállítást vezettünk be, amelyek elérhetők az Application proxyn keresztül közzétett alkalmazások számára:

- **Használjon csak HTTP-alapú cookie-t.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy-hozzáférési és munkamenet-cookie-kra. A beállítás bekapcsolása további biztonsági előnyöket biztosít, például segít megakadályozni a cookie-k másolását vagy módosítását az ügyféloldali parancsfájlok használatával. Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon biztonságos cookie-t.** Beállítja az alkalmazásproxy-hozzáférési és munkamenet-cookie-k **biztonságos** jelölőjét. A beállítás bekapcsolása további biztonsági előnyöket biztosít, mivel a cookie-k továbbítása csak TLS-alapú biztonságos csatornákon keresztül történik (például HTTPS). Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon állandó cookie-t.** Megakadályozza, hogy a böngésző bezárása után a hozzáférés-cookie-k lejárnak. Ezek a cookie-k a hozzáférési jogkivonat élettartamának végéig tartanak. A cookie-k azonban alaphelyzetbe állnak, ha eléri a lejárati időt, vagy ha a felhasználó manuálisan törli a cookie-t. Azt javasoljuk, hogy tartsa meg az alapértelmezett **beállítást, és**csak olyan régebbi alkalmazások beállítását kapcsolja be, amelyek nem osztják meg a cookie-kat a folyamatok között.

További információ az új cookie-król: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – január 2019

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Január 2019-én ezeket a 35 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent paletta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler internet-hozzáférés rendszergazdája](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lejárati emlékeztető](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR-megjelenítő](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CORPTAX](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [művelet](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitális Bezárás](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [működőképes](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive,](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial) [Ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [látogasson el](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)a [Korn Ferry Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Új Azure AD Identity Protection fejlesztések (nyilvános előzetes verzió)

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** Identity Protection **termék képesség:** Identity Security & Protection

Örömmel jelentjük be, hogy a következő fejlesztéseket adtuk hozzá a Azure AD Identity Protection nyilvános előzetes ajánlathoz, beleértve a következőket:

- Frissített és integráltabb felhasználói felület

- További API-k

- Továbbfejlesztett kockázatértékelés a gépi tanulás segítségével

- A kockázatos felhasználók és a kockázatos bejelentkezések teljes termékre kiterjedő összehangolása

További információ a fejlesztésekről: [Mi az Azure Active Directory Identity Protection (frissítve)?](https://aka.ms/IdentityProtectionDocs) Ha többet szeretne megtudni, és megoszthatja gondolatait a terméken belüli kérdésekkel.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>A Microsoft Authenticator alkalmazás új alkalmazás-zárolási funkciója iOS-és Android-eszközökön

**Írja be a következőt:** Új szolgáltatás **szolgáltatás kategóriája:** Microsoft Authenticator app **Product képesség:** Identity Security & Protection

Az egyszer használatos PIN-kódok, az alkalmazásadatok és az Alkalmazásbeállítások biztonságosabbá tételéhez bekapcsolhatja az alkalmazás zárolási funkcióját a Microsoft Authenticator alkalmazásban. Az alkalmazás zárolásának bekapcsolása azt jelenti, hogy minden alkalommal, amikor megnyitja a Microsoft Authenticator alkalmazást, a rendszer kérni fogja a PIN-kód vagy a biometrikus adatok hitelesítését.

További információ: [Microsoft Authenticator alkalmazás – gyakori kérdések](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett Azure AD Privileged Identity Management (PIM) exportálási képességei

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

A Privileged Identity Management (PIM) rendszergazdák mostantól exportálják az összes aktív és jogosult szerepkör-hozzárendelést egy adott erőforráshoz, beleértve az összes alárendelt erőforráshoz tartozó szerepkör-hozzárendeléseket is. Korábban nehéz volt, hogy a rendszergazdák teljes listát kapjanak az előfizetéshez tartozó szerepkör-hozzárendelésekről, és minden egyes erőforráshoz hozzá kellett exportálni a szerepkör-hozzárendeléseket.

További információ: a [PIM-beli Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem váltanak csak felhőalapú fiókokra

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** felhasználói felügyelet **termék képesség:** könyvtár

>[!Important]
>A javítás miatt meghallottuk és megértettük a frusztrációt. Ezért ezt a módosítást csak addig a lépésig adtuk vissza, amíg a javítás könnyebben megvalósítható a szervezetében.

Kijavítottunk egy hibát, amelyben a felhasználó DirSyncEnabled jelölője helytelenül lett **hamis** értékre állítva, ha a Active Directory tartományi szolgáltatások (AD DS) objektumot kizárták a szinkronizációs hatókörből, majd az Azure ad-ben a következő szinkronizálási ciklusban helyezték át a Lomtárba. A javítás eredményeképpen, ha a felhasználó ki van zárva a szinkronizálási hatókörből, és ezt követően az Azure AD Lomtárból lett visszaállítva, a felhasználói fiók a várt módon szinkronizálva marad a helyszíni AD-ből, és nem kezelhető a felhőben, mivel a forrás-és a SoA-kiszolgáló a helyszíni AD-ként marad.

A javítás előtt probléma merült fel, ha a DirSyncEnabled jelzőt hamis értékre váltották. Helytelen benyomást tett, hogy ezeket a fiókokat csak felhőalapú objektumokra konvertálták, és a fiókokat a felhőben lehet felügyelni. Azonban a fiókok továbbra is megtartották a SoA-t a helyszínen, és minden szinkronizált tulajdonságot (Shadow Attribute) a helyszíni AD-ből. Ez az állapot több problémát okozott az Azure AD-ben és más Felhőbeli számítási feladatokban (például az Exchange Online-ban), amely a fiókokat az Active Directory-ból szinkronizálva várta, de mostantól csak a csak felhőalapú fiókokkal működött.

Ebben az esetben az egyetlen módja, hogy valóban átalakítson egy szinkronizált-ból-AD-fiókot a csak felhőalapú fiókra, ha letiltja az rSync-t a bérlői szinten, ami egy háttérbeli műveletet indít el a SoA átviteléhez. Ilyen típusú SoA-változásra van szükség (de nem korlátozódik a) a helyszíni kapcsolódó attribútumok (például a LastDirSyncTime és az árnyék attribútumai) tisztítására, és más Felhőbeli számítási feladatokra mutató jeleket küld, hogy a megfelelő objektuma csak Felhőbeli fiókra legyen konvertálva.

Ez a javítás ezért megakadályozza, hogy a közvetlen frissítések az AD-ből szinkronizált felhasználó ImmutableID attribútumán legyenek, ami a múltban bizonyos esetekben szükséges. A tervezés szerint az Azure AD-beli objektum ImmutableID, ahogy azt a név is jelenti, nem változtatható. Az Azure AD Connect Health és Azure AD Connect szinkronizálási ügyfél által megvalósított új szolgáltatások az ilyen forgatókönyvek kezeléséhez érhetők el:

- **Nagy léptékű ImmutableID-frissítés számos felhasználó számára a szakaszos megközelítésekben**

  Tegyük fel például, hogy hosszú AD DS erdők közötti áttelepítést kell végeznie. Megoldás: a Azure AD Connect segítségével **konfigurálja a forrás-horgonyt** , és a felhasználó áttelepítésekor másolja a meglévő ImmutableID-értékeket az Azure ad-ből a helyi AD DS felhasználó ms-DS-konzisztencia-GUID attribútumára az új erdőben. További információ: [using ms-DS-ConsistencyGuid as sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Nagy méretű ImmutableID-frissítések egy adott felvétel sok felhasználója számára**

  A Azure AD Connect megvalósítása során például hiba történt, és most meg kell változtatnia a SourceAnchor attribútumot. Megoldás: tiltsa le az rSync-et a bérlői szinten, és törölje az összes érvénytelen ImmutableID-értéket. További információ: [a címtár-szinkronizálás kikapcsolása az Office 365](/office365/enterprise/turn-off-directory-synchronization)-ben.

- **Helyi felhasználó megkeresése egy meglévő felhasználóval az Azure ad-ben** Például egy, a AD DSban újra létrehozott felhasználó létrehoz egy duplikált értéket az Azure AD-fiókban ahelyett, hogy egy meglévő Azure AD-fiókkal (árva objektummal) társítja. Megoldás: használja a Azure Portal Azure AD Connect Health a forrás-és ImmutableID újrafelhasználásához. További információ: [árva objektum forgatókönyve](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Változás megszakítása: a naplózási és a bejelentkezési naplókra vonatkozó frissítések Azure Monitor

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Jelenleg a naplózási és a bejelentkezési naplókat is közzéteszjük Azure Monitoron keresztül, így zökkenőmentesen integrálhatja a naplófájlokat a SIEM-eszközökkel vagy a Log Analyticsokkal. A visszajelzések alapján, és a szolgáltatás általánosan elérhetővé tételének előkészítésében a következő módosításokat végezjük el a sémán. Ezek a séma-változások és a kapcsolódó dokumentációs frissítések a januári első héten történnek.

#### <a name="new-fields-in-the-audit-schema"></a>Új mezők a naplózási sémában
Új **Művelettípus** mezőt adunk hozzá az erőforráson végrehajtott művelet típusának megadásához. Például: **Hozzáadás**, **frissítés**vagy **Törlés**.

#### <a name="changed-fields-in-the-audit-schema"></a>A naplózási séma módosított mezői
A következő mezők változnak a naplózási sémában:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|Kategória|Ez volt a **szolgáltatás neve** mező. Most már a **naplózási kategóriák** mező. A **szolgáltatás neve** átnevezve lett a **loggedByService** mezőre.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazások kezelése</li></ul>|
|targetResources|A legfelső szinten tartalmazza a **TargetResourceType** .|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Csoportosítás</li></ul>|
|loggedByService|Annak a szolgáltatásnak a nevét adja meg, amely a naplót generálta.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|A naplók eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Módosult mezők a bejelentkezési sémában
A bejelentkezési sémában a következő mezők változnak:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ez volt a **conditionalaccessPolicies** mező. Most már a **appliedConditionalAccessPolicies** mező.|Nincs változás besorolás|Nincs változás besorolás|
|conditionalAccessStatus|A feltételes hozzáférési házirend állapotának eredményét adja meg a bejelentkezéskor. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Disabled (Letiltva)</li></ul>|
|appliedConditionalAccessPolicies: eredmény|A bejelentkezéskor az egyes feltételes hozzáférési szabályzatok állapotának eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Disabled (Letiltva)</li></ul>|

További információ a sémáról: [Az Azure ad-naplók sémájának értelmezése Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>A felügyelt gépi tanulási modell és a kockázati pontszám motorjának Identity Protection-fejlesztése

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** Identity Protection **Product képesség:** kockázati pontszámok

Az Identity Protection szolgáltatással kapcsolatos felhasználói és bejelentkezési kockázatkezelési motor fejlesztései segíthetnek a felhasználók kockázati pontosságának és lefedettségének javításában. A rendszergazdák láthatják, hogy a felhasználói kockázati szint már nem kapcsolódik az adott észlelések kockázati szintjéhez, és a kockázatos bejelentkezési események száma és szintje is növekszik.

A kockázatos észleléseket a felügyelt gépi tanulási modell értékeli ki, amely a felhasználó bejelentkezési funkcióinak és észlelési mintázatának további funkcióival kiszámítja a felhasználói kockázatot. A modell alapján előfordulhat, hogy a rendszergazda magas kockázati pontszámokkal rendelkező felhasználókat talál, még akkor is, ha az adott felhasználóhoz tartozó észlelések alacsony vagy közepes kockázattal rendelkeznek.

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák a Microsoft Authenticator alkalmazással (nyilvános előzetes verzió) alaphelyzetbe állíthatják a saját jelszavukat

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** önkiszolgáló jelszó-visszaállítási **termék funkció:** felhasználói hitelesítés

Az Azure AD-rendszergazdák mostantól alaphelyzetbe állíthatják a saját jelszavukat a Microsoft Authenticator alkalmazás értesítésein vagy egy, a mobil hitelesítő alkalmazásból vagy a hardver-tokenből származó kóddal. A saját jelszavának alaphelyzetbe állításához a rendszergazdák mostantól a következő módszerek közül kettőt képesek használni:

- Microsoft Authenticator alkalmazás értesítése

- Egyéb mobil hitelesítő alkalmazás/hardver token kódja

- E-mail

- Telefonhívás

- Szöveges üzenet

További információ a jelszavak alaphelyzetbe állítására szolgáló Microsoft Authenticator alkalmazás használatáról: [Azure ad önkiszolgáló jelszó-visszaállítás – Mobile App és SSPR (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új Azure AD Cloud Device-rendszergazdai szerepkör (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** az eszköz regisztrálása és kezelése **termék funkciója:** hozzáférés-vezérlés

A rendszergazdák a felhőalapú eszköz rendszergazdai feladatainak elvégzéséhez rendelhetnek felhasználókat az új felhőalapú eszköz rendszergazdai szerepkörhöz. A Cloud Device Administrators szerepkörrel rendelkező felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, valamint beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal.

További információ a szerepkörökről és az engedélyekről: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Az eszközök kezelése az Azure AD új tevékenység-időbélyegével (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** eszköz regisztrálása és kezelése **:** eszköz életciklusának kezelése

Tisztában vagyunk azzal, hogy idővel frissítenie kell és ki kell vonnia a szervezete eszközeit az Azure AD-ben, hogy elkerülje az elavult eszközöket a környezetben. Ennek a folyamatnak az elősegítése érdekében az Azure AD mostantól új tevékenység-időbélyeggel frissíti az eszközöket, és segít az eszközök életciklusának kezelésében.

További információ az időbélyegző beszerzéséről és használatáról [: How to: az elavult eszközök kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák megkövetelhetik a felhasználóktól a használati feltételek elfogadását minden eszközön

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** irányítás

A rendszergazdák most bekapcsolhatják a **felhasználók megkövetelése minden eszközre** lehetőséget, hogy a felhasználók elfogadják a használati feltételeket a bérlőn használt összes eszközön.

További információ: a Azure Active Directory használati [feltételeinek használati feltételei című szakasza](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét egy ismétlődő ütemterv alapján

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** irányítás


A rendszergazdák mostantól bekapcsolhatják a **lejárati hozzájárulási** lehetőséget, hogy a használati feltételek lejárnak az összes felhasználó számára a megadott ismétlődő ütemterv alapján. Az ütemterv lehet évente, kétévente, negyedévente vagy havonta. A használati feltételek lejárta után a felhasználóknak újra el kell fogadniuk.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét az egyes felhasználók ütemterve alapján

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** irányítás

A rendszergazdák mostantól megadhatnak egy időtartamot, amelyet a felhasználónak újra el kell fogadnia a használati feltételeket. A rendszergazdák például megadhatják, hogy a felhasználóknak minden 90 naponként újra el kell fogadniuk a használati feltételeket.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új Azure AD Privileged Identity Management (PIM) e-mailek Azure Active Directory szerepkörökhöz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

A Azure AD Privileged Identity Managementt (PIM) használó ügyfelek mostantól heti kivonatoló e-mailt kaphatnak, beleértve az elmúlt hét nap alábbi adatait:

- A legfontosabb jogosult és állandó szerepkör-hozzárendelések áttekintése

- Szerepköröket aktiváló felhasználók száma

- A PIM-szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-n kívüli szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-ben "állandónak" kitett felhasználók száma

A PIM-vel és az elérhető e-mail-értesítésekkel kapcsolatos további információkért lásd: [e-mailes értesítések a PIM-ben](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>A csoport alapú licencelés mostantól általánosan elérhető

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** egyéb **termék funkció:** könyvtár

A csoportos licencelés kívül esik a nyilvános előzetes verzióban, és már általánosan elérhető. Ennek az általános kiadásnak a részeként a szolgáltatás skálázható, és lehetővé tette a csoportos licencelési hozzárendelések újrafeldolgozását egyetlen felhasználó számára, valamint az Office 365 E3/a3 licenccel rendelkező csoportos licencelés használatát.

A csoport alapú licenceléssel kapcsolatos további információkért lásd: [Mi az a Azure Active Directory csoportos licencelés?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – november 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

November 2018-én a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [végtelen Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge tag portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [plex alkalmazások – klasszikus teszt](https://test.plexonline.com/signon), [plex alkalmazások – klasszikus](https://www.plexonline.com/signon), [plex-alkalmazások – UX-teszt](https://test.cloud.plex.com/sso), [plex-alkalmazások – UX](https://cloud.plex.com/sso), [plex alkalmazások – iam](https://accounts.plex.com/), [mesterségek – gyermekgondozási nyilvántartások, részvétel, &](https://getcrafts.ca/craftsregistration)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD-naplók mostantól az Azure Log Analytics (nyilvános előzetes verzió) szolgáltatással működnek

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Örömmel jelentjük be, hogy most már továbbíthatja Azure AD-naplóit az Azure Log Analyticsba! Ez a legtöbbet kért funkció lehetővé teszi, hogy még jobban hozzáférhessen az elemzésekhez az üzleti, a műveletekhez és a biztonsághoz, valamint az infrastruktúra monitorozásához. További információkért tekintse meg az [Azure-beli Azure Active Directoryi tevékenységek naplóit log Analytics most elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – október 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2018 októberében a következő 14 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással:

[Saját díjátadó pontok, a](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial) [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), a ambyint, a [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), a [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), a [ON24 virtuális környezet](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), a [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), a [Zscaler három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial) [, a Phraseanet,](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)a [kiértékelt](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), a [Workspot-szabályozás](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), a [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial) [, a Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mail-értesítések Azure AD Domain Services

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Domain Services **termék képesség:** Azure ad Domain Services

A Azure AD Domain Services riasztásokat biztosít a Azure Portal a hibás konfigurációkkal és a felügyelt tartománnyal kapcsolatos problémákról. Ezek a riasztások részletes útmutatókat tartalmaznak, így megpróbálhatja elhárítani a problémákat anélkül, hogy kapcsolatba kellene lépnie a támogatási szolgálattal.

Októbertől kezdve testreszabhatja a felügyelt tartomány értesítési beállításait, hogy új riasztások bekövetkezésekor a rendszer e-mailt küldjön egy kijelölt csoportba, így nem kell folyamatosan ellenőriznie a portált a frissítésekhez.

További információ: [Azure ad Domain Services értesítési beállításai](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD-portál támogatja a ForceDelete tartományi API-t az egyéni tartományok törléséhez

**Írja be a következőt:** A szolgáltatás **szolgáltatásának kategóriája megváltozott:** címtárszolgáltatás-kezelő **termék képesség:** könyvtár

Örömmel jelentjük be, hogy mostantól a ForceDelete tartományi API használatával törölheti az egyéni tartományneveket, ha aszinkron módon átnevezi a hivatkozásokat, például a felhasználókat, a csoportokat és az alkalmazásokat az egyéni tartománynévről (contoso.com) a kezdeti alapértelmezett tartománynévre (contoso.onmicrosoft.com).

Ezzel a módosítással gyorsabban törölheti az egyéni tartományneveket, ha a szervezet már nem használja a nevet, vagy ha a tartománynevet egy másik Azure AD-vel kell használnia.

További információ: [Egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>2018. szeptember

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>A dinamikus csoportok rendszergazdai szerepkörére vonatkozó engedélyek frissítve

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** csoport-felügyeleti **termék képesség:** együttműködés

Kijavítunk egy problémát, hogy az adott rendszergazdai szerepkörök most már létrehozhatnak és frissíthetnek dinamikus tagsági szabályokat anélkül, hogy a csoport tulajdonosának kellene lennie.

A szerepkörök a következők:

- Globális rendszergazda

- Intune-rendszergazda

- Felhasználói rendszergazda

További információ: [dinamikus csoport létrehozása és állapotának ellenõrzése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>A harmadik féltől származó alkalmazások egyszerűsített egyszeri bejelentkezési (SSO) konfigurációs beállításai

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

Tisztában vagyunk azzal, hogy az egyszeri bejelentkezés (SSO) a szolgáltatott szoftver (SaaS) alkalmazásokhoz való beállítása az egyes alkalmazások konfigurációjának egyedi jellege miatt kihívást jelenthet. A következő külső SaaS-alkalmazásokhoz készült SSO-konfigurációs beállítások automatikus feltöltéséhez készítettünk egy egyszerűsített konfigurációs élményt:

- Zendesk

- Online ArcGis

- Jamf Pro

Az egykattintásos élmény használatának megkezdéséhez nyissa meg az alkalmazás **Azure Portal**  >  **SSO konfigurációs** lapját. További információ: Saas- [alkalmazások integrációja Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – hol találhatók az adatai? oldalala

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** egyéb **termék képesség:** GoLocal

Válassza ki a vállalat régióját a **Azure Active Directoryból – itt** megtekintheti, hogy melyik Azure-adatközpontban található az Azure ad-adatok az összes Azure ad-szolgáltatáshoz. Az információkat a vállalat régiójának adott Azure AD-szolgáltatásai alapján szűrheti.

A szolgáltatás eléréséhez és további információkért tekintse meg a [Azure Active Directory-hol található az adatai](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új központi telepítési terv érhető el a saját alkalmazások hozzáférési paneljén

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** saját alkalmazások **termék képesség:** SSO

Tekintse meg a saját alkalmazások hozzáférési paneljén elérhető új központi telepítési csomagot ( https://aka.ms/deploymentplans) .
A saját alkalmazások hozzáférési panelje lehetővé teszi, hogy a felhasználók egyetlen helyen megtalálják és hozzáférjenek az alkalmazásaihoz. Ez a portál olyan önkiszolgáló lehetőségeket is biztosít a felhasználók számára, mint például az alkalmazások és csoportok elérésének kérelmezése vagy az erőforrásokhoz való hozzáférés kezelése mások nevében.

További információ: [Mi a My apps portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új hibaelhárítás és támogatás lap a Azure Portal bejelentkezési naplók lapján

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

A Azure Portal **bejelentkezési** lapjának új **Hibaelhárítás és támogatás** lapján segítséget nyújt a rendszergazdáknak az Azure ad-bejelentkezésekkel kapcsolatos problémák elhárításában. Az új lapon a hibakód, a hibaüzenet és a Szervizelési javaslatok (ha vannak) a probléma megoldásához nyújtanak segítséget. Ha nem tudja megoldani a problémát, a **Másolás a vágólapra** lehetőség használatával új módszert biztosítunk a támogatási jegy létrehozásához, amely feltölti a **kérelem azonosítóját** és **dátumát (UTC)** a naplófájlhoz a támogatási jegyben.

![Az új lapot megjelenítő bejelentkezési naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozásához használt egyéni bővítmények tulajdonságainak továbbfejlesztett támogatása

**Írja be a következőt:** A szolgáltatás **szolgáltatásának kategóriája** módosítva: csoport felügyelete – **termékcsoport képesség:** együttműködés

Ezzel a frissítéssel mostantól az **Egyéni bővítmény tulajdonságainak beolvasása** hivatkozásra kattintva megtekintheti a dinamikus felhasználói csoport-szerkesztőt, megadhatja az egyedi alkalmazás azonosítóját, és megkaphatja a dinamikus tagsági szabály létrehozásakor használni kívánt egyéni bővítmény-tulajdonságok teljes listáját. Ez a lista az alkalmazás új egyéni bővítmény-tulajdonságainak beolvasására is frissíthető.

A dinamikus tagsági szabályok egyéni bővítmény-tulajdonságainak használatával kapcsolatos további információkért lásd a [bővítmény tulajdonságai és az egyéni bővítmény tulajdonságai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) című témakört.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: a feltételes hozzáférési **termék funkciói:** az identitás biztonsága és védelme

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)listáján:

- Microsoft To-Do

- Microsoft Stream

További információkért lásd:

- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Új támogatás az önkiszolgáló jelszó-visszaállításhoz a Windows 7/8/8.1 zárolási képernyőjéről

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** SSPR **termék funkció:** felhasználói hitelesítés

Miután beállította ezt az új szolgáltatást, a felhasználók a Windows 7, a Windows 8 vagy a Windows 8,1 operációs rendszert futtató eszközök **zárolási** képernyőjén egy hivatkozást fognak látni a jelszavuk visszaállítására. Ha erre a hivatkozásra kattint, a felhasználó ugyanazzal a jelszó-visszaállítási folyamattal vezérli, mint a böngészőben.

További információ: a [jelszó-visszaállítás engedélyezése a Windows 7, 8 és 8,1](https://aka.ms/ssprforwindows78) rendszerből

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változási Megjegyzés: az engedélyezési kódok többé nem lesznek elérhetők újra

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

A 2018. november 15-én kezdődően az Azure AD nem fogadja el a korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági változás segíti az Azure AD-t az OAuth-specifikációnak megfelelően bevezetni, és a v1 és v2 végpontokon is érvényben lesz.

Ha az alkalmazás az engedélyezési kódokat több erőforráshoz tartozó jogkivonatok lekérésére használja, javasoljuk, hogy a kód segítségével szerezzen be egy frissítési tokent, majd a frissítési token használatával további jogkivonatokat szerezzen be más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési tokenek többször is használhatók több erőforrásban. Egy olyan alkalmazás, amely egy hitelesítési kód újrafelhasználását kísérli meg a OAuth-kód folyamata során, invalid_grant hibát fog kapni.

Ehhez és más protokollok kapcsolódó módosításaihoz tekintse [meg a hitelesítés újdonságait tartalmazó teljes listát](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – szeptember 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2018 szeptemberében a következő 16 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [a](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial) [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), a [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), a [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), a [Ambrus kiry noemi Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), a [hópehely](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), a [NavigoCloud, a Figma, a](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)JOIN.me, a ZEPHYRSSO, a Silverback, a Riverbed Xirrus EasyPass, a [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), a Enlyft SSO az Azure-hoz, surveymonkey, [összehívás](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial) [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial) [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="support-for-additional-claims-transformations-methods"></a>További jogcímek átalakítási módszereinek támogatása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

Bevezetjük az új jogcím-átalakítási módszereket, ToLower () és ToUpper (), amelyek az SAML-tokenekre alkalmazhatók az SAML-alapú **egyszeri bejelentkezés konfigurációs** oldaláról.

További információ: [az SAML-tokenben kiadott jogcímek testreszabása az Azure ad-ben nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissített SAML-alapú alkalmazás konfigurációs felhasználói felülete (előzetes verzió)

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** vállalati alkalmazások **termék funkciója:** SSO

A frissített SAML-alapú alkalmazás konfigurációs felhasználói felületének részeként a következőkre lesz szüksége:

- Frissített útmutató az SAML-alapú alkalmazások konfigurálásához.

- A konfigurációban található hiányzó vagy helytelen elemek láthatósága.

- Több e-mail-cím is felvehető a lejárati tanúsítvány értesítéséhez.

- Új jogcím-átalakítási módszerek, ToLower () és ToUpper () és sok más.

- A saját jogkivonat-aláíró tanúsítványának feltöltése a vállalati alkalmazásokhoz.

- Az SAML-alkalmazások NameID-formátumának beállítása, valamint a NameID értékének megadásának módja.

A frissített nézet bekapcsolásához kattintson az **egyszeri bejelentkezési** oldal tetején található **új felhasználói felület kipróbálása** hivatkozásra. További információ: [oktatóanyag: SAML-alapú egyszeri bejelentkezés konfigurálása alkalmazáshoz Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)használatával.

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory IP-címtartományok módosításai

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: egyéb **termék funkció:** platform

Nagyobb IP-tartományokat vezetünk be az Azure AD-be, ami azt jelenti, hogy ha konfigurálta az Azure AD IP-címtartományt a tűzfalak, az útválasztók vagy a hálózati biztonsági csoportok számára, frissítenie kell őket. Ezt a frissítést így nem kell újra módosítania a tűzfal, az útválasztó vagy a hálózati biztonsági csoportok IP-tartományának konfigurációjában, amikor az Azure AD új végpontokat ad hozzá.

A hálózati forgalom ezen új tartományokra kerül át a következő két hónapban. A megszakítás nélküli szolgáltatás folytatásához ezeket a frissített értékeket a 2018. szeptember 10. előtt adja hozzá az IP-címekhez:

- 20.190.128.0/18

- 40.126.0.0/18

Javasoljuk, hogy ne távolítsa el a régi IP-címtartományt, amíg az összes hálózati forgalom az új tartományokra nem került. Az áthelyezéssel és a régi tartományok eltávolításával kapcsolatos további információkért lásd: [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változási Megjegyzés: az engedélyezési kódok többé nem lesznek elérhetők újra

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

A 2018. november 15-én kezdődően az Azure AD nem fogadja el a korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági változás segíti az Azure AD-t az OAuth-specifikációnak megfelelően bevezetni, és a v1 és v2 végpontokon is érvényben lesz.

Ha az alkalmazás az engedélyezési kódokat több erőforráshoz tartozó jogkivonatok lekérésére használja, javasoljuk, hogy a kód segítségével szerezzen be egy frissítési tokent, majd a frissítési token használatával további jogkivonatokat szerezzen be más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési tokenek többször is használhatók több erőforrásban. Egy olyan alkalmazás, amely egy hitelesítési kód újrafelhasználását kísérli meg a OAuth-kód folyamata során, invalid_grant hibát fog kapni.

Ehhez és más protokollok kapcsolódó módosításaihoz tekintse [meg a hitelesítés újdonságait tartalmazó teljes listát](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Átszervezett biztonsági adatok kezelése az önkiszolgáló jelszó (SSPR) és a Multi-Factor Authentication (MFA) számára

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** SSPR **termék funkció:** felhasználói hitelesítés

Ez az új szolgáltatás lehetővé teszi a felhasználók számára, hogy a SSPR és az MFA-hoz hasonló biztonsági adatokat (például telefonszámot, mobil alkalmazást stb.) kezeljenek egyetlen helyen és környezetben. az előzőekhez képest, ahol két különböző helyen történt.

Ez az összevont élmény a SSPR vagy MFA-t használó felhasználók számára is működik. Továbbá, ha a szervezet nem érvényesíti az MFA-t vagy a SSPR-regisztrációt, a felhasználók továbbra is regisztrálhatják a szervezet által a saját alkalmazások portálon engedélyezett MFA-vagy SSPR-biztonsági adatokat.

Ez egy nyilvános előzetes verzió. A rendszergazdák bekapcsolhatják az új felhasználói élményt (ha szükséges) egy kiválasztott csoporthoz vagy egy bérlő összes felhasználója számára. Az átszervezett felülettel kapcsolatos további információkért tekintse meg a [konvergens élmény blogját](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) .

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új, csak HTTP-alapú cookie-k beállítása az Azure AD Application proxy-alkalmazásokban

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** Access Control

Létezik egy új beállítás, amely **csak HTTP-cookie-kat** használ az alkalmazásproxy alkalmazásaiban. Ezzel a beállítással további biztonságot biztosíthat, ha a HTTPOnly jelzőt a HTTP-válasz fejlécében adja meg mind az alkalmazásproxy-elérési, mind a munkamenet-cookie-k esetében, így leállítja a cookie-hoz való hozzáférést egy ügyféloldali parancsfájlból, valamint megakadályozza a cookie másolását vagy módosítását. Bár ez a jelző korábban nem lett felhasználva, a cookie-k mindig titkosítottak, és TLS-kapcsolatok használatával lettek továbbítva a helytelen módosítások elleni védelem érdekében.

Ez a beállítás nem kompatibilis az olyan alkalmazásokkal, amelyek ActiveX-vezérlőket használnak, például Távoli asztal. Ha ebben a helyzetben van, javasoljuk, hogy kapcsolja ki ezt a beállítást.

A csak HTTP-alapú cookie-k beállításával kapcsolatos további információkért lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Az Azure-erőforrások Privileged Identity Management (PIM) támogatja a felügyeleti csoport erőforrás-típusait

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

Az igény szerinti aktiválási és hozzárendelési beállítások mostantól alkalmazhatók a felügyeleti csoport erőforrásaira, ugyanúgy, mint az előfizetések, az erőforráscsoportok és az erőforrások (például virtuális gépek, App Services stb.). Emellett bárki, aki egy felügyeleti csoport rendszergazdai hozzáférését biztosító szerepkörrel rendelkezik, felderítheti és kezelheti az adott erőforrást a PIM-ben.

A PIM-és az Azure-erőforrásokkal kapcsolatos további információkért lásd: [Azure-erőforrások felderítése és kezelése Privileged Identity Management használatával](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

Ma, amikor a PIM használatával aktiválja a szerepkört, több mint 10 percet is igénybe vehet, amíg az engedélyek érvénybe lépnek. Ha az alkalmazás-hozzáférés használata mellett dönt, amely jelenleg nyilvános előzetes verzióban érhető el, akkor a rendszergazdák az aktiválási kérelem befejeződése után azonnal hozzáférhetnek az Azure AD-portálhoz.

Az alkalmazás-hozzáférés jelenleg csak az Azure AD-portál és az Azure-erőforrások használatát támogatja. További információ a PIM-ről és az alkalmazás-hozzáférésről: [Mi az Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – augusztus 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2018 augusztusában a következő 16 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Szarvascsőrű](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline kötetlen](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs-mobil-és webes tesztelés](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [meta Networks-összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [így teszünk](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentáció](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-költségelszámolás](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 élő csevegés](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív tabló támogatása mostantól elérhető az Azure-ban AD Application Proxy

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosítva: app proxy- **termék képesség:** Access Control

Az OpenID-vel való frissítéssel az előhitelesítési protokollhoz tartozó OAuth 2,0 Code Grant protokollhoz való csatlakozással már nem kell további konfigurálást végeznie a tabló és az alkalmazásproxy használatához. Ez a protokoll változása azt is lehetővé teszi, hogy az alkalmazásproxy jobban támogassa a modern alkalmazásokat csak a HTTP-átirányítások használatával, amelyek JavaScript-és HTML-címkékben általában támogatottak.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Új támogatás a Google identitás-szolgáltatóként való hozzáadásához Azure Active Directoryban (előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2B- **termék funkció:** B2B/B2C

A szervezete Google-nal való összevonásának beállításával lehetővé teheti a meghívott Gmail-felhasználók számára, hogy meglévő Google-Fiókjával jelentkezzenek be a megosztott alkalmazásaiba és erőforrásaiba anélkül, hogy személyes Microsoft-fiókot (MSAs) vagy Azure AD-fiókot kellene létrehoznia.

Ez egy nyilvános előzetes verzió. A Google Federation szolgáltatással kapcsolatos további információkért lásd: a [Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory e-mail-értesítések fejlesztése

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosítva: egyéb **termék funkció:** az identitás életciklusának kezelése

A Azure Active Directory (Azure AD) e-mailek mostantól egy frissített kialakítással, valamint a küldő e-mail-címének és a feladó megjelenített nevének a változásával is rendelkeznek, ha a következő szolgáltatásokból küldenek:

- Azure AD-hozzáférési felülvizsgálatok
- Azure AD Connect Health
- Azure AD Identity Protection
- Azure AD Privileged Identity Management
- Vállalati alkalmazás lejárati tanúsítványának értesítései
- Vállalati alkalmazások kiépítési szolgáltatásával kapcsolatos értesítések

Az e-mail-értesítések a következő e-mail-cím és megjelenítendő név alapján lesznek elküldve:

- E-mail cím: azure-noreply@microsoft.com
- Megjelenítendő név: Microsoft Azure

Az új e-mail-tervekre és további információkra például az [e-mailes értesítések az Azure ad PIM-ben](https://go.microsoft.com/fwlink/?linkid=2005832)című témakörben olvashat.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD-tevékenységek naplói mostantól elérhetők a Azure Monitor

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Az Azure AD-tevékenységek naplói mostantól nyilvános előzetes verzióban érhetők el a Azure Monitor (az Azure platform szintű figyelési szolgáltatása). A Azure Monitor a következő fejlesztéseken túl hosszú távú adatmegőrzést és zökkenőmentes integrációt biztosít:

- Hosszú távú adatmegőrzés a naplófájlok saját Azure Storage-fiókba való átirányításával.

- Zökkenőmentes SIEM-integráció, anélkül, hogy az egyéni parancsfájlok írására vagy karbantartására lenne szükség.

- Zökkenőmentes integráció saját egyéni megoldásokkal, elemzési eszközökkel vagy incidens-kezelési megoldásokkal.

Az új képességekkel kapcsolatos további információkért tekintse meg a blog [Azure ad-tevékenységek naplói a Azure monitor Diagnostics szolgáltatásban már nyilvános előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) verzióban és a dokumentációban [Azure Active Directory a tevékenység naplóit a Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)című témakörben.

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentéshez hozzáadott feltételes hozzáférési információk

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** Identitáskezelés biztonsági & védelme

Ezzel a frissítéssel megtekintheti, hogy mely házirendeket értékeli ki a rendszer, amikor a felhasználó bejelentkezik a szabályzat eredményével együtt. Emellett a jelentés most már tartalmazza a felhasználó által használt ügyfélalkalmazás típusát is, így azonosíthatja a régi protokoll forgalmát. A jelentési bejegyzéseket is megkeresheti egy korrelációs AZONOSÍTÓra, amely a felhasználó által megtekinthető hibaüzenetben található, és felhasználható a megfelelő bejelentkezési kérelem azonosítására és megoldására.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Örökölt hitelesítések megtekintése a bejelentkezési tevékenység naplóiban

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

A bejelentkezési tevékenység naplóiban a **Client app (ügyfélalkalmazás** ) mező bevezetésével az ügyfelek már láthatják az örökölt hitelesítést használó felhasználókat. Az ügyfelek a bejelentkezések Microsoft Graph API-val vagy az Azure AD-portálon keresztül érhetik el ezeket az információkat, ahol az **ügyfélalkalmazás** vezérlőelem használatával szűrheti az örökölt hitelesítéseket. További részletekért tekintse meg a dokumentációt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – július 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2018 júliusában a következő 16 új alkalmazást adtuk hozzá az App Gallery összevonási támogatásával:

[Innovációs központ](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bizonyos adminisztrációs egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC-előkészítés, [ipass-SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [képernyőfelvétel-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified osztályterem, [Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)bevezetése, Bomgar-alapú [távoli támogatás](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagine webvízió](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow-összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](../saas-apps/kanbanize-tutorial.md), [SmartLPA](../saas-apps/smartlpa-tutorial.md), [ismeretek alapja](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Új felhasználó üzembe helyezése SaaS-alkalmazás-integrációk – július 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app kiépítési **termék képesség:** harmadik féltől származó integráció

Az Azure AD lehetővé teszi, hogy automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását SaaS-alkalmazásokban, például a Dropbox, a Salesforce, a ServiceNow és más rendszerekben. Július 2018-én a következő alkalmazásokhoz is hozzáadunk felhasználói üzembe helyezési támogatást az Azure AD App Galleryben:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók kiépítését támogató összes alkalmazás listáját itt tekintheti meg: [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>A kapcsolódási állapot szinkronizáláshoz – egyszerűbb megoldás az árva és ismétlődő attribútumok szinkronizálási hibáinak kijavításához

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** ad-kapcsolati **termék funkció:** figyelés & jelentéskészítés

A Azure AD Connect Health önkiszolgáló szervizelést vezet be, amely segít kiemelni és elhárítani a szinkronizálási hibákat. Ez a szolgáltatás a duplikált attribútum-szinkronizálási hibákat és az Azure AD-ből származó, árva objektumokat javítja. Ez a diagnózis a következő előnyökkel jár:

- Leszűkíti a duplikált attribútum-szinkronizálási hibákat, és adott javításokat biztosít

- A dedikált Azure AD-forgatókönyvek javításának alkalmazása, a hibák feloldása egyetlen lépésben

- A funkció bekapcsolásához és használatához nincs szükség frissítésre vagy konfigurálásra

További információ: [duplikált attribútum-szinkronizálási hibák diagnosztizálása és szervizelése](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuális frissítések az Azure AD-hez és MSA bejelentkezési tapasztalatai

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosult: Azure ad- **termék funkció:** felhasználói hitelesítés

Frissítettük a Microsoft online szolgáltatások bejelentkezési FELÜLETét, például az Office 365-es és az Azure-ban. Ezzel a módosítással a képernyők kevésbé zsúfoltak és egyszerűbbek. További információ erről a változásról: az [Azure ad bejelentkezési élményének közelgő újdonságai](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect új kiadása – július 2018

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** app kiépítési **termék funkció:** az identitás életciklusának kezelése

A Azure AD Connect legújabb kiadása a következőket tartalmazza:

- Hibajavítások és támogatási frissítések

- A ping-összevonása integráció általános elérhetősége

- A legújabb SQL 2012-ügyfél frissítései

További információ erről a frissítésről [: Azure ad Connect verzió kiadási előzményei](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>A használati feltételek végfelhasználói felhasználói felületének frissítése

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** használati feltételek a **termék képességei:** irányítás

Frissítjük az elfogadási karakterláncot a felhasználási feltételek végfelhasználói felhasználói felületén.

**Aktuális szöveg** A [tenantName] erőforrásainak eléréséhez el kell fogadnia a használati feltételeket.<br>**Új szöveg.** A (z) [tenantName] erőforrás eléréséhez el kell olvasnia a használati feltételeket.

**Aktuális szöveg:** Az elfogadás kiválasztása azt jelenti, hogy elfogadja a fenti használati feltételeket.<br>**Új szöveg:** Az elfogadás gombra kattintva erősítse meg, hogy elolvasta és megértette a használati feltételeket.

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja az örökölt protokollokat és alkalmazásokat

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az átmenő hitelesítés mostantól támogatja az örökölt protokollokat és alkalmazásokat. A következő korlátozások mostantól teljes mértékben támogatottak:

- Felhasználói bejelentkezések a régi Office-ügyfélalkalmazásokba, az Office 2010-be és az Office 2013-be, modern hitelesítés nélkül.

- Hozzáférés a naptár megosztásához és az ingyenes/foglalt információkhoz az Exchange hibrid környezetekben az Office 2010-ben.

- Felhasználói bejelentkezések a Skype for Business-ügyfélalkalmazások számára anélkül, hogy modern hitelesítést kellene igényelni.

- Felhasználói bejelentkezések a PowerShell 1,0-es verziójára.

- Az Apple Készülékregisztrációs program (Apple DEP) az iOS beállítási asszisztens használatával.

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Átszervezett biztonsági adatok kezelése az önkiszolgáló jelszó-visszaállításhoz és Multi-Factor Authentication

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** SSPR **termék funkció:** felhasználói hitelesítés

Ez az új szolgáltatás lehetővé teszi a felhasználók számára, hogy az önkiszolgáló jelszó-visszaállítás (SSPR) és a Multi-Factor Authentication (MFA) számára egyetlen felhasználói felületen kezeljék biztonsági adataikat (például telefonszám, e-mail cím, mobil alkalmazás stb.). A felhasználóknak már nem kell ugyanazon biztonsági adatokat regisztrálniuk a SSPR és az MFA-hoz két különböző felhasználói élményben. Ez az új felhasználói élmény a SSPR vagy MFA-t használó felhasználókra is érvényes.

Ha egy szervezet nem érvényesíti az MFA-t vagy a SSPR-regisztrációt, akkor a felhasználók a **saját alkalmazások** portálon regisztrálhatják biztonsági adataikat. A felhasználók az MFA-hoz vagy a SSPR-hez engedélyezett bármely metódust regisztrálhatnak.

Ez egy nyilvános előzetes verzió. A rendszergazdák bekapcsolhatják az új felhasználói élményt (ha szükséges) egy adott felhasználó vagy egy bérlő összes felhasználója számára.

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>A jelszó alaphelyzetbe állításakor használja a Microsoft Authenticator alkalmazást az identitás ellenőrzéséhez

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** SSPR **termék funkció:** felhasználói hitelesítés

Ez a funkció lehetővé teszi, hogy a nem rendszergazdák ellenőrizzék az identitást, miközben visszaállítanak egy jelszót a Microsoft Authenticator (vagy bármely más hitelesítő alkalmazástól származó) értesítés vagy kód használatával. Miután a rendszergazdák bekapcsolják ezt az önkiszolgáló jelszó-visszaállítási módszert, a aka.ms/mfasetup-on vagy a aka.ms/setupsecurityinfo-en keresztül regisztrált Mobile App ellenőrzési módszerként használhatják a jelszavukat a jelszavuk alaphelyzetbe állításakor.

A Mobile App Notification szolgáltatás csak olyan szabályzat részeként kapcsolható be, amelynek két módszere van a jelszó alaphelyzetbe állításához.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Változási Megjegyzés: biztonsági javítás az alkalmazások delegált engedélyezési folyamatához az Azure AD-tevékenység naplófájljainak API-ját használó alkalmazásokhoz

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: jelentéskészítési **termék képesség:** & jelentéskészítés figyelése

Az erősebb biztonsági kényszerítés miatt módosítani kellett a delegált engedélyezési folyamatot használó alkalmazások engedélyeit az [Azure ad-tevékenység naplófájljainak API-jai](https://aka.ms/aadreportsapi)eléréséhez. Ez a módosítás 2018. **június 26-án**lép működésbe.

Ha bármelyik alkalmazás az Azure AD Activity log API-kat használja, kövesse az alábbi lépéseket annak biztosításához, hogy az alkalmazás ne törje meg a módosítás után.

**Az alkalmazás engedélyeinek frissítése**

1. Jelentkezzen be a Azure Portalba, válassza a **Azure Active Directory**lehetőséget, majd válassza az **alkalmazás-regisztrációk**lehetőséget.
2. Válassza ki az Azure AD Activity logs API-t használó alkalmazást, válassza a **Beállítások**lehetőséget, válassza a **szükséges engedélyek**lehetőséget, majd válassza ki a **Windows Azure Active Directory** API-t.
3. A **hozzáférés engedélyezése** panel **meghatalmazott engedélyek** területén jelölje be a **Címtáradatok olvasása** melletti jelölőnégyzetet, majd kattintson a **Mentés**gombra.
4. Válassza az **engedélyek megadása**lehetőséget, majd válassza az **Igen**lehetőséget.

    >[!Note]
    >Az alkalmazásra vonatkozó engedélyek megadásához globális rendszergazdának kell lennie.

További információkért tekintse meg az előfeltételek az Azure AD Reporting API-hoz való hozzáférésének előfeltételei az [engedélyek megadása](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) területen.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>A TLS beállításainak konfigurálása az Azure AD-szolgáltatásokhoz való kapcsolódás PCI DSS megfelelőség érdekében

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** N/A **termék képesség:** platform

A Transport Layer Security (TLS) egy olyan protokoll, amely adatvédelmet és adatintegritást biztosít két kommunikáló alkalmazás között, és a legszélesebb körben használt biztonsági protokoll, amelyet jelenleg használtak.

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) megállapította, hogy a TLS és SSL (SSL) korai verzióit le kell tiltani az új és biztonságosabb alkalmazás-protokollok engedélyezése mellett, a megfelelőség pedig **2018. június 30-ig**érvényes. Ez a módosítás azt jelenti, hogy ha az Azure AD-szolgáltatásokhoz csatlakozik, és PCI DSS megfelelőséget igényel, le kell tiltania a TLS 1,0-et. A TLS több verziója érhető el, de a TLS 1,2 a legújabb elérhető verzió Azure Active Directory szolgáltatásokhoz. Javasoljuk, hogy az ügyfél/kiszolgáló, valamint a böngésző/kiszolgáló kombinációk esetében közvetlenül a TLS 1,2-et helyezze el.

Előfordulhat, hogy az elavult böngészők nem támogatják az újabb TLS-verziókat, például a TLS 1,2-t. Ha szeretné megtekinteni, hogy a böngésző mely verzióit támogatja a TLS, lépjen a [QUALYS SSL Labs](https://www.ssllabs.com/) -webhelyre, és kattintson a **böngésző tesztelése**elemre. Javasoljuk, hogy frissítsen a webböngésző legújabb verziójára, és lehetőleg csak TLS 1,2-et engedélyezzen.

**A TLS 1,2 engedélyezése böngésző szerint**

- **A Microsoft Edge és az Internet Explorer (mindkettő az Internet Explorerben van beállítva)**

    1. Nyissa meg az Internet Explorert, válassza az **eszközök**  >  **Internetbeállítások**  >  **speciális**lehetőséget.
    2. A **Biztonság** területen válassza a **TLS 1,2 használata**lehetőséget, majd kattintson **az OK gombra**.
    3. Zárjunk be minden böngészőablakot, és indítsa újra az Internet Explorert.

- **Google Chrome**

    1. Nyissa meg a Google Chrome-t, írja be a *Chrome://Settings/* a címsorba, majd nyomja le az **ENTER**billentyűt.
    2. Bontsa ki a **speciális** beállításokat, lépjen a **Rendszerterületen, és válassza a** **Proxybeállítások megnyitása**lehetőséget.
    3. Az **Internet tulajdonságok** mezőben válassza a **speciális** lapot, lépjen a **biztonság** területen válassza a **TLS 1,2 használata**lehetőséget, majd kattintson az **OK gombra**.
    4. Zárjunk be minden böngészőablakot, és indítsa újra a Google Chrome-ot.

- **Mozilla Firefox**

    1. Nyissa meg a Firefox programot, írja be a *about: config* fájlt a címsorba, majd nyomja le az **ENTER**billentyűt.
    2. Keresse meg a kifejezést, a *TLS*-t, majd válassza ki a **Security. TLS. Version. max** bejegyzést.
    3. A **3** érték megadásával kényszerítse a böngészőt, hogy a TLS 1,2-es verziót használja, majd válassza az **OK gombot**.

        >[!NOTE]
        >A Firefox 60,0-es verziója támogatja a TLS 1,3-et, így a Security. TLS. Version. max értéket is beállíthatja **4**értékre.

    4. Zárjunk be minden böngészőablakot, és indítsa újra a Mozilla Firefox rendszert.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – június 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2018 júniusában az alábbi 15 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), a [Music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), az [SAML 1,1 token engedélyezve LOB-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), a felszínek [, az](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial)automatikus [feladat](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), a [végpontok biztonsági mentése](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), a [SkyHigh-hálózatok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), a Smartway2, a [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), a [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), a [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [a helyszíni SharePoint, a](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial) [CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), a [Vidyard,](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial)a [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Az Azure AD jelszavas védelme nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity Protection **termék funkció:** felhasználói hitelesítés

Az Azure AD jelszavas védelemmel segít kiszűrni a környezetből származó könnyen kitalált jelszavakat. Ezeknek a jelszavaknak a kiküszöbölése segít csökkenteni a támadás veszélyét a jelszó típusú spray-típusokkal szemben.

Az Azure AD jelszavas védelme különösen a következőket segíti:

- A szervezet fiókjait az Azure AD-ben és a Windows Server Active Directoryban (AD) is védetté teheti.
- Leállítja a felhasználók számára a jelszavak használatát a leggyakrabban használt jelszavak 500-nál nagyobb listáján, és több mint 1 000 000 karakteres helyettesítő változatban.
- Az Azure AD jelszavas védelmet az Azure ad-portálon egyetlen helyről felügyelheti, mind az Azure AD-hez, mind a helyszíni Windows Server AD-hoz.

Az Azure AD jelszavas védelemmel kapcsolatos további információkért lásd: [a helytelen jelszavak kizárása a szervezetben](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Új "minden vendég" – a használati feltételek létrehozása során létrehozott feltételes hozzáférési szabályzat sablonja

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** irányítás

A használati feltételek létrehozása során a rendszer létrehoz egy új feltételes hozzáférési szabályzatot a "minden vendég" és az "összes alkalmazás" számára is. Ez az új házirend-sablon az újonnan létrehozott felhasználási feltételeket alkalmazza, és egyszerűbbé teszi a vendégek számára a létrehozás és a kényszerítés folyamatát.

További információ: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>A használati feltételek létrehozása során létrehozott új "egyéni" feltételes hozzáférési szabályzat sablonja

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** irányítás

A használati feltételek létrehozásakor létrejön egy új "egyéni" feltételes hozzáférési házirend-sablon is. Ez az új házirend-sablon lehetővé teszi a felhasználási feltételek létrehozását, majd a feltételes hozzáférési szabályzat létrehozási paneljén való azonnali megnyitását anélkül, hogy manuálisan kellene navigálnia a portálon.

További információ: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Új és átfogó útmutató az Azure-Multi-Factor Authentication üzembe helyezéséhez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** egyéb **termék funkció:** Identitáskezelés biztonsági & védelme

Új lépésenkénti útmutatót kaptunk az Azure Multi-Factor Authentication (MFA) üzembe helyezéséhez a szervezetben.

Az MFA üzembe helyezési útmutatójának megtekintéséhez nyissa meg a [személyazonosság telepítési útmutatók](https://aka.ms/DeploymentPlans) tárházát a githubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [központi telepítési terv visszajelzési űrlapját](https://aka.ms/deploymentplanfeedback). Ha bármilyen kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk a következő címen: [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD által delegált alkalmazások felügyeleti szerepkörei nyilvános előzetes verzióban érhetők el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** Access Control

A rendszergazdák mostantól a globális rendszergazdai szerepkör kiosztása nélkül is delegálják az alkalmazás-felügyeleti feladatokat. Az új szerepkörök és képességek a következők:

- **Új standard szintű Azure AD-rendszergazdai szerepkörök:**

    - **Alkalmazás-rendszergazda.** Lehetővé teszi az alkalmazások minden aspektusának felügyeletét, beleértve a regisztrációt, az egyszeri bejelentkezés beállításait, az alkalmazások hozzárendelését és a licencelést, az alkalmazás-proxy beállításait és a jóváhagyást (kivéve az Azure AD-erőforrásokat).

    - **Cloud Application Administrator.** Minden alkalmazás-rendszergazdai képességet biztosít, kivéve az App proxyt, mivel nem biztosít helyszíni hozzáférést.

    - **Alkalmazás fejlesztője.** Lehetővé teszi az alkalmazások regisztrációjának létrehozását, még akkor is, ha a **felhasználók regisztrációjának engedélyezése** lehetőség ki van kapcsolva.

- **Tulajdonos (az alkalmazáson belüli regisztráció és a vállalati alkalmazások beállítása, hasonlóan a csoport tulajdonosi folyamatához:**

    - **Az alkalmazás regisztrációjának tulajdonosa.** Lehetővé teszi a tulajdonosi alkalmazások regisztrálásának minden aspektusának kezelését, beleértve az alkalmazás jegyzékfájlját és további tulajdonosok hozzáadását.

    - **Vállalati alkalmazás tulajdonosa.** Lehetővé teszi a vállalati alkalmazások számos aspektusának kezelését, beleértve az egyszeri bejelentkezési beállításokat, az alkalmazás-hozzárendeléseket és a belefoglalást (kivéve az Azure AD-erőforrásokat).

A nyilvános előzetes verzióval kapcsolatos további információkért tekintse [meg az Azure ad delegált alkalmazás-felügyeleti szerepkörök nyilvános előzetesét.](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) blog. További információ a szerepkörökről és az engedélyekről: [rendszergazdai szerepkörök Kiosztása Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>2018. május

### <a name="expressroute-support-changes"></a>ExpressRoute-támogatás módosításai

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: hitelesítések (bejelentkezések) a **termék képességei:** platform

A szolgáltatásként kínált szoftverek, például a Azure Active Directory (Azure AD) úgy vannak kialakítva, hogy a lehető legjobban működjenek közvetlenül az interneten keresztül anélkül, hogy ExpressRoute vagy más magánhálózati VPN-alagutakra lenne szükség. Ezért az Azure AD-szolgáltatások ExpressRoute az Azure-beli nyilvános és az Azure-közösségeket használó Microsoft-partnerekkel való támogatását követően leállítjuk. **2018** A változás által érintett szolgáltatások esetében előfordulhat, hogy az Azure AD-forgalom fokozatosan változik a ExpressRoute és az internet között.

A támogatás módosításakor azt is tudjuk, hogy még mindig vannak olyan helyzetek, amikor szükség lehet egy dedikált áramkör használatára a hitelesítési forgalomhoz. Emiatt az Azure AD továbbra is támogatja a bérlői IP-címtartomány korlátozásait a Microsoft ExpressRoute és szolgáltatásainak használatával a "További Office 365 Online Services" Közösséggel. Ha a szolgáltatások érintettek, de ExpressRoute van szüksége, a következőket kell tennie:

- **Ha Azure-beli nyilvános társ-összevonást használ.** Lépjen a Microsoft-partneri kapcsolatra, és regisztráljon a **másik Office 365 Online Services (12076:5100)** Közösségre. További információ arról, hogyan lehet áthelyezni az Azure-ban a nyilvános Microsoft-partnerektől a [Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) -partnerek felé.

- **Ha Microsoft-társat használ.** Regisztráljon a **másik Office 365 online Service (12076:5100)** Közösségre. Az útválasztási követelményekkel kapcsolatos további információkért tekintse meg a ExpressRoute-útválasztási követelmények cikk a [BGP-Közösségek támogatása című szakaszát](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) .

Ha továbbra is dedikált áramköröket kell használnia, a Microsoft-fiókkal foglalkozó csapatának kell megadnia, hogy hogyan kérhet engedélyt a **másik Office 365 online szolgáltatás (12076:5100)** Közösség használatára. Az MS Office által felügyelt felülvizsgálati tábla ellenőrzi, hogy szüksége van-e az adott áramkörökre, és hogy tisztában legyenek a technikai következményeivel. Az Office 365 útválasztási szűrőinek létrehozására tett jogosulatlan előfizetések hibaüzenetet kapnak.

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Felügyeleti forgatókönyvek Microsoft Graph API-k használata a felhasználási feltételekhez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék funkció:** fejlesztői élmény

Az Azure AD használati feltételeinek felügyeleti működéséhez Microsoft Graph API-kat adtunk hozzá. Létrehozhatja, frissítheti és törölheti a használati feltételeket.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD több-bérlős végpont hozzáadása identitás-szolgáltatóként Azure AD B2C

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Az egyéni szabályzatok használatával mostantól az Azure AD Common Endpoint Azure AD B2C identitás-szolgáltatóként is hozzáadhatja. Ez lehetővé teszi, hogy egyetlen belépési ponttal rendelkezzen az összes olyan Azure AD-felhasználó számára, akik bejelentkeznek az alkalmazásokba. További információ [: Azure Active Directory B2C: lehetővé teszi a felhasználók számára, hogy egyéni szabályzatok használatával bejelentkezzenek egy több-bérlős Azure ad-identitásba](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címek használatával bárhonnan elérheti alkalmazásait a saját alkalmazások bejelentkezési bővítménnyel és az Azure AD Application Proxy

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** saját alkalmazások **termék képesség:** SSO

A felhasználók mostantól belső URL-címeken keresztül érhetik el az alkalmazásokat, még akkor is, ha a vállalati hálózaton kívül esnek a saját alkalmazások biztonságos bejelentkezési bővítménnyel az Azure AD-hoz. Ez minden olyan alkalmazással működni fog, amelyet az Azure AD Application Proxy használatával tett közzé, minden olyan böngészőben, amelyen telepítve van a hozzáférési panel böngésző bővítménye. Az URL-átirányítás funkció automatikusan engedélyezve lesz, amint a felhasználó bejelentkezik a bővítménybe. A bővítmény letölthető a [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), a [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)és a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)számára.

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – az Európa Európai ügyfeleinek

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** egyéb **termék képesség:** GoLocal

Az Európai ügyfelek az adatoknak Európában maradnak, és nem replikálódnak az Európai adatközpontokon kívül az adatvédelem és az európai jogszabályok megtartása érdekében. Ez a [cikk](https://go.microsoft.com/fwlink/?linkid=872328) részletesen ismerteti, hogy milyen információk lesznek tárolva az Európai adatközpontokban, valamint az adatközpontokon kívül tárolt információkkal kapcsolatos részleteket is tartalmaz.

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználó üzembe helyezése SaaS-alkalmazás-integrációk – május 2018

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app kiépítési **termék képesség:** harmadik féltől származó integráció

Az Azure AD lehetővé teszi, hogy automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását SaaS-alkalmazásokban, például a Dropbox, a Salesforce, a ServiceNow és más rendszerekben. Május 2018-én a következő alkalmazásokhoz is hozzáadunk felhasználói üzembe helyezési támogatást az Azure AD App Galleryben:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók üzembe helyezését támogató alkalmazások listáját itt tekintheti meg: [https://aka.ms/appstutorial](https://aka.ms/appstutorial) .

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok és az alkalmazás-hozzáférés Azure AD hozzáférési felülvizsgálatai mostantól ismétlődő értékeléseket is biztosítanak

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hozzáférési felülvizsgálatok **termék képesség:** irányítás

A csoportok és alkalmazások hozzáférési felülvizsgálata mostantól általánosan elérhető a prémium szintű Azure AD P2 részeként.  A rendszergazdák a csoporttagság és az alkalmazás-hozzárendelések hozzáférési felülvizsgálatait rendszeres időközönként, például havonta vagy negyedévente is megismétlődnek.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure AD-tevékenységek naplói (bejelentkezések és naplózás) mostantól az MS Graphon keresztül érhetők el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Az Azure AD-tevékenységek naplói, amelyek a bejelentkezéseket és a naplókat is tartalmazzák, mostantól elérhetők a Microsoft Graph API-n keresztül. A naplók eléréséhez két végpontot tettünk elérhetővé a Microsoft Graph API-n keresztül. Az első lépésekhez tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az Azure ad Reporting API-k programozott eléréséhez.

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>A B2B beváltási élményének fejlesztése és a szervezeti feladatok elhagyása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2B- **termék funkció:** B2B/B2C

**Egyszeri visszaváltás:** Miután megoszt egy erőforrást egy vendég felhasználóval a B2B API használatával – nem kell elküldenie egy különleges meghívó e-mailt. A legtöbb esetben a vendég felhasználói hozzáférhetnek az erőforráshoz, és a beváltási élmény időben lesznek elvégezve. Nincs több hatása a kihagyott e-mailek miatt. Nem kell többé megkérdezni a vendég felhasználóit "a visszaváltási hivatkozásra kattintva a rendszer küldte?". Ez azt jelenti, hogy ha a "a" meghívót használja, akkor a Felhőbeli mellékletek ugyanazzal a kanonikus URL-címmel rendelkezhetnek az összes felhasználóhoz – belső és külső – a beváltási állapotban.

**Modern beváltási élmény:** Nincs több osztott képernyős beváltási Kezdőlap. A felhasználók a meghívásos szervezet Adatvédelmi nyilatkozatával modern beleegyező felhasználói élményt kapnak, ugyanúgy, mint a harmadik féltől származó alkalmazások esetében.

**A vendég felhasználói elhagyhatják a szervezeti** szervezetet: Ha a felhasználó kapcsolata egy szervezettel már véget ért, a szervezete elhagyhatja őket. Nem kell többé felhívni a meghívót, hogy a szervezet rendszergazdája "el kellene távolítani", és ne legyen több támogatási jegy.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások – 2018. május

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

A 2018-es verzióban az alábbi 18 új alkalmazást bővítettük az alkalmazás-katalógusban található összevonási támogatással:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty szabályozása [, Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe rabul Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel [, arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Új lépésenkénti üzembe helyezési útmutatók a Azure Active Directory

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** egyéb **termék funkció:** könyvtár

Új, lépésenkénti útmutató a Azure Active Directory (Azure AD) üzembe helyezéséhez, beleértve az önkiszolgáló jelszó-visszaállítás (SSPR), az egyszeri bejelentkezés (SSO), a feltételes hozzáférés (CA), az App proxy, a felhasználók kiépítése, a Active Directory összevonási szolgáltatások (AD FS) (ADFS) és a jelszó-kivonat szinkronizálása (PHS) használatát.

Az üzembe helyezési útmutatók megtekintéséhez nyissa meg az [Identity Deployment Guides](https://aka.ms/DeploymentPlans) -tárházat a githubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [központi telepítési terv visszajelzési űrlapját](https://aka.ms/deploymentplanfeedback). Ha bármilyen kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk a következő címen: [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése – további alkalmazások betöltése

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

Nem sikerül megkeresni az alkalmazásokat/egyszerű szolgáltatásokat? A vállalati alkalmazások minden alkalmazás listájában további alkalmazások tölthetők be. Alapértelmezés szerint 20 alkalmazást mutatunk be. Most kattintson a lehetőségre **, majd** a további alkalmazások megjelenítéséhez.

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>A AADConnect megjelenése a PingFederate, fontos biztonsági frissítésekkel, számos hibajavítással és új, nagyszerűen új hibaelhárítási eszközökkel való integráció nyilvános előzetes verzióját tartalmazza.

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosult: ad-kapcsolati **termék funkció:** identitás-életciklus kezelése

A AADConnect megjelenése a PingFederate, fontos biztonsági frissítésekkel, számos hibajavítással és új, nagyszerűen új hibaelhárítási eszközökkel való integráció nyilvános előzetes verzióját tartalmazza. A kibocsátási megjegyzések [itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)találhatók.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazás

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosult: a hozzáférési felülvizsgálatok **termék képesség:** irányítás

A csoportok és alkalmazások hozzáférési felülvizsgálatai mostantól általánosan elérhetők a prémium szintű Azure AD P2 részeként. A rendszergazda beállíthatja, hogy automatikusan alkalmazza a véleményező módosításait az adott csoportra vagy alkalmazásra, mivel a hozzáférési felülvizsgálat befejeződik. A rendszergazda azt is megadhatja, hogy mi történik a felhasználó folyamatos hozzáférésével, ha a felülvizsgálók nem válaszolnak, nem távolítják el a hozzáférést, megtartják a hozzáférést vagy a rendszerjavaslatokat.

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Az azonosító jogkivonatok már nem adhatók vissza az új alkalmazások lekérdezési response_mode használatával.

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

A 2018 április 25-én vagy azt követően létrehozott alkalmazások nem igényelhetnek **id_token** a **lekérdezési** response_mode használatával.  Ez az Azure AD-t a OIDC-specifikációkkal együtt ismerteti, és segít csökkenteni az alkalmazások támadási felületét.  Az 2018. április 25. előtt létrehozott alkalmazások nem tiltják le a **lekérdezési** response_mode response_type **id_token**.  A visszaadott hiba, amikor a HRE id_token kér, a **AADSTS70007: "Query" nem támogatott "response_mode" érték a jogkivonat kérésekor**.

A **töredék** és a **form_post** response_modes továbbra is működik – új alkalmazásobjektumok létrehozásakor (például az alkalmazás proxyjának használatakor) response_modes, mielőtt új alkalmazást hozna létre.

---

## <a name="april-2018"></a>2018. április

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C hozzáférési jogkivonat a GA

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

A hozzáférési jogkivonatok használatával mostantól hozzáférhet a Azure AD B2C által védett webes API-khoz. A szolgáltatás a nyilvános előzetes verzióról a GA-ra vált. Az Azure AD B2C-alkalmazások és webes API-k konfigurálásának felhasználói felülete továbbfejlesztett, és egyéb kisebb fejlesztéseket hajtottak végre.

További információt a [Azure ad B2C: hozzáférési tokenek igénylése](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)című témakörben talál.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Az egyszeri bejelentkezés konfigurációjának tesztelése SAML-alapú alkalmazásokhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

Az SAML-alapú SSO-alkalmazások konfigurálásakor tesztelheti az integrációt a konfiguráció lapon. Ha a bejelentkezés során hibát észlel, megadhatja a hibát a tesztelési élményben, és az Azure AD megoldást nyújt az adott probléma megoldásához szükséges megoldási lépések elvégzésére.

További információkért lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Az SAML-alapú egyszeri bejelentkezések hibakeresése Azure Active Directory-alkalmazásokban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati feltételei mostantól felhasználónkénti jelentéskészítéssel rendelkeznek

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** megfelelőség

A rendszergazdák mostantól kiválaszthatnak egy adott felhasználási helyet, és megtekinthetik az összes olyan felhasználót, aki beleegyezett az adott felhasználási feltételekbe, és hogy milyen időpontban és időpontban történt.

További információkért lásd az [Azure ad használati feltételeinek funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: kockázatos IP-cím AD FS extranetes zárolási védelemhez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** egyéb **termék funkció:** figyelés & jelentéskészítés

A csatlakoztatási állapot mostantól lehetővé teszi azon IP-címek észlelését, amelyek a sikertelen U/P-bejelentkezések küszöbértékét óránként vagy napi szinten túllépik. A szolgáltatás által biztosított képességek a következők:

- Átfogó jelentés, amely az IP-címet és a sikertelen bejelentkezések másodpercenkénti számát jeleníti meg, testreszabható küszöbértékkel.
- E-mail-alapú riasztások, amelyekben egy adott IP-cím túllépte a sikertelen U/P bejelentkezések küszöbértékét óránként/naponta.
- Letöltési lehetőség az adatgyűjtés részletes elemzéséhez

További információ: [kockázatos IP-jelentés](https://aka.ms/aadchriskyip).

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazás konfigurációja metaadat-fájllal vagy URL-címmel

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

A vállalati alkalmazások lapon a rendszergazdák feltölthetnek egy SAML-metaadat-fájlt az SAML-alapú bejelentkezés konfigurálásához a HRE Gallery és a nem katalógus alkalmazáshoz.

Emellett az Azure AD Application Federation metaadat-URL-címével is konfigurálhatja az SSO-t a célként megadott alkalmazással.

További információ: [az egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek a Azure Active Directory alkalmazás-galériában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Az Azure AD Használati feltételek már általánosan elérhető

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** megfelelőség


Az Azure AD használati feltételei a nyilvános előzetes verzióról általánosan elérhetővé váltanak.

További információkért lásd az [Azure ad használati feltételeinek funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Adott szervezetek B2B-felhasználók felé irányuló meghívásainak engedélyezése vagy letiltása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2B- **termék funkció:** B2B/B2C


Mostantól megadhatja, hogy mely partnerszervezetek számára szeretne megosztani és együttműködni az Azure AD B2B együttműködésben. Ehhez dönthet úgy, hogy létrehoz egy adott engedélyezési vagy megtagadási tartományt tartalmazó listát. Ha a tartományok blokkolva vannak ezekkel a képességekkel, az alkalmazottak többé nem küldhetnek meghívókat az adott tartományban lévő személyek számára.

Ez segít az erőforrásokhoz való hozzáférés szabályozásában, miközben zökkenőmentes felhasználói élményt tesz lehetővé a jóváhagyott felhasználók számára.

Ez a VÁLLALATKÖZI csoportmunka-szolgáltatás minden Azure Active Directory ügyfél számára elérhető, és prémium szintű Azure AD-funkciókkal, például a feltételes hozzáféréssel és az identitás-védelemmel együtt használható a külső üzleti felhasználók bejelentkezésének és hozzáférésének részletesebb szabályozása érdekében.

További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Április 2018-én a következő 13 új alkalmazást adtuk hozzá az alkalmazás-galériához való összevonási támogatással:

Az HCM, [a FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), a [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), a [dinamikus jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), a [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), a [szervezeti diagram most](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), a [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), a [AppNeta Teljesítményfigyelő](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), a [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , a [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), a [Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), a polc, a [Biztonság](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-felhasználók engedélyezése az Azure AD-ben a helyszíni alkalmazásokhoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2B- **termék funkció:** B2B/B2C

Azure Active Directory (Azure AD) B2B együttműködési képességeket használó szervezetként, amellyel meghívhatja a vendég felhasználókat a partnerszervezetektól az Azure AD-be, mostantól elérhetővé teheti a B2B-felhasználók számára a helyszíni alkalmazásokhoz való hozzáférést. Ezek a helyszíni alkalmazások SAML-alapú hitelesítést vagy integrált Windows-hitelesítést (IWA) használhatnak a Kerberos által korlátozott delegálással (KCD).

További információkért lásd: [B2B-felhasználók engedélyezése az Azure ad-ben a helyszíni alkalmazásokhoz való hozzáféréshez](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>SSO-integrációs útmutatók beszerzése az Azure Marketplace-ről

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosítva: egyéb **termék funkció:** harmadik féltől származó integráció

Ha az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) -en felsorolt alkalmazások TÁMOGATJÁK az SAML-alapú egyszeri bejelentkezést, a letöltés gombra kattintva elérhetővé **válik** az alkalmazáshoz társított integrációs oktatóanyag.

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Az Azure AD automatikus felhasználói üzembe helyezésének gyorsabb teljesítménye SaaS-alkalmazásokhoz

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** app kiépítési **termék képesség:** külső felek integrációja

Korábban a Azure Active Directory felhasználó kiépítési összekötőket használó SaaS-alkalmazásokhoz (például a Salesforce, a ServiceNow és a Box-hoz) lassú teljesítményt jelenthet, ha az Azure AD-bérlők több mint 100 000 együttes felhasználót és csoportot használnak, és felhasználói és csoportos hozzárendelésekkel határozzák meg, hogy mely felhasználók legyenek kiépítve.

2018. április 2-án jelentős teljesítménybeli fejlesztések történtek az Azure AD-létesítési szolgáltatásban, amely nagy mértékben csökkenti a Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizálások elvégzéséhez szükséges időt.

Ennek eredményeképpen sok olyan ügyfelet, akik kezdeti szinkronizálást végeztek a sok napot vagy soha nem teljesített alkalmazásokhoz, mostantól perc vagy óra múlva is befejeződik.

További információ: [Mi történik a kiépítés során?](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Önkiszolgáló jelszó-visszaállítás a Windows 10 zárolási képernyőjéről a hibrid Azure AD-hez csatlakoztatott gépekhez

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** önkiszolgáló jelszó-visszaállítási **termék funkció:** felhasználói hitelesítés

Frissítettük a Windows 10 SSPR funkcióját, hogy támogassa a hibrid Azure AD-hez csatlakoztatott gépek támogatását is. Ez a funkció a Windows 10 RS4 lehetővé teszi a felhasználók számára, hogy a Windows 10 rendszerű gép zárolási képernyőjéről visszaállítsák a jelszavukat. Az önkiszolgáló jelszó-visszaállításra engedélyezett és regisztrált felhasználók használhatják ezt a funkciót.

További információ: [Az Azure ad-jelszó alaphelyzetbe állítása a bejelentkezési képernyőn](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>2018. március

### <a name="certificate-expire-notification"></a>Tanúsítvány lejárati értesítése

**Írja be a következőt:** Rögzített **szolgáltatáskategória:** Enterprise apps **termék funkció:** SSO

Az Azure AD értesítést küld, ha egy katalógus vagy nem katalógusbeli alkalmazás tanúsítványa hamarosan lejár.

Néhány felhasználó nem kapott értesítéseket az SAML-alapú egyszeri bejelentkezéshez konfigurált nagyvállalati alkalmazásokhoz. A probléma megoldódott. Az Azure AD értesítést küld a 7, 30 és 60 nap alatt lejáró tanúsítványokról. Ezt az eseményt a naplókban láthatja.

További információkért lásd:

- [Az összevont egyszeri bejelentkezés tanúsítványainak kezelése Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Naplózott tevékenységekre vonatkozó jelentések az Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter-és GitHub-identitás-szolgáltatók Azure AD B2C

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** B2C – fogyasztói Identity Management **termék képesség:** B2B/B2C

Mostantól a Twitter vagy a GitHub is felvehető identitás-szolgáltatóként Azure AD B2Cban. A Twitter a nyilvános előzetes verzióról a GA-ra vált. A GitHub nyilvános előzetes verzióban érhető el.

További információ: [Mi az az Azure ad B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Böngésző-hozzáférés korlátozása Intune Managed Browser használatával az Azure AD alkalmazás-alapú feltételes hozzáférés iOS-hez és Androidhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** feltételes hozzáférésű **termék funkció:** Identity Security & Protection

**Már nyilvános előzetes verzióban érhető el!**

**Egyszeri bejelentkezés Intune Managed Browser:** Az alkalmazottak használhatnak egyszeri bejelentkezést a natív ügyfeleken (például a Microsoft Outlookban) és az Azure AD-hez csatlakoztatott összes alkalmazás Intune Managed Browser.

**Intune Managed Browser feltételes hozzáférési támogatás:** Mostantól megkövetelheti, hogy az alkalmazottak az Intune által felügyelt böngészőt használják az alkalmazás alapú feltételes hozzáférési szabályzatok használatával.

Erről a [blogbejegyzésben](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)olvashat bővebben.

További információkért lásd:

- [Alkalmazás-alapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed Browser-szabályzatok konfigurálása](https://aka.ms/managedbrowser)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App proxy-parancsmagok a PowerShell GA modulban

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** Access Control

Az alkalmazásproxy-parancsmagok támogatása most már a PowerShell GA modulban érhető el. Ehhez szükség van a PowerShell-modulok frissítésére – ha több mint egy évig tart, néhány parancsmag leállhat.

További információ: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Az Office 365 natív ügyfeleket nem interaktív protokoll használatával támogatja a zökkenőmentes egyszeri bejelentkezés.

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az Office 365 natív ügyfeleket (16.0.8730. xxxx vagy újabb verzió) használó felhasználó csendes bejelentkezési élményt nyújt a zökkenőmentes SSO használatával. Ezt a támogatást a nem interaktív protokoll (WS-Trust) Azure AD-hez való hozzáadásával biztosítjuk.

További információ: [how bejelentkezni egy natív ügyfélen zökkenőmentes egyszeri bejelentkezéssel?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Ha egy alkalmazás bejelentkezési kéréseket küld az Azure AD bérlői végpontjának, a felhasználók csendes bejelentkezési élményt nyújtanak, zökkenőmentes egyszeri bejelentkezéssel.

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

A felhasználók csendes bejelentkezési élményt nyújtanak, zökkenőmentes egyszeri bejelentkezéssel, ha egy alkalmazás (például `https://contoso.sharepoint.com` ) a bejelentkezési kéréseket az Azure ad bérlői végpontokra küldi, vagyis az `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` Azure ad közös végpontja ( `https://login.microsoftonline.com/common/<...>` ) helyett.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Csak egy Azure AD-URL-címet kell hozzáadnia két URL-cím helyett a felhasználók intranetes zónájának beállításaira a zökkenőmentes egyszeri bejelentkezéshez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

A zökkenőmentes egyszeri bejelentkezésnek a felhasználók számára való kiváltásához hozzá kell adnia egy Azure AD-URL-címet a felhasználók intranetes zónájának beállításaihoz a csoportházirend használatával Active Directory: `https://autologon.microsoftazuread-sso.com` . Korábban az ügyfeleknek két URL-címet kellett felvenniük.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Március 2018-én a következő 15 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), segéd: FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial), [Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Általánosan elérhető a PIM az Azure-erőforrásokhoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

Ha Azure AD Privileged Identity Managementt használ a címtárbeli szerepkörökhöz, mostantól használhatja a PIM időkorlátos hozzáférési és hozzárendelési képességeit az Azure-erőforrás-szerepkörökhöz, például előfizetésekhez, erőforráscsoportokhoz, Virtual Machineshoz és a Azure Resource Manager által támogatott egyéb erőforrásokhoz. Multi-Factor Authentication betartatása, amikor a szerepköröket időben aktiválja, és az aktiválásokat a jóváhagyott változtatási ablakokkal egyeztetve ütemezhet. Emellett ez a kiadás a nyilvános előzetes verzióban nem elérhető fejlesztéseket is tartalmaz, beleértve a frissített felhasználói felületet, a jóváhagyási munkafolyamatokat, valamint a szerepkörök kibővítésének lehetőségét a hamarosan lejár, és megújíthatja a lejárt szerepköröket.

További információ: [PIM for Azure Resources (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Opcionális jogcímek hozzáadása az alkalmazások jogkivonatához (nyilvános előzetes verzió)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az Azure AD-alkalmazás mostantól egyéni vagy választható jogcímeket is igényelhet a JWTs-vagy SAML-jogkivonatokban.  Ezek olyan felhasználói vagy bérlői jogcímek, amelyek nem szerepelnek a jogkivonatban alapértelmezés szerint, a méret vagy az alkalmazhatósági kényszer miatt.  Ez jelenleg nyilvános előzetes verzióban érhető el az Azure AD-alkalmazásokhoz a 1.0-s és a v 2.0-végpontokon.  A dokumentációban tájékozódhat arról, hogy milyen jogcímeket lehet felvenni, és hogyan szerkesztheti az alkalmazás-jegyzékfájlt a kéréshez.

További információ: [opcionális jogcímek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja a biztonságosabb OAuth-folyamatok PKCE

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az Azure AD-dokumentumok frissítve lettek a PKCE támogatásához, ami lehetővé teszi a biztonságosabb kommunikációt a OAuth 2,0 engedélyezési kód engedélyezése során.  A S256 és az egyszerű szöveges code_challenges is támogatott a 1.0-s és a v 2.0-végpontokon.

További információ: [engedélyezési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code).

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>A munkanapokon Get_Workers API-ban elérhető összes felhasználói attribútum értékének kiépítés támogatása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app kiépítési **termék képesség:** harmadik féltől származó integráció

A munkahelyről az Azure AD-be Active Directory való bejövő kiépítés nyilvános előzetes verziója mostantól lehetővé teszi a munkanapokon Get_Workers API-ban elérhető összes attribútumérték kinyerését és üzembe helyezését. Ez a szolgáltatás több száz további szabványos és egyéni attribútumot is támogat, amelyek a munkaidőn kívüli bejövő létesítési összekötő kezdeti verziójával együtt szállítottak.

További információkért lásd: [a munkanap felhasználói attribútumai listájának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A csoporttagság dinamikusról statikusra való módosítása és fordítva

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** csoport kezelése **termék képesség:** együttműködés

Megváltoztathatja, hogy a tagság hogyan legyen kezelve egy csoportban. Ez akkor hasznos, ha meg szeretné tartani a csoport nevét és AZONOSÍTÓját a rendszeren, így a csoportra vonatkozó meglévő hivatkozások továbbra is érvényesek; új csoport létrehozásához szükség lesz a hivatkozások frissítésére.
Frissítettük az Azure AD felügyeleti központot a funkció támogatásához. Az ügyfelek mostantól a meglévő csoportokat a dinamikus tagságból a hozzárendelt tagságra válthatják, és fordítva. A meglévő PowerShell-parancsmagok továbbra is elérhetők.

További információ: a [Azure Active Directory csoportok dinamikus tagsági szabályai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Továbbfejlesztett kijelentkezési viselkedés zökkenőmentes egyszeri bejelentkezéssel

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Korábban, még akkor is, ha a felhasználók explicit módon kijelentkezettek az Azure AD által védett alkalmazásból, a rendszer automatikusan visszaküldi őket a zökkenőmentes SSO használatával, ha a Corpnet a tartományhoz csatlakoztatott eszközökről próbálnak újra hozzáférni egy Azure AD-alkalmazáshoz. Ezzel a módosítással a kijelentkezés támogatott.  Ez lehetővé teszi, hogy a felhasználók azonos vagy eltérő Azure AD-fiókot használjanak a szolgáltatásba való bejelentkezéshez ahelyett, hogy automatikusan bejelentkeznek a zökkenőmentes SSO használatával.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)

---

### <a name="application-proxy-connector-version-154020-released"></a>Az alkalmazásproxy-összekötő verziója 1.5.402.0 kiadva

**Írja be a következőt:** A szolgáltatáshoz **tartozó szolgáltatás kategóriája** módosítva: app proxy- **termék képesség:** Identitáskezelés biztonsági & védelme

Ez az összekötő-verzió fokozatosan zajlik novemberben. Az új összekötő verziója a következő módosításokat tartalmazza:

- Az összekötő mostantól tartományi szintű cookie-kat állít be az altartomány szintjén. Ez zökkenőmentes SSO-élményt biztosít, és elkerüli a redundáns hitelesítési kéréseket.
- A darabolásos kódolási kérelmek támogatása
- Továbbfejlesztett összekötő állapotának figyelése
- Számos hibajavítás és stabilitási javítás

További információ: az [Azure ad Application proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).

---

## <a name="february-2018"></a>2018. február

### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett Navigálás felhasználók és csoportok kezeléséhez

**Írja be a következőt:** A módosítási **szolgáltatás kategóriájának** megtervezése: címtárszolgáltatás-kezelő **termék képesség:** könyvtár

A felhasználók és csoportok kezelésének navigációs felülete egyszerűbbé válik. Most már közvetlenül a címtár áttekintésében is megtekintheti az összes felhasználó listáját, és könnyebben hozzáférhet a törölt felhasználók listájához. A címtár áttekintését is megtekintheti közvetlenül az összes csoport listájára, és könnyebben hozzáférhet a csoport felügyeleti beállításaihoz. Továbbá a címtár – áttekintés oldalon megkeresheti a felhasználók, csoportok, vállalati alkalmazások vagy alkalmazások regisztrációját.

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A bejelentkezések és a naplózási jelentések rendelkezésre állása Microsoft Azure által üzemeltetett 21Vianet (Azure China 21Vianet)

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure stack **termék képesség:** monitoring & Reporting

Az Azure AD-tevékenységek naplózási jelentései mostantól a 21Vianet (Azure China 21Vianet)-példányok által üzemeltetett Microsoft Azureban érhetők el. A következő naplók tartoznak ide:

- **Bejelentkezési tevékenységek naplói**  – a bérlőhöz társított összes bejelentkezési naplót tartalmazza.

- **Önkiszolgáló jelszó-naplózási naplók** – tartalmazza az összes SSPR-naplót.

- **Címtár-felügyeleti naplók** – tartalmazza a címtár-felügyelettel kapcsolatos naplókat, például a felhasználók felügyeletét, az alkalmazások felügyeletét és egyebeket.

A naplók segítségével betekintést nyerhet a környezetével. A megadott adatokkal a következőket teheti:

- Határozza meg, hogy a felhasználók hogyan használják az alkalmazásokat és szolgáltatásokat.

- Hibák elhárítása a felhasználók munkavégzésének megakadályozása érdekében.

További információ a jelentések használatáról: [Azure Active Directory jelentéskészítés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Az Azure AD-tevékenységek jelentéseinek megtekintése a "jelentéskészítő olvasó" szerepkör (nem rendszergazdai szerepkör) használatával

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** jelentéskészítési **termék funkció:** figyelés & jelentéskészítés

Az ügyfelek visszajelzései részeként, hogy a nem rendszergazdai szerepkörök hozzáférjenek az Azure AD-tevékenység naplóihoz, engedélyezte, hogy a "jelentéskészítő" szerepkörbe tartozó felhasználók hozzáférjenek a bejelentkezésekhez és a naplózási tevékenységekhez a Azure Portalon belül, valamint a Microsoft Graph API-t használva.

További információ a jelentések használatáról: [Azure Active Directory jelentéskészítés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az Alkalmazottkód-jogcím felhasználói attribútumként és felhasználói azonosítóként érhető el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** vállalati alkalmazások **termék képesség:** SSO

Az **AlkalmazottKód** -t felhasználói azonosítóként és felhasználói attribútumként is konfigurálhatja a felhasználók és a vállalatközi vendégek számára az SAML-alapú bejelentkezési alkalmazásokban a vállalati alkalmazás felhasználói felületén.

További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Egyszerűsített alkalmazás-kezelés helyettesítő karakterek használatával az Azure AD Application Proxy

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék funkció:** felhasználói hitelesítés

Az alkalmazások központi telepítésének egyszerűbbé tétele és a felügyeleti terhelés csökkentése érdekében mostantól támogatjuk a helyettesítő karaktereket használó alkalmazások közzétételének lehetőségét. Helyettesítő karakteres alkalmazás közzétételéhez kövesse a normál alkalmazás-közzétételi folyamatot, de használjon helyettesítő karaktert a belső és külső URL-címekben.

További információ: [helyettesítő alkalmazások a Azure Active Directory alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Az alkalmazásproxy konfigurálását támogató új parancsmagok

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** platform

A AzureAD PowerShell előzetes verziójának legújabb kiadása olyan új parancsmagokat tartalmaz, amelyek lehetővé teszik, hogy az ügyfelek konfiguráljanak alkalmazásproxy-alkalmazásokat a PowerShell használatával.

Az új parancsmagok a következők:

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- Új – AzureADApplicationProxyApplication
- Új – AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>A csoportok konfigurálását támogató új parancsmagok

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** app proxy **termék képesség:** platform

A AzureAD PowerShell-modul legújabb kiadása parancsmagokat tartalmaz a csoportok Azure AD-ben való kezeléséhez. Ezek a parancsmagok korábban a AzureADPreview modulban voltak elérhetők, és most már hozzá lettek adva a AzureAD modulhoz

A jelenleg általánosan elérhetővé tett csoport-parancsmagok a következők:

- Get-AzureADMSGroup
- Új – AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- Új – AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Alaphelyzetbe állítás – AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>Azure AD Connect új kiadása érhető el

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** ad-szinkronizáló **termék képesség:** platform

Azure AD Connect az Azure AD és a helyszíni adatforrások közötti adatszinkronizálás előnyben részesített eszköze, beleértve a Windows Server Active Directoryt és az LDAP-t.

>[!Important]
>Ez a Build bevezeti a séma-és szinkronizálási szabályok változásait. A Azure AD Connect synchronization szolgáltatás a frissítés után teljes importálási és teljes szinkronizálási lépéseket indít el. További információ a viselkedés módosításáról: a [teljes szinkronizálás késleltetése a frissítés után](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ez a kiadás a következő frissítésekkel és módosításokkal rendelkezik:

**Kijavított problémák**

- A következő lapra váltáskor javítsa ki az időzítési időszakot a particionálási feladatok oldalának háttérben végzett feladatainál.

- Kijavítva egy olyan hibát, amely a configdb elemre egyéni művelet során hozzáférési szabálysértést okozott.

- Kijavított egy hibát az SQL-kapcsolat időtúllépése miatti helyreállításhoz.

- Kijavítva a hiba, ha a SAN-helyettesítő karakterekkel rendelkező tanúsítványok nem rendelkeznek előre-REQ ellenőrzéssel.

- Kijavítva egy olyan hibát, amely miiserver.exe összeomlást okoz a HRE-összekötő exportálásakor.

- Kijavítva a hiba, ha a Futtatás során helytelen jelszó történt a TARTOMÁNYVEZÉRLŐn, amikor a HRE-csatlakozás varázsló a konfiguráció módosítását okozta

**Új funkciók és Újdonságok**

- Alkalmazás telemetria – a rendszergazdák be-és kikapcsolhatják ezt az adatosztályt.

- Azure AD Health-adatkezelés – a rendszergazdáknak meg kell adniuk az állapotfigyelő portált az állapotuk szabályozása érdekében. A szolgáltatási házirend megváltozása után az ügynökök beolvassák és érvényesítik azt.

- Az eszköz visszaírási-konfigurációs műveletei és egy folyamatjelző sáv lett hozzáadva az oldal inicializálásához.

- Továbbfejlesztett általános diagnosztika HTML-jelentéssel és teljes adatgyűjtéssel egy ZIP-text/HTML-jelentésben.

- Az automatikus frissítés jobb megbízhatósága és további telemetria hozzáadása a kiszolgáló állapotának megállapításához.

- Az AD Connector-fiókra vonatkozó jogosultsági szintű fiókok számára elérhető engedélyek korlátozása. Új telepítések esetén a varázsló a MSOL-fiók létrehozása után korlátozza a Kiemelt fiókok által a MSOL fiókra vonatkozó engedélyeket. A módosítások az expressz telepítéseket és az egyéni telepítéseket érintik az automatikus létrehozási fiókkal.

- Módosította a telepítőt, hogy a AADConnect tiszta telepítésekor ne kelljen SA jogosultsága.

- Új segédprogram egy adott objektum szinkronizálási problémáinak elhárításához. A segédprogram jelenleg a következő dolgokat ellenőrzi:

    - UserPrincipalName eltérés a szinkronizált felhasználói objektum és az Azure AD-bérlő felhasználói fiókja között.

    - Ha a rendszer tartományi szűrés miatt szűri az objektumot a szinkronizálásból

    - Ha a rendszer a szervezeti egység (OU) szűrése miatt szűri az objektumot a szinkronizálásból

- Új segédprogram a helyszíni Active Directoryban tárolt aktuális jelszó-kivonat szinkronizálásához egy adott felhasználói fiókhoz. A segédprogram nem igényli a jelszó módosítását.

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Az Azure AD alkalmazás-alapú feltételes hozzáféréssel együtt használható Intune App Protection szabályzatokat támogató alkalmazások

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** feltételes hozzáférésű **termék funkció:** Identity Security & Protection

További alkalmazásokat is felvettünk, amelyek támogatják az alkalmazás-alapú feltételes hozzáférést. Mostantól hozzáférhet az Office 365-hez és más Azure AD-hez kapcsolódó felhőalapú alkalmazásokhoz is a jóváhagyott ügyfélalkalmazások használatával.

Február végére a következő alkalmazások lesznek hozzáadva:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft-számlázás

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>A Mobile Experience Használati feltételek frissítése

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** használati feltételek a **termék képességei:** megfelelőség

Ha megjelenik a használati feltételek, akkor a problémák megtekintése lehetőségre kattinthat **? Kattintson ide**. Erre a hivatkozásra kattintva a használati feltételek natív módon megnyitható az eszközön. A dokumentumban lévő betűmérettől vagy az eszköz képernyőjének méretétől függetlenül szükség szerint nagyíthatja és elolvashatja a dokumentumot.

---

## <a name="january-2018"></a>2018. január

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

Január 2018-án a következő új alkalmazások lettek hozzáadva az összevonási támogatással az alkalmazás-gyűjteményben:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust adatvédelem-kezelő szoftver](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk összevont könyvtár és [hűség NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---

### <a name="sign-in-with-additional-risk-detected"></a>Bejelentkezés további észlelt kockázattal

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Identity Protection- **termék funkció:** Identitáskezelés biztonsági & védelme

Az észlelt kockázatok észlelésével kapcsolatos betekintés az Azure AD-előfizetéshez van kötve. A prémium szintű Azure AD P2 kiadással a legrészletesebb információkat kapja meg az összes mögöttes észlelésről.

A prémium szintű Azure AD P1 kiadással a licenc által nem érintett észlelések nem jelennek meg, mivel a kockázatkezelési szolgáltatás a további kockázatok észlelésével jelentkezik.

További információ: [Azure Active Directory kockázati észlelések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazások elrejtése a végfelhasználó hozzáférési panelei között

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** saját alkalmazások **termék képesség:** SSO

Mostantól hatékonyabban kezelheti, hogy az Office 365-alkalmazások hogyan jelenjenek meg a felhasználó hozzáférési panelén egy új felhasználói beállítással. Ez a beállítás hasznos lehet a felhasználók hozzáférési panelén lévő alkalmazások számának csökkentésében, ha csak az Office-alkalmazásokat szeretné megjeleníteni az Office-portálon. A beállítás a **felhasználói beállítások** területen található, és a **felhasználók csak az Office 365-alkalmazásokat látják az Office 365-portálon**.

További információ: [alkalmazás elrejtése a felhasználói felületről Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Az alkalmazás URL-címéről való zökkenőmentes bejelentkezés a jelszó-SSO-hoz

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** saját alkalmazások **termék képesség:** SSO

A saját alkalmazások böngésző bővítménye mostantól egy kényelmes eszközön keresztül érhető el, amely a saját alkalmazások egyszeri bejelentkezés lehetőségét kínálja a böngészőben. A telepítés után a felhasználó egy Waffle ikont fog látni a böngészőben, amely gyors hozzáférést biztosít az alkalmazásokhoz. A felhasználók mostantól kihasználhatják a következőket:

- A jelszó-SSO-alapú alkalmazásokhoz való közvetlen bejelentkezés lehetősége az alkalmazás bejelentkezési oldaláról
- Bármely alkalmazás elindítása a gyors keresési funkció használatával
- A legutóbb használt alkalmazásokra mutató parancsikonok a bővítményből
- A bővítmény a Microsoft Edge, a Chrome és a Firefox számára érhető el.

További információt a [saját alkalmazások biztonságos bejelentkezési bővítménnyel](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)foglalkozó témakörben talál.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A klasszikus Azure portál Azure AD felügyeleti felülete megszűnt

**Írja be a következőt:** Elavult **szolgáltatási kategória:** Azure ad- **termék funkció:** könyvtár

2018. január 8-án a klasszikus Azure portálon elérhető Azure AD felügyeleti élmény megszűnt. Ez a klasszikus Azure-portál kivonulásával együtt zajlott. A későbbiekben az Azure ad [felügyeleti központot](https://aad.portal.azure.com) kell használnia az Azure ad portálon alapuló felügyeletéhez.

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor webportál ki lett vonva.

**Írja be a következőt:** Elavult **szolgáltatási kategória:** Azure ad- **termék funkció:** könyvtár

2018. január 8-án a PhoneFactor webportál kivonult. Ezt a portált használták az MFA-kiszolgáló felügyeletéhez, de ezek a függvények át lettek helyezve a Azure Portalba a következő címen: portal.azure.com.

Az MFA-konfiguráció a következő helyen található: **Azure Active Directory \> MFA-kiszolgáló**

---

### <a name="deprecate-azure-ad-reports"></a>Azure AD-jelentések elavultak

**Írja be a következőt:** Elavult **szolgáltatáskategória:** jelentéskészítési **termék képesség:** identitás életciklusának kezelése


Az új Azure Active Directory felügyeleti konzol és az új API-k általánosan elérhetővé válnak a tevékenység-és biztonsági jelentésekhez, a "/reports" végpont alatt a jelentés API-jai a 2017-as végén megszűntek.

**Mi érhető el?**

Az új felügyeleti konzolra való áttérés részeként 2 új API-t tettünk elérhetővé az Azure AD-tevékenységek naplófájljainak beolvasásához. Az új API-k szélesebb körű szűrési és rendezési funkciókat biztosítanak, továbbá gazdagabb naplózási és bejelentkezési tevékenységeket is biztosítanak. A biztonsági jelentésekben korábban elérhető adatok mostantól az Identity Protection kockázati észlelések API-n keresztül érhetők el Microsoft Graphban.

További információkért lásd:

- [Ismerkedés a Azure Active Directory Reporting API-val](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [A Azure Active Directory Identity Protection és a Microsoft Graph első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017. december

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panelen

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** megfelelőség

Ezután megnyithatja a hozzáférési panelt, és megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. Lépjen a [MyApps portálra](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a listából.

3. A **profilban**válassza a **használati feltételek áttekintése**elemet.

4. Most már áttekintheti az elfogadott használati feltételeket.

További információkért lásd az [Azure ad használati feltételeinek (előzetes verzió) funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-azure-ad-sign-in-experience"></a>Az Azure AD új bejelentkezési felülete

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad- **termék funkció:** felhasználói hitelesítés

Az Azure AD és a Microsoft-fiók Identity rendszer felülete úgy lett újratervezve, hogy egységes megjelenést és működést biztosítson. Emellett az Azure AD bejelentkezési oldala először a felhasználónevet gyűjti, amelyet a hitelesítő adatok követnek egy második képernyőn.

További információ: [Az Azure ad új bejelentkezési felülete már nyilvános előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)verzióban érhető el.

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérés: az Azure AD-bejelentkezéshez új "bejelentkezett"

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad- **termék funkció:** felhasználói hitelesítés

Az Azure AD bejelentkezési oldalának **bejelentkezett állapotban** jelölőnégyzete egy új, a sikeres hitelesítés után megjelenő üzenettel lett lecserélve.

Ha **igennel** válaszol erre a kérésre, a szolgáltatás állandó frissítési jogkivonatot biztosít. Ez a viselkedés ugyanaz, mint amikor bejelölte a a **bejelentkezve** szeretnék maradni jelölőnégyzetet a régi felületén. Összevont bérlők esetében ez a kérdés az összevont szolgáltatás sikeres hitelesítését követően jelenik meg.

További információ [: kevesebb bejelentkezési kérés: az Azure ad-ben az új "bejelentkezve tartom" élmény előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)verzióban érhető el.

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Konfiguráció hozzáadása a használati feltételek kibontásának megköveteléséhez az elfogadás előtt

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** megfelelőség

A rendszergazdák számára a feltételek elfogadását megelőzően a felhasználóknak a használati feltételek kibontására van szükségük.

Válassza ki **a** be vagy **ki** lehetőséget, hogy a felhasználók kiterjesszék a használati feltételeket. A **on** beállítás megadásához a felhasználóknak meg kell tekinteniük a használati feltételeket az elfogadásuk előtt.

További információkért lásd az [Azure ad használati feltételeinek (előzetes verzió) funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Jogosult szerepkör-hozzárendelések hatókörön belüli aktiválása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

A hatókörön belüli aktiválással aktiválhatja a jogosult Azure-erőforrás-szerepkör-hozzárendeléseket, és az eredeti hozzárendelés alapértelmezett értékeinél kevesebb autonómiát is használhat. Ilyen például, ha a bérlőben egy előfizetés tulajdonosaként van hozzárendelve. A hatókörön belüli aktiválással a tulajdonosi szerepkört akár öt, az előfizetésben található erőforráshoz (például erőforráscsoportok és virtuális gépek) is aktiválhatja. Az aktiválás hatóköre csökkentheti a kritikus Azure-erőforrások nemkívánatos módosításainak lehetőségét.

További információ: [Mi az Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Új összevont alkalmazások az Azure AD App Galleryben

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Enterprise apps **termék képesség:** harmadik féltől származó integráció

2017 decemberében a következő új alkalmazásokat adtuk hozzá az alkalmazás-galériában az összevonási támogatással:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI digitális kirakat](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe [, FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [kép működik](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure ad-integráció](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO a Bamboo by Solution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO for bitbucket by Solution GmbH](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure ad-integráció.

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD-címtárbeli szerepkörök jóváhagyási munkafolyamatai

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** Privileged Identity Management a **termék képességei:** Privileged Identity Management

Az Azure AD-címtár szerepköreinek jóváhagyási munkafolyamata általánosan elérhető.

A jóváhagyási munkafolyamatok, a Kiemelt szerepkörű rendszergazdák megkövetelhetik a jogosult szerepkör-tagokat a szerepkör-aktiválás kérelmezéséhez, mielőtt használhatják a Kiemelt szerepkört. Több felhasználó és csoport is delegálható jóváhagyási felelősséggel. A jogosult szerepkör tagjai értesítést kapnak, ha a jóváhagyás elkészült, és a szerepkörük aktív.

---

### <a name="pass-through-authentication-skype-for-business-support"></a>Átmenő hitelesítés: Skype vállalati verzió támogatása

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** hitelesítések (bejelentkezések) a **termék képességei:** felhasználói hitelesítés

Az átmenő hitelesítés mostantól támogatja a felhasználói bejelentkezéseket a Skype vállalati ügyfélalkalmazások számára, amelyek támogatják a modern hitelesítést, beleértve az online és a hibrid topológiákat is.

További információ: [a Skype for Business-topológiák, amelyek modern hitelesítéssel támogatottak](https://technet.microsoft.com/library/mt803262.aspx).

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC Azure AD Privileged Identity Management frissítései (előzetes verzió)

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** Privileged Identity Management a **termék képességei:** Privileged Identity Management

A Azure AD Privileged Identity Management (PIM) Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) való nyilvános előzetes frissítésével mostantól a következőket teheti:

* Csak elég felügyeletet használjon.
* Az erőforrás-szerepkörök aktiválásához jóváhagyás szükséges.
* Ütemezzen egy olyan szerepkör jövőbeli aktiválását, amely az Azure AD és az Azure szerepköreinek jóváhagyását igényli.

További információ: [Privileged Identity Management for Azure Resources (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="november-2017"></a>2017. november

### <a name="access-control-service-retirement"></a>Access Control szolgáltatás nyugdíjazása

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: Access Control Service **termék képesség:** Access Control szolgáltatás

A Azure Active Directory Access Control (más néven Access Control szolgáltatás) a 2018-es későn megszűnik. A következő hetekben további információkat talál a részletes ütemtervről és a magas szintű áttelepítési útmutatóról. A lap megjegyzéseit a Access Control szolgáltatással kapcsolatos bármilyen kérdéssel elhagyhatja, és a csapattagok is megválaszolják őket.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Böngésző hozzáférésének korlátozása a Intune Managed Browserhoz

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: a feltételes hozzáférési **termék funkciói:** az identitás biztonsága és védelme

A böngésző hozzáférését korlátozhatja az Office 365-hez és más Azure AD-hez kapcsolódó felhőalapú alkalmazásokhoz a Intune Managed Browser jóváhagyott alkalmazásként való használatával.

Mostantól a következő feltételt állíthatja be az alkalmazás alapú feltételes hozzáféréshez:

**Ügyfélalkalmazások:** Böngésző

**Mi a változás hatása?**

Jelenleg a hozzáférés le van tiltva, amikor ezt a feltételt használja. Ha az előzetes verzió elérhető, az összes hozzáféréshez a Managed Browser alkalmazást kell használnia.

Keresse meg ezt a képességet, és további információkat a közelgő blogokban és a kibocsátási megjegyzésekben.

További információ: [feltételes hozzáférés az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: a feltételes hozzáférési **termék funkciói:** az identitás biztonsága és védelme

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)listáján:

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek támogatása több nyelvhez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** megfelelőség

A rendszergazdák mostantól létrehozhatnak olyan új használati feltételeket, amelyek több PDF-dokumentumot tartalmaznak. Ezeket a PDF-dokumentumokat a megfelelő nyelven címkézheti. A felhasználók a megfelelő nyelven jelennek meg a PDF-ben a beállítások alapján. Ha nincs egyezés, az alapértelmezett nyelv jelenik meg.

---

### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó visszaírási-ügyfél állapota

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** önkiszolgáló jelszó-visszaállítási **termék funkció:** felhasználói hitelesítés

Most már áttekintheti a helyszíni jelszó visszaírási-ügyfelének állapotát. Ez a beállítás a [jelszó-visszaállítási](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) oldal helyszíni **integráció** szakaszában érhető el.

Ha problémák merülnek fel a helyszíni visszaírási-ügyféllel létesített kapcsolataival kapcsolatban, a következő hibaüzenet jelenik meg:

- Információ arról, hogy miért nem tud csatlakozni a helyszíni visszaírási-ügyfélhez.
- A probléma megoldásához segítséget nyújtó dokumentációra mutató hivatkozás.

További információ: helyszíni [integráció](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-alkalmazás-alapú feltételes hozzáférés

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad- **termék funkció:** az identitás biztonsága és védelme

Mostantól az [Azure ad-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)használatával korlátozhatja az Office 365 és más Azure ad-hez kapcsolódó felhőalapú alkalmazások elérését az Intune app Protection-szabályzatokat támogató [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview) számára. Az Intune app Protection-szabályzatok az ügyfélalkalmazások vállalati adatainak konfigurálására és védelmére szolgálnak.

Az [eszközökön alapuló](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications) feltételes hozzáférési szabályzatok [alkalmazásának](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) kombinálásával rugalmasan kezelheti a személyes és a vállalati eszközökre vonatkozó adatvédelmet.

Az alábbi feltételek és vezérlők mostantól használhatók az alkalmazás-alapú feltételes hozzáféréssel:

**Támogatott platform feltétele**

- iOS
- Android

**Ügyfélalkalmazások állapota**

- Mobile apps és asztali ügyfelek

**Hozzáférés-vezérlés**

- Jóváhagyott ügyfélalkalmazás megkövetelése

További információ: [Azure ad alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access).

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>Azure AD-eszközök kezelése a Azure Portal

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** az eszköz regisztrációja és kezelése **termék funkciója:** az identitás biztonsága és védelme

Az Azure AD-hez csatlakoztatott összes eszközt és az eszközhöz kapcsolódó tevékenységeket egy helyen találja meg. A Azure Portal minden eszköz-identitását és beállítását új adminisztrációs megoldással felügyelheti. Ebben a kiadásban a következőket teheti:

- Az Azure AD-ben feltételes hozzáféréshez elérhető összes eszköz megtekintése.
- Megtekintheti a hibrid Azure AD-hez csatlakoztatott eszközöket tartalmazó tulajdonságokat.
- Megkeresheti az Azure AD-hez csatlakoztatott eszközök BitLocker-kulcsait, kezelheti az eszközt az Intune-nal, és így tovább.
- Az Azure AD-eszközökhöz kapcsolódó beállítások kezelése.

További információ: [eszközök kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Támogatás macOS platformként az Azure AD feltételes hozzáféréshez

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** feltételes hozzáférésű **termék funkció:** az identitás biztonsága és védelme

Mostantól belefoglalhatja (vagy kizárhatja) a macOS-t az Azure AD feltételes hozzáférési szabályzatában található eszköz-platform feltételként. A macOS és a támogatott eszközök platformjának hozzáadásával a következőket teheti:

- **MacOS-eszközök regisztrálása és kezelése az Intune használatával.** Hasonló más platformokhoz, mint például az iOS és az Android, a macOS-hez készült vállalati portál alkalmazás egyesített regisztrációkat tesz elérhetővé. A macOS-hez készült új céges portál alkalmazással regisztrálhat egy eszközt az Intune-ban, és regisztrálhatja azt az Azure AD-ben.
- **Győződjön meg arról, hogy a macOS-eszközök megfelelnek a szervezetnek az Intune-ban definiált megfelelőségi szabályzatoknak.** A Azure Portal Intune-ban mostantól beállíthatja a macOS-eszközökre vonatkozó megfelelőségi szabályzatokat.
- **Az Azure AD-alkalmazásokhoz való hozzáférés korlátozása csak a megfelelő macOS-eszközökre.** A feltételes hozzáférési szabályzat létrehozásához a macOS-t külön eszköz platformként kell megválasztani. Most már készíthet macOS-specifikus feltételes hozzáférési szabályzatokat az Azure-beli megcélzott alkalmazás számára.

További információkért lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló bővítmény az Azure Multi-Factor Authentication

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:**  multi-Factor Authentication **termék funkció:** felhasználói hitelesítés

Az Azure Multi-Factor Authenticationhez készült hálózati házirend-kiszolgáló bővítmény felhőalapú Multi-Factor Authentication képességeket biztosít a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló bővítmény használatával telefonhívást, szöveges üzenetet vagy telefonos alkalmazás-ellenőrzést adhat meg a meglévő hitelesítési folyamathoz. Nem kell új kiszolgálókat telepítenie, konfigurálnia és karbantartani.

Ez a bővítmény olyan szervezetek számára lett létrehozva, amelyek az Azure-Multi-Factor Authentication-kiszolgáló telepítése nélkül szeretnék védelemmel ellátni a virtuális magánhálózati kapcsolatokat. A hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure Multi-Factor Authentication között, hogy az összevont vagy szinkronizált felhasználók hitelesítésének második tényezője legyen.

További információ: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).

---

### <a name="restore-or-permanently-remove-deleted-users"></a>Törölt felhasználók visszaállítása vagy végleges eltávolítása

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** felhasználói kezelés **termék képesség:** könyvtár

Az Azure AD felügyeleti központban mostantól a következőket végezheti el:

- Törölt felhasználó visszaállítása.
- Felhasználó végleges törlése.

**A kipróbáláshoz:**

1. Az Azure AD felügyeleti központban válassza a **kezelés** szakaszban az [összes felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) lehetőséget.

2. A **Megjelenítés** listából válassza a **nemrég törölt felhasználók**lehetőséget.

3. Válasszon ki egy vagy több nemrég törölt felhasználót, majd állítsa vissza, vagy véglegesen törölje őket.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosult: a feltételes hozzáférés **termékének funkciója:** az identitás biztonsága és védelme

A következő alkalmazások lettek hozzáadva a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)listájához:

- Microsoft Planner
- Azure Information Protection

További információkért lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzatban található vezérlők közötti "vagy" használata

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája** módosult: a feltételes hozzáférés **termékének funkciója:** az identitás biztonsága és védelme

Most már használhatja a "vagy a" (a kijelölt vezérlők egyikének megkövetelése) használatát a feltételes hozzáférés-vezérléshez. Ezzel a funkcióval házirendek hozhatók létre a hozzáférés-vezérlések között "vagy". Ezzel a szolgáltatással például létrehozhat egy olyan házirendet, amely megköveteli, hogy a felhasználó bejelentkezzen a Multi-Factor Authentication "vagy" utasítással kompatibilis eszközre.

További információ: [vezérlők az Azure ad feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).

---

### <a name="aggregation-of-real-time-risk-detections"></a>A valós idejű kockázati észlelések összesítése

**Írja be a következőt:** Módosult a szolgáltatás **kategóriája:** Identity Protection **termék funkció:** az identitás biztonsága és védelme

Azure AD Identity Protection az összes olyan valós idejű kockázati észlelést, amely egy adott napon belül ugyanabból az IP-címről származik, az egyes kockázati észlelési típusok esetében összesítve lesz. Ez a változás korlátozza a kockázati észlelések mennyiségét, ami a felhasználói biztonság módosítása nélkül jelenik meg.

A mögöttes valós idejű észlelés minden alkalommal működik, amikor a felhasználó bejelentkezik. Ha van olyan biztonsági házirendje, amely Multi-Factor Authentication vagy letiltja a hozzáférést, az egyes kockázatos bejelentkezések során továbbra is aktiválódik.

---

## <a name="october-2017"></a>2017. október

### <a name="deprecate-azure-ad-reports"></a>Azure AD-jelentések elavultak

**Írja be a következőt:** A változási **szolgáltatás kategóriájának** megtervezése: jelentéskészítési **termék képesség:** identitás-életciklus kezelése

A Azure Portal a következőket biztosítja:

- Egy új Azure AD felügyeleti konzol.
- Új API-k tevékenység-és biztonsági jelentésekhez.

Az új képességek miatt a/Reports-végpont alatti jelentés API-k 2017. december 10-én megszűntek.

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezési mező észlelése

**Írja be a következőt:** Rögzített **szolgáltatás kategóriája:** saját alkalmazások **termék képesség:** egyszeri bejelentkezés

Az Azure AD támogatja az automatikus bejelentkezési mezők észlelését olyan alkalmazások esetében, amelyek HTML-felhasználónevet és jelszót tartalmazó mezőt jelenítenek meg. Ezek a lépések az [alkalmazások bejelentkezési mezőinek automatikus rögzítését ismertetik](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Ezt a képességet a [Azure Portal](https://aad.portal.azure.com) **vállalati alkalmazások** lapján található *nem* katalógusbeli alkalmazás hozzáadásával érheti el. Ezen felül az új alkalmazás **egyszeri bejelentkezési** módját **jelszó alapú egyszeri bejelentkezésre**is beállíthatja, MEGadhat egy webes URL-címet, majd mentheti a lapot.

Szolgáltatási probléma miatt ez a funkció átmenetileg le lett tiltva. A probléma megoldódott, és az automatikus bejelentkezési mező észlelése ismét elérhető.

---

### <a name="new-multi-factor-authentication-features"></a>Új Multi-Factor Authentication funkciók

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** multi-Factor Authentication **termék funkció:** az identitás biztonsága és védelme

A többtényezős hitelesítés (MFA) elengedhetetlen része a szervezet védelmének. Ahhoz, hogy a hitelesítő adatok jobban alkalmazkodnak és a tapasztalatok zökkenőmentesek legyenek, a következő funkciók lettek hozzáadva:

- A multi-Factor Challenge eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentésbe, amely az MFA-eredmények programozott elérését is magában foglalja.
- Az MFA-konfiguráció mélyebben integrálva van az Azure AD konfigurációs felületére a Azure Portalban.

Ebben a nyilvános előzetes verzióban az MFA-felügyelet és a jelentéskészítés az Azure AD-konfiguráció alapvető felhasználói felületének szerves részét képezi. Most már kezelheti az MFA felügyeleti portál funkcióit az Azure AD-ben.

További információ: [az MFA-jelentéskészítés referenciája a Azure Portalban](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa).

---

### <a name="terms-of-use"></a>Használati feltételek

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** használati feltételek **termék képesség:** megfelelőség

Használhatja az Azure AD használati feltételeit, amelyek olyan információkat mutatnak be, mint például a felhasználókra vonatkozó jogi vagy megfelelőségi követelményekhez kapcsolódó jogcímek.

Az Azure AD használati feltételeit a következő esetekben használhatja:

- Általános használati feltételek a szervezet összes felhasználója számára
- Meghatározott használati feltételek egy felhasználó attribútumai (például orvosok, ápolók vagy belföldi és nemzetközi alkalmazottak, dinamikus csoportok által végzett) alapján
- Bizonyos használati feltételek a nagy hatású üzleti alkalmazások, például a Salesforce eléréséhez

További információ: az [Azure ad használati feltételei](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>A Privileged Identity Management fejlesztései

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Privileged Identity Management **termék képesség:** Privileged Identity Management

A Azure AD Privileged Identity Management segítségével a szervezeten belül felügyelheti, vezérelheti és figyelheti az Azure-erőforrásokhoz való hozzáférést (előzetes verzió):

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek)

Az Azure RBAC funkciót használó Azure Portal összes erőforrása kihasználhatja a Azure AD Privileged Identity Management által kínált biztonsági és életciklus-kezelési képességek előnyeit.

További információ: [Privileged Identity Management Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** a hozzáférési felülvizsgálatok **termék képesség:** megfelelőség

A szervezetek a hozzáférési felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelhetik a csoporttagságok és a vállalati alkalmazásokhoz való hozzáférést:

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. A felülvizsgálók hatékonyan dönthetnek arról, hogy a vendégek továbbra is hozzáférhetnek-e a hozzáférési felülvizsgálatok által biztosított megállapítások alapján.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információ: [Azure ad hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Harmadik féltől származó alkalmazások elrejtése az alkalmazásokból és az Office 365 app launcherből

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** saját alkalmazások **termék képesség:** egyszeri bejelentkezés

Mostantól hatékonyabban kezelheti a felhasználói portálon megjelenő alkalmazásokat egy új **Hide app** -tulajdonság használatával. Az alkalmazások elrejtésével olyan esetekben lehet segíteni, amikor az alkalmazás csempéi megjelennek a háttér-szolgáltatásokhoz, illetve az ismétlődő csempék és a felhasználói alkalmazások elindításához. A váltógomb a harmadik féltől származó alkalmazás **Tulajdonságok** részében található, és **látható a felhasználó számára?** Egy alkalmazást programozott módon is el lehet rejteni a PowerShell használatával.

További információ: [harmadik féltől származó alkalmazás elrejtése a felhasználó Azure ad-beli felhasználói felületéről](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).


**Mi érhető el?**

 Az új felügyeleti konzolra való áttérés részeként két új API érhető el az Azure AD-tevékenységek naplófájljainak beolvasásához. Az új API-k szélesebb körű szűrési és rendezési funkciókat biztosítanak, továbbá gazdagabb naplózási és bejelentkezési tevékenységeket is biztosítanak. A biztonsági jelentésekben korábban elérhető adatok mostantól az Identity Protection kockázati észlelési API-n keresztül érhetők el Microsoft Graphban.


## <a name="september-2017"></a>2017. szeptember

### <a name="hotfix-for-identity-manager"></a>Az Identity Manager gyorsjavítása

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriájának módosítása:** Identity Manager- **termék funkció:** Identity Lifecycle Management

A 4.4.1642.0-alapú gyorsjavítások összesítő csomagja (Build) 2017. szeptember 25-én érhető el az Identity Manager 2016 Service Pack 1 verzióhoz. Ez az összesítő csomag:

- Elhárítja a problémákat, és javítja a javítást.
- A egy összegző frissítés, amely az összes Identity Manager 2016 Service Pack 1 frissítést felváltja az Identity Manager 2016 4.4.1459.0 létrehozásához.
- Az Identity Manager 2016 Build 4.4.1302.0 szükséges.

További információ: a [gyorsjavítások kumulatív csomagja (Build 4.4.1642.0) elérhető a Identity Manager 2016 Service Pack 1 verzióhoz](https://support.microsoft.com/help/4021562).

---
