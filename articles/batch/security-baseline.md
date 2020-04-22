---
title: Azure biztonsági alapkonfiguráció köteghez
description: Azure biztonsági alapkonfiguráció köteghez
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8bda712523faa90f32a12fe7eecce27dccf3a55c
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756632"
---
# <a name="azure-security-baseline-for-batch"></a>Azure biztonsági alapkonfiguráció köteghez

Az Azure Security Baseline for Batch javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Telepítse az Azure Batch-készlet(ek) virtuális hálózaton belül. Annak engedélyezéséhez, hogy a készlet számítási csomópontjai biztonságosan kommunikálhassanak más virtuális gépekkel vagy egy helyszíni hálózattal, kiépítheti a készletet egy Azure virtuális hálózat alhálózatában. Emellett a készlet virtuális hálózaton belüli üzembe helyezése lehetővé teszi az egyes csomópontok hálózati összeköttetések (NIC) és az alhálózat védelmére használt hálózati biztonsági csoport (NSG) vezérlését. Állítsa be az NSG-t úgy, hogy csak megbízható IP-címről/helyekről engedélyezze a forgalmat az interneten.

Azure-kötegkészlet létrehozása virtuális hálózaton belül:

https://docs.microsoft.com/azure/batch/batch-virtual-network

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és a NICS konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és orvosolja a kötegkészlethez társított virtuális hálózati/ hálózati biztonsági csoporthoz (NSG) kapcsolódó hálózatvédelmi javaslatokat. Engedélyezze a folyamatnaplókat a batch-készlet védelmére használt NSG-n, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. NSG-folyamatnaplókat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. Az Azure Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati téves konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Az Azure Security Center által biztosított hálózati biztonság megismerése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: A kritikus fontosságú webes alkalmazások védelme

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Engedélyezze az Azure DDoS (elosztott szolgáltatásmegtagadási) standard szintű védelmet a virtuális hálózaton, amely védi az Azure Batch-készletet a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A DDoS-védelem konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Folyamatnaplók engedélyezése az Azure Batch-készlet védelmére használt hálózati biztonsági csoporton (NSG), és naplók küldése egy Azure Storage-fiókba a forgalom naplózása érdekében.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems"></a>1.6: Hálózatalapú behatolásérzékelő/behatolásmegelőző rendszerek telepítése

**Útmutatás:** Ha megfelelőségi célokból szükséges, válasszon ki egy hálózati virtuális berendezést az Azure Piactérről, amely támogatja a behatolásészlelési rendszereket (IDS) és a behatolásmegelőző rendszerek (IPS) funkcióit a hasznos terhelés vizsgálati képességekkel.

Ha a behatolásészlelés és/vagy a hasznos adatvizsgálaton alapuló megelőzés nem követelmény, az Azure Firewall fenyegetésfelderítési szolgáltatással használható. Az Azure Firewall fenyegetésintelligencia-alapú szűrés riasztást adhat és megtagadhatja az ismert rosszindulatú IP-címekre és tartományokra irányuló forgalmat. Az IP-címek és tartományok a Microsoft Threat Intelligence hírcsatornából származnak.

Telepítse az Azure Firewall nyilvános IP-címet ugyanabban a virtuális hálózatban, mint az Azure Batch Pool-csomópontok. Konfigurálja a hálózati címfordítási (NAT) szabályokat az interneten lévő megbízható helyek és az egyes készletcsomópontok privát IP-címei között. Az Azure tűzfalon a Fenyegetésintelligencia alatt konfigurálja a "Riasztás és megtagadás" beállítást az ismert rosszindulatú IP-címek és tartományok irányuló/ onnan érkező forgalom riasztásához és blokkolásához. Az IP-címek és tartományok a Microsoft Threat Intelligence hírcsatornából származnak, és csak a legmagasabb megbízhatósági rekordok szerepelnek. 

Azure-kötegkészlet létrehozása virtuális hálózaton belül:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Az Azure Tűzfal telepítése:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Azure Piactér:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-your-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás:** Nem alkalmazható, a Benchmark az Azure App Service- vagy IaaS-példányokon futó webalkalmazásokhoz készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutatás:** A virtuális hálózati szolgáltatás címkék segítségével határozza meg a hálózati hozzáférés-vezérlésa a hálózati biztonsági csoportok vagy az Azure Batch-készlet(ek) társított Azure-csoport.Guidance: Use virtual network service tags to define network access controls on network security groups or Azure Firewalls associated with your Azure Batch pool(s). Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

A szolgáltatáscímkék ismertetése és használata:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása az Azure Batch-készlet(ek)hez az Azure-szabályzattal társított hálózati erőforrásokhoz. A "Microsoft.Batch" és a "Microsoft.Network" névterekben az Azure-szabályzataliasok használatával egyéni szabályzatokat hozhat létre az Azure Batch-készletek hálózati konfigurációjának naplózásához vagy érvényesítéséhez.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: Dokumentum forgalom konfigurációs szabályok

**Útmutató:** Címkék használata hálózati szolgáltatáscsoportok (NSG-k) és egyéb, a hálózati biztonsághoz és az Azure-kötegkészletekhez társított hálózati biztonsághoz és forgalomhoz kapcsolódó erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezőben adhatja meg az üzleti igényt és/vagy az időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló/onnan érkező forgalmat.

Használja a címkézéssel kapcsolatos beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy minden erőforrás címkékkel jön létre, és értesítheti a meglévő címkézetlen erőforrásokról.

Használhatja az Azure PowerShell vagy az Azure CLI a look-up, vagy műveleteket hajt végre az erőforrások alapján a címkéket.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Hogyan hozzunk létre egy virtuális hálózat:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Hogyan hozzunk létre egy NSG:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure Batch-készletekhez kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view 

Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizálási források használata

**Útmutató:** Az Azure Batch esetében a Microsoft alapértelmezés szerint időszinkronizálást biztosít. Ha azonban konkrét időszinkronizálási követelményekkel rendelkezik, ezeket a módosításokat megvalósíthatja.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure Batch-fiók az Azure Monitorra a fürteszközök által létrehozott biztonsági adatok összesítéséhez. Az egyéni lekérdezések kihasználásával észlelheti és reagálhat a környezetben lévő fenyegetésekre.  Az Azure Batch erőforrásszintű figyelése, a Batch API-k segítségével az erőforrások állapotának figyelése vagy lekérdezése, beleértve a feladatok, feladatok, csomópontok és készletek.

Azure Batch-fiók berendelése az Azure Monitorba:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Naplózás engedélyezése az Azure Resources számára

**Útmutató:** Az Azure Batch-fiók szintfigyeléséhez figyelheti az egyes Batch-fiókokat az Azure Monitor funkcióival. Az Azure Monitor metrikákat és opcionálisan diagnosztikai naplókat gyűjt a Batch-fiók szintjén, például készletek, feladatok és feladatok hatóköre i. erőforrásokhoz. Ezeket az adatokat manuálisan vagy programozott módon gyűjtse össze és használja fel a Batch-fiók tevékenységeinek figyeléséhez és a problémák diagnosztizálásához.

Az Azure Batch erőforrásszint-figyeléséhez használja az Azure Batch API-kat az erőforrások állapotának figyelésére vagy lekérdezésére, beleértve a feladatokat, feladatokat, csomópontokat és készleteket.

Az Azure Batch-fiókszintű figyelés és naplózás konfigurálása:

https://docs.microsoft.com/azure/batch/monitoring-overview

A Kötegelt erőforrásszintű figyelés ismertetése:

https://docs.microsoft.com/azure/batch/monitoring-overview#batch-resource-monitoring

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-system"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerből

**Útmutatás: Az**Azure Monitor az Azure Batch-fiókban lévő erőforrások metrikákat és diagnosztikai naplókat gyűjti. Gyűjtse össze és használja fel ezeket az adatokat az Azure Batch-fiók figyelése és a problémák diagnosztizálása számos módon. Metrikariasztásokat is konfigurálhat, így értesítést kaphat, ha egy metrika elér egy megadott értéket.

Ha szükséges, a helyi operációs rendszer naplóinak eléréséhez csatlakozhat az egyes készletcsomópontokhoz a Biztonságos rendszerhéj (SSH) vagy a Remote Desktop Protocol (RDP) segítségével.

Diagnosztikai naplók gyűjtése az Azure Batch-fiókból:

https://docs.microsoft.com/azure/batch/batch-diagnostics#batch-diagnostics

Hogyan csatlakozhat távolról az Azure Batch-készlet csomópontjaihoz:

https://docs.microsoft.com/azure/batch/batch-api-basics#error-handling

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának konfigurálása

**Útmutató:** Az Azure Batch-fiók az Azure Monitorra. Győződjön meg arról, hogy az Azure Log Analytics használt munkaterület a szervezet megfelelőségi szabályzatainak megfelelően beállított naplómegőrzési időszakkal rendelkezik

Az Azure Batch figyelésének és naplózásának konfigurálása:

https://docs.microsoft.com/azure/batch/monitoring-overview

Az Azure Log Analytics munkaterület-megőrzési időszakának konfigurálása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók nyomon követése és felülvizsgálata

**Útmutatás:** Hozzon létre Azure Batch metrika riasztások, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlépi egy adott küszöbértéket.

Az Azure Batch metrikariasztásai konfigurálása:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutatás:** Hozzon létre Azure Batch metrika riasztások, amelyek akkor aktiválódnak, ha egy adott metrika értéke átlépi egy adott küszöbértéket.

Az Azure Batch metrikariasztásai konfigurálása:

https://docs.microsoft.com/azure/batch/batch-diagnostics

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó rendszer központosítása

**Útmutató:** A Windows operációs rendszerek esetében használja a Windows Defender rendszert az egyes kötegcsomópontokon, vagy biztosítson saját kártevőirtó megoldást Linux használata esetén.

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezés naplózásának engedélyezése

**Útmutató:** Harmadik féltől származó megoldás megvalósítása a dns naplózáshoz

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató:** A konzolnaplózás és a PowerShell-átírás manuális konfigurálása csomópontonként.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-inventory-of-administrative-accounts"></a>3.1: Az igazgatási számlák leltárának karbantartása

**Útmutató:** Az Azure Batch-készlet kiépítése során létrehozott helyi felügyeleti fiók, valamint a létrehozott egyéb fiókok rekordjának karbantartása. Emellett az Azure Active Directory (AAD) integráció, az AAD beépített szerepkörök, amelyek explicit módon hozzá kell rendelni, és ezért lekérdezhető. Az AAD PowerShell modul segítségével adhoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjaiként rendelkező fiókok felderítéséhez.

Emellett használhatja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Címtárszerepkör beszereznie az AAD-ben a PowerShell használatával:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárszerepkör tagjainak beszereznie az AAD-ben a PowerShell használatával:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Az identitás okának és a hozzáférésnek az Azure Security Centerrel való figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Az alapértelmezett jelszavak módosítása adott esetben

**Útmutató:** Az Azure Batch-készlet kiépítésekor lehetőséget kap helyi számítógép-fiókok létrehozására. Nincsenek módosítható alapértelmezett jelszavak, azonban különböző jelszavakat adhat meg a Biztonságos rendszerhéj (SSH) és a Távoli asztali protokoll (RDP) eléréséhez. Az Azure Batch-készlet konfigurálása után létrehozhat egy véletlenszerű felhasználót az azure-portálon belüli egyes csomópontokhoz, vagy az Azure Resource Manager API-n keresztül.

Felhasználó hozzáadása adott számítási csomóponthoz:

https://docs.microsoft.com/rest/api/batchservice/computenode/adduser

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="33-ensure-the-use-of-dedicated-administrative-accounts"></a>3.3: A dedikált adminisztratív számlák használatának biztosítása

**Útmutató:** Az Azure Batch-alkalmazások hitelesítésének integrálása az Azure Active Directoryval. Házirendeket és eljárásokat hozhat létre a dedikált felügyeleti fiókok használatával kapcsolatban.

Emellett használhatja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Kötegelt alkalmazások hitelesítése az Azure Active Directoryval:

https://docs.microsoft.com/azure/batch/batch-aad-auth

Az identitás okának és a hozzáférésnek az Azure Security Centerrel való figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="34-utilize-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) kihasználása az Azure Active Directoryval

**Útmutatás:** Nem alkalmazható, míg az Azure Batch támogatja az Azure AD-hitelesítést, egyszeri bejelentkezés nem támogatott.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="35-use-multifactor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Használja a többtényezős hitelesítés t az összes Azure Active Directory-alapú hozzáféréshez.

**Útmutató:** Az Azure Batch-alkalmazások hitelesítésének integrálása az Azure Active Directoryval (AAD). Engedélyezze az AAD többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

 

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Dedikált gépek (kiemelt hozzáférésű munkaállomások) használata minden felügyeleti feladathoz

**Útmutató:** Emelt szintű munkaállomások (privilegizált hozzáférési munkaállomások) használata többtényezős hitelesítéssel (MFA) konfigurálva az Azure Batch-erőforrások ba való bejelentkezéshez és konfigurálásához.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: Napló és riasztás a gyanús tevékenységekről az adminisztratív fiókokból

**Útmutató:** Ha integrált hitelesítést rendelkezik az Azure Batch-alkalmazások hoz az Azure Active Directory (AAD), azure Active Directory biztonsági jelentések létrehozásához naplók és riasztások, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure Security Center használatával figyelheti az identitás- és hozzáférési tevékenységeket.

Kockázatos tevékenységesetén megjelölt Azure AD-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitásának és hozzáférési tevékenységének figyelése az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resource-from-only-approved-locations"></a>3.8: Az Azure-erőforrás kezelése csak jóváhagyott helyekről

**Útmutató:** Ha integrált hitelesítést az Azure Batch-alkalmazások az Azure Active Directory, használhatja feltételes hozzáférés elnevezett helyek, hogy csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezi a hozzáférést.

Named-helyek konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directoryt (AAD) központi hitelesítési és engedélyezési rendszerként, és integrálja az Azure Batch-alkalmazások hitelesítését az AAD-vel. Az AAD az adatokat erős titkosítással védi az inaktív és az átvitel során tárolt adatokhoz. Az AAD a felhasználói hitelesítő adatokat is megsózta, kiigazítja és biztonságosan tárolja.

AAD-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

A Batch-alkalmazások hitelesítése az AAD-vel:

https://docs.microsoft.com/azure/batch/batch-aad-auth

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory(AAD) naplókat biztosít az elavult fiókok felderítéséhez. Emellett használhatja az Azure Identity Access Reviews segítségével hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő felhasználók rendelkezhessenek folyamatos hozzáféréssel.

Az Azure Identity Access-vélemények használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató:** Diagnosztikai beállítások létrehozása az Azure Active Directory felhasználói fiókokhoz, a naplónaplók és a bejelentkezési naplók küldése az Azure Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat az Azure Log Analytics-munkaterületen belül.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiókbejelentkezési viselkedés eltéréséről

**Útmutató:** Az Azure Active Directory (AAD) kockázatészlelési és identitásvédelmi szolgáltatás használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Emellett további vizsgálat céljából adatokat is bevihet az Azure Sentinelbe.

Az AAD kockázatos bejelentkezések megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel fedélzeti szolgáltatása:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-divprovide-microsoft-with-access-to-relevant-customer-data-during-support-scenariosbrdiv"></a>3.13: <div>Hozzáférés biztosítása a Microsoft számára a releváns ügyféladatokhoz támogatási forgatókönyvek során<br></div>

**Útmutató**: Nem áll rendelkezésre; Az Ügyfélszéf még nem támogatott az Azure Batch számára.
 
Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Címkék használatával segítséget nyújt a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követéséhez.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. Az Azure Batch-készleteket virtuális hálózatnak/alhálózatnak kell elválasztani, megfelelően címkézni és hálózati biztonsági csoportokkal (NSG) kell biztosítani. Az Azure Batch-adatokat egy biztonságos Azure Storage-fiókban kell tárolni.

Azure-kötegkészlet létrehozása virtuális hálózaton belül:

https://docs.microsoft.com/azure/batch/batch-virtual-network

Az Azure Storage-fiókok védelme:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Figyelemmel kíséri és blokkolja a bizalmas adatok jogosulatlan továbbítását.

**Útmutató:** Az Azure Storage-fiókok társított Azure Batch Pool(ok), amelyek bizalmas információkat tartalmaznak, jelölje meg őket bizalmas címkék használatával, és biztonságossá őket az Azure gyakorlati.

Az Azure Storage- és számítási erőforrásokhoz még nem érhetők el adatazonosítási, besorolás- és veszteségmegelőzési funkciók. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Az Azure Storage-fiókok védelme:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató:** Az összes bizalmas információ titkosítása az átvitel során. A Microsoft Azure-erőforrások alapértelmezés szerint egyeztetik a TLS 1.2-t. Győződjön meg arról, hogy az Azure Batch-készletekhez vagy adattárakhoz (Azure Storage-fiókok) csatlakozó ügyfelek képesek-e a TLS 1.2-es vagy nagyobb egyeztetésére.

Győződjön meg arról, hogy a HTTPS szükséges az Azure Batch-adatokat tartalmazó tárfiók eléréséhez.

Az Azure Storage-fiók titkosításának ismertetése átvitel közben:

https://docs.microsoft.com/azure/storage/common/storage-security-guide#encryption-in-transit

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az Azure Storage-fiókok társított Azure Batch Pool(ok), amelyek bizalmas információkat tartalmaznak, jelölje meg őket bizalmas címkék használatával, és biztonságossá őket az Azure gyakorlati.

Az Azure Storage- és számítási erőforrásokhoz még nem érhetők el adatazonosítási, besorolás- és veszteségmegelőzési funkciók. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

Az Azure Storage-fiókok védelme:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure Active Directory(AAD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja az Azure-erőforrások felügyeleti síkjához való hozzáférést, beleértve a batch-fiókot, a kötegkészlet(eke)t és a storage-fiókokat.

Az Azure RBAC megismerése:

https://docs.microsoft.com/azure/role-based-access-control/overview

Az RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagép-alapú adatveszteség-megelőzéshasználatával

**Útmutató:** Az adatok azonosításával, besorolásával és a veszteségmegelőzési funkciókmég nem érhetők el az Azure Storage- vagy számítási erőforrásokhoz. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Az Azure Batch-fiókhoz társított tárfiókok esetében ajánlott engedélyezni a Microsoft számára a titkosítási kulcsok kezelését, azonban szükség esetén kezelheti a saját kulcsait.

Az Azure Storage-fiókok titkosítási kulcsainak kezelése:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure-figyelő és az Azure-tevékenységnapló használatával riasztásokat hozhat létre az Azure Batch-fiókokhoz/-készletekhez kapcsolódó vagy azokhoz társított kritikus Azure-erőforrások módosításakor.

Konfigurálja az Azure Batch-készlethez társított tárfiókok diagnosztikai beállításait az összes CRUD-művelet figyeléséhez és naplózásához a készletadatokkal.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

További naplózás/naplózás engedélyezése egy Azure Storage-fiókhoz:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági résellenőrző eszközök futtatása

**Útmutató:** Az Azure Batch Pool-csomópontok, ön felelős a biztonsági rés kezelési megoldás kezeléséért.

Ha Rapid7, Qualys vagy bármely más biztonsági réskezelési platform-előfizetéssel rendelkezik, manuálisan telepítheti a biztonsági rések felmérésére szolgáló ügynököket a Batch készletcsomópontokra, és kezelheti a csomópontokat az adott portálon keresztül.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutató: A**Microsoft az alap Azure Batch Pool-készlet csomópontrendszerképek karbantartásához és frissítéséhez. Győződjön meg arról, hogy az Azure Batch Pool-csomópontok operációs rendszere a fürt élettartama alatt is javításra szorul, amely automatikus frissítések engedélyezését, a csomópontok figyelését vagy az időszakos újraindításokat igényelheti.


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutató:** Győződjön meg arról, hogy az Azure Batch Pool-csomópontok külső alkalmazások továbbra is javítva a fürt élettartama alatt, amely szükség lehet az automatikus frissítések engedélyezéséhez, a csomópontok figyelése, vagy időszakos újraindítások végrehajtása.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Összehasonlítás back-to-back biztonsági rés vizsgál

**Útmutató:** Rapid7, Qualys vagy bármely más biztonsági réskezelő platform előfizetés e esetén használhatja az adott szállító portálját a biztonsági rések megtekintéséhez és összehasonlításához.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: A felfedezett biztonsági rések helyreállításának fontossági sorrendbe adására kockázatminősítési eljárást kell alkalmazni.

**Útmutató:** Használjon közös kockázatpontozási programot (pl. közös biztonsági réspontozási rendszert) vagy a külső ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás:** Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati stb.) az előfizetés(ek)en belül. Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes-e az összes Azure-előfizetés, valamint az előfizetéseken belüli erőforrások számbavételére.

Bár a klasszikus Azure-erőforrások fedezhetők fel a Resource Graph,erősen ajánlott azure Resource Manager (ARM) erőforrások létrehozása és használata a jövőben.

Lekérdezések létrehozása az Azure Resource Graph segítségével:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszköz metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása az Azure-erőforrásokra, amelyek metaadatokat adnak, hogy logikusan rendszerezzék őket egy taxonómiába.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Adott esetben címkézéssel, felügyeleti csoportokkal és külön előfizetésekkel rendszerezheti és nyomon követheti az eszközöket. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Hogyan hozzunk létre és felhasználói címkék:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása.

**Útmutató:** A jóváhagyott Azure-erőforrások és a számítási erőforrások jóváhagyott szoftvereinek listájának meghatározása


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával erőforrásokat kérdezhet le/fedezhet fel az előfizetése(i)n belül. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóváhagyásra kerül.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Az Azure Batch Pool-csomópontok, valósítson meg egy harmadik féltől származó megoldás a nem jóváhagyott szoftveralkalmazások fürtcsomópontok figyelésére.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató:** Az Azure Batch Pool-csomópontok, valósítson meg egy harmadik féltől származó megoldás a nem jóváhagyott szoftveralkalmazások fürtcsomópontok figyelésére.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutató:** Az Azure Batch Pool-csomópontok, valósítson meg egy harmadik féltől származó megoldást, hogy megakadályozzák a jogosulatlan szoftverek végrehajtását.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása az Azure-szabályzattal:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutató:** Az Azure Batch Pool-csomópontok, valósítson meg egy harmadik féltől származó megoldás, hogy megakadályozzák a jogosulatlan fájltípusok végrehajtását.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resource Managerrel</div>

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával.

A feltételes hozzáférés beállítása az Azure Resource Manager elérésének letiltásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutató**: Nem alkalmazható,

Ez nem vonatkozik az Azure Batch, a felhasználók (nem rendszergazdák) az Azure Batch-készletek nem kell hozzáférni az egyes csomópontok feladatok futtatásához. A fürt rendszergazdája már rendelkezik root hozzáféréssel az összes csomóponthoz.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazások

**Útmutatás:** Nem alkalmazható, a Benchmark az Azure App Service- vagy IaaS-példányokon futó webalkalmazásokhoz készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Azure Policy aliasok a "Microsoft.Batch" névtérben egyéni szabályzatok létrehozásához az Azure Batch-fiókok és készletek konfigurációjának naplózásához vagy érvényesítéséhez.

Az elérhető Azure Policy-aliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-configurations-for-your-operating-system"></a>7.2: Biztonságos konfigurációk létrehozása az operációs rendszerhez

**Útmutató:** Hozzon létre biztonságos konfigurációkat a batch pool-csomópontok operációs rendszeréhez.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-configurations-for-all-azure-resources"></a>7.3: Biztonságos konfigurációk karbantartása az összes Azure-erőforráshoz

**Útmutató:** Az Azure-szabályzat [megtagadás] és a [deploy, ha nem létezik] használatával kényszerítheti a batch-fiókhoz és készletekhez kapcsolódó Azure-erőforrások (például virtuális hálózatok, alhálózatok, Azure-tűzfalak, Azure Storage-fiókok stb.) biztonságos beállításait. Az azure-szabályzat aliasait a következő névterekből egyéni szabályzatok létrehozásához használhatja:

- Microsoft.Batch

- Microsoft.Storage

- Microsoft.Network

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-szabályzat hatásainak megismerése:https://docs.microsoft.com/azure/governance/policy/concepts/effects


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-configurations-for-operating-systems"></a>7.4: Biztonságos konfigurációk karbantartása az operációs rendszerekhez

**Útmutató:** Az Azure Batch Pool operációsrendszer-lemezképek, amelyeket a Microsoft kezel és karbantart. Ön felelős az operációs rendszer szintű állapotkonfiguráció megvalósításáért.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása az Azure-erőforrások konfigurálásában

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ az Azure Batch-fiókokhoz, készletekhez vagy kapcsolódó erőforrásokhoz, használja az Azure Repos-t a kód biztonságos tárolásához és kezeléséhez.

Kód tárolása az Azure DevOps-ban:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolja az operációs rendszer egyedi lemezképeit

**Útmutató:** Ha egyéni lemezképek et használ az Azure Batch-készletek, használja a szerepköralapú hozzáférés-vezérlés (RBAC) annak biztosításához, hogy csak a jogosult felhasználók férhetnek hozzá a rendszerképekhez.

Az RBAC ismertetése az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles

Az RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: A rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutatás:** A beépített Azure-szabályzat-definíciók használatával riasztást, naplózást és kényszerítést az Azure Batch-hez kapcsolódó erőforrás-konfigurációk.  A "Microsoft.Batch" névtérben az Azure Policy-aliasok használatával egyéni szabályzatokat hozhat létre az Azure Batch-fiókokhoz és -készletekhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutató:** Valósítson meg egy külső gyártótól származó megoldást az Azure Batch Pool-csomópontok operációs rendszerei kívánt állapotának fenntartásához.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** Azure Policy aliasok a "Microsoft.Batch" névtérben egyéni szabályzatok létrehozásához az Azure Batch-példány naplózása vagy kényszerítése. Használhatja a kifejezetten az Azure Batchhez létrehozott beépített szabályzatokat vagy az Azure Batch által használt erőforrásokat is, például:

- Az alhálózatokat hálózati biztonsági csoporthoz kell társítani

-A tárfiókoknak virtuális hálózati szolgáltatás végpontját kell használniuk

- A Batch-fiókok diagnosztikai naplóit engedélyezni kell

Az elérhető Azure Policy-aliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatikus konfigurációfigyelésének megvalósítása

**Útmutató:** Harmadik féltől származó megoldás megvalósítása az Azure Batch-készlet csomópontjaioperációs rendszerei állapotának figyelésére.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="711-securely-manage-azure-secrets"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure Key Vault azure batch-telepítésekkel használható az Azure Storage-fiókokon belüli készlettárolás kulcsainak kezeléséhez.

Integrálás az Azure felügyelt identitásaival:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Azure Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-securely-and-automatically-manage-identities"></a>7.12: Biztonságosés automatikus annektálta a személyazonosságot

**Útmutató:** Nem érhető el, az Azure Batch nem támogatja a felügyelt szolgáltatásidentitást


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

**Útmutató:** A hitelesítő adatok at a kódon belüli hitelesítő adatok azonosítására valósíthatja meg. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault. 

A Hitelesítő adatok képolvasójának beállítása:https://secdevtools.azurewebsites.net/helpcredscan.html

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag kezelt kártevőirtó szoftverek használata

**Útmutató:** A Windows-operációs rendszerek esetében használja a Windows Defender szolgáltatást az egyes Azure Batch-készletcsomópontokon, vagy biztosítson saját kártevőirtó megoldást Linux használata esetén.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft Antimalware engedélyezve van az alapul szolgáló gazdagépen, amely támogatja az Azure-szolgáltatásokat (például az Azure Batch), de nem fut az ügyfél tartalom.

A nem számítási Azure-erőforrásokba feltöltött fájlok, például az App Service, a Data Lake Storage, a Blob Storage stb. A Microsoft ezekben az esetekben nem fér hozzá az ügyféladatokhoz.

Ismerje meg a Microsoft kártevőirtó szoftvereit az Azure Felhőszolgáltatásokhoz és a virtuális gépekhez:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: A kártevőirtó szoftverek és aláírások frissítésének biztosítása

**Útmutató:** Windows operációs rendszerek esetén használja a Windows Defender t az egyes Azure Batch-készletcsomópontokon, és győződjön meg arról, hogy az automatikus frissítés engedélyezve van. Biztosítson saját kártevőirtó megoldást Linux használata esetén.


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatot kell biztosítani

**Útmutató:** Ha egy Azure Storage-fiók az Azure Batch Pool adattár, válassza ki a megfelelő redundancia-beállítás (LRS, ZRS, GRS, RA-GRS). 

A tárolási redundancia beállítása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés és biztonsági mentés bármely ügyfél által kezelt kulcsról

**Útmutató:** Ha egy Azure Storage-fiók az Azure Batch Pool adattár, válassza ki a megfelelő redundancia-beállítás (LRS, ZRS, GRS, RA-GRS).  Ha az Azure Key Vault az Azure Batch-telepítés bármely részében, győződjön meg a kulcsokról.

A tárolási redundancia beállítása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy

A kulcstároló kulcsainak biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Ha az Azure Storage-fiókok saját kulcsait vagy az Azure Batch-implementációhoz kapcsolódó bármely más erőforrást kezel, rendszeresen tesztelje a biztonsági másolatot kérő kulcsok visszaállítását.

A kulcstároló kulcsainak biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

Ügyfél által kezelt kulcs visszaállítása a PowerShell használatával:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Ha az Azure Key Vault az Azure Batch Pool storage-fiókokhoz kapcsolódó kulcsok tárolására szolgál, engedélyezze a soft-delete szolgáltatást az Azure Key Vaultban a kulcsok véletlen vagy rosszindulatú törlés elleni védelme érdekében.

A beállítható törlés engedélyezése az Azure Key Vaultban:

https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidenskezelés/-kezelés fázisait.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás: A**Security Center súlyossági szintet rendel a riasztásokhoz, hogy segítsen rangsorolni az egyes riasztások hozadékát, így ha egy erőforrás biztonsága veszélybe kerül, azonnal hozzáérhet. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-nbspfor-security-incidents"></a>10.4: Adja meg a biztonsági &nbsp;incidensek elérhetőségét, és állítsa be a riasztási értesítéseket a biztonsági eseményekhez

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az ön adataihoz jogellenes vagy jogosulatlan fél fért hozzá.

Az Azure Security Center biztonsági kapcsolattartójának beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center riasztások és javaslatok a folyamatos exportálás funkció használatával. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötőt a riasztások streameléséhez az Azure Sentinel.

A folyamatos exportálás konfigurálása:

https://docs.microsoft.com/azure/security-center/continuous-export

Értesítések streamelése az Azure Sentinelbe:

https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" biztonsági riasztásokra és javaslatokra vonatkozó válaszokat.

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-to-remediate-all-critical-security-findings-within-60-days"></a>11.1: Végezzen rendszeres behatolási tesztelést az Azure-erőforrásokon, és 60 napon belül biztosítsa az összes kritikus biztonsági megállapítás elhárítását.

**Útmutató:** Kérjük, kövesse a Microsoft kötelezettségvállalási szabályzatát, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkat itt talál: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
