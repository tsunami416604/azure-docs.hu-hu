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
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea6e654037e545a1b969aa2a9b41245fb2892bff
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273825"
---
# <a name="whats-new-in-azure-active-directory"></a>Újdonságok az Azure Active Directoryban?

>Értesítést kaphat arról, hogy mikor kell újra megkeresni ezt az oldalt a frissítésekhez az URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` -cím ![másolásával és beillesztésével: az RSS-hírcsatorna olvasójának ikonját](./media/whats-new/feed-icon-16x16.png) a hírcsatorna-olvasóba.

Az Azure AD folyamatosan fejlesztései kap. Naprakész a legújabb fejlemények, ez a cikk azt ismerteti kapcsolatban:

- A legújabb kiadásaihoz.
- Ismert problémák
- Hibajavítások
- Elavult funkciók
- Módosítások tervek

Ezen a lapon havonta frissül, így rendszeresen ellenőrizni. Ha hat hónapnál régebbi elemeket keres, az archívumban találhatja meg a [Azure Active Directory újdonságait](whats-new-archive.md).

---

## <a name="august-2019"></a>Augusztus 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Tervezze meg a változást: A Power BI Content Packs elavult

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

A 2019. október 1-től kezdődően a Power BI megkezdi az összes tartalomforrás érvénytelenítését, beleértve az Azure AD Power BI Content Pack csomagot is. A Content Pack alternatívájaként az Azure AD-munkafüzetek használatával betekintést nyerhet az Azure AD-hez kapcsolódó szolgáltatásaiba. További munkafüzetek érkeznek, beleértve a feltételes hozzáférési szabályzatokkal kapcsolatos munkafüzeteket a csak jelentés módban, az alkalmazások belefoglalási és egyéb információk alapján.

További információ a munkafüzetekről: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). További információ a tartalmi csomagok érvénytelenítéséről: a [Bejelentési Power bi sablon alkalmazásai általános elérhetősége](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) blogbejegyzés.

---

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>A csoportok továbbfejlesztett keresése, szűrése és rendezése az Azure AD portálon érhető el (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

Örömmel jelentjük be, hogy az Azure AD-portálon elérhetővé tettük a bővített csoportok kapcsolódó felhasználói élményének nyilvános előzetes verzióját. Ezek a fejlesztések megkönnyítik a csoportok és a tagok névsorának jobb kezelését a következő biztosításával:

- Speciális keresési funkciók, például alsztringek keresése a csoportok listájára.
- Speciális szűrési és rendezési beállítások a tag és a tulajdonos listán.
- Új keresési funkciók a tagok és a tulajdonosok listájához.
- A nagyméretű csoportok pontosabb csoportosítási száma.

További információ: [csoportok kezelése a Azure Portalban](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>Új egyéni szerepkörök érhetők el az alkalmazás-regisztrálási felügyelethez (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** RBAC  
**A termék képességei:** Hozzáférés-vezérlés

Az Azure AD P1 vagy P2 előfizetéssel elérhető egyéni szerepkörök mostantól részletes hozzáférést biztosíthatnak, ha a szerepkör-definíciókat meghatározott engedélyekkel hozza létre, majd hozzárendeli a szerepköröket adott erőforrásokhoz. Jelenleg egyéni szerepköröket hozhat létre az alkalmazások regisztrálásának kezeléséhez szükséges engedélyekkel, majd hozzárendelheti a szerepkört egy adott alkalmazáshoz. További információ az egyéni szerepkörökről: [Egyéni rendszergazdai szerepkörök Azure Active Directoryban (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview).

Ha további engedélyekre vagy erőforrásokra van szüksége, amelyek jelenleg nem láthatók, küldjön visszajelzést az [Azure feedback-webhelyre](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032) , és felvesszük a kérést az Update Road-térképre.

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>Az új kiépítési naplók segítségével figyelheti és elháríthatja az alkalmazások üzembe helyezésének központi telepítését (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Identitáséletciklus-menedzsment

Az új kiépítési naplók segítenek a felhasználók és csoportok üzembe helyezésének figyelésében és hibakeresésében. Ezek az új naplófájlok a következőkkel kapcsolatos információkat tartalmazzák:

- A [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial) sikeresen létrehozott csoportok
- Milyen szerepkörök lettek importálva a [Amazon Web Servicesból (AWS)](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- Milyen alkalmazottak lettek importálva a [munkanapokból](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)

További információ: [jelentések kiépítési jelentései a Azure Active Directory portálon (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs).

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>Új biztonsági jelentések az összes Azure AD-rendszergazda számára (általánosan elérhető)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitás-biztonsági & védelme

Alapértelmezés szerint az összes Azure AD-rendszergazda hamarosan hozzáférhet a modern biztonsági jelentésekhez az Azure AD-n belül. Szeptember végéig a modern biztonsági jelentések tetején található szalagcím használatával visszatérhet a régi jelentésekhez.

A modern biztonsági jelentések további képességeket biztosítanak a régebbi verziókról, többek között a következőkről:

- Speciális szűrés és rendezés
- Tömeges műveletek, például a felhasználói kockázat elvetése
- Sérült vagy biztonságos entitások megerősítése
- Kockázati állapot, amely a következőkre terjed ki: Veszélyeztetett, elvetett, szervizelt és megerősített sérült
- Új kockázattal kapcsolatos észlelések (prémium szintű Azure AD előfizetők számára elérhető)

További információ: [kockázatos felhasználók](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report), [kockázatos bejelentkezések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report)és [kockázati észlelések](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections-report).

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>A felhasználó által hozzárendelt felügyelt identitás elérhető Virtual Machines és Virtual Machine Scale Sets (általánosan elérhető)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure-erőforrások felügyelt identitásai  
**A termék képességei:** Fejlesztői környezet

A felhasználó által hozzárendelt felügyelt identitások mostantól általánosan elérhetők Virtual Machines és Virtual Machine Scale Sets számára. Ennek részeként az Azure olyan identitást hozhat létre az Azure AD-bérlőben, amelyet a használatban lévő előfizetés megbízhatónak tekint, és hozzárendelhető egy vagy több Azure-szolgáltatáshoz. A felhasználó által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](https://aka.ms/azuremanagedidentity)című témakört.

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>A felhasználók a Mobile App vagy a Hardware token (általánosan elérhető) használatával állíthatják vissza a jelszavukat

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék képességei:** Felhasználók hitelesítése

Azok a felhasználók, akik regisztráltak egy mobil alkalmazást a szervezettel, most visszaállíthatják a saját jelszavukat, ha jóváhagyják a Microsoft Authenticator alkalmazásból érkező értesítést, vagy megadhatnak egy kódot a Mobile App vagy a Hardware token használatával.

További információkért lásd [: Hogyan működik? Azure AD önkiszolgáló jelszó-visszaállítás](https://aka.ms/authappsspr). További információ a felhasználói élményről: [saját munkahelyi vagy iskolai jelszó átállítása – áttekintés](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview).

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>A ADAL.NET figyelmen kívül hagyja a MSAL.NET megosztott gyorsítótárat a következő esetekben:

**Típusa** Rögzített  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

Az Azure AD Authentication Library (ADAL.NET) verziójának 5.0.0-előzetes verziójától kezdődően az alkalmazások fejlesztőinek [a Web Apps és a webes API-k esetében egy gyorsítótárat kell szerializálni](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). Ellenkező esetben előfordulhat, hogy egyes forgatókönyvek, amelyek a (z) [-](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)t használja, és bizonyos használati `UserAssertion`esetekkel rendelkeznek, a jogosultság megemelését eredményezhetik. A biztonsági rés elkerülése érdekében a ADAL.NET mostantól figyelmen kívül hagyja a Microsoft hitelesítési függvénytárát a DotNet (MSAL.NET) megosztott gyorsítótárához a következő esetekben:.

További információ erről a hibáról: [Azure Active Directory a hitelesítési függvénytár jogosultságszint-emelését érintő biztonsági rés](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – augusztus 2019

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2019 augusztusában a következő 26 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Civic platform](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial), [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial), [](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial) [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial), [Viareport 's Inativ portál (Európa)](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial), [Azure Databricks](https://azure.microsoft.com/services/databricks), [Robin](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial), [Academy részvétel](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial), [prioritási mátrix](https://sync.appfluence.com/pmwebng/), [Cousto MySpace](https://cousto.platformers.be/account/login), [Uploadcare](https://uploadcare.com/accounts/signup/), [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial), [CPQSync by Cincom](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial), [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial), [kézbesítés. Media™ portál](https://portal.deliver.media), [Frontline Education](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial), [F5](https://www.f5.com/products/security/access-policy-manager), [stashcat ad összekapcsolás](https://www.stashcat.com), [Pislogás](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial), [Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial) [, a](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial) [Sigstr](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial), a [Darwinbox](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial), [a Watch by Colors](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial), a [hám](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial), a [Emődi stratégiai gondozása](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>A AzureAD PowerShell és a AzureADPreview PowerShell-modulok új verziói érhetők el

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Egyéb  
**A termék képességei:** Címtár

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

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

A Azure Portal elérhetővé tettük a dinamikus csoportházirend-szerkesztőt, amely megkönnyíti az új szabályok egyszerűbb beállítását, vagy a meglévő szabályok módosítását. Ez a kialakítás lehetővé teszi, hogy csak egy legfeljebb öt kifejezéssel hozzon létre szabályokat. Az eszköz tulajdonságainak listáját is frissítettük, hogy eltávolítsa az elavult eszköz tulajdonságait.

További információ: a [dinamikus tagsági szabályok kezelése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-update-rule).

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>A hozzáférési felülvizsgálatokkal használható új Microsoft Graph alkalmazás engedélyezése

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék képességei:** Identity Governance

Bevezetünk egy új Microsoft Graph alkalmazás engedélyt, `AccessReview.ReadWrite.Membership`amely lehetővé teszi, hogy az alkalmazások automatikusan hozzanak létre és kérjenek hozzáférési felülvizsgálatokat a csoporttagságok és az alkalmazás-hozzárendelések számára. Ezt az engedélyt az ütemezett feladatok vagy az automatizálás részeként használhatja, anélkül, hogy bejelentkezett felhasználói környezetre lenne szükség.

További információkért tekintse meg a következő [példát: Azure ad hozzáférési felülvizsgálatok létrehozása Microsoft Graph app permissions with PowerShell blog használatával](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241).

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Az Azure AD-Tevékenységnaplók mostantól elérhetők a kormányzati Felhőbeli példányok számára Azure Monitor

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy az Azure AD-beli adatnaplók mostantól elérhetők a Azure Monitor kormányzati Felhőbeli példányaihoz. Most már küldhet Azure AD-naplókat a Storage-fiókjába vagy egy Event hub-ba az SIEM-eszközökkel, például a [Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic), a [splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)és a [ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)való integráláshoz. 

A Azure Monitor beállításával kapcsolatos további információkért lásd: [Az Azure ad-tevékenység naplófájljai Azure monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations).

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>Frissítse a felhasználókat az új, fokozott biztonsági információkkal kapcsolatos felhasználói élményre

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:**  Hitelesítések (bejelentkezések)   
**A termék képességei:** Felhasználók hitelesítése

2019. szeptember 25-én kikapcsoljuk a régi, nem továbbfejlesztett biztonsági információkat a felhasználói biztonsági adatok regisztrálásához és kezeléséhez, és csak az új, [továbbfejlesztett verzió](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)bekapcsolását. Ez azt jelenti, hogy a felhasználók többé nem fogják tudni használni a régi felhasználói élményt.

A fokozott biztonsági információkkal kapcsolatos további információkért tekintse meg a [rendszergazdai dokumentációt](https://aka.ms/securityinfodocs) és a [felhasználói dokumentációt](https://aka.ms/securityinfoguide).

#### <a name="to-turn-on-this-new-experience-you-must"></a>Az új felhasználói élmény bekapcsolásához a következőket kell tennie:

1. Jelentkezzen be a Azure Portal globális rendszergazdaként vagy felhasználói rendszergazdaként.

2. Nyissa meg **Azure Active Directory > felhasználói beállítások > a hozzáférési panel előzetes verziójának beállításainak kezelése lehetőséget**.

3. A **felhasználók használhatják az előzetes verziójú funkciókat a biztonsági adatok regisztrálásához és kezeléséhez – bővített** terület, válassza a **kijelölt**lehetőséget, majd válasszon ki egy felhasználói csoportot, vagy válassza az **összes** lehetőséget a szolgáltatás bekapcsolásához a bérlő összes felhasználója számára.

4. A * * felhasználók használhatják az előzetes verziójú funkciókat a Security * * info * * * * terület regisztrálásához és kezeléséhez, válassza a **nincs**lehetőséget.

5. Mentse a beállításokat.

    A beállítások mentése után már nem fog tudni hozzáférni a régi biztonsági információkhoz.

>[!Important]
>Ha nem hajtja végre ezeket a lépéseket a 2019. szeptember 25. előtt, a Azure Active Directory bérlője automatikusan engedélyezve lesz a továbbfejlesztett felhasználói élményhez. Ha kérdése van, kérjük, lépjen kapcsolatba velünk registrationpreview@microsoft.coma következő címen:.

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>A POST bejelentkezéseket használó hitelesítési kérelmeket a rendszer szigorúan érvényesíti

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Szabványok

2019. szeptember 2-ától kezdődően a POST metódust használó hitelesítési kérelmeket a rendszer szigorúan érvényesíti a HTTP-szabványokkal szemben. A szóközök és a kettős idézőjelek (") nem lesznek eltávolítva a kérelmek űrlapjának értékeiből. Ezeknek a változásoknak nem kell megszüntetniük a meglévő ügyfeleket, és meg kell győződniük arról, hogy az Azure AD-ba küldött kérelmek minden alkalommal megbízhatóan kezelhetők.

További információ: az [Azure ad Breaking Changes – megjegyzések](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored).

---

## <a name="july-2019"></a>Július 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Tervezze meg a változást: Alkalmazásproxy-szolgáltatás frissítése csak TLS 1,2-támogatáshoz

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés

Ha segítségre van szüksége a legerősebb titkosításhoz, megkezdjük az alkalmazásproxy szolgáltatás elérésének korlátozását csak a TLS 1,2 protokollokhoz. Ez a korlátozás kezdetben a TLS 1,2 protokollokat használó ügyfelek számára lesz bevezetve, így a hatás nem fog megjelenni. A TLS 1,0 és a TLS 1,1 protokollok teljes elavulása a 2019. augusztus 31-én fejeződik be. A TLS 1,0-et és a TLS 1,1-t használó ügyfeleink speciális értesítést kapnak a változás előkészítéséhez.

Annak érdekében, hogy az alkalmazásproxy szolgáltatáshoz való csatlakozás megmaradjon a változás során, javasoljuk, hogy az ügyfél-kiszolgáló és a böngészőalapú kiszolgálók kombinációit a TLS 1,2 használatára frissítse. Azt is javasoljuk, hogy minden olyan ügyfélszoftvert tartalmazzon, amelyet az alkalmazottak az alkalmazásproxy szolgáltatáson keresztül közzétett alkalmazások eléréséhez használnak.

További információ: helyszíni [alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Tervezze meg a változást: A terv frissítései az alkalmazás-katalógushoz jönnek

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** SSO

Új felhasználói felületi változások jönnek létre a **Hozzáadás** az **alkalmazás hozzáadása** panel katalógus területéről. Ezek a változások könnyebben megtalálják az alkalmazásokat, amelyek támogatják az automatikus kiépítés, az OpenID Connect, a Security Assertion Markup Language (SAML) és a jelszavas egyszeri bejelentkezést (SSO).

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Tervezze meg a változást: Az MFA-kiszolgáló IP-címének eltávolítása az Office 365 IP-címről

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** MFA  
**A termék képességei:** Identitás-biztonsági & védelme

A rendszer eltávolítja az MFA-kiszolgáló IP-címét az [Office 365 IP-címe és az URL-webszolgáltatás webszolgáltatásból](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Ha ezekkel az oldalakkal frissíti a tűzfalbeállítások frissítését, meg kell győződnie arról, hogy az első lépések során az **Azure multi-Factor Authentication-kiszolgáló Firewall követelmények** című szakaszban dokumentált IP-címek listáját is tartalmazza-e. [ Az Azure Multi-Factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) cikkében.

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Az alkalmazáshoz tartozó tokenek mostantól megkövetelik az ügyfélalkalmazás létezését az erőforrás-bérlőben

**Típusa** Rögzített  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

2019. július 26-án módosítjuk, hogy az [ügyfél hitelesítő adataival](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)hogyan biztosítjuk az alkalmazáshoz tartozó jogkivonatokat. Korábban az alkalmazások jogkivonatokat kérhetnek más alkalmazások meghívásához, függetlenül attól, hogy az ügyfélalkalmazás a bérlőben volt-e. Frissítettük ezt a viselkedést, így az egybérlős erőforrásokat – más néven webes API-kat – csak az erőforrás-bérlőben található ügyfélalkalmazások hívhatják meg.

Ha az alkalmazás nem az erőforrás-bérlőben található, hibaüzenet jelenik meg, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` amely szerint a probléma megoldásához létre kell hoznia az ügyfélalkalmazás-szolgáltatást a bérlőben, a [rendszergazdai engedélyezési végponttal](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) vagy a [PowerShell használatával. ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), amely biztosítja, hogy a bérlő a bérlőn belül működjön az alkalmazás engedélyével.

További információ: [Újdonságok a hitelesítéshez](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Az ügyfél és az API közötti meglévő beleegyezés továbbra sem szükséges. Az alkalmazásoknak továbbra is saját engedélyezési ellenőrzéseket kell végezniük.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Új jelszó nélküli bejelentkezés az Azure AD-be az FIDO2 biztonsági kulcsainak használatával

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

Az Azure AD-ügyfelek mostantól szabályzatokat állíthatnak be a szervezet felhasználóinak és csoportjainak FIDO2 biztonsági kulcsainak kezeléséhez. A végfelhasználók emellett saját maguk is regisztrálhatják biztonsági kulcsaikat, a kulcsokkal bejelentkezhetnek a Microsoft-fiókjaikat a webhelyekre, miközben a-kompatibilis eszközökön vannak, valamint bejelentkezhetnek az Azure AD-hez csatlakoztatott Windows 10-es eszközökre.

További információkért lásd: a [jelszó nélküli bejelentkezés engedélyezése az Azure ad-ben (előzetes verzió)](/azure/active-directory/authentication/concept-authentication-passwordless) a rendszergazda által kapcsolatos információkhoz, valamint biztonsági adatok beállítása a végfelhasználókkal kapcsolatos információk [biztonsági kulcsának (előzetes verzió) használatához](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) .

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Új összevont alkalmazások érhetők el Azure AD alkalmazás-katalógusban – július 2019

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2019 júliusában az alábbi 18 új alkalmazást bővítettük az alkalmazás-katalógusban található összevonási támogatással:

[Ungerboeck szoftver](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), okos [minta Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [okos Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO hozzáférés Ethidex megfelelőségi iroda™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Absztrakt](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [átfedés](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wanda](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), i2B- [kapcsolat](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [beli jfrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** & Jelentéskészítés figyelése

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Tárcsázz](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont könyvtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használatával című témakört.

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>A hálózati biztonsági csoport új Azure AD Domain Services szolgáltatásának címkéje

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
**A termék képességei:** Azure AD Domain Services

Ha nem tudja kezelni az IP-címek és tartományok hosszú listáját, az Azure hálózati biztonsági csoportjában található új **AzureActiveDirectoryDomainServices** hálózati szolgáltatás címkével biztonságossá teheti a Azure ad Domain Services virtuális hálózatra irányuló bejövő forgalmat hálózati alhálózat.

További információ erről az új szolgáltatási címkéről: [hálózati biztonsági csoportok Azure ad domain Serviceshoz](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás a Azure AD Domain Serviceshoz (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
**A termék képességei:** Azure AD Domain Services

Örömmel jelentjük be, hogy megjelent az Azure AD tartományi szolgáltatások biztonsági naplózásának nyilvános előzetes verziója. A biztonsági naplózás segít a hitelesítési szolgáltatásokhoz való kritikus betekintésben, ha a biztonsági naplózási eseményeket a célként megadott erőforrások, például az Azure Storage, az Azure Log Analytics-munkaterületek és az Azure Event hub használatával biztosítja az Azure AD tartományi szolgáltatással portál.

További információ: [Azure ad Domain Services biztonsági naplózásának engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Új hitelesítési módszerek a használat & a bepillantást (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Önkiszolgáló jelszóátállítás  
**A termék képességei:** & Jelentéskészítés figyelése

Az új hitelesítési módszerek használati & a betekintő jelentések segítségével megismerheti, hogy az Azure Multi-Factor Authentication és az önkiszolgáló jelszó-visszaállítási funkció hogyan legyen regisztrálva és használatban a szervezetében, beleértve a regisztrált az egyes szolgáltatásokhoz tartozó felhasználók, hogy milyen gyakran használják az önkiszolgáló jelszó-visszaállítást a jelszavak alaphelyzetbe állítására, és hogy az Alaphelyzetbe állítás milyen módon történik.

További információ: [hitelesítési módszerek használata & információk (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Új biztonsági jelentések érhetők el az összes Azure AD-rendszergazda számára (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitás-biztonsági & védelme

Az összes Azure AD-rendszergazda mostantól kiválaszthatja a meglévő biztonsági jelentések (például a kockázati jelentésre **megjelölt felhasználók** ) felső részén látható szalagcímet, hogy megkezdje az új biztonsági élmény használatát a **kockázatos felhasználók** és a **kockázatos bejelentkezési** jelentések alapján. . Az idő múlásával az összes biztonsági jelentés a régebbi verziókról az új verzióra kerül, és az új jelentések a következő további képességeket biztosítják:

- Speciális szűrés és rendezés

- Tömeges műveletek, például a felhasználói kockázat elvetése

- Sérült vagy biztonságos entitások megerősítése

- Kockázati állapot, amely a következőkre terjed ki: Veszélyeztetett, elvetett, szervizelt és megerősített sérült

További információ: [kockázatos felhasználók jelentés](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users-report) és [kockázatos bejelentkezések jelentés](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Új biztonsági naplózás a Azure AD Domain Serviceshoz (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Domain Services  
**A termék képességei:** Azure AD Domain Services

Örömmel jelentjük be, hogy megjelent az Azure AD tartományi szolgáltatások biztonsági naplózásának nyilvános előzetes verziója. A biztonsági naplózás segít a hitelesítési szolgáltatásokhoz való kritikus betekintésben, ha a biztonsági naplózási eseményeket a célként megadott erőforrások, például az Azure Storage, az Azure Log Analytics-munkaterületek és az Azure Event hub használatával biztosítja az Azure AD tartományi szolgáltatással portál.

További információ: [Azure ad Domain Services biztonsági naplózásának engedélyezése (előzetes verzió)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Új B2B közvetlen összevonás SAML/WS-fed használatával (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2B  
**A termék képességei:** B2B/B2C

A közvetlen összevonás segít megkönnyíteni az olyan partnerekkel való munkavégzést, akik felügyelt identitási megoldását nem az Azure AD, az SAML-vagy WS-fed szabványokat támogató Identity Systems használatával. Miután létrehozott egy közvetlen összevonási kapcsolatot egy partnerrel, az adott tartományból meghívó új vendég-felhasználók a meglévő szervezeti fiókkal dolgozhatnak, így a vendégek zökkenőmentesen használhatják a felhasználói élményt.

További információ: [közvetlen összevonás AD FS és külső szolgáltatókkal a vendég felhasználók számára (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** & Jelentéskészítés figyelése

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Tárcsázz](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Összevont könyvtár](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)használatával című témakört.

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Ismétlődő csoportok nevének új keresése az Azure AD-portálon

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

Most, amikor létrehoz vagy frissít egy csoportnevet az Azure AD-portálról, akkor ellenőrizze, hogy van-e duplikálva egy meglévő csoportnév az erőforrásban. Ha azt állapítjuk meg, hogy a nevet már egy másik csoport használja, a rendszer megkéri, hogy módosítsa a nevét.

További információ: [csoportok kezelése az Azure ad-portálon](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Az Azure AD mostantól támogatja a statikus lekérdezési paramétereket a válasz (átirányítás) URI-k között.

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

Az Azure ad-alkalmazások most már regisztrálhatják és használhatják a válasz-(átirányítási) URI-ket `https://contoso.com/oauth2?idp=microsoft`statikus lekérdezési paraméterekkel (például) a OAuth 2,0-kérelmek esetében. A statikus lekérdezési paraméter a válasz URI azonosítójának megfelelő karakterláncra vonatkozik, ugyanúgy, mint a válasz URI más része. Ha nincs olyan regisztrált karakterlánc, amely megfelel az URL-cím-dekódolású átirányítás-URI azonosítónak, a rendszer elutasítja a kérelmet. Ha a válasz URI-ja megtalálható, a rendszer a teljes karakterláncot használja a felhasználó átirányítására, beleértve a statikus lekérdezési paramétert is.

A dinamikus válasz URI-k továbbra is tiltottak, mert biztonsági kockázatot jelentenek, és nem használhatók az állapotadatok megőrzésére a hitelesítési kérelem során. Erre a célra használja a `state` paramétert.

Jelenleg a Azure Portal alkalmazás-regisztrációs képernyői továbbra is letiltják a lekérdezési paramétereket. Az alkalmazás jegyzékfájlját azonban manuálisan is szerkesztheti, ha lekérdezési paramétereket ad hozzá az alkalmazáshoz. További információ: [Újdonságok a hitelesítéshez](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Az Azure AD-hez készült Tevékenységnaplók (MS Graph API-k) mostantól a PowerShell-parancsmagok használatával érhetők el

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy az Azure AD-tevékenységek naplói (naplózási és bejelentkezési jelentések) mostantól elérhetők az Azure AD PowerShell-modulon keresztül. Korábban létrehozhat saját parancsfájlokat MS Graph API-végpontok használatával, és most már kiterjesztjük ezt a képességet a PowerShell-parancsmagokra.

További információ a parancsmagok használatáról: [Azure ad PowerShell-parancsmagok jelentéskészítéshez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Frissített szűrési vezérlők az Azure AD naplózási és bejelentkezési naplóihoz

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Frissítettük a naplózási és bejelentkezési naplózási jelentéseket, így mostantól különböző szűrőket alkalmazhat anélkül, hogy azokat oszlopként kellene hozzáadni a jelentés képernyőjén. Emellett most már eldöntheti, hogy hány szűrőt szeretne megjeleníteni a képernyőn. Ezek a frissítések mind együtt működnek, hogy a jelentések könnyebben olvashatók legyenek, és több hatókört is igénybe lehessen venni.

További információ ezekről a frissítésekről: a [naplózási naplók szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) és [a bejelentkezési tevékenységek szűrése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Június 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Új riskDetections API a Microsoft Graphhoz (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitás-biztonsági & védelme

Örömmel jelentjük be, hogy a Microsoft Graph új riskDetections API már nyilvános előzetes verzióban érhető el. Ezzel az új API-val megtekintheti a szervezete személyazonosságának védelmével kapcsolatos felhasználói és bejelentkezési kockázati észlelések listáját. Ezzel az API-val hatékonyabban kérdezheti le a kockázati észleléseket, beleértve az észlelési típussal, az állapottal, a szinttel és egyebekkel kapcsolatos részleteket is.

További információ: [kockázatkezelési API-dokumentáció](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – június 2019

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

2019 júniusában a következő 22 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Azure ad SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML-hitelesítés](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise os](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Kiskereskedelmi merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, a PeopleSoft Oracle IDCS, Oracle IDCS for JD Edwards

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Felhasználói fiókok üzembe helyezésének automatizálása az újonnan támogatott SaaS-alkalmazásokhoz

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** & Jelentéskészítés figyelése

Mostantól automatizálhatja az újonnan integrált alkalmazások felhasználói fiókjainak létrehozását, frissítését és törlését:

- [Nagyítás](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Különmegbízottja](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Ha többet szeretne megtudni arról, hogyan javíthatja a szervezetét a felhasználói fiókok automatikus kiépítésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) használatával című témakört.

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Az Azure AD-kiépítési szolgáltatás valós idejű előrehaladásának megtekintése

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Identitáséletciklus-menedzsment

Frissítettük az Azure AD üzembe helyezési élményét, hogy szerepeljen egy új folyamatjelző sáv, amely megmutatja, hogy meddig tart a felhasználó kiépítési folyamata. Ez a frissített élmény az aktuális ciklusban kiosztott felhasználók számával, valamint a dátummal rendelkező felhasználók számával kapcsolatos információkat is tartalmazza.

További információ: [a felhasználó kiépítési állapotának ellenõrzése](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>A céges arculat mostantól megjelenik a kijelentkezés és a hibaüzenetek képernyőjén

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

Frissítettük az Azure AD-t, hogy a cég arculata mostantól megjelenjen a kijelentkezés és a hibák képernyőjén, valamint a bejelentkezési oldalon. A funkció bekapcsolásához semmit nem kell tennie, az Azure AD egyszerűen azokat az eszközöket használja, amelyeket már beállított a Azure Portal **vállalati védjegyezés** területén.

A vállalat arculatának beállításával kapcsolatos további információkért lásd: [branding hozzáadása a szervezet Azure Active Directory oldalaihoz](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Az Azure Multi-Factor Authentication-(MFA-) kiszolgáló már nem érhető el az új üzemelő példányokhoz

**Típusa** Elavult  
**Szolgáltatás kategóriája:** MFA  
**A termék képességei:** Identitás-biztonsági & védelme

2019. július 1-től a Microsoft már nem kínál új, az MFA-kiszolgálót az új üzemelő példányokhoz. A többtényezős hitelesítést a szervezetében megkövetelő új ügyfeleknek most már a felhőalapú Azure-Multi-Factor Authentication kell használniuk. Az MFA-kiszolgálót július 1. előtt aktivált ügyfelek nem fogják látni a változást. Továbbra is letöltheti a legújabb verziót, lekérheti a jövőbeli frissítéseket, és előállíthatja az aktiválási hitelesítő adatokat.

További információ: [Bevezetés az Azure multi-Factor Authentication-kiszolgáló](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)használatába. További információ a felhőalapú Azure-Multi-Factor Authenticationről: [felhőalapú Azure-beli multi-Factor Authentication üzembe helyezésének tervezése](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>2019. május

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Szolgáltatás módosítása: Az Application proxy szolgáltatásban csak a TLS 1,2 protokollok jövőbeli támogatása

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés

A legjobb titkosítás biztosítása érdekében ügyfeleinknek biztosítjuk, hogy csak a TLS 1,2 protokollokra korlátozza a hozzáférést az alkalmazásproxy szolgáltatásban. Ezt a változást fokozatosan a TLS 1,2 protokollokat használó ügyfeleknek kell bevezetni, így nem kell semmilyen változást látnia.

A TLS 1,0 és a TLS 1,1 elavult változata a 2019-as augusztus 31-én történik, de további speciális értesítéseket is biztosítunk, így a változás előkészítéséhez időt kell igénybe vennie. A módosítás előkészítéséhez győződjön meg arról, hogy az ügyfél és a böngésző kiszolgálói kombinációja, beleértve azokat az ügyfeleket, amelyeket a felhasználók az Application proxyn keresztül közzétett alkalmazások elérésére használnak, frissülnek a TLS 1,2 protokoll használatával az alkalmazással létesített kapcsolat fenntartásához. Proxy szolgáltatás. További információ: helyszíni [alkalmazás hozzáadása a táveléréshez az alkalmazásproxy használatával Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Az alkalmazással kapcsolatos bejelentkezési adatok megtekintéséhez használja a használati és elemzési jelentést.

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** & Jelentéskészítés figyelése

Mostantól használhatja a Azure Portal **vállalati alkalmazások** területén található használati és elemzési jelentést a bejelentkezési adatok alkalmazás-központú nézetének beszerzéséhez, beleértve a következőkkel kapcsolatos információkat:

- A szervezet leggyakrabban használt alkalmazásai

- A legtöbb sikertelen bejelentkezést tartalmazó alkalmazások

- Leggyakoribb bejelentkezési hibák az egyes alkalmazásokhoz

A szolgáltatással kapcsolatos további információkért tekintse [meg a használati és adatáttekintési jelentést a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report) .

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>A felhasználók üzembe helyezésének automatizálása a Cloud apps szolgáltatásban az Azure AD használatával

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** & Jelentéskészítés figyelése

Ezeket az új oktatóanyagokat követve az Azure AD kiépítési szolgáltatásával automatizálhatja a következő felhőalapú alkalmazások felhasználói fiókjainak létrehozását, törlését és frissítését:

- [Megfelelés](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

Ezt az új [Dropbox-oktatóanyagot](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)is követheti, amely a csoport objektumainak kiépítésével kapcsolatos információkat tartalmaz.

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)című témakört.

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Az identitások biztonságos pontszáma mostantól elérhető az Azure AD-ben (általános elérhetőség)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** –  
**A termék képességei:** Identitás-biztonsági & védelme

Most már nyomon követheti és javíthatja az identitás biztonsági állapotát az Azure AD biztonságos pontszám funkciója segítségével. Az Identitáskezelés biztonságos pontszáma funkció egyetlen irányítópultot használ, amely a következőket nyújtja:

- Az azonosító biztonsági helyzet tárgyilagos mérése az 1 és 223 közötti pontszám alapján.

- Tervezze meg az identitás biztonsági frissítéseit

- Tekintse át a biztonsági tökéletesítések sikerességét

További információ a személyazonossági biztonsági pontszám funkcióról: [Mi az Identity Secure pontszám a Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Mostantól elérhető az új Alkalmazásregisztrációki élmény (általános rendelkezésre állás)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Fejlesztői környezet

Az új [Alkalmazásregisztrációk](https://aka.ms/appregistrations) élmény már általánosan elérhető. Ez az új felhasználói élmény magában foglalja a Azure Portal és az alkalmazás regisztrációs portáljának összes fontos funkcióját, és a következőkön keresztül fejleszti őket:

- **Hatékonyabb alkalmazások kezelése.** Az alkalmazások különböző portálokon való megjelenítése helyett mostantól egyetlen helyen láthatja az összes alkalmazást.

- **Egyszerűsített alkalmazás regisztrálása.** A továbbfejlesztett navigációs felületről a kijavított engedélyek kiválasztásának folyamata révén könnyebben regisztrálhatók és kezelhetők az alkalmazások.

- **Részletesebb információ.** Az alkalmazással kapcsolatos további részleteket, például a gyors útmutatókat és egyebeket is megtalálhatja.

További információ: a [Microsoft Identity platform](https://docs.microsoft.com/azure/active-directory/develop/) és a [Alkalmazásregisztrációk-élmény már általánosan elérhető!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) blog bejelentése.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>A kockázatos felhasználók API-ban a személyazonosság védelme érdekében elérhető új képességek

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identitásvédelem  
**A termék képességei:** Identitás-biztonsági & védelme

Örömmel jelentjük, hogy mostantól a kockázatos felhasználók API-val lekérheti a felhasználók kockázati előzményeit, elhagyhatja a kockázatos felhasználókat, és megerősítheti a felhasználókat a feltörtek szerint. Ezzel a módosítással hatékonyabban frissítheti a felhasználók kockázati állapotát, és megismerheti a kockázati Előzményeiket.

További információkért tekintse meg a [kockázatos felhasználók API-referenciájának dokumentációját](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Az Azure AD App Galleryben elérhető új összevont alkalmazások – 2019. május

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

A 2019-es verzióban a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [valós hivatkozások](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [egyszerű aláírás](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [forrasztás](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [lejátszó](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo értékesítések](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [rendszerek](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 globális HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Munkahely](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [kobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Továbbfejlesztett csoportok létrehozása és kezelése az Azure AD-portálon

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

Továbbfejlesztettük az Azure AD-portálon a csoportokkal kapcsolatos tapasztalatokat. Ezen fejlesztésekkel a rendszergazdák hatékonyabban kezelhetik a csoportok listáját, a tagok listáját, és további létrehozási lehetőségeket biztosíthatnak.

Fejlesztések a következők:

- Alapszintű szűrés a tagság típusa és a csoport típusa alapján.

- Új oszlopok, például forrás-és e-mail-cím hozzáadása.

- A csoportok, a tagok és a tulajdonosi listák többszörös kiválasztásának lehetősége az egyszerű törléshez.

- Lehetőség van e-mail-cím kiválasztására és a tulajdonosok hozzáadására a csoportok létrehozása során.

További információkért lásd: [alapszintű csoport létrehozása és Tagok hozzáadása Azure Active Directory használatával](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (általánosan elérhető)

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>A Microsoft Graph API-végpontok mostantól elérhetők az Azure AD-tevékenységek naplóihoz (általános elérhetőség)

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Örömmel jelentjük be, hogy Microsoft Graph API-végpontok támogatásának általános elérhetősége az Azure AD-tevékenységek naplóiban. Ebben a kiadásban mostantól az Azure AD-naplók 1,0-es verzióját, valamint a bejelentkezési naplók API-jait is használhatja.

További információ: az [Azure ad naplózási naplójának API áttekintése](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>A rendszergazdák mostantól feltételes hozzáférést is használhatnak a kombinált regisztrációs folyamathoz (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás-biztonsági & védelme  

A rendszergazdák mostantól feltételes hozzáférési szabályzatokat hozhatnak létre a kombinált regisztrációs lap általi használatra. Ez magában foglalja a szabályzatok alkalmazását a regisztráció engedélyezéséhez, ha:

- A felhasználók megbízható hálózaton vannak.

- A felhasználók alacsony bejelentkezési kockázatot jelentenek.

- A felhasználók felügyelt eszközön találhatók.

- A felhasználók elfogadják a szervezet használati feltételeit (felhasználási feltételek).

A feltételes hozzáféréssel és a jelszó-visszaállítással kapcsolatos további információkért tekintse meg az [Azure ad kombinált MFA és a jelszó-változtatási regisztrációs élmény blogbejegyzésének feltételes hozzáférését](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). A kombinált regisztrációs folyamat feltételes hozzáférési házirendjeivel kapcsolatos további információkért lásd a [feltételes hozzáférési szabályzatok a kombinált regisztrációhoz](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)című témakört. További információ az Azure AD használati feltételeiről: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Április 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Az Azure AD veszélyforrások felderítésének új észlelése mostantól a Azure AD Identity Protection részeként érhető el

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Azure AD Identity Protection  
**A termék képességei:** Identitás-biztonsági & védelme

Az Azure AD Threat Intelligence észlelése mostantól a frissített Azure AD Identity Protection funkció részeként érhető el. Ez az új funkció lehetővé teszi, hogy szokatlan felhasználói tevékenységet jelezzen egy adott felhasználó vagy tevékenység számára, amely konzisztens a Microsoft belső és külső veszélyforrások elleni hírszerzési forrásai alapján ismert támadási mintákkal.

A Azure AD Identity Protection frissített verziójával kapcsolatos további információkért tekintse meg a [négy jelentős Azure ad Identity Protection fejlesztést a nyilvános előzetes verzióban](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) , a mi az [Azure Active Directory Identity Protection (frissítve)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) cikk. Az Azure AD veszélyforrások észlelésével kapcsolatos további információkért tekintse meg a [Azure Active Directory Identity Protection kockázati észlelésekkel](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) foglalkozó cikket.

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Az Azure AD-jogosultságok kezelése mostantól elérhető (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Identity Governance  
**A termék képességei:** Identity Governance

A nyilvános előzetes verzióban elérhető Azure AD-jogosultsági felügyelet segít az ügyfeleknek a hozzáférési csomagok felügyeletének delegálásában, amely meghatározza, hogy az alkalmazottak és az üzleti partnerek hogyan igényelhetnek hozzáférést, jóvá kell hagynia, és hogy mennyi ideig férhetnek hozzá. A hozzáférési csomagok kezelhetik az Azure AD és az Office 365-csoportok tagságát, a vállalati alkalmazásokban lévő szerepkör-hozzárendeléseket, valamint a SharePoint Online-webhelyek szerepkör-hozzárendeléseit. További információ a jogosultságok kezeléséről az [Azure ad-jogosultságok kezelésének áttekintése című](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)témakörben olvasható. Ha többet szeretne megtudni a Azure AD Identity Governance-funkciók széles köréről, beleértve a Privileged Identity Management, a hozzáférési felülvizsgálatokat és a használati feltételeket, tekintse meg a [Mi az Azure ad Identity Governance?](../governance/identity-governance-overview.md)című témakört.

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Az Office 365-csoportok elnevezési szabályzatának konfigurálása az Azure AD Portalon (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

A rendszergazdák mostantól az Azure AD-portál használatával konfigurálhatják az Office 365-csoportok elnevezési szabályzatát. Ez a változás segít kikényszeríteni a szervezet felhasználói által létrehozott vagy szerkesztett Office 365-csoportok konzisztens elnevezési konvencióit.

Az Office 365-csoportok elnevezési házirendjét kétféleképpen állíthatja be:

- Adja meg az előtagokat vagy az utótagokat, amelyeket a rendszer automatikusan hozzáad a csoport nevéhez.

- Feltöltheti a szervezete számára a tiltott szavak testreszabott készletét, amely nem engedélyezett a csoportok neveiben (például "VEZÉRIGAZGATÓ, bérszámfejtés, HR").

További információ: az [Office 365-csoportok elnevezési szabályzatának érvényesítése](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Az Azure AD-Tevékenységnaplók mostantól Azure Monitorban érhetők el (általánosan elérhető)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Az Azure AD-tevékenység naplóival kapcsolatos vizualizációk visszajelzésének megkönnyítése érdekében a Log Analytics új elemzési funkciót vezetünk be. Ez a funkció segít az Azure AD-erőforrásokkal kapcsolatos elemzések megszerzésében interaktív sablonjaink használatával, az úgynevezett munkafüzeteknél. Ezek az előre elkészített munkafüzetek részletes adatokat biztosíthatnak az alkalmazásokhoz és a felhasználókhoz, és a következőket tartalmazzák:

- **Bejelentkezések.** Részletes információkat nyújt az alkalmazásokról és a felhasználókról, beleértve a bejelentkezési helyet, a használatban lévő operációs rendszert vagy böngésző-ügyfelet és-verziót, valamint a sikeres vagy sikertelen bejelentkezések számát.

- **Örökölt hitelesítés és feltételes hozzáférés.** A régi hitelesítést használó alkalmazások és felhasználók részletes adatait tartalmazza, beleértve a feltételes hozzáférési házirendek által aktivált, feltételes hozzáférési házirendeket használó alkalmazások és így tovább Multi-Factor Authentication használatát.

- **Sikertelen bejelentkezések elemzése.** Segít megállapítani, hogy a bejelentkezési hibák egy felhasználói művelet, házirend-problémák vagy az infrastruktúra miatt történnek-e.

- **Egyéni jelentések.** Létrehozhat új, vagy szerkesztheti a meglévő munkafüzeteket, amelyek segítségével testre szabhatja a szervezete által használt adatellenőrzési funkciót.

További információ: [Azure monitor munkafüzetek használata Azure Active Directory jelentésekhez](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Új összevont alkalmazások érhetők el az Azure AD App Galleryben – április 2019

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

Április 2019-én a következő 21 új alkalmazást adtuk hozzá az alkalmazás-katalógusban az összevonási támogatással:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks egyszeri bejelentkezés](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [átszivárog](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [padok](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085) [, PageDNA,](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial) [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio-kapcsolat ](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel-kapcsolat](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (szerepköralapú SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [Monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [Surveymonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Új hozzáférési felülvizsgálatok gyakoriságának beállítása és több szerepkör kiválasztása

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hozzáférési felülvizsgálatok  
**A termék képességei:** Identity Governance

Az Azure AD hozzáférési felülvizsgálatok új frissítései a következőket teszik lehetővé:

- A korábbi, heti, havi, negyedéves és évente meglévő lehetőségeken felül a hozzáférési felülvizsgálatok gyakoriságát **is módosíthatja**félévente.

- Egyetlen hozzáférési felülvizsgálat létrehozásakor több Azure AD-és Azure-erőforrás-szerepkört is kijelölhet. Ebben az esetben minden szerepkör ugyanazokkal a beállításokkal van beállítva, és minden felülvizsgáló értesítést kap egyszerre.

A hozzáférési felülvizsgálat létrehozásával kapcsolatos további információkért lásd: [csoportok vagy alkalmazások hozzáférési felülvizsgálatának létrehozása az Azure ad hozzáférési](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)felülvizsgálatokban.

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect e-mail-riasztási rendszer (ek) át lettek irányítva, és minden ügyfél számára új e-mail feladói adatokat küldenek

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
**A termék képességei:** Platform

Azure AD Connect az e-mail-riasztási rendszer (ek) átváltásának folyamata, amely egy új e-mail-feladót mutat be. Ennek megoldásához hozzá kell adnia `azure-noreply@microsoft.com` a szervezet engedélyezési listájához, vagy nem fogja tudni fogadni az Office 365, az Azure vagy a Sync Services fontos riasztásait.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Az UPN-utótag módosításai mostantól sikeresek az összevont tartományok között Azure AD Connect

**Típusa** Rögzített  
**Szolgáltatás kategóriája:** AD-szinkronizáló  
**A termék képességei:** Platform

Most már sikeresen módosíthatja egy felhasználó UPN-utótagját az egyik összevont tartományból egy másik összevont tartományba Azure AD Connectban. Ez a javítás azt jelenti, hogy a szinkronizálási ciklusban többé nem tapasztalja a FederatedDomainChangeError hibaüzenetet, vagy értesítést kap a következőről: "nem lehet frissíteni ezt az objektumot Azure Active Directory, mert az attribútum [ A FederatedUser. UserPrincipalName] érvénytelen. Frissítse az értéket a helyi címtárszolgáltatások szolgáltatásban.

További információ: [hibaelhárítási hibák a szinkronizálás során](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Fokozott biztonság az App Protection-alapú feltételes hozzáférési szabályzattal az Azure AD-ben (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás-biztonsági & védelme

Az App Protection-alapú feltételes hozzáférés mostantól az alkalmazás- **védelmi házirend megkövetelése** eszközzel érhető el. Ez az új szabályzat segít a szervezet biztonságának növelésében azáltal, hogy megakadályozza a következőket:

- A felhasználók Microsoft Intune licenc nélkül férhetnek hozzá az alkalmazásokhoz.

- A felhasználók nem tudnak Microsoft Intune app Protection-szabályzatot beolvasni.

- A felhasználók a konfigurált Microsoft Intune app Protection-házirend nélkül szereznek hozzáférést az alkalmazásokhoz.

További információkért lásd: [alkalmazás-védelmi szabályzat megkövetelése a Cloud app Accesshez feltételes hozzáféréssel](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Új támogatás az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez a Microsoft Edge-ben (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás-biztonsági & védelme

Bővítettük a Microsoft Edge-hez készült Azure AD-támogatást, amely új támogatást nyújt az Azure AD egyszeri bejelentkezéshez és a feltételes hozzáféréshez. Ha korábban már használta Microsoft Intune Managed Browser, a Microsoft Edge-t használhatja helyette.

Az eszközök és alkalmazások feltételes hozzáférés használatával történő beállításával és kezelésével kapcsolatos további információkért lásd: [felügyelt eszközök megkövetelése a felhőalapú alkalmazásokhoz való hozzáféréshez feltételes](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) hozzáféréssel, valamint [jóváhagyott ügyfélalkalmazások megkövetelése a Cloud app Accesshez feltételes hozzáféréssel ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). További információ arról, hogyan kezelhető a hozzáférés a Microsoft Edge használatával Microsoft Intune házirendekkel: az [Internet-hozzáférés kezelése Microsoft Intune házirenddel védett böngésző használatával](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Március 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Az identitási élmény keretrendszere és az egyéni szabályzatok támogatása Azure Active Directory B2C már elérhető (GA)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** B2C – fogyasztói identitások kezelése  
**A termék képességei:** B2B/B2C

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

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Vállalati alkalmazások  
**A termék képességei:** Külső integráció

Március 2019-án a következő 14 új alkalmazást bővítettük az alkalmazás-katalógusban az összevonási támogatással:

[ISEC7 Mobile Exchange-delegált](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [magyarázat-alapú naplózási rendszer](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [sovány](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool teljesítményű ügyek](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Iris intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [Tas](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Az alkalmazásokkal kapcsolatos további információkért lásd: [SaaS integrációja az Azure Active Directoryval](https://aka.ms/appstutorial). Az alkalmazás szerepeltetése az Azure AD-alkalmazásgyűjtemény ajánlati kapcsolatos további információkért lásd: [az alkalmazás szerepeltetése az Azure Active Directory alkalmazáskatalógusában](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Új Zscaler-és Atlassian-létesítési összekötők az Azure AD-galériában – március 2019

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Alkalmazás kiépítése  
**A termék képességei:** Külső integráció

A következő alkalmazásokhoz tartozó felhasználói fiókok létrehozásának, frissítésének és törlésének automatizálása:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler három](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Ha többet szeretne megtudni arról, hogyan védheti meg a szervezetét a felhasználói fiókok automatikus üzembe helyezésével, tekintse meg a felhasználók kiépítésének [automatizálása SaaS-alkalmazásokhoz az Azure ad-vel](https://aka.ms/ProvisioningDocumentation)című témakört.

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Törölt Office 365-csoportok visszaállítása és kezelése az Azure AD-portálon

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Csoportkezelés  
**A termék képességei:** Együttműködés

Most már megtekintheti és kezelheti a törölt Office 365-csoportokat az Azure AD-portálon. Ez a módosítás segít megtekinteni, hogy mely csoportok állíthatók vissza a visszaállításhoz, és hogy véglegesen törli azokat a csoportokat, amelyek nem szükségesek a szervezet számára.

További információ: [lejárt vagy törölt csoportok visszaállítása](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Az egyszeri bejelentkezés mostantól elérhető az Azure AD SAML-védelemmel ellátott helyszíni alkalmazásokhoz az Application proxyn keresztül (nyilvános előzetes verzió)

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** App Proxy  
**A termék képességei:** Hozzáférés-vezérlés

Mostantól egyszeri bejelentkezéses (SSO) élményt biztosíthat a helyszíni, SAML-hitelesítésen alapuló alkalmazásokhoz, valamint az alkalmazások távoli elérését az Application proxyn keresztül. Az SAML SSO helyszíni alkalmazásokkal való beállításával kapcsolatos további információkért lásd: [SAML egyszeri bejelentkezés a helyszíni alkalmazásokhoz alkalmazásproxy (előzetes verzió)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)használatával.

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>A megbízhatóság és a felhasználói élmény fokozása érdekében a kérelemben szereplő ügyfélalkalmazások megszakadnak.

**Típusa** Új funkció  
**Szolgáltatás kategóriája:** Hitelesítések (bejelentkezések)  
**A termék képességei:** Felhasználók hitelesítése

Az ügyfélalkalmazások nem tudják megfelelően kiadni több száz azonos bejelentkezési kérést rövid idő alatt. Ezek a kérések, akár sikeresek, akár nem, mind hozzájárulnak a gyenge felhasználói élményhez és a megnövekedett munkaterhelésekhez a IDENTITÁSSZOLGÁLTATÓ, az összes felhasználó késésének növeléséhez és a IDENTITÁSSZOLGÁLTATÓ rendelkezésre állásának csökkentéséhez.

Ez a frissítés `invalid_grant` hibaüzenetet küld `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` : olyan ügyfélalkalmazások számára, amelyek rövid idő alatt többször is kiadják a duplikált kérelmeket, a normál működés hatókörén kívül. A problémát tapasztaló ügyfélalkalmazások interaktív üzenetet jelenítenek meg, amely megköveteli, hogy a felhasználó újra bejelentkezzen. További információ erről a változásról, valamint arról, hogy az alkalmazás Hogyan oldható meg, ha ez a hiba ütközik: Újdonságok [a hitelesítéshez?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Új naplózási naplók – felhasználói élmény most elérhető

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Létrehoztunk egy új Azure AD- **naplókat** tartalmazó oldalt, amely segít az olvashatóság és az információk megkeresésének javításában. Ha meg szeretné tekinteni **az új naplók** lapot, válassza a **naplók** lehetőséget az Azure ad **tevékenység** szakaszában.

![Új naplózási naplók oldal, a minta adataival](media/whats-new/audit-logs-page.png)

További információ az új **naplók** lapról: [naplózási tevékenység jelentései a Azure Active Directory portálon](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Új figyelmeztetések és útmutatás a nem megfelelően konfigurált feltételes hozzáférési szabályzatok véletlen rendszergazdai zárolásának megakadályozásához

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Identitás-biztonsági & védelme

Annak megakadályozása érdekében, hogy a rendszergazdák véletlenül zárolják magukat a saját bérlőik nem megfelelően konfigurált feltételes hozzáférési szabályzatokkal, új figyelmeztetéseket hoztunk létre, és frissítettük az útmutatót a Azure Portal. Az új útmutatással kapcsolatos további információkért lásd: [Mi a szolgáltatás függőségei a Azure Active Directory feltételes hozzáférésben](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Jobb végfelhasználói használati feltételek a mobileszközök esetében

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Használati feltételek  
**A termék képességei:** Cégirányítás

Frissítettük a meglévő használati feltételeket, amelyek segítségével javítható a mobileszközök használati feltételeinek áttekintése és belefoglalása. Most nagyíthatja és kibővítheti, visszatérhet, letöltheti az adatokat, és kiválaszthatja a hiperhivatkozásokat. A frissített használati feltételekkel kapcsolatos további információkért lásd: [Azure Active Directory használati feltételek szolgáltatás](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Az Azure AD-beli új tevékenység naplóinak letöltési felülete elérhető

**Típusa** Megváltozott funkció  
**Szolgáltatás kategóriája:** Jelentéskészítés  
**A termék képességei:** & Jelentéskészítés figyelése

Most már nagy mennyiségű tevékenységet is letölthet közvetlenül a Azure Portalból. Ez a frissítés a következőket teszi lehetővé:

- Legfeljebb 250 000 sort tölthet le.

- Értesítést kap a letöltés befejeződése után.

- Szabja testre a fájlnevet.

- Határozza meg a kimeneti formátumot JSON-ként vagy CSV-ként.

További információ erről a szolgáltatásról: gyors [útmutató: Naplózási jelentés letöltése a Azure Portal használatával](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Kompatibilitástörő változás: Az Exchange ActiveSync (EAS) által a feltételek kiértékelésének frissítései

**Típusa** Tervezett módosítás  
**Szolgáltatás kategóriája:** Feltételes hozzáférés  
**A termék képességei:** Hozzáférés-vezérlés

Folyamatban van az Exchange ActiveSync (EAS) az alábbi feltételek kiértékelésének frissítése:

- Felhasználói hely, ország, régió vagy IP-cím alapján

- Bejelentkezési kockázat

- Eszközplatform

Ha korábban már használta ezeket a feltételeket a feltételes hozzáférési házirendekben, vegye figyelembe, hogy a feltétel viselkedése változhat. Ha például korábban a felhasználói hely feltételét használta egy házirendben, akkor a rendszer a felhasználó helye alapján kihagyhatja a házirendet.

---
