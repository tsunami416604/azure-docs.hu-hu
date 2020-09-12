---
title: Azure Monitor Azure biztonsági alapterve
description: A Azure Monitor biztonsági alapterve az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósítására szolgáló eljárási útmutatást és erőforrásokat biztosít.
author: msmbaldwin
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: a4b0cb9b53adbf6362d79766fcd7bce57f5441ac
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89322931"
---
# <a name="azure-security-baseline-for-azure-monitor"></a>Azure Monitor Azure biztonsági alapterve

Ez a biztonsági alapkonfiguráció az [Azure biztonsági teljesítménytesztre](../security/benchmarks/overview.md) vonatkozó útmutatást alkalmazza Azure monitorra. Az Azure biztonsági teljesítményteszt az Azure-beli felhőalapú megoldások biztonságossá tételével kapcsolatos ajánlásokat tartalmaz. A tartalom az Azure biztonsági teljesítményteszt által meghatározott **biztonsági vezérlők** szerint van csoportosítva, valamint a Azure monitor vonatkozó kapcsolódó útmutatás. A Azure Monitor nem alkalmazható **vezérlők** ki vannak zárva. Ha szeretné megtekinteni, hogyan Azure Monitor teljes mértékben leképezni az Azure biztonsági Teljesítménytesztét, tekintse meg a [teljes Azure monitor biztonsági alapterv-leképezési fájlt](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

Azure Monitor az Azure Core-szolgáltatások része, és a Azure Monitor szolgáltatás nem telepíthető külön szolgáltatásként. Azure Monitor összetevők üzembe helyezhetők az erőforrásokkal, és ez hatással lehet ezeknek az erőforrásoknak a biztonsági testtartására is.

## <a name="network-security"></a>Hálózati biztonság

*További információt az [Azure biztonsági teljesítményteszt: hálózati biztonság](/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure privát hivatkozásának engedélyezése az Azure SaaS-szolgáltatásokhoz (például Azure monitor) és az Azure által üzemeltetett ügyfél-és partneri szolgáltatásokhoz való hozzáférés engedélyezéséhez a virtuális hálózat egy privát végpontján keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget.

Ahhoz, hogy a forgalom elérje a Azure Monitor, a "AzureMonitor" szolgáltatás címkéit használva engedélyezze a bejövő és kimenő forgalmat a hálózati biztonsági csoportokon keresztül. Annak érdekében, hogy a rendelkezésre állás monitorozása Azure Monitor elérje a forgalmat, használja a "ApplicationInsightsAvailability" szolgáltatási címkét az összes bejövő forgalom számára a hálózati biztonsági csoportokon keresztül.

A virtuális hálózati szabályok lehetővé teszik, hogy a Azure Monitor csak a virtuális hálózaton belüli kijelölt alhálózatokból továbbított kommunikációt fogadják.

Log Analytics átjáró használata az adatLog Analytics-munkaterületre való adatküldéshez Azure Monitor azon számítógépek nevében, amelyek nem tudnak közvetlenül csatlakozni az internethez, ami megakadályozza, hogy a számítógépek csatlakozni tudjanak az internethez. 

- [Privát hivatkozás beállítása Azure Monitorhoz](platform/private-link-security.md)

- [Internet-hozzáférés nélküli számítógépek összekötése a Log Analytics átjáró használatával Azure Monitor](platform/gateway.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Azure monitor egy alapszolgáltatás, és nem támogatja közvetlenül a virtuális hálózatra történő telepítést, a mögöttes infrastruktúrát a Microsoft kezeli. Azonban olyan erőforrások esetében, amelyek hálózati kapcsolatot tesznek elérhetővé az Azure Monitor ajánlathoz, a hálózatot hálózati biztonsági csoporttal védik. Engedélyezze a hálózati biztonsági csoport folyamatábráit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. A flow-naplókat Log Analytics munkaterületre is elküldheti, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Ha a Azure Monitort privát hivatkozással használja, akkor a hálózati naplózáshoz, például a privát végpont által feldolgozott (vagy kimenő) adatkapcsolathoz férhet hozzá.

- [Azure Monitor ügynökök hálózati követelményei](platform/log-analytics-agent.md#network-requirements)

- [Internet-hozzáférés nélküli számítógépek összekötése a Log Analytics átjáró használatával Azure Monitor](platform/gateway.md)

- [Hálózati biztonsági csoport folyamatábráinak engedélyezése](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [A Traffic Analytics engedélyezése és használata](../network-watcher/traffic-analytics.md)

- [A Azure Security Center által biztosított hálózati biztonság ismertetése](../security-center/security-center-network-recommendations.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: ahhoz, hogy a forgalom elérje a Azure monitor, a "AzureMonitor" szolgáltatás címkéit használva engedélyezze a bejövő és kimenő forgalmat a hálózati biztonsági csoportokon keresztül. Annak érdekében, hogy a rendelkezésre állás monitorozása Azure Monitor elérje a forgalmat, használja a "ApplicationInsightsAvailability" szolgáltatási címkét az összes bejövő forgalom számára a hálózati biztonsági csoportokon keresztül. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

- [A szolgáltatási címkék megismerése és használata](../virtual-network/service-tags-overview.md) 

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: a Azure monitor az Azure alapszolgáltatások részét képezi, és nem telepíthető külön szolgáltatásként. Azure Monitor összetevőket, beleértve az Azure Monitor ügynököt, és a Application Insights SDK üzembe helyezhető az erőforrásaival, és ez hatással lehet az erőforrások biztonsági helyzetére.

- [Azure Monitor ügynökök hálózati követelményei](platform/log-analytics-agent.md#network-requirements)

- [Internet-hozzáférés nélküli számítógépek összekötése a Log Analytics átjáró használatával Azure Monitor](platform/gateway.md) 

- [Lásd: a Application Insights első lépései](//azure/azure-monitor/app/app-insights-overview#get-started)

- [Rendelkezésre állási webes tesztek beállítása](app/monitor-web-app-availability.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használatával figyelheti az erőforrás-konfigurációkat, és azonosíthatja a Azure monitorhoz kapcsolódó hálózati erőforrások módosításait. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrások módosításai megváltoznak.

- [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](/azure/azure-monitor/platform/activity-log-view)

- [Riasztások létrehozása a Azure Monitorban](platform/alerts-activity-log.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információt az [Azure biztonsági teljesítményteszt: naplózás és figyelés](/azure/security/benchmarks/security-control-logging-monitoring)című témakörben talál.*

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure monitor tevékenység-naplók használatával naplózzák az erőforrásaik változásait. Ezeket a naplókat az Azure Storage, az Event hub vagy egy Log Analytics munkaterületre is exportálhatja. Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. Azure Monitoron belül az adatokra vonatkozó elemzéseket végezhet, és az Azure Storage-fiókokat használhatja a naplók hosszú távú/archiválási tárolásához.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](platform/diagnostic-settings.md)

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](learn/quick-collect-azurevm.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a Azure monitor tevékenység-naplókat használ, a tevékenység naplója automatikusan engedélyezve van, és naplózza Azure monitor erőforrásokon végrehajtott műveleteket, például: a művelet elindítását, a művelet állapotát és egyéb hasznos naplózási információt. 

- [Platform-naplók és-metrikák összegyűjtése Azure Monitor](platform/diagnostic-settings.md)

- [A naplózás és a különböző naplózási típusok megismerése az Azure-ban](platform/platform-logs-overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitor a szervezet megfelelőségi szabályainak megfelelően állítsa be a log Analytics munkaterület megőrzési időszakát. Használja az Azure Storage-fiókokat a naplók hosszú távú/archiválási tárolására.

- [Az adatmegőrzési időszak módosítása Log Analytics](platform/manage-cost-storage.md#change-the-data-retention-period)

- [Adatmegőrzési szabályzat konfigurálása az Azure Storage-fiók naplóihoz](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint az eredmények rendszeres áttekintése. A naplók áttekintéséhez és a naplózási adatok lekérdezéséhez használja a Azure Monitor és egy Log Analytics munkaterületet.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Log Analytics lekérdezések első lépései](log-query/get-started-portal.md)

- [Egyéni lekérdezések végrehajtása a Azure Monitorban](log-query/get-started-queries.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: Azure Security Center használata log Analytics munkaterülettel a biztonsági naplókban és eseményekben észlelt rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel szolgáltatásban való ellátását.

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

- [Riasztások kezelése Azure Security Centerban](../security-center/security-center-managing-and-responding-alerts.md)

- [Riasztás a log Analytics-naplófájlok adatkezeléséről](learn/tutorial-response.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt az [Azure biztonsági teljesítményteszt: identitás-és hozzáférés-vezérlés](/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) lehetővé teszi az Azure-erőforrásokhoz való hozzáférés kezelését a szerepkör-hozzárendeléseken keresztül. Ezeket a szerepköröket hozzárendelheti a felhasználókhoz, a csoportok egyszerű szolgáltatásaihoz és a felügyelt identitásokhoz. Bizonyos erőforrásokhoz előre definiált beépített szerepkörök tartoznak, és ezeket a szerepköröket az Azure CLI-vel, a Azure PowerShellsal vagy a Azure Portal eszközzel leltározott vagy lekérdezheti.

- [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

A Microsoft-szolgáltatásokhoz igénybe vehet egy igény szerinti vagy elég hozzáférésű hozzáférést Azure AD Privileged Identity Management Kiemelt szerepkörökkel, és Azure Resource Manager is. 

- [Azure AD Privileged Identity Management áttekintése](../active-directory/privileged-identity-management/pim-configure.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: ha lehetséges, használjon Azure Active Directory SSO-t a különálló önálló hitelesítő adatok konfigurálása helyett. Azure Security Center identitás-és hozzáférés-kezelési javaslatok használata.

- [Az egyszeri bejelentkezés ismertetése az Azure AD-vel](../active-directory/manage-apps/what-is-single-sign-on.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad MFA engedélyezése és a Azure Security Center identitás-és hozzáférési javaslatok követése.

- [Az MFA engedélyezése az Azure-ban](../active-directory/authentication/howto-mfa-getstarted.md)

- [Identitás és hozzáférés figyelése Azure Security Centeron belül](../security-center/security-center-identity-access.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: emelt szintű jogosultságokat igénylő felügyeleti feladatokhoz használjon biztonságos, Azure által felügyelt munkaállomás (más néven privilegizált hozzáférési munkaállomás vagy Paw) használatát.

- [A biztonságos, Azure által felügyelt munkaállomások ismertetése](../active-directory/devices/concept-azure-managed-workstation.md)

- [Az Azure AD MFA engedélyezése](../active-directory/authentication/howto-mfa-getstarted.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések és figyelés használata a környezetben előforduló gyanús vagy nem biztonságos tevékenységek észlelésére. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket. 

- [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](/azure/active-directory/reports-monitoring/concept-user-at-risk)

- [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](../security-center/security-center-identity-access.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

- [Elnevezett helyszínek konfigurálása az Azure-ban](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (Azure ad) használata központi hitelesítési és engedélyezési rendszerrel. Az Azure AD az adatok védelme érdekében erős titkosítást használ a nyugalmi és a továbbítási adatokhoz. Az Azure AD emellett a felhasználó hitelesítő adatainak a sók, a kivonatok és a biztonságos tárolását is tartalmazza.

- [Azure AD-példány létrehozása és konfigurálása](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. 

- [Az Azure AD jelentéskészítés ismertetése](/azure/active-directory/reports-monitoring/)

- [Az Azure Identity hozzáférési felülvizsgálatok használata](../active-directory/governance/access-reviews-overview.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását. Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

- [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center figyelés**: nincs beállítva. Adjon meg egy értéket a munkaelemben.

**Felelősség**: ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure ad kockázatkezelési és identitás-védelmi funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

- [Az Azure AD kockázatos bejelentkezések megtekintése](/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

- [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Az Azure Sentinel előkészítése](../sentinel/quickstart-onboard.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információt az [Azure biztonsági teljesítményteszt: Adatvédelem](/azure/security/benchmarks/security-control-data-protection)című témakörben talál.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: ha lehetséges, használja a címkéket olyan Azure monitor erőforrások nyomon követéséhez, amelyek bizalmas adatokat tárolnak vagy dolgoznak fel, például log Analytics munkaterületeket.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

- [Naplóadatok és munkaterületek elérésének felügyelete az Azure Monitorban](platform/manage-access.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: az elkülönítés megvalósítása különálló előfizetések és felügyeleti csoportok használatával az egyes biztonsági tartományokhoz, például a környezeti típusokhoz és az adatérzékeny szintekhez. Korlátozhatja a Azure Monitorhoz és az alkalmazásokhoz és a vállalati környezetekhez kapcsolódó erőforrásokhoz való hozzáférés szintjét. A Azure Monitor hozzáférését Azure Active Directory szerepköralapú hozzáférés-vezérléssel szabályozhatja.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure monitor alapértelmezés szerint a TLS 1,2-et tárgyalja. Győződjön meg arról, hogy az Azure-erőforrásokhoz csatlakozó ügyfelek képesek a TLS 1,2 vagy újabb egyeztetésére. 

Application Insights és Log Analytics mindkettő továbbra is engedélyezi a TLS 1,1-és TLS 1,0-adatmennyiséget. Az adat a TLS 1,2-re korlátozható az ügyféloldali oldalon való konfigurálással.

- [Adatküldés biztonságos továbbítása a TLS 1,2 használatával](platform/data-security.md#sending-data-securely-using-tls-12)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el Azure monitor számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.
A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

- [Az ügyfelek adatvédelmének megismerése az Azure-ban](../security/fundamentals/protection-customer-data.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használata a Azure monitorhoz való hozzáférés kezelésére.

- [Szerepkörök, engedélyek és biztonság a Azure Monitorban](platform/roles-permissions-security.md)

- [Az Azure RBAC konfigurálása](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure monitor biztosítja, hogy az összes adatok és mentett lekérdezések a Microsoft által felügyelt kulcsok (MMK-EK) használatával titkosítva legyenek. A Azure Monitor a saját Azure Key Vault tárolt és a rendszer által hozzárendelt felügyelt identitás-hitelesítéssel hozzáférő saját kulcs használatával is lehetőséget nyújt a titkosításra. Ez az ügyfél által felügyelt kulcs (CMK) lehet szoftveres vagy hardveres HSM-védelemmel ellátott.

- [Ügyfél által felügyelt kulcsok Azure Monitor](platform/customer-managed-keys.md)

- [Adatbiztonság Log Analytics](platform/data-security.md)

- [Adatgyűjtés, -megőrzés és -tárolás az Application Insightsban](app/data-retention-privacy.md)

- [A inaktív adatok titkosításának megismerése az Azure-ban](../security/fundamentals/encryption-atrest.md) 

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások Azure monitor és kapcsolódó erőforrásokban lépnek életbe.

- [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](platform/alerts-activity-log.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információért lásd az [Azure biztonsági teljesítményteszt: biztonsági rés kezelése](/azure/security/benchmarks/security-control-vulnerability-management)című témakört.*

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: közös kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információt az [Azure biztonsági teljesítményteszt: leltár és eszközkezelés](/azure/security/benchmarks/security-control-inventory-asset-management)című témakörben talál.*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure CLI használatával kérdezheti le és derítheti fel Azure monitor erőforrásait az előfizetéseken belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

- [Azure Monitor parancssori felület](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest)

- [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Az Azure RBAC ismertetése](../role-based-access-control/overview.md)

- [Szerepkörök, engedélyek és biztonság a Azure Monitorban](platform/roles-permissions-security.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkék alkalmazása olyan Azure monitor erőforrásokhoz, amelyekkel a metaadatok logikailag rendezhetők a besorolásba.

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és a különálló előfizetések használata, ahol szükséges, Azure monitor kapcsolódó erőforrások rendszerezése és nyomon követése. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

- [További Azure-előfizetések létrehozása](/azure/billing/billing-create-subscription)

- [Management Groups létrehozása](/azure/governance/management-groups/create)

- [Címkék létrehozása és használata](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a számítási erőforrásokhoz jóváhagyott Azure-erőforrások és jóváhagyott szoftverek leltárának létrehozása a szervezeti igényeknek megfelelően.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetésekben létrehozható erőforrásokra vonatkozóan.

Az Azure Resource Graph segítségével lekérdezheti és felderítheti az előfizetésen belüli erőforrásokat.  Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Lekérdezések létrehozása az Azure Resource Graph Explorerrel](../governance/resource-graph/first-query-portal.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: a leltár rendszeres összeegyeztetése és a jogosulatlan Azure monitor kapcsolódó erőforrások törlésének biztosítása az előfizetésből időben.  

- [Azure Log Analytics-munkaterület törlése](platform/delete-workspace.md)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozhatja, hogy mely Azure monitor kapcsolódó erőforrások hozhatók létre a környezetben. 

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md) 

- [Adott erőforrástípus megtagadása a következővel Azure Policy](/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja, hogy a felhasználók képesek legyenek az Azure Resources Managerrel való interakcióra az "Microsoft Azure felügyelet" alkalmazás "hozzáférés letiltása" beállításával.

- [Feltételes hozzáférés konfigurálása az Azure Resources Manager elérésének blokkolásához](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt az [Azure biztonsági teljesítményteszt: biztonságos konfiguráció](/azure/security/benchmarks/security-control-secure-configuration)című témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: egyéni Azure Policy-definíciók használata a Azure monitor kapcsolódó erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. A beépített Azure Policy-definíciók elérhetőként is használhatók.

Emellett Azure Resource Manager lehetősége van a sablon exportálására JavaScript Object Notation (JSON), amelyet át kell tekinteni, hogy a konfigurációk megfeleljenek/túllépik a szervezete biztonsági követelményeit.

Az Azure-erőforrások biztonságos alapkonfigurációjának megfelelően Azure Security Center javaslatokat is alkalmazhat.

Ha élő adatfolyam-továbbítási funkciót használ, a kialakítási kulcs mellett tegye biztonságossá a csatornát egy titkos API-kulccsal.

- [Biztonságos APM-Élő metrikastream](app/live-stream.md#secure-the-control-channel)

- [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../governance/policy/tutorials/create-and-manage.md)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Biztonsági javaslatok – útmutató](../security-center/recommendations-reference.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] használatával kényszerítheti ki a Azure monitor kapcsolódó erőforrások biztonságos beállításait.  Emellett Azure Resource Manager sablonokat is használhat a szervezete által igényelt Azure Monitor kapcsolódó erőforrások biztonsági beállításainak fenntartásához.

- [Azure Policy effektusok ismertetése](../governance/policy/concepts/effects.md)

- [Szabályzatok létrehozása és kezelése a megfelelőség kikényszerítése céljából](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager sablonok áttekintése](../azure-resource-manager/templates/overview.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: az Azure DevOps használatával biztonságosan tárolhatók és kezelhetők a kódok, például az egyéni Azure-házirendek és Azure Resource Manager-sablonok. Az Azure DevOps felügyelt erőforrásainak eléréséhez engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: Azure monitor kapcsolódó erőforrások szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Az egyéni Azure Policy-definíciók használatával naplózhatja vagy érvényesítheti Azure Monitor kapcsolódó erőforrásainak biztonsági konfigurációját. Az adott erőforrásokhoz kapcsolódó beépített szabályzat-definíciókat is igénybe vehet.

- [Azure Policy konfigurálása és kezelése](../governance/policy/tutorials/create-and-manage.md)

- [Aliasok Azure Policy](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center használatával végezheti el az Azure monitor kapcsolódó erőforrások alapkonfigurációjának vizsgálatát.  Emellett az Azure-erőforrások konfigurációjának riasztására és naplózására Azure Policy is használhatja.

- [Javaslatok szervizelése Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Managed Service Identity együttes használata a Azure Key Vault használatával egyszerűbbé és biztonságossá teheti az Azure monitorral kapcsolatos támogatott erőforrások titkos kezelését.

- [Integráció az Azure felügyelt identitásokkal](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Key Vault létrehozása](/azure/key-vault/quick-create-portal)

- [Key Vault hitelesítés biztosítása felügyelt identitással](/azure/key-vault/managed-identity)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az Azure ad-ben automatikusan felügyelt identitással rendelkező Azure-szolgáltatások biztosításához. A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Monitor erőforrásokat, a kódban szereplő hitelesítő adatok nélkül. 

- [Felügyelt identitások konfigurálása az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

- [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információt az [Azure biztonsági teljesítményteszt: kártevők elleni védelem](/azure/security/benchmarks/security-control-malware-defense)című témakörben talál.*

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure monitor kapcsolódó erőforrások esetében), de nem fut a tartalomon. 

A megfelelő Azure Monitor kapcsolódó erőforrásokra feltöltött fájlok előzetes vizsgálata, például Log Analytics munkaterület.

A Storage-fiókokba feltöltött kártevők észleléséhez használja a Azure Security Center veszélyforrások észlelését az adatszolgáltatásokhoz. 

- [Ismerje meg a Microsoft kártevő szoftvereket az Azure Cloud Services és Virtual Machines](../security/fundamentals/antimalware.md)

- [Az adatszolgáltatások fenyegetés-észlelésének megismerése Azure Security Center](/azure/security-center/security-center-alerts-data-services)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információt az [Azure biztonsági teljesítményteszt: adat-helyreállítás](/azure/security/benchmarks/security-control-data-recovery)című témakörben talál.*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Resource Manager használatával exportálhatja a Azure monitor és a kapcsolódó erőforrásokat egy JavaScript Object Notation (JSON) sablonban, amely biztonsági mentésként használható a Azure monitor és a kapcsolódó konfigurációkhoz.  A Azure Automation használatával automatikusan futtathatja a biztonsági mentési parancsfájlokat. 

- [Log Analytics munkaterület kezelése Azure Resource Manager sablonok használatával](/azure/azure-monitor/platform/template-workspace-configuration)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Tudnivalók Azure Automation](../automation/automation-intro.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: a Azure Resource Manager használatával exportálhatja a Azure monitor és a kapcsolódó erőforrásokat egy JavaScript Object Notation (JSON) sablonban, amely biztonsági mentésként használható a Azure monitor és a kapcsolódó konfigurációkhoz.  Az ügyfél által felügyelt kulcsok biztonsági mentése Azure Key Vault, ha Azure Monitor kapcsolódó erőforrások ügyfél által felügyelt kulcsokat használnak, 

- [Log Analytics munkaterület kezelése Azure Resource Manager sablonok használatával](/azure/azure-monitor/platform/template-workspace-configuration)

- [Egy-és többerőforrásos exportálás Azure Portal sablonba](../azure-resource-manager/templates/export-template-portal.md)

- [Key Vault-kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a helyreállítás rendszeres végrehajtásának lehetősége Azure Resource Manager támogatott sablonfájlok használatával.  Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

- [Log Analytics munkaterület kezelése Azure Resource Manager sablonok használatával](/azure/azure-monitor/platform/template-workspace-configuration)

- [Key Vault-kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: az Azure DevOps segítségével biztonságosan tárolhatja és kezelheti a kódokat, például az egyéni Azure-szabályzatokat, Azure Resource Manager sablonokat. Az Azure DevOps felügyelt erőforrások védelme érdekében engedélyeket adhat meg vagy tagadhat meg bizonyos felhasználók, beépített biztonsági csoportok vagy Azure Active Directory (Azure AD) által meghatározott csoportok számára, ha az integrálva van az Azure DevOps, vagy Active Directory, ha a TFS integrálva van.   A szerepköralapú hozzáférés-vezérlés használatával biztosíthatja az ügyfél által felügyelt kulcsok használatát. 

Emellett engedélyezze a helyreállítható törlést és a védelem kiürítését Key Vault a kulcsok véletlen vagy rosszindulatú Törlés elleni védelme érdekében. Ha az Azure Storage Azure Resource Manager sablonok biztonsági mentéseit tárolja, engedélyezze a Soft delete-et az adatok mentéséhez és helyreállításához, amikor blobokat vagy blob-pillanatképeket törölnek. 

- [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Engedélyek és csoportok az Azure DevOps](/azure/devops/organizations/security/about-permissions)

- [A helyreállítható törlés és a védelem engedélyezése a Key Vaultban](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

- [Az Azure Storage-blobok helyreállítható törlése](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információt az [Azure biztonsági teljesítményteszt: incidens válasza](/azure/security/benchmarks/security-control-incident-response)című témakörben talál.*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

- [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [A NIST számítógépes biztonsági incidensek kezelésének útmutatója a saját incidensekre vonatkozó válaszadási terv létrehozásához nyújtott támogatáshoz](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben, nem gyártva) címkék használatával és elnevezési rendszer létrehozása az Azure-erőforrások egyértelmű azonosításához és kategorizálásához, különösen a bizalmas adatok feldolgozásához.  Az Ön felelőssége, hogy rangsorolja a riasztások szervizelését az Azure-erőforrások és-környezet kritikus jellemzői alapján, ahol az incidens történt.

- [Biztonsági riasztások az Azure Security Centerben](../security-center/security-center-alerts-overview.md)

- [Címkék használata az erőforrások rendszerezéséhez](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: az Azure-erőforrások védelmének biztosítása érdekében a rendszer az incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatokat hajt végre. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

- [A NIST kiadványa – útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és alkalmazásához](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

- [A Azure Security Center biztonsági kapcsolattartó beállítása](../security-center/security-center-provide-security-contact-details.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és javaslatok exportálása a folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosítása érdekében. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba. 

- [Folyamatos exportálás konfigurálása](../security-center/continuous-export.md) 

- [Riasztások továbbítása az Azure Sentinelbe](../sentinel/connect-azure-security-center.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan elindíthatja a válaszokat az "Logic apps" használatával a biztonsági riasztások és az Azure-erőforrások védelme érdekében javasolt javaslatok alapján.

- [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](../security-center/workflow-automation.md)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért tekintse meg az [Azure biztonsági teljesítményteszt: behatolási tesztek és a Red Team gyakorlatok](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises)című témakört.*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket. A Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatban a Microsoft stratégiáját és a Red Teaming és a Live site penetráció tesztelését is használhatja.

- [Behatolás-tesztelési szabályok engagement](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud vörös összevonása](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
