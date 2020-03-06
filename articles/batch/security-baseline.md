---
title: Azure biztonsági alapkonfiguráció a Batch szolgáltatáshoz
description: Azure biztonsági alapkonfiguráció a Batch szolgáltatáshoz
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: af0466299d1f972fe9334beb5f31c886c1928a1c
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78328999"
---
# <a name="azure-security-baseline-for-batch"></a>Azure biztonsági alapkonfiguráció a Batch szolgáltatáshoz

A Batch Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatások alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: Azure batch készlet (ek) üzembe helyezése a virtuális hálózaton belül. Ahhoz, hogy a készlet számítási csomópontjai biztonságosan kommunikáljanak más virtuális gépekkel vagy helyszíni hálózattal, egy Azure-beli virtuális hálózat alhálózatában is kiépítheti a készletet. Emellett a készletet a virtuális hálózaton belül is üzembe helyezheti, így szabályozhatja az egyes csomópontok hálózati adapterei (NIC) és az alhálózatok biztonságossá tételéhez használt hálózati biztonsági csoportot (NSG). Konfigurálja úgy a NSG, hogy csak a megbízható IP-/Locations érkező adatforgalmat engedélyezze az interneten.


Azure Batch-készlet létrehozása egy Virtual Networkon belül:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: a Batch-készlethez társított virtuális hálózattal/hálózati biztonsági csoporttal (NSG) kapcsolatos hálózati védelmi javaslatok Azure Security Center használata és szervizelése. Engedélyezze a flow-naplókat a Batch-készlet védetté tételéhez használt NSG, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. NSG-naplókat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. Az Azure Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati hibás konfigurációk meghatározását.


A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


A Azure Security Center által biztosított hálózati biztonság ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható, a teljesítményteszt a Azure app Service-vagy IaaS-példányokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure DDoS (Distributed szolgáltatásmegtagadás) standard szintű védelem engedélyezése a virtuális hálózaton, amely megvédi Azure batch-készletét a DDOS-támadások elleni védelemhez. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.


A DDoS Protection konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Az Azure Security Center integrált veszélyforrások felderítésének ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: engedélyezze a flow-naplókat a Azure batch készletének védelméhez használt hálózati biztonsági csoporton (NSG), és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához.


A NSG folyamat naplófájljainak engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek üzembe helyezése

**Útmutató**: Ha megfelelőségi célokra van szükség, válasszon ki egy hálózati virtuális berendezést az Azure piactéren, amely támogatja a betöltési észlelési rendszerek (azonosítók) és a behatolás-megelőzési rendszerek (IP-címek) funkcióit.


Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és tartományok forrása a Microsoft Threat Intelligence-hírcsatorna.


Azure Firewall üzembe helyezése egy nyilvános IP-címmel, ugyanabban a virtuális hálózatban, mint a Azure Batch Pool-csomópontok. Konfigurálja a hálózati címfordítási (NAT-) szabályokat az interneten található megbízható helyek és az egyes készlet-csomópontok magánhálózati IP-címei között. A Azure Firewall a veszélyforrások felderítése területen állítsa be a "riasztás és megtagadás" beállítást, hogy megakadályozza a riasztást, és blokkolja az ismert kártékony IP-címekre és tartományokra irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence-hírcsatornából származnak, és csak a legmagasabb szintű megbízhatósági rekordok tartoznak ide. 


Azure Batch-készlet létrehozása egy Virtual Networkon belül:

https://docs.microsoft.com/azure/batch/batch-virtual-network


Azure Firewall üzembe helyezése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal


Azure Marketplace:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="17-manage-traffic-to-your-web-applications"></a>1,7: a webes alkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható, a teljesítményteszt a Azure app Service-vagy IaaS-példányokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a virtuális hálózati szolgáltatás címkéi segítségével definiálhatja a hálózati biztonsági csoportokhoz vagy a Azure batch-készlet (ek) hez társított Azure-tűzfalakhoz tartozó hálózati hozzáférés-vezérlést. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.


A szolgáltatási címkék megismerése és használata:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: a Azure batch készlet (ek) hoz tartozó hálózati erőforrások szabványos biztonsági konfigurációinak definiálása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. batch" és a "Microsoft. Network" névterekben, hogy egyéni szabályzatokat hozzon létre a Azure Batch-készletek hálózati konfigurációjának naplózásához vagy érvénybe léptetéséhez.



Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: az Azure batch-készletekhez társított hálózati biztonsággal és adatforgalommal kapcsolatos címkék használata a hálózati szolgáltatási csoportokhoz (NSG) és az egyéb erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.


A címkézéshez kapcsolódó beépített Azure Policy-definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.


A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.


Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Virtuális hálózat létrehozása:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


NSG létrehozása:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure batch-készletekhez kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

Azure-Tevékenységnaplók eseményeinek megtekintése és beolvasása: https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Riasztások létrehozása Azure Monitorban: https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Azure batch alapértelmezés szerint a Microsoft biztosítja az idő szinkronizálását. Ha azonban bizonyos időszinkronizálási követelményekkel rendelkezik, ezeket a módosításokat végrehajthatja.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a Azure batch fiókja Azure monitor a fürtözött eszközök által generált biztonsági adatokat összesítve. Az egyéni lekérdezések használatával észlelheti és reagálhat a környezetbeli fenyegetésekre.  Az erőforrás-szintű figyelés Azure Batch a Batch API-k segítségével figyelheti vagy lekérdezheti az erőforrások állapotát, beleértve a feladatokat, a feladatokat, a csomópontokat és a készleteket.



Azure Batch fiók bevezetésének módja Azure Monitor:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: Azure batch fiók szintű figyeléshez az egyes batch-fiókokat a Azure monitor szolgáltatásainak használatával figyelheti. Azure Monitor a Batch-fiók szintjén (például készletekben, feladatokban és feladatokban) lévő erőforrások mérőszámait és opcionálisan diagnosztikai naplóit gyűjti. Az adatok gyűjtése és felhasználása manuálisan vagy programozott módon a Batch-fiókban lévő tevékenységek figyeléséhez és a problémák diagnosztizálásához.


Az erőforrás-szint figyeléséhez használja a Azure Batch API-kat az erőforrások állapotának figyeléséhez vagy lekérdezéséhez, beleértve a feladatokat, a feladatokat, a csomópontokat és a készleteket. Azure Batch


Azure Batch fiók szintű figyelés és naplózás konfigurálása:

https://docs.microsoft.com/azure/batch/monitoring-overview


A Batch erőforrás-szintű figyelésének ismertetése:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="24-collect-security-logs-from-operating-system"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerből

**Útmutató**: a Azure Monitor a Azure batch-fiók erőforrásaihoz tartozó mérőszámokat és diagnosztikai naplókat gyűjt. Az adatok gyűjtését és felhasználását többféleképpen is megfigyelheti Azure Batch-fiókja és a problémák diagnosztizálásához. A metrikai riasztásokat úgy is konfigurálhatja, hogy értesítést kapjon, ha egy metrika eléri a megadott értéket.


Ha szükséges, az egyes készlet-csomópontokhoz biztonságos rendszerhéj (SSH) vagy RDP protokoll (RDP) használatával kapcsolódhat a helyi operációsrendszer-naplók eléréséhez.


Diagnosztikai naplók gyűjtése a Azure Batch-fiókból:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics


Távoli kapcsolódás a Azure Batch Pool-csomópontokhoz:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Azure batch fiók beléptetése Azure monitorra. Győződjön meg arról, hogy a használt Azure Log Analytics munkaterület rendelkezik a szervezet megfelelőségi előírásai szerint beállított napló megőrzési időtartamával


Azure Batch figyelés és naplózás konfigurálása:

https://docs.microsoft.com/azure/batch/monitoring-overview


Az Azure Log Analytics munkaterület megőrzési idejének konfigurálása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: Azure batch metrikai riasztások létrehozása, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy adott küszöbértéket.


Azure Batch metrikai riasztások konfigurálása:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: Azure batch metrikai riasztások létrehozása, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlép egy adott küszöbértéket.


Azure Batch metrikai riasztások konfigurálása:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: a Windows Defender használata az egyes batch-csomópontokon a Windows operációs rendszerek esetében, illetve a Linux használata esetén saját kártevő szoftvereket is biztosíthat.

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: harmadik féltől származó megoldás implementálása DNS-naplózáshoz

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: a konzol naplózásának és a PowerShell átírásának manuális konfigurálása a csomópontok alapján.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információ [: Security Control (identitás és Access Control](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)).*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3,1: a rendszergazdai fiókok leltárának fenntartása

**Útmutató**: a Azure batch-készlet üzembe helyezése során létrehozott helyi rendszergazdai fiók, valamint a létrehozott egyéb fiókok nyilvántartásának fenntartása. Továbbá, ha Azure Active Directory (HRE) integrációt használ, a HRE beépített szerepkörökkel rendelkezik, amelyeket explicit módon kell hozzárendelni, és ezért lekérdezhető. A HRE PowerShell-modullal ad hoc lekérdezéseket olyan fiókok felderítéséhez, amelyek tagjai a felügyeleti csoportoknak.


Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.


Címtárbeli szerepkör beszerzése a HRE-ben a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Címtárbeli szerepkör tagjainak beszerzése a HRE-ben a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Identitás és hozzáférés figyelése Azure Security Centerekkel:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: Azure batch-készlet kiépítés esetén a helyi számítógép-fiókok létrehozására van lehetőség. Nincsenek módosítható alapértelmezett jelszavak, azonban a Secure Shell-(SSH-) és a RDP protokoll-(RDP-) hozzáféréshez eltérő jelszavakat is megadhat. Azure Batch készlet konfigurálása után létrehozhat egy véletlenszerű felhasználót az egyes csomópontokhoz a Azure Portal vagy a Azure Resource Manager API-n keresztül.


Felhasználó hozzáadása adott számítási csomóponthoz:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3,3: a dedikált rendszergazdai fiókok használatának biztosítása

**Útmutató**: Azure batch-alkalmazások hitelesítésének integrálása Azure Active Directory használatával. Szabályzatokat és eljárásokat hozhat létre a dedikált rendszergazdai fiókok használata körül.


Emellett Azure Security Center identitás-és hozzáférés-kezelési javaslatokat is használhat.


Batch-alkalmazások hitelesítése Azure Active Directory használatával:

https://docs.microsoft.com/azure/batch/batch-aad-auth


Identitás és hozzáférés figyelése Azure Security Centerekkel:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) kihasználása Azure Active Directory

**Útmutató**: nem alkalmazható, míg a Azure batch támogatja az Azure ad-hitelesítést, az egyszeri bejelentkezés nem támogatott.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3,5: többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez.

**Útmutató**: Azure batch-alkalmazások hitelesítésének integrálása Azure Active Directory (HRE) használatával. Engedélyezze a HRE multi-Factor Authentication (MFA) használatát, és kövesse Azure Security Center identitás-és hozzáférés-kezelési javaslatait.
 


Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Identitás és hozzáférés figyelése Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3,6: dedikált gépek (privilegizált hozzáférési munkaállomások) használata az összes felügyeleti feladathoz

**Útmutató**: a (Kiemelt jogosultságú hozzáférési munkaállomások) használata a többtényezős hitelesítés (MFA) használatával, amely a Azure batch-erőforrások bejelentkezésére és konfigurálására van konfigurálva.


További tudnivalók az emelt szintű hozzáférésű munkaállomásokról:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: ha a Azure batch-alkalmazások integrált hitelesítése Azure Active Directory (HRE) használatával történik, a naplók és riasztások generálásához Azure Active Directory biztonsági jelentéseket kell használni, ha a környezetben gyanús vagy nem biztonságos tevékenységet végeznek. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket.


A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: ha a Azure batch-alkalmazások integrált hitelesítése Azure Active Directory-mel rendelkezik, használhatja a feltételes hozzáférést elnevezett helyekkel, hogy az IP-címtartományok, illetve országok/régiók számára csak az adott logikai csoportokban engedélyezze a hozzáférést.



Elnevezett helyszínek konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Active Directory (HRE) használata központi hitelesítési és engedélyezési rendszerként, valamint a Azure batch alkalmazások hitelesítésének integrálása a HRE használatával. A HRE erős titkosítással védi az adatok védelmét a nyugalmi és átviteli állapotban lévő adatokhoz. A HRE a felhasználó hitelesítő adatait is sók, kivonatok és biztonságosan tárolja.


HRE-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance


Batch-alkalmazások hitelesítése a HRE:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory (HRE) olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity hozzáférési felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férhessenek hozzá.


Az Azure Identity hozzáférési felülvizsgálatok használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: diagnosztikai beállítások létrehozása Azure Active Directory felhasználói fiókok számára, a naplók és a bejelentkezési naplók elküldése egy Azure log Analytics-munkaterületre. A kívánt riasztások konfigurálása az Azure Log Analytics-munkaterületen belül.


Azure-beli tevékenység-naplók integrálása a Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: a Azure Active Directory (HRE) kockázati észlelésekkel és az Identity Protection szolgáltatással konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálatot is betöltheti az Azure Sentinelbe.


HRE kockázatos bejelentkezések megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies


Az Azure Sentinel előkészítése:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3,13: <div>A Microsoft számára biztosítson hozzáférést a kapcsolódó ügyféladatok számára a támogatási forgatókönyvek során<br></div>

**Útmutató**: nem érhető el; Azure Batch Ügyfélszéf még nem támogatott. Ügyfélszéf támogatott szolgáltatások listája: https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.


Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Azure Batch készleteket virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és hálózati biztonsági csoportokkal (NSG) kell biztosítani. Azure Batch az adataikat egy biztonságos Azure Storage-fiókban kell tárolni.


Azure Batch-készlet létrehozása egy Virtual Networkon belül:

https://docs.microsoft.com/azure/batch/batch-virtual-network


Azure Storage-fiókok biztonságossá tétele:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása.

**Útmutató**: az olyan Azure Storage-fiókok esetében, amelyek bizalmas információkat tartalmaznak a Azure batch készlet (ek) hez, és amelyek bizalmas adatokat tartalmaznak, megjelölhetik őket a címkék használatával, és biztonságossá tehetik azokat az Azure ajánlott eljárásaival.


Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.


A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.


Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Azure Storage-fiókok biztonságossá tétele:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: az összes bizalmas adat titkosítása az átvitel során. A Microsoft Azure-erőforrások alapértelmezés szerint a TLS 1,2-et fogják egyeztetni. Győződjön meg arról, hogy a Azure Batch készletekhez vagy adattárakhoz (Azure Storage-fiókokhoz) csatlakozó ügyfelek képesek a TLS 1,2-es vagy újabb egyeztetésére.


Győződjön meg arról, hogy HTTPS szükséges a Azure Batch adatait tartalmazó Storage-fiók eléréséhez.


Az Azure Storage-fiók titkosításának megismerése az átvitel során:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az olyan Azure Storage-fiókok esetében, amelyek bizalmas információkat tartalmaznak a Azure batch készlet (ek) hez, és amelyek bizalmas adatokat tartalmaznak, megjelölhetik őket a címkék használatával, és biztonságossá tehetik azokat az Azure ajánlott eljárásaival.


Az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.


A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.


Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data


Azure Storage-fiókok biztonságossá tétele:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: a Azure Active Directory (HRE) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja az Azure-erőforrások felügyeleti síkjával, például a Batch-fiókkal, a Batch-készlettel és a Storage-fiókokkal való hozzáférést.


Az Azure RBAC ismertetése:

https://docs.microsoft.com/azure/role-based-access-control/overview


A RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: az adatazonosítási, besorolási és veszteség-megelőzési funkciók még nem érhetők el az Azure Storage vagy a számítási erőforrások számára. Külső gyártótól származó megoldás implementálása, ha az szükséges a megfelelőség szempontjából.



A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.



Az ügyfelek adatvédelem az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a Azure batch fiókjához társított Storage-fiókok esetében ajánlott engedélyezni a Microsoft számára a titkosítási kulcsok kezelését, azonban szükség esetén lehetősége van a saját kulcsok kezelésére.



Az Azure Storage-fiókok titkosítási kulcsainak kezelése:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használata riasztások létrehozásához, amikor a módosítások a Azure batch-fiókokhoz/készletekhez kapcsolódó vagy azokhoz kapcsolódó kritikus Azure-erőforrásokra vonatkoznak.



A Azure Batch készlethez társított Storage-fiókok diagnosztikai beállításainak konfigurálása a készlet adatain végzett összes szifilisz-művelet figyeléséhez és naplózásához.



Riasztások létrehozása az Azure Activity log-eseményekhez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



További naplózás/naplózás engedélyezése egy Azure Storage-fiókhoz:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: a Azure batch Pool-csomópontok esetében a biztonsági rések kezelésére szolgáló megoldás kezeléséért felelős.


Ha Rapid7, Qualys vagy bármely más sebezhetőség-kezelési platformra vonatkozó előfizetéssel rendelkezik, manuálisan telepítheti a sebezhetőségi felmérési ügynököket a Batch-készlet csomópontjain, és kezelheti a csomópontokat a megfelelő portálon keresztül.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Microsoft az alapszintű Azure batch Pool-csomópontok rendszerképeinek karbantartásához és frissítéséhez. Győződjön meg arról, hogy Azure Batch a készlet csomópontjainak operációs rendszere továbbra is a fürt élettartama alatt marad, ami szükségessé teheti az automatikus frissítések engedélyezését, a csomópontok figyelését vagy rendszeres újraindítást.

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: automatizált Third Party Software javítás-felügyeleti megoldás telepítése

**Útmutató**: Azure batch Pool-csomópontok harmadik féltől származó alkalmazásai a fürt élettartamának időtartamára kiterjedően maradnak, ami szükségessé teheti az automatikus frissítések engedélyezését, a csomópontok figyelését vagy az időszakos újraindítást.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: Ha Rapid7, Qualys vagy bármely más sebezhetőségi kezelési platformra vonatkozó előfizetéssel rendelkezik, használhatja a gyártó portálját a biztonsági rések vizsgálatának megtekintésére és összehasonlítására.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését.

**Útmutató**: általános kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az előfizetése (i) n belüli összes erőforrást (például a számítási, tárolási, hálózati stb.). Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.


Bár a klasszikus Azure-erőforrások felderítése az erőforrás-gráfon keresztül lehetséges, erősen ajánlott Azure Resource Manager (ARM) erőforrások létrehozása és használata.


Lekérdezések létrehozása az Azure Graph használatával:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Az Azure-előfizetések megtekintése:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Az Azure RBAC ismertetése:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.


Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.



További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription



Management Groups létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create



Létrehozás és felhasználói Címkék:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának karbantartása.

**Útmutató**: a jóváhagyott Azure-erőforrások és jóváhagyott szoftverek listájának meghatározása számítási erőforrásokhoz

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok


Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.


Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Lekérdezések létrehozása az Azure Graph használatával: https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: Azure batch Pool-csomópontok esetében hozzon létre egy külső gyártótól származó megoldást, amely figyeli a nem jóváhagyott szoftveralkalmazások fürt csomópontjait.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: Azure batch Pool-csomópontok esetében hozzon létre egy külső gyártótól származó megoldást, amely figyeli a nem jóváhagyott szoftveralkalmazások fürt csomópontjait.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: Azure batch Pool-csomópontok esetében hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan szoftverek futtatását.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:


- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok


Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Adott erőforrástípus megtagadása a következővel: Azure Policy: https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: Azure batch Pool-csomópontokhoz hozzon létre egy harmadik féltől származó megoldást, amely megakadályozza a jogosulatlan fájltípusok végrehajtását.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>A felhasználók Azure Resource Manager interakciójának korlátozása parancsfájlok használatával</div>

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.


A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához: https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható,

Ez nem alkalmazható Azure Batchre, mert a Azure Batch-készletek felhasználói (nem rendszergazdák) nem igényelnek hozzáférést az egyes csomópontokhoz a feladatok futtatásához. A fürt rendszergazdája már rendelkezik rendszergazdai hozzáféréssel az összes csomóponthoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható, a teljesítményteszt a Azure app Service-vagy IaaS-példányokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. batch" névtérben egyéni szabályzatok létrehozásához a Azure batch-fiókok és-készletek konfigurációjának naplózásához vagy érvényesítéséhez.


Az elérhető Azure Policy aliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7,2: biztonságos konfigurációk létrehozása az operációs rendszerhez

**Útmutató**: biztonságos konfigurációk létrehozása a Batch Pool-csomópontok operációs rendszeréhez.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7,3: biztonságos konfigurációk karbantartása az összes Azure-erőforráshoz

**Útmutató**: a Batch-fiókhoz és-készletekhez (például virtuális hálózatokhoz, alhálózatokhoz, Azure-tűzfalakhoz, Azure Storage-fiókokhoz stb.) kapcsolódó Azure-erőforrások biztonságos beállításainak érvénybe léptetéséhez használja az Azure Policy [deny] és a [telepítés, ha nem létezik] lehetőséget. A következő névterekben található Azure Policy aliasokat használhatja egyéni házirendek létrehozásához:

- Microsoft.Batch
- Microsoft.Storage
- Microsoft.Network


Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Azure Policy effektusok ismertetése: https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7,4: biztonságos konfigurációk karbantartása operációs rendszerekhez

**Útmutató**: Azure Batch a Microsoft által felügyelt és karbantartott készlet operációs rendszer lemezképeit. Az operációs rendszer szintű állapot konfigurációjának végrehajtásáért felelős.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ a Azure batch fiókokhoz, készletekhez vagy kapcsolódó erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.


Kód tárolása az Azure DevOps:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops


Az Azure Repos dokumentációja:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni rendszerképeket használ a Azure batch készletekhez, használja a szerepköralapú hozzáférés-vezérlést (RBAC) annak biztosítására, hogy csak a jogosult felhasználók férhessenek hozzá a lemezképekhez.


A RBAC megismerése az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles


A RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: beépített Azure Policy-definíciók használata a Azure batch kapcsolódó erőforrás-konfigurációk riasztásához, naplózásához és érvényesítéséhez.  Használjon Azure Policy aliasokat a "Microsoft. batch" névtérben, hogy egyéni szabályzatokat hozzon létre a Azure Batch-fiókokhoz és-készletekhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirend-kivételek kezeléséhez.



Azure Policy konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: egy külső gyártótól származó megoldás implementálása a Azure batch Pool-csomópontok operációs rendszereinek kívánt állapotának fenntartásához.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: Azure Policy aliasok használata a "Microsoft. batch" névtérben egyéni szabályzatok létrehozásához a Azure batch példányának naplózásához vagy érvényesítéséhez. A kifejezetten a Azure Batchhoz létrehozott beépített szabályzatokat vagy a Azure Batch által használt erőforrásokat is használhatja, például:

- Az alhálózatokat hálózati biztonsági csoporttal kell társítani – a Storage-fiókoknak virtuális hálózati szolgáltatási végpontot kell használniuk
- A Batch-fiókokban lévő diagnosztikai naplókat engedélyezni kell

Az elérhető Azure Policy aliasok megtekintése: https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Azure Policy konfigurálása és kezelése: https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: külső gyártótól származó megoldás implementálása Azure batch Pool-csomópontok operációs rendszerei állapotának figyeléséhez.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="711-securely-manage-azure-secrets"></a>7,11: biztonságos felügyelet az Azure Secrets-ben

**Útmutató**: az Azure Storage-fiókokon belüli készlet-tárolás kulcsainak kezeléséhez Azure Key Vault használható Azure batch üzemelő példányok esetében.


Integráció az Azure felügyelt identitásokkal:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity


Azure Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/quick-create-portal


Felügyelt identitással rendelkező Key Vault hitelesítés biztosítása:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="712-securely-and-automatically-manage-identities"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: nem érhető el, Managed Service Identity Azure batch nem támogatott

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze. 

Hitelesítőadat-olvasó beállítása: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: a Windows Defender használata a Windows operációs rendszerek esetében az egyes Azure batch Pool-csomópontokon, illetve a Linux használata esetén saját kártevő szoftvereket is biztosíthat.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure batch), azonban nem az ügyfél tartalmán fut.


A nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage stb. A Microsoft nem fér hozzá az ügyféladatokat ezekben a példányokban.


Ismerje meg a Microsoft antimalware az Azure Cloud Services és Virtual Machines:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: a Windows Defender használata az egyes Azure batch Pool-csomópontokon Windows operációs rendszerek esetén, valamint az automatikus frissítés engedélyezése. A Linux használata esetén saját kártevő-ellenes megoldást biztosíthat.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Ha Azure Storage-fiókot használ a Azure batch készlet adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS). 


Tárolási redundancia konfigurálása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: Ha Azure Storage-fiókot használ a Azure batch készlet adattárához, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, ra-GRS).  Ha a Azure Batch üzembe helyezésének bármely részéhez Azure Key Vault használ, győződjön meg róla, hogy a kulcsok biztonsági mentése megtörténik.


Tárolási redundancia konfigurálása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Key Vault-kulcsok biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: Ha saját kulcsokat felügyel az Azure Storage-fiókokhoz vagy az Azure batch-implementációhoz kapcsolódó bármely más erőforráshoz, a biztonsági másolatban szereplő kulcsok visszaállítását rendszeresen tesztelje.


Key Vault-kulcsok biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0


Ügyfél által felügyelt kulcs visszaállítása a PowerShell-lel:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: ha a Azure Key Vault a Azure batch Pool Storage-fiókokhoz kapcsolódó kulcsok tárolására használatos, engedélyezze a helyreállítható törlést Azure Key Vault a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához.


A Soft delete engedélyezése a Azure Key Vaultban:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10,1: incidens-válasz létrehozási útmutatója

**Útmutató**: Ellenőrizze, hogy vannak-e olyan írásos incidensek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.



Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10,2: incidens-pontozási és rangsorolási eljárás létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához, valamint a programok gyakorlati képzéséhez: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása &nbsp;biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensekkel kapcsolatos kapcsolattartási adatokat arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az Ön adatait egy törvénytelen vagy jogosulatlan fél is hozzáférte.



A Azure Security Center biztonsági kapcsolattartó beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Az Azure Security Center adatösszekötővel továbbíthatja a riasztásokat az Azure Sentinel szolgáltatásba.


Folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export


Riasztások továbbítása az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10,6: a biztonsági riasztásokra adott válasz automatizálása

**Útmutató**: a Azure Security Center munkafolyamat-automatizálási funkciója segítségével automatikusan aktiválhatja a válaszokat a "Logic apps" használatával a biztonsági riasztások és javaslatok esetében.



A Munkafolyamat-automatizálás és a Logic Apps konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat 60 napon belüli szervizelésének biztosítása.

**Útmutató**: kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék meg a Microsoft-házirendeket:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos további információkért tekintse meg a következő témakört: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági Alaptervekről](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
