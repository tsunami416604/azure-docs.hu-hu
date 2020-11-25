---
title: Újdonságok Kibocsátási megjegyzések – Azure Active Directory | Microsoft Docs
description: Ismerje meg a Azure Active Directory újdonságait; ilyenek például a legújabb kibocsátási megjegyzések, ismert problémák, hibajavítások, elavult funkciók és a közelgő változások.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 10/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71726724e7c018f34b1175f323d0c8e55b604931
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95973634"
---
# <a name="whats-new-in-azure-active-directory"></a>A Azure Active Directory újdonságai

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL-cím másolásával és beillesztésével: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` az ![ RSS-hírcsatorna olvasójának ikonját a ](./media/whats-new/feed-icon-16x16.png) hírcsatorna-olvasóba.

Az Azure AD folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- A változtatások tervei

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia. Ha hat hónapnál régebbi elemeket keres, akkor az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---
## <a name="october-2020"></a>2020. október

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-tls-certificate-changes"></a>Az Azure AD helyszíni hibrid ügynökök által érintett Azure TLS-tanúsítványok változásai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Platform

A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Ezt a frissítést az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok jelentik, amelyek nem felelnek meg a CA/Browser fórum alapkövetelményének. Ez a változás hatással lesz a helyileg telepített Azure AD hibrid ügynökökre, amelyek megerősített környezettel rendelkeznek a főtanúsítványok rögzített listájával, és frissíteni kell az új tanúsítvány-kiállítók megbízhatóságára.

Ez a változás a szolgáltatás megszakadását eredményezi, ha nem azonnal intézkedik. Ezek az ügynökök többek között olyan [alkalmazásproxy-összekötőket](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) tartalmaznak, amelyek lehetővé teszik a helyszíni, [áteresztő hitelesítési](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökök számára, hogy a felhasználók ugyanazzal a jelszóval jelentkezzenek be az alkalmazásokba, és a [Felhőbeli kiépítési előnézet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökei az ad-t az Azure ad Sync szolgáltatáshoz használják. 

Ha a tűzfalszabályok olyan környezettel rendelkeznek, amely engedélyezi a kimenő hívások számára a visszavont tanúsítványok listájának (CRL) letöltését, akkor engedélyeznie kell a következő CRL-eket és az OCSP URL-címeket. A módosítással és a CRL-ekkel, valamint az OCSP URL-címekkel kapcsolatos részletes információkért lásd: az  [Azure TLS-tanúsítvány változásai](../../security/fundamentals/tls-certificate-changes.md).

---

### <a name="provisioning-events-will-be-removed-from-audit-logs-and-published-solely-to-provisioning-logs"></a>A kiépítési események el lesznek távolítva a naplókból, és kizárólag az üzembe helyezési naplók számára lettek közzétéve.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
A SCIM [kiépítési szolgáltatásának](../app-provisioning/user-provisioning.md) tevékenységeit a rendszer naplózza a naplók és a kiépítési naplók között is. Ez olyan tevékenységeket foglal magában, mint például a felhasználó létrehozása a ServiceNow-ben, a csoport GSuite vagy az AWS szerepkör importálása. A későbbiekben ezek az események csak a kiépítési naplókban lesznek közzétéve. Ennek a változásnak a megvalósítása folyamatban van, hogy elkerülje a naplók ismétlődő eseményeit, és a log Analyticsben a naplókat fogyasztó ügyfelek által felmerülő további költségeket. 

A dátum befejeződik után egy frissítést biztosítunk. Ez az elavultság nem a 2020-es naptári évre van tervezve. 

> [!NOTE]
> Ez nem befolyásolja a naplókban lévő eseményeket a kiépítési szolgáltatás által kibocsátott szinkronizálási eseményeken kívül. Az eseményeket, például egy alkalmazás létrehozását, a feltételes hozzáférési szabályzatot, a címtárban lévő felhasználót, stb. továbbra is a naplók lesznek kibocsátva a naplókban. [További információk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs?context=azure/active-directory/app-provisioning/context/app-provisioning-context).
 

---

### <a name="azure-ad-on-premises-hybrid-agents-impacted-by-azure-transport-layer-security-tls-certificate-changes"></a>Az Azure AD helyszíni hibrid ügynökeit érinti az Azure Transport Layer Security (TLS) tanúsítványának változásai

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Platform
 
A Microsoft frissíti az Azure-szolgáltatásokat, hogy TLS-tanúsítványokat használjanak egy másik legfelső szintű hitelesítésszolgáltatóktól (CA). Az aktuális HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok nem a HITELESÍTÉSSZOLGÁLTATÓ/böngésző alapkonfigurációjának egyik követelményét követve frissülni fognak. Ez a változás hatással lesz a helyileg telepített Azure AD hibrid ügynökökre, amelyek megerősített környezettel rendelkeznek a főtanúsítványok rögzített listájával. Ezeket az ügynököket frissíteni kell, hogy megbízzanak az új tanúsítvány-kiállítókkal.

Ez a változás a szolgáltatás megszakadását eredményezi, ha nem azonnal intézkedik. Ezek az ügynökök a következők: 
- [Alkalmazásproxy-összekötők](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AppProxy) a helyszíni távoli eléréshez 
- [Továbbító hitelesítési](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) ügynökök, amelyek lehetővé teszik a felhasználók számára, hogy ugyanazzal a jelszóval jelentkezzenek be az alkalmazásokba
- [Felhőalapú kiépítési előnézet](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AzureADConnect) -ügynökök, amelyek ad az Azure ad Sync szolgáltatáshoz. 

Ha a tűzfalszabályok olyan környezettel rendelkeznek, amely engedélyezi a kimenő hívások számára a visszavont tanúsítványok listájának (CRL) letöltését, engedélyeznie kell a CRL-eket és az OCSP URL-címeket. A módosítással és a CRL-ekkel, valamint az OCSP URL-címekkel kapcsolatos részletes információkért lásd: az  [Azure TLS-tanúsítvány változásai](../../security/fundamentals/tls-certificate-changes.md).
 
---

### <a name="azure-active-directory-tls-10-tls-11-and-3des-deprecation-in-us-gov-cloud"></a>Azure Active Directory TLS 1,0, TLS 1,1 és 3DES elavulttá US Gov-felhőben

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Szabványok
 
A Azure Active Directory a következő protokollok elavulttá válik a 2021. március 31-ig:
- TLS 1.0
- TLS 1.1
- 3DES titkosítási csomag (TLS_RSA_WITH_3DES_EDE_CBC_SHA)

Az összes ügyfél-kiszolgáló és böngésző-kiszolgáló kombinációnak a TLS 1,2 és a modern titkosítási csomagokat kell használnia az Azure-hoz, az Office 365-hoz és a Microsoft 365-szolgáltatásokhoz Azure Active Directoryhoz való biztonságos kapcsolódás fenntartásához.

Az érintett környezetek a következők:
- Azure-US Gov
- [Office 365 GCC High & DoD](https://docs.microsoft.com/microsoft-365/compliance/tls-1-2-in-office-365-gcc?view=o365-worldwide)
 
---

### <a name="assign-applications-to-roles-on-au-and-object-scope"></a>Alkalmazások kiosztása szerepkörökhöz az AU és az objektum hatókörében

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
Ez a funkció lehetővé teszi, hogy az alkalmazás (SPN) rendszergazdai szerepkörhöz rendeljen a felügyeleti egység hatókörén. További információért lásd: [hatókörrel rendelkező szerepkörök társítása egy felügyeleti egységhez](../roles/admin-units-assign-roles.md).

---

### <a name="now-you-can-disable-and-delete-guest-users-when-theyre-denied-access-to-a-resource"></a>Mostantól letilthatja és törölheti a vendég felhasználókat, amikor nem férhetnek hozzá az erőforrásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás
 
A Letiltás és törlés az Azure AD hozzáférési felülvizsgálatok speciális vezérlése, amely segít a szervezeteknek a csoportok és alkalmazások külső vendégeit jobban kezelni. Ha a vendégek megtagadják a hozzáférési felülvizsgálatot, a **Letiltás és törlés** funkció automatikusan letiltja a bejelentkezést 30 napig. 30 nap elteltével a rendszer teljesen eltávolítja a bérlőből.

További információ erről a szolgáltatásról: [külső identitások letiltása és törlése az Azure ad hozzáférési felülvizsgálatokkal (előzetes verzió)](../governance/access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews-preview).
 
---

### <a name="access-review-creators-can-add-custom-messages-in-emails-to-reviewers"></a>A hozzáférés-felülvizsgálati létrehozói egyéni üzeneteket adhatnak az e-mailekben a felülvizsgálók számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás
 
Az Azure AD hozzáférési felülvizsgálatok szolgáltatásban az értékeléseket létrehozó rendszergazdák mostantól egyéni üzenetet írhatnak a véleményezők számára. A felülvizsgálók látni fogják a kapott e-mailben szereplő üzenetet, amely a felülvizsgálat befejezését kéri. Ha többet szeretne megtudni a funkció használatáról, tekintse meg a [Speciális beállítások](../governance/create-access-review.md#advanced-settings) szakasz 6. lépését.

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---october-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – október 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Apple Business Manager](../saas-apps/apple-business-manager-provision-tutorial.md)
- [Apple School Manager](../saas-apps/apple-school-manager-provision-tutorial.md)
- [Code42](../saas-apps/code42-provisioning-tutorial.md)
- [AlertMedia](../saas-apps/alertmedia-provisioning-tutorial.md)
- [OpenText Directory Services](../saas-apps/open-text-directory-services-provisioning-tutorial.md)
- [Cinode](../saas-apps/cinode-provisioning-tutorial.md)
- [Globális Relay-identitás szinkronizálása](../saas-apps/global-relay-identity-sync-provisioning-tutorial.md)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.
 
---

### <a name="integration-assistant-for-azure-ad-b2c"></a>Azure AD B2C integrációs asszisztense

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Az Integration Assistant (előzetes verzió) felhasználói felülete mostantól Azure AD B2C Alkalmazásregisztrációk számára elérhető. Ez a megoldás segítséget nyújt az alkalmazás gyakori forgatókönyvekhez való konfigurálásában. További információ a [Microsoft Identity platform ajánlott eljárásairól és javaslatairól](../develop/identity-platform-integration-checklist.md).
 
---

### <a name="view-role-template-id-in-azure-portal-ui"></a>Szerepkör-sablon AZONOSÍTÓjának megtekintése Azure Portal felhasználói felületen

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure-szerepkörök  
A **termék képességei:** Access Control
 

Most már megtekintheti az egyes Azure AD-szerepkörökhöz tartozó sablon AZONOSÍTÓját a Azure Portal. Az Azure AD-ben válassza a kiválasztott szerepkör  **leírását** . 

Javasoljuk, hogy az ügyfelek a megjelenítendő név helyett a szerepkör-sablon azonosítóit használják a PowerShell-parancsfájlban és a kódban. A szerepkör-sablon azonosítója [directoryRoles](https://docs.microsoft.com/graph/api/resources/directoryrole?view=graph-rest-1.0) és [definíciós](https://docs.microsoft.com/graph/api/resources/unifiedroledefinition?view=graph-rest-beta) objektumokhoz használható. További információ a szerepkör-sablon azonosítói: [szerepkör-sablon azonosítói](../roles/permissions-reference.md#role-template-ids).

---

### <a name="api-connectors-for-azure-ad-b2c-sign-up-user-flows-is-now-in-public-preview"></a>A Azure AD B2C regisztrációs felhasználói folyamatok API-összekötői mostantól nyilvános előzetes verzióban érhetők el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 

Az API-összekötők most már használhatók a Azure Active Directory B2C. Az API-összekötők lehetővé teszik a webes API-k használatát a regisztrációs felhasználói folyamatok testre szabására és a külső felhőalapú rendszerekkel való integrációra. Az API-összekötők a következőket vehetik igénybe:

- Integrálás egyéni jóváhagyási munkafolyamatokkal
- Felhasználói bemeneti adatok érvényesítése
- Felhasználói attribútumok felülírása 
- Egyéni üzleti logika futtatása 

 További információkért tekintse meg az [API-összekötők használata a regisztrációs dokumentáció testreszabásához és bővítéséhez](../../active-directory-b2c/api-connectors-overview.md) című témakört.

---

### <a name="state-property-for-connected-organizations-in-entitlement-management"></a>A jogosultságok kezeléséhez tartozó csatlakoztatott szervezetek State tulajdonsága

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Címtárszolgáltatás- **képesség:** jogosultságok kezelése
 

 Az összes csatlakoztatott szervezet már rendelkezik egy "State" nevű további tulajdonsággal. Az állapot határozza meg, hogyan használja a rendszer a csatlakoztatott szervezetet az "összes konfigurált csatlakoztatott szervezetre" vonatkozó házirendekben. Az érték "konfigurálva" (ami azt jelenti, hogy a szervezet az "all" záradékot használó szabályzatok hatókörében van) vagy "javasolt" (ami azt jelenti, hogy a szervezet nincs a hatókörben).  

A csatlakoztatott szervezetek manuálisan létrehozott alapértelmezett beállítása a "configured". Eközben az automatikusan létrehozott (szabályzatok alapján létrehozott), amelyek lehetővé teszik bármely felhasználó számára, hogy hozzáférést kérjenek, alapértelmezés szerint "javasolt" lesz.  A 9 2020 szeptember előtt létrehozott csatlakoztatott szervezetek beállítása "konfigurálva" lesz. A rendszergazdák szükség szerint frissíthetik ezt a tulajdonságot. [További információk](../governance/entitlement-management-organization.md#managing-a-connected-organization-programmatically).
 

---

### <a name="azure-active-directory-external-identities-now-has-premium-advanced-security-settings-for-b2c"></a>Azure Active Directory külső identitások mostantól prémium szintű, speciális biztonsági beállításokkal rendelkeznek a B2C-hez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A kockázatalapú feltételes hozzáférés és a kockázatkezelési funkciók mostantól elérhetők [Azure ad B2Cban](../..//active-directory-b2c/conditional-access-identity-protection-overview.md). Ezekkel a speciális biztonsági funkciókkal az ügyfelek mostantól a következőket tehetik:
- Intelligens elemzéseket használhat a B2C-alkalmazások és a végfelhasználói fiókok kockázatának felméréséhez. Az észlelések közé tartoznak az atipikus utazások, a névtelen IP-címek, a kártevők által hivatkozott IP-címek és az Azure AD veszélyforrások felderítése. A portál és az API-alapú jelentések is elérhetők.
- Az adaptív hitelesítési házirendek a B2C-felhasználók számára történő konfigurálásával automatikusan kezeli a kockázatokat. Az alkalmazások fejlesztői és rendszergazdái a többtényezős hitelesítés (MFA) vagy a hozzáférés blokkolásának a felhasználói kockázati szinttől függően történő megkövetelésével csökkenthetik a valós idejű kockázatokat, és a hely, a csoport és az alkalmazás alapján további vezérlők is elérhetők.
- Integrálható Azure AD B2C felhasználói folyamatokkal és egyéni házirendekkel. A feltételek beindíthatók a Azure AD B2C beépített felhasználói folyamataiból, vagy beépíthető a B2C egyéni házirendjeibe. A B2C felhasználói folyamat más szempontjaihoz hasonlóan a végfelhasználói élmények is testreszabhatók. A Testreszabás a szervezet hang-, márka-és enyhítő alternatívái alapján történik.
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---october-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – október 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 októberében a következő 27 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Sentry](../saas-apps/sentry-tutorial.md), [darázs-termelékenység superapp](https://app.yellowmessenger.com/user/login), [ABBYY FlexiCapture Cloud](../saas-apps/abbyy-flexicapture-cloud-tutorial.md), [EAComposer](../saas-apps/eacomposer-tutorial.md), [Genesys Cloud integration for Azure](https://apps.mypurecloud.com/msteams-integration/), [Zone Technologies portál](https://portail.zonetechnologie.com/signin), [Beautiful.ai](../saas-apps/beautiful.ai-tutorial.md), [Datawiza Access Broker](https://console.datawiza.com/), [ZOKRI](https://app.zokri.com/), [CheckProof](../saas-apps/checkproof-tutorial.md), [Ecochallenge.org](https://events.ecochallenge.org/users/login), [atSpoke](http://atspoke.com/login), [kinevezési emlékeztető](https://app.appointmentreminder.co.nz/account/login), [felhő. piac](https://cloud.market/), [TravelPerk](../saas-apps/travelperk-tutorial.md), [üdvözlés](https://app.greetly.com/), [OrgVitality SSO} (.. /SaaS-apps/orgvitality-SSO-tutorial.MD), [web Cargo Air](../saas-apps/web-cargo-air-tutorial.md), [Loop flow CRM](../saas-apps/loop-flow-crm-tutorial.md), [Starmind](../saas-apps/starmind-tutorial.md), [Workstem](https://hrm.workstem.com/login), [kiskereskedelmi zipline](../saas-apps/retail-zipline-tutorial.md), [Hoxhunt](../saas-apps/hoxhunt-tutorial.md), [MEVISIO](../saas-apps/mevisio-tutorial.md), [Samsara](../saas-apps/samsara-tutorial.md), [Nimbus](../saas-apps/nimbus-tutorial.md), [Pulse Secure Virtual Traffic Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="provisioning-logs-can-now-be-streamed-to-log-analytics"></a>A naplók üzembe helyezése mostantól a log Analytics szolgáltatásba is továbbítható

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 

Tegye közzé a kiépítési naplókat a log Analytics szolgáltatásban a következő sorrendben:
- Több mint 30 napig tárolja a kiépítési naplókat
- Egyéni riasztások és értesítések definiálása
- Irányítópultok létrehozása a naplók megjelenítéséhez
- Összetett lekérdezések végrehajtása a naplók elemzéséhez 

A szolgáltatás használatának megismeréséhez lásd: a [kiépítés integrálása Azure monitor naplókkal](../app-provisioning/application-provisioning-log-analytics.md).
 
---

### <a name="provisioning-logs-can-now-be-viewed-by-application-owners"></a>A naplók kiépítési naplóit mostantól az alkalmazások tulajdonosai tekinthetik meg

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
Mostantól engedélyezheti, hogy az alkalmazások tulajdonosai nyomon kövessék a tevékenységeket a kiépítési szolgáltatással, és hibaelhárítást végezzenek anélkül, hogy emelt szintű szerepkört kellene benyújtaniuk, vagy szűk keresztmetszetet kellene [További információk](../reports-monitoring/concept-provisioning-logs.md).
 
---

### <a name="renaming-10-azure-active-directory-roles"></a>10 Azure Active Directory szerepkör átnevezése

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure-szerepkörök  
A **termék képességei:** Access Control
 
Néhány Azure Active Directory (AD) beépített szerepkör olyan névvel rendelkezik, amely különbözik a Microsoft 365 felügyeleti központban, az Azure AD-Portálon és a Microsoft Graphban megjelenő nevektől. Ez az inkonzisztencia problémákat okozhat az automatizált folyamatokban. Ezzel a frissítéssel a rendszer 10 szerepkör-nevet átnevez, hogy azok konzisztensek legyenek. Az új szerepkörök nevei a következő táblázatban láthatók:

![Új szerepkör-nevek táblázata](media/whats-new/azure-role.png)

---

### <a name="azure-ad-b2c-support-for-auth-code-flow-for-spas-using-msal-js-2x"></a>Azure AD B2C támogatás a MSAL JS 2. x használatával a fürdők hitelesítési kódjának folyamatához

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
A MSAL.js 2. x verziója mostantól támogatja az egyoldalas webalkalmazások (Gyógyfürdők) engedélyezési kódjának áramlását. A Azure AD B2C mostantól támogatja a SPA-alkalmazás típusának használatát a Azure Portal és a MSAL.js engedélyezési kód PKCE való használatát egyoldalas alkalmazásokhoz. Ez lehetővé teszi a fürdők számára, hogy a Azure AD B2C használatával megőrizze az SSO-t újabb böngészőkkel, és betartja a hitelesítési protokollok újabb javaslatait. Ismerkedjen meg az [egyoldalas alkalmazások (Spa) regisztrálásával Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-spa.md) oktatóanyagban.

---

### <a name="updates-to-remember-multi-factor-authentication-mfa-on-a-trusted-device-setting"></a>A (z) Multi-Factor Authentication (MFA) frissítési megjegyzése egy megbízható eszköz beállításainál

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 

Nemrég frissítettük a [Remember multi-Factor Authentication (MFA)](../authentication/howto-mfa-mfasettings.md#remember-multi-factor-authentication) szolgáltatást egy megbízható eszköz-szolgáltatáson a hitelesítés legfeljebb 365 napig való meghosszabbításához. A Azure Active Directory (Azure AD) prémium szintű licencek a [feltételes hozzáférés – bejelentkezési gyakorisági szabályzatot](../conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency) is használhatják, amely nagyobb rugalmasságot biztosít az újrahitelesítési beállításokhoz.

Az optimális felhasználói élmény érdekében javasoljuk, hogy a feltételes hozzáférés bejelentkezési gyakoriságának használatával terjessze ki a munkamenetek élettartamát a megbízható eszközökön, helyszíneken vagy alacsony kockázatú munkamenetekben a megbízható eszközre vonatkozó, a többtényezős hitelesítés helyett. Első lépésként tekintse át a [legújabb útmutatót az újrahitelesítés élményének optimalizálásához](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

---

## <a name="september-2020"></a>2020. szeptember

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – szeptember 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [A Webroot biztonsági ismertetése](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.
 
---
### <a name="cloud-provisioning-public-preview-refresh"></a>A Cloud kiépítés nyilvános előzetes verziójának frissítése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD-beli felhőalapú kiépítési **termék funkció:** identitás-életciklus kezelése
 
Azure AD Connect Cloud kiépítés nyilvános előzetes verziójának frissítése két fő fejlesztést fejlesztett ki az ügyfelek visszajelzései közül: 

- Attribútum-hozzárendelési élmény Azure Portal

    Ezzel a funkcióval a rendszergazdák leképezhetők a felhasználó-, csoport-vagy kapcsolattartási attribútumok az AD-ből az Azure AD-be a mai napon elérhető különböző leképezési típusok használatával. Az attribútum-hozzárendelés egy olyan szolgáltatás, amely a Active Directorytól a Azure Active Directoryig áramló attribútumok értékeinek egységesítésére szolgál. Megadhatja, hogy az attribútum értéke közvetlenül az AD-ből az Azure AD-ba kerüljön-e, vagy kifejezések használatával alakítsa át az attribútum értékeit a felhasználók kiépítés során. [További információ](../cloud-provisioning/how-to-attribute-mapping.md)

- Igény szerinti kiépítési vagy tesztelési felhasználói élmény

    Ha beállította a konfigurációt, érdemes lehet tesztelni, hogy a felhasználói átalakítás a várt módon működik-e, mielőtt az összes felhasználóra alkalmazná a hatókört. Igény szerinti kiépítés esetén a rendszergazdák megadhatják az AD-felhasználók megkülönböztető nevét (DN), és megtekinthetik, hogy az elvárt módon szinkronizálva vannak-e. Az igény szerinti kiépítés nagyszerű módot biztosít arra, hogy az attribútum-hozzárendelések a várt módon működjenek. [További információ](../cloud-provisioning/how-to-on-demand-provision.md)
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>BitLocker-helyreállítás naplózása az Azure AD-ben – nyilvános előzetes verzió

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Eszköz-hozzáférés kezelése  
A **termék képességei:** Eszközök életciklusának kezelése
 
Ha a rendszergazdák vagy a végfelhasználók beolvasják a BitLocker helyreállítási kulcs (oka) t, Azure Active Directory most létrehoz egy naplót, amely rögzíti a helyreállítási kulcs elérését. Ugyanez a naplózás részletesen ismerteti az eszközt, amelyhez a BitLocker-kulcs hozzá volt rendelve.

A végfelhasználók a fiókomon [keresztül érhetik el a helyreállítási kulcsaikat](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). A rendszergazdák a [BitLocker helyreállítási kulcs API-](/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta) n keresztül érhetik el a helyreállítási kulcsokat a bétaverzióban, vagy az Azure ad portálon keresztül. További információ: [BitLocker-kulcsok megtekintése vagy másolása az Azure ad-portálon](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>A Teams-eszközök rendszergazdai beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
A **termék képességei:** Access Control
 
A [Teams-eszközök rendszergazdai](../roles/permissions-reference.md#teams-devices-administrator) szerepkörrel rendelkező felhasználók kezelhetik a csapatok [által hitelesített eszközöket](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) a csapatok felügyeleti központjából. 

Ez a szerepkör lehetővé teszi, hogy a felhasználó egyetlen pillantással megtekintse az összes eszközt, és képes legyen az eszközök keresésére és szűrésére. A felhasználó megtekintheti az egyes eszközök részleteit is, beleértve a bejelentkezett fiókot, valamint az eszköz gyártmányát és modelljét. A felhasználó módosíthatja az eszköz beállításait, és frissítheti a szoftver verzióit. Ez a szerepkör nem biztosít engedélyeket a csapatok ellenőrzéséhez és az eszköz minőségének meghívásához.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>A címtár-objektumok speciális lekérdezési képességei

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** MS Graph  
A **termék képességei:** Fejlesztői élmény
 
Az Azure AD API-k címtár-objektumaihoz bevezetett új lekérdezési képességek mostantól elérhetők a 1.0-s végponton és a termelésre készen. A fejlesztők a szabványos OData operátorok segítségével megszámíthatják, megkereshetik, szűrhetik és rendezhetik a címtár-objektumokat és a kapcsolódó hivatkozásokat.

További információkért tekintse meg [a dokumentációt](https://aka.ms/BlogPostMezzoGA), és visszajelzést is küldhet erről a [rövid kérdőívről](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Nyilvános előzetes verzió: a feltételes hozzáférési szabályzatokat konfigurált bérlők folyamatos hozzáférésének kiértékelése

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Identitás-biztonsági & védelme
 
A folyamatos hozzáférés kiértékelése (CAE) mostantól elérhető nyilvános előzetes verzióban az Azure AD-bérlők számára feltételes hozzáférési szabályzatokkal. A CAE a kritikus fontosságú biztonsági eseményeket és szabályzatokat valós időben értékeli ki. Ide tartozik a fiókok letiltása, a jelszó alaphelyzetbe állítása és a hely módosítása. További információ: [folyamatos hozzáférés kiértékelése](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="public-preview-ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Nyilvános előzetes verzió: a jóváhagyási döntések javítása érdekében a hozzáférési csomagot kérő felhasználók további kérdéseket tehetnek fel

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
A rendszergazdák mostantól megkövetelhetik, hogy a hozzáférési csomagot kérő felhasználók további kérdéseket válaszoljon meg az Azure AD-jogosultságok felügyeletének saját hozzáférési portálján. A felhasználók válaszai ezután megjelennek a jóváhagyók számára, hogy könnyebben meglegyenek a hozzáférés-jóváhagyási döntések. További tudnivalókért tekintse meg [a további kérelmező adatainak összegyűjtése jóváhagyásra (előzetes verzió)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview)című témakört.
 
---

### <a name="public-preview-enhanced-user-management"></a>Nyilvános előzetes verzió: továbbfejlesztett felhasználói felügyelet

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói felügyelet  
A **termék képességei:** Felhasználói felügyelet
 

Az Azure AD-portál frissült, így könnyebben megtalálhatja a felhasználókat a minden felhasználó és a törölt felhasználók oldalain. Az előzetes verzió változásai a következők: 
- További látható felhasználói tulajdonságok, például az objektumazonosító, a címtár-szinkronizálás állapota, a létrehozás típusa és az identitás kiállítója.
- A keresés mostantól lehetővé teszi a nevek, e-mailek és objektumazonosítók együttes keresését.
- Továbbfejlesztett szűrés felhasználói típus szerint (tag, vendég és nincs), címtár-szinkronizálási állapot, létrehozás típusa, cégnév és tartománynév.
- Új rendezési képességek olyan tulajdonságokhoz, mint a név, a felhasználó egyszerű neve és a törlés dátuma.
- Az új felhasználók száma minden kereséssel vagy szűrőkkel együtt frissül.

További információkért lásd: [a felhasználói felügyeleti fejlesztések (előzetes verzió) a Azure Active Directory](../enterprise-users/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Új megjegyzések mező vállalati alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások **termék képesség:** SSO

A vállalati alkalmazásokhoz ingyenes szöveges megjegyzéseket is hozzáadhat. Bármilyen releváns információt hozzáadhat, amely segítséget nyújt a vállalati alkalmazások kezelő alkalmazásai számára. További információ: gyors útmutató [: alkalmazás tulajdonságainak konfigurálása a Azure Active Directory (Azure ad) bérlőben](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – szeptember 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

Szeptember 2020-én a következő 34 új alkalmazásokat bővítettük az alkalmazás-galériában az összevonási támogatással:

[VMware Horizon-Unified Access Gateway](), [Pulse Secure PC-k](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for Business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [Glint Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [gender fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [nyelvtanilag](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), [Saviynt](../saas-apps/saviynt-tutorial.md), [BizMerlinHR](https://marketplace.bizmerlin.net/bmone/signup), [mobil zárolás](../saas-apps/mobile-locker-tutorial.md), [Zengine](../saas-apps/zengine-tutorial.md) [, CloudCADI,](https://app.cloudcadi.com/login)Simfoni- [elemzés](https://simfonianalytics.com/accounts/microsoft/login/), [Priva Identity & hozzáférés-kezelés](https://my.priva.com/), [nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [Fexa](../saas-apps/fexa-tutorial.md), [biztonságos aláírás Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [biztonságos aláírás Enterprise Portal HRE telepítő](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [WISTEC online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft által védett F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md)

Az összes alkalmazás dokumentációját itt tekintheti meg: https://aka.ms/AppsTutorial .

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a következő részleteket: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Új delegálási szerepkör az Azure AD-jogosultságok kezelésében: hozzáférés a csomag hozzárendelés-kezelőjéhez

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 
Új hozzáférési csomag hozzárendelés-kezelői szerepkör lett hozzáadva az Azure AD-jogosultságok kezelése szolgáltatásban a hozzárendelések kezeléséhez szükséges részletes engedélyek biztosításához. Most már delegálhatja a feladatokat a szerepkör egyik felhasználója számára, aki egy hozzáférési csomag hozzárendelés-kezelését egy üzleti tulajdonosnak delegálhatja. A hozzáférési csomag hozzárendelés-kezelője azonban nem tudja módosítani a hozzáférési csomag szabályzatait vagy a rendszergazdák által beállított egyéb tulajdonságokat. 

Ezzel az új szerepkörrel kihasználhatja a hozzárendelések felügyeletének delegálásához és a felügyeleti ellenőrzés megtartásához szükséges legalacsonyabb jogosultságokat az összes többi hozzáférési csomag konfigurációjában. További információ: [jogosultságok felügyeleti szerepkörei](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Privileged Identity Management a bevezetési folyamat változásai

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Privileged Identity Management  
A **termék képességei:** Privileged Identity Management
 
Korábban a Privileged Identity Management (PIM) megkövetelt felhasználói engedély bevezetését és a PIM paneljén bevezetési folyamatot, amely tartalmazza az Azure AD MFA-ban való regisztrálást. A PIM-élmény az Azure AD szerepköreibe és a rendszergazdák paneljére való legutóbbi integrálásával eltávolítjuk ezt a folyamatot. Minden érvényes P2 licenccel rendelkező bérlő automatikusan bekerül a PIM-be.

A PIM-be való bevezetésnek nincs közvetlen káros hatása a bérlőre. A következő módosításokat várhatja el:
- További hozzárendelési beállítások, mint például az aktív és a befejezési időpont, ha a hozzárendelést a PIM vagy az Azure AD szerepkörei és a rendszergazdák panelen végzi el. 
- A további hatókör-mechanizmusok, például a felügyeleti egységek és az egyéni szerepkörök közvetlenül a hozzárendelési élményben jelennek meg. 
- Ha Ön globális rendszergazda vagy Kiemelt szerepkörű rendszergazda, elindíthat néhány további e-mailt, például a PIM heti kivonatát. 
- Előfordulhat, hogy az MS-PIM szolgáltatásnév is megjelenik a szerepkör-hozzárendeléshez kapcsolódó naplóban. Ez a várt változás nem befolyásolja a normál munkafolyamatot.

 További információ: a [Privileged Identity Management használatának megkezdése](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Azure AD-jogosultságok kezelése: a hozzáférési csomag erőforrásai közül a Select (kiválasztás) ablaktábla mostantól alapértelmezés szerint a kiválasztott katalógusban aktuálisan megjelenő erőforrásokat jeleníti meg.

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Felhasználói hozzáférés kezelése  
A **termék képességei:** Jogosultságok kezelése
 

A hozzáférési csomag létrehozási folyamata alatt az erőforrás-szerepkörök lapon a kiválasztás ablaktábla viselkedése változik. Jelenleg az alapértelmezett viselkedés a kiválasztott katalógushoz hozzáadott felhasználó és erőforrások tulajdonában lévő összes erőforrás megjelenítése. 

Ez a felhasználói élmény úgy módosul, hogy csak a katalógusban jelenleg hozzáadott erőforrások jelenjenek meg alapértelmezés szerint, így a felhasználók könnyedén választhatják ki az erőforrásokat a katalógusból. A frissítés segítséget nyújt a csomagok eléréséhez hozzáadandó erőforrások felfedezésében, valamint a nem a katalógus részét képező felhasználó tulajdonában lévő erőforrások véletlen hozzáadásának kockázatának csökkentésében. További információk: [új hozzáférési csomag létrehozása az Azure ad-jogosultságok kezelésében](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>2020. augusztus 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Az Azure Multi-Factor Authentication-kiszolgáló tűzfal követelményeinek frissítései

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
2020 október 1-től az Azure MFA-kiszolgáló tűzfalának követelményei további IP-címtartományok szükségesek.

Ha a szervezetében kimenő tűzfalszabályok vannak, akkor frissítse a szabályokat, hogy az MFA-kiszolgálók kommunikálhassanak az összes szükséges IP-tartománnyal. Az IP-címtartományok dokumentálva vannak az [Azure multi-Factor Authentication-kiszolgáló tűzfal követelményeiben](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>A felhasználói élmény jövőbeli változásai a személyazonossággal védett pontszámban

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Identity Protection- **termék funkció:** identity Security & Protection

A Microsoft biztonságos pontszám [új kiadásában](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)bevezetett változásokkal összhangban frissítjük az Identity Secure score portált. 

A módosítások a szeptember elején lesznek elérhetők. Az előzetes verzió változásai a következők:
- A "személyazonosság biztonságos pontszáma" átnevezve a "Secure score for Identity" kifejezésre a Microsoft biztonságos pontszámának használatával
- A standard skálázásra normalizált pontok és a pontok helyett százalékosan jelentett jelentések

Ebben az előzetes verzióban az ügyfelek válthatnak a meglévő és az új felhasználói élmény között. Ez az előzetes verzió 2020 november végéig tart. Az előzetes verzió után az ügyfelek automatikusan az új UX-élményre lesznek irányítva.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Új korlátozott vendég hozzáférési engedélyek az Azure AD-ben – nyilvános előzetes verzió

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Access Control   
A **termék képességei:** Felhasználói felügyelet

Frissítettük a címtár szintű engedélyeket a vendég felhasználók számára. Ezek az engedélyek lehetővé teszik, hogy a rendszergazdák további korlátozásokat és vezérlőket igényeljenek a külső vendég felhasználói hozzáféréshez. A rendszergazdák mostantól további korlátozásokat adhatnak a külső vendégek számára a felhasználói és a csoportok profiljához és a tagsági adatokhoz való hozzáféréshez. Ennek a nyilvános előzetes verziónak a használatával az ügyfelek az egymással megnövelt felhasználói hozzáférést kezelhetik, így többek között a vendég felhasználókat a csoport (ok) tagságának megtekintésével.

További információ: [korlátozott hozzáférésű vendég hozzáférési engedélyek](../enterprise-users/users-restrict-guest-permissions.md) és [felhasználók alapértelmezett engedélyei](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Az egyszerű szolgáltatásokkal kapcsolatos különbözeti lekérdezések általános elérhetősége

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** MS Graph  
A **termék képességei:** Fejlesztői élmény
 
Microsoft Graph különbözeti lekérdezés mostantól támogatja az erőforrás típusát a következő 1.0-s verzióban:
- Szolgáltatásnév

Az ügyfelek mostantól hatékonyan nyomon követhetik az erőforrások módosításait, és a legjobb megoldást biztosítják, hogy szinkronizálják ezeket az erőforrásokat egy helyi adattárral. Ha meg szeretné tudni, hogyan konfigurálhatja ezeket az erőforrásokat egy lekérdezésben, tekintse meg a [különbözeti lekérdezés használata a Microsoft GRAPHI adatváltozások nyomon követéséhez](/graph/delta-query-overview)
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>A oAuth2PermissionGrant kapcsolatos különbözeti lekérdezések általános elérhetősége

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** MS Graph  
A **termék képességei:** Fejlesztői élmény

Microsoft Graph különbözeti lekérdezés mostantól támogatja az erőforrás típusát a következő 1.0-s verzióban:
- OAuth2PermissionGrant

Az ügyfelek mostantól hatékonyan nyomon követhetik az adott erőforrások módosításait, és a legjobb megoldást biztosítják, hogy szinkronizálják ezeket az erőforrásokat egy helyi adattárral. Ha meg szeretné tudni, hogyan konfigurálhatja ezeket az erőforrásokat egy lekérdezésben, tekintse meg a [különbözeti lekérdezés használata a Microsoft GRAPHI adatváltozások nyomon követéséhez](/graph/delta-query-overview)

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – augusztus 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció

2020 augusztusában a következő 25 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Backup365](https://portal.backup365.io/login), [szappanos](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [Enlyft Dynamics 365-összekötő](http://enlyft.com/), Serraview- [felhasználhatósági szoftveres megoldások](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), [egyedek](https://web.uniq.app/), [láthatók](../saas-apps/visibly-tutorial.md), [Zylo](../saas-apps/zylo-tutorial.md), [Edmentum-tanfolyami felmérések pontos elérési útja](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), WireWheel, [Zix megfelelőség és rögzítés](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), [Greenlight Enterprise Business Controls platform](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), Genetec- [vámkezelés](https://www.clearance.network/), [ISAM](../saas-apps/isams-tutorial.md), [Scalefusion](https://scalefusion.com/users/sign_in/) [VeraSMART](../saas-apps/verasmart-tutorial.md), [Bpanda](https://goto.bpanda.com/login) [Amiko](https://amiko.web.rivero.app/), [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect) [Twingate](https://auth.twingate.com/signup), [tölcsér](https://nestiolistings.com/sso/oidc/azure/authorize/) [WireWheel](../saas-apps/wirewheel-tutorial.md) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md) [Wandera End User](https://www.wandera.com/)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás az Azure AD-katalógusban való listázásához olvassa el a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Az Azure AD DS számára már elérhetők az erőforrás-erdők 

**Írja be a következőt:** Új szolgáltatás **szolgáltatásának kategóriája:** Azure ad Domain Services   
A **termék képességei:** Azure AD Domain Services
 
A Azure AD Domain Services erőforrás-erdők képességei már általánosan elérhetők. Mostantól engedélyezheti a jelszó-kivonatolási szinkronizálás nélküli engedélyezést a Azure AD Domain Services használatához, beleértve az intelligens kártyás hitelesítést is. További információ: [a replika-készletek fogalmai és funkciói Azure Active Directory Domain Services (előzetes verzió)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>A regionális replika támogatása az Azure AD DS felügyelt tartományokhoz már elérhető

**Írja be a következőt:** Új funkció   
**Szolgáltatás kategóriája:** Azure AD Domain Services  
A **termék képességei:** Azure AD Domain Services
 
A felügyelt tartományokat kiterjesztheti úgy, hogy az Azure AD-bérlőn belüli több replikát is hozzon létre. A rendszer a replikákat bármely olyan Azure-régióban felhasználhatja, amely támogatja a Azure AD Domain Services. A különböző Azure-régiókban található további replikák földrajzi vész-helyreállítást biztosítanak az örökölt alkalmazásokhoz, ha egy Azure-régió offline állapotba kerül. További információ: [a replika-készletek fogalmai és funkciói Azure Active Directory Domain Services (előzetes verzió)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Az Azure AD saját Sign-Ins általános elérhetősége

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Végfelhasználói élmények
 
Az Azure AD saját Sign-Ins egy új szolgáltatás, amely lehetővé teszi a vállalati felhasználók számára, hogy megtekintsék a bejelentkezési Előzményeiket a szokatlan tevékenységek ellenőrzéséhez. Emellett ez a funkció lehetővé teszi, hogy a végfelhasználók a gyanús tevékenységekről jelentést tegyenek "ez nem nekem" vagy "Ez volt számomra". Ha többet szeretne megtudni a szolgáltatás használatáról, tekintse meg [a legutóbbi bejelentkezési tevékenység megtekintése és keresése a saját Sign-Ins oldalon](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity)című témakört.
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Az SAP SuccessFactors HR vezérelt felhasználói kiépítés az Azure AD-be már általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Mostantól integrálhatja az SAP SuccessFactors-t mérvadó identitásként az Azure AD-vel, és automatizálhatja a teljes körű identitás-életciklust olyan HR-események használatával, mint az új bérletek és a megszüntetések a fiókok az Azure AD-ben való kiépítésének és kiépítésének megkezdéséhez. 

Ha többet szeretne megtudni arról, hogyan konfigurálhatja az SAP SuccessFactors bejövő üzembe helyezését az Azure AD-be, tekintse meg az oktatóanyag az [SAP SuccessFactors konfigurálása a felhasználók kiépítési Active Directoryához](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)című témakört.
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Egyéni Open ID – a csatlakozási MS Graph API támogatása Azure AD B2C

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Korábban az egyéni Open ID összekapcsolási szolgáltatókat csak a Azure Portal lehet felvenni vagy felügyelni. A Azure AD B2C ügyfelek most már Microsoft Graph API-k bétaverziójának használatával is hozzáadhatnak és kezelhetik azokat. Az erőforrás API-kkal való konfigurálásának megismeréséhez lásd: [identityProvider erőforrástípus](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Azure AD beépített szerepkörök kiosztása a Felhőbeli csoportoknak

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD-szerepkörök  
A **termék képességei:** Access Control

Ezzel az új funkcióval mostantól hozzárendelhet Azure AD beépített szerepköröket a felhőalapú csoportokhoz. Hozzárendelheti például a SharePoint-rendszergazda szerepkört Contoso_SharePoint_Admins csoporthoz. Azt is megteheti, hogy a PIM használatával a csoport jogosult tagja legyen az állandó hozzáférés megadása helyett. A szolgáltatás konfigurálásával kapcsolatos további információkért lásd: [felhőalapú csoportok használata a szerepkör-hozzárendelések kezeléséhez Azure Active Directory (előzetes verzió)](../roles/groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Elérhetővé vált az üzleti vezető beépített szerepköre

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD-szerepkörök  
A **termék képességei:** Access Control
 
A bepillantást nyerhető üzleti vezető szerepkör felhasználói az irányítópultok és az adatellenőrzések egy készletét érhetik el az M365-betekintő [alkalmazáson](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)keresztül. Ebbe beletartozik az összes irányítópulthoz való teljes hozzáférés, valamint az elemzések és az adatfeltárási funkciók. Azonban ebben a szerepkörben a felhasználók nem férhetnek hozzá a termék konfigurációs beállításaihoz, amely az információ-felügyeleti szerepkör felelőssége. Ha többet szeretne megtudni erről a szerepkörről, tekintse meg a [rendszergazdai szerepkörre vonatkozó engedélyeket Azure Active Directory](../roles/permissions-reference.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Az elemzések rendszergazda beépített szerepköre már elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Azure AD-szerepkörök  
A **termék képességei:** Access Control
 
Az információkkal rendelkező rendszergazda szerepkör felhasználói a M365-betekintő [alkalmazásban](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics)hozzáférhetnek a felügyeleti funkciók teljes készletéhez. Az ebben a szerepkörben lévő felhasználó beolvashatja a címtáradatok adatait, figyelheti a szolgáltatás állapotát, a fájl-támogatási jegyeket, és elérheti az információkhoz való rendszergazdai beállításokkal kapcsolatos szempontokat. Ha többet szeretne megtudni erről a szerepkörről, tekintse meg a [rendszergazdai szerepkörre vonatkozó engedélyeket Azure Active Directory](../roles/permissions-reference.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>Az alkalmazás-rendszergazda és a Felhőbeli alkalmazás rendszergazdája kezelheti az alkalmazások bővítményének tulajdonságait

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD-szerepkörök  
A **termék képességei:** Access Control
 
Korábban csak a globális rendszergazda kezelhetik a [bővítmény tulajdonságot](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Most engedélyezzük ezt a képességet az alkalmazás-rendszergazda és a Felhőbeli alkalmazás-rendszergazda számára is.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>4.6.263.0 és-összekötők 1.1.1301.0 2016 SP2 gyorsjavítások

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Microsoft Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése

A [gyorsjavítások kumulatív csomagja (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) elérhető a következőhöz: Microsoft Identity Manager (2016 Service Pack 2 (SP2). Ez a kumulatív csomag a MIM CM, a beszinkronizálási kezelő és a PAM-összetevők frissítéseit tartalmazza. Emellett a 1.1.1301.0-hoz készült általános összekötők buildek a Graph-összekötő frissítéseire is kiterjednek.

---
 
## <a name="july-2020"></a>2020. július

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>Rendszergazdaként feltételes hozzáféréssel szeretném megcélozni az ügyfélalkalmazások használatát

**Írja be a következőt:** Tervezze meg a változást   
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
Ha a feltételes hozzáférésben az ügyfélalkalmazások jelennek meg a GA kiadásával, a rendszer alapértelmezés szerint új szabályzatokat alkalmaz az összes ügyfélalkalmazás számára. Ilyenek például az örökölt hitelesítési ügyfelek. A meglévő szabályzatok változatlanok maradnak, de a *configure igen/No* váltógomb el lesz távolítva a meglévő szabályzatokból, így könnyen látható, hogy mely ügyfélalkalmazások lesznek alkalmazva a szabályzat által. 

Új szabályzat létrehozásakor ügyeljen arra, hogy kizárjon egy örökölt hitelesítést használó felhasználókat és szolgáltatásfiókokat; Ha nem, akkor le lesznek tiltva. [További információk](../conditional-access/concept-conditional-access-conditions.md).
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Közelgő SCIM-megfelelőségi javítások

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure AD-kiépítési szolgáltatás a SCIM szabványt használja az alkalmazásokkal való integrációhoz. A SCIM standard implementációja fejlődik, és elvárjuk, hogy módosítjuk a javítási műveleteket, valamint az "Active" tulajdonságot egy erőforráson. [További információk](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md).
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>A csoport tulajdonosának beállítása az Azure felügyeleti portálon módosítva lesz

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés

A csoportok általános beállítási lapján beállíthatja, hogy a tulajdonos-hozzárendelési jogosultságokat egy korlátozott felhasználói csoportra korlátozza az Azure felügyeleti portál és a hozzáférési panel. Hamarosan lehetőség van arra, hogy a csoport tulajdonosi jogosultságait ne csak a két UX-portálon lehessen hozzárendelni, hanem a háttérbeli szabályzatot is kikényszeríteni, hogy konzisztens viselkedést biztosítson a végpontok, például a PowerShell és a Microsoft Graph között. 

Elkezdjük letiltani a jelenlegi beállítást azon ügyfelek esetében, akik nem használják azt, és a következő néhány hónapban lehetőséget biztosítanak a csoport tulajdonosi jogosultságának hatókörére a felhasználók számára. A csoportok beállításainak frissítésével kapcsolatos útmutatásért lásd: a csoport adatainak szerkesztése [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)használatával.

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory regisztrációs szolgáltatás befejezte a TLS 1,0 és 1,1 támogatását

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Eszközök regisztrációja és kezelése  
A **termék képességei:** Platform

A Transport Layer Security (TLS) 1,2 és a frissítési kiszolgálók és ügyfelek hamarosan Azure Active Directory Eszközregisztráció szolgáltatással fognak kommunikálni. Az Azure AD-eszközök regisztrálási szolgáltatásával folytatott kommunikációhoz használt TLS 1,0 és 1,1 támogatása a következő módon fog kivonulni:
- 2020. augusztus 31-én, minden szuverén felhőkben (GCC High, DoD stb.)
- 2020. október 30-án minden kereskedelmi felhőben

[További](../devices/reference-device-registration-tls-1-2.md) információ az Azure ad regisztrációs szolgáltatás TLS 1,2-ről.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>A vállalati Windows Hello-bejelentkezések az Azure AD bejelentkezési naplóiban láthatók

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Jelentési  
A **termék képességei:** & jelentéskészítés figyelése
 
A vállalati Windows Hello lehetővé teszi a végfelhasználók számára, hogy egy kézmozdulattal (például PIN-kóddal vagy biometrikus adatokkal) jelentkezzenek be a Windows rendszerű gépekre. Előfordulhat, hogy az Azure AD-rendszergazdák meg szeretnék különböztetni a vállalati Windows Hello-bejelentkezéseket más Windows-bejelentkezésekhez a szervezetnek a jelszó nélküli hitelesítéshez való hozzáférése részeként. 

A rendszergazdák mostantól láthatják, hogy egy Windows-hitelesítés használta-e a Windows Hello for businesst a Azure Portal Azure AD Sign-Ins paneljén található hitelesítés részletei lapon. A vállalati Windows Hello hitelesítés a hitelesítési módszer mezőben a "WindowsHelloForBusiness" kifejezést fogja tartalmazni. Sign-In naplók értelmezésével kapcsolatos további információkért tekintse meg a [bejelentkezési naplók dokumentációját](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Javítások a csoportok törlési viselkedésére és a teljesítmény javítására

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Korábban, amikor egy csoport a "hatókörből" a "hatókörön kívülre" módosult, és egy rendszergazda a módosítás befejeződése előtt újraindult, a csoport objektum nem lett törölve. Most a Group objektum törlődik a célalkalmazás területéről, ha a hatókörön kívül esik (letiltva, törölve, nincs hozzárendelve vagy nem adta meg a hatókör-szűrőt). [További információk](../app-provisioning/how-provisioning-works.md#incremental-cycles).
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Nyilvános előzetes verzió: a rendszergazdák mostantól hozzáadhatnak egyéni tartalmat az e-mailben a felülvizsgálók számára hozzáférési felülvizsgálat létrehozásakor

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
A **termék képességei:** Identitás-szabályozás
 
Új hozzáférési felülvizsgálat létrehozásakor a felülvizsgáló e-mailt kap, amely a hozzáférési felülvizsgálat befejezését kéri. Számos ügyfelünk arra kérte a felhasználókat, hogy egyéni tartalmakat adjanak hozzá az e-mailekhez, például kapcsolattartási adatokhoz vagy egyéb kiegészítő tartalomhoz a felülvizsgáló irányításához. 

A nyilvános előzetes verzióban elérhető rendszergazdák megadhatnak egyéni tartalmakat a felülvizsgálók számára az Azure AD hozzáférési felülvizsgálatok "speciális" szakaszában található tartalom hozzáadásával. A hozzáférési felülvizsgálatok létrehozásával kapcsolatos útmutatásért lásd: [csoportok és alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](../governance/create-access-review.md)felülvizsgálatokban.
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Engedélyezési kód folyamatábrája elérhető egyoldalas alkalmazásokhoz

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Fejlesztői élmény
 
A modern böngésző harmadik féltől származó cookie-korlátozásai (például a Safari ITP) miatt a fürdőhelyeknek az engedélyezési kód folyamatát kell használniuk az egyszeri bejelentkezés fenntartása helyett, és a MSAL.js v 2. x mostantól támogatja az engedélyezési kód folyamatát. 

A Azure Portal megfelelő frissítései vannak, így frissítheti a SPA-t a "Spa" típusra, és használhatja az Auth Code flow-t. További útmutatásért lásd: [bejelentkezési felhasználók és hozzáférési token beszerzése egy JavaScript Spa-beli hitelesítési kód használatával](../develop/quickstart-v2-javascript-auth-code.md) .
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Az Azure AD Application Proxy mostantól támogatja a Távoli asztali szolgáltatások webes ügyfélprogramot

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazásproxy  
A **termék képességei:** Access Control

Az Azure AD Application Proxy mostantól támogatja a Távoli asztali szolgáltatások (RDS) webes ügyfélprogramot. Az RDS webes ügyfélprogram lehetővé teszi, hogy a felhasználók bármilyen HTLM5-kompatibilis böngészővel hozzáférhessenek Távoli asztal infrastruktúrához, például a Microsoft Edge, az Internet Explorer 11, a Google Chrome stb. A felhasználók ugyanúgy kezelhetik a távoli alkalmazásokat vagy asztalokat, mint a helyi eszközökkel. Az Azure AD Application Proxy használatával növelheti az RDS-környezet biztonságát úgy, hogy az előhitelesítést és a feltételes hozzáférési szabályzatokat minden különféle típusú ügyfélalkalmazás számára kikényszeríti. Útmutatásért lásd: [Távoli asztal közzététele az Azure ad Application proxyban](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>A következő generációs Azure AD B2C felhasználói folyamatok nyilvános előzetes verziója

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
A **termék képességei:** B2B/B2C
 
Az egyszerűsített felhasználói folyamat az előzetes verziójú funkciókkal rendelkező funkciók paritását kínálja, és az összes új funkció otthona. A felhasználók az azonos felhasználói folyamaton belül is engedélyezhetik az új szolgáltatásokat, ami csökkenti a több verzió létrehozásának szükségességét minden új szolgáltatás kiadásával. Végül az új, felhasználóbarát UX leegyszerűsíti a felhasználói folyamatok kiválasztását és létrehozását. Próbálja ki most [egy felhasználói folyamat létrehozásával](../../active-directory-b2c/tutorial-create-user-flows.md). 

További információ a felhasználók forgalmáról: [felhasználói folyamatok verziói Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – július 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 júliusában a következő 55 új alkalmazásokat bővítettük az alkalmazás-galériában az összevonási támogatással:

[Taps a kezed](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor](https://inexto.com/inexto-suite/inextor)-tár [, Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects recepciós](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [Cocoom](https://start.cocoom.com/), [Coins Construction Cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote MT](https://task.teamsmain.medx.im/authorization), [reflekt](https://reflekt.konsolute.com/login), [álmodozás](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/), [GReminders](https://app.greminders.com/o365-oauth), [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US), Opentext [Directory Services](../saas-apps/opentext-directory-services-tutorial.md) [, Datasite,](../saas-apps/datasite-tutorial.md) [BlogIn](../saas-apps/blogin-tutorial.md) [, INTSIGHTS,](../saas-apps/intsights-tutorial.md) [Kpifire,](../saas-apps/kpifire-tutorial.md) [Textline](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [közösségi Spark](../saas-apps/community-spark-tutorial.md), [Chatwork](../saas-apps/chatwork-tutorial.md) [, CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Michigan adatközpont egyszeri bejelentkezés](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md), [kimenő](../saas-apps/egress-tutorial.md)forgalom [, SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [jobb oldali kiberbiztonsági ADI](https://right-hand.ai/), [Fyde nagyvállalati hitelesítés](https://enterprise.fyde.com/), verme, [Lenses.IO](../saas-apps/lensesio-tutorial.md) [,](../saas-apps/verme-tutorial.md) [Momenta](../saas-apps/momenta-tutorial.md), [növekedés](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), [mond robot](https://tellme365liteweb.azurewebsites.net/), [INSPIRE](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML-összekötő](https://www.strata.io/identity-fabric/), [Smartschool (iskolai felügyeleti rendszer)](https://smartschoolz.com/login), [Zepto-intelligens időmérő](https://user.zepto-ai.com/signin), [Studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [Kemp Loadmaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack egyszeri bejelentkezés](../saas-apps/browserstack-single-sign-on-tutorial.md)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial

Az alkalmazás Azure AD-katalógusban való listázásához tekintse meg a részleteket itt: https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – július 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció

Mostantól automatizálhatja az újonnan integrált alkalmazás [LinkedIn learning](../saas-apps/linkedin-learning-provisioning-tutorial.md)felhasználói fiókjainak létrehozását, frissítését és törlését.

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Szerepkör-hozzárendelések megtekintése az összes hatókörben és a fájlok CSV-fájlba való letöltésének lehetősége

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Azure AD-szerepkörök  
A **termék képességei:** Access Control
 
Most már megtekintheti a szerepkör-hozzárendeléseket az összes hatókörben az Azure AD-portál "szerepkörök és rendszergazdák" lapján. Az egyes szerepkörökhöz tartozó szerepkör-hozzárendeléseket egy CSV-fájlba is letöltheti. A szerepkör-hozzárendelések megtekintésével és hozzáadásával kapcsolatos útmutatásért lásd: [rendszergazdai szerepkörök megtekintése és hozzárendelése a Azure Active Directory-ben](../roles/manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication szoftverfejlesztés (Azure MFA SDK) – elavult

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** MFA  
A **termék képességei:** Identitás-biztonsági & védelme
 
Az Azure Multi-Factor Authentication szoftverfejlesztés (Azure MFA SDK) 2018 november 14-én ért véget az élet végére, amely az első, 2017. novemberében jelent meg. A Microsoft az SDK-szolgáltatást a 2020-es szeptember 30-ig érvénybe lép. Az SDK-ra irányuló hívások sikertelenek lesznek.

Ha a szervezete az Azure MFA SDK-t használja, a 2020 szeptember 30-ig kell áttelepítenie:
- Azure MFA SDK a következőhöz: Ha az SDK-t a webplatformon használja, telepítse át az Azure MFA-kiszolgálóra, és aktiválja Privileged Access Management (PAM) műveletet az alábbi [utasítások](/microsoft-identity-manager/working-with-mfaserver-for-mim)követésével.   
- Azure MFA SDK testreszabott alkalmazásokhoz: érdemes lehet integrálni az alkalmazást az Azure AD-be, és feltételes hozzáféréssel használni az MFA betartatására. Az első lépésekhez tekintse át ezt a [lapot](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>2020. június 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Felhasználói kockázati feltétel a feltételes hozzáférési házirendben

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 

Az Azure AD feltételes hozzáférési szabályzatának felhasználói kockázati támogatása lehetővé teszi több felhasználói kockázati alapú házirend létrehozását. Különböző felhasználók és alkalmazások esetében különböző minimális felhasználói kockázati szintek szükségesek. A felhasználói kockázat alapján házirendeket hozhat létre a hozzáférés letiltásához, a többtényezős hitelesítés megköveteléséhez, a jelszavak biztonságos módosításához, vagy a munkamenet-szabályzat érvényesítéséhez Microsoft Cloud App Security átirányításához, például további naplózáshoz.

A felhasználói kockázati feltétel prémium szintű Azure AD P2-t igényel, mivel az Azure Identity Protectiont használja, amely P2-ajánlat. a feltételes hozzáféréssel kapcsolatos további információkért tekintse meg az [Azure ad feltételes hozzáférési dokumentációját](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>Az SAML SSO mostantól támogatja azokat az alkalmazásokat, amelyekhez szükség van a SPNameQualifier beállítására

**Írja be a következőt:** Rögzített  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Egyes SAML-alkalmazások esetén SPNameQualifier kell visszaadni az állítás tárgyában, ha erre szükség van. Most az Azure AD megfelelően reagál, ha a kérelem NameID szabályzata SPNameQualifier kér. Ez az SP által kezdeményezett bejelentkezéshez is használható, és a identitásszolgáltató kezdeményezett bejelentkezés a következő lesz:.  Ha többet szeretne megtudni a Azure Active Directory SAML protokollról, tekintse meg a következőt: [Single Sign-On SAML protokoll](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Az Azure AD B2B együttműködés támogatja a MSA és a Google-felhasználók meghívását Azure Government bérlők számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

A B2B csoportmunka-szolgáltatásokat használó bérlők a Microsoft-vagy Google-fiókkal rendelkező felhasználókat is meghívhatják Azure Government. Annak megállapításához, hogy a bérlő használhatja-e ezeket a képességeket, kövesse az utasításokat, hogyan állapítható meg, hogy a [B2B együttműködés elérhető-e az Azure US government-bérlőben?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>Az MS Graph v1 felhasználói objektuma mostantól tartalmazza a externalUserState és a externalUserStateChangedDateTime tulajdonságokat

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 

A externalUserState és a externalUserStateChangedDateTime tulajdonsággal megkeresheti azokat a meghívott B2B vendégeket, akik még nem fogadták el a meghívókat, valamint olyan automatizálást is készíthetnek, mint például olyan felhasználók törlése, akik nem fogadták el a meghívókat néhány nap Ezek a tulajdonságok mostantól elérhetők az MS Graph v1-ben. A tulajdonságok használatával kapcsolatos útmutatásért tekintse meg a [felhasználói erőforrás típusát](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Mostantól általánosan elérhető a hitelesítési munkamenetek kezelése az Azure AD feltételes hozzáférésben

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme
 
A hitelesítési munkamenet-kezelési funkciók lehetővé teszik annak a konfigurálását, hogy a felhasználóknak milyen gyakran kell megadniuk a bejelentkezési hitelesítő adatokat, és hogy szükségük van-e a hitelesítő adatok megadására a böngészők bezárása és újbóli megnyitása után, hogy nagyobb biztonságot és rugalmasságot nyújtsanak
 
Emellett a hitelesítési munkamenet-kezelés csak az Azure AD-hez csatlakoztatott, a hibrid Azure AD-hez csatlakoztatott és az Azure AD által regisztrált eszközök első tényezős hitelesítésére vonatkozik. A hitelesítési munkamenetek kezelése mostantól az MFA-ra is érvényes lesz. További információ: [a hitelesítési munkamenetek kezelésének beállítása feltételes hozzáféréssel](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – június 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
2020 júniusában a következő 29 új alkalmazást bővítettük az alkalmazás-galériában az összevonási támogatással:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [szegmens](../saas-apps/segment-tutorial.md), [AI auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca-kapcsolat](https://app.pobu.ca/), [proto.IO](../saas-apps/proto.io-tutorial.md), [forgalomirányító](https://www.gatekeeperhq.com/), [hub Planner](../saas-apps/hub-planner-tutorial.md), [Ansira-partner piacra](https://ansira.com/technology/channel-engagement)jutási eszközkészlet, [IBM digitális üzleti automatizálás felhőben](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [kisi fizikai biztonság](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [INTELLIGENCEBANK](../saas-apps/intelligencebank-tutorial.md), [Pymetrics](../saas-apps/pymetrics-tutorial.md), [nulla](https://www.teamzero.com/), [instation](https://instation.invillia.com/), [edX for Business SAML 2,0 integráció](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [mező azonosítója](../saas-apps/field-id-tutorial.md), [tananyag SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Helix Core-Helix hitelesítési szolgáltatás](../saas-apps/perforce-helix-core-tutorial.md), MyCompliance- [felhő](https://cloud.metacompliance.com/), Smallstep [SSH](https://smallstep.com/sso-ssh/)  

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

További információ az API-összekötők által elérhető összes élményről: [API-összekötők használata az önkiszolgáló regisztráció testreszabásához és kiterjesztéséhez](../external-identities/api-connectors-overview.md), illetve [külső identitások önkiszolgáló regisztrációjának testreszabása webes API-integrációkkal](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
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
 
Egy új delegált engedély EntitlementManagement. Read. All már használható a jogosultsági felügyeleti API-hoz a Microsoft Graph Beta-ban. További információ az elérhető API-król: [Az Azure ad jogosultság-kezelési API használata](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>A 1.0-s verzióban elérhető Identity Protection API-k

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Identity Protection  
A **termék képességei:** Identitás-biztonsági & védelme
 
A riskyUsers és a riskDetections Microsoft Graph API-k már általánosan elérhetők. Most, hogy elérhetők a 1.0-s végponton, meghívja Önt, hogy éles környezetben használja őket. További információkért tekintse meg a [Microsoft Graph dokumentációját](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Mostantól általánosan elérhetők a szabályzatok Microsoft 365 csoportokra való alkalmazásának érzékenységi címkéi

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Csoport kezelése  
A **termék képességei:** Együttműködés
 

Mostantól érzékenységi címkéket is létrehozhat, és a címke beállításaival házirendeket alkalmazhat Microsoft 365 csoportokra, beleértve az adatvédelmi (nyilvános vagy magánjellegű) és a külső felhasználói hozzáférési házirendet. Létrehozhat egy címkét az adatvédelmi szabályzattal, hogy magánjellegű legyen, és a külső felhasználói hozzáférési házirend ne engedélyezze a vendég felhasználók hozzáadását. Ha a felhasználó ezt a címkét egy csoportra alkalmazza, a csoport privát lesz, és a csoportba nem vehetők fel vendég felhasználók. 

Az érzékeny címkék fontosak az üzleti szempontból kritikus fontosságú adatmennyiségek védelme érdekében, és lehetővé teszik a csoportok megfelelő és biztonságos módon történő kezelését. Az érzékenységi címkék használatával kapcsolatos útmutatásért lásd [: érzékenységi címkék társítása Microsoft 365 csoportokhoz Azure Active Directory (előzetes verzió)](../enterprise-users/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>A prémium szintű Azure AD ügyfelek Microsoft Identity Manager támogatásának frissítései

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Microsoft Identity Manager  
A **termék képességei:** Identitás-életciklus kezelése
 
Az Azure-támogatás mostantól elérhető a Microsoft Identity Manager 2016 Azure AD integrációs összetevői számára, a Microsoft Identity Manager 2016 kiterjesztett támogatásának végéig. További információ: [prémium szintű Azure ad ügyfelek támogatása Microsoft Identity Manager használatával](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>A csoporttagság feltételeinek használata az SSO-jogcímek konfigurációjában nagyobb

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Korábban a jogcímek a csoporttagság alapján való feltételeit egy adott alkalmazás konfigurációjában legfeljebb 10 értékre lehet használni. Az SSO-jogcímek konfigurációjában a csoporttagság feltételeinek használata már legfeljebb 50 csoportra nőtt. A jogcímek konfigurálásával kapcsolatos további információkért tekintse meg a [vállalati alkalmazások SSO-jogcímek konfigurálása](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions)című témakört. 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Alapszintű formázás engedélyezése a bejelentkezési oldal Text összetevőjében a vállalati arculatban.

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
A **termék képességei:** Felhasználói hitelesítés
 
A vállalati védjegyezési funkció az Azure AD/Microsoft 365 bejelentkezési élményben frissült, így lehetővé teszi az ügyfél számára a hiperhivatkozások és egyszerű formázás hozzáadását, beleértve a félkövér betűtípust, az aláhúzást és a dőlt betűket. A funkció használatáról a következő témakörben talál útmutatást: [branding hozzáadása a szervezet Azure Active Directory bejelentkezési lapjához](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Teljesítménybeli javítások kiépítés

**Írja be a következőt:** Módosított funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
A kiépítési szolgáltatás frissítve lett, hogy csökkentse a [növekményes ciklus](../app-provisioning/how-provisioning-works.md#incremental-cycles) befejezésének idejét. Ez azt jelenti, hogy a felhasználók és a csoportok gyorsabban lesznek kiépítve az alkalmazásba, mint korábban. Az 6/10/2020-es után létrehozott összes új kiépítési feladat automatikusan kihasználja a teljesítménybeli javításokat. A 6/10/2020 előtti üzembe helyezéshez konfigurált összes alkalmazást újra kell indítani a 6/10/2020 után, hogy kihasználhassa a teljesítmény javításait. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>A ADAL és az MS Graph paritás érvénytelenítésének bejelentése

**Írja be a következőt:** Elavult  
**Szolgáltatás kategóriája:** N/A  
A **termék képességei:** Eszközök életciklusának kezelése

Most, hogy elérhetővé vált a Microsoft Authentication librarys (MSAL), már nem fogunk új funkciókat hozzáadni a Azure Active Directory Authentication librarys (ADAL) szolgáltatáshoz, és a biztonsági javítások 2022. június 30-án fognak megjelenni. A MSAL-re való áttelepítéssel kapcsolatos további információkért tekintse át az [alkalmazások áttelepítését a Microsoft Authentication Library (MSAL) webhelyre](../develop/msal-migration.md)című témakört.

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
 

Jelenleg az Azure AD-ben összevont tartományokba tartozó, de a bérlőbe nem szinkronizált felhasználók nem férhetnek hozzá a csapatokhoz. Június végétől kezdve ez az új funkció lehetővé teszi számukra, hogy kiterjessze a meglévő e-mailek ellenőrzött regisztrációs funkcióját. Ez lehetővé teszi, hogy a felhasználók egy összevont identitásszolgáltató jelentkezzenek be, de még nem rendelkeznek felhasználói objektummal az Azure ID-ben, hogy a felhasználói objektum automatikusan létrejöjjön, és a csapatok hitelesítve legyenek. A felhasználói objektum "önkiszolgáló regisztráció" állapotúként lesz megjelölve. Ez a meglévő, az e-mailek ellenőrzésére szolgáló lehetőség kiterjesztése, amelyet a felügyelt tartományokban lévő felhasználók megtehetnek, és ugyanazzal a jelzővel vezérelhetők. Ez a változás a következő két hónap során fejeződik be. Tekintse meg a dokumentációs frissítéseket [itt](../enterprise-users/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Közelgő javítás: a Azure Government-felhő OIDC-felderítési dokumentumának frissítése folyamatban van a megfelelő gráf-végpontokra való hivatkozáshoz.

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Szuverén felhők  
A **termék képességei:** Felhasználói hitelesítés
 
Júniusban a OIDC-felderítési dokumentum a [Microsoft Identity platform és az OpenID Connect protokoll](../develop/v2-protocols-oidc.md) a [Azure Government cloud](../develop/authentication-national-cloud.md) Endpoint (login.microsoftonline.us) alkalmazásban megkezdi a megfelelő [nemzeti Cloud Graph](/graph/deployments) -végpont ( https://graph.microsoft.us vagy https://dod-graph.microsoft.us) a megadott bérlő alapján) visszaadását.  Jelenleg a helytelen gráf végpont (graph.microsoft.com) "msgraph_host" mezőt biztosítja.  

Ez a hibajavítás fokozatosan, körülbelül 2 hónapig lesz kivezetve.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure Government felhasználók többé nem fognak tudni bejelentkezni a login.microsoftonline.com

**Írja be a következőt:** Tervezze meg a változást  
**Szolgáltatás kategóriája:** Szuverén felhők  
A **termék képességei:** Felhasználói hitelesítés
 
2018 június 1-én a Azure Government hivatalos Azure Active Directory (Azure AD) hatósága a verzióról a verzióra változott https://login-us.microsoftonline.com https://login.microsoftonline.us . Ha egy Azure Government bérlőn belül egy alkalmazás tulajdonosa, frissítenie kell az alkalmazást, hogy a. us végponton jelentkezzen be a felhasználókba.

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
 
A rendszergazdák az Azure ADConnect Cloud-kiépítés beállításakor az új "hibrid rendszergazda" szerepkört is megkezdhetik a legkevésbé Kiemelt szerepkörbe. Ezzel az új szerepkörrel már nem kell a globális rendszergazdai szerepkört használnia a felhőalapú kiépítés beállításához és konfigurálásához. [További információk](../roles/delegate-by-task.md#connect).
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Új összevont alkalmazások érhetők el az Azure AD Application Galleryben – május 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**Termék képesség:** harmadik féltől származó integráció
 
A 2020-es verzióban az alábbi 36 új alkalmazások is elérhetők az alkalmazás-galériában az összevonási támogatással:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell Teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [Asc rögzítési ismeretek](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-engagement](https://b-engaged.se/), [Competella Contact Center munkacsoportja](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft-identitás](https://identity.imagesoftinc.com/), [saját IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX megbízhatósági platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [intelligens globális irányítás](../saas-apps/smart-global-governance-tutorial.md), [Prezi](../saas-apps/prezi-tutorial.md), [Mapbox](../saas-apps/mapbox-tutorial.md), [Datava Enterprise Service platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [szeszélyes](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO for torkolatánál](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for BitBucket](../saas-apps/easysso-for-bitbucket-tutorial.md), EasySSO [for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii,](../saas-apps/torii-tutorial.md) [Axiad felhő](../saas-apps/axiad-cloud-tutorial.md), [humán](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md), [CCH Tagetik](../saas-apps/cch-tagetik-tutorial.md), [ShareVault](../saas-apps/sharevault-tutorial.md), [Vyond](../saas-apps/vyond-tutorial.md), [TextExpander](../saas-apps/textexpander-tutorial.md), [bárki otthoni CRM](../saas-apps/anyone-home-crm-tutorial.md), [askSpoke](../saas-apps/askspoke-tutorial.md), [Ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

Itt megtalálhatja az összes alkalmazás dokumentációját is https://aka.ms/AppsTutorial .

Az alkalmazás Azure AD-katalógusban való listázásához tekintse meg a részleteket itt https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>A feltételes hozzáféréshez már általánosan elérhető a csak jelentés üzemmód

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az [Azure ad feltételes hozzáféréshez csak jelentési mód](../conditional-access/concept-conditional-access-report-only.md) lehetővé teszi a szabályzatok eredményének kiértékelését a hozzáférés-vezérlés kényszerítése nélkül. A szervezeten belül csak a jelentésekre vonatkozó házirendeket lehet tesztelni, és megismerni a hatásukat, mielőtt engedélyezné őket, így biztonságosabbá és egyszerűbbé teheti a telepítést. Az elmúlt néhány hónapban már láttuk a csak jelentési mód erős bevezetését – a 26M-felhasználókra már csak jelentésekre vonatkozó szabályzatok tartoznak. A bejelentéssel az új Azure AD feltételes hozzáférési szabályzatok alapértelmezés szerint csak jelentési módban lesznek létrehozva. Ez azt jelenti, hogy nyomon követheti a szabályzatok hatását a létrehozásuk pillanatától kezdve. Továbbá azok számára, akik az MS Graph API-kat használják, a [jelentésekre vonatkozó szabályzatokat programozott módon is kezelheti](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Önkiszolgáló regisztráció a vendég felhasználók számára

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** B2B  
A **termék képességei:** B2B/B2C
 
Az Azure AD külső identitásokkal lehetővé teheti, hogy a szervezeten kívüli személyek hozzáférjenek az alkalmazásokhoz és az erőforrásokhoz, miközben a felhasználó bármilyen identitással bejelentkezhetnek. Ha külső felhasználókkal oszt meg egy alkalmazást, előfordulhat, hogy nem mindig tudja előre, hogy kinek van szüksége az alkalmazáshoz való hozzáférésre. Az [önkiszolgáló regisztráció](../external-identities/self-service-sign-up-overview.md)lehetővé teszi a vendég felhasználók számára, hogy regisztráljanak, és vendég fiókot kapjanak az üzletági (LOB) alkalmazások számára. A regisztrációs folyamat létrehozható és testreszabható az Azure AD és a közösségi identitások támogatásához. A regisztráció során további információkat is gyűjthet a felhasználóról.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>A feltételes hozzáférési információ és a jelentéskészítési munkafüzet általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az információk [és a jelentéskészítési munkafüzet](../conditional-access/howto-conditional-access-insights-reporting.md) a rendszergazdáknak az Azure ad feltételes hozzáférés összefoglaló nézetét adja meg a bérlőn. Az egyes szabályzatok kiválasztásának lehetőségével a rendszergazdák jobban megismerhetik, hogy az egyes szabályzatok hogyan és valós időben figyelik a módosításokat. A munkafüzet a Azure Monitorban tárolt adatstreameket adatfolyamként tárolja, amelyeket néhány percen belül megadhat az [utasításokat követve](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Az irányítópult könnyebben felderíthetővé tételéhez áthelyezte az Azure AD feltételes hozzáférés menüjének új megállapítások és jelentéskészítés lapjára.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>A feltételes hozzáférésre vonatkozó szabályzat részletei panel nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
A **termék képességei:** Identitás-biztonsági & védelme

Az új [szabályzat részletei](../conditional-access/troubleshoot-conditional-access.md) panelen a feltételes hozzáférési szabályzat kiértékelése során megjelenő hozzárendelések, feltételek és vezérlőelemek láthatók. A panelt úgy érheti el, ha kiválasztja a feltételes hozzáférés vagy csak jelentési lapokon a bejelentkezési adatokat tartalmazó sort.

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
 
Mostantól elérhető az Azure AD Galleryben az MS Graph API-k használatával történő alkalmazások létrehozásának és konfigurálásának támogatása. Ha SAML-alapú egyszeri bejelentkezést kell beállítania egy alkalmazás több példányához, időt takaríthat meg a Microsoft Graph API-k használatával, hogy [automatizálja az SAML-alapú egyszeri bejelentkezés konfigurációját](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Új kiépítési összekötők az Azure AD Application Galleryben – 2020

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
**Termék képesség:** harmadik féltől származó integráció
 
Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

* [8x8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic a szervezettől](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](../app-provisioning/user-provisioning.md)használatával című témakört.

---

### <a name="saml-token-encryption-is-generally-available"></a>Az SAML-jogkivonat titkosítása általánosan elérhető

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
Az [SAML-jogkivonat titkosítása](../manage-apps/howto-saml-token-encryption.md) lehetővé teszi, hogy az alkalmazások a titkosított SAML-kijelentések fogadására legyenek konfigurálva. A szolgáltatás mostantól általánosan elérhető minden felhőben.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Általánosan elérhetők a csoportnév jogcímei az alkalmazási jogkivonatokban.

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
A **termék képességei:** SSO
 
A jogkivonatban kiadott csoportos jogcímek mostantól csak az alkalmazáshoz hozzárendelt csoportokra korlátozódnak.  Ez különösen akkor fontos, ha a felhasználók nagy számú csoport tagjai, és fennáll a kockázata, hogy túllépte a tokenek méretének korlátozását. Ezzel az új funkcióval általánosan elérhetővé válnak a [csoportok nevei a tokenekhez](../hybrid/how-to-connect-fed-group-claims.md) .
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>A munkanap visszaírási mostantól támogatja a munkahelyi telefonszám-attribútumok beállítását

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítés  
A **termék képességei:** Identitás-életciklus kezelése
 
Továbbfejlesztettük a munkanap visszaírási kiépítési alkalmazását, hogy mostantól támogassa a munkahelyi telefonszámok és a visszaírási-attribútumok számát. Az e-mailek és a felhasználónevek mellett most már beállíthatja, hogy a munkanap visszaírási kiépítési alkalmazás az Azure AD-ból a munkanapokra is áthaladjon. A telefonszámok visszaírási konfigurálásával kapcsolatos további információkért tekintse meg a [munkanap visszaírási](../saas-apps/workday-writeback-tutorial.md) alkalmazás oktatóanyagát. 

---

### <a name="publisher-verification-preview"></a>Közzétevő ellenőrzése (előzetes verzió)

**Írja be a következőt:** Új funkció  
**Szolgáltatás kategóriája:** Más  
A **termék képességei:** Fejlesztői élmény
 
A kiadói ellenőrzés (előzetes verzió) segít a rendszergazdáknak és a végfelhasználóknak megérteni az alkalmazások fejlesztőinek a Microsoft Identity platformmal való integrálásának eredetiségét. További részletekért tekintse meg a [kiadói ellenőrzés (előzetes verzió)](../develop/publisher-verification-overview.md)című témakört.
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Engedélyezési kód folyamatábrája egyoldalas alkalmazásokhoz

**Írja be a következőt:** A szolgáltatás **szolgáltatási kategóriája megváltozott:** hitelesítési **termék képesség:** fejlesztői felület

A modern böngésző [harmadik féltől származó cookie-korlátozásai (például a Safari itp](../develop/reference-third-party-cookies-spas.md)) miatt a fürdőhelyeknek az engedélyezési kód folyamatát kell használniuk, nem pedig az SSO fenntartására szolgáló implicit folyamatot. A MSAL.js v 2. x mostantól támogatja az engedélyezési kód folyamatát. A Azure Portalnek megfelelő frissítésekkel rendelkezik, így frissítheti a SPA-t a "Spa" típusra, és használhatja az Auth Code flow-t. Útmutatásért tekintse meg a gyors üzembe helyezési útmutatót [: Jelentkezzen be a felhasználókba, és szerezzen be egy hozzáférési jogkivonatot egy JavaScript Spa-beli hitelesítési kód használatával](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Az eszközök továbbfejlesztett szűrése nyilvános előzetes verzióban érhető el

**Írja be a következőt:** Módosított funkció   
**Szolgáltatás kategóriája:** Eszközkezelés **termék képesség:** eszközök életciklusának kezelése
 
Korábban a használni kívánt szűrők "engedélyezve" és "tevékenység dátuma" volt. Most már [szűrheti az eszközök listáját több tulajdonságra](../devices/device-management-azure-portal.md#device-list-filtering-preview), például az operációs rendszer típusára, a csatlakozás típusára, a megfelelőségre és egyéb lehetőségekre. Ezeknek a kiegészítéseknek egyszerűbbé kell tenniük egy adott eszköz megkeresését.

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

További információért látogasson el [a Azure ad B2C új alkalmazás-regisztrációs felületére](../../active-directory-b2c/app-registrations-training-guide.md).

---
