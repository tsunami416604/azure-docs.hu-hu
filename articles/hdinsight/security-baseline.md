---
title: Azure biztonsági alapkonfiguráció a HDInsighthoz
description: Azure biztonsági alapkonfiguráció a HDInsighthoz
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 61c2b914671020b822814fc283b5f2641c2e787b
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657460"
---
# <a name="azure-security-baseline-for-hdinsight"></a>Azure biztonsági alapkonfiguráció a HDInsighthoz

Az Azure Security Baseline for HDInsight olyan javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatások alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutatás:** Az Azure HDInsight perembiztonsága virtuális hálózatokon keresztül érhető el. A vállalati rendszergazda létrehozhat egy fürtöt egy virtuális hálózaton belül, és hálózati biztonsági csoport (NSG) használatával korlátozhatja a virtuális hálózathoz való hozzáférést. Csak a bejövő hálózati biztonsági csoport szabályokban engedélyezett IP-címek képesek lesznek kommunikálni az Azure HDInsight-fürttel. Ez a konfiguráció biztosítja a peremhálózat biztonságát. A virtuális hálózatban üzembe helyezett összes fürt rendelkezik egy privát végponttal is, amely a virtuális hálózaton belül egy privát IP-címre oldódik fel a fürtátjárók privát HTTP-hozzáféréséhez.


Az Azure HDInsight telepítése virtuális hálózaton belül és biztonságos egy hálózati biztonsági csoporttal:

https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és orvosolja a hálózatvédelmi javaslatokat az Azure HDInsight-fürt védelmére használt virtuális hálózathoz, alhálózathoz és hálózati biztonsági csoporthoz. Engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplókat, és küldje el a naplókat egy Azure Storage-fiókba a forgalom naplózásához. NSG-folyamatnaplókat is küldhet egy Azure Log Analytics-munkaterületre, és az Azure Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. Az Azure Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati téves konfigurációk azonosítása.


Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


Az Azure Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics


Az Azure Security Center által biztosított hálózati biztonság megismerése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; a benchmark az Azure Apps Service vagy a webalkalmazásokat tároló számítási erőforrások számára készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** A DDoS-támadások elleni védelemhez engedélyezze az Azure DDoS Standard védelmet azon a virtuális hálózaton, ahol az Azure HDInsight telepítve van. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.


A DDoS-védelem konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection


Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplóit az alhálózathoz csatlakoztatott NSG-hez, amelyet az Azure HDInsight-fürt védelmére használnak. Rögzítse az NSG-folyamatnaplókat egy Azure Storage-fiókba folyamatrekordok létrehozásához. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze az Azure Network Watcher csomagrögzítését.


Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal


A Network Watcher engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutatás**: Követelmény teljesíthető az Azure 1.1-es biztonsági ellenőrző azonosítója; Az Azure HDInsight-fürtöt virtuális hálózatba helyezheti, és egy hálózati biztonsági csoporttal (NSG) biztosítva.

Az Azure HDInsight számos függőséget igényel bejövő forgalmat. A bejövő felügyeleti forgalom nem küldhető tűzfaleszközön keresztül. A szükséges felügyeleti forgalom forráscímei ismertek és közzé vannak téve. Ezzel az információval hozzon létre hálózati biztonsági csoportszabályokat, hogy csak megbízható helyekről lehessen forgalmat bonyolítani, és ezzel biztosíthassa a fürtök felé irányuló bejövő forgalmat.

A HDInsight telepítése virtuális hálózaton belül és biztonságos egy hálózati biztonsági csoporttal:https://docs.microsoft.com/azure/hdinsight/hdinsight-create-virtual-network

A HDInsight-függőségek és a tűzfalhasználat megismerése:https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic

HDInsight felügyeleti IP-címek:https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; a benchmark az Azure Apps Service vagy a webalkalmazásokat tároló számítási erőforrások számára készült.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A virtuális hálózati szolgáltatás címkék segítségével határozza meg a hálózati hozzáférés-vezérlésa hálózati biztonsági csoportok (NSG), amelyek kapcsolódnak az alhálózat az Azure HDInsight-fürt telepítve van. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.


Az Azure HDInsight szolgáltatáscímkéinek ismertetése és használata:

https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása az Azure HDInsight-fürthöz kapcsolódó hálózati erőforrásokhoz. A "Microsoft.HDInsight" és a "Microsoft.Network" névterekben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre az Azure HDInsight-fürt hálózati konfigurációjának naplózásához vagy érvényesítéséhez.


Azure Blueprints is használhatja a nagy méretű Azure-központi telepítések a csomagolás kulcsfontosságú környezeti összetevők, például az Azure Resource Manager-sablonok, RBAC-vezérlők és szabályzatok, egyetlen tervezet definíciójában. Egyszerűen alkalmazhatja a tervezetet az új előfizetésekre és környezetekre, és finomíthatja a vezérlést és a felügyeletet a verziószámozással.


Az elérhető Azure-szabályzataliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Azure Blueprint létrehozása:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata a hálózati biztonsági csoport (NSG) és egyéb, az Azure HDInsight-fürthöz társított hálózati biztonsággal és forgalommal kapcsolatos erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezőben adhatja meg az üzleti igényt és/vagy az időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló/onnan érkező forgalmat.


Használja a címkézéssel kapcsolatos beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy minden erőforrás címkékkel jön létre, és értesítse a meglévő címkézetlen erőforrásokról.


Használhatja az Azure PowerShell vagy az Azure parancssori felület (CLI) a kikeres, vagy műveleteket hajt végre az erőforrások alapján a címkéket.


Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags


Hogyan hozzunk létre egy virtuális hálózat:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal


Hogyan hozzunk létre egy NSG egy biztonsági config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure HDInsight-telepítésekhez kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.


Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view


Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft időforrásokat tart fenn az Azure HDInsight fürtösszetevőkhöz, frissítheti a számítási központi telepítések időszinkronizálását.


Az Azure számítási erőforrásainak időszinkronizálásának konfigurálása:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure HDInsight-fürt az Azure Monitorra való bedeszkázásával összesítheti a fürt által létrehozott biztonsági adatokat. Az egyéni lekérdezések kihasználásával észlelheti és reagálhat a környezetben lévő fenyegetésekre. 


Azure HDInsight-fürt be- és feldolgozása az Azure Monitorra:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


Egyéni lekérdezések létrehozása Azure HDInsight-fürthöz:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze az Azure Monitort a HDInsight-fürthöz, irányítsa azt egy Log Analytics-munkaterületre. Ez naplózza a megfelelő fürtadatokat és az operációs rendszer metrikáit az összes Azure HDInsight fürtcsomóponthoz.


A HDInsight-fürt naplózásának engedélyezése:

 https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


HdInsight-naplók lekérdezése:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutató:** Az Azure HDInsight-fürt alaplapja az Azure Monitorhoz. Győződjön meg arról, hogy a Log Analytics-munkaterület használt a napló megőrzési időszak beállítása a szervezet megfelelőségi előírásoknak megfelelően.


Azure HDInsight-fürt be- és az Azure Monitorra való bedeszkázása:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


A Log Analytics-munkaterület-megőrzési időszak beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure HDInsight-fürt alaplapja az Azure Monitorhoz. Győződjön meg arról, hogy az Azure Log Analytics-munkaterület használt a napló megőrzési időszak beállítása a szervezet megfelelőségi előírásoknak megfelelően.


Azure HDInsight-fürt be- és az Azure Monitorra való bedeszkázása:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial


A Log Analytics-munkaterület-megőrzési időszak beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Azure Log Analytics-munkaterületi lekérdezések használatával lekérdezheti az Azure HDInsight-naplókat:


Egyéni lekérdezések létrehozása az Azure HDInsight-fürtökhöz:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-use-queries

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Az Azure Log Analytics-munkaterület használatával figyelheti és riasztást készíthet a biztonsági naplókban és az Azure HDInsight-fürthöz kapcsolódó eseményekben a rendellenes tevékenységekről.


Riasztások kezelése az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts


A naplóelemzési napló adatainak riasztása:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutató:** Az Azure HDInsight jön clamscan előre telepített és engedélyezve van a fürtcsomópont-lemezképek, azonban a szoftver kezelése kell, és manuálisan összesítése/figyelése naplók Clamscan termel.


Megérteni Clamscan:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutató**: Harmadik féltől származó megoldás megvalósítása a dns naplózáshoz.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató**: A konzolnaplózás manuális konfigurálása csomópontonként.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutató:** Az Azure HDInsight-fürt fürtkiépítés során létrehozott helyi felügyeleti fiók, valamint a létrehozott egyéb fiókok rekordjának karbantartása. Emellett ha az Azure AD-integráció t használja, az Azure AD beépített szerepkörök, amelyek et explicit módon hozzá kell rendelni, és ezért lekérdezhető. Az Azure AD PowerShell modul használatával adhoc lekérdezések felügyeleti csoportok tagjaiként ad ki ad hoc lekérdezéseket.


Emellett használhatja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.


Címtárszerepkör beszereznie az Azure AD-ben a PowerShell használatával:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0


Címtárszerepkör tagjainak beszereznie az Azure AD-ben a PowerShell segítségével:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0


Az identitás okának és a hozzáférésnek az Azure Security Centerrel való figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutató:** Fürt kiépítésekor az Azure-nak új jelszavakat kell létrehoznia a webportálhoz és a Biztonságos rendszerhéj (SSH) hozzáféréshez. Nincsenek módosítható alapértelmezett jelszavak, azonban különböző jelszavakat adhat meg az SSH és a webportál-hozzáféréshez.


Jelszavak beállítása Azure HDInsight-fürtök kiépítésekor:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató:** Az Azure HDInsight-fürt hitelesítésének integrálása az Azure Active Directoryval. Házirendeket és eljárásokat hozhat létre a dedikált felügyeleti fiókok használatával kapcsolatban.


Emellett használhatja az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.


Az Azure HDInsight-hitelesítés integrálása az Azure Active Directoryval:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds


Az identitás okának és a hozzáférésnek az Azure Security Centerrel való figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutató:** Az Azure HDInsight ID Broker használatával jelentkezzen be a vállalati biztonsági csomag (ESP) fürtök segítségével többtényezős hitelesítés, jelszavak megadása nélkül. Ha már bejelentkezett más Azure-szolgáltatásokba, például az Azure Portalra, egyetlen bejelentkezéssel (SSO) bejelentkezhet az Azure HDInsight-fürtbe.


Az Azure HDInsight ID Broker engedélyezése:

https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker#enable-hdinsight-id-broker

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure AD MFA-t, és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait. Az Enterprise Security Package konfigurált Azure HDInsight-fürtök csatlakoztathatók egy tartományhoz, így a tartományi felhasználók a tartományi hitelesítő adataikkal hitelesíthetik magukat a fürtökkel, és big data-feladatokat futtathatnak. Ha többtényezős hitelesítéssel (MFA) van engedélyezve, a felhasználók számára kihívást jelent egy második hitelesítési tényező biztosítása.


Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted


Az Azure Security Center identitásának és hozzáférésének figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Emelt szintű munkaállomások (privilegizált hozzáférési munkaállomások) használata többtényezős hitelesítéssel (MFA) konfigurálva az Azure HDInsight-fürtökbe és a kapcsolódó erőforrásokba való bejelentkezéshez és konfiguráláshoz.


Tudnivalók a kiemelt hozzáférésű munkaállomásokról:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations


Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató: Az**Azure HDInsight-fürtök a vállalati biztonsági csomag konfigurálva lehet csatlakoztatni egy tartományhoz, hogy a tartományi felhasználók használhatják a tartományi hitelesítő adatok hitelesítéséhez. Az Azure Active Directory (AAD) biztonsági jelentéseket használhat naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik az AAD környezetben. Az Azure Security Center használatával figyelheti az identitás- és hozzáférési tevékenységeket.


A kockázatos tevékenységre megjelölt AAD-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk


A felhasználók identitásának és hozzáférési tevékenységének figyelése az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató: Az**Azure HDInsight-fürtök a vállalati biztonsági csomag konfigurálva lehet csatlakoztatni egy tartományhoz, hogy a tartományi felhasználók használhatják a tartományi hitelesítő adatok hitelesítéséhez. A feltételes hozzáféréssel elnevezett helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezheti a hozzáférést.


Named-helyek konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directoryt (AAD) központi hitelesítési és engedélyezési rendszerként. Az AAD az adatokat erős titkosítással védi az inaktív és az átvitel során tárolt adatokhoz. Az AAD a felhasználói hitelesítő adatokat is megsózta, kiigazítja és biztonságosan tárolja.

Az Enterprise Security Package (ESP) konfigurált Azure HDInsight-fürtök csatlakoztathatók egy tartományhoz, így a tartományi felhasználók a tartományi hitelesítő adataikkal hitelesíthetik magukat a fürtökkel.


AAD-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant


A vállalati biztonsági csomag konfigurálása az Azure Active Directory tartományi szolgáltatásokkal az Azure HDInsightban:

https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Azure Active Directory (AAD) hitelesítés t az Azure HDInsight-fürthasználatával. Az AAD naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access-vélemények használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználó hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel. 


Az Azure Identity Access-vélemények használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Az Azure Active Directory (AAD) bejelentkezési és naplózási naplók segítségével figyelheti az inaktivált fiókok elérésére irányuló kísérleteket; ezek a naplók bármely külső siem/monitoring eszközbe integrálhatók.


Ezt a folyamatot egyszerűsítheti az AAD-felhasználói fiókok diagnosztikai beállításainak létrehozásával, a naplónaplók és a bejelentkezési naplók elküldésével az Azure Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat az Azure Log Analytics-munkaterületen belül.


Az Azure-tevékenységnaplók integrálása az Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Enterprise Security Package (ESP) konfigurált Azure HDInsight-fürtök csatlakoztathatók egy tartományhoz, így a tartományi felhasználók a tartományi hitelesítő adataikkal hitelesíthetik magukat a fürtökkel.  Az Azure Active Directory (AAD) kockázatészlelési és identitásvédelmi funkciójával konfigurálhatja a felhasználói identitásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Emellett további vizsgálat céljából adatokat is bevihet az Azure Sentinelbe.


Az AAD kockázatos bejelentkezések megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins


Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató**: Nem áll rendelkezésre; Az Ügyfélszéf még nem támogatott az Azure HDInsight számára.

Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Az Azure HDInsight-telepítésekhez kapcsolódó erőforrások címkéivel segítséget nyújthat a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követésében.


Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. Az Azure HDInsight-fürtöket és a kapcsolódó tárfiókokat virtuális hálózatnak/alhálózatnak kell elválasztani, megfelelően címkézni kell, és egy hálózati biztonsági csoporton (NSG) vagy az Azure Firewall-en belül kell biztosítani. Fürtadatokat kell tárolni egy biztonságos Azure Storage-fiók vagy az Azure Data Lake Storage (Gen1 vagy Gen2).


Válassza ki az Azure HDInsight-fürt tárolási beállításait:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


Az Azure Data Lake Storage védelme:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Az Azure Storage-fiókok védelme:

https://docs.microsoft.com/azure/storage/common/storage-security-guide

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** A bizalmas adatokat tároló vagy feldolgozó Azure HDInsight-fürtök esetében jelölje meg a fürtöt és a kapcsolódó erőforrásokat címkék használatával bizalmasként. A kiszivárgás általi adatvesztés kockázatának csökkentése érdekében korlátozza az Azure HDInsight-fürtök kimenő hálózati forgalmát az Azure Firewall használatával.


A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.


Az Azure HDInsight-fürtök kimenő forgalmának korlátozása az Azure Tűzfallal:

https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic


Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató:** Az összes bizalmas információ titkosítása az átvitel során. Győződjön meg arról, hogy az Azure HDInsight-fürthöz vagy fürtadattáraihoz (Azure Storage-fiókok vagy Azure Data Lake Storage Gen2/Gen2) csatlakozó ügyfelek képesek-e a TLS 1.2-es vagy nagyobb szintű egyeztetésére. A Microsoft Azure-erőforrások alapértelmezés szerint egyeztetik a TLS 1.2-t. 


Ismerje meg az Azure Data Lake Storage titkosítását az átvitel során:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-security-overview


Az Azure Storage-fiók titkosításának ismertetése átvitel közben:

https://docs.microsoft.com/azure/storage/blobs/security-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az adatok azonosításával, besorolásával és a veszteségmegelőzési funkciókmég nem érhetők el az Azure Storage- vagy számítási erőforrásokhoz. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.



A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.



Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure HDInsight Enterprise Security Package (ESP) segítségével az Apache Ranger segítségével részletes hozzáférés-vezérlési és adathiba-kezelési szabályzatokat hozhat létre és kezelhet a fájlokban, mappákban, adatbázisokban, táblázatokban és sorokban/oszlopokban tárolt adatokhoz. A hadoop-rendszergazda konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC) az Apache Hive, a HBase, a Kafka és a Spark biztonságossá tétele érdekében az Apache Ranger-ben található bővítmények használatával.

Az RBAC-házirendek konfigurálása az Apache Rangerrel lehetővé teszi, hogy engedélyeket társítson a szervezetben lévő szerepkörhöz. Ez az absztrakciós réteg megkönnyíti annak biztosítását, hogy a felhasználók csak a munkahelyi feladataik elvégzéséhez szükséges engedélyekkel rendelkezzenek.

Vállalati biztonsági csomag konfigurációi az Azure Active Directory tartományi szolgáltatásokkal a HDInsightban:https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds

Az Azure HDInsight vállalati biztonságának áttekintése:https://docs.microsoft.com/azure/hdinsight/domain-joined/hdinsight-security-overview



**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató:** A bizalmas adatokat tároló vagy feldolgozó Azure HDInsight-fürtök esetében jelölje meg a fürtöt és a kapcsolódó erőforrásokat címkék használatával bizalmasként. Az Azure Storage- és számítási erőforrásokhoz még nem érhetők el adatazonosítási, besorolás- és veszteségmegelőzési funkciók. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.


A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.


Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Ha az Azure SQL Database használatával tárolja az Apache Hive és az Apache Oozie metaadatait, győződjön meg arról, hogy az SQL-adatok mindig titkosítva maradnak. Az Azure Storage-fiókok és a Data Lake Storage (Gen1 vagy Gen2) esetén ajánlott engedélyezni a Microsoft számára a titkosítási kulcsok kezelését, azonban lehetősége van a saját kulcsok kezelésére.



Az Azure Storage-fiókok titkosítási kulcsainak kezelése:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal



Az Azure Data Lake Storage létrehozása az ügyfelek által felügyelt titkosítási kulcsok használatával:

https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal



Az Azure SQL Database titkosításának ismertetése:

https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview#data-encryption



Az Átlátszó adattitkosítás konfigurálása az SQL Database-hez az ügyfél által kezelt kulcsok használatával:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Konfigurálja az Azure HDInsight-fürtökhöz társított Azure Storage-fiókok diagnosztikai beállításait az összes CRUD-művelet figyeléséhez és naplózásához fürtadatokkal. Engedélyezze a naplózást az Azure HDInsight-fürthöz társított storage-fiókok vagy Data Lake-áruházak számára.


További naplózás/naplózás engedélyezése egy Azure Storage-fiókhoz:

https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account


További naplózás/naplózás engedélyezése az Azure Data Lake Storage-ban:

https://docs.microsoft.com/azure/data-lake-analytics/data-lake-analytics-diagnostic-logs

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató:** Harmadik fél től származó biztonsági réskezelési megoldás megvalósítása.


Ha Rapid7, Qualys vagy bármely más biztonsági réskezelési platform-előfizetéssel rendelkezik, parancsfájlműveletekkel telepítheti a biztonsági résértékelési ügynököket az Azure HDInsight fürtcsomópontjain, és kezelheti a csomópontokat az adott portálon keresztül.


A Rapid7 agent manuális telepítése:

https://insightvm.help.rapid7.com/docs/azure-security-center


A Qualys Agent manuális telepítése:

https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf


Parancsfájlműveletek használata:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutató:** A fürtcsomópont-lemezképek hez engedélyezve van az automatikus rendszerfrissítés, azonban a frissítések alkalmazásának biztosításához rendszeresen újra kell indítani a fürtcsomópontokat.


A Microsoft az alap Azure HDInsight-csomópontrendszerképek karbantartásához és frissítéséhez.


A HDInsight-fürtök operációsrendszer-javítási ütemezésének konfigurálása:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutató:** Parancsfájl-műveletek vagy más mechanizmusok segítségével az Azure HDInsight-fürtök javításához. Az újonnan létrehozott fürtök mindig a legújabb elérhető frissítésekkel rendelkeznek, beleértve a legújabb biztonsági javításokat is.


Linux alapú Azure HDInsight-fürtök operációsrendszer-javítási ütemezésének konfigurálása:

https://docs.microsoft.com/azure/hdinsight/hdinsight-os-patching


Parancsfájlműveletek használata:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutató:** Valósítson meg egy külső biztonsági rést kezelő megoldást, amely képes összehasonlítani a biztonsági résvizsgálatait az idő múlásával. Ha Rapid7 vagy Qualys-előfizetéssel rendelkezik, az adott szállítóportálon megtekintheti és összehasonlíthatja a biztonsági réseket.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutató:** Használjon közös kockázatpontozási programot (pl. közös biztonsági réspontozási rendszert) vagy a külső ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutató:** Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.), beleértve az Azure HDInsight-fürtöket is, az előfizetés(ek)en belül).  Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.


Bár a klasszikus Azure-erőforrások fedezhetők fel az Azure Resource Graph,erősen ajánlott létrehozni és használni az Azure Resource Manager erőforrások at megy előre.


Lekérdezések létrehozása az Azure Resource Graph segítségével:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


Az Azure-előfizetések megtekintése:

https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0


Az Azure RBAC megismerése:

https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

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


Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

**Útmutató:** A jóváhagyott Azure-erőforrások és a számítási erőforrások jóváhagyott szoftvereinek listájának meghatározása

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával erőforrásokat kérdezhet le/fedezhet fel az előfizetése(i)n belül. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóváhagyásra kerül.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató:** Harmadik féltől származó megoldás megvalósítása a nem jóváhagyott szoftveralkalmazások fürtcsomópontjainak figyelésére.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató:** Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.), beleértve az Azure HDInsight-fürtöket is, az előfizetés(ek)en belül).  Távolítsa el a felderített, nem jóváhagyott Azure-erőforrásokat. Az Azure HDInsight fürtcsomópontok, valósítson meg egy harmadik féltől származó megoldást, hogy távolítsa el vagy riasztást nem jóváhagyott szoftverek.


Lekérdezések létrehozása az Azure Resource Graph segítségével:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutató:** Az Azure HDInsight fürtcsomópontok hoz egy külső megoldás, hogy megakadályozzák a jogosulatlan szoftverek végrehajtását.

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

**Útmutató:** Az Azure HDInsight fürtcsomópontok hoz egy külső megoldás, hogy megakadályozzák a jogosulatlan fájltípusok végrehajtását.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resources Managerrel

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával.


A feltételes hozzáférés beállítása az Azure Resource Manager elérésének letiltásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; Ez nem vonatkozik az Azure HDInsight, a felhasználók (nem rendszergazdák) a fürt nem kell hozzáférni az egyes csomópontok feladatok futtatásához. A fürtrendszergazdának gyökérhozzáférése van az összes fürtcsomóponthoz.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

**Útmutatás**: Nem alkalmazható; a benchmark az Azure Apps Service vagy a webalkalmazásokat tároló számítási erőforrások számára készült.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** A "Microsoft.HDInsight" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a HDInsight-fürt hálózati konfigurációjának naplózásához vagy érvényesítéséhez.


Az elérhető Azure-szabályzataliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató:** A Microsoft által felügyelt és karbantartott Azure HDInsight operációsrendszer-rendszerképek. A fürtcsomópontok operációs rendszeréhez biztonságos konfigurációk megvalósításáért felelős ügyfél. 

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] az Azure HDInsight-fürtök és a kapcsolódó erőforrások biztonságos beállításainak kényszerítéséhez.


Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


Az Azure-szabályzat hatásainak megismerése:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutató:** A Microsoft által felügyelt és karbantartott Azure HDInsight operációsrendszer-rendszerképek. Az operációsrendszer-szintű állapotkonfiguráció megvalósításáért felelős ügyfél.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ, használja az Azure DevOps vagy az Azure Repos biztonságos anamminda a kódot.



Kód tárolása az Azure DevOps-ban:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops



Azure Repos dokumentáció:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; az Azure HDInsight-ra nem alkalmazható egyéni lemezképek.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** A "Microsoft.HDInsight" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.



Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutató:** Harmadik féltől származó megoldás megvalósítása a fürtcsomópont operációs rendszerei kívánt állapotának fenntartásához.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** A "Microsoft.HDInsight" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a HDInsight-fürt konfigurációjának naplózásához vagy érvényesítéséhez.


Az elérhető Azure-szabályzataliasok megtekintése:

https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0


Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutató:** Harmadik féltől származó megoldás megvalósítása a fürtcsomópont operációs rendszerei állapotának figyelésére.

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató: Az**Azure HDInsight tartalmazza a Saját kulcs (BYOK) támogatását az Apache Kafka számára. Ez a funkció lehetővé teszi az inaktív adatok titkosításához használt kulcsok birtoklását és kezelését.


Az Azure HDInsight összes felügyelt lemeze az Azure Storage Service Encryption (SSE) szolgáltatással védett. Alapértelmezés szerint a lemezeken lévő adatok microsoftáltal kezelt kulccsal vannak titkosítva. Ha engedélyezi a BYOK-ot, megadja az Azure HDInsight titkosítási kulcsát az Azure Key Vault használatával való használatra és kezelésére.


A Key Vault az Azure HDInsight-telepítésekkel is használható a fürtszintű tárolás (Azure Storage-fiókok és Azure Data Lake Storage) kulcsainak kezeléséhez.


Saját kulcs létrehozása az Apache Kafka számára az Azure HDInsightban:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


Az Azure Storage-fiókok titkosítási kulcsainak kezelése:

https://docs.microsoft.com/azure/storage/common/storage-encryption-keys-portal

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutatás: Felügyelt**identitások az Azure HDInsight ban is használhatók, amelyek lehetővé teszik a fürtök számára az Azure Active Directory tartományi szolgáltatások elérését, az Azure Key Vault elérését vagy az Azure Data Lake Storage Gen2 fájljainak elérését.


A felügyelt identitások megismerése az Azure HDInsight segítségével:

https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

**Útmutató:** Ha az Azure HDInsight-telepítéshez kapcsolódó kódot használ, implementálhatja a Hitelesítő adatok olvasóját a kódon belüli hitelesítő adatok azonosítására. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault. 


A Hitelesítő adatok képolvasójának beállítása:

https://secdevtools.azurewebsites.net/helpcredscan.html

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag kezelt kártevőirtó szoftverek használata

**Útmutató:** Az Azure HDInsight jön clamscan előre telepített és engedélyezve van a fürtcsomópont-lemezképek, azonban a szoftver kezelése kell, és manuálisan összesítése/figyelése naplók Clamscan termel.


Ismerje meg az Azure HDInsight Clamscan szolgáltatását:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft Antimalware engedélyezve van az alapul szolgáló gazdagépen, amely támogatja az Azure-szolgáltatásokat, de nem fut az ügyfél tartalom.


Az Azure HDInsight-fürt telepítésével kapcsolatos Azure-erőforrásokba feltöltött fájlok előzetes vizsgálata, például a Data Lake Storage, a Blob Storage stb. A Microsoft ezekben az esetekben nem fér hozzá az ügyféladatokhoz.


Ismerje meg a Microsoft kártevőirtó szoftvereit az Azure Felhőszolgáltatásokhoz és a virtuális gépekhez:

 https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás:** Az Azure HDInsight a Clamscan előre telepített és a fürtcsomópont-lemezképekhez engedélyezett. Clamscan elvégzi a motor és a definíció frissítések automatikusan, azonban összesítése és kezelése naplók kell végezni kézzel.


Ismerje meg az Azure Azure HDInsight clamscan szolgáltatását:

https://docs.microsoft.com/azure/hdinsight/hdinsight-faq#security-and-certificates

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** Ha egy Azure Storage-fiók a HDInsight fürt adattár, válassza ki a megfelelő redundancia-beállítás (LRS, ZRS, GRS, RA-GRS).  Ha egy Azure SQL-adatbázist használ az Azure HDInsight fürtadattárhoz, konfigurálja az aktív georeplikációt.


A tárolási redundancia beállítása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


A redundancia beállítása az Azure SQL-adatbázisokhoz:

https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutató:** Ha egy Azure Storage-fiók az Azure HDInsight fürt adattár, válassza ki a megfelelő redundancia-beállítás (LRS, ZRS, GRS, RA-GRS). Ha az Azure Key Vault az Azure HDInsight-telepítés bármely részéhez használja, győződjön meg arról, hogy a kulcsokról biztonsági mentés készül.


Válassza ki az Azure HDInsight-fürt tárolási beállításait:

https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options


A tárolási redundancia beállítása az Azure Storage-fiókokhoz:

https://docs.microsoft.com/azure/storage/common/storage-redundancy


Kulcstároló-kulcsok biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Ha az Azure Key Vault az Azure HDInsight üzembe helyezésével van használva, tesztelje a biztonsági másolatot az ügyfél által felügyelt kulcsok visszaállítását.


Saját kulcs létrehozása az Apache Kafka számára az Azure HDInsightban:

https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-byok


A kulcstároló kulcsainak visszaállítása az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Ha az Azure Key Vault az Azure HDInsight-telepítéssel együtt használatos, engedélyezze a Kulcstárolóban a kulcsok véletlen vagy rosszindulatú törlése elleni védelmet.


A soft-delete engedélyezése az Azure Key Vaultban:

https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének/kezelésének fázisait.



Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás: A**Security Center súlyossági szintet rendel a riasztásokhoz, hogy segítsen rangsorolni az egyes riasztások hozadékát, így ha egy erőforrás biztonsága veszélybe kerül, azonnal hozzáérhet. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet. Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az Ön adataihoz jogosulatlan vagy jogosulatlan fél fért hozzá.



Az Azure Security Center biztonsági kapcsolattartójának beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center riasztások és javaslatok a folyamatos exportálás funkció használatával. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötő a Sentinel riasztások streameléséhez.



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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelést végez az Azure-erőforrásokon, és 60 napon belül biztosítja az összes kritikus biztonsági megállapítás helyreállítását

**Útmutató:** Kérjük, kövesse a Microsoft kötelezettségvállalási szabályzatát, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.



A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkat itt talál:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
