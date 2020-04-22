---
title: Azure-biztonsági alapkonfiguráció a PostgreSQL single server azure-adatbázisához
description: Azure-biztonsági alapkonfiguráció a PostgreSQL single server azure-adatbázisához
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dfe1bd1cbc99838f427aaba5bc29b23f8bfcbaff
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758678"
---
# <a name="azure-security-baseline-for-azure-database-for-postgresql-single-server"></a>Azure-biztonsági alapkonfiguráció a PostgreSQL single server azure-adatbázisához

Az Azure Security Baseline for Azure Database for PostgreSQL Single Server olyan javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Konfigurálja a privát kapcsolatot az Azure Database for PostgreSQL-hez a privát végpontokkal. A Private Link lehetővé teszi, hogy egy privát végponton keresztül csatlakozzon különböző PaaS-szolgáltatásokhoz az Azure-ban. Az Azure Private Link lényegében a privát virtuális hálózaton (VNet) belül hozza az Azure-szolgáltatásokat. A virtuális hálózat és a PostgreSQL-példány közötti forgalom a Microsoft gerinchálózatát járja.

Azt is megteheti, hogy virtuális hálózati szolgáltatás végpontok védelmére és korlátozza a hálózati hozzáférést az Azure-adatbázis postgreSQL-megvalósítások. A virtuális hálózati szabályok egy tűzfalbiztonsági szolgáltatás, amely azt szabályozza, hogy az Azure Database for PostgreSQL-kiszolgáló elfogadja-e a virtuális hálózatok bizonyos alhálózataiból küldött kommunikációt.

Az Azure Database for PostgreSQL-kiszolgáló tűzfalszabályokkal is biztonságossá teheti. A kiszolgáló tűzfala mindaddig megakadályozza az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfalszabályokat a kiszolgáló szintjén hozhat létre.

A Private Link for Azure Database for PostgreSQL beállítása:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Virtuálishálózati szolgáltatásvégpontok és virtuálishálózati szabályok létrehozása és kezelése a PostgreSQL-hez készült Azure Database ben:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Az Azure Database beállítása a PostgreSQL tűzfalszabályokhoz:https://docs.microsoft.com/azure/postgresql/howto-manage-firewall-using-portal

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Ha az Azure Database for PostgreSQL-példány egy privát végponthoz van biztosítva, virtuális gépeket telepíthet ugyanabban a virtuális hálózatban. A hálózati biztonsági csoport (NSG) segítségével csökkentheti az adatok kiszivárgásának kockázatát. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

A Private Link for Azure Database for PostgreSQL beállítása:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** A PostgreSQL Azure Database speciális veszélyforrások elleni védelem használata. A komplex veszélyforrások elleni védelem észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányuló kísérletekre utalnak.

Engedélyezze a DDoS Protection Standard-ot az Azure Database for PostgreSQL-példányok hoz tartozó virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A komplex veszélyforrások elleni védelem beállítása az Azure Database for PostgreSQL-hez:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

A DDoS-védelem konfigurálása:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Ha az Azure Database for PostgreSQL-példány egy privát végponthoz van biztosítva, virtuális gépeket telepíthet ugyanabban a virtuális hálózatban. Ezután konfigurálhat egy hálózati biztonsági csoportot (NSG) az adatok kiszivárgási kockázatának csökkentése érdekében. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** A PostgreSQL Azure Database speciális veszélyforrások elleni védelem használata. A komplex veszélyforrások elleni védelem észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányuló kísérletekre utalnak.

A komplex veszélyforrások elleni védelem beállítása az Azure Database for PostgreSQL-hez:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Az erőforrások, amelyek hozzáférést igényelnek az Azure Database for PostgreSQL-példányok, használja a virtuális hálózati szolgáltatás címkék et a hálózati hozzáférés-vezérlések a hálózati biztonsági csoportok vagy az Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének megadásával (pl. SQL. WestUs) a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

Megjegyzés: Az Azure Database for PostgreSQL a "Microsoft.Sql" szolgáltatáscímkét használja.

A szolgáltatáscímkék használatáról további információ:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

A PostgreSQL-hez készült Azure Database szolgáltatáscímke-használatának ismertetése:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet#terminology-and-description

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása az Azure Database for PostgreSQL-példányok hoz tartozó hálózati beállításokhoz és hálózati erőforrásokhoz az Azure Policy használatával. A "Microsoft.DBforPostgreSQL" és a "Microsoft.Network" névterekben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre az Azure Database for PostgreSQL-példányok hálózati konfigurációjának naplózásához vagy érvényesítéséhez. A hálózatkezeléshez kapcsolódó beépített szabályzatdefiníciókat vagy a PostgreSQL-példányok Azure-adatbázisát is használhatja, például:

- A DDoS Protection Standard-ot engedélyezni kell

- Az SSL-kapcsolat kényszerítése engedélyezve kell lennie a PostgreSQL adatbázis-kiszolgálókon

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-minták hálózatépítéshez:https://docs.microsoft.com/azure/governance/policy/samples/

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** A postgreSQL-példányok azure-adatbázisához kapcsolódó hálózati biztonsággal és forgalomforgalommal kapcsolatos erőforrások címkéivel metaadatok és logikai szervezet biztosításához használjon címkéket.

Használja a címkézéshez kapcsolódó beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy az összes erőforrás címkékkel jön létre, és értesítheti a meglévő címkézetlen erőforrásokról.

Használhatja az Azure PowerShell vagy az Azure CLI a look-up, vagy műveleteket hajt végre az erőforrások alapján a címkéket.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure-adatbázishoz a PostgreSQL-példányokhoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például a PostgreSQL-adatbázist a naplókban lévő időbélyegekhez.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Engedélyezze a diagnosztikai beállításokat és a kiszolgálónaplókat, valamint a betöltési naplókat az Azure Database által a PostgreSQL-példányok számára létrehozott biztonsági adatok összesítéséhez. Az Azure Monitoron belül a Log Analytics-munkaterület(ek) segítségével lekérdezheti és elvégezheti az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM.

A Kiszolgálónaplók konfigurálása és elérése az Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Az Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Diagnosztikai beállítások engedélyezése az Azure Database postgreSQL-példányok a naplózási, biztonsági és diagnosztikai naplók eléréséhez. Győződjön meg arról, hogy kifejezetten engedélyezi a PostgreSQL naplózási naplót. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek. Engedélyezheti az Azure-tevékenységnapló diagnosztikai beállításait is, és elküldheti a naplókat ugyanahhoz a Log Analytics-munkaterületre vagy storage-fiókba.

A Kiszolgálónaplók konfigurálása és elérése az Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Az Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Diagnosztikai beállítások konfigurálása az Azure-tevékenységnaplóhoz:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitoron belül a Log Analytics-munkaterület et az Azure Database for PostgreSQL-naplók tárolására használja, állítsa be a megőrzési időszakot a szervezet megfelelőségi előírásainak megfelelően. Azure Storage-fiókok használata hosszú távú/archiválási tároláshoz.

A loganalytics-munkaterületek naplómegőrzési paramétereinek beállítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

Erőforrásnaplók tárolása Egy Azure Storage-fiókban:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Elemezze és figyelje a naplóit az Azure Database-ből a PostgreSQL-példányok rendellenes viselkedés. Az Azure Monitor Log Analytics szolgáltatásával áttekintheti a naplókat, és lekérdezéseket hajthat végre a naplóadatokon. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM.

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

A Log Analytics szolgáltatással kapcsolatos további tudnivalókért:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Speciális veszélyforrások elleni védelem engedélyezése a PostgreSQL Azure-adatbázisához. A komplex veszélyforrások elleni védelem észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányuló kísérletekre utalnak.

Ezenkívül engedélyezheti a Kiszolgálónaplók at és a PostgreSQL diagnosztikai beállításait, és naplókat küldhet a Log Analytics-munkaterületre. A Log Analytics-munkaterület et az Azure Sentinelbe, mivel egy biztonsági vezénylési automatikus válasz (SZÁRNYALÁs) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák elhárítását.

A részletes veszélyforrások elleni védelem engedélyezése a PostgreSQL-hez készült Azure Database számára:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

A Kiszolgálónaplók konfigurálása és elérése az Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Az Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Diagnosztikai beállítások konfigurálása az Azure-tevékenységnaplóhoz:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutatás**: Nem alkalmazható; Az Azure Database for PostgreSQL nem dolgozza fel és nem készít kártevők elleni kapcsolódó naplókat.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutatás**: Nem alkalmazható; Az Azure Database for PostgreSQL nem dolgozza fel és nem készít DNS-sel kapcsolatos naplókat.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutató:** Leltárt tartathat azazure-adatbázis PostgreSQL-példányokhoz a vezérlősíkjához (pl. Azure Portal) rendszergazdai hozzáféréssel rendelkező felhasználói fiókokról. Emellett leltárt tartathat azon felügyeleti fiókokról, amelyek hozzáférnek az Azure Database for PostgreSQL-példányok adatsíkjához (az adatbázison belül). (A PostgreSQL-kiszolgáló létrehozásakor hitelesítő adatokat ad meg egy rendszergazdai felhasználónak. Ezzel a rendszergazdával további PostgreSQL-felhasználókat hozhat létre.)

A PostgreSQL Azure Database nem támogatja a beépített szerepköralapú hozzáférés-vezérlést, de létrehozhat egyéni szerepköröket adott erőforrás-szolgáltatói műveletek alapján.

Az Azure-előfizetés egyéni szerepkörei:https://docs.microsoft.com/azure/role-based-access-control/custom-roles 

Az Azure Database for PostgreSQL erőforrás-szolgáltató műveleteinek ismertetése:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdbforpostgresql 

A PostgreSQL Azure Database hozzáférés-kezelésének ismertetése:https://docs.microsoft.com/azure/postgresql/concepts-security#access-management

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutató:** Az Azure Active Directory és az Azure Database for PostgreSQL nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az Azure Database for PostgreSQL erőforrás létrehozásakor az Azure kényszeríti egy rendszergazdai felhasználó létrehozását egy erős jelszóval. A PostgreSQL-példány létrehozása után azonban az első létrehozott kiszolgálói rendszergazdai fiókkal további felhasználókat hozhat létre, és rendszergazdai hozzáférést biztosíthat hozzájuk. A fiókok létrehozásakor győződjön meg arról, hogy minden fiókhoz más-más, erős jelszót állít be.

További fiókok létrehozása az Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-create-users

Rendszergazdai jelszó frissítése:https://docs.microsoft.com/azure/postgresql/howto-create-manage-server-portal#update-admin-password

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató:** Hozzon létre szabványos működési eljárásokat a postgreSQL-példányokhoz az Azure-adatbázishoz hozzáféréssel rendelkező dedikált felügyeleti fiókok használatával kapcsolatban. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát. 

Ismerje meg az Azure Security Center identitását és hozzáférését:https://docs.microsoft.com/azure/security-center/security-center-identity-access 

Ismerje meg, hogyan hozhat létre rendszergazdai felhasználókat a PostgreSQL Azure Database szolgáltatásában:https://docs.microsoft.com/azure/postgresql/howto-create-users#the-server-admin-account


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutató:** A PostgreSQL Azure Database for PostgreSQL-be való bejelentkezés egyaránt támogatott a közvetlenül az adatbázisban konfigurált felhasználónév/jelszó használatával, valamint az Azure Active Directory (AD) identitás használatával, valamint egy Azure AD-jogkivonat használatával a csatlakozáshoz. Egy Azure AD-jogkivonat használata esetén a különböző módszerek támogatottak, például egy Azure AD-felhasználó, egy Azure AD-csoport vagy egy Azure AD-alkalmazás, amely csatlakozik az adatbázishoz.

Külön-külön a PostgreSQL vezérlősík-hozzáférése rest API-n keresztül érhető el, és támogatja az SSO-t. A hitelesítéshez állítsa be a kérelmek engedélyezési fejlécét egy JSON webtokenre, amelyet az Azure Active Directoryból szerez be.

Az Azure Active Directory használata a PostgreSQL Azure Database használatával történő hitelesítéshez:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Ismerje meg a PostgreSQL REST API Azure-adatbázisát:https://docs.microsoft.com/rest/api/postgresql/

Az SSO ismertetése az Azure AD-vel:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait. Az adatbázisba való bejelentkezéshez azure AD-jogkivonatok használatakor ez lehetővé teszi, hogy többtényezős hitelesítést igényeljen az adatbázis-bejelentkezésekhez.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Active Directory használata a PostgreSQL Azure Database használatával történő hitelesítéshez:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Használja a kiemelt hozzáférésű munkaállomásokat többtényezős hitelesítéssel (MFA) konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfiguráláshoz.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Engedélyezze a részletes veszélyforrások elleni védelmet a PostgreSQL Azure-adatbázisához a gyanús tevékenységekriasztásainak létrehozásához.

Emellett használhatja az Azure Active Directory (AD) kiemelt identitáskezelés (PIM) a naplók és riasztások generálása, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Az Azure AD-kockázati észlelések használatával megtekintheti a riasztásokat és a kockázatos felhasználói viselkedésről szóló jelentéseket.

A Speciális veszélyforrások elleni védelem beállítása a PostgreSQL-hez készült Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Kiemelt identitáskezelés (PIM) telepítése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Ismerje meg az Azure AD-kockázatészleléseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés sel ellátott helyek használatával engedélyezheti a portál és az Azure Resource Manager hozzáférését csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directory (AD) a központi hitelesítési és engedélyezési rendszer. Az Azure AD védi az adatokat azáltal, hogy erős titkosítást használ az inaktív és az átvitel során. Az Azure AD is sók, kihegedések, és biztonságosan tárolja a felhasználói hitelesítő adatokat.

Az Azure Database for PostgreSQL-be való bejelentkezéshez ajánlott az Azure AD használata és egy Azure AD-token használata a csatlakozáshoz. Egy Azure AD-jogkivonat használata esetén a különböző módszerek támogatottak, például egy Azure AD-felhasználó, egy Azure AD-csoport vagy egy Azure AD-alkalmazás, amely csatlakozik az adatbázishoz.

Az Azure AD-hitelesítő adatok is használhatók a felügyeleti sík szintjén (pl. az Azure portal) a PostgreSQL felügyeleti fiókok vezérléséhez.

Az Azure Active Directory használata a PostgreSQL Azure Database használatával történő hitelesítéshez:https://docs.microsoft.com/azure/postgresql/howto-configure-sign-in-aad-authentication

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Tekintse át az Azure Active Directory-naplókat az elavult fiókok felderítéséhez, amelyek magukban foglalhatják az Azure Database postgreSQL felügyeleti szerepkörökkel való adatait. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a PostgreSQL-hez készült Azure Database eléréséhez használható vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférést rendszeresen, például 90 naponta felül kell vizsgálni, hogy csak a megfelelő Felhasználók férhessenek hozzá.

Ismerje meg az Azure AD-jelentéseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity Access-vélemények használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Tekintse át a PostgreSQL-felhasználókat és a hozzárendelt szerepköröket:https://www.postgresql.org/docs/current/database-roles.html

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Engedélyezze az Azure Database diagnosztikai beállításait a PostgreSQL és az Azure Active Directory számára, és küldje el az összes naplót egy Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat (például a sikertelen hitelesítési kísérleteket) a Log Analytics szolgáltatásban.

A Kiszolgálónaplók konfigurálása és elérése az Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-configure-server-logs-in-portal

Az Azure Database for PostgreSQL naplózási naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/postgresql/concepts-audit

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Engedélyezze a részletes veszélyforrások elleni védelmet a PostgreSQL Azure-adatbázisához a gyanús tevékenységekriasztásainak létrehozásához.

Az Azure Active Directory identitásvédelmi és kockázatészlelési szolgáltatásaival konfigurálhatja az észlelt gyanús műveletekre adott automatikus válaszokat. Az Azure Sentinelen keresztül engedélyezheti az automatikus válaszokat a szervezet biztonsági válaszainak megvalósításához.

További vizsgálat céljából az Azure Sentinel betöltésével is beviheti a naplókat.

A Speciális veszélyforrások elleni védelem beállítása a PostgreSQL-hez készült Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/howto-database-threat-protection-portal

Az Azure AD-identitásvédelem áttekintése:https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection

Az Azure AD kockázatos bejelentkezései megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató**: Jelenleg nem áll rendelkezésre; Az ügyfélszéf még nem támogatott a PostgreSQL-alapú Azure Database szolgáltatásban.

Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Címkék használatával segítséget nyújt az Azure Database postgreSQL-példányok vagy a bizalmas adatokat tároló vagy feldolgozó kapcsolódó erőforrások nyomon követésében.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. A Private Link, a Service Endpoints és/vagy a tűzfalszabályok kombinációjával elkülönítheti és korlátozhatja az Azure-adatbázishoz való hálózati hozzáférést a PostgreSQL-példányokhoz.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

A Private Link for Azure Database for PostgreSQL beállítása:https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal

Virtuálishálózati szolgáltatásvégpontok és virtuálishálózati szabályok létrehozása és kezelése a PostgreSQL-hez készült Azure Database ben:https://docs.microsoft.com/azure/postgresql/howto-manage-vnet-using-portal

Az Azure Database beállítása a PostgreSQL tűzfalszabályokhoz:https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules

**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** Ha az Azure Virtuális gépek használatával éri el az Azure Database for PostgreSQL-példányok, használja a Private Link, PostgreSQL hálózati konfigurációk, hálózati biztonsági csoportok és a szolgáltatás címkék et az adatok kiszivárgásának lehetőségének csökkentése érdekében.

A Microsoft kezeli az Azure Database for PostgreSQL mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az Azure Database for PostgreSQL adatainak kiszivárgásának csökkentése:https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató: Az**Azure Database for PostgreSQL támogatja a PostgreSQL kiszolgáló ügyfélalkalmazásokhoz való csatlakoztatását a Secure Sockets Layer (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Az Azure Portalon győződjön meg arról, hogy az "SSL-kapcsolat kényszerítése" alapértelmezés szerint engedélyezve van az összes Azure-adatbázis postgreSQL-példányok.

Jelenleg a TLS-verzió támogatott Azure Database for PostgreSQL a TLS 1.0, TLS 1.1, TLS 1.2.

A titkosítás konfigurálása átvitel közben az Azure Database for PostgreSQL számára:https://docs.microsoft.com/azure/postgresql/concepts-ssl-connection-security

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az adatok azonosításával, besorolásával és a veszteségmegelőzési funkciókkal még nem érhetők el a PostgreSQL-alapú Azure Database szolgáltatáshoz. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja az Azure Database for PostgreSQL vezérlősíkhoz való hozzáférést (például az Azure Portal). Adatsík-hozzáférés esetén (magában az adatbázisban) SQL-lekérdezésekkel hozhat létre felhasználókat, és konfigurálhatja a felhasználói engedélyeket. Az RBAC nem befolyásolja az adatbázison belüli felhasználói engedélyeket.

Az RBAC konfigurálása az Azure-ban:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

A felhasználói hozzáférés konfigurálása az SQL for Azure Database for PostgreSQL szolgáltatással:https://docs.microsoft.com/azure/postgresql/howto-create-users

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kezeli az Azure Database for PostgreSQL mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Az Azure Database for PostgreSQL szolgáltatás a FIPS 140-2 érvényesített kriptográfiai modult használja az adatok inaktív tárolásának titkosításához. Az adatok, beleértve a biztonsági másolatokat is, titkosítva vannak a lemezen, kivéve a lekérdezések futtatása közben létrehozott ideiglenes fájlokat. A szolgáltatás az Azure storage titkosításában található AES 256 bites titkosítást használja, és a kulcsok rendszeráltal felügyeltek. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.

Az ügyfél által felügyelt kulcsokkal (CMK) a PostgreSQL Single server hez készült Azure Database for PostgreSQL Single server adattitkosításával lehetővé teszi, hogy saját kulcsát (BYOK) hozza nyugalmi állapotba. Ebben az időben hozzáférést kell kérnie a funkció használatához. Ehhez forduljon a következő höz:

AskAzureDBforPostgreSQL@service.microsoft.com.

A PostgreSQL Azure Database titkosításának ismertetése:https://docs.microsoft.com/azure/postgresql/concepts-security

Ismerje meg az Azure Database for PostgreSQL inaktív titkosítását az ügyfél által felügyelt kulcsok használatával:https://docs.microsoft.com/azure/postgresql/concepts-data-encryption-postgresql


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Monitor és az Azure-tevékenységnapló használatával riasztásokat hozhat létre az Azure Database for PostgreSQL éles példányainak és más kritikus vagy kapcsolódó erőforrásoknak az éles környezetben történő módosításaihoz.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató**: Jelenleg nem áll rendelkezésre; Az Azure Security Center még nem támogatja az Azure Database for PostgreSQL biztonsági résfelmérését.

Az Azure PaaS-szolgáltatások szolgáltatásának lefedettsége az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/features-paas

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutató: A**Microsoft biztonsági réskezelést hajt végre az Azure Database for PostgreSQL-t támogató mögöttes rendszereken.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás: Az**Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure Database for PostgreSQL-példányokat is) az előfizetés(ek)en belül). Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása az Azure Database postgreSQL-példányok és más kapcsolódó erőforrások, amelyek metaadatok logikailag rendszerezni őket egy taxonómia.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Adott esetben tagging, felügyeleti csoportok és külön előfizetések használatával rendszerezheti és nyomon követheti az Azure Database for PostgreSQL-példányokat és a kapcsolódó erőforrásokat. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra és az Azure egészére szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti/felderítheti az erőforrásokat az előfizetés(ek)en belül.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6.9: Csak jóváhagyott Azure-szolgáltatások használata

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása az Azure-szabályzattal:https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resources Managerrel

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával. Ez megakadályozhatja az erőforrások létrehozását és módosításait egy magas biztonsági szintű környezetben, például a bizalmas adatokat tartalmazó Azure Database for PostgreSQL példányait.

A feltételes hozzáférés beállítása az Azure Resource Manager elérésének letiltásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Az Azure Database for PostgreSQL-példányok szabványos biztonsági konfigurációit definiálhatja és valósíthatja meg az Azure Policy használatával. A "Microsoft.DBforPostgreSQL" névtérben az Azure-adatbázis PostgreSQL-példányok hálózati konfigurációjának naplózására vagy kényszerítésére szolgáló egyéni szabályzatok létrehozásához használja az Azure Policy aliasokat. Az Azure Database for PostgreSQL-példányokhoz kapcsolódó beépített szabályzatdefiníciókat is használhatja, például:

- Az SSL-kapcsolat kényszerítése engedélyezve kell lennie a PostgreSQL adatbázis-kiszolgálókon

- A PostgreSQL adatbázis-kiszolgálókon engedélyezni kell a naplókapcsolatokat

Az elérhető Azure Policy-aliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] biztonságos beállítások kényszerítése az Azure-erőforrások között.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-szabályzat hatásainak megismerése:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ az Azure Database for PostgreSQL-példányok hoz és a kapcsolódó erőforrásokhoz, használja az Azure Repos-t a kód biztonságos tárolásához és kezeléséhez.

Kód tárolása az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** A "Microsoft.DBforPostgreSQL" névtérben használja az Azure Policy aliasokat egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztásához, naplózásához és kényszerítéséhez. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** A "Microsoft.DBforPostgreSQL" névtérben használja az Azure Policy aliasokat egyéni szabályzatok létrehozásához a rendszerkonfigurációk riasztásához, naplózásához és kényszerítéséhez. Azure-szabályzat [naplózás], [megtagadás], és [üzembe helyezés, ha nem létezik] automatikusan kényszeríti a konfigurációk az Azure Database postgreSQL-példányok és a kapcsolódó erőforrások.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure-beli virtuális gépek hez vagy az Azure App Service-en futó webes alkalmazásokhoz, amelyek et a PostgreSQL-példányok Azure-adatbázisának elérésére használnak, használja a Felügyelt szolgáltatásidentitást az Azure Key Vaultszolgáltatással együtt a PostgreSQL titkos kezelésével kapcsolatos Azure-adatbázis egyszerűsítéséhez és biztonságossá tételéhez. Győződjön meg arról, hogy a Key Vault ideiglenes törlése engedélyezve van.

Integrálás az Azure felügyelt identitásaival:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:https://docs.microsoft.com/azure/key-vault/managed-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Az Azure Database for PostgreSQL server támogatja az Azure Active Directory-hitelesítést (előzetes verzióban) az adatbázisok eléréséhez.  A PostgreSQL-kiszolgálóhoz készült Azure Database létrehozásakor hitelesítő adatokat adhat meg egy rendszergazdai felhasználónak. Ezzel a rendszergazdával további adatbázis-felhasználókat hozhat létre.  

Az Azure Database for PostgreSQL-kiszolgálóhoz való hozzáféréshez használt Azure-alapú virtuális gépek vagy az Azure App Service szolgáltatáson futó webalkalmazások esetében az Azure Key Vault szolgáltatással együtt a Felügyelt szolgáltatás identitását használja az Azure Database for PostgreSQL-kiszolgáló hitelesítő adatainak tárolására és lekéréséhez. Győződjön meg arról, hogy a Key Vault ideiglenes törlése engedélyezve van.

Felügyelt identitások használatával automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásoknak az Azure Active Directoryban (AD). Felügyelt identitások lehetővé teszi, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatások, beleértve a Key Vault, hitelesítő adatok nélkül a kódot.

Felügyelt identitások konfigurálása:https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integrálás az Azure felügyelt identitásaival:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: A nem kívánt hitelesítő adatok expozíciójának kiküszöbölése

**Útmutató:** A hitelesítő adatok at a kódon belüli hitelesítő adatok azonosítására valósíthatja meg. A Hitelesítő adatok olvasója azt is ösztönzi a felderített hitelesítő adatok áthelyezése biztonságosabb helyekre, például az Azure Key Vault.

A Hitelesítő adatok képolvasójának beállítása:https://secdevtools.azurewebsites.net/helpcredscan.html

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információ: [Security Control: Malware Defense](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1: Központilag kezelt kártevőirtó szoftverek használata

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköz e-alapú gazdagépen (például az Azure App Service- szolgáltatást) támogató gazdagépen engedélyezve van, azonban nem fut az ügyféltartalomon.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure Database for PostgreSQL- en), azonban nem fut az ügyféltartalomon.

A nem számítási Azure-erőforrásokba feltöltött tartalmak, például az App Service, a Data Lake Storage, a Blob Storage, az Azure Database for PostgreSQL stb. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató gazdagépen (például a PostgreSQL-adatbázisban), azonban nem fut az ügyféltartalomon.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** Az Azure Database for PostgreSQL biztonsági másolatot készít az adatfájlokról és a tranzakciós naplóról. A támogatott maximális tárterület méretétől függően teljes és különbözeti biztonsági mentéseket (4 TB max tárolókiszolgálók) vagy pillanatkép-biztonsági mentéseket (akár 16 TB-os maximális tárolókiszolgálókat) készítünk. Ezek a biztonsági mentések lehetővé teszik, hogy a konfigurált biztonsági mentési megőrzési időszakon belül bármikor visszaállítsa a kiszolgálót a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Igény szerint legfeljebb 35 napig konfigurálhatja. Minden biztonsági mentés a 256 bites AES titkosítással van titkosítva.

Kiszolgáló biztonsági mentése az Azure Database for PostgreSQL szolgáltatásban:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

Ismerje meg az Azure Database for PostgreSQL kezdeti konfigurációját:https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutató: Az**Azure Database for PostgreSQL automatikusan létrehozza a kiszolgáló biztonsági másolatait, és helyileg redundáns vagy georedundáns tárolóban tárolja azokat, a felhasználó választásának megfelelően. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és visszaállítás minden üzletmenet-folytonossági stratégia alapvető részét képezi, mivel megvédi az adatokat a véletlen sérüléstől vagy törléstől.

Ha az Azure Key Vault használatával tárolja a postgreSQL-példányok azure-adatbázisának hitelesítő adatait, biztosítsa a kulcsok rendszeres automatikus biztonsági mentését.

Kiszolgáló biztonsági mentése az Azure Database for PostgreSQL szolgáltatásban:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

A kulcstároló kulcsainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az Azure Database for PostgreSQL,a visszaállítás végrehajtása létrehoz egy új kiszolgálót az eredeti kiszolgáló biztonsági mentések. Kétféle visszaállítás érhető el: pont-in-time visszaállítás és geo-visszaállítás. Az időponthoz való intézés visszaállítása biztonsági mentési redundancia beállítással érhető el, és az eredeti kiszolgálóval azonos régióban hoz létre új kiszolgálót. A geo-visszaállítás csak akkor érhető el, ha a kiszolgálót georedundáns tárolásra konfigurálta, és lehetővé teszi a kiszolgáló visszaállítását egy másik régióba.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázis méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az ugyanabban a régióban egyidejűleg helyreállító adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

Rendszeresen tesztelje az Azure-adatbázis postgreSQL-példányok visszaállítását.

Kiszolgáló biztonsági mentése az Azure Database for PostgreSQL szolgáltatásban:https://docs.microsoft.com/azure/postgresql/howto-restore-server-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Az Azure Database for PostgreSQL teljes, különbözeti és tranzakciós napló biztonsági mentést készít. Ezek a biztonsági mentések lehetővé teszik, hogy a konfigurált biztonsági mentési megőrzési időszakon belül bármikor visszaállítsa a kiszolgálót a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Igény szerint legfeljebb 35 napig konfigurálhatja. Minden biztonsági mentés a 256 bites AES titkosítással van titkosítva.

A biztonsági mentés és visszaállítás a PostgreSQL Azure Database szolgáltatásában:https://docs.microsoft.com/azure/postgresql/concepts-backup

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy incidens anatómia:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az Ügyfél a NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítse saját eseményreagálási tervének létrehozását:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás:** A Biztonsági központ minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

Ezenkívül egyértelműen jelölje meg az előfizetéseket (pl. termelés, nem prod), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosítására és kategorizálására.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz:https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az ügyfél adataihoz jogellenes vagy jogosulatlan fél fért hozzá.  Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.

Az Azure Security Center biztonsági kapcsolattartójának beállítása:https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center riasztások és javaslatok a folyamatos exportálás funkció használatával. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötő a Sentinel riasztások streameléséhez.

A folyamatos exportálás konfigurálása:https://docs.microsoft.com/azure/security-center/continuous-export

Értesítések streamelése az Azure Sentinelbe:https://docs.microsoft.com/azure/sentinel/connect-azure-security-center

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" biztonsági riasztásokra és javaslatokra vonatkozó válaszokat.

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása:https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelést végez az Azure-erőforrásokon, és 60 napon belül biztosítja az összes kritikus biztonsági megállapítás helyreállítását

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályzatát annak érdekében, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrák, szolgáltatások és alkalmazások terén itt olvashat bővebben:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
