---
title: Azure biztonsági alapkonfiguráció az Azure SQL-adatbázishoz
description: Azure biztonsági alapkonfiguráció az Azure SQL-adatbázishoz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 8c0e7c6dfb1275e1fafbab1dd9e89cb2fe4376ad
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759150"
---
# <a name="azure-security-baseline-for-azure-sql-database"></a>Azure biztonsági alapkonfiguráció az Azure SQL-adatbázishoz

Az Azure Security Baseline for Azure SQL Database olyan javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Engedélyezheti az Azure Private Link számára az Azure PaaS-szolgáltatások (például az SQL-adatbázis) és az Azure által üzemeltetett ügyfél-/partnerszolgáltatások elérését a virtuális hálózat privát végpontján keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. 

Ahhoz, hogy a forgalom elérje az Azure SQL Database-t, használja az SQL szolgáltatás címkék et a hálózati biztonsági csoportokon keresztül érkező kimenő forgalom engedélyezéséhez.

A virtuális hálózati szabályok lehetővé teszik, hogy az Azure SQL Database csak a virtuális hálózaton belül kiválasztott alhálózatokról küldött kommunikációt fogadja el.

Privát kapcsolat beállítása az Azure SQL Database-hez:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

A virtuális hálózati szolgáltatás végpontjainak és szabályainak használata adatbázis-kiszolgálókhoz:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és orvosolja a hálózatvédelmi javaslatokat az Azure SQL Database Server üzembe helyezett alhálózatához. 

Az Azure SQL Database Server-példányhoz csatlakozó Azure virtuális gépek (VM) esetében engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplóit a virtuális gépeket védő NSG-k számára, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózásához. 

NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Az Azure Security Center által biztosított hálózati biztonság megismerése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

Az Azure Security Center által biztosított hálózati biztonság megismerése:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure Apps Service vagy a webalkalmazásokat tároló számítási erőforrások számára készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Engedélyezze a DDoS-védelmi szabványt az SQL Server-példányokkal társított virtuális hálózatokon az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A DDoS-védelem konfigurálása:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:

https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Az Azure virtuális gépek (VM-ek), amelyek csatlakoznak az Azure SQL Database-példány, engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplók az NSG-k a virtuális gépek védelme, és naplókat küld egy Azure Storage-fiók forgalmi naplózás. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze a Network Watcher csomagrögzítését.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Network Watcher engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Engedélyezze a komplex veszélyforrások elleni védelmet (ATP) az Azure SQL Database számára.  A felhasználók riasztást kapnak a gyanús adatbázis-tevékenységekről, a potenciális biztonsági résekről és az SQL-injektálási támadásokról, valamint a rendellenes adatbázis-hozzáférési és lekérdezési mintákról. A komplex veszélyforrások elleni védelem az Azure Security Centerrel is integrálja a riasztásokat. 

Az Azure SQL Database speciális veszélyforrások elleni védelmének ismertetése és használata:https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure Apps Service vagy a webalkalmazásokat tároló számítási erőforrások számára készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A virtuális hálózati szolgáltatás címkék segítségével határozza meg a hálózati hozzáférés-vezérlés a hálózati biztonsági csoportok vagy az Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

Ha szolgáltatásvégpontokat használ az Azure SQL Database-hez, az Azure SQL Database nyilvános IP-címei befelé kell tartani: a kapcsolat engedélyezéséhez meg kell nyitni a hálózati biztonsági csoportokat (NSG-ket) az Azure SQL Database IP-k között. Ezt az Azure SQL Database NSG szolgáltatáscímkéinek használatával teheti meg.

A szolgáltatáscímkék ismertetése az Azure SQL Database szolgáltatásvégpontjaival:

https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations

A szolgáltatáscímkék ismertetése és használata:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Az Azure SQL Database-kiszolgálópéldányok hálózati biztonsági konfigurációinak definiálása és megvalósítása az Azure Policy használatával. Használhatja a "Microsoft.Sql" névtér egyéni házirend-definíciók definiálására, vagy használhatja az Azure SQL Database Server hálózati védelemhez tervezett beépített házirend-definíciók bármelyikét. Egy példa az Azure SQL Database-kiszolgáló megfelelő beépített hálózati biztonsági házirendje: "Az SQL Server virtuális hálózati szolgáltatásvégpontot kell használnia".

 

Azure Blueprints használatával egyszerűsítheti a nagy méretű Azure-központi telepítések csomagolásával kulcsfontosságú környezeti összetevők, például az Azure Resource Management sablonok, szerepköralapú hozzáférés-vezérlés (RBAC) és a szabályzatok, egyetlen tervezet definíciójában. Egyszerűen alkalmazhatja a tervezetet az új előfizetésekre és környezetekre, és finomíthatja a vezérlést és a felügyeletet a verziószámozással.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Címkék használata hálózati biztonsági csoportok (NSG) és egyéb, a hálózati biztonsághoz és a forgalomhoz kapcsolódó erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezőben adhatja meg az üzleti igényt és/vagy az időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló/onnan érkező forgalmat.

Használja a címkézéssel kapcsolatos beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy minden erőforrás címkékkel jön létre, és értesítheti a meglévő címkézetlen erőforrásokról.

Használhatja az Azure PowerShell vagy az Azure CLI a look-up, vagy műveleteket hajt végre az erőforrások alapján a címkéket.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure Activity Log segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure SQL Database-kiszolgálópéldányaihoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása az Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft időforrásokat tart fenn az Azure-erőforrásokhoz. Frissítheti az időszinkronizálást a számítási központi telepítésekhez.

Az Azure számítási erőforrásainak időszinkronizálásának konfigurálása:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Engedélyezze az Azure SQL Database naplózását az adatbázis-események nyomon követéséhez, és naplóba írást az Azure Storage-fiókban, a Log Analytics-munkaterületen vagy az Event Hubs-ban.

Emellett az Azure SQL diagnosztikai telemetriáját streamelheti az Azure SQL Analytics felhőalapú megoldásába, amely az Azure SQL-adatbázisok, rugalmas készletek és felügyelt példányok teljesítményét felügyeli, nagy méretekben és több előfizetésben. Segíthet az Azure SQL Database teljesítménymutatóinak összegyűjtésében és megjelenítésében, és beépített intelligenciával rendelkezik a teljesítményhibaelhárításhoz.

Az Azure SQL-adatbázis naplózásának beállítása:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

Platformnaplók és metrikák gyűjtése az Azure Monitor segítségével:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging

A diagnosztika streamelése az Azure SQL Analytics szolgáltatásba:

https://docs.microsoft.com/azure/sql-database/sql-database-metrics-diag-logging#stream-into-azure-sql-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze a naplózást az Azure SQL Database-kiszolgálópéldányán, és válasszon tárolási helyet a naplónaplókhoz (Azure Storage, Log Analytics vagy Event Hub).

Az Azure SQL Server naplózásának engedélyezése:

https://docs.microsoft.com/azure/sql-database/sql-database-auditing

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a referenciaérték számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Ha az Azure SQL Database-naplókat egy Log Analytics-munkaterületen tárolja, állítsa be a naplómegőrzési időszakot a szervezet megfelelőségi előírásainak megfelelően.

A naplómegőrzési paraméterek beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Elemezze és figyelje a naplókat a rendellenes viselkedések hez, és rendszeresen tekintse át az eredményeket. Az Azure Security Center komplex veszélyforrások elleni védelem használatával riasztást az Azure SQL Database-példányhoz kapcsolódó szokatlan tevékenységekről. Másik lehetőségként konfigurálja a riasztásokat metrikaértékek vagy az Azure-beli SQL Database-példányokhoz kapcsolódó Azure-tevékenységnapló-bejegyzések alapján.

A speciális veszélyforrások elleni védelem és az Azure SQL Server riasztása:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Egyéni riasztások konfigurálása az Azure SQL Database-hez:

https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Használja az Azure Security Center komplex veszélyforrások elleni védelem az Azure SQL-adatbázisok figyelése és riasztása a rendellenes tevékenység. Engedélyezze a speciális adatbiztonságot az SQL-adatbázisokszámára. A Speciális adatbiztonság magában foglalja a bizalmas adatok felderítését és besorolását, az adatbázis esetleges biztonsági rései felderítését és mérséklését, valamint az adatbázist fenyegető rendellenes tevékenységek észlelését.

Ismerje meg a komplex veszélyforrások elleni védelmet és az Azure SQL-adatbázis riasztását:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview

Az Azure SQL Database speciális adatbiztonságának engedélyezése:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Riasztások kezelése az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutatás**: Nem alkalmazható; Az Azure SQL Server esetében a kártevőirtó megoldást a Microsoft kezeli az alapul szolgáló platformon.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutatás**: Nem alkalmazható; A DNS-naplózás nem alkalmazható az Azure SQL Server kiszolgálóra.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutatás**: Nem alkalmazható; A parancssori naplózás nem alkalmazható az Azure SQL Server szolgáltatásra.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutatás:** Az Azure Active Directory (AAD) beépített szerepkörök, amelyek explicit módon hozzá kell rendelni, és lekérdezhető. Az AAD PowerShell modul segítségével ad-hoc lekérdezések végrehajtásával felügyeleti csoportok tagjai fiókok felderítése.

Címtárszerepkör beszereznie az Azure AD-ben a PowerShell használatával:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárszerepkör tagjainak beszereznie az Azure AD-ben a PowerShell segítségével:

https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutató:** Az Azure Active Directory nem rendelkezik az alapértelmezett jelszavak fogalmát. Azure SQL Database-példány kiépítésekor ajánlott úgy dönt, hogy integrálja a hitelesítést az Azure Active Directoryval.

Az Azure Active Directory-hitelesítés konfigurálása és kezelése az Azure SQL-rel:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató**: Házirendek és eljárások létrehozása a dedikált felügyeleti fiókok használatával kapcsolatban. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát.

Ismerje meg az Azure Security Center identitását és hozzáférését:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutatás**: Nem alkalmazható; míg konfigurálhatja az Azure Active Directory-hitelesítés integrálható az Azure SQL Server, egyszeri bejelentkezés nem támogatott.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory (AAD) többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Használjon emelt szintű hozzáférésű munkaállomás (PAW) a többtényezős hitelesítés mfa konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfigurálásához.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Az Azure Active Directory biztonsági jelentések használata a naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Az Azure SQL Database komplex veszélyforrások elleni védelem használatával észlelheti az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.

Kockázatos tevékenységesetén megjelölt Azure AD-felhasználók azonosítása:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk

A felhasználók identitásának és hozzáférési tevékenységének figyelése az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-identity-access

Tekintse át a komplex veszélyforrások elleni védelmet és a lehetséges riasztásokat:

https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáféréssel ellátott helyek használatával csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból engedélyezheti a portál- és Azure-erőforrás-kezelési hozzáférést.

Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Hozzon létre egy Azure Active Directory (AAD) rendszergazda az Azure SQL Database-kiszolgáló példányaihoz.

Az Azure Active Directory-hitelesítés konfigurálása és kezelése az Azure SQL-rel:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

AAD-példány létrehozása és konfigurálása:

https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory (AAD) naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity access-felülvizsgálatok használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználók hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő felhasználók rendelkezhessenek folyamatos hozzáféréssel.

Az Azure Identity Access-vélemények használata:

https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Konfigurálja az Azure Active Directory (AAD) hitelesítést az Azure SQL-rel, és hozzon létre diagnosztikai beállításokat az Azure Active Directory felhasználói fiókokhoz, elküldve a naplónaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat a Log Analytics-munkaterületen belül.

Az Azure Active Directory-hitelesítés konfigurálása és kezelése az Azure SQL-rel:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Azure Active Directory (AAD) identitásvédelem és a kockázatészlelések segítségével konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús gyanús műveletekre. Emellett további vizsgálat céljából adatokat is bevihet az Azure Sentinelbe.

Az Azure AD kockázati bejelentkezések megtekintése:

https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:

https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató:** Olyan támogatási forgatókönyvekben, ahol a Microsoftnak hozzá kell férnie az ügyféladatokhoz, az Azure Customer Lockbox felületet biztosít az ügyfelek számára az ügyféladatok-hozzáférési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához.

Az ügyfélszéf ismertetése:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview

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

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. Az erőforrásokat vnet/alhálózat nak kell elválasztani, megfelelően címkézni és egy NSG-n vagy Az Azure tűzfalon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrásokat el kell különíteni. Privát hivatkozás használata; az Azure SQL Server telepítése a virtuális hálózaton belül, és privát végpontok használatával privát csatlakozás.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Privát kapcsolat beállítása az Azure SQL Database-hez:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** A bizalmas adatokat tároló vagy feldolgozó Azure SQL-adatbázisok esetében jelölje meg az adatbázist és a kapcsolódó erőforrásokat címkék használatával bizalmasként. Konfigurálja a privát kapcsolatot a Network Security Group service tags együttesen az Azure SQL Database-példányokon a bizalmas adatok kiszivárgásának megakadályozása érdekében.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

A Privát kapcsolat és az NSG-k konfigurálása az Azure SQL Database-példányok adatainak kiszivárgásának megakadályozására:

https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview

Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató: Az**Azure SQL Database a Transport Layer Security segítségével mozgásban lévő adatok titkosításával biztosítja az adatokat. Az SQL Server minden kapcsolatesetében mindig kényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az ügyfél és a kiszolgáló közötti összes adat "átvitel közben" titkosítva legyen, függetlenül a ttól, hogy a kapcsolati karakterláncban a Titkosítás vagy a TrustServerCertificate van-e.

Ismerje meg az Azure SQL-titkosítást átvitel közben:

https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Használja az Azure SQL Database-adatok felderítési és besorolási szolgáltatás. Az adatok felderítése és besorolása az Azure SQL Database-be &amp; épített speciális képességeket biztosít az adatbázisokban lévő bizalmas adatok felderítéséhez, besorolásához és címkézéséhez.

Az Azure SQL Server adatfelderítésének és besorolásának használata:

https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure Active Directory (AAD) használatával hitelesíti és szabályaszabályolása az Azure SQL Database-példányok.

Az Azure SQL Server integrálása az Azure Active Directoryval a hitelesítéshez:

https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication

A hozzáférés szabályozása az Azure SQL Server ben:

https://docs.microsoft.com/azure/sql-database/sql-database-control-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató: A**Microsoft kezeli az Azure SQL Database alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Az átlátszó adattitkosítás (TDE) segít megvédeni az Azure SQL Database-t, az Azure SQL felügyelt példányt és az Azure Data Warehouse-t a rosszindulatú offline tevékenységek fenyegetésével szemben az inaktív adatok titkosításával. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Alapértelmezés szerint a TDE az összes újonnan üzembe helyezett Azure SQL-adatbázis esetében engedélyezve van. A TDE titkosítási kulcsot a Microsoft vagy az ügyfél is kezelheti.

Az átlátható adattitkosítás kezelése és a saját titkosítási kulcsok használata:

https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql?tabs=azure-portal#manage-transparent-data-encryption

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Monitor és az Azure-tevékenységnapló használatával riasztásokat hozhat létre az Azure SQL-adatbázisok és más kritikus vagy kapcsolódó erőforrások éles példányainak módosításaihoz.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató:** Engedélyezze a speciális adatbiztonságot az Azure SQL Database számára, és kövesse az Azure Security Center ajánlásait az Azure SQL-kiszolgálókon végzett biztonsági résértékelésekről.

A biztonsági rések felmérése az Azure SQL-adatbázisokban:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment

A speciális adatbiztonság engedélyezése:

https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security

Az Azure Security Center biztonsági résértékelési javaslatainak megvalósítása:

https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutatás**: Nem alkalmazható; ez a referenciaérték számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutató:** Rendszeres ismétlődő vizsgálatok engedélyezése az Azure SQL Database-példányok; ez úgy állítja be a biztonsági rés felmérését, hogy hetente egyszer automatikusan futtasson egy vizsgálaton az adatbázison. A vizsgálat eredményének összegzését az Ön által megadott e-mail címre küldjük. Hasonlítsa össze az eredményeket a biztonsági rések kiújításának ellenőrzéséhez.

Biztonsági résértékelési jelentés exportálása az Azure Security Centerben:

https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment#implementing-vulnerability-assessment

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutatás:** Használja az Azure Security Center által biztosított alapértelmezett kockázati minősítéseket (Secure Score).

Ismerje meg az Azure Security Center biztonságos pontszámát:

https://docs.microsoft.com/azure/security-center/security-center-secure-score

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás: Az**Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure SQL Server-példányokat is) az előfizetése(i)n belül.  Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.

Bár a klasszikus Azure-erőforrások fedezhetők fel a Resource Graph, erősen ajánlott az Azure Resource Manager erőforrásainak létrehozása és használata a jövőben.

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:https://docs.microsoft.com/azure/role-based-access-control/overview


**Az Azure Security Center figyelése:** Nem alkalmazható

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

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával erőforrásokat kérdezhet le/fedezhet fel az előfizetése(i)n belül. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóváhagyásra kerül.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: A nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetésben (előfizetéseiben) létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával erőforrásokat kérdezhet le/fedezhet fel az előfizetése(i)n belül. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóváhagyásra kerül.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása az Azure-szabályzattal:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokon futó alkalmazásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resources Managerrel

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával.

A feltételes hozzáférés beállítása az Azure Resource Manager elérésének letiltásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management


**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az App Service-hez vagy az asztali vagy webes alkalmazásokat tároló számítási erőforrásokhoz készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Az Azure Policy vagy az Azure Security Center azure-beli SQL-kiszolgálókra/adatbázisokra vonatkozó javaslatait az összes Azure-erőforrás biztonsági konfigurációjának karbantartásához használja.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutatás**: Nem alkalmazható; ezt az ajánlást számítási erőforrásokra szánják.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] biztonságos beállítások kényszerítése az Azure-erőforrások között.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-szabályzat hatásainak megismerése:

https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ, használja az Azure DevOps vagy az Azure Repos biztonságos anamminda a kódot.

Kód tárolása az Azure DevOps-ban:

https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:

https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** A "Microsoft.SQL" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** Használja ki az Azure Security Center t alapszintű vizsgálatok at az Azure SQL-kiszolgálók és adatbázisok.

Javaslatok kijavítása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure Key Vault használatával titkosítási kulcsokat tárolaz Azure SQL Database transzparens adattitkosítás (TDE) tárolására.

Az Azure SQL Serverben tárolt bizalmas adatok védelme és a titkosítási kulcsok tárolása az Azure Key Vaultban:

https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Felügyelt identitások használatával automatikusan felügyelt identitást biztosítaz Azure-szolgáltatások nak az Azure Active Directoryban (AAD). Felügyelt identitások lehetővé teszi, hogy hitelesítse minden olyan szolgáltatás, amely támogatja az AAD-hitelesítés, beleértve az Azure Key Vault, anélkül, hogy a kód hitelesítő adatokat.

Oktatóanyag: Az Azure SQL eléréséhez használjon Windows virtuális géphez rendelt felügyelt identitást:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql

Felügyelt identitások konfigurálása:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

**Útmutató:** A hitelesítő adatok at a kódon belüli hitelesítő adatok azonosítására valósíthatja meg. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault. 

A Hitelesítő adatok képolvasójának beállítása:https://secdevtools.azurewebsites.net/helpcredscan.html

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag kezelt kártevőirtó szoftverek használata

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál. A Microsoft kezeli a kártevőirtót az alapul szolgáló platformhoz.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató alapjául szolgáló gazdagépen (például az Azure App Service), azonban nem fut az ügyféltartalomon.

A nem számítási Azure-erőforrásokba feltöltött tartalmak, például az App Service, a Data Lake Storage, a Blob Storage, az Azure SQL Server stb. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

Ismerje meg a Microsoft kártevőirtó szoftvereit az Azure Felhőszolgáltatásokhoz és a virtuális gépekhez:https://docs.microsoft.com/azure/security/fundamentals/antimalware

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál. A Microsoft kezeli a kártevőirtót az alapul szolgáló platformhoz.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** Az adatok elvesztésétől való védelme érdekében az Azure SQL Database hetente automatikusan létrehozza a teljes adatbázis-biztonsági mentéseket, a különbözeti adatbázis biztonsági mentéseit 12 óránként, és a tranzakciós napló biztonsági mentéseit 5–10 percenként. A biztonsági mentések tárolják RA-GRS tároló legalább 7 napig az összes szolgáltatási szint. Az alapszintű támogatási konfigurálható biztonsági mentési megőrzési időszak kivételével minden szolgáltatási szint, akár 35 napig is használható.

A különböző megfelelőségi követelmények teljesítéséhez különböző megőrzési időszakokat választhat ki a heti, havi és/vagy éves biztonsági mentéshez. A tárolási felhasználás a biztonsági mentések kiválasztott gyakoriságától és a megőrzési időszak(ok)tól függ.

Ismerje meg a biztonsági mentéseket és az üzletmenet folytonosságát az Azure SQL Server rel:

https://docs.microsoft.com/azure/sql-database/sql-database-business-continuity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutatás: Az**Azure SQL Database automatikusan létrehozza az adatbázis-biztonsági mentéseket, amelyek 7 és 35 nap között vannak megőrizve, és az Azure olvasási hozzáférésű georedundáns tárolást (RA-GRS) használja annak biztosítására, hogy azok akkor is megmaradjanak, ha az adatközpont nem érhető el. Ezek a biztonsági mentések automatikusan létrejönnek. Szükség esetén engedélyezze a hosszú távú georedundáns biztonsági mentéseket az Azure SQL-adatbázisokhoz.

Ha az ügyfél által felügyelt kulcsokat használja az átlátszó adattitkosításhoz, győződjön meg arról, hogy a kulcsokról biztonsági másolatot készül.

Az Azure SQL Server biztonsági mentései:

https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups?tabs=single-database

A kulcstároló kulcsainak biztonsági mentése az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az Azure Backup tartalom-visszaállításának rendszeres időközönkénti visszaállítása. Ha szükséges, tesztelje a tartalom visszaállítását egy elkülönített VLAN-ra. Az ügyfél által felügyelt kulcsok biztonsági másolatot készülő kulcsainak visszaállítása.

A kulcstároló kulcsainak visszaállítása az Azure-ban:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

Az Azure SQL Database biztonsági másolatainak helyreállítása az időponthoz képest visszaállítás sal:

https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#point-in-time-restore

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Engedélyezze a helyreállítható törlést az Azure Key Vaultban a kulcsok véletlen vagy rosszindulatú törlés elleni védelme érdekében.

A helyreállítható törlés engedélyezése a Key Vaultban:

https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center figyelése:** Igen

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

Biztonsági riasztások az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-alerts-overview



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

A NIST kiadványa: Útmutató az informatikai tervekhez és képességekhez szükséges teszt-, képzési és edzésprogramokhoz:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az Ön adataihoz jogosulatlan vagy jogosulatlan fél fért hozzá.

Az Azure Security Center biztonsági kapcsolattartójának beállítása:

https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center riasztások és javaslatok a folyamatos exportálás funkció használatával. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötő a riasztások streamelése a Sentinel.

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
