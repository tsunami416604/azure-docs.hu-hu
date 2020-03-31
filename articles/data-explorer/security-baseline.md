---
title: Azure-biztonsági alapkonfiguráció az adatkezelőhöz
description: Azure-biztonsági alapkonfiguráció az adatkezelőhöz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 92a607e4c17a16d07f07c97f626ed98a213d509b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289718"
---
# <a name="azure-security-baseline-for-data-explorer"></a>Azure-biztonsági alapkonfiguráció az adatkezelőhöz

Az Azure Security Baseline for Data Explorer javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Az Azure Data Explorer támogatja a fürt üzembe helyezését egy alhálózatba a virtuális hálózatban. Ez a funkció lehetővé teszi a hálózati biztonsági csoport (NSG) szabályainak kényszerítését az Azure Data Explorer fürtforgalmában, csatlakoztathatja a helyszíni hálózatot az Azure Data Explorer fürt alhálózatához, és biztonságossá teszi az adatkapcsolati forrásokat (Event Hub és Event Grid) szolgáltatásvégpontok.

Az Azure Data Explorer-fürt telepítése virtuális hálózatba:https://docs.microsoft.com/azure/data-explorer/vnet-deployment


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és a NICS konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplóit, és küldjön naplókat egy tárfiókba a forgalom naplózása érdekében.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Az Azure Security Center által biztosított hálózati biztonság ismertetése:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: A kritikus fontosságú webes alkalmazások védelme

**Útmutatás**: Nem alkalmazható; A javaslat az Azure App Service-szolgáltatáson vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Engedélyezze az Azure DDoS Protection Standard ot a Data Explorer-fürtök et a DDoS-támadások elleni védelem érdekében a virtuális hálózaton. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A DDoS-védelem konfigurálása:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Engedélyezze a folyamatnaplókat az Azure Data Explorer-fürt védelmére használt hálózati biztonsági csoportokon (NSG), és küldjön naplókat egy tárfiókba a forgalom naplózása érdekében.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Hálózatalapú behatolásérzékelő/behatolásmegelőző rendszerek telepítése

**Útmutatás**: Nem alkalmazható; Ez a vezérlő a végponton vagy a tűzfalon történik.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A virtuális hálózati szolgáltatás címkék segítségével határozza meg a hálózati hozzáférés-vezérlésa a hálózati biztonsági csoportok vagy az Azure Data Explorer-fürtöktársított Azure Data Explorer-fürtök hálózati hozzáférés-vezérlés. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

A szolgáltatáscímkék ismertetése és használata:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Szolgáltatáscímkék konfigurációs követelményei az Azure Data Explorer számára:https://docs.microsoft.com/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Az Ügyfél az Azure Policy használatával szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg a hálózati erőforrásokhoz.

Az ügyfél is használhatja az Azure Blueprints egyszerűsítésére nagyszabású Azure-központi telepítések csomagolásával kulcsfontosságú környezeti összetevők, például arm sablonok, RBAC-vezérlők és szabályzatok, egyetlen tervezet definíciójában. Egyszerűen alkalmazhatja a tervezetet az új előfizetésekre és környezetekre, és finomíthatja a vezérlést és a felügyeletet a verziószámozással.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentum forgalom konfigurációs szabályok

**Útmutató:** Címkék használata hálózati biztonsági csoportok (NSG) és egyéb, a hálózati biztonsággal és a forgalommal kapcsolatos erőforrások hoz létre az Adatkezelő-fürtökhöz. Az egyes NSG-szabályok esetében a "Leírás" mezőben adhatja meg az üzleti igényt és/vagy az időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló/onnan érkező forgalmat.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-szabályzat használatával érvényesítheti (és/vagy kiigazítja) a hálózati erőforrások konfigurációját.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizálási források használata

**Útmutató: A**Microsoft időforrásokat tart fenn az Azure-erőforrásokhoz, az ügyfelek frissíthetik az ügyfél tulajdonában lévő számítási telepítések időszinkronizálását.

Az Azure számítási erőforrásainak időszinkronizálásának konfigurálása:https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure Data Explorer diagnosztikai naplókat használ a betöltési sikeresés kudarcokkal kapcsolatos elemzésekhez. A műveletnaplók at exportálhatja az Azure Storage, az Event Hub vagy a Log Analytics szolgáltatásba a betöltési állapot figyeléséhez.

Az Azure Data Explorer betöltési műveleteinek figyelése:

https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

A figyelési adatok betöltése és lekérdezése az Azure Data Explorerben:

https://docs.microsoft.com/azure/data-explorer/ingest-data-no-code

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Naplózás engedélyezése az Azure Resources számára

**Útmutató:** Engedélyezze az Azure Data Explorer diagnosztikai beállításait a szolgáltatásspecifikus műveletekhez és naplózáshoz való hozzáféréshez és naplózáshoz. Az Azure-tevékenység naplók az Azure Monitoron belül, amely magában foglalja az erőforrás magas szintű naplózása alapértelmezés szerint engedélyezve van.

Az Azure Data Explorer betöltési műveleteinek figyelése:https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs

Platformnaplók és metrikák gyűjtése az Azure Monitor segítségével:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Az Azure platformnaplók áttekintése:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerből

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának konfigurálása

**Útmutató:** Az Azure Monitoron belül állítsa be a Log Analytics-munkaterület-megőrzési időszakot a szervezet megfelelőségi előírásainak megfelelően. Azure Storage-fiókok használata hosszú távú/archiválási tároláshoz.

A loganalytics-munkaterületek naplómegőrzési paramétereinek beállítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók nyomon követése és felülvizsgálata

**Útmutató:** Elemezze és figyelje a naplókat a rendellenes viselkedések hez, és rendszeresen tekintse át az eredményeket. Miután engedélyezte az Azure Data Explorer diagnosztikai beállításait, az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket a naplóadatokon.

A Naplóelemzési munkaterület ismertetése:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutatás**: Nem alkalmazható; Az Azure Data Explorer nem rendelkezik ezzel a képességgel.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó rendszer központosítása

**Útmutatás**: Nem alkalmazható; Az Azure Data Explorer nem dolgozza fel a kártevők elleni naplózást.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezés naplózásának engedélyezése

**Útmutató:** Nem alkalmazható: A DNS-lekérdezés nem az Azure Data Explorer függvénye.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Az igazgatási számlák leltárának karbantartása

**Útmutató:** Az Azure Data Explorerben a biztonsági szerepkörök határozzák meg, hogy mely rendszerbiztonsági tagok (felhasználók és alkalmazások) rendelkeznek engedéllyel egy biztonságos erőforráson, például egy adatbázison vagy egy táblán, és milyen műveletek engedélyezettek.  A Kusto-lekérdezés segítségével felsorolhatja az Azure Data Explorer-fürtök és -adatbázisok felügyeleti szerepkörében lévő alapelveket.
Biztonsági szerepkörök kezelése az Azure Data Explorerben a Kusto-lekérdezés használatával:https://docs.microsoft.com/azure/kusto/management/security-roles



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása adott esetben

**Útmutató:** Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmát. A jelszóhoz szükséges egyéb Azure-erőforrások bonyolultsági követelményekkel és minimális jelszóhosszal rendelkező jelszót hoznak létre, amely a szolgáltatástól függően eltérő. Ön felelős a harmadik féltől származó alkalmazásokért és piactéri szolgáltatásokért, amelyek alapértelmezett jelszavakat használhatnak.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: A dedikált adminisztratív számlák használatának biztosítása

**Útmutató**: Az Ügyfél szabványos működési eljárásokat hozhat létre a dedikált adminisztratív számlák használata körül. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát.

Az ügyfelek is engedélyezhetik a Just-In-Time / Just-Enough-Access az Azure AD kiemelt identitáskezelési kiemelt szerepkörök a Microsoft Services és az Azure ARM használatával. 

Mi az Azure AD kiemelt identitáskezelés?:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) kihasználása az Azure Active Directoryval

**Útmutató:** Ahol csak lehetséges, az ügyfél az Egyszeri használat az Azure Active Directory (Azure AD) használatával, ahelyett, hogy szolgáltatásonként egyéni önálló hitelesítő adatokat konfigurálna. Használja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az SSO ismertetése az Azure AD-vel:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Használja a többtényezős hitelesítés t az összes Azure Active Directory-alapú hozzáféréshez.

**Útmutató:** Engedélyezze az Azure Active Directory (Azure AD) többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted 

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-of-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (kiemelt hozzáférésű munkaállomások) használata minden adminisztratív feladathoz

**Útmutató:** Emelt szintű munkaállomások (kiemelt hozzáférésű munkaállomások) használata többtényezős hitelesítéssel (MFA) konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-on-administrative-accounts"></a>3.7: Napló és riasztás az adminisztratív fiókokon végzett gyanús tevékenységekről

**Útmutató:** Használja az Azure Active Directory (Azure AD) biztonsági jelentések et a naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure Security Center használata az identitás- és hozzáférési tevékenységek figyeléséhez

Kockázatos tevékenységesetén megjelölt Azure AD-felhasználók azonosítása:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitásának és hozzáférési tevékenységének figyelése az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-identity-access


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Az Azure-erőforrás kezelése csak jóváhagyott helyekről

**Útmutató:** Az ügyfél feltételes hozzáféréssel ellátott helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezi a hozzáférést.

Elnevezett helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-utilize-azure-active-directory"></a>3.9: Az Azure Active Directory hasznosítása

**Útmutató:** Az Azure Active Directory (Azure AD) az Azure Data Explorer hitelesítésének előnyben részesített módja. Számos hitelesítési forgatókönyvet támogat:

Felhasználói hitelesítés (interaktív bejelentkezés): Az emberi egyszerű hitelesítésre szolgál.

Alkalmazáshitelesítés (nem interaktív bejelentkezés): Olyan szolgáltatások és alkalmazások hitelesítésére szolgál, amelyeknek emberi felhasználó nélkül kell futniuk/hitelesíteniük.

Az Azure Data Explorer hozzáférés-vezérlésének áttekintése:https://docs.microsoft.com/azure/kusto/management/access-control

Hitelesítés az Azure Active Directoryval:https://docs.microsoft.com/azure/kusto/management/access-control/aad



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory (Azure AD) naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access-vélemények használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználó hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel. 

Hogyan hitelesíthető az Azure AD-vel az Azure Data Explorer Access-hez:https://docs.microsoft.com/azure/kusto/management/access-control/how-to-authenticate-with-aad

Azure AD-jelentés:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity Access-vélemények használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató: Használhatja**az Azure Active Directory (Azure AD) Bejelentkezési tevékenység, naplózási és kockázati eseménynapló-források figyelése, amely lehetővé teszi, hogy integrálja a biztonsági információk és események kezelése (SIEM) / figyelési eszköz.

 Ezt a folyamatot egyszerűsítheti az Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, a naplónaplók és a bejelentkezési naplók elküldésével a Log Analytics-munkaterületre. Az ügyfél konfigurálja a kívánt riasztásokat a Log Analytics-munkaterületen belül.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiókbejelentkezési viselkedés eltéréséről

**Útmutató:** Az Azure Active Directory (Azure AD) kockázatészlelési és identitásvédelmi szolgáltatás használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Emellett további vizsgálat céljából adatokat is bevihet az Azure Sentinelbe.

Az Azure AD kockázatos bejelentkezései megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férnie az ügyféladatokhoz, az Ügyfélszéf egy felületet biztosít az ügyfelek számára az ügyféladatok-hozzáférési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához.

Az ügyfélszéf ismertetése:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: A bizalmas információk leltárának karbantartása

**Útmutató:** Címkék használatával segítséget nyújt a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követéséhez.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. Az Azure Data Explorer-fürtöket virtuális hálózat/alhálózat választja el más erőforrásoktól, megfelelően címkézve és egy hálózati biztonsági csoporton (NSG) vagy Az Azure Tűzfalon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó adatkezelő fürtöket megfelelően el kell különíteni.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Az Azure Data Explorer-fürt telepítése virtuális hálózatba:https://docs.microsoft.com/azure/data-explorer/vnet-deployment

Hogyan hozzunk létre egy NSG egy biztonsági config:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Figyelemmel kíséri és blokkolja a bizalmas adatok jogosulatlan továbbítását

**Útmutatás**: Nem alkalmazható; A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Minden bizalmas információ titkosítása a szállítás során

**Útmutató:** Az Azure Data Explorer fürt alapértelmezés szerint egyezteti a TLS 1.2-t. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek egyeztetni a TLS 1.2-es vagy annál nagyobb összeegyeztetését.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="45-divuse-an-active-discovery-tool-to-identify-sensitive-datadiv"></a>4.5: <div>Bizalmas adatok azonosítása aktív felderítési eszközzel</div>

**Útmutató:** Az Azure Data Explorer hez még nem érhetők el adatazonosítási, besorolási és veszteségmegelőzési funkciók. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="46-divuse-azure-rbac-to-control-access-to-resourcesdiv"></a>4.6: <div>Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést</div>

**Útmutató:** Az Azure Data Explorer lehetővé teszi az adatbázisokhoz és táblákhoz való hozzáférés szabályozását egy szerepköralapú hozzáférés-vezérlési (RBAC) modell használatával. Ebben a modellben a rendszerbiztonsági tagok (felhasználók, csoportok és alkalmazások) szerepkörökhöz vannak rendelve. A rendszerbiztonsági tagok a hozzárendelt szerepkörök nek megfelelően férhetnek hozzá az erőforrásokhoz.

Szerepkörök és engedélyek listája, valamint az RBAC Azure Data Explorer hez való konfigurálására vonatkozó utasítások:https://docs.microsoft.com/azure/data-explorer/manage-database-permissions


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagép-alapú adatveszteség-megelőzéshasználatával

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kezeli az Azure Data Explorer alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató: Az**Azure Disk Encryption segít megvédeni és megvédeni az adatokat, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségvállalásokat. Kötettitkosítást biztosít a fürt virtuális gépeinek operációs rendszeréhez és adatlemezeihez. Emellett integrálható az Azure Key Vault, amely lehetővé teszi számunkra, hogy ellenőrizzék és kezeljék a lemez titkosítási kulcsok és titkos kulcsok, és biztosítja, hogy a virtuális gép lemezeken lévő összes adat titkosítva van az Azure Storage-ban.

Az Azure Data Explorer-fürtök titkosításának engedélyezése:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure-figyelő és az Azure-tevékenységnapló használatával riasztásokat hozhat létre az Azure Data Explorer-fürtökerőforrás-szintű változásainak időpontjára vonatkozóan.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági résellenőrző eszközök futtatása

**Útmutató:** Kövesse az Azure Security Center ajánlásait az Azure Data Explorer-erőforrások védelmével kapcsolatban.

A Microsoft az Azure Data Explorert támogató mögöttes rendszereken is végez biztonsági réskezelést.

Ismerje meg az Azure Security Center rekedéseit:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Összehasonlítás back-to-back biztonsági rés vizsgál

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="55-utilize-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: A felfedezett biztonsági rések helyreállításának fontossági sorrendbe adására kockázatminősítési eljárást kell használni.

**Útmutatás:** Használja az Azure Security Center által biztosított alapértelmezett kockázati minősítéseket (Secure Score).
Ismerje meg az Azure Security Center biztonságos pontszámát:https://docs.microsoft.com/azure/security-center/security-center-secure-score


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-utilize-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery hasznosítása

**Útmutatás:** Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés(ek)en belül). Biztosítsa a megfelelő (olvasási) engedélyeket a bérlőben, és számba vesse az összes Azure-előfizetést, valamint az előfizetéseken belüli erőforrásokat.

Bár a klasszikus Azure-erőforrások fedezhetők fel a Resource Graph, erősen ajánlott az Azure Resource Manager erőforrásainak létrehozása és használata a jövőben.

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:https://docs.microsoft.com/azure/role-based-access-control/overview



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszköz metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása az Azure-erőforrásokra, amelyek metaadatokat adnak, hogy logikusan rendszerezzék őket egy taxonómiába.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Adott esetben megfelelő elnevezési konvenciókat, címkézést, felügyeleti csoportokat vagy külön előfizetéseket használhat az eszközök rendszerezéséhez és nyomon követéséhez. Az Azure Resource Graph segítségével rendszeresen egyeztetheti a készletet, és biztosíthatja, hogy a jogosulatlan erőforrások időben törlődjenek az előfizetésből. 

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása.

**Útmutató:** Létre kell hoznia egy leltárt a jóváhagyott Azure-erőforrásokról és jóváhagyott szoftverekről a számítási erőforrásokhoz a szervezeti igényeinek megfelelően.  


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató:** Az Azure-szabályzatok segítségével korlátozhatja az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusát a következő beépített szabályzatdefiníciók használatával:

    - Nem engedélyezett erőforrástípusok

    - Engedélyezett erőforrástípusok

A házirend által generált eseményeket a 

Tevékenységnaplók, amelyek az Azure Monitor használatával figyelhető.

Emellett használhatja az Azure Resource Graph lekérdezése/felderítése erőforrások az előfizetés(ek) belül.

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Rövid útmutató: Futtassa az első Resource Graph-lekérdezést az Azure Resource Graph Explorer használatával:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Tevékenységnapló-riasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra és az Azure egészére szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="68-utilize-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="69-utilize-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások hasznosítása

**Útmutató:** Az Azure-szabályzatok segítségével korlátozhatja az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusát a következő beépített szabályzatdefiníciók használatával:

    - Nem engedélyezett erőforrástípusok

    - Engedélyezett erőforrástípusok

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure-szabályzatminták:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resources Managerrel

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával. Ez megakadályozza az Azure-előfizetéseken belüli erőforrások létrehozását és módosításait. 

Az Azure-kezeléshez való hozzáférés kezelése feltételes hozzáféréssel:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazások

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutatás:** Azure-szabályzat aliasok használatával egyéni szabályzatok létrehozásához az Azure-erőforrások naplózása vagy kényszerítése. Beépített Azure-szabályzat-definíciókat is használhat.

Emellett az Azure Resource Manager képes a sablon exportálására javascript-objektumnotítás (JSON), amelyet felül kell vizsgálni annak érdekében, hogy a konfigurációk megfelelnek / meghaladják a szervezet biztonsági követelményeit.

Az Azure Security Center ajánlásait is használhatja az Azure-erőforrások biztonságos konfigurációs alapkonfigurációjaként.

Az elérhető Azure-szabályzataliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Oktatóanyag: Szabályzatok létrehozása és kezelése a megfelelőség kényszerítéséhez:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Egy- és többerőforrásos exportálás egy sablonba az Azure Portalon:https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal

Biztonsági ajánlások - referenciaútmutató:https://docs.microsoft.com/azure/security-center/recommendations-reference



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Biztonságos konfigurációk létrehozása az operációs rendszerhez

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Biztonságos konfigurációk karbantartása az összes Azure-erőforráshoz

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] biztonságos beállítások kényszerítése az Azure-erőforrások között.  Használhatja a megoldásokat, például a változáskövetés, a szabályzat megfelelőségi irányítópultja vagy egy egyéni megoldás segítségével könnyen azonosíthatja a környezetében bekövetkező biztonsági változásokat.

Az Azure-szabályzat hatásainak ismertetése:https://docs.microsoft.com/azure/governance/policy/concepts/effects

A megfelelőség érvényesítéséhez hozzon létre és kezeljen szabályzatokat:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Kövesse nyomon a környezet változásait a Változáskövetés megoldással:https://docs.microsoft.com/azure/automation/change-tracking

Az Azure-erőforrások megfelelőségi adatainak beszerezni:https://docs.microsoft.com/azure/governance/policy/how-to/get-compliance-data



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Biztonságos konfigurációk karbantartása az operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása az Azure-erőforrások konfigurálásában

**Útmutató:** Az Azure-adatfájlok segítségével biztonságosan tárolhatja és kezelheti a kódot, például az egyéni Azure-szabályzatokat, az Azure Resource Manager-sablonokat, a kívánt állapotkonfigurációs parancsfájlokat stb. Az Azure DevOps által kezelt erőforrások eléréséhez engedélyeket adhat vagy tagadhat meg adott felhasználóknak, beépített biztonsági csoportoknak vagy az Azure Active Directoryban (Azure AD) definiált csoportoknak, ha integrálva vannak az Azure DevOps-szal, vagy az Active Directoryt, ha integrálva vannak a TFS-sel.

Kód tárolása az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Engedélyek és csoportok az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/organizations/security/about-permissions



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: A rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** Az Azure-erőforrások szabványos biztonsági konfigurációinak definiálása és megvalósítása az Azure Policy használatával. Azure Policy aliasok használatával egyéni szabályzatok létrehozásához az Azure-erőforrások hálózati konfigurációjának naplózásához vagy érvényesítéséhez. Az adott erőforrásokhoz kapcsolódó beépített házirend-definíciókat is használhat.  Emellett használhatja az Azure Automation konfigurációs módosítások üzembe helyezéséhez.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Aliases használata:https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** Azure Policy aliasok használatával egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztásához, naplózásához és kényszerítéséhez. Az Azure-erőforrások konfigurációinak automatikus kényszerítéséhez használja az Azure-szabályzat [naplózás], [megtagadás] és [üzembe helyezés, ha nem létezik] használatával.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatikus konfigurációfigyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-securely-manage-azure-secrets"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure Disk Encryption kötettitkosítást biztosít az Azure Data Explorer fürt virtuális gépeinek operációs rendszeréhez és adatlemezeihez. Emellett integrálható az Azure Key Vault, amely lehetővé teszi a lemez titkosítási kulcsok és titkos kulcsok vezérlését és kezelését, és biztosítja, hogy a virtuális gép lemezein lévő összes adat inaktív módon titkosítva van, míg az Azure Storage-ban.

A fürt biztonságossá tétele az Azure Data Explorerben:https://docs.microsoft.com/azure/data-explorer/manage-cluster-security


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Biztonságosés automatikus annektálta a személyazonosságot

**Útmutató:** Felügyelt identitások használatával biztosítható az Azure-szolgáltatások automatikusan felügyelt identitást az Azure AD-ben. Felügyelt identitások lehetővé teszi, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatások, beleértve a Key Vault, hitelesítő adatok nélkül a kódot. Felügyelt identitások konfigurálása:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Felügyelt identitások konfigurálása az Azure Data Explorer-fürthöz:https://docs.microsoft.com/azure/data-explorer/managed-identities


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

**Útmutató:** A hitelesítő adatok at a kódon belüli hitelesítő adatok azonosítására valósíthatja meg. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault. 

A Hitelesítő adatok képolvasójának beállítása:https://secdevtools.azurewebsites.net/helpcredscan.html

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-utilize-centrally-managed-anti-malware-software"></a>8.1: Használja központilag kezelt kártevőirtó szoftver

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató gazdagépen (például az Azure Data Explorerben), azonban nem fut az ügyféltartalomon.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure Data Explorerben), azonban nem fut az ügyféltartalomon.

A nem számítási Azure-erőforrásokba feltöltött tartalmak, például az Azure Data Explorer, a Data Lake Storage, a Blob Storage, az Azure Database for PostgreSQL stb. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: A kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen, de nem fut az ügyféltartalmakon.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatot kell biztosítani

**Útmutató:** A Data Explorer-fürt által használt Microsoft Azure tárfiókban lévő adatok mindig replikálódnak a tartósság és a magas rendelkezésre állás biztosítása érdekében. Az Azure Storage úgy másolja az adatokat, hogy védve legyenek a tervezett és nem tervezett eseményektől, beleértve az átmeneti hardverhibákat, a hálózati vagy áramkimaradásokat és a súlyos természeti katasztrófákat. Dönthet úgy, hogy az adatokat ugyanazon az adatközponton belül, az ugyanazon a régión belüli zónaszintű adatközpontokban vagy földrajzilag elválasztott régiók között replikálja.

Az Azure Storage redundanciájának és szolgáltatásiszintű szerződésének ismertetése:https://docs.microsoft.com/azure/storage/common/storage-redundancy

Adatok exportálása a tárolóba:https://docs.microsoft.com/azure/kusto/management/data-export/export-data-to-storage



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés és biztonsági mentés bármely ügyfél által kezelt kulcsról

**Útmutató:** Az Azure Data Explorer titkosítja az összes adatot egy tárolófiók ban inaktív. Alapértelmezés szerint az adatok microsoftáltal kezelt kulccsal vannak titkosítva. A titkosítási kulcsok további szabályozásához megadhat ügyfél által felügyelt kulcsokat az adattitkosításhoz. Az ügyfél által felügyelt kulcsokat egy Azure Key Vaultban kell tárolni. 

Ügyfél által kezelt kulcsok konfigurálása C# használatával:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager

Az Azure Key Vault-tanúsítványok biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate?view=azurermps-6.13.0


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Rendszeresidőközönként tesztelje az Azure Key Vault-titkos kulcsok adatok visszaállítását.

Az Azure Key Vault-tanúsítványok visszaállítása:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate?view=azurermps-6.13.0 

Ügyfél által kezelt kulcsok konfigurálása C# használatával:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Az ügyfél engedélyezi a soft-delete a Key Vault ban, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törlés.  A kiürítési védelmet is engedélyezheti, így ha egy tároló vagy egy objektum törölt állapotban van, nem lehet törölni, amíg a megőrzési időszak le nem telt.  

A soft-delete és a purge védelem engedélyezése a Key Vaultban:https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

Ügyfél által kezelt kulcsok konfigurálása C# használatával:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-csharp

Ügyfél által kezelt kulcsok konfigurálása az Azure Resource Manager sablon használatával:https://docs.microsoft.com/azure/data-explorer/customer-managed-keys-resource-manager



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final

    



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás:** A Biztonsági központ minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

    Use tags to organize your Azure resources: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A biztonsági reagálási eljárások tesztelése

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Adja meg a biztonsági &nbsp;incidensek elérhetőségét, és állítsa be a riasztási értesítéseket a biztonsági eseményekhez

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az Ön adataihoz jogosulatlan vagy jogosulatlan fél fért hozzá. Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.
    

Az Azure Security Center biztonsági kapcsolattartójának beállítása:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center-riasztásokat és javaslatokat a Folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosításához. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötőt a riasztások streameléséhez az Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" szolgáltatáson keresztül a biztonsági riasztásokra és javaslatokra adott válaszokat az Azure-erőforrások védelme érdekében.
    

    How to configure Workflow Automation and Logic Apps: https://docs.microsoft.com/azure/security-center/workflow-automation



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelésaz Azure-erőforrásokon, és 60 napon belül biztosítani kell az összes kritikus biztonsági megállapítás elhárítását

**Útmutató:** Kérjük, kövesse a Microsoft kötelezettségvállalási szabályzatát, hogy https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1a behatolási tesztek ne sértsék meg a Microsoft irányelveit: .

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkat itt talál:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
