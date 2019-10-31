---
title: Archiválja a Azure Active Directory újdonságait? | Microsoft Docs
description: A jelen tartalomforrás Áttekintés szakaszában található Újdonságok kibocsátási megjegyzései 6 hónapos tevékenységet tartalmaznak. 6 hónap elteltével az elemek el lesznek távolítva a főcikkből, és ebbe az archív cikkbe kerül.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fcf1e5966be7708e7c6278839cbfbe5d65cdffa
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149128"
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

## <a name="april-2019"></a>2019. április

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Az Azure AD veszélyforrások felderítésének új észlelése mostantól a Azure AD Identity Protection részeként érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Az Azure AD Threat Intelligence észlelése mostantól a frissített Azure AD Identity Protection funkció részeként érhető el. Ez az új funkció lehetővé teszi, hogy szokatlan felhasználói tevékenységet jelezzen egy adott felhasználó vagy tevékenység számára, amely konzisztens a Microsoft belső és külső veszélyforrások elleni hírszerzési forrásai alapján ismert támadási mintákkal.

A Azure AD Identity Protection frissített verziójával kapcsolatos további információkért tekintse meg a [négy jelentős Azure ad Identity Protection fejlesztést a nyilvános előzetes verzióban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a mi az [Azure Active Directory Identity Protection (frissítve)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) cikk. Az Azure AD veszélyforrások észlelésével kapcsolatos további információkért tekintse meg a [Azure Active Directory Identity Protection kockázati észlelésekkel](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) foglalkozó cikket.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Az Azure AD-jogosultságok kezelése mostantól elérhető (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identitás-szabályozás  
A **termék képességei:** Identitás-szabályozás

A nyilvános előzetes verzióban elérhető Azure AD-jogosultsági felügyelet segít az ügyfeleknek a hozzáférési csomagok felügyeletének delegálásában, amely meghatározza, hogy az alkalmazottak és az üzleti partnerek hogyan igényelhetnek hozzáférést, jóvá kell hagynia, és hogy mennyi ideig férhetnek hozzá. A hozzáférési csomagok kezelhetik az Azure AD és az Office 365-csoportok tagságát, a vállalati alkalmazásokban lévő szerepkör-hozzárendeléseket, valamint a SharePoint Online-webhelyek szerepkör-hozzárendeléseit. További információ a jogosultságok kezeléséről az [Azure ad-jogosultságok kezelésének áttekintése című](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)témakörben olvasható. Ha többet szeretne megtudni a Azure AD Identity Governance-funkciók széles köréről, beleértve a Privileged Identity Management, a hozzáférési felülvizsgálatokat és a használati feltételeket, tekintse meg a [Mi az Azure ad Identity Governance?](../governance/identity-governance-overview.md)című témakört.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-Tevékenységnaplók mostantól Azure Monitorban érhetők el (általánosan elérhető)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Az Azure AD-tevékenység naplóival kapcsolatos vizualizációk visszajelzésének megkönnyítése érdekében a Log Analytics új elemzési funkciót vezetünk be. Ez a funkció segít az Azure AD-erőforrásokkal kapcsolatos elemzések megszerzésében interaktív sablonjaink használatával, az úgynevezett munkafüzeteknél. Ezek az előre elkészített munkafüzetek részletes adatokat biztosíthatnak az alkalmazásokhoz és a felhasználókhoz, és a következőket tartalmazzák:

- **Bejelentkezések.** Részletes információkat nyújt az alkalmazásokról és a felhasználókról, beleértve a bejelentkezési helyet, a használatban lévő operációs rendszert vagy böngésző-ügyfelet és-verziót, valamint a sikeres vagy sikertelen bejelentkezések számát.

- **Örökölt hitelesítés és feltételes hozzáférés.** A régi hitelesítést használó alkalmazások és felhasználók részletes adatait tartalmazza, beleértve a feltételes hozzáférési házirendek által aktivált, feltételes hozzáférési házirendeket használó alkalmazások és így tovább Multi-Factor Authentication használatát.

- **Sikertelen bejelentkezések elemzése.** Segít megállapítani, hogy a bejelentkezési hibák egy felhasználói művelet, házirend-problémák vagy az infrastruktúra miatt történnek-e.

- **Egyéni jelentések.** Létrehozhat új, vagy szerkesztheti a meglévő munkafüzeteket, amelyek segítségével testre szabhatja a szervezete által használt adatellenőrzési funkciót.

További információ: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – április 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Április 2019-én a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [átszivárog](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [padok](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085) [, PageDNA,](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio-kapcsolat ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel-kapcsolat](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (szerepköralapú SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [Surveymonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakoriságának beállítása és több szerepkör kiválasztása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás

Az Azure AD hozzáférési felülvizsgálatok új frissítései a következőket teszik lehetővé:

- A korábbi, heti, havi, negyedéves és évente meglévő lehetőségeken felül a hozzáférési felülvizsgálatok gyakoriságát **is módosíthatja**félévente.

- Egyetlen hozzáférési felülvizsgálat létrehozásakor több Azure AD-és Azure-erőforrás-szerepkört is kijelölhet. Ebben az esetben minden szerepkör ugyanazokkal a beállításokkal van beállítva, és minden felülvizsgáló értesítést kap egyszerre.

A hozzáférési felülvizsgálat létrehozásával kapcsolatos további információkért lásd: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)felülvizsgálatokban.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail-riasztási rendszer (ek) át lettek irányítva, és minden ügyfél számára új e-mail feladói adatokat küldenek

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
A **termék képességei:** Platform

Azure AD Connect az e-mail-riasztási rendszer (ek) átváltásának folyamata, amely egy új e-mail-feladót mutat be. Ennek megoldásához hozzá kell adnia `azure-noreply@microsoft.com` értéket a szervezet engedélyezési listájához, vagy nem fogja tudni fogadni a fontos riasztásokat az Office 365, az Azure vagy a Sync Services szolgáltatásból.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Az UPN-utótag módosításai mostantól sikeresek az összevont tartományok között Azure AD Connect

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
A **termék képességei:** Platform

Most már sikeresen módosíthatja egy felhasználó UPN-utótagját az egyik összevont tartományból egy másik összevont tartományba Azure AD Connectban. Ez a javítás azt jelenti, hogy a szinkronizálási ciklusban többé nem tapasztalja a FederatedDomainChangeError hibaüzenetet, vagy értesítést kap a következőről: "nem lehet frissíteni ezt az objektumot Azure Active Directory, mert az attribútum [ A FederatedUser. UserPrincipalName] érvénytelen. Frissítse az értéket a helyi címtárszolgáltatások szolgáltatásban.

További információ: [hibaelhárítási hibák a szinkronizálás során](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Fokozott biztonság az App Protection-alapú feltételes hozzáférési szabályzattal az Azure AD-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az App Protection-alapú feltételes hozzáférés mostantól az alkalmazás- **védelmi házirend megkövetelése** eszközzel érhető el. Ez az új szabályzat segít a szervezet biztonságának növelésében azáltal, hogy megakadályozza a következőket:

- A felhasználók Microsoft Intune licenc nélkül férhetnek hozzá az alkalmazásokhoz.

- A felhasználók nem tudnak Microsoft Intune app Protection-szabályzatot beolvasni.

- A felhasználók a konfigurált Microsoft Intune app Protection-házirend nélkül szereznek hozzáférést az alkalmazásokhoz.

További információkért lásd: [alkalmazás-védelmi szabályzat megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Új támogatás az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez a Microsoft Edge-ben (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Bővítettük a Microsoft Edge-hez készült Azure AD-támogatást, amely új támogatást nyújt az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez. Ha korábban már használta Microsoft Intune Managed Browser, a Microsoft Edge-t használhatja helyette.

Az eszközök és alkalmazások feltételes hozzáférés használatával történő beállításával és kezelésével kapcsolatos további információkért lásd: [felügyelt eszközök megkövetelése a felhőalapú alkalmazásokhoz való hozzáféréshez feltételes](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) hozzáféréssel, valamint [jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). További információ arról, hogyan kezelhető a hozzáférés a Microsoft Edge használatával Microsoft Intune házirendekkel: az [Internet-hozzáférés kezelése Microsoft Intune házirenddel védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>2019. március

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Az identitási élmény keretrendszere és az egyéni szabályzatok támogatása Azure Active Directory B2C már elérhető (GA)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Március 2019-án a következő 14 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással:

[ISEC7 Mobile Exchange-delegált](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [magyarázat-alapú naplózási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [sovány](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool teljesítményű ügyek](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Iris intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [Tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler-és Atlassian-létesítési összekötők az Azure AD-galériában – március 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció

A következő alkalmazásokhoz tartozó felhasználói fiókok létrehozásának, frissítésének és törlésének automatizálása:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler három](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://aka.ms/ProvisioningDocumentation)című témakört.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Törölt Office 365-csoportok visszaállítása és kezelése az Azure AD-portálon

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Most már megtekintheti és kezelheti a törölt Office 365-csoportokat az Azure AD-portálon. Ez a módosítás segít megtekinteni, hogy mely csoportok állíthatók vissza a visszaállításhoz, és hogy véglegesen törli azokat a csoportokat, amelyek nem szükségesek a szervezet számára.

További információ: [lejárt vagy törölt csoportok visszaállítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Az egyszeri bejelentkezés mostantól elérhető az Azure AD SAML-védelemmel ellátott helyszíni alkalmazásokhoz az Application proxyn keresztül (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Mostantól egyszeri bejelentkezéses (SSO) élményt biztosíthat a helyszíni, SAML-hitelesítésen alapuló alkalmazásokhoz, valamint az alkalmazások távoli elérését az Application proxyn keresztül. Az SAML SSO helyszíni alkalmazásokkal való beállításával kapcsolatos további információkért lásd: [SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)használatával.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A megbízhatóság és a felhasználói élmény fokozása érdekében a kérelemben szereplő ügyfélalkalmazások megszakadnak.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Az ügyfélalkalmazások nem tudják megfelelően kiadni több száz azonos bejelentkezési kérést rövid idő alatt. Ezek a kérések, akár sikeresek, akár nem, mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.

Ez a frissítés `invalid_grant` hibát küld: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` olyan ügyfélalkalmazások számára, amelyek rövid idő alatt többször is kiadják a duplikált kérelmeket, a normál működés hatókörén kívül. A problémát tapasztaló ügyfélalkalmazások interaktív üzenetet jelenítenek meg, amely megköveteli, hogy a felhasználó újra bejelentkezzen. További információ erről a változásról, valamint arról, hogy az alkalmazás Hogyan oldható meg, ha ez a hiba ütközik: Újdonságok [a hitelesítéshez?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Új naplózási naplók – felhasználói élmény most elérhető

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Létrehoztunk egy új Azure AD- **naplókat** tartalmazó oldalt, amely segít az olvashatóság és az információk megkeresésének javításában. Ha meg szeretné tekinteni **az új naplók** lapot, válassza a **naplók** lehetőséget az Azure ad **tevékenység** szakaszában.

![Új naplózási naplók oldal, a minta adataival](media/whats-new/audit-logs-page.png)

További információ az új **naplók** lapról: [naplózási tevékenység jelentései a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatás a nem megfelelően konfigurált feltételes hozzáférési szabályzatok véletlen rendszergazdai zárolásának megakadályozásához

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Annak megakadályozása érdekében, hogy a rendszergazdák véletlenül zárolják magukat a saját bérlőik nem megfelelően konfigurált feltételes hozzáférési szabályzatokkal, új figyelmeztetéseket hoztunk létre, és frissítettük az útmutatót a Azure Portal. Az új útmutatással kapcsolatos további információkért lásd: [Mi a szolgáltatás függőségei a Azure Active Directory feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Jobb végfelhasználói használati feltételek a mobileszközök esetében

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

Frissítettük a meglévő használati feltételeket, amelyek segítségével javítható a mobileszközök használati feltételeinek áttekintése és belefoglalása. Most nagyíthatja és kibővítheti, visszatérhet, letöltheti az adatokat, és kiválaszthatja a hiperhivatkozásokat. A frissített használati feltételekkel kapcsolatos további információkért lásd: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Az Azure AD-beli új tevékenység naplóinak letöltési felülete elérhető

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Most már nagy mennyiségű tevékenységet is letölthet közvetlenül a Azure Portalból. Ez a frissítés a következőket teszi lehetővé:

- Legfeljebb 250 000 sort tölthet le.

- Értesítést kap a letöltés befejeződése után.

- Szabja testre a fájlnevet.

- Határozza meg a kimeneti formátumot JSON-ként vagy CSV-ként.

További információ erről a szolgáltatásról: gyors útmutató [: naplózási jelentés letöltése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Változás megszakítása: az Exchange ActiveSync (EAS) által a feltételek kiértékelésének frissítései

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Access Control

Folyamatban van az Exchange ActiveSync (EAS) az alábbi feltételek kiértékelésének frissítése:

- Felhasználói hely, ország, régió vagy IP-cím alapján

- Bejelentkezési kockázat

- Eszköz platformja

Ha korábban már használta ezeket a feltételeket a feltételes hozzáférési házirendekben, vegye figyelembe, hogy a feltétel viselkedése változhat. Ha például korábban a felhasználói hely feltételét használta egy házirendben, akkor a rendszer a felhasználó helye alapján kihagyhatja a házirendet.

---

## <a name="february-2019"></a>2019. február

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Konfigurálható Azure AD SAML-jogkivonat titkosítása (nyilvános előzetes verzió) 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Mostantól a támogatott SAML-alkalmazások is konfigurálhatók titkosított SAML-tokenek fogadására. Az Azure AD az Azure AD-ben tárolt tanúsítványból beszerzett nyilvános kulccsal titkosítja a kibocsátott SAML-kijelentéseket, ha az alkalmazás konfigurálva van és használatban van.

Az SAML-jogkivonat titkosításának konfigurálásával kapcsolatos további információkért lásd: az [Azure ad SAML-jogkivonat titkosításának konfigurálása](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Hozzáférési felülvizsgálat létrehozása csoportok vagy alkalmazások számára az Azure AD hozzáférési felülvizsgálatok használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Cégirányítási

Mostantól több csoportot vagy alkalmazást is hozzáadhat egyetlen Azure AD-hozzáférési felülvizsgálatban csoporttagság vagy alkalmazás-hozzárendelés esetén. A különböző csoportokkal vagy alkalmazásokkal való hozzáférési felülvizsgálatok ugyanazokkal a beállításokkal vannak beállítva, és az összes belefoglalt felülvizsgálók egyszerre vannak bejelentve.

További információ az Azure AD hozzáférési felülvizsgálatok használatával történő hozzáférési felülvizsgálat létrehozásáról: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) felülvizsgálatokban

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – február 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Február 2019-án az alábbi 27 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT Finger](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [iDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [ég felé Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Engedély kattintás, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [szeizmikus](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [A Dream megosztása](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge Bárhol az LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [ou Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [periszkóp](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)-és [NetOp-portál](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [Smartvid.IO](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp hatékonyságnövelő platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="enhanced-combined-mfasspr-registration"></a>Bővített összetett MFA/SSPR-regisztráció

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

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

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

A szabályzat létrehozásának és felügyeletének folyamatát frissítettük a felhasználói folyamatok (korábbi nevén a beépített házirendek) egyszerűbbé tétele érdekében. Ez az új felhasználói élmény mostantól az összes Azure AD-bérlő alapértelmezett alapszolgáltatása.

A portál képernyő felső részén található a **visszajelzés küldése** terület a mosoly vagy a komor ikon használatával további visszajelzést és javaslatokat is megadhat.

További információ az új házirend-kezelési feladatokról: a [Azure ad B2C mostantól JavaScript-testreszabást és számos további új funkciót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogot tartalmaz.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Válassza ki a Azure AD B2C által biztosított adott oldal elemeinek verzióját.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Mostantól kiválaszthatja a Azure AD B2C által biztosított oldal elemeinek egy adott verzióját. Egy adott verzió kiválasztásával tesztelheti a frissítéseket, mielőtt megjelennek az oldalon, és kiszámítható működést érhet el. Emellett a JavaScript-testreszabások engedélyezéséhez is engedélyezheti, hogy bizonyos lapokat is kikényszerítse. A funkció bekapcsolásához nyissa meg a felhasználói folyamatok **Tulajdonságok** lapját.

További információ az oldalelemek adott verzióinak kiválasztásáról: a [Azure ad B2C mostantól JavaScript-testreszabást és számos további új funkciót](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blogot tartalmaz.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>A B2C (GA) konfigurálható végfelhasználói jelszavának követelményei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Most már beállíthatja a szervezete jelszavának összetettségét a végfelhasználók számára, ahelyett, hogy a natív Azure AD-beli jelszavas szabályzatot kellene használnia. A felhasználói folyamatok (korábbi nevén beépített szabályzatok) **Tulajdonságok** paneljén kiválaszthatja az **egyszerű** vagy **erős**jelszó bonyolultságát, vagy létrehozhat egy **Egyéni** követelményt is.

A jelszó-összetettségi követelmények konfigurálásával kapcsolatos további információkért lásd: az [összetettségi követelmények konfigurálása a jelszavakhoz a Azure Active Directory B2Cban](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Új alapértelmezett sablonok az egyéni márkás hitelesítési élményekhez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C

Az új alapértelmezett sablonokat a felhasználói folyamatok (korábbi nevén beépített szabályzatok) **lap elrendezések** paneljén is használhatja, hogy egyéni márkás hitelesítési élményt hozzon létre a felhasználók számára.

További információ a sablonok használatáról: [Azure ad B2C most már rendelkezik JavaScript-testreszabással és számos további új funkcióval](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>2019. január

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>VÁLLALATKÖZI együttműködés Active Directory egyszeri jelszó-hitelesítéssel (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

Bevezetünk egy egyszeri jelszavas hitelesítést (OTP) a B2B vendég felhasználói számára, akik nem hitelesíthetők más módon (például Azure AD, Microsoft-fiók (MSA) vagy Google Federation). Ez az új hitelesítési módszer azt jelenti, hogy a vendég felhasználóknak nem kell új Microsoft-fiók létrehozniuk. Ehelyett a meghívások beváltásakor vagy egy megosztott erőforrás elérésekor a vendég felhasználó egy ideiglenes kódot kérhet az e-mail-címre történő küldéshez. Ezt az ideiglenes kódot használva a vendég felhasználó továbbra is bejelentkezhet.

További információ: az [e-mailek egyszeri jelszavas hitelesítése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) és a blog, az [Azure ad lehetővé teszi a megosztást és együttműködést bármilyen fiókkal rendelkező felhasználó számára](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Új Azure AD Application Proxy cookie-beállítások

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Három új cookie-beállítást vezettünk be, amelyek elérhetők az Application proxyn keresztül közzétett alkalmazások számára:

- **Használjon csak HTTP-alapú cookie-t.** Beállítja a **HTTPOnly** jelzőt az alkalmazásproxy-hozzáférési és munkamenet-cookie-kra. A beállítás bekapcsolása további biztonsági előnyöket biztosít, például segít megakadályozni a cookie-k másolását vagy módosítását az ügyféloldali parancsfájlok használatával. Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon biztonságos cookie-t.** Beállítja az alkalmazásproxy-hozzáférési és munkamenet-cookie-k **biztonságos** jelölőjét. A beállítás bekapcsolása további biztonsági előnyöket biztosít, mivel a cookie-k továbbítása csak TLS-alapú biztonságos csatornákon keresztül történik (például HTTPS). Javasoljuk, hogy kapcsolja be ezt a jelzőt (válassza az **Igen**lehetőséget) a hozzáadott előnyökhöz.

- **Használjon állandó cookie-t.** Megakadályozza, hogy a böngésző bezárása után a hozzáférés-cookie-k lejárnak. Ezek a cookie-k a hozzáférési jogkivonat élettartamának végéig tartanak. A cookie-k azonban alaphelyzetbe állnak, ha eléri a lejárati időt, vagy ha a felhasználó manuálisan törli a cookie-t. Azt javasoljuk, hogy tartsa meg az alapértelmezett **beállítást, és**csak olyan régebbi alkalmazások beállítását kapcsolja be, amelyek nem osztják meg a cookie-kat a folyamatok között.

További információ az új cookie-król: [cookie-beállítások a helyszíni alkalmazások eléréséhez Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – január 2019

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
Január 2019-én ezeket a 35 új alkalmazásokat az alkalmazás-katalógushoz való összevonási támogatással bővítettük:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent paletta](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler internet-hozzáférés rendszergazdája](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [lejárati emlékeztető](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [ Művelet](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso digitális Bezárás](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [működőképes](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease ](https://webapp.callplease.com/create-account/create-account.html), [GTNexus SSO-rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Ares for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager ](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [látogatás](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn Ferry Alp](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Új Azure AD Identity Protection fejlesztések (nyilvános előzetes verzió)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Örömmel jelentjük be, hogy a következő fejlesztéseket adtuk hozzá a Azure AD Identity Protection nyilvános előzetes ajánlathoz, beleértve a következőket:

- Frissített és integráltabb felhasználói felület

- További API-k

- Továbbfejlesztett kockázatértékelés a gépi tanulás segítségével

- A kockázatos felhasználók és a kockázatos bejelentkezések teljes termékre kiterjedő összehangolása

További információ a fejlesztésekről: [Mi az Azure Active Directory Identity Protection (frissítve)?](https://aka.ms/IdentityProtectionDocs) Ha többet szeretne megtudni, és megoszthatja gondolatait a terméken belüli kérdésekkel.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>A Microsoft Authenticator alkalmazás új alkalmazás-zárolási funkciója iOS-és Android-eszközökön

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Microsoft Authenticator alkalmazás  
A **termék képességei:** Identitás-biztonsági & védelme

Az egyszer használatos PIN-kódok, az alkalmazásadatok és az Alkalmazásbeállítások biztonságosabbá tételéhez bekapcsolhatja az alkalmazás zárolási funkcióját a Microsoft Authenticator alkalmazásban. Az alkalmazás zárolásának bekapcsolása azt jelenti, hogy minden alkalommal, amikor megnyitja a Microsoft Authenticator alkalmazást, a rendszer kérni fogja a PIN-kód vagy a biometrikus adatok hitelesítését.

További információ: [Microsoft Authenticator alkalmazás – gyakori kérdések](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Továbbfejlesztett Azure AD Privileged Identity Management (PIM) exportálási képességei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management

A Privileged Identity Management (PIM) rendszergazdák mostantól exportálják az összes aktív és jogosult szerepkör-hozzárendelést egy adott erőforráshoz, beleértve az összes alárendelt erőforráshoz tartozó szerepkör-hozzárendeléseket is. Korábban nehéz volt, hogy a rendszergazdák teljes listát kapjanak az előfizetéshez tartozó szerepkör-hozzárendelésekről, és minden egyes erőforráshoz hozzá kellett exportálni a szerepkör-hozzárendeléseket.

További információ: a [PIM-beli Azure-erőforrás szerepköreinek tevékenység-és naplózási előzményeinek megtekintése](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>November/december 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>A szinkronizálási hatókörből eltávolított felhasználók már nem váltanak csak felhőalapú fiókokra

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Felhasználói felügyelet  
A **termék képességei:** Directory

>[!Important]
>A javítás miatt meghallottuk és megértettük a frusztrációt. Ezért ezt a módosítást csak addig a lépésig adtuk vissza, amíg a javítás könnyebben megvalósítható a szervezetében.

Kijavítottunk egy hibát, amelyben a felhasználó DirSyncEnabled jelölője helytelenül lett **hamis** értékre állítva, ha a Active Directory tartományi szolgáltatások (AD DS) objektumot kizárták a szinkronizációs hatókörből, majd áthelyezték a Lomtárba az Azure ad-ben az alábbiak szerint szinkronizálási ciklus. A javítás eredményeképpen, ha a felhasználó ki van zárva a szinkronizálási hatókörből, és ezt követően az Azure AD Lomtárból lett visszaállítva, a felhasználói fiók a várt módon szinkronizálva marad a helyszíni AD-ből, és nem kezelhető a felhőben, mivel a szolgáltatói forrás (SoA) továbbra is a következő marad: helyszíni AD.

A javítás előtt probléma merült fel, ha a DirSyncEnabled jelzőt hamis értékre váltották. Helytelen benyomást tett, hogy ezeket a fiókokat csak felhőalapú objektumokra konvertálták, és a fiókokat a felhőben lehet felügyelni. Azonban a fiókok továbbra is megtartották a SoA-t a helyszínen, és minden szinkronizált tulajdonságot (Shadow Attribute) a helyszíni AD-ből. Ez az állapot több problémát okozott az Azure AD-ben és más Felhőbeli számítási feladatokban (például az Exchange Online-ban), amely a fiókokat az Active Directory-ból szinkronizálva várta, de mostantól csak a csak felhőalapú fiókokkal működött.

Ebben az esetben az egyetlen módja, hogy valóban átalakítson egy szinkronizált-ból-AD-fiókot a csak felhőalapú fiókra, ha letiltja az rSync-t a bérlői szinten, ami egy háttérbeli műveletet indít el a SoA átviteléhez. Ilyen típusú SoA-változásra van szükség (de nem korlátozódik a) a helyszíni kapcsolódó attribútumok (például a LastDirSyncTime és az árnyék attribútumai) tisztítására, és más Felhőbeli számítási feladatokhoz is küld egy jelet, hogy a megfelelő objektuma csak felhőalapú fiókra legyen konvertálva. .

Ez a javítás ezért megakadályozza, hogy a közvetlen frissítések az AD-ből szinkronizált felhasználó ImmutableID attribútumán legyenek, ami a múltban bizonyos esetekben szükséges. A tervezés szerint az Azure AD-beli objektum ImmutableID, ahogy azt a név is jelenti, nem változtatható. Az Azure AD Connect Health és Azure AD Connect szinkronizálási ügyfél által megvalósított új szolgáltatások az ilyen forgatókönyvek kezeléséhez érhetők el:

- **Nagy léptékű ImmutableID-frissítés számos felhasználó számára a szakaszos megközelítésekben**
  
  Tegyük fel például, hogy hosszú AD DS erdők közötti áttelepítést kell végeznie. Megoldás: a Azure AD Connect segítségével **konfigurálja a forrás-horgonyt** , és a felhasználó áttelepítésekor másolja a meglévő ImmutableID-értékeket az Azure ad-ből a helyi AD DS felhasználó ms-DS-konzisztencia-GUID attribútumára az új erdőben. További információ: [using ms-DS-ConsistencyGuid as sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Nagy méretű ImmutableID-frissítések egy adott felvétel sok felhasználója számára**

  A Azure AD Connect megvalósítása során például hiba történt, és most meg kell változtatnia a SourceAnchor attribútumot. Megoldás: tiltsa le az rSync-et a bérlői szinten, és törölje az összes érvénytelen ImmutableID-értéket. További információ: [a címtár-szinkronizálás kikapcsolása az Office 365](/office365/enterprise/turn-off-directory-synchronization)-ben.

- **Helyi felhasználó megkeresése egy meglévő felhasználóval az Azure ad-ben** Például egy, a AD DSban újra létrehozott felhasználó létrehoz egy duplikált értéket az Azure AD-fiókban ahelyett, hogy egy meglévő Azure AD-fiókkal (árva objektummal) társítja. Megoldás: használja a Azure Portal Azure AD Connect Health a forrás-és ImmutableID újrafelhasználásához. További információ: [árva objektum forgatókönyve](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Változás megszakítása: a naplózási és a bejelentkezési naplókra vonatkozó frissítések Azure Monitor

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Jelenleg a naplózási és a bejelentkezési naplókat is közzéteszjük Azure Monitoron keresztül, így zökkenőmentesen integrálhatja a naplófájlokat a SIEM-eszközökkel vagy a Log Analyticsokkal. A visszajelzések alapján, és a szolgáltatás általánosan elérhetővé tételének előkészítésében a következő módosításokat végezjük el a sémán. Ezek a séma-változások és a kapcsolódó dokumentációs frissítések a januári első héten történnek.

#### <a name="new-fields-in-the-audit-schema"></a>Új mezők a naplózási sémában
Új **Művelettípus** mezőt adunk hozzá az erőforráson végrehajtott művelet típusának megadásához. Például: **Hozzáadás**, **frissítés**vagy **Törlés**.

#### <a name="changed-fields-in-the-audit-schema"></a>A naplózási séma módosított mezői
A következő mezők változnak a naplózási sémában:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|Kategória|Ez volt a **szolgáltatás neve** mező. Most már a **naplózási kategóriák** mező. A **szolgáltatás neve** átnevezve lett a **loggedByService** mezőre.|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Önkiszolgáló jelszó-visszaállítás</li></ul>|<ul><li>Felhasználókezelés</li><li>Csoportkezelés</li><li>Alkalmazások kezelése</li></ul>|
|targetResources|A legfelső szinten tartalmazza a **TargetResourceType** .|&nbsp;|<ul><li>Szabályzat</li><li>Alkalmazás</li><li>Felhasználó</li><li>Csoport</li></ul>|
|loggedByService|Annak a szolgáltatásnak a nevét adja meg, amely a naplót generálta.|Null|<ul><li>Fiók kiépítése</li><li>Alapvető könyvtár</li><li>Új jelszó önkiszolgáló kérése</li></ul>|
|Eredmény|A naplók eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li></ul>|<ul><li>Sikeres</li><li>Hiba</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Módosult mezők a bejelentkezési sémában
A bejelentkezési sémában a következő mezők változnak:

|Mező neve|Mi változott|Régi értékek|Új értékek|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Ez volt a **conditionalaccessPolicies** mező. Most már a **appliedConditionalAccessPolicies** mező.|Nem változott|Nem változott|
|conditionalAccessStatus|A feltételes hozzáférési házirend állapotának eredményét adja meg a bejelentkezéskor. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Letiltva</li></ul>|
|appliedConditionalAccessPolicies: eredmény|A bejelentkezéskor az egyes feltételes hozzáférési szabályzatok állapotának eredményét adja meg. Korábban ez a számbavétel megtörtént, de most már megmutatjuk a tényleges értéket.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Sikeres</li><li>Hiba</li><li>Nincs alkalmazva</li><li>Letiltva</li></ul>|

További információ a sémáról: [Az Azure ad-naplók sémájának értelmezése Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>A felügyelt gépi tanulási modell és a kockázati pontszám motorjának Identity Protection-fejlesztése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Kockázati pontszámok

Az Identity Protection szolgáltatással kapcsolatos felhasználói és bejelentkezési kockázatkezelési motor fejlesztései segíthetnek a felhasználók kockázati pontosságának és lefedettségének javításában. A rendszergazdák láthatják, hogy a felhasználói kockázati szint már nem kapcsolódik az adott észlelések kockázati szintjéhez, és a kockázatos bejelentkezési események száma és szintje is növekszik.

A kockázatos észleléseket a felügyelt gépi tanulási modell értékeli ki, amely a felhasználó bejelentkezési funkcióinak és észlelési mintázatának további funkcióival kiszámítja a felhasználói kockázatot. A modell alapján előfordulhat, hogy a rendszergazda magas kockázati pontszámokkal rendelkező felhasználókat talál, még akkor is, ha az adott felhasználóhoz tartozó észlelések alacsony vagy közepes kockázattal rendelkeznek. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>A rendszergazdák a Microsoft Authenticator alkalmazással (nyilvános előzetes verzió) alaphelyzetbe állíthatják a saját jelszavukat

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

Az Azure AD-rendszergazdák mostantól alaphelyzetbe állíthatják a saját jelszavukat a Microsoft Authenticator alkalmazás értesítésein vagy egy, a mobil hitelesítő alkalmazásból vagy a hardver-tokenből származó kóddal. A saját jelszavának alaphelyzetbe állításához a rendszergazdák mostantól a következő módszerek közül kettőt képesek használni:

- Microsoft Authenticator alkalmazás értesítése

- Egyéb mobil hitelesítő alkalmazás/hardver token kódja

- E-mail cím

- Telefonhívás

- Szöveges üzenet

További információ a jelszavak alaphelyzetbe állítására szolgáló Microsoft Authenticator alkalmazás használatáról: [Azure ad önkiszolgáló jelszó-visszaállítás – Mobile App és SSPR (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Új Azure AD Cloud Device-rendszergazdai szerepkör (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Hozzáférés-vezérlés

A rendszergazdák a felhőalapú eszköz rendszergazdai feladatainak elvégzéséhez rendelhetnek felhasználókat az új felhőalapú eszköz rendszergazdai szerepkörhöz. A Cloud Device Administrators szerepkörrel rendelkező felhasználók engedélyezheti, letilthatja és törölheti az eszközöket az Azure AD-ben, valamint beolvashatja a Windows 10 BitLocker-kulcsokat (ha vannak) a Azure Portal.

További információ a szerepkörökről és az engedélyekről: [rendszergazdai szerepkörök hozzárendelésének Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Az eszközök kezelése az Azure AD új tevékenység-időbélyegével (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Eszközök életciklusának kezelése

Tisztában vagyunk azzal, hogy idővel frissítenie kell és ki kell vonnia a szervezete eszközeit az Azure AD-ben, hogy elkerülje az elavult eszközöket a környezetben. Ennek a folyamatnak az elősegítése érdekében az Azure AD mostantól új tevékenység-időbélyeggel frissíti az eszközöket, és segít az eszközök életciklusának kezelésében.

További információ az időbélyegző beszerzéséről és használatáról [: How to: az elavult eszközök kezelése az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>A rendszergazdák megkövetelhetik a felhasználóktól a használati feltételek elfogadását minden eszközön

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási
 
A rendszergazdák most bekapcsolhatják a **felhasználók megkövetelése minden eszközre** lehetőséget, hogy a felhasználók elfogadják a használati feltételeket a bérlőn használt összes eszközön.

További információ: a Azure Active Directory használati [feltételeinek használati feltételei című szakasza](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét egy ismétlődő ütemterv alapján

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási
 

A rendszergazdák mostantól bekapcsolhatják a **lejárati hozzájárulási** lehetőséget, hogy a használati feltételek lejárnak az összes felhasználó számára a megadott ismétlődő ütemterv alapján. Az ütemterv lehet évente, kétévente, negyedévente vagy havonta. A használati feltételek lejárta után a felhasználóknak újra el kell fogadniuk.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>A rendszergazdák megadhatják a használati feltételek érvényességét az egyes felhasználók ütemterve alapján

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

A rendszergazdák mostantól megadhatnak egy időtartamot, amelyet a felhasználónak újra el kell fogadnia a használati feltételeket. A rendszergazdák például megadhatják, hogy a felhasználóknak minden 90 naponként újra el kell fogadniuk a használati feltételeket.

További információ: a használati [feltételek hozzáadása szakasz, a Azure Active Directory használati feltételek funkció](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Új Azure AD Privileged Identity Management (PIM) e-mailek Azure Active Directory szerepkörökhöz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
A Azure AD Privileged Identity Managementt (PIM) használó ügyfelek mostantól heti kivonatoló e-mailt kaphatnak, beleértve az elmúlt hét nap alábbi adatait:

- A legfontosabb jogosult és állandó szerepkör-hozzárendelések áttekintése

- Szerepköröket aktiváló felhasználók száma

- A PIM-szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-n kívüli szerepkörökhöz hozzárendelt felhasználók száma

- A PIM-ben "állandónak" kitett felhasználók száma

A PIM-vel és az elérhető e-mail-értesítésekkel kapcsolatos további információkért lásd: [e-mailes értesítések a PIM-ben](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>A csoport alapú licencelés mostantól általánosan elérhető

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory

A csoportos licencelés kívül esik a nyilvános előzetes verzióban, és már általánosan elérhető. Ennek az általános kiadásnak a részeként a szolgáltatás skálázható, és lehetővé tette a csoportos licencelési hozzárendelések újrafeldolgozását egyetlen felhasználó számára, valamint az Office 365 E3/a3 licenccel rendelkező csoportos licencelés használatát.

A csoport alapú licenceléssel kapcsolatos további információkért lásd: [Mi az a Azure Active Directory csoportos licencelés?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – november 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
November 2018-én a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [GRA-PE](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [végtelen Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [ Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge tag portál](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [ideo](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [plex alkalmazások – klasszikus teszt ](https://test.plexonline.com/signon), [Plex-alkalmazások – klasszikus](https://www.plexonline.com/signon), [plex-alkalmazások – UX-teszt](https://test.cloud.plex.com/sso), [plex-alkalmazások – UX](https://cloud.plex.com/sso), [plex-alkalmazások – iam](https://accounts.plex.com/), [mesterségek – gyermekgondozási nyilvántartások, részvétel, & pénzügyi nyomkövető rendszer](https://getcrafts.ca/craftsregistration) 

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

## <a name="october-2018"></a>2018. október

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Az Azure AD-naplók mostantól az Azure Log Analytics (nyilvános előzetes verzió) szolgáltatással működnek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy most már továbbíthatja Azure AD-naplóit az Azure Log Analyticsba! Ez a legtöbbet kért funkció lehetővé teszi, hogy még jobban hozzáférhessen az elemzésekhez az üzleti, a műveletekhez és a biztonsághoz, valamint az infrastruktúra monitorozásához. További információkért tekintse meg az [Azure-beli Azure Active Directoryi tevékenységek naplóit log Analytics most elérhető](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) blog.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – október 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2018 októberében a következő 14 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással:

[Saját díjak](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), a [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), a ambyint, a [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), a [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), a [ON24 virtuális környezet](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), a [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), a [Zscaler három](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial) [, a Phraseanet,](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)az [értékelés](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), a [Workspot Vezérlés](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="azure-ad-domain-services-email-notifications"></a>E-mail-értesítések Azure AD Domain Services

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services

A Azure AD Domain Services riasztásokat biztosít a Azure Portal a hibás konfigurációkkal és a felügyelt tartománnyal kapcsolatos problémákról. Ezek a riasztások részletes útmutatókat tartalmaznak, így megpróbálhatja elhárítani a problémákat anélkül, hogy kapcsolatba kellene lépnie a támogatási szolgálattal.

Októbertől kezdve testreszabhatja a felügyelt tartomány értesítési beállításait, hogy új riasztások bekövetkezésekor a rendszer e-mailt küldjön egy kijelölt csoportba, így nem kell folyamatosan ellenőriznie a portált a frissítésekhez.

További információ: [Azure ad Domain Services értesítési beállításai](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Az Azure AD-portál támogatja a ForceDelete tartományi API-t az egyéni tartományok törléséhez 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Címtár-kezelés  
A **termék képességei:** Directory

Örömmel jelentjük be, hogy mostantól a ForceDelete tartományi API használatával törölheti az egyéni tartományneveket, ha aszinkron módon átnevezi a hivatkozásokat, például a felhasználókat, a csoportokat és az alkalmazásokat az egyéni tartománynévről (contoso.com) a kezdeti alapértelmezett tartománynévre ( contoso.onmicrosoft.com).

Ezzel a módosítással gyorsabban törölheti az egyéni tartományneveket, ha a szervezet már nem használja a nevet, vagy ha a tartománynevet egy másik Azure AD-vel kell használnia.

További információ: [Egyéni tartománynév törlése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name).

---

## <a name="september-2018"></a>2018. szeptember
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>A dinamikus csoportok rendszergazdai szerepkörére vonatkozó engedélyek frissítve

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Kijavítunk egy problémát, hogy az adott rendszergazdai szerepkörök most már létrehozhatnak és frissíthetnek dinamikus tagsági szabályokat anélkül, hogy a csoport tulajdonosának kellene lennie.

A szerepkörök a következők:

- Globális rendszergazda

- Intune-rendszergazda

- Felhasználói rendszergazda

További információ: [dinamikus csoport létrehozása és állapotának ellenõrzése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>A harmadik féltől származó alkalmazások egyszerűsített egyszeri bejelentkezési (SSO) konfigurációs beállításai

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Tisztában vagyunk azzal, hogy az egyszeri bejelentkezés (SSO) a szolgáltatott szoftver (SaaS) alkalmazásokhoz való beállítása az egyes alkalmazások konfigurációjának egyedi jellege miatt kihívást jelenthet. A következő külső SaaS-alkalmazásokhoz készült SSO-konfigurációs beállítások automatikus feltöltéséhez készítettünk egy egyszerűsített konfigurációs élményt:

- Zendesk

- Online ArcGis

- Jamf Pro

Az egykattintásos élmény használatának megkezdéséhez nyissa meg az alkalmazás **Azure Portal** > **SSO konfigurációs** lapját. További információ: Saas- [alkalmazások integrációja Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory – hol találhatók az adatai? oldalala

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** GoLocal

Válassza ki a vállalat régióját a **Azure Active Directoryból – itt** megtekintheti, hogy melyik Azure-adatközpontban található az Azure ad-adatok az összes Azure ad-szolgáltatáshoz. Az információkat a vállalat régiójának adott Azure AD-szolgáltatásai alapján szűrheti.

A szolgáltatás eléréséhez és további információkért tekintse meg a [Azure Active Directory-hol található az adatai](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Új központi telepítési terv érhető el a saját alkalmazások hozzáférési paneljén

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO

Tekintse meg a saját alkalmazások hozzáférési paneljén elérhető új központi telepítési csomagot (https://aka.ms/deploymentplans).
A saját alkalmazások hozzáférési panelje lehetővé teszi, hogy a felhasználók egyetlen helyen megtalálják és hozzáférjenek az alkalmazásaihoz. Ez a portál olyan önkiszolgáló lehetőségeket is biztosít a felhasználók számára, mint például az alkalmazások és csoportok elérésének kérelmezése vagy az erőforrásokhoz való hozzáférés kezelése mások nevében.

További információ: [Mi a My apps portál?](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Új hibaelhárítás és támogatás lap a Azure Portal bejelentkezési naplók lapján

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

A Azure Portal **bejelentkezési** lapjának új **Hibaelhárítás és támogatás** lapján segítséget nyújt a rendszergazdáknak az Azure ad-bejelentkezésekkel kapcsolatos problémák elhárításában. Az új lapon a hibakód, a hibaüzenet és a Szervizelési javaslatok (ha vannak) a probléma megoldásához nyújtanak segítséget. Ha nem tudja megoldani a problémát, a **Másolás a vágólapra** lehetőség használatával új módszert biztosítunk a támogatási jegy létrehozásához, amely feltölti a **kérelem azonosítóját** és **dátumát (UTC)** a naplófájlhoz a támogatási jegyben.  

![Az új lapot megjelenítő bejelentkezési naplók](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>A dinamikus tagsági szabályok létrehozásához használt egyéni bővítmények tulajdonságainak továbbfejlesztett támogatása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

Ezzel a frissítéssel mostantól az **Egyéni bővítmény tulajdonságainak beolvasása** hivatkozásra kattintva megtekintheti a dinamikus felhasználói csoport-szerkesztőt, megadhatja az egyedi alkalmazás azonosítóját, és megkaphatja a dinamikus tagsági szabály létrehozásakor használni kívánt egyéni bővítmény-tulajdonságok teljes listáját. Ez a lista az alkalmazás új egyéni bővítmény-tulajdonságainak beolvasására is frissíthető.

A dinamikus tagsági szabályok egyéni bővítmény-tulajdonságainak használatával kapcsolatos további információkért lásd a [bővítmény tulajdonságai és az egyéni bővítmény tulajdonságai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties) című témakört.

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement)listáján:

- Microsoft To-Do

- Microsoft Stream

További információ eléréséhez lásd:

- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Új támogatás az önkiszolgáló jelszó-visszaállításhoz a Windows 7/8/8.1 zárolási képernyőjéről

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés

Miután beállította ezt az új szolgáltatást, a felhasználók a Windows 7, a Windows 8 vagy a Windows 8,1 operációs rendszert futtató eszközök **zárolási** képernyőjén egy hivatkozást fognak látni a jelszavuk visszaállítására. Ha erre a hivatkozásra kattint, a felhasználó ugyanazzal a jelszó-visszaállítási folyamattal vezérli, mint a böngészőben.

További információ: a [jelszó-visszaállítás engedélyezése a Windows 7, 8 és 8,1](https://aka.ms/ssprforwindows78) rendszerből

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változási Megjegyzés: az engedélyezési kódok többé nem lesznek elérhetők újra 

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

A 2018. november 15-én kezdődően az Azure AD nem fogadja el a korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági változás segíti az Azure AD-t az OAuth-specifikációnak megfelelően bevezetni, és a v1 és v2 végpontokon is érvényben lesz.

Ha az alkalmazás az engedélyezési kódokat több erőforráshoz tartozó jogkivonatok lekérésére használja, javasoljuk, hogy a kód segítségével szerezzen be egy frissítési tokent, majd a frissítési token használatával további jogkivonatokat szerezzen be más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési tokenek többször is használhatók több erőforrásban. Egy olyan alkalmazás, amely egy hitelesítési kód újrafelhasználását kísérli meg a OAuth-kód folyamata során, invalid_grant hibaüzenetet kap.

Ehhez és más protokollok kapcsolódó módosításaihoz tekintse [meg a hitelesítés újdonságait tartalmazó teljes listát](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – szeptember 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 szeptemberében a következő 16 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [a](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial) [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), a [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), a [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), a [Ambrus kiry noemi Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), a [hópehely](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), a NavigoCloud, a [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), a JOIN.me, a [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial) [, a Silverback,](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)a Riverbed Xirrus EasyPass, [RACKSPACE SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO az Azure-hoz, surveymonkey, [összehívás](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="support-for-additional-claims-transformations-methods"></a>További jogcímek átalakítási módszereinek támogatása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Bevezetjük az új jogcím-átalakítási módszereket, ToLower () és ToUpper (), amelyek az SAML-tokenekre alkalmazhatók az SAML-alapú **egyszeri bejelentkezés konfigurációs** oldaláról.

További információ: [az SAML-tokenben kiadott jogcímek testreszabása az Azure ad-ben nagyvállalati alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Frissített SAML-alapú alkalmazás konfigurációs felhasználói felülete (előzetes verzió)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

A frissített SAML-alapú alkalmazás konfigurációs felhasználói felületének részeként a következőkre lesz szüksége:

- Frissített útmutató az SAML-alapú alkalmazások konfigurálásához.

- A konfigurációban található hiányzó vagy helytelen elemek láthatósága.

- Több e-mail-cím is felvehető a lejárati tanúsítvány értesítéséhez.

- Új jogcím-átalakítási módszerek, ToLower () és ToUpper () és sok más.

- A saját jogkivonat-aláíró tanúsítványának feltöltése a vállalati alkalmazásokhoz.

- Az SAML-alkalmazások NameID-formátumának beállítása, valamint a NameID értékének megadásának módja.

A frissített nézet bekapcsolásához kattintson az **egyszeri bejelentkezési** oldal tetején található **új felhasználói felület kipróbálása** hivatkozásra. További információ: [oktatóanyag: SAML-alapú egyszeri bejelentkezés konfigurálása alkalmazáshoz Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal)használatával.

---

## <a name="august-2018"></a>2018. augusztus

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Azure Active Directory IP-címtartományok módosításai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Platform

Nagyobb IP-tartományokat vezetünk be az Azure AD-be, ami azt jelenti, hogy ha konfigurálta az Azure AD IP-címtartományt a tűzfalak, az útválasztók vagy a hálózati biztonsági csoportok számára, frissítenie kell őket. Ezt a frissítést így nem kell újra módosítania a tűzfal, az útválasztó vagy a hálózati biztonsági csoportok IP-tartományának konfigurációjában, amikor az Azure AD új végpontokat ad hozzá. 

A hálózati forgalom ezen új tartományokra kerül át a következő két hónapban. A megszakítás nélküli szolgáltatás folytatásához ezeket a frissített értékeket a 2018. szeptember 10. előtt adja hozzá az IP-címekhez:

- 20.190.128.0/18 

- 40.126.0.0/18 

Javasoljuk, hogy ne távolítsa el a régi IP-címtartományt, amíg az összes hálózati forgalom az új tartományokra nem került. Az áthelyezéssel és a régi tartományok eltávolításával kapcsolatos további információkért lásd: [Office 365 URL-címek és IP-címtartományok](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Változási Megjegyzés: az engedélyezési kódok többé nem lesznek elérhetők újra 

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

A 2018. november 15-én kezdődően az Azure AD nem fogadja el a korábban használt hitelesítési kódokat az alkalmazásokhoz. Ez a biztonsági változás segíti az Azure AD-t az OAuth-specifikációnak megfelelően bevezetni, és a v1 és v2 végpontokon is érvényben lesz.

Ha az alkalmazás az engedélyezési kódokat több erőforráshoz tartozó jogkivonatok lekérésére használja, javasoljuk, hogy a kód segítségével szerezzen be egy frissítési tokent, majd a frissítési token használatával további jogkivonatokat szerezzen be más erőforrásokhoz. Az engedélyezési kódok csak egyszer használhatók, de a frissítési tokenek többször is használhatók több erőforrásban. Egy olyan alkalmazás, amely egy hitelesítési kód újrafelhasználását kísérli meg a OAuth-kód folyamata során, invalid_grant hibaüzenetet kap.

Ehhez és más protokollok kapcsolódó módosításaihoz tekintse [meg a hitelesítés újdonságait tartalmazó teljes listát](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Átszervezett biztonsági adatok kezelése az önkiszolgáló jelszó (SSPR) és a Multi-Factor Authentication (MFA) számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés
 
Ez az új szolgáltatás lehetővé teszi a felhasználók számára, hogy a SSPR és az MFA-hoz hasonló biztonsági adatokat (például telefonszámot, mobil alkalmazást stb.) kezeljenek egyetlen helyen és környezetben. az előzőekhez képest, ahol két különböző helyen történt.

Ez az összevont élmény a SSPR vagy MFA-t használó felhasználók számára is működik. Továbbá, ha a szervezet nem érvényesíti az MFA-t vagy a SSPR-regisztrációt, a felhasználók továbbra is regisztrálhatják a szervezet által a saját alkalmazások portálon engedélyezett MFA-vagy SSPR-biztonsági adatokat.

Ez egy nyilvános előzetes verzió. A rendszergazdák bekapcsolhatják az új felhasználói élményt (ha szükséges) egy kiválasztott csoporthoz vagy egy bérlő összes felhasználója számára. Az átszervezett felülettel kapcsolatos további információkért tekintse meg a [konvergens élmény blogját](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/) .

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Új, csak HTTP-alapú cookie-k beállítása az Azure AD Application proxy-alkalmazásokban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Létezik egy új beállítás, amely **csak HTTP-cookie-kat** használ az alkalmazásproxy alkalmazásaiban. Ez a beállítás segít a fokozott biztonság biztosításában, beleértve a HTTPOnly jelzőt is a HTTP-válasz fejlécében az alkalmazásproxy-hozzáférés és a munkamenet-cookie-k esetében, így leállítja a cookie-hoz való hozzáférést egy ügyféloldali parancsfájlból, és megelőzi a másolást vagy a cookie módosítása. Bár ez a jelző korábban nem volt használatban, a cookie-k mindig titkosítottak és SSL-kapcsolaton keresztül lettek továbbítva a helytelen módosítások elleni védelem érdekében.

Ez a beállítás nem kompatibilis az olyan alkalmazásokkal, amelyek ActiveX-vezérlőket használnak, például Távoli asztal. Ha ebben a helyzetben van, javasoljuk, hogy kapcsolja ki ezt a beállítást.

A csak HTTP-alapú cookie-k beállításával kapcsolatos további információkért lásd: [alkalmazások közzététele az Azure ad Application proxy használatával](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Az Azure-erőforrások Privileged Identity Management (PIM) támogatja a felügyeleti csoport erőforrás-típusait

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Az igény szerinti aktiválási és hozzárendelési beállítások mostantól alkalmazhatók a felügyeleti csoport erőforrásaira, ugyanúgy, mint az előfizetések, az erőforráscsoportok és az erőforrások (például virtuális gépek, App Services stb.). Emellett bárki, aki egy felügyeleti csoport rendszergazdai hozzáférését biztosító szerepkörrel rendelkezik, felderítheti és kezelheti az adott erőforrást a PIM-ben.

A PIM-és az Azure-erőforrásokkal kapcsolatos további információkért lásd: [Azure-erőforrások felderítése és kezelése Privileged Identity Management használatával](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>Az alkalmazás-hozzáférés (előzetes verzió) gyorsabb hozzáférést biztosít az Azure AD-portálhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Ma, amikor a PIM használatával aktiválja a szerepkört, több mint 10 percet is igénybe vehet, amíg az engedélyek érvénybe lépnek. Ha az alkalmazás-hozzáférés használata mellett dönt, amely jelenleg nyilvános előzetes verzióban érhető el, akkor a rendszergazdák az aktiválási kérelem befejeződése után azonnal hozzáférhetnek az Azure AD-portálhoz.

Az alkalmazás-hozzáférés jelenleg csak az Azure AD-portál és az Azure-erőforrások használatát támogatja. További információ a PIM-ről és az alkalmazás-hozzáférésről: [Mi az Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – augusztus 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 augusztusában a következő 16 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Szarvascsőrű](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [bridgeline kötetlen](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs-mobil-és webes tesztelés](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [meta Networks-összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [így teszünk](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [Promaster (Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [dokumentáció](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F-költségelszámolás](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 élő csevegés](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>A natív tabló támogatása mostantól elérhető az Azure-ban AD Application Proxy

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Az OpenID-vel való frissítéssel az előhitelesítési protokollhoz tartozó OAuth 2,0 Code Grant protokollhoz való csatlakozással már nem kell további konfigurálást végeznie a tabló és az alkalmazásproxy használatához. Ez a protokoll változása azt is lehetővé teszi, hogy az alkalmazásproxy jobban támogassa a modern alkalmazásokat csak a HTTP-átirányítások használatával, amelyek JavaScript-és HTML-címkékben általában támogatottak.

A tabló natív támogatásával kapcsolatos további információkért tekintse meg [Az Azure ad Application proxy most a natív tabló-támogatással foglalkozó](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)témakört.

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Új támogatás a Google identitás-szolgáltatóként való hozzáadásához Azure Active Directoryban (előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

A szervezete Google-nal való összevonásának beállításával lehetővé teheti a meghívott Gmail-felhasználók számára, hogy meglévő Google-Fiókjával jelentkezzenek be a megosztott alkalmazásaiba és erőforrásaiba anélkül, hogy személyes Microsoft-fiókot (MSAs) vagy Azure AD-fiókot kellene létrehoznia.

Ez egy nyilvános előzetes verzió. A Google Federation szolgáltatással kapcsolatos további információkért lásd: a [Google hozzáadása identitás-szolgáltatóként a B2B vendég felhasználói számára](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

## <a name="july-2018"></a>2018. július

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Azure Active Directory e-mail-értesítések fejlesztése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás-életciklus kezelése
 
A Azure Active Directory (Azure AD) e-mailek mostantól egy frissített kialakítással, valamint a küldő e-mail-címének és a feladó megjelenített nevének a változásával is rendelkeznek, ha a következő szolgáltatásokból küldenek:
 
- Azure AD-hozzáférési felülvizsgálatok
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Azure AD Privileged Identity Management
- Vállalati alkalmazás lejárati tanúsítványának értesítései
- Vállalati alkalmazások kiépítési szolgáltatásával kapcsolatos értesítések
 
Az e-mail-értesítések a következő e-mail-cím és megjelenítendő név alapján lesznek elküldve:

- E-mail-cím: azure-noreply@microsoft.com
- Megjelenítendő név: Microsoft Azure
 
Az új e-mail-tervekre és további információkra például az [e-mailes értesítések az Azure ad PIM-ben](https://go.microsoft.com/fwlink/?linkid=2005832)című témakörben olvashat.

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Az Azure AD-tevékenységek naplói mostantól elérhetők a Azure Monitor

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Az Azure AD-tevékenységek naplói mostantól nyilvános előzetes verzióban érhetők el a Azure Monitor (az Azure platform szintű figyelési szolgáltatása). A Azure Monitor a következő fejlesztéseken túl hosszú távú adatmegőrzést és zökkenőmentes integrációt biztosít:

- Hosszú távú adatmegőrzés a naplófájlok saját Azure Storage-fiókba való átirányításával.

- Zökkenőmentes SIEM-integráció, anélkül, hogy az egyéni parancsfájlok írására vagy karbantartására lenne szükség.

- Zökkenőmentes integráció saját egyéni megoldásokkal, elemzési eszközökkel vagy incidens-kezelési megoldásokkal.

Az új képességekkel kapcsolatos további információkért tekintse meg a blog [Azure ad-tevékenységek naplói a Azure monitor Diagnostics szolgáltatásban már nyilvános előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) verzióban és a dokumentációban [Azure Active Directory a tevékenység naplóit a Azure monitor (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)című témakörben.

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Az Azure AD bejelentkezési jelentéshez hozzáadott feltételes hozzáférési információk

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-biztonsági & védelme
 
Ezzel a frissítéssel megtekintheti, hogy mely házirendeket értékeli ki a rendszer, amikor a felhasználó bejelentkezik a szabályzat eredményével együtt. Emellett a jelentés most már tartalmazza a felhasználó által használt ügyfélalkalmazás típusát is, így azonosíthatja a régi protokoll forgalmát. A jelentési bejegyzéseket is megkeresheti egy korrelációs AZONOSÍTÓra, amely a felhasználó által megtekinthető hibaüzenetben található, és felhasználható a megfelelő bejelentkezési kérelem azonosítására és megoldására.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Örökölt hitelesítések megtekintése a bejelentkezési tevékenység naplóiban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése
 
A bejelentkezési tevékenység naplóiban a **Client app (ügyfélalkalmazás** ) mező bevezetésével az ügyfelek már láthatják az örökölt hitelesítést használó felhasználókat. Az ügyfelek hozzáférhetnek ezekhez az adatokhoz a bejelentkezési MS Graph API használatával vagy az Azure AD-portál bejelentkezési tevékenység naplófájljaiban, ahol az **ügyfélalkalmazás** vezérlőelem használatával szűrheti a régi hitelesítéseket. További részletekért tekintse meg a dokumentációt.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – július 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 júliusában a következő 16 új alkalmazást adtuk hozzá az App Gallery összevonási támogatásával:

[Innovációs központ](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [bizonyos rendszergazdai egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC-előkészítés [, ipass-SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [képernyőfelvétel-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified osztályterem, [Eli](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)előkészítés, Bomgar-alapú [távoli támogatás](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [NimbleX ](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imaginer webvízió](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow-összekötő](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [rendszerismeretek alapja](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://aka.ms/azureadapprequest)című témakört.

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Új felhasználó üzembe helyezése SaaS-alkalmazás-integrációk – július 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Az Azure AD lehetővé teszi, hogy automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását SaaS-alkalmazásokban, például a Dropbox, a Salesforce, a ServiceNow és más rendszerekben. Július 2018-én a következő alkalmazásokhoz is hozzáadunk felhasználói üzembe helyezési támogatást az Azure AD App Galleryben:

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bónusz](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók kiépítését támogató összes alkalmazás listáját itt tekintheti meg: [SaaS Application Integration with Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>A kapcsolódási állapot szinkronizáláshoz – egyszerűbb megoldás az árva és ismétlődő attribútumok szinkronizálási hibáinak kijavításához

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** AD-kapcsolat  
A **termék képességei:** & Jelentéskészítés figyelése
 
A Azure AD Connect Health önkiszolgáló szervizelést vezet be, amely segít kiemelni és elhárítani a szinkronizálási hibákat. Ez a szolgáltatás a duplikált attribútum-szinkronizálási hibákat és az Azure AD-ből származó, árva objektumokat javítja. Ez a diagnózis a következő előnyökkel jár:

- Leszűkíti a duplikált attribútum-szinkronizálási hibákat, és adott javításokat biztosít

- A dedikált Azure AD-forgatókönyvek javításának alkalmazása, a hibák feloldása egyetlen lépésben

- A funkció bekapcsolásához és használatához nincs szükség frissítésre vagy konfigurálásra

További információ: [duplikált attribútum-szinkronizálási hibák diagnosztizálása és szervizelése](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Vizuális frissítések az Azure AD-hez és MSA bejelentkezési tapasztalatai

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Felhasználói hitelesítés

Frissítettük a Microsoft online szolgáltatások bejelentkezési FELÜLETét, például az Office 365-es és az Azure-ban. Ezzel a módosítással a képernyők kevésbé zsúfoltak és egyszerűbbek. További információ erről a változásról: az [Azure ad bejelentkezési élményének közelgő újdonságai](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) .

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect új kiadása – július 2018

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése

A Azure AD Connect legújabb kiadása a következőket tartalmazza: 

- Hibajavítások és támogatási frissítések 

- A ping-összevonása integráció általános elérhetősége

- A legújabb SQL 2012-ügyfél frissítései 

További információ erről a frissítésről [: Azure ad Connect verzió kiadási előzményei](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>A használati feltételek végfelhasználói felhasználói felületének frissítése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

Frissítjük az elfogadási karakterláncot a felhasználási feltételek végfelhasználói felhasználói felületén.

**Aktuális szöveg** A [tenantName] erőforrásainak eléréséhez el kell fogadnia a használati feltételeket.<br>**Új szöveg.** A (z) [tenantName] erőforrás eléréséhez el kell olvasnia a használati feltételeket.

**Aktuális szöveg:** Az elfogadás kiválasztása azt jelenti, hogy elfogadja a fenti használati feltételeket.<br>**Új szöveg:** Az elfogadás gombra kattintva erősítse meg, hogy elolvasta és megértette a használati feltételeket.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>Az átmenő hitelesítés támogatja az örökölt protokollokat és alkalmazásokat

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az átmenő hitelesítés mostantól támogatja az örökölt protokollokat és alkalmazásokat. A következő korlátozások mostantól teljes mértékben támogatottak:

- Felhasználói bejelentkezések a régi Office-ügyfélalkalmazásokba, az Office 2010-be és az Office 2013-be, modern hitelesítés nélkül.

- Hozzáférés a naptár megosztásához és az ingyenes/foglalt információkhoz az Exchange hibrid környezetekben az Office 2010-ben.

- Felhasználói bejelentkezések a Skype for Business-ügyfélalkalmazások számára anélkül, hogy modern hitelesítést kellene igényelni.

- Felhasználói bejelentkezések a PowerShell 1,0-es verziójára.

- Az Apple Készülékregisztrációs program (Apple DEP) az iOS beállítási asszisztens használatával. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Átszervezett biztonsági adatok kezelése az önkiszolgáló jelszó-visszaállításhoz és Multi-Factor Authentication

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés

Ez az új szolgáltatás lehetővé teszi a felhasználók számára, hogy az önkiszolgáló jelszó-visszaállítás (SSPR) és a Multi-Factor Authentication (MFA) számára egyetlen felhasználói felületen kezeljék biztonsági adataikat (például telefonszám, e-mail cím, mobil alkalmazás stb.). A felhasználóknak már nem kell ugyanazon biztonsági adatokat regisztrálniuk a SSPR és az MFA-hoz két különböző felhasználói élményben. Ez az új felhasználói élmény a SSPR vagy MFA-t használó felhasználókra is érvényes.

Ha egy szervezet nem érvényesíti az MFA-t vagy a SSPR-regisztrációt, akkor a felhasználók a **saját alkalmazások** portálon regisztrálhatják biztonsági adataikat. A felhasználók az MFA-hoz vagy a SSPR-hez engedélyezett bármely metódust regisztrálhatnak. 

Ez egy nyilvános előzetes verzió. A rendszergazdák bekapcsolhatják az új felhasználói élményt (ha szükséges) egy adott felhasználó vagy egy bérlő összes felhasználója számára.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>A jelszó alaphelyzetbe állításakor használja a Microsoft Authenticator alkalmazást az identitás ellenőrzéséhez

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** SSPR  
A **termék képességei:** Felhasználói hitelesítés

Ez a funkció lehetővé teszi, hogy a nem rendszergazdák ellenőrizzék az identitást, miközben visszaállítanak egy jelszót a Microsoft Authenticator (vagy bármely más hitelesítő alkalmazástól származó) értesítés vagy kód használatával. Miután a rendszergazdák bekapcsolják ezt az önkiszolgáló jelszó-visszaállítási módszert, a aka.ms/mfasetup-on vagy a aka.ms/setupsecurityinfo-en keresztül regisztrált Mobile App ellenőrzési módszerként használhatják a jelszavukat a jelszavuk alaphelyzetbe állításakor.

A Mobile App Notification szolgáltatás csak olyan szabályzat részeként kapcsolható be, amelynek két módszere van a jelszó alaphelyzetbe állításához.

---

## <a name="june-2018"></a>2018. június

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Változási Megjegyzés: biztonsági javítás az alkalmazások delegált engedélyezési folyamatához az Azure AD-tevékenység naplófájljainak API-ját használó alkalmazásokhoz

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Platform

A Transport Layer Security (TLS) egy olyan protokoll, amely adatvédelmet és adatintegritást biztosít két kommunikáló alkalmazás között, és a legszélesebb körben használt biztonsági protokoll, amelyet jelenleg használtak.

A [PCI biztonsági szabványok Tanácsa](https://www.pcisecuritystandards.org/) megállapította, hogy a TLS és SSL (SSL) korai verzióit le kell tiltani az új és biztonságosabb alkalmazás-protokollok engedélyezése mellett, a megfelelőség pedig **2018. június 30-ig**érvényes. Ez a módosítás azt jelenti, hogy ha az Azure AD-szolgáltatásokhoz csatlakozik, és PCI DSS megfelelőséget igényel, le kell tiltania a TLS 1,0-et. A TLS több verziója érhető el, de a TLS 1,2 a legújabb elérhető verzió Azure Active Directory szolgáltatásokhoz. Javasoljuk, hogy az ügyfél/kiszolgáló, valamint a böngésző/kiszolgáló kombinációk esetében közvetlenül a TLS 1,2-et helyezze el.

Előfordulhat, hogy az elavult böngészők nem támogatják az újabb TLS-verziókat, például a TLS 1,2-t. Ha szeretné megtekinteni, hogy a böngésző mely verzióit támogatja a TLS, lépjen a [QUALYS SSL Labs](https://www.ssllabs.com/) -webhelyre, és kattintson a **böngésző tesztelése**elemre. Javasoljuk, hogy frissítsen a webböngésző legújabb verziójára, és lehetőleg csak TLS 1,2-et engedélyezzen.

**A TLS 1,2 engedélyezése böngésző szerint**

- **A Microsoft Edge és az Internet Explorer (mindkettő az Internet Explorerben van beállítva)**

    1. Nyissa meg az Internet Explorert, válassza az **eszközök** > az Internetbeállítások > a **speciális** **lehetőséget** .
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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2018 júniusában az alábbi 15 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), a [Music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), az [SAML 1,1 token engedélyezve LOB-alkalmazás](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), a felépítése [, az automatikus](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial) [feladat](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), a [végpont biztonsági mentése](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), a [SkyHigh hálózatok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), a Smartway2, a [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), a [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), a [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), Helyszíni [SharePoint](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [előre látható CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial). Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>Az Azure AD jelszavas védelme nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Felhasználói hitelesítés

Az Azure AD jelszavas védelemmel segít kiszűrni a környezetből származó könnyen kitalált jelszavakat. Ezeknek a jelszavaknak a kiküszöbölése segít csökkenteni a támadás veszélyét a jelszó típusú spray-típusokkal szemben.

Az Azure AD jelszavas védelme különösen a következőket segíti:

- A szervezet fiókjait az Azure AD-ben és a Windows Server Active Directoryban (AD) is védetté teheti. 
- Leállítja a felhasználók számára a jelszavak használatát a leggyakrabban használt jelszavak 500-nál nagyobb listáján, és több mint 1 000 000 karakteres helyettesítő változatban.
- Az Azure AD jelszavas védelmet az Azure ad-portálon egyetlen helyről felügyelheti, mind az Azure AD-hez, mind a helyszíni Windows Server AD-hoz.

Az Azure AD jelszavas védelemmel kapcsolatos további információkért lásd: [a helytelen jelszavak kizárása a szervezetben](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>Új "minden vendég" – a használati feltételek létrehozása során létrehozott feltételes hozzáférési szabályzat sablonja

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

A használati feltételek létrehozása során a rendszer létrehoz egy új feltételes hozzáférési szabályzatot a "minden vendég" és az "összes alkalmazás" számára is. Ez az új házirend-sablon az újonnan létrehozott felhasználási feltételeket alkalmazza, és egyszerűbbé teszi a vendégek számára a létrehozás és a kényszerítés folyamatát.

További információ: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>A használati feltételek létrehozása során létrehozott új "egyéni" feltételes hozzáférési szabályzat sablonja

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Cégirányítási

A használati feltételek létrehozásakor létrejön egy új "egyéni" feltételes hozzáférési házirend-sablon is. Ez az új házirend-sablon lehetővé teszi a felhasználási feltételek létrehozását, majd a feltételes hozzáférési szabályzat létrehozási paneljén való azonnali megnyitását anélkül, hogy manuálisan kellene navigálnia a portálon.

További információ: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Új és átfogó útmutató az Azure-Multi-Factor Authentication üzembe helyezéséhez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Identitás-biztonsági & védelme
 
Új lépésenkénti útmutatót kaptunk az Azure Multi-Factor Authentication (MFA) üzembe helyezéséhez a szervezetben.

Az MFA üzembe helyezési útmutatójának megtekintéséhez nyissa meg a [személyazonosság telepítési útmutatók](https://aka.ms/DeploymentPlans) tárházát a githubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [központi telepítési terv visszajelzési űrlapját](https://aka.ms/deploymentplanfeedback). Ha bármilyen kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk a következő címen: [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Az Azure AD által delegált alkalmazások felügyeleti szerepkörei nyilvános előzetes verzióban érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** Access Control

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

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Platform  

A szolgáltatásként kínált szoftverek, például a Azure Active Directory (Azure AD) úgy vannak kialakítva, hogy a lehető legjobban működjenek közvetlenül az interneten keresztül anélkül, hogy ExpressRoute vagy más magánhálózati VPN-alagutakra lenne szükség. Ezért az Azure AD-szolgáltatások ExpressRoute az Azure-beli nyilvános és az Azure-közösségeket használó Microsoft-partnerekkel való támogatását követően leállítjuk. **2018** A változás által érintett szolgáltatások esetében előfordulhat, hogy az Azure AD-forgalom fokozatosan változik a ExpressRoute és az internet között.

A támogatás módosításakor azt is tudjuk, hogy még mindig vannak olyan helyzetek, amikor szükség lehet egy dedikált áramkör használatára a hitelesítési forgalomhoz. Emiatt az Azure AD továbbra is támogatja a bérlői IP-címtartomány korlátozásait a Microsoft ExpressRoute és szolgáltatásainak használatával a "További Office 365 Online Services" Közösséggel. Ha a szolgáltatások érintettek, de ExpressRoute van szüksége, a következőket kell tennie:

- **Ha Azure-beli nyilvános társ-összevonást használ.** Lépjen a Microsoft-partneri kapcsolatra, és regisztráljon a **másik Office 365 Online Services (12076:5100)** Közösségre. További információ arról, hogyan lehet áthelyezni az Azure-ban a nyilvános Microsoft-partnerektől a [Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering) -partnerek felé.

- **Ha Microsoft-társat használ.** Regisztráljon a **másik Office 365 online Service (12076:5100)** Közösségre. Az útválasztási követelményekkel kapcsolatos további információkért tekintse meg a ExpressRoute-útválasztási követelmények cikk a [BGP-Közösségek támogatása című szakaszát](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) .

Ha továbbra is dedikált áramköröket kell használnia, a Microsoft-fiókkal foglalkozó csapatának kell megadnia, hogy hogyan kérhet engedélyt a **másik Office 365 online szolgáltatás (12076:5100)** Közösség használatára. Az MS Office által felügyelt felülvizsgálati tábla ellenőrzi, hogy szüksége van-e az adott áramkörökre, és hogy tisztában legyenek a technikai következményeivel. Az Office 365 útválasztási szűrőinek létrehozására tett jogosulatlan előfizetések hibaüzenetet kapnak. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>Felügyeleti forgatókönyvek Microsoft Graph API-k használata a felhasználási feltételekhez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Fejlesztői élmény
 
Az Azure AD használati feltételeinek felügyeleti működéséhez Microsoft Graph API-kat adtunk hozzá. Létrehozhatja, frissítheti és törölheti a használati feltételeket.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Azure AD több-bérlős végpont hozzáadása identitás-szolgáltatóként Azure AD B2C

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Az egyéni szabályzatok használatával mostantól az Azure AD Common Endpoint Azure AD B2C identitás-szolgáltatóként is hozzáadhatja. Ez lehetővé teszi, hogy egyetlen belépési ponttal rendelkezzen az összes olyan Azure AD-felhasználó számára, akik bejelentkeznek az alkalmazásokba. További információ [: Azure Active Directory B2C: lehetővé teszi a felhasználók számára, hogy egyéni szabályzatok használatával bejelentkezzenek egy több-bérlős Azure ad-identitásba](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Belső URL-címek használatával bárhonnan elérheti alkalmazásait a saját alkalmazások bejelentkezési bővítménnyel és az Azure AD Application Proxy

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO
 
A felhasználók mostantól belső URL-címeken keresztül érhetik el az alkalmazásokat, még akkor is, ha a vállalati hálózaton kívül esnek a saját alkalmazások biztonságos bejelentkezési bővítménnyel az Azure AD-hoz. Ez minden olyan alkalmazással működni fog, amelyet az Azure AD Application Proxy használatával tett közzé, minden olyan böngészőben, amelyen telepítve van a hozzáférési panel böngésző bővítménye. Az URL-átirányítás funkció automatikusan engedélyezve lesz, amint a felhasználó bejelentkezik a bővítménybe. A bővítmény letölthető a [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), a [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)és a [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)számára.

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory – az Európa Európai ügyfeleinek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** GoLocal

Az Európai ügyfelek az adatoknak Európában maradnak, és nem replikálódnak az Európai adatközpontokon kívül az adatvédelem és az európai jogszabályok megtartása érdekében. Ez a [cikk](https://go.microsoft.com/fwlink/?linkid=872328) részletesen ismerteti, hogy milyen információk lesznek tárolva az Európai adatközpontokban, valamint az adatközpontokon kívül tárolt információkkal kapcsolatos részleteket is tartalmaz. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Új felhasználó üzembe helyezése SaaS-alkalmazás-integrációk – május 2018

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Az Azure AD lehetővé teszi, hogy automatizálja a felhasználói identitások létrehozását, karbantartását és eltávolítását SaaS-alkalmazásokban, például a Dropbox, a Salesforce, a ServiceNow és más rendszerekben. Május 2018-én a következő alkalmazásokhoz is hozzáadunk felhasználói üzembe helyezési támogatást az Azure AD App Galleryben:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Sarokköve OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Az Azure AD-katalógusban a felhasználók üzembe helyezését támogató összes alkalmazás listáját itt tekintheti meg: [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>A csoportok és az alkalmazás-hozzáférés Azure AD hozzáférési felülvizsgálatai mostantól ismétlődő értékeléseket is biztosítanak

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Cégirányítási
 
A csoportok és alkalmazások hozzáférési felülvizsgálata mostantól általánosan elérhető a prémium szintű Azure AD P2 részeként.  A rendszergazdák a csoporttagság és az alkalmazás-hozzárendelések hozzáférési felülvizsgálatait rendszeres időközönként, például havonta vagy negyedévente is megismétlődnek.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Az Azure AD-tevékenységek naplói (bejelentkezések és naplózás) mostantól az MS Graphon keresztül érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése
 
Az Azure AD-tevékenységek naplói, amelyek a bejelentkezéseket és a naplókat is tartalmazzák, mostantól az MS Graph használatával érhetők el. A naplók eléréséhez két végpontot tettünk elérhetővé az MS Graph használatával. Az első lépésekhez tekintse meg a [dokumentációt](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) az Azure ad Reporting API-k programozott eléréséhez. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>A B2B beváltási élményének fejlesztése és a szervezeti feladatok elhagyása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

**Egyszeri visszaváltás:** Miután megoszt egy erőforrást egy vendég felhasználóval a B2B API használatával – nem kell elküldenie egy különleges meghívó e-mailt. A legtöbb esetben a vendég felhasználói hozzáférhetnek az erőforráshoz, és a beváltási élmény időben lesznek elvégezve. Nincs több hatása a kihagyott e-mailek miatt. Nem kell többé megkérdezni a vendég felhasználóit "a visszaváltási hivatkozásra kattintva a rendszer küldte?". Ez azt jelenti, hogy ha a "a" meghívót használja, akkor a Felhőbeli mellékletek ugyanazzal a kanonikus URL-címmel rendelkezhetnek az összes felhasználóhoz – belső és külső – a beváltási állapotban.

**Modern beváltási élmény:** Nincs több osztott képernyős beváltási Kezdőlap. A felhasználók a meghívásos szervezet Adatvédelmi nyilatkozatával modern beleegyező felhasználói élményt kapnak, ugyanúgy, mint a harmadik féltől származó alkalmazások esetében.

**A vendég felhasználói elhagyhatják a szervezeti** szervezetet: Ha a felhasználó kapcsolata egy szervezettel már véget ért, a szervezete elhagyhatja őket. Nem kell többé felhívni a meghívót, hogy a szervezet rendszergazdája "el kellene távolítani", és ne legyen több támogatási jegy.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások – 2018. május

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
A 2018-es verzióban az alábbi 18 új alkalmazást bővítettük az alkalmazás-katalógusban található összevonási támogatással:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty szabályozása, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [JAMF Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [ENVI MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe rabul Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial),[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [arc Publishing-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Új lépésenkénti üzembe helyezési útmutatók a Azure Active Directory

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Directory
 
Új, lépésenkénti útmutató a Azure Active Directory (Azure AD) üzembe helyezéséhez, beleértve az önkiszolgáló jelszó-visszaállítás (SSPR), az egyszeri bejelentkezés (SSO), a feltételes hozzáférés (CA), az App proxy, a felhasználók kiépítése, a Active Directory összevonási szolgáltatások (AD FS) (ADFS) Átmenő hitelesítés (PTA ESP) és az ADFS jelszavas kivonatának szinkronizálása (PHS).

Az üzembe helyezési útmutatók megtekintéséhez nyissa meg az [Identity Deployment Guides](https://aka.ms/DeploymentPlans) -tárházat a githubon. Ha visszajelzést szeretne küldeni a telepítési útmutatókról, használja a [központi telepítési terv visszajelzési űrlapját](https://aka.ms/deploymentplanfeedback). Ha bármilyen kérdése van az üzembe helyezési útmutatókkal kapcsolatban, lépjen kapcsolatba velünk a következő címen: [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Vállalati alkalmazások keresése – további alkalmazások betöltése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Nem sikerül megkeresni az alkalmazásokat/egyszerű szolgáltatásokat? A vállalati alkalmazások minden alkalmazás listájában további alkalmazások tölthetők be. Alapértelmezés szerint 20 alkalmazást mutatunk be. Most kattintson a lehetőségre **, majd** a további alkalmazások megjelenítéséhez. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>A AADConnect megjelenése a PingFederate, fontos biztonsági frissítésekkel, számos hibajavítással és új, nagyszerűen új hibaelhárítási eszközökkel való integráció nyilvános előzetes verzióját tartalmazza. 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** AD-kapcsolat  
A **termék képességei:** Identitás-életciklus kezelése
 
A AADConnect megjelenése a PingFederate, fontos biztonsági frissítésekkel, számos hibajavítással és új, nagyszerűen új hibaelhárítási eszközökkel való integráció nyilvános előzetes verzióját tartalmazza. A kibocsátási megjegyzések [itt](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)találhatók.

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD hozzáférési felülvizsgálatok: automatikus alkalmazás

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Cégirányítási

A csoportok és alkalmazások hozzáférési felülvizsgálatai mostantól általánosan elérhetők a prémium szintű Azure AD P2 részeként. A rendszergazda beállíthatja, hogy automatikusan alkalmazza a véleményező módosításait az adott csoportra vagy alkalmazásra, mivel a hozzáférési felülvizsgálat befejeződik. A rendszergazda azt is megadhatja, hogy mi történik a felhasználó folyamatos hozzáférésével, ha a felülvizsgálók nem válaszolnak, nem távolítják el a hozzáférést, megtartják a hozzáférést vagy a rendszerjavaslatokat. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>Az azonosító jogkivonatok már nem adhatók vissza az új alkalmazások lekérdezési response_mode használatával. 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A 2018 április 25-én vagy azt követően létrehozott alkalmazások nem igényelhetnek **id_token** a **lekérdezési** response_mode használatával.  Ez az Azure AD-t a OIDC-specifikációkkal együtt ismerteti, és segít csökkenteni az alkalmazások támadási felületét.  Az 2018. április 25. előtt létrehozott alkalmazások nincsenek letiltva a **lekérdezési** response_mode a **id_token**response_type való használatával.  A visszaadott hiba, amikor a HRE id_token kér, a **AADSTS70007: "Query" nem támogatott "response_mode" érték a jogkivonat kérésekor**.

A **töredék** -és a **form_post** -response_modes továbbra is működik – új alkalmazásobjektumok létrehozásakor (például az alkalmazásproxy-használathoz) gondoskodjon arról, hogy az egyik response_modes használja, mielőtt új alkalmazást hozna létre.  

---
 
## <a name="april-2018"></a>2018. április 

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C hozzáférési jogkivonat a GA

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C 

A hozzáférési jogkivonatok használatával mostantól hozzáférhet a Azure AD B2C által védett webes API-khoz. A szolgáltatás a nyilvános előzetes verzióról a GA-ra vált. Az Azure AD B2C-alkalmazások és webes API-k konfigurálásának felhasználói felülete továbbfejlesztett, és egyéb kisebb fejlesztéseket hajtottak végre.
 
További információt a [Azure ad B2C: hozzáférési tokenek igénylése](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)című témakörben talál.

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>Az egyszeri bejelentkezés konfigurációjának tesztelése SAML-alapú alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

Az SAML-alapú SSO-alkalmazások konfigurálásakor tesztelheti az integrációt a konfiguráció lapon. Ha a bejelentkezés során hibát észlel, megadhatja a hibát a tesztelési élményben, és az Azure AD megoldást nyújt az adott probléma megoldásához szükséges megoldási lépések elvégzésére.

További információ eléréséhez lásd:

- [Egyszeri bejelentkezés konfigurálása az Azure Active Directory alkalmazáskatalógusában nem szereplő alkalmazásokhoz](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [Az SAML-alapú egyszeri bejelentkezések hibakeresése Azure Active Directory-alkalmazásokban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---
 
### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Az Azure AD használati feltételei mostantól felhasználónkénti jelentéskészítéssel rendelkeznek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 
A rendszergazdák mostantól kiválaszthatnak egy adott felhasználási helyet, és megtekinthetik az összes olyan felhasználót, aki beleegyezett az adott felhasználási feltételekbe, és hogy milyen időpontban és időpontban történt.

További információkért lásd az [Azure ad használati feltételeinek funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---
 
### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health: kockázatos IP-cím AD FS extranetes zárolási védelemhez 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** & Jelentéskészítés figyelése

A csatlakoztatási állapot mostantól lehetővé teszi azon IP-címek észlelését, amelyek a sikertelen U/P-bejelentkezések küszöbértékét óránként vagy napi szinten túllépik. A szolgáltatás által biztosított képességek a következők:

- Átfogó jelentés, amely az IP-címet és a sikertelen bejelentkezések másodpercenkénti számát jeleníti meg, testreszabható küszöbértékkel.
- E-mail-alapú riasztások, amelyekben egy adott IP-cím túllépte a sikertelen U/P bejelentkezések küszöbértékét óránként/naponta.
- Letöltési lehetőség az adatgyűjtés részletes elemzéséhez

További információ: [kockázatos IP-jelentés](https://aka.ms/aadchriskyip).

---
 
### <a name="easy-app-config-with-metadata-file-or-url"></a>Egyszerű alkalmazás konfigurációja metaadat-fájllal vagy URL-címmel

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO

A vállalati alkalmazások lapon a rendszergazdák feltölthetnek egy SAML-metaadat-fájlt az SAML-alapú bejelentkezés konfigurálásához a HRE Gallery és a nem katalógus alkalmazáshoz.

Emellett az Azure AD Application Federation metaadat-URL-címével is konfigurálhatja az SSO-t a célként megadott alkalmazással.

További információ: [az egyszeri bejelentkezés konfigurálása olyan alkalmazásokhoz, amelyek nem szerepelnek a Azure Active Directory alkalmazás-galériában](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps).

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Az Azure AD Használati feltételek már általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 

Az Azure AD használati feltételei a nyilvános előzetes verzióról általánosan elérhetővé váltanak.

További információkért lásd az [Azure ad használati feltételeinek funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>Adott szervezetek VÁLLALATKÖZI felhasználói meghívásának engedélyezése vagy letiltása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

Mostantól megadhatja, hogy mely partnerszervezetek számára szeretne megosztani és együttműködni az Azure AD B2B együttműködésben. Ehhez dönthet úgy, hogy létrehoz egy adott engedélyezési vagy megtagadási tartományt tartalmazó listát. Ha a tartományok blokkolva vannak ezekkel a képességekkel, az alkalmazottak többé nem küldhetnek meghívókat az adott tartományban lévő személyek számára.

Ez segít az erőforrásokhoz való hozzáférés szabályozásában, miközben zökkenőmentes felhasználói élményt tesz lehetővé a jóváhagyott felhasználók számára.

Ez a VÁLLALATKÖZI együttműködési funkció minden Azure Active Directory ügyfél számára elérhető, és prémium szintű Azure AD-funkciókkal, például a feltételes hozzáféréssel és az identitás-védelemmel együtt használható a külső üzleti felhasználók aláírásának részletesebb szabályozása érdekében. és hozzáférés.

További információ: a [vállalatközi felhasználók meghívásának engedélyezése vagy letiltása adott szervezetekben](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Április 2018-én a következő 13 új alkalmazást adtuk hozzá az alkalmazás-galériához való összevonási támogatással:

HCM, [FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial), [Secret Server (on-premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial), [dinamikus jel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial), [mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial), [szervezeti diagram most](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial), [Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial), [AppNeta Performance monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial), [Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial) , [Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial), [ Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial), polc, [Biztonság](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört.

---
 
### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>B2B-felhasználók engedélyezése az Azure AD-ben a helyszíni alkalmazásokhoz (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C

Azure Active Directory (Azure AD) B2B együttműködési képességeket használó szervezetként, amellyel meghívhatja a vendég felhasználókat a partnerszervezetektól az Azure AD-be, mostantól elérhetővé teheti a B2B-felhasználók számára a helyszíni alkalmazásokhoz való hozzáférést. Ezek a helyszíni alkalmazások SAML-alapú hitelesítést vagy integrált Windows-hitelesítést (IWA) használhatnak a Kerberos által korlátozott delegálással (KCD).

További információkért lásd: [B2B-felhasználók engedélyezése az Azure ad-ben a helyszíni alkalmazásokhoz való hozzáféréshez](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises).

---
 
### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>SSO-integrációs útmutatók beszerzése az Azure Marketplace-ről

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Más  
**Termék képesség:** harmadik féltől származó integráció

Ha az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1) -en felsorolt alkalmazások TÁMOGATJÁK az SAML-alapú egyszeri bejelentkezést, a letöltés gombra kattintva elérhetővé **válik** az alkalmazáshoz társított integrációs oktatóanyag. 

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>Az Azure AD automatikus felhasználói üzembe helyezésének gyorsabb teljesítménye SaaS-alkalmazásokhoz

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Korábban a Azure Active Directory felhasználó kiépítési összekötőket használó SaaS-alkalmazásokhoz (például Salesforce, ServiceNow és box) a teljesítmény lassú teljesítményt jelenthet, ha az Azure AD-bérlők több mint 100 000 egyesített felhasználóval rendelkeznek, és csoportok, és felhasználói és csoportos hozzárendelések használatával határozzák meg, hogy mely felhasználókat kell kiépíteni.

2018. április 2-án jelentős teljesítménybeli fejlesztések történtek az Azure AD-létesítési szolgáltatásban, amely nagy mértékben csökkenti a Azure Active Directory és a cél SaaS-alkalmazások közötti kezdeti szinkronizálások elvégzéséhez szükséges időt.

Ennek eredményeképpen sok olyan ügyfelet, akik kezdeti szinkronizálást végeztek a sok napot vagy soha nem teljesített alkalmazásokhoz, mostantól perc vagy óra múlva is befejeződik.

További információ: [Mi történik a kiépítés során?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning#what-happens-during-provisioning)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>Önkiszolgáló jelszó-visszaállítás a Windows 10 zárolási képernyőjéről a hibrid Azure AD-hez csatlakoztatott gépekhez

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés
 
Frissítettük a Windows 10 SSPR funkcióját, hogy támogassa a hibrid Azure AD-hez csatlakoztatott gépek támogatását is. Ez a funkció a Windows 10 RS4 lehetővé teszi a felhasználók számára, hogy a Windows 10 rendszerű gép zárolási képernyőjéről visszaállítsák a jelszavukat. Az önkiszolgáló jelszó-visszaállításra engedélyezett és regisztrált felhasználók használhatják ezt a funkciót.

További információ: [Az Azure ad-jelszó alaphelyzetbe állítása a bejelentkezési képernyőn](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows).

---

## <a name="march-2018"></a>2018. március
 
### <a name="certificate-expire-notification"></a>Tanúsítvány lejárati értesítése

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Az Azure AD értesítést küld, ha egy katalógus vagy nem katalógusbeli alkalmazás tanúsítványa hamarosan lejár. 

Néhány felhasználó nem kapott értesítéseket az SAML-alapú egyszeri bejelentkezéshez konfigurált nagyvállalati alkalmazásokhoz. A probléma megoldódott. Az Azure AD értesítést küld a 7, 30 és 60 nap alatt lejáró tanúsítványokról. Ezt az eseményt a naplókban láthatja. 

További információ eléréséhez lásd:

- [Az összevont egyszeri bejelentkezés tanúsítványainak kezelése Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Tevékenység-jelentések naplózása a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)
 
---
 
### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Twitter-és GitHub-identitás-szolgáltatók Azure AD B2C

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Mostantól a Twitter vagy a GitHub is felvehető identitás-szolgáltatóként Azure AD B2Cban. A Twitter a nyilvános előzetes verzióról a GA-ra vált. A GitHub nyilvános előzetes verzióban érhető el.

További információ: [Mi az az Azure ad B2B Collaboration?](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).
 
---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>Böngésző-hozzáférés korlátozása Intune Managed Browser használatával az Azure AD alkalmazás-alapú feltételes hozzáférés iOS-hez és Androidhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
**Már nyilvános előzetes verzióban érhető el!**

**Egyszeri bejelentkezés Intune Managed Browser:** Az alkalmazottak használhatnak egyszeri bejelentkezést a natív ügyfeleken (például a Microsoft Outlookban) és az Azure AD-hez csatlakoztatott összes alkalmazás Intune Managed Browser.

**Intune Managed Browser feltételes hozzáférési támogatás:** Mostantól megkövetelheti, hogy az alkalmazottak az Intune által felügyelt böngészőt használják az alkalmazás alapú feltételes hozzáférési szabályzatok használatával.

Erről a [blogbejegyzésben](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)olvashat bővebben.

További információ eléréséhez lásd:

- [Alkalmazás-alapú feltételes hozzáférés beállítása](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [Managed Browser-szabályzatok konfigurálása](https://aka.ms/managedbrowser)  

---
 
### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>App proxy-parancsmagok a PowerShell GA modulban

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control
 
Az alkalmazásproxy-parancsmagok támogatása most már a PowerShell GA modulban érhető el. Ehhez szükség van a PowerShell-modulok frissítésére – ha több mint egy évig tart, néhány parancsmag leállhat. 

További információ: [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0).
 
---
 
### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>Az Office 365 natív ügyfeleket nem interaktív protokoll használatával támogatja a zökkenőmentes egyszeri bejelentkezés.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az Office 365 natív ügyfeleket (16.0.8730. xxxx vagy újabb verzió) használó felhasználó csendes bejelentkezési élményt nyújt a zökkenőmentes SSO használatával. Ezt a támogatást a nem interaktív protokoll (WS-Trust) Azure AD-hez való hozzáadásával biztosítjuk.

További információ: [how bejelentkezni egy natív ügyfélen zökkenőmentes egyszeri bejelentkezéssel?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)
 
---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>Ha egy alkalmazás bejelentkezési kéréseket küld az Azure AD bérlői végpontjának, a felhasználók csendes bejelentkezési élményt nyújtanak, zökkenőmentes egyszeri bejelentkezéssel.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A felhasználók csendes bejelentkezési élményt nyújtanak, zökkenőmentes egyszeri bejelentkezéssel, ha egy alkalmazás (például `https://contoso.sharepoint.com`) bejelentkezési kéréseket küld az Azure AD bérlői végpontoknak – vagyis `https://login.microsoftonline.com/contoso.com/<..>` vagy `https://login.microsoftonline.com/<tenant_ID>/<..>` – az Azure AD közös végpontja (`https://login.microsoftonline.com/common/<...>`) helyett.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 

---
 
### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>Csak egy Azure AD-URL-címet kell hozzáadnia két URL-cím helyett a felhasználók intranetes zónájának beállításaira a zökkenőmentes egyszeri bejelentkezéshez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Ha a zökkenőmentes SSO-t a felhasználók számára szeretné kideríteni, csak egy Azure AD-URL-címet kell hozzáadnia a felhasználók intranetes zónájának beállításaihoz a csoportházirend használatával Active Directory: `https://autologon.microsoftazuread-sso.com`. Korábban az ügyfeleknek két URL-címet kellett felvenniük.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso). 
 
---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Március 2018-én a következő 15 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial), [CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial), Wrike, [SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial), segéd: FirstAgenda, [YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial), Vtiger CRM, inwink, [amplitúdó](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial), [Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial), [ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial), [Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial), [Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial) [, Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial), [Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial).
 
Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 

---
 
### <a name="pim-for-azure-resources-is-generally-available"></a>Általánosan elérhető a PIM az Azure-erőforrásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Ha Azure AD Privileged Identity Managementt használ a címtárbeli szerepkörökhöz, mostantól használhatja a PIM időkorlátos hozzáférési és hozzárendelési képességeit az Azure-erőforrás-szerepkörökhöz, például előfizetésekhez, erőforráscsoportokhoz, Virtual Machineshoz és bármely más támogatott erőforráshoz Azure Resource Manager szerint. Multi-Factor Authentication betartatása, amikor a szerepköröket időben aktiválja, és az aktiválásokat a jóváhagyott változtatási ablakokkal egyeztetve ütemezhet. Emellett ez a kiadás a nyilvános előzetes verzióban nem elérhető fejlesztéseket is tartalmaz, beleértve a frissített felhasználói felületet, a jóváhagyási munkafolyamatokat, valamint a szerepkörök kibővítésének lehetőségét a hamarosan lejár, és megújíthatja a lejárt szerepköröket.

További információ: [PIM for Azure Resources (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)
 
---
 
### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>Opcionális jogcímek hozzáadása az alkalmazások jogkivonatához (nyilvános előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD-alkalmazás mostantól egyéni vagy választható jogcímeket is igényelhet a JWTs-vagy SAML-jogkivonatokban.  Ezek olyan felhasználói vagy bérlői jogcímek, amelyek nem szerepelnek a jogkivonatban alapértelmezés szerint, a méret vagy az alkalmazhatósági kényszer miatt.  Ez jelenleg nyilvános előzetes verzióban érhető el az Azure AD-alkalmazásokhoz a 1.0-s és a v 2.0-végpontokon.  A dokumentációban tájékozódhat arról, hogy milyen jogcímeket lehet felvenni, és hogyan szerkesztheti az alkalmazás-jegyzékfájlt a kéréshez.  

További információ: [opcionális jogcímek az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).
 
---
 
### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Az Azure AD támogatja a biztonságosabb OAuth-folyamatok PKCE

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD-dokumentumok frissítve lettek a PKCE támogatásához, ami lehetővé teszi a biztonságosabb kommunikációt a OAuth 2,0 engedélyezési kód engedélyezése során.  A S256 és az egyszerű szöveges code_challenges is támogatottak a 1.0-s és a v 2.0-végpontokon. 

További információ: [engedélyezési kód kérése](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code). 
 
---
 
### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>A munkanap Get_Workers API-ban elérhető összes felhasználói attribútum értékének kiépítés támogatása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
A munkahelyről az Azure AD-be Active Directory való bejövő kiépítés nyilvános előzetes verziója mostantól támogatja a munkanap Get_Workers API-ban elérhető összes attribútum értékének kinyerését és kiépítési lehetőségét. Ez a szolgáltatás több száz további szabványos és egyéni attribútumot is támogat, amelyek a munkaidőn kívüli bejövő létesítési összekötő kezdeti verziójával együtt szállítottak.

További információkért lásd: [a munkanap felhasználói attribútumai listájának testreszabása](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>A csoporttagság dinamikusról statikusra való módosítása és fordítva

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés
 
Megváltoztathatja, hogy a tagság hogyan legyen kezelve egy csoportban. Ez akkor hasznos, ha meg szeretné tartani a csoport nevét és AZONOSÍTÓját a rendszeren, így a csoportra vonatkozó meglévő hivatkozások továbbra is érvényesek; új csoport létrehozásához szükség lesz a hivatkozások frissítésére.
Frissítettük az Azure AD felügyeleti központot a funkció támogatásához. Az ügyfelek mostantól a meglévő csoportokat a dinamikus tagságból a hozzárendelt tagságra válthatják, és fordítva. A meglévő PowerShell-parancsmagok továbbra is elérhetők.

További információ: a [Azure Active Directory csoportok dinamikus tagsági szabályai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>Továbbfejlesztett kijelentkezési viselkedés zökkenőmentes egyszeri bejelentkezéssel

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
Korábban, még akkor is, ha a felhasználók explicit módon kijelentkezettek az Azure AD által védett alkalmazásból, a rendszer automatikusan visszaküldi őket a zökkenőmentes SSO használatával, ha a Corpnet a tartományhoz csatlakoztatott eszközökről próbálnak újra hozzáférni egy Azure AD-alkalmazáshoz. Ezzel a módosítással a kijelentkezés támogatott.  Ez lehetővé teszi, hogy a felhasználók azonos vagy eltérő Azure AD-fiókot használjanak a szolgáltatásba való bejelentkezéshez ahelyett, hogy automatikusan bejelentkeznek a zökkenőmentes SSO használatával.

További információ: [Azure Active Directory zökkenőmentes egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)
 
---
 
### <a name="application-proxy-connector-version-154020-released"></a>Az alkalmazásproxy-összekötő verziója 1.5.402.0 kiadva

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Identitás-biztonsági & védelme
 
Ez az összekötő-verzió fokozatosan zajlik novemberben. Az új összekötő verziója a következő módosításokat tartalmazza:

- Az összekötő mostantól tartományi szintű cookie-kat állít be az altartomány szintjén. Ez zökkenőmentes SSO-élményt biztosít, és elkerüli a redundáns hitelesítési kéréseket.
- A darabolásos kódolási kérelmek támogatása
- Továbbfejlesztett összekötő állapotának figyelése 
- Számos hibajavítás és stabilitási javítás

További információ: az [Azure ad Application proxy-összekötők ismertetése](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors).
 
---

## <a name="february-2018"></a>2018. február
 
### <a name="improved-navigation-for-managing-users-and-groups"></a>Továbbfejlesztett Navigálás felhasználók és csoportok kezeléséhez

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Címtár-kezelés  
A **termék képességei:** Directory

A felhasználók és csoportok kezelésének navigációs felülete egyszerűbbé válik. Most már közvetlenül a címtár áttekintésében is megtekintheti az összes felhasználó listáját, és könnyebben hozzáférhet a törölt felhasználók listájához. A címtár áttekintését is megtekintheti közvetlenül az összes csoport listájára, és könnyebben hozzáférhet a csoport felügyeleti beállításaihoz. Továbbá a címtár – áttekintés oldalon megkeresheti a felhasználók, csoportok, vállalati alkalmazások vagy alkalmazások regisztrációját. 

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>A bejelentkezések és a naplózási jelentések rendelkezésre állása Microsoft Azure által üzemeltetett 21Vianet (Azure China 21Vianet)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure Stack  
A **termék képességei:** & Jelentéskészítés figyelése

Az Azure AD-tevékenységek naplózási jelentései mostantól a 21Vianet (Azure China 21Vianet)-példányok által üzemeltetett Microsoft Azureban érhetők el. A következő naplók tartoznak ide:

- **Bejelentkezési tevékenységek naplói** – a bérlőhöz társított összes bejelentkezési naplót tartalmazza.

- **Önkiszolgáló jelszó-naplózási naplók** – tartalmazza az összes SSPR-naplót.

- **Címtár-felügyeleti naplók** – tartalmazza a címtár-felügyelettel kapcsolatos naplókat, például a felhasználók felügyeletét, az alkalmazások felügyeletét és egyebeket.

A naplók segítségével betekintést nyerhet a környezetével. A megadott adatokkal a következőket teheti:

- Határozza meg, hogy a felhasználók hogyan használják az alkalmazásokat és szolgáltatásokat.

- Hibák elhárítása a felhasználók munkavégzésének megakadályozása érdekében.

További információ a jelentések használatáról: [Azure Active Directory jelentéskészítés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal).

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>Az Azure AD-tevékenységek jelentéseinek megtekintése a "jelentéskészítő olvasó" szerepkör (nem rendszergazdai szerepkör) használatával

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & Jelentéskészítés figyelése

Az ügyfelek visszajelzései részeként, hogy a nem rendszergazdai szerepkörök hozzáférjenek az Azure AD-tevékenység naplóihoz, engedélyezte, hogy a "jelentéskészítő" szerepkörbe tartozó felhasználók hozzáférjenek a bejelentkezésekhez és a naplózási tevékenységekhez a Azure Portalon belül, valamint a Graph API-k használatával. 

További információ a jelentések használatáról: [Azure Active Directory jelentéskészítés](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal). 

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>Az Alkalmazottkód-jogcím felhasználói attribútumként és felhasználói azonosítóként érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Az **AlkalmazottKód** -t felhasználói azonosítóként és felhasználói attribútumként is konfigurálhatja a felhasználók és a vállalatközi vendégek számára az SAML-alapú bejelentkezési alkalmazásokban a vállalati alkalmazás felhasználói felületén.

További információ: [az SAML-jogkivonatban kiadott jogcímek testreszabása Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>Egyszerűsített alkalmazás-kezelés helyettesítő karakterek használatával az Azure AD Application Proxy

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Felhasználói hitelesítés
 
Az alkalmazások központi telepítésének egyszerűbbé tétele és a felügyeleti terhelés csökkentése érdekében mostantól támogatjuk a helyettesítő karaktereket használó alkalmazások közzétételének lehetőségét. Helyettesítő karakteres alkalmazás közzétételéhez kövesse a normál alkalmazás-közzétételi folyamatot, de használjon helyettesítő karaktert a belső és külső URL-címekben.

További információ: [helyettesítő alkalmazások a Azure Active Directory alkalmazásproxy](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>Az alkalmazásproxy konfigurálását támogató új parancsmagok

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Platform

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Platform

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
A **termék képességei:** Platform
 
Azure AD Connect az Azure AD és a helyszíni adatforrások közötti adatszinkronizálás előnyben részesített eszköze, beleértve a Windows Server Active Directoryt és az LDAP-t.

>[!Important]
>Ez a Build bevezeti a séma-és szinkronizálási szabályok változásait. A Azure AD Connect synchronization szolgáltatás a frissítés után teljes importálási és teljes szinkronizálási lépéseket indít el. További információ a viselkedés módosításáról: a [teljes szinkronizálás késleltetése a frissítés után](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade).

Ez a kiadás a következő frissítésekkel és módosításokkal rendelkezik:

**Kijavított problémák**

- A következő lapra váltáskor javítsa ki az időzítési időszakot a particionálási feladatok oldalának háttérben végzett feladatainál.

- Kijavítva egy olyan hibát, amely a configdb elemre egyéni művelet során hozzáférési szabálysértést okozott.

- Kijavított egy hibát az SQL-kapcsolat időtúllépése miatti helyreállításhoz.

- Kijavítva a hiba, ha a SAN-helyettesítő karakterekkel rendelkező tanúsítványok nem rendelkeznek előre-REQ ellenőrzéssel.

- Kijavítva egy hiba, amely a MIIServer. exe összeomlását okozza a HRE-összekötő exportálásakor.

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

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

További alkalmazásokat is felvettünk, amelyek támogatják az alkalmazás-alapú feltételes hozzáférést. Mostantól hozzáférhet az Office 365-hez és más Azure AD-hez kapcsolódó felhőalapú alkalmazásokhoz is a jóváhagyott ügyfélalkalmazások használatával.

Február végére a következő alkalmazások lesznek hozzáadva:

- Microsoft Power BI

- Microsoft Launcher

- Microsoft-számlázás

További információ eléréséhez lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>A Mobile Experience Használati feltételek frissítése 

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának

Ha megjelenik a használati feltételek, akkor a problémák megtekintése lehetőségre kattinthat **? Kattintson ide**. Erre a hivatkozásra kattintva a használati feltételek natív módon megnyitható az eszközön. A dokumentumban lévő betűmérettől vagy az eszköz képernyőjének méretétől függetlenül szükség szerint nagyíthatja és elolvashatja a dokumentumot. 

---
 
## <a name="january-2018"></a>2018. január
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Január 2018-án a következő új alkalmazások lettek hozzáadva az összevonási támogatással az alkalmazás-gyűjteményben:

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698), [OneTrust adatvédelem-kezelő szoftver](https://go.microsoft.com/fwlink/?linkid=861660), [Dealpath](https://go.microsoft.com/fwlink/?linkid=863526), [IriusRisk összevont könyvtár és [hűség NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701).

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 

---
 
### <a name="sign-in-with-additional-risk-detected"></a>Bejelentkezés további észlelt kockázattal

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme

Az észlelt kockázatok észlelésével kapcsolatos betekintés az Azure AD-előfizetéshez van kötve. A prémium szintű Azure AD P2 kiadással a legrészletesebb információkat kapja meg az összes mögöttes észlelésről.

A prémium szintű Azure AD P1 kiadással a licenc által nem érintett észlelések nem jelennek meg, mivel a kockázatkezelési szolgáltatás a további kockázatok észlelésével jelentkezik.

További információ: [Azure Active Directory kockázati észlelések](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events).
 
---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>Office 365-alkalmazások elrejtése a végfelhasználó hozzáférési panelei között

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO

Mostantól hatékonyabban kezelheti, hogy az Office 365-alkalmazások hogyan jelenjenek meg a felhasználó hozzáférési panelén egy új felhasználói beállítással. Ez a beállítás hasznos lehet a felhasználók hozzáférési panelén lévő alkalmazások számának csökkentésében, ha csak az Office-alkalmazásokat szeretné megjeleníteni az Office-portálon. A beállítás a **felhasználói beállítások** területen található, és a **felhasználók csak az Office 365-alkalmazásokat látják az Office 365-portálon**.

További információ: [alkalmazás elrejtése a felhasználói felületről Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app).

---
 
### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>Az alkalmazás URL-címéről való zökkenőmentes bejelentkezés a jelszó-SSO-hoz 

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** SSO

A saját alkalmazások böngésző bővítménye mostantól egy kényelmes eszközön keresztül érhető el, amely a saját alkalmazások egyszeri bejelentkezés lehetőségét kínálja a böngészőben. A telepítés után a felhasználó egy Waffle ikont fog látni a böngészőben, amely gyors hozzáférést biztosít az alkalmazásokhoz. A felhasználók mostantól kihasználhatják a következőket:

- A jelszó-SSO-alapú alkalmazásokhoz való közvetlen bejelentkezés lehetősége az alkalmazás bejelentkezési oldaláról
- Bármely alkalmazás elindítása a gyors keresési funkció használatával
- A legutóbb használt alkalmazásokra mutató parancsikonok a bővítményből
- A bővítmény a Microsoft Edge, a Chrome és a Firefox számára érhető el.
 
További információt a [saját alkalmazások biztonságos bejelentkezési bővítménnyel](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)foglalkozó témakörben talál.

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>A klasszikus Azure portál Azure AD felügyeleti felülete megszűnt

**Írja be a következőt:** Elavult   
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Directory

2018. január 8-án a klasszikus Azure portálon elérhető Azure AD felügyeleti élmény megszűnt. Ez a klasszikus Azure-portál kivonulásával együtt zajlott. A későbbiekben az Azure ad [felügyeleti központot](https://aad.portal.azure.com) kell használnia az Azure ad portálon alapuló felügyeletéhez.
 
---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>A PhoneFactor webportál ki lett vonva.

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Directory
 
2018. január 8-án a PhoneFactor webportál kivonult. Ezt a portált használták az MFA-kiszolgáló felügyeletéhez, de ezek a függvények át lettek helyezve a Azure Portalba a következő címen: portal.azure.com. 

Az MFA-konfiguráció a következő helyen található: **Azure Active Directory \> MFA-kiszolgáló**
 
---
 
### <a name="deprecate-azure-ad-reports"></a>Azure AD-jelentések elavultak

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-életciklus kezelése  


Az új Azure Active Directory felügyeleti konzol és az új API-k általánosan elérhetővé válnak a tevékenység-és biztonsági jelentésekhez, a "/reports" végpont alatt a jelentés API-jai a 2017-as végén megszűntek.

**Mi érhető el?**

Az új felügyeleti konzolra való áttérés részeként 2 új API-t tettünk elérhetővé az Azure AD-tevékenységek naplófájljainak beolvasásához. Az új API-k szélesebb körű szűrési és rendezési funkciókat biztosítanak, továbbá gazdagabb naplózási és bejelentkezési tevékenységeket is biztosítanak. A biztonsági jelentésekben korábban elérhető adatok mostantól az Identity Protection kockázati észlelések API-n keresztül érhetők el Microsoft Graphban.

További információ eléréséhez lásd:

- [Ismerkedés a Azure Active Directory Reporting API-val](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [A Azure Active Directory Identity Protection és a Microsoft Graph első lépései](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>December 2017

### <a name="terms-of-use-in-the-access-panel"></a>Használati feltételek a hozzáférési panelen

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 
Ezután megnyithatja a hozzáférési panelt, és megtekintheti a korábban elfogadott használati feltételeket.

Kövesse az alábbi lépéseket:

1. Lépjen a [MyApps portálra](https://myapps.microsoft.com), és jelentkezzen be.

2. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a listából. 

3. A **profilban**válassza a **használati feltételek áttekintése**elemet. 

4. Most már áttekintheti az elfogadott használati feltételeket. 

További információkért lásd az [Azure ad használati feltételeinek (előzetes verzió) funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---
 
### <a name="new-azure-ad-sign-in-experience"></a>Az Azure AD új bejelentkezési felülete

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD és a Microsoft-fiók Identity rendszer felülete úgy lett újratervezve, hogy egységes megjelenést és működést biztosítson. Emellett az Azure AD bejelentkezési oldala először a felhasználónevet gyűjti, amelyet a hitelesítő adatok követnek egy második képernyőn.

További információ: [Az Azure ad új bejelentkezési felülete már nyilvános előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)verzióban érhető el.
 
---
 
### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>Kevesebb bejelentkezési kérés: az Azure AD-bejelentkezéshez új "bejelentkezett"

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Felhasználói hitelesítés
 
Az Azure AD bejelentkezési oldalának **bejelentkezett állapotban** jelölőnégyzete egy új, a sikeres hitelesítés után megjelenő üzenettel lett lecserélve. 

Ha **igennel** válaszol erre a kérésre, a szolgáltatás állandó frissítési jogkivonatot biztosít. Ez a viselkedés ugyanaz, mint amikor bejelölte a a **bejelentkezve** szeretnék maradni jelölőnégyzetet a régi felületén. Összevont bérlők esetében ez a kérdés az összevont szolgáltatás sikeres hitelesítését követően jelenik meg.

További információ [: kevesebb bejelentkezési kérés: az Azure ad-ben az új "bejelentkezve tartom" élmény előzetes](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)verzióban érhető el. 

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>Konfiguráció hozzáadása a használati feltételek kibontásának megköveteléséhez az elfogadás előtt

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának
 
A rendszergazdák számára a feltételek elfogadását megelőzően a felhasználóknak a használati feltételek kibontására van szükségük.

Válassza ki **a** be vagy **ki** lehetőséget, hogy a felhasználók kiterjesszék a használati feltételeket. A **on** beállítás megadásához a felhasználóknak meg kell tekinteniük a használati feltételeket az elfogadásuk előtt.

További információkért lásd az [Azure ad használati feltételeinek (előzetes verzió) funkcióját](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).
 
---

### <a name="scoped-activation-for-eligible-role-assignments"></a>Jogosult szerepkör-hozzárendelések hatókörön belüli aktiválása

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
A hatókörön belüli aktiválással aktiválhatja a jogosult Azure-erőforrás-szerepkör-hozzárendeléseket, és az eredeti hozzárendelés alapértelmezett értékeinél kevesebb autonómiát is használhat. Ilyen például, ha a bérlőben egy előfizetés tulajdonosaként van hozzárendelve. A hatókörön belüli aktiválással a tulajdonosi szerepkört akár öt, az előfizetésben található erőforráshoz (például erőforráscsoportok és virtuális gépek) is aktiválhatja. Az aktiválás hatóköre csökkentheti a kritikus Azure-erőforrások nemkívánatos módosításainak lehetőségét.

További információ: [Mi az Azure ad Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)
 
---
 
### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Új összevont alkalmazások az Azure AD App Galleryben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2017 decemberében a következő új alkalmazásokat adtuk hozzá az alkalmazás-galériában az összevonási támogatással:

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523), Adobe Experience Manager, [EFI digitális kirakat](https://go.microsoft.com/fwlink/?linkid=861685), [Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe, [FactSet](https://go.microsoft.com/fwlink/?linkid=863525), [rendszerkép működik](https://go.microsoft.com/fwlink/?linkid=863517), [mobi](https://go.microsoft.com/fwlink/?linkid=863521), [MobileIron Azure ad-integráció](https://go.microsoft.com/fwlink/?linkid=858027), [Reflektive](https://go.microsoft.com/fwlink/?linkid=863518), [SAML SSO a Bamboo by Solution GmbH](https://go.microsoft.com/fwlink/?linkid=863520), [SAML SSO a bitbucket-hez](https://go.microsoft.com/fwlink/?linkid=863519), [Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522), WebHR, Zenegy Azure ad-integrációhoz.

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS-alkalmazások integrálása Azure Active Directoryokkal](https://aka.ms/appstutorial).

Az alkalmazás Azure AD-katalógusban való listázásával kapcsolatos további információkért tekintse [meg az alkalmazás listázása a Azure Active Directory Application Galleryben](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)című témakört. 
 
---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD-címtárbeli szerepkörök jóváhagyási munkafolyamatai

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Az Azure AD-címtár szerepköreinek jóváhagyási munkafolyamata általánosan elérhető.

A jóváhagyási munkafolyamatok, a Kiemelt szerepkörű rendszergazdák megkövetelhetik a jogosult szerepkör-tagokat a szerepkör-aktiválás kérelmezéséhez, mielőtt használhatják a Kiemelt szerepkört. Több felhasználó és csoport is delegálható jóváhagyási felelősséggel. A jogosult szerepkör tagjai értesítést kapnak, ha a jóváhagyás elkészült, és a szerepkörük aktív.

---
 
### <a name="pass-through-authentication-skype-for-business-support"></a>Átmenő hitelesítés: Skype vállalati verzió támogatása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés

Az átmenő hitelesítés mostantól támogatja a felhasználói bejelentkezéseket a Skype vállalati ügyfélalkalmazások számára, amelyek támogatják a modern hitelesítést, beleértve az online és a hibrid topológiákat is. 

További információ: [a Skype for Business-topológiák, amelyek modern hitelesítéssel támogatottak](https://technet.microsoft.com/library/mt803262.aspx).
 
---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>Az Azure RBAC Azure AD Privileged Identity Management frissítései (előzetes verzió)

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Az Azure szerepköralapú Access Control (RBAC) Azure AD Privileged Identity Management (PIM) nyilvános előzetes frissítésével mostantól a következőket teheti:

* Csak elég felügyeletet használjon.
* Az erőforrás-szerepkörök aktiválásához jóváhagyás szükséges.
* Ütemezzen egy olyan szerepkör jövőbeli aktiválását, amely az Azure AD és az Azure RBAC szerepköreinek jóváhagyását igényli.
 
További információ: [Privileged Identity Management for Azure Resources (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---
 
## <a name="november-2017"></a>November 2017
 
### <a name="access-control-service-retirement"></a>Access Control szolgáltatás nyugdíjazása

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Access Control szolgáltatás  
A **termék képességei:** Access Control szolgáltatás 

A Azure Active Directory Access Control (más néven Access Control szolgáltatás) a 2018-es későn megszűnik. A következő hetekben további információkat talál a részletes ütemtervről és a magas szintű áttelepítési útmutatóról. A lap megjegyzéseit a Access Control szolgáltatással kapcsolatos bármilyen kérdéssel elhagyhatja, és a csapattagok is megválaszolják őket.

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>Böngésző hozzáférésének korlátozása a Intune Managed Browserhoz 

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A böngésző hozzáférését korlátozhatja az Office 365-hez és más Azure AD-hez kapcsolódó felhőalapú alkalmazásokhoz a Intune Managed Browser jóváhagyott alkalmazásként való használatával. 

Mostantól a következő feltételt állíthatja be az alkalmazás alapú feltételes hozzáféréshez:

**Ügyfélalkalmazások:** Böngésző

**Mi a változás hatása?**

Jelenleg a hozzáférés le van tiltva, amikor ezt a feltételt használja. Ha az előzetes verzió elérhető, az összes hozzáféréshez a Managed Browser alkalmazást kell használnia. 

Keresse meg ezt a képességet, és további információkat a közelgő blogokban és a kibocsátási megjegyzésekben. 

További információ: [feltételes hozzáférés az Azure ad-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások szerepelnek a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)listáján:

- [Microsoft-Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

További információ eléréséhez lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>Használati feltételek támogatása több nyelvhez

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának

A rendszergazdák mostantól létrehozhatnak olyan új használati feltételeket, amelyek több PDF-dokumentumot tartalmaznak. Ezeket a PDF-dokumentumokat a megfelelő nyelven címkézheti. A felhasználók a megfelelő nyelven jelennek meg a PDF-ben a beállítások alapján. Ha nincs egyezés, az alapértelmezett nyelv jelenik meg.

---
 
### <a name="real-time-password-writeback-client-status"></a>Valós idejű jelszó visszaírási-ügyfél állapota

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszó-visszaállítás  
A **termék képességei:** Felhasználói hitelesítés

Most már áttekintheti a helyszíni jelszó visszaírási-ügyfelének állapotát. Ez a beállítás a [jelszó-visszaállítási](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset) oldal helyszíni **integráció** szakaszában érhető el. 

Ha problémák merülnek fel a helyszíni visszaírási-ügyféllel létesített kapcsolataival kapcsolatban, a következő hibaüzenet jelenik meg:

- Információ arról, hogy miért nem tud csatlakozni a helyszíni visszaírási-ügyfélhez.
- A probléma megoldásához segítséget nyújtó dokumentációra mutató hivatkozás. 

További információ: helyszíni [integráció](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration).

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD-alkalmazás-alapú feltételes hozzáférés 
 
**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD  
A **termék képességei:** Identitás biztonsága és védelme

Mostantól az [Azure ad-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)használatával korlátozhatja az Office 365 és más Azure ad-hez kapcsolódó felhőalapú alkalmazások elérését az Intune app Protection-szabályzatokat támogató [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement) számára. Az Intune app Protection-szabályzatok az ügyfélalkalmazások vállalati adatainak konfigurálására és védelmére szolgálnak.

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

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Identitás biztonsága és védelme

Az Azure AD-hez csatlakoztatott összes eszközt és az eszközhöz kapcsolódó tevékenységeket egy helyen találja meg. A Azure Portal minden eszköz-identitását és beállítását új adminisztrációs megoldással felügyelheti. Ebben a kiadásban a következőket teheti:

- Az Azure AD-ben feltételes hozzáféréshez elérhető összes eszköz megtekintése.
- Megtekintheti a hibrid Azure AD-hez csatlakoztatott eszközöket tartalmazó tulajdonságokat.
- Megkeresheti az Azure AD-hez csatlakoztatott eszközök BitLocker-kulcsait, kezelheti az eszközt az Intune-nal, és így tovább.
- Az Azure AD-eszközökhöz kapcsolódó beállítások kezelése.

További információ: [eszközök kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>Támogatás macOS platformként az Azure AD feltételes hozzáféréshez 

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme 

Mostantól belefoglalhatja (vagy kizárhatja) a macOS-t az Azure AD feltételes hozzáférési szabályzatában található eszköz-platform feltételként. A macOS és a támogatott eszközök platformjának hozzáadásával a következőket teheti:

- **MacOS-eszközök regisztrálása és kezelése az Intune használatával.** Hasonló más platformokhoz, mint például az iOS és az Android, a macOS-hez készült vállalati portál alkalmazás egyesített regisztrációkat tesz elérhetővé. A macOS-hez készült új céges portál alkalmazással regisztrálhat egy eszközt az Intune-ban, és regisztrálhatja azt az Azure AD-ben.
- **Győződjön meg arról, hogy a macOS-eszközök megfelelnek a szervezetnek az Intune-ban definiált megfelelőségi szabályzatoknak.** A Azure Portal Intune-ban mostantól beállíthatja a macOS-eszközökre vonatkozó megfelelőségi szabályzatokat. 
- **Az Azure AD-alkalmazásokhoz való hozzáférés korlátozása csak a megfelelő macOS-eszközökre.** A feltételes hozzáférési szabályzat létrehozásához a macOS-t külön eszköz platformként kell megválasztani. Most már készíthet macOS-specifikus feltételes hozzáférési szabályzatokat az Azure-beli megcélzott alkalmazás számára.

További információ eléréséhez lásd:

- [Megfelelőségi szabályzat létrehozása MacOS-eszközökhöz az Intune-nal](https://aka.ms/macoscompliancepolicy)
- [Feltételes hozzáférés az Azure AD-ben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)
 
---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>Hálózati házirend-kiszolgáló bővítmény az Azure Multi-Factor Authentication 

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:**  Multi-Factor Authentication  
A **termék képességei:** Felhasználói hitelesítés

Az Azure Multi-Factor Authenticationhez készült hálózati házirend-kiszolgáló bővítmény felhőalapú Multi-Factor Authentication képességeket biztosít a hitelesítési infrastruktúrához a meglévő kiszolgálók használatával. A hálózati házirend-kiszolgáló bővítmény használatával telefonhívást, szöveges üzenetet vagy telefonos alkalmazás-ellenőrzést adhat meg a meglévő hitelesítési folyamathoz. Nem kell új kiszolgálókat telepítenie, konfigurálnia és karbantartani. 

Ez a bővítmény olyan szervezetek számára lett létrehozva, amelyek az Azure-Multi-Factor Authentication-kiszolgáló telepítése nélkül szeretnék védelemmel ellátni a virtuális magánhálózati kapcsolatokat. A hálózati házirend-kiszolgáló bővítmény adapterként működik a RADIUS és a felhőalapú Azure Multi-Factor Authentication között, hogy az összevont vagy szinkronizált felhasználók hitelesítésének második tényezője legyen.

További információ: [a meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure multi-Factor Authentication](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension).
 
---

### <a name="restore-or-permanently-remove-deleted-users"></a>Törölt felhasználók visszaállítása vagy végleges eltávolítása

**Írja be a következőt:** Új funkció    
**Szolgáltatás kategóriája:** Felhasználói felügyelet  
A **termék képességei:** Directory 

Az Azure AD felügyeleti központban mostantól a következőket végezheti el:

- Törölt felhasználó visszaállítása. 
- Felhasználó végleges törlése.

**A kipróbáláshoz:**

1. Az Azure AD felügyeleti központban válassza a **kezelés** szakaszban az [összes felhasználó](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All) lehetőséget. 

2. A **Megjelenítés** listából válassza a **nemrég törölt felhasználók**lehetőséget. 

3. Válasszon ki egy vagy több nemrég törölt felhasználót, majd állítsa vissza, vagy véglegesen törölje őket.
 
---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Új jóváhagyott ügyfélalkalmazások az Azure AD alkalmazás-alapú feltételes hozzáféréshez
 
**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme

A következő alkalmazások lettek hozzáadva a [jóváhagyott ügyfélalkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)listájához:

- Microsoft Planner
- Azure Information Protection 

További információ eléréséhez lásd:

- [Jóváhagyott ügyfélalkalmazás-követelmény](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference#approved-client-app-requirement)
- [Azure AD-alkalmazás-alapú feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzatban található vezérlők közötti "vagy" használata 

**Írja be a következőt:** Módosított funkció    
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás biztonsága és védelme
 
Most már használhatja a "vagy a" (a kijelölt vezérlők egyikének megkövetelése) használatát a feltételes hozzáférés-vezérléshez. Ezzel a funkcióval házirendek hozhatók létre a hozzáférés-vezérlések között "vagy". Ezzel a szolgáltatással például létrehozhat egy olyan házirendet, amely megköveteli, hogy a felhasználó bejelentkezzen a Multi-Factor Authentication "vagy" utasítással kompatibilis eszközre.

További információ: [vezérlők az Azure ad feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls).
 
---

### <a name="aggregation-of-real-time-risk-detections"></a>A valós idejű kockázati észlelések összesítése

**Írja be a következőt:** Módosított funkció    
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás biztonsága és védelme

Azure AD Identity Protection az összes olyan valós idejű kockázati észlelést, amely egy adott napon belül ugyanabból az IP-címről származik, az egyes kockázati észlelési típusok esetében összesítve lesz. Ez a változás korlátozza a kockázati észlelések mennyiségét, ami a felhasználói biztonság módosítása nélkül jelenik meg.

A mögöttes valós idejű észlelés minden alkalommal működik, amikor a felhasználó bejelentkezik. Ha van olyan biztonsági házirendje, amely Multi-Factor Authentication vagy letiltja a hozzáférést, az egyes kockázatos bejelentkezések során továbbra is aktiválódik.
 
---
 
## <a name="october-2017"></a>Október 2017

### <a name="deprecate-azure-ad-reports"></a>Azure AD-jelentések elavultak

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** Identitás-életciklus kezelése  

A Azure Portal a következőket biztosítja:

- Egy új Azure AD felügyeleti konzol.
- Új API-k tevékenység-és biztonsági jelentésekhez.
 
Az új képességek miatt a/Reports-végpont alatti jelentés API-k 2017. december 10-én megszűntek. 

---

### <a name="automatic-sign-in-field-detection"></a>Automatikus bejelentkezési mező észlelése

**Írja be a következőt:** Rögzített   
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Egyszeri bejelentkezés  

Az Azure AD támogatja az automatikus bejelentkezési mezők észlelését olyan alkalmazások esetében, amelyek HTML-felhasználónevet és jelszót tartalmazó mezőt jelenítenek meg. Ezek a lépések az [alkalmazások bejelentkezési mezőinek automatikus rögzítését ismertetik](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app). Ezt a képességet a [Azure Portal](https://aad.portal.azure.com) **vállalati alkalmazások** lapján található *nem* katalógusbeli alkalmazás hozzáadásával érheti el. Ezen felül az új alkalmazás **egyszeri bejelentkezési** módját **jelszó alapú egyszeri bejelentkezésre**is beállíthatja, MEGadhat egy webes URL-címet, majd mentheti a lapot.
 
Szolgáltatási probléma miatt ez a funkció átmenetileg le lett tiltva. A probléma megoldódott, és az automatikus bejelentkezési mező észlelése ismét elérhető.

---

### <a name="new-multi-factor-authentication-features"></a>Új Multi-Factor Authentication funkciók

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Multi-Factor Authentication  
A **termék képességei:** Identitás biztonsága és védelme  

A többtényezős hitelesítés (MFA) elengedhetetlen része a szervezet védelmének. Ahhoz, hogy a hitelesítő adatok jobban alkalmazkodnak és a tapasztalatok zökkenőmentesek legyenek, a következő funkciók lettek hozzáadva: 

- A multi-Factor Challenge eredményei közvetlenül integrálva vannak az Azure AD bejelentkezési jelentésbe, amely az MFA-eredmények programozott elérését is magában foglalja.
- Az MFA-konfiguráció mélyebben integrálva van az Azure AD konfigurációs felületére a Azure Portalban.

Ebben a nyilvános előzetes verzióban az MFA-felügyelet és a jelentéskészítés az Azure AD-konfiguráció alapvető felhasználói felületének szerves részét képezi. Most már kezelheti az MFA felügyeleti portál funkcióit az Azure AD-ben.

További információ: [az MFA-jelentéskészítés referenciája a Azure Portalban](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa). 

---

### <a name="terms-of-use"></a>Használati feltételek

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
A **termék képességei:** Betartásának  

Használhatja az Azure AD használati feltételeit, amelyek olyan információkat mutatnak be, mint például a felhasználókra vonatkozó jogi vagy megfelelőségi követelményekhez kapcsolódó jogcímek.

Az Azure AD használati feltételeit a következő esetekben használhatja:

- Általános használati feltételek a szervezet összes felhasználója számára
- Meghatározott használati feltételek egy felhasználó attribútumai (például orvosok, ápolók vagy belföldi és nemzetközi alkalmazottak, dinamikus csoportok által végzett) alapján
- Bizonyos használati feltételek a nagy hatású üzleti alkalmazások, például a Salesforce eléréséhez

További információ: az [Azure ad használati feltételei](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

### <a name="enhancements-to-privileged-identity-management"></a>A Privileged Identity Management fejlesztései

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management  

A Azure AD Privileged Identity Management segítségével a szervezeten belül felügyelheti, vezérelheti és figyelheti az Azure-erőforrásokhoz való hozzáférést (előzetes verzió):

- Előfizetések
- Erőforráscsoportok
- Virtual machines (Virtuális gépek) 

Az Azure RBAC funkciót használó Azure Portal összes erőforrása kihasználhatja a Azure AD Privileged Identity Management által kínált biztonsági és életciklus-kezelési képességek előnyeit.

További információ: [Privileged Identity Management Azure-erőforrásokhoz](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

### <a name="access-reviews"></a>Hozzáférési felülvizsgálatok

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Betartásának  

A szervezetek a hozzáférési felülvizsgálatok (előzetes verzió) segítségével hatékonyan kezelhetik a csoporttagságok és a vállalati alkalmazásokhoz való hozzáférést: 

- Az alkalmazásokhoz való hozzáférések és a csoporttagságok felülvizsgálatával újrahitelesítheti a vendégfelhasználói hozzáférést. A felülvizsgálók hatékonyan dönthetnek arról, hogy a vendégek továbbra is hozzáférhetnek-e a hozzáférési felülvizsgálatok által biztosított megállapítások alapján.
- A hozzáférési felülvizsgálatok segítségével az alkalmazottak alkalmazás-hozzáférését és csoporttagságait is újból hitelesítheti.

A hozzáférési felülvizsgálati vezérlőket cég- vagy szervezetspecifikus programokban is összegyűjtheti a megfelelőség vagy a kockázatérzékeny alkalmazások felülvizsgálatának nyomon követése céljából.

További információ: [Azure ad hozzáférési felülvizsgálatok](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview).

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>Harmadik féltől származó alkalmazások elrejtése az alkalmazásokból és az Office 365 app launcherből

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Saját alkalmazások  
A **termék képességei:** Egyszeri bejelentkezés  

Mostantól hatékonyabban kezelheti a felhasználói portálon megjelenő alkalmazásokat egy új **Hide app** -tulajdonság használatával. Az alkalmazások elrejtésével olyan esetekben lehet segíteni, amikor az alkalmazás csempéi megjelennek a háttér-szolgáltatásokhoz, illetve az ismétlődő csempék és a felhasználói alkalmazások elindításához. A váltógomb a harmadik féltől származó alkalmazás **Tulajdonságok** részében található, és **látható a felhasználó számára?** Egy alkalmazást programozott módon is el lehet rejteni a PowerShell használatával. 

További információ: [harmadik féltől származó alkalmazás elrejtése a felhasználó Azure ad-beli felhasználói felületéről](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app). 


**Mi érhető el?**

 Az új felügyeleti konzolra való áttérés részeként két új API érhető el az Azure AD-tevékenységek naplófájljainak beolvasásához. Az új API-k szélesebb körű szűrési és rendezési funkciókat biztosítanak, továbbá gazdagabb naplózási és bejelentkezési tevékenységeket is biztosítanak. A biztonsági jelentésekben korábban elérhető adatok mostantól az Identity Protection kockázati észlelési API-n keresztül érhetők el Microsoft Graphban.


## <a name="september-2017"></a>Szeptember 2017

### <a name="hotfix-for-identity-manager"></a>Az Identity Manager gyorsjavítása

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése  

A 4.4.1642.0-alapú gyorsjavítások összesítő csomagja (Build) 2017. szeptember 25-én érhető el az Identity Manager 2016 Service Pack 1 verzióhoz. Ez az összesítő csomag:

- Elhárítja a problémákat, és javítja a javítást.
- A egy összegző frissítés, amely az összes Identity Manager 2016 Service Pack 1 frissítést felváltja az Identity Manager 2016 4.4.1459.0 létrehozásához. 
- Az Identity Manager 2016 Build 4.4.1302.0 szükséges. 

További információ: a [gyorsjavítások kumulatív csomagja (Build 4.4.1642.0) elérhető a Identity Manager 2016 Service Pack 1 verzióhoz](https://support.microsoft.com/help/4021562). 

---
