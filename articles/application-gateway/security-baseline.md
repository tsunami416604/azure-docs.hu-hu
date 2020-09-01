---
title: Azure biztonsági alapkonfiguráció az Azure Application Gateway
description: Azure biztonsági alapkonfiguráció az Azure Application Gateway
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b470fa72518f805c10403931280bdec96bda0fbe
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89226539"
---
# <a name="azure-security-baseline-for-azure-application-gateway"></a>Azure biztonsági alapkonfiguráció az Azure Application Gateway

Az Azure Application Gateway Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: gondoskodjon arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezzen egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel van alkalmazva. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

* [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: az Azure Application Gateway alhálózatokhoz társított hálózati biztonsági csoportok (NSG-EK) esetében engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Megjegyzés: bizonyos esetekben előfordulhat, hogy az Azure Application Gateway alhálózatokhoz társított NSG-adatforgalmi naplók nem jelenítik meg az engedélyezett forgalmat. Ha a konfiguráció megfelel a következő forgatókönyvnek, nem jelenik meg az engedélyezett forgalom a NSG flow naplóiban:
- Telepítette Application Gateway v2-t
- Rendelkezik egy NSG az Application Gateway alhálózaton
- Engedélyezte a NSG folyamat naplóit az adott NSG

* [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Az Azure Application Gateway diagnosztikai és naplózási kérdései](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat. A WAF a OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya, a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9.

* [Az Azure Application Gateway funkcióinak megismerése](https://docs.microsoft.com/azure/application-gateway/features)

* [Az Azure WAF ismertetése](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Az Azure WAF üzembe helyezése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a DDoS standard Protection engedélyezése az azure-Application Gateway üzemi példányaihoz társított Azure-beli virtuális hálózatokon a DDOS-támadások elleni védelem érdekében. Az ismert kártékony IP-címekkel folytatott kommunikáció megtagadásához használja Azure Security Center integrált veszélyforrások felderítését.

* [A DDoS Protection konfigurálása](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: az Azure Application Gateway alhálózatokhoz társított hálózati biztonsági csoportok (NSG-EK) esetében engedélyezze a NSG flow-naplókat, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

Megjegyzés: bizonyos esetekben előfordulhat, hogy az Azure Application Gateway alhálózatokhoz társított NSG-adatforgalmi naplók nem jelenítik meg az engedélyezett forgalmat. Ha a konfiguráció megfelel a következő forgatókönyvnek, nem jelenik meg az engedélyezett forgalom a NSG flow naplóiban:
- Telepítette Application Gateway v2-t
- Rendelkezik egy NSG az Application Gateway alhálózaton
- Engedélyezte a NSG folyamat naplóit az adott NSG

* [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [Az Azure Application Gateway diagnosztikai és naplózási kérdései](https://docs.microsoft.com/azure/application-gateway/application-gateway-faq#diagnostics-and-logging)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) üzembe helyezése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat. A WAF a OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya, a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9.

Azt is megteheti, hogy több Piactéri lehetőség áll rendelkezésre, például az Azure-hoz készült Barracuda-WAF, amelyek az Azure piactéren érhetők el, beleértve az AZONOSÍTÓk/IP-

* [Az Azure Application Gateway funkcióinak megismerése](https://docs.microsoft.com/azure/application-gateway/features)

* [Az Azure WAF ismertetése](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview)

* [Az Azure WAF üzembe helyezése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [A Barracuda WAF Cloud Service ismertetése](https://docs.microsoft.com/azure/app-service/environment/app-service-app-service-environment-web-application-firewall#configuring-your-barracuda-waf-cloud-service)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: Azure-Application Gateway üzembe helyezése a megbízható tanúsítványokhoz engedélyezett https/SSL-alapú webalkalmazásokhoz.

* [Application Gateway üzembe helyezése](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

* [A Application Gateway konfigurálása a HTTPS használatára](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

* [A 7. rétegbeli terheléselosztás és az Azure-webalkalmazás-átjárók ismertetése](https://docs.microsoft.com/azure/application-gateway/overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használjon Virtual Network szolgáltatás-címkéket. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például GatewayManager), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Az Azure Application Gateway alhálózatokhoz társított hálózati biztonsági csoportok (NSG-EK) esetében engedélyeznie kell a bejövő internetes forgalmat a 65503-65534-as TCP-portokon a Application Gateway v1 SKU esetében, valamint a v2 SKU-hoz készült 65200-65535-es TCP-portokat a GatewayManager szolgáltatás címkéje. Ez a porttartomány az Azure-infrastruktúra kommunikációja esetén szükséges. Ezeket a portokat az Azure-tanúsítványok védik (zárolják). A külső entitások, beleértve az átjárók ügyfeleit, nem tudnak kommunikálni ezeken a végpontokon.

* [A szolgáltatási címkék megismerése és használata](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

* [Az Azure Application Gateway konfigurációjának áttekintése](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: az Azure Application Gateway üzemelő példányokhoz kapcsolódó hálózati beállítások szabványos biztonsági konfigurációinak meghatározása és implementálása. A "Microsoft. Network" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure Application Gateway-, Azure-alapú virtuális hálózatok és hálózati biztonsági csoportok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a beépített szabályzat-definíciót is.

Az Azure-tervrajzok segítségével leegyszerűsítheti a nagy léptékű Azure-környezeteket a főbb környezeti összetevők, például a Azure Resource Manager sablonok, az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) és a házirendek egyetlen terv szerinti definícióban való kicsomagolásával. A tervet egyszerűen alkalmazhatja új előfizetésekre, környezetekre, valamint a verziószámozáson keresztül történő finomhangolásra és felügyeletre.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint létrehozása](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure Application Gateway alhálózatához társított hálózati biztonsági csoportok (NSG-EK), valamint a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrások használata címkék használatával. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Virtual Network létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [NSG létrehozása biztonsági konfigurációval](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a hálózati beállítások és az Azure Application Gateway üzemelő példányokhoz kapcsolódó erőforrások változásainak észlelésére. Hozzon létre riasztásokat a Azure Monitoron belül, amelyek akkor aktiválódnak, ha a kritikus hálózati beállítások vagy erőforrások változása zajlik.

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Riasztások létrehozása a Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások, például a Azure app Service számára használt időforrást.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat log Analytics munkaterületre, Azure Event hub vagy Azure Storage-fiókba. Az Azure-tevékenység naplózási adatainak használatával meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure-Application Gateway és a kapcsolódó erőforrások, például a hálózati biztonsági csoportok (NSG-EK) számára az Azure Application Gateway-alhálózat védelmére szolgáló műveletekhez.

A tevékenységek naplóin kívül az Azure Application Gateway üzemelő példányok diagnosztikai beállításait is konfigurálhatja. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

Az Azure Application Gateway az Azure Application Insights beépített integrációját is biztosítja. Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. Application Insights automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a webalkalmazások használatának megismerésében. A folyamatos exportálást engedélyezheti a telemetria exportálásához a Application Insightsból egy központi helyre, hogy az adatok továbbra is a normál megőrzési időtartamnál hosszabbak maradjanak.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [A Application Insights engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [Folyamatos exportálás konfigurálása](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: a vezérlési sík naplózásához engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat log Analytics munkaterületre, Azure Event hub vagy Azure Storage-fiókba. Az Azure-tevékenység naplózási adatainak használatával meghatározhatja a "mi, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az Azure-Application Gateway és a kapcsolódó erőforrások, például a hálózati biztonsági csoportok (NSG-EK) számára az Azure Application Gateway-alhálózat védelmére szolgáló műveletekhez.

A tevékenységek naplóin kívül az Azure Application Gateway üzemelő példányok diagnosztikai beállításait is konfigurálhatja. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

Az Azure Application Gateway az Azure Application Insights beépített integrációját is biztosítja. Application Insights gyűjti a napló-, a teljesítmény-és a hiba adatait. Application Insights automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket, és hatékony elemzési eszközöket tartalmaz, amelyek segítenek a problémák diagnosztizálásában és a webalkalmazások használatának megismerésében. A folyamatos exportálást engedélyezheti a telemetria exportálásához a Application Insightsból egy központi helyre, hogy az adatok továbbra is a normál megőrzési időtartamnál hosszabbak maradjanak.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [A Application Insights engedélyezése](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)

* [Folyamatos exportálás konfigurálása](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure monitoron belül állítsa be a log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi előírásai szerint. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

* [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: az Azure-Tevékenységnaplók diagnosztikai beállításainak, valamint az azure-Application Gateway diagnosztikai beállításainak engedélyezése, valamint a naplók elküldése egy log Analytics-munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján.

Az összes telepített hálózati erőforrás, beleértve az Azure Application Gateway-t, a hálózatokra vonatkozó Azure Monitor használatával átfogó képet kaphat az állapotról és a metrikáról.

Opcionálisan engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Hálózati Azure Monitor használata](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: az Azure Web Application Firewall (WAF) v2 SKU üzembe helyezése kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat. A WAF a OWASP (webalkalmazás-biztonsági projekt megnyitása) alapszabálya, a 3,1 (csak WAF_v2), a 3,0 és a 2.2.9.

Engedélyezze az Azure-műveletnapló diagnosztikai beállításait, valamint az Azure-WAF diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. Lekérdezéseket hajthat végre Log Analytics a kifejezések kereséséhez, a trendek azonosításához, a mintázatok elemzéséhez és számos más elemzéshez az összegyűjtött adatok alapján. A Log Analytics munkaterület-lekérdezések alapján létrehozhat riasztásokat.

Az összes telepített hálózati erőforrás, beleértve az Azure Application Gateway-t, a hálózatokra vonatkozó Azure Monitor használatával átfogó képet kaphat az állapotról és a metrikáról. A hálózati konzolon Azure Monitoron megtekintheti és létrehozhatja az Azure Application Gateway riasztásait.

* [Az Azure WAF üzembe helyezése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [Az Azure Application Gateway diagnosztikai beállításainak engedélyezése](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

* [Hálózati Azure Monitor használata](https://docs.microsoft.com/azure/azure-monitor/insights/network-insights-overview)

* [Riasztások létrehozása az Azure-ban](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) v2 bevezetése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat.

Konfigurálja az Azure Application Gateway üzemelő példányok diagnosztikai beállításait. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

* [Az Azure WAF üzembe helyezése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Az Azure WAF diagnosztikai beállításainak konfigurálása](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; Az Azure Application Gateway nem dolgozza fel és nem hozza létre a felhasználók számára elérhető DNS-naplókat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a Azure Active Directory (ad) beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és lekérdezhető. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítéséhez.

* [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: az Azure Application Gatewayhoz való hozzáférés szabályozása Azure Active Directory (ad) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabványos üzemeltetési eljárások létrehozása a dedikált rendszergazdai fiókok használata körül. A rendszergazdai fiókok számának figyeléséhez használja a Azure Security Center identitás-és hozzáférés-kezelés lehetőséget.

Emellett a dedikált rendszergazdai fiókok nyomon követésének elősegítése érdekében Azure Security Center vagy beépített Azure-szabályzatokból származó javaslatokat is használhat, például:
- Az előfizetéshez egynél több tulajdonos rendelhető hozzá
- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből
- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

* [Az identitás és a hozzáférés figyelésének Azure Security Center használata (előzetes verzió)](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [A Azure Policy használata](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: Azure-alkalmazás-regisztráció (egyszerű szolgáltatásnév) használata az Azure Application Gateway API-hívásokkal való interakcióhoz használható tokenek lekéréséhez.

* [Az Azure REST API-k meghívása](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása az Azure AD-vel](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure Recovery Services API-információk](https://docs.microsoft.com/rest/api/recoveryservices/)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: az Azure ad MFA engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatok követése.

* [Az MFA engedélyezése az Azure-ban](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: az Azure-erőforrások bejelentkezésére és konfigurálására szolgáló, az MFA használatára konfigurált, a mancsok (emelt szintű hozzáférési munkaállomások) használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Az MFA engedélyezése az Azure-ban](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.

* [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

* [Elnevezett helyszínek konfigurálása az Azure-ban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (HRE) használata központi hitelesítési és engedélyezési rendszerrel. A HRE erős titkosítással védi az adatok védelmét a nyugalmi és átviteli állapotban lévő adatokhoz. A HRE a felhasználó hitelesítő adatait is sók, kivonatok és biztonságosan tárolja.

* [HRE-példány létrehozása és konfigurálása](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá.

* [Az Azure AD jelentéskészítés ismertetése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

* [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure ad Identity Protection és a kockázati észlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget.

* [Az Azure AD kockázatos bejelentkezések megtekintése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: nem alkalmazható; Ügyfélszéf nem alkalmazható az Azure Application Gatewayra.

* [Ügyfélszéf által támogatott szolgáltatások listája](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Győződjön meg arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira vonatkozó hálózati hozzáférés-vezérléssel van alkalmazva. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: gondoskodjon arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezzen egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira jellemző hálózati hozzáférés-vezérléssel van alkalmazva. Korlátozza a kimenő forgalmat csak a megbízható helyek számára, hogy csökkentse az adatok kiszűrése veszélyét. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

* [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: végpontok közötti titkosítás konfigurálása az Azure Application Gateway-átjárók TLS használatával.

* [Végpontok közötti TLS konfigurálása az Azure Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/end-to-end-ssl-portal)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: nem alkalmazható, az Azure Application Gateway nem tárolja az ügyféladatokat a nyugalmi állapotban.

A Microsoft felügyeli az Azure Application Gateway mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata az Azure Application Gateway vezérlési síkon való hozzáférés vezérléséhez (a Azure Portal).

* [Az Azure RBAC konfigurálása](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: nem alkalmazható; Az Azure Application Gateway nem tárolja az ügyféladatokat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel az Azure Application Gateway-példányok, valamint más kritikus vagy kapcsolódó erőforrások változásai zajlanak.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: jelenleg nem érhető el; a sebezhetőségi felmérés Azure Security Center az Azure Application Gateway számára még nem érhető el.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Application Gateway számára elérhető biztonsági vezérlőket a szolgáltatás konfigurálásával kapcsolatos biztonsági rések csökkentése érdekében.

* [A szolgáltatás lefedettsége (a sebezhetőségi felmérést is beleértve) az Azure Pásti szolgáltatásaihoz](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: még nem érhető el; a sebezhetőségi felmérés Azure Security Center az Azure Application Gateway számára még nem érhető el.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Application Gateway számára elérhető biztonsági vezérlőket a szolgáltatás konfigurálásával kapcsolatos biztonsági rések csökkentése érdekében.

* [A szolgáltatás lefedettsége (a sebezhetőségi felmérést is beleértve) az Azure Pásti szolgáltatásaihoz](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: még nem érhető el; a sebezhetőségi felmérés Azure Security Center az Azure Application Gateway számára még nem érhető el.

A Microsoft által beolvasott és javított mögöttes platform. Tekintse át az Azure Application Gateway számára elérhető biztonsági vezérlőket a szolgáltatás konfigurálásával kapcsolatos biztonsági rések csökkentése érdekében.

* [A szolgáltatás lefedettsége (a sebezhetőségi felmérést is beleértve) az Azure Pásti szolgáltatásaihoz](https://docs.microsoft.com/azure/security-center/features-paas)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy a bérlőben a megfelelő (olvasási) engedélyek szerepelnek, valamint az összes Azure-előfizetés, valamint az előfizetésekben lévő erőforrások számbavétele.

Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott a Azure Resource Manager erőforrások létrehozása és használata.

* [Lekérdezések létrehozása az Azure Resource Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Az Azure RBAC ismertetése](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: az Azure-erőforrások rendszerezéséhez és nyomon követéséhez használja a címkézést, a felügyeleti csoportokat és a különálló előfizetéseket, ahol szükséges. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

Emellett az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6,4: jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: jóváhagyott Azure-erőforrások definiálása.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az előfizetése (i) ban létrehozható erőforrások típusára.

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférésének konfigurálása a felhasználók "Microsoft Azure felügyelet" alkalmazáshoz való hozzáférésének tiltása a Azure Resource Manager való interakcióra.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Győződjön meg arról, hogy az összes Virtual Network Azure Application Gateway alhálózati üzemelő példány rendelkezik egy hálózati biztonsági csoporttal (NSG), amely az alkalmazás megbízható portjaira és forrásaira vonatkozó hálózati hozzáférés-vezérléssel van alkalmazva. Habár a hálózati biztonsági csoportok támogatottak az Azure Application Gatewayon, vannak olyan korlátozások és követelmények, amelyeket be kell tartani ahhoz, hogy a NSG és az Azure-Application Gateway a várt módon működjön.

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [A korlátozások és követelmények ismertetése a NSG és az Azure Application Gateway használatával](https://docs.microsoft.com/azure/application-gateway/configuration-overview)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: az Azure Application Gateway üzemelő példányokhoz kapcsolódó hálózati beállítások szabványos biztonsági konfigurációinak meghatározása és implementálása. A "Microsoft. Network" névtérben Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre az Azure Application Gateway-, Azure-alapú virtuális hálózatok és hálózati biztonsági csoportok hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez. Használhatja a beépített szabályzat-definíciót is.

* [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure Policy [megtagadás] és a [telepítés, ha nem létezik] az Azure-erőforrások biztonságos beállításainak betartatására használható.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy effektusok ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

* [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: a "Microsoft. Network" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a "Microsoft. Network" névtérben található beépített Azure Policy definíciók és Azure Policy Aliasok használatával egyéni szabályzatokat hozhat létre a riasztáshoz, a naplózáshoz és a rendszerkonfigurációk kényszerítéséhez. Az Azure Policy [audit], [megtagadás] és [telepítés ha nem létezik] használatával automatikusan kényszerítheti ki az Azure-erőforrások konfigurációit.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrások IaaS szolgál.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az Azure-Application Gateway automatikus felügyelt identitással a Azure Active Directoryban (ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

A tanúsítványok biztonságos tárolásához használja a Azure Key Vault. A Azure Key Vault egy platform által felügyelt titkos tároló, amely a titkok, kulcsok és SSL-tanúsítványok védelmére használható. Az Azure Application Gateway támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault-integrációját. Ez a támogatás a Application Gateway v2 SKU-ra korlátozódik.

* [Az SSL-lezárás konfigurálása Key Vault tanúsítványokkal a Azure PowerShell használatával](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használatával biztosíthatja az Azure-Application Gateway automatikus felügyelt identitással a Azure Active Directoryban (ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

A tanúsítványok biztonságos tárolásához használja a Azure Key Vault. A Azure Key Vault egy platform által felügyelt titkos tároló, amely a titkok, kulcsok és SSL-tanúsítványok védelmére használható. Az Azure Application Gateway támogatja a HTTPS-kompatibilis figyelőkhöz csatolt kiszolgálói tanúsítványok Key Vault-integrációját. Ez a támogatás a Application Gateway v2 SKU-ra korlátozódik.

* [Az SSL-lezárás konfigurálása Key Vault tanúsítványokkal a Azure PowerShell használatával](https://docs.microsoft.com/azure/application-gateway/configure-keyvault-ps)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) v2 bevezetése a kritikus webalkalmazások előtt a bejövő forgalom további ellenőrzéséhez. A webalkalmazási tűzfal (WAF) az Azure Application Gateway szolgáltatása, amely központosított védelmet biztosít a webalkalmazások számára a gyakori biztonsági rések és sebezhetőségek ellen. Az Azure WAF segítségével biztonságossá teheti Azure App Service webalkalmazásait úgy, hogy a bejövő webes forgalmat úgy vizsgálja, hogy blokkolja a támadásokat, például az SQL-injektálásokat, a helyek közötti parancsfájlkezelést, a kártevők feltöltését és a DDoS-támadásokat.

Konfigurálja az Azure Application Gateway üzemelő példányok diagnosztikai beállításait. a diagnosztikai beállítások segítségével konfigurálhatja az erőforráshoz tartozó platform-naplók és-metrikák adatfolyam-exportálását a választott célra (Storage-fiókok, Event Hubs és Log Analytics).

* [Az Azure WAF üzembe helyezése](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

* [Az Azure WAF diagnosztikai beállításainak konfigurálása](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: nem alkalmazható; Az Azure Application Gateway nem tárolja az ügyféladatokat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: az Azure webalkalmazási TŰZFAL (WAF) használata esetén WAF házirendeket konfigurálhat. A WAF-szabályzatok két különböző típusú biztonsági szabályt alkotnak: az ügyfél által létrehozott egyéni szabályokat, valamint a felügyelt szabálykészlet, amely az Azure által felügyelt, előre konfigurált szabályok gyűjteménye. Az Azure által felügyelt szabálykészlet egyszerű módszert kínál a védelem közös biztonsági fenyegetésekkel való üzembe helyezésére. Mivel az ilyen szabályrendszerek az Azure felügyeli, a szabályok az új támadási aláírások elleni védelemhez szükséges módon frissülnek.

* [Az Azure által felügyelt WAF-szabályok ismertetése](https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview#waf-policy)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: az Azure Application Gateway nem tárolja az ügyféladatokat. Ha azonban egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

Az Azure DevOps Services számos Azure Storage-funkciót használ, így biztosítva az adatmennyiséget a hardverhiba, a szolgáltatások megszakítása vagy a régió katasztrófája esetén. Emellett az Azure DevOps csapata a véletlen vagy rosszindulatú törlésből származó adatok védelme érdekében eljárást követ.

* [Az adatelérhetőség ismertetése az Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/security/data-protection?view=azure-devops#data-availability)

* [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: az ügyfél által felügyelt tanúsítványok Azure Key Vaulton belüli biztonsági mentése.

* [Key Vault-tanúsítványok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultcertificate)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a biztonsági másolatban kezelt ügyfelek által felügyelt tanúsítványok visszaállításának tesztelése.

* [Key Vault-tanúsítványok visszaállítása](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultcertificate)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: Ellenőrizze, hogy engedélyezve van-e a soft delete Azure Key Vault. A Soft delete lehetővé teszi a törölt kulcstartók és tároló objektumok, például kulcsok, titkos kódok és tanúsítványok helyreállítását.

* [A Azure Key Vault Soft delete használata](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

* [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Az ügyfél a NIST számítógépes biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

* [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyfél adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Azure Security Center adatösszekötőt a riasztások Sentinel továbbításához.

* [Folyamatos exportálás konfigurálása](https://docs.microsoft.com/azure/security-center/continuous-export)

* [Riasztások továbbítása az Azure Sentinelbe](https://docs.microsoft.com/azure/sentinel/connect-azure-security-center)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.

* [A Munkafolyamat-automatizálás és a Logic Apps konfigurálása](https://docs.microsoft.com/azure/security-center/workflow-automation)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: 

* [Kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alaptervekről](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
