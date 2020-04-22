---
title: Azure biztonsági alapkonfiguráció a Cosmos DB-hez
description: Azure biztonsági alapkonfiguráció a Cosmos DB-hez
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7e927b398bd95160e02ee915eb98ea3cb78d68fe
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758687"
---
# <a name="azure-security-baseline-for-cosmos-db"></a>Azure biztonsági alapkonfiguráció a Cosmos DB-hez

Az Azure Security Baseline for Cosmos DB javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Az Azure Private Link használatával csatlakozhat egy Azure Cosmos-fiókhoz egy privát végponton keresztül. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinchálózatán keresztül halad át, így kiküszöböli a nyilvános internet jelentette kitettséget. Az adatok kiszivárgásának kockázatát is csökkentheti, ha egy hálózati biztonsági csoport (NSG) kimenő szabályainak szigorú készletét konfigurálja, és az NSG-t az alhálózathoz társítja.

A Szolgáltatásvégpontok is használhatja az Azure Cosmos-fiók biztonságossá tétele. Egy szolgáltatásvégpont engedélyezésével konfigurálhatja az Azure Cosmos-fiókokat úgy, hogy csak egy Azure virtuális hálózat egy adott alhálózatáról engedélyezze a hozzáférést. Miután az Azure Cosmos DB service endpoint engedélyezve van, korlátozhatja a hozzáférést egy Azure Cosmos-fiókhoz, amely egy virtuális hálózat alhálózatából származó kapcsolatokat biztosít.

Az Azure Cosmos-fiókban tárolt adatokat ip-tűzfalak használatával is biztonságossá teheti. Az Azure Cosmos DB támogatja az IP-alapú hozzáférés-vezérlésa a bejövő tűzfal támogatása. Az Azure Cosmos-fiókban beállíthat egy IP-tűzfalat az Azure Portalon, az Azure Resource Manager-sablonokon, illetve az Azure CLI-n vagy az Azure PowerShellen keresztül.

Az Azure privát hivatkozás áttekintése:https://docs.microsoft.com/azure/private-link/private-link-overview

Az Azure Cosmos DB privát végpontjának konfigurálása:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints 

Hálózati biztonsági csoport létrehozása biztonsági konfigurációval:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Az IP-tűzfal konfigurálása a Cosmos DB-ben:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-firewall

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és kövesse a hálózatvédelmi javaslatokat az Azure Cosmos-fiókhoz kapcsolódó hálózati erőforrások biztonsága érdekében.

Ha a virtuális gépek az Azure Cosmos-fiókkal azonos virtuális hálózatban vannak telepítve, egy hálózati biztonsági csoport (NSG) használatával csökkentheti az adatok kiszivárgásának kockázatát. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy Azure Storage-fiókba a forgalomnaplózáshoz. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az Azure Security Center által biztosított hálózati biztonság megismerése:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutató:** Az originek közötti erőforrás-megosztás (CORS) szolgáltatás használatával engedélyezheti, hogy az egyik tartományban futó webalkalmazások hozzáférjenek egy másik tartomány erőforrásaihoz. A webböngészők azonos eredetű házirendként ismert biztonsági korlátozást alkalmaznak, amely megakadályozza, hogy egy weblap egy másik tartományban API-kat hívjon meg. A CORS azonban biztonságos módot biztosít arra, hogy az eredeti tartomány egy másik tartományAPI-kat hívjon meg. Miután engedélyezte a CORS-támogatást az Azure Cosmos-fiókhoz, csak a hitelesített kérelmek et értékeli ki a rendszer annak megállapítására, hogy azok a megadott szabályok szerint engedélyezettek-e.

Több forrásból származó erőforrás-megosztás konfigurálása:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-cross-origin-resource-sharing

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Az Azure Cosmos DB speciális veszélyforrások elleni védelem (ATP) használata. Az Azure Cosmos DB-hez az ATP egy további biztonsági intelligenciaréteget biztosít, amely észleli az Azure Cosmos-fiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését és integrálását a központi biztonsági felügyeleti rendszerekkel.

Engedélyezze a DDoS Protection Standard az Azure Cosmos DB-példányokkal társított virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

Az Azure Cosmos DB komplex veszélyforrások elleni védelem beállítása:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

A DDoS-védelem konfigurálása:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Ismerje meg az Azure Security Center integrált fenyegetésekkel kapcsolatos intelligenciáját:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplóit, és küldjön naplókat egy tárfiókba a forgalom naplózása érdekében. NSG-folyamatnaplókat küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Az Azure Cosmos DB speciális veszélyforrások elleni védelem (ATP) használata. Az Azure Cosmos DB-hez az ATP egy további biztonsági intelligenciaréteget biztosít, amely észleli az Azure Cosmos-fiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését és integrálását a központi biztonsági felügyeleti rendszerekkel. 

A Cosmos DB komplex veszélyforrások elleni védelem beállítása:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; a javaslat az Azure App Service szolgáltatáson vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Az Azure Cosmos-fiókhoz hozzáférést igénylő erőforrásokhoz használja a Virtuális hálózat szolgáltatáscímkéket a hálózati biztonságigcsoportok vagy az Azure Firewall hálózati hozzáférés-vezérlésének definiálására. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. AzureCosmosDB) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

A szolgáltatáscímkék használatáról további információ:https://docs.microsoft.com/azure/virtual-network/service-tags-overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Az Azure Policy használatával szabványos biztonsági konfigurációkat határozhat meg és valósíthat meg a hálózati erőforrásokhoz. A "Microsoft.DocumentDB" és a "Microsoft.Network" névterekben az Azure Cosmos DB-példányok hálózati konfigurációjának naplózására vagy kényszerítésére egyéni szabályzatok létrehozásához használjon Azure Policy aliasokat. Az Azure Cosmos DB beépített szabályzatdefinícióit is használhatja, például:

- Komplex veszélyforrások elleni védelem telepítése cosmos DB-fiókokhoz

- A Cosmos DB-nek virtuális hálózati szolgáltatás végpontját kell használnia

Azure Blueprints is használhatja a nagyméretű Azure-telepítések egyszerűsítésére a kulcsfontosságú környezeti összetevők, például az Azure Resource Manager-sablonok, szerepköralapú hozzáférés-vezérlés (RBAC) és a szabályzatok egyetlen tervezetdefinícióban csomagolásával. A tervrajzot egyszerűen alkalmazhatja új előfizetésekre, környezetekre, és a verziószámozással finomhangolhatja a vezérlést és a felügyeletet.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutatás:** Az Azure Cosmos DB üzembe helyezéséhez társított hálózati erőforrások címkéinek használata annak érdekében, hogy logikusan rendszerezhesse őket egy taxonómiába.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure Cosmos DB-példányaihoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak. 

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft fenntartja az Azure-erőforrásokhoz, például az Azure Cosmos DB-hez használt időforrást a naplókban lévő időbélyegek hez.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure-figyelőn keresztüli naplók betöltése az Azure Cosmos DB által létrehozott biztonsági adatok összesítéséhez. Az Azure Monitoron belül a Log Analytics munkaterület(ek) használatával lekérdezheti és elvégezheti az elemzéseket, és tárfiókokat használhat a hosszú távú/archiválási tároláshoz. Másik lehetőségként engedélyezheti, és a fedélzeti adatokat az Azure Sentinel vagy egy külső biztonsági incidens és eseménykezelés (SIEM). 

Diagnosztikai naplók engedélyezése az Azure Cosmos DB számára:https://docs.microsoft.com/azure/cosmos-db/logging

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze az Azure Cosmos DB diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre vagy tárfiókba. Az Azure Cosmos DB diagnosztikai beállításai az erőforrásnaplók gyűjtésére szolgálnak. Ezeket a naplókat kérésenként rögzíti, és "adatsík-naplóknak" is nevezik őket. Néhány példa az adatsík műveleteire: törlés, beszúrás és olvasás. Engedélyezheti az Azure-tevékenységnapló diagnosztikai beállításait is, és elküldheti őket ugyanarra a Log Analytics-munkaterületre.

Az Azure Cosmos DB diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/cosmos-db/logging

Az Azure-tevékenységnapló diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitorban állítsa be az Azure Cosmos DB-példányaihoz társított Log Analytics-munkaterületek naplómegőrzési időszakát a szervezet megfelelőségi előírásainak megfelelően.

A naplómegőrzési paraméterek beállítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** A Naplóanalytics egy munkaterületet a keresési kifejezések, trendek azonosítása, minták elemzése, és számos más betekintést az Azure Cosmos DB naplók, amelyek összegyűjtött.

Az Azure Cosmos DB lekérdezéseinek végrehajtása a Log Analytics-munkaterületeken:https://docs.microsoft.com/azure/cosmos-db/monitor-cosmos-db

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Az Azure Security Centerben engedélyezze az Azure Cosmos DB komplex veszélyforrások elleni védelmét a rendellenes tevékenységek biztonsági naplókban és eseményekben való figyeléséhez. Engedélyezze a diagnosztikai beállításokat az Azure Cosmos DB-ben, és küldjön naplókat egy Log Analytics-munkaterületre.

 

A Log Analytics-munkaterületet is beszervezheti az Azure Sentinelbe, mivel biztonsági vezénylési automatikus válasz (SZÁRNYALÁS) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák elhárítását. Emellett egyéni naplóriasztásokat is létrehozhat a Log Analytics-munkaterületen az Azure Monitor használatával.

Az Azure Cosmos DB fenyegetésvédelmi riasztásainak listája:https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurecosmos

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Naplóriasztások létrehozása, megtekintése és kezelése az Azure Monitor használatával:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutatás**: Nem alkalmazható; Az Azure Cosmos DB nem dolgozza fel és nem készít kártevők elleni kapcsolódó naplókat.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutatás**: Nem alkalmazható; Az Azure Cosmos DB nem dolgozza fel és nem készít DNS-sel kapcsolatos naplókat.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutató:** Az Azure Portalon az Identitás- és hozzáférés-vezérlési (IAM) ablaktáblán konfigurálhatja a szerepköralapú hozzáférés-vezérlést (RBAC), és leltárt tarthat az Azure Cosmos DB-erőforrásokról. A szerepkörök az Active Directoryban lévő felhasználókra, csoportokra, egyszerű szolgáltatástagokra és felügyelt identitásokra vonatkoznak. A beépített szerepköröket vagy egyéni szerepköröket egyénekhez és csoportokhoz használhatja.

Az Azure Cosmos DB beépített RBAC-ot biztosít az Azure Cosmos DB gyakori felügyeleti forgatókönyveihez. Az Azure Active Directoryban (AD) profillal rendelkező egyén hozzárendelheti ezeket az RBAC-szerepköröket a felhasználókhoz, csoportokhoz, szolgáltatásnévcsoportokhoz vagy felügyelt identitásokhoz az Azure Cosmos DB-erőforrások erőforrásaihoz és műveleteihez való hozzáférés biztosításához vagy megtagadásához.

Az Azure AD PowerShell-modul használatával adhoc lekérdezések felügyeleti csoportok tagjaiként is felderítheti a fiókokat. 

Emellett az Azure Cosmos DB egyes műveletek et az Azure Active Directory és a fiókspecifikus főkulcsok vezérelhetik.  Használja a "disableKeyBasedMetadataWriteAccess" fiók beállítást a kulcshozzáférés szabályozásához.

A szerepköralapú hozzáférés-vezérlés ismertetése az Azure Cosmos DB-ben:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

Saját egyéni szerepkörök létrehozása az Azure Cosmos DB-műveletek (Microsoft.DocumentDB névtér) használatával:https://docs.microsoft.com/azure/role-based-access-control/resource-provider-operations#microsoftdocumentdb

Hozzon létre egy új szerepkört az Azure Active Directoryban:https://docs.microsoft.com/azure/role-based-access-control/custom-roles

Címtárszerepkör beszereznie az Azure Active Directoryban a PowerShell használatával:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárszerepkör tagjainak beszereznie az Azure Active Directoryban a PowerShell segítségével:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

Csak a felhasználói hozzáférés korlátozása az adatműveletekhez:https://docs.microsoft.com/azure/cosmos-db/how-to-restrict-user-data

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutatás**: Az alapértelmezett vagy üres jelszavak fogalma nem létezik az Azure AD vagy az Azure Cosmos DB kapcsolatban. Ehelyett az Azure Cosmos DB kétféle kulcsot használ a felhasználók hitelesítéséhez, és hozzáférést biztosít az adatokhoz és az erőforrásokhoz; főkulcsok és erőforrás-tokenek. A kulcsok bármikor újragenerálhatók.

Az azure Cosmos DB-ben az adatokhoz való biztonságos hozzáférés ismertetése:https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data

Az Azure Cosmos DB-kulcsok újragenerálása:https://docs.microsoft.com/azure/cosmos-db/manage-with-powershell#regenerate-keys

A kulcsok programozott elérése az Azure Active Directory használatával:https://docs.microsoft.com/azure/cosmos-db/certificate-based-authentication

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutatás**: Nem alkalmazható; Az Azure Cosmos DB nem támogatja a rendszergazdai fiókokat.  Minden hozzáférés integrálva van az Azure Active Directory és az Azure szerepköralapú hozzáférés-vezérlés (RBAC).



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutatás: Az**Azure Cosmos DB kétféle kulcsot használ a felhasználók engedélyezéséhez, és nem támogatja az egyszeri bejelentkezést (SSO) az adatsík szintjén. A Cosmos DB vezérlősíkjához való hozzáférés REST API-n keresztül érhető el, és támogatja az SSO-t. A hitelesítéshez állítsa be a kérelmek engedélyezési fejlécét egy JSON webtokenre, amelyet az Azure Active Directoryból szerez be.

Ismerje meg az Azure Database for Cosmos DB REST API-t:https://docs.microsoft.com/rest/api/cosmos-db/

Az SSO megismerése az Azure Active Directoryval:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory többtényezős hitelesítését, és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Használja a kiemelt hozzáférésű munkaállomások (PAW) a többtényezős hitelesítés konfigurálva, hogy jelentkezzen be, és konfigurálja az Azure-erőforrások.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Az Azure Cosmos DB speciális veszélyforrások elleni védelem (ATP) használata. Az Azure Cosmos DB-hez az ATP egy további biztonsági intelligenciaréteget biztosít, amely észleli az Azure Cosmos-fiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését és integrálását a központi biztonsági felügyeleti rendszerekkel. 

Emellett használhatja az Azure Active Directory (AD) kiemelt identitáskezelés (PIM) a naplók és riasztások generálása, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Az Azure AD-kockázati észlelések használatával megtekintheti a riasztásokat és a kockázatos felhasználói viselkedésről szóló jelentéseket.

Kiemelt identitáskezelés (PIM) telepítése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Ismerje meg az Azure AD-kockázatészleléseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Konfigurálja a feltételes hozzáférési házirend helyfeltételét, és kezelje a megnevezett helyeket. Az elnevezett helyek segítségével logikai csoportosításokat hozhat létre AZ IP-címtartományokból vagy országokból és régiókból. Korlátozhatja a hozzáférést a bizalmas erőforrásokhoz, például az Azure Cosmos DB-példányokhoz a konfigurált elnevezett helyekre.

Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directory (AD) a központi hitelesítési és engedélyezési rendszer. Az Azure AD védi az adatokat azáltal, hogy erős titkosítást használ az inaktív és az átvitel során. Az Azure AD is sók, kihegedések, és biztonságosan tárolja a felhasználói hitelesítő adatokat.

Azure Active Directory-példány létrehozása és konfigurálása:https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance

Az Azure Active Directory-hitelesítés konfigurálása és kezelése az Azure SQL-rel:https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access Reviews segítségével hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználó hozzáférése rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel.

Az Azure Identity Access-vélemények használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Diagnosztikai beállításokat hozhat létre az Azure Active Directory felhasználói fiókokhoz, elküldheti a naplónaplókat és a bejelentkezési naplókat egy Log Analytics-munkaterületre, ahol konfigurálhatja a kívánt riasztásokat.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Azure Cosmos DB speciális veszélyforrások elleni védelem (ATP) használata. Az Azure Cosmos DB-hez az ATP egy további biztonsági intelligenciaréteget biztosít, amely észleli az Azure Cosmos-fiókok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket. Ez a védelmi réteg lehetővé teszi a fenyegetések kezelését és integrálását a központi biztonsági felügyeleti rendszerekkel. 

Az Azure AD Identity Protection és a kockázatészlelési funkció használatával is konfigurálhatja az automatikus válaszokat a felhasználói identitásokkal kapcsolatos gyanús gyanús műveletekre. Emellett további vizsgálat céljából beviheti a naplókat az Azure Sentinelbe.

Az Azure Active Directory kockázatos bejelentkezéseinek megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató**: Jelenleg nem áll rendelkezésre; Az Ügyfélszéf még nem támogatott az Azure Database for Cosmos DB számára.

Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Címkék használatával segítséget nyújt az Azure Cosmos DB-példányok nyomon követéséhez, amelyek bizalmas adatokat tárolnak vagy dolgoznak fel.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. Az Azure Cosmos DB-példányokat virtuális hálózat/alhálózat választja el egymástól, megfelelően címkézve és egy hálózati biztonsági csoporton (NSG) vagy az Azure Firewall-en belül biztosítva vannak. A bizalmas adatokat tároló Azure Cosmos DB-példányokat el kell különíteni. Az Azure Private Link használatával csatlakozhat egy Azure Cosmos DB-példányfiókhoz egy privát végponton keresztül. A privát végpont a virtuális hálózaton belüli alhálózatban lévő privát IP-címek készlete. Ezután korlátozhatja a hozzáférést a kiválasztott privát IP-címekhez. 

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Az Azure Cosmos DB privát végpontjának konfigurálása:https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints

Hálózati biztonsági csoport létrehozása biztonsági konfigurációval:https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** Az Azure Cosmos DB speciális veszélyforrások elleni védelmét is üzembe helyezheti, amely észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket. Jelenleg a következő riasztásokat indíthatja el:

- Hozzáférés szokatlan helyekről

- Szokatlan adatkinyerés

Emellett ha virtuális gépek használatával éri el az Azure Cosmos DB-példányok eléréséhez, használja a Private Link, tűzfal, hálózati biztonsági csoportok és a szolgáltatás címkék et az adatok kiszivárgásának lehetőségének csökkentése érdekében. A Microsoft kezeli az Azure Cosmos DB alapjául szolgáló infrastruktúrát, és szigorú vezérlőket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

A Cosmos DB komplex veszélyforrások elleni védelem beállítása:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-advanced-threat-protection

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató:** Az Azure Cosmos DB összes kapcsolata támogatja a HTTPS-t. Az Azure Cosmos DB a TLS1.2-t is támogatja. Lehetőség van egy minimális TLS verziókiszolgálói oldal kényszerítésére. Ehhez vegye fel [azurecosmosdbtls@service.microsoft.com](mailto:azurecosmosdbtls@service.microsoft.com)a kapcsolatot.

A Cosmos DB Security áttekintése:https://docs.microsoft.com/azure/cosmos-db/database-security

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az Azure Cosmos DB-hez még nem érhetők el automatikus adatazonosítási, besorolási és veszteségmegelőzési funkciók. Azonban használhatja az Azure Cognitive Search integráció osztályozási és adatelemzés. Egy külső gyártótól származó megoldást is megvalósíthat, ha az megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Index el- az Azure Cosmos https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb?toc=/azure/cosmos-db/toc.json&ampDB-adatait az Azure Cognitive Search szolgáltatással: ;bc=/azure/cosmos-db/breadcrumb/toc.json

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutatás: Az**Azure Cosmos DB beépített szerepköralapú hozzáférés-vezérlést (RBAC) biztosít az Azure Cosmos DB gyakori felügyeleti forgatókönyveihez. Az Azure Active Directoryban profillal rendelkező egyén hozzárendelheti ezeket az RBAC-szerepköröket a felhasználókhoz, csoportokhoz, szolgáltatástagokhoz vagy felügyelt identitásokhoz az Azure Cosmos DB-erőforrások erőforrásaihoz és műveleteihez való hozzáférés biztosításához vagy megtagadásához. Szerepkör-hozzárendelések hatóköre csak a vezérlősík-hozzáférés, amely magában foglalja az Azure Cosmos-fiókok, adatbázisok, tárolók és ajánlatok (átviteli érték) eléréséhez való hozzáférést.

Az RBAC megvalósítása az Azure Cosmos DB-ben:https://docs.microsoft.com/azure/cosmos-db/role-based-access-control

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

A Microsoft kezeli a Cosmos DB mögöttes infrastruktúráját, és szigorú vezérlőket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** A Cosmos DB-ben tárolt összes felhasználói adat alapértelmezés szerint in-t titkosítva van. Nincsenek kikapcsolási vezérlők. Az Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut.

Alapértelmezés szerint a Microsoft kezeli az Azure Cosmos-fiókban lévő adatok titkosításához használt kulcsokat. Választhat, hogy saját kulcsaival második titkosítási réteget ad hozzá.

Az Azure Cosmos DB titkosításának ismertetése:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

Az Azure Cosmos DB titkosításának kulcskezelésének ismertetése:https://docs.microsoft.com/azure/cosmos-db/cosmos-db-security-controls

Ügyfél által felügyelt kulcsok konfigurálása az Azure Cosmos DB-fiókjához:https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Monitor az Azure-tevékenységnapló használatával riasztásokat hozhat létre az Azure Cosmos DB éles példányainak módosításaihoz.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató:** Kövesse az Azure Security Center ajánlásait az Azure Cosmos DB-példányaihoz. 

A Microsoft rendszerjavítási és biztonsági réskezelést hajt végre az Azure Cosmos DB-példányait támogató mögöttes gazdagépeken. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az Azure Security Center ben elérhető támogatott funkciók:https://docs.microsoft.com/azure/security-center/security-center-services?tabs=features-windows

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás:** Az Azure Portalon vagy az Azure Resource Graph segítségével az összes erőforrást (nem csak az Azure Cosmos DB-t, hanem az előfizetés(ek)en belül olyan erőforrásokat is felderíthet, mint a számítási, egyéb tároló, hálózati, portok és protokollok).  Győződjön meg arról, hogy rendelkezik a megfelelő engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.

Bár a klasszikus Azure-erőforrások fedezhetők fel a Resource Graph, erősen ajánlott az Azure Resource Manager erőforrásainak létrehozása és használata a jövőben.

Lekérdezések létrehozása az Azure Resource Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure szerepköralapú hozzáférés-vezérlésismertetése:https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

**Útmutató:** Címkékalkalmazása az Azure Cosmos DB-példányokra és a metaadatokkal rendelkező kapcsolódó erőforrásokra, hogy logikusan rendszerezhesse őket egy taxonómiába.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Mely Azure Cosmos DB-erőforrások támogatják a címkéket:https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support#microsoftdocumentdb

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutatás:** Adott esetben tagging, felügyeleti csoportok és külön előfizetések használatával rendszerezheti és nyomon követheti az eszközöket, beleértve, de nem kizárólagosan az Azure Cosmos DB-erőforrásokat. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra és az Azure egészére szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

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

**Útmutatás**: Nem alkalmazható; ez az alapterv számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: A nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra és az Azure egészére szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6.8: Csak jóváhagyott alkalmazásokat használjon

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


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

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azureresources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az AzureResources Managerrel

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával. Ez megakadályozhatja az erőforrások létrehozását és módosítását egy magas biztonsági szintű környezetben.

A feltételes hozzáférés beállítása az Azure Resource Manager elérésének letiltásához:https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12: Korlátozza a felhasználók azon képességét, hogy parancsfájlokat hajtsanak végre a számítási erőforrásokon belül

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Fizikailag vagy logikailag elkülönítve a magas kockázatú alkalmazásokat

**Útmutatás**: Nem alkalmazható; Ez az iránymutatás az Azure App Service szolgáltatáson vagy számítási erőforrásokon futó webalkalmazásokhoz készült.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információt a [Biztonsági ellenőrzés: Biztonságos konfiguráció című](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration)témakörben talál.*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató:** Definiálja és valósítsa meg a Cosmos DB-példányok szabványos biztonsági konfigurációit az Azure Policy használatával. A "Microsoft.DocumentDB" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a Cosmos DB-példányok naplózásához vagy kényszerítéséhez. Az Azure Cosmos DB beépített szabályzatdefinícióit is használhatja, például:

- Komplex veszélyforrások elleni védelem telepítése cosmos DB-fiókokhoz

- A Cosmos DB-nek virtuális hálózati szolgáltatás végpontját kell használnia

Az elérhető Azure Policy-aliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] biztonságos beállítások kényszerítése az Azure-erőforrások között.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Az Azure-szabályzat hatásainak megismerése:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ a Cosmos DB-höz vagy a kapcsolódó erőforrásokhoz, használja az Azure Repos-t a kód biztonságos tárolásához és kezeléséhez.

Azure Repos https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops dokumentáció:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** A "Microsoft.DocumentDB" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** A "Microsoft.DocumentDB" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Azure-szabályzat [audit], [megtagadás], és [üzembe helyezés, ha nem létezik] automatikusan kényszeríti a konfigurációkat az Azure Cosmos DB-példányok és a kapcsolódó erőforrások. 

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure Cosmos DB-példányok eléréséhez használt Azure-beli virtuális gépek hez vagy az Azure App Service-szolgáltatáson futó webalkalmazásokhoz használja a Felügyelt szolgáltatásidentitást az Azure Key Vaulttal együtt az Azure Cosmos DB titkos kezelésének egyszerűsítéséhez és védelméhez. Győződjön meg arról, hogy a Key Vault ideiglenes törlése engedélyezve van.

Integrálás az Azure felügyelt identitásaival:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:https://docs.microsoft.com/azure/key-vault/managed-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Az Azure Cosmos DB-példányok eléréséhez használt Azure-beli virtuális gépek hez vagy az Azure App Service-szolgáltatáson futó webalkalmazásokhoz használja a Felügyelt szolgáltatásidentitást az Azure Key Vaulttal együtt az Azure Cosmos DB titkos kezelésének egyszerűsítéséhez és védelméhez.

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

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál. A Microsoft Antimalware engedélyezve van az alapul szolgáló gazdagépen, amely támogatja az Azure-szolgáltatásokat (például az Azure App Service), azonban nem fut az ügyfél tartalom.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft Antimalware engedélyezve van az alapul szolgáló gazdagépen, amely támogatja az Azure-szolgáltatásokat (például az Azure App Service), azonban nem fut az ügyfél tartalom.

Az Ön felelőssége, hogy előzetesen bevizsgálja a nem számítási Azure-erőforrásokba feltöltött fájlokat, beleértve az Azure Cosmos DB-t is. A Microsoft nem tud hozzáférni az ügyféladatokhoz, ezért nem végezhet kártevőirtó vizsgálatot az Ön nevében.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; számítási erőforrásokra vonatkozó teljesítményteszt. A Microsoft Antimalware engedélyezve van az alapul szolgáló gazdagépen, amely támogatja az Azure-szolgáltatásokat, de nem fut az ügyfél tartalom.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** Az Azure Cosmos DB négyóránként pillanatképeket készít az adatokról. Az összes biztonsági mentés külön tárolja a tárolási szolgáltatásban, és ezek a biztonsági mentések globálisan replikálódik a regionális katasztrófák elleni rugalmasság érdekében. Adott időpontban csak az utolsó két pillanatkép marad meg. Tároló vagy adatbázis törlése esetén azonban az Azure Cosmos DB 30 napig megőrzi az adott tároló vagy adatbázis meglévő pillanatképeit. Lépjen kapcsolatba az Azure-támogatással a biztonsági mentésből történő visszaállításhoz.

Az Azure Cosmos DB automatikus biztonsági mentések ismertetése:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutatás: Az**Azure Cosmos DB automatikusan rendszeres időközönként biztonsági mentést készít az adatokról. Ha az adatbázis vagy tároló törlődik, nyújthat be egy támogatási jegyet, vagy hívja az Azure-támogatást az automatikus online biztonsági mentések adatainak visszaállításához. Az Azure-támogatás csak a kiválasztott csomagokhoz érhető el, például a Standard, a Developer és a csomagok magasabbak náluk. A biztonsági mentés egy adott pillanatképének visszaállításához az Azure Cosmos DB megköveteli, hogy az adatok elérhetők a pillanatkép biztonsági mentési ciklusának időtartama alatt. 

Ha a Key Vault segítségével tárolja a Cosmos DB-példányok hitelesítő adatait, biztosítsa a kulcsok rendszeres automatikus biztonsági mentését.

Ismerje meg az Azure Cosmos DB automatikus biztonsági mentéseit:https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Adatok visszaállítása az Azure Cosmos DB-ben:https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

A kulcstároló kulcsainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Ha az adatbázis vagy a tároló törlődik, nyújthat be egy támogatási jegyet, vagy hívja az Azure-támogatást az automatikus online biztonsági mentések adatainak visszaállításához. Az Azure-támogatás csak a kiválasztott csomagokhoz érhető el, például a Standard, a Developer és a csomagok magasabbak náluk. A biztonsági mentés egy adott pillanatképének visszaállításához az Azure Cosmos DB megköveteli, hogy az adatok elérhetők a pillanatkép biztonsági mentési ciklusának időtartama alatt.

Tesztelje az Azure Key Vaultban tárolt titkos kulcsok visszaállítását a PowerShell használatával. Az AzureKeyVaultKey-parancsmag létrehoz egy kulcsot a megadott key vaultban. Ez a kulcs a bemeneti fájlban lévő biztonsági másolat kulcsának másolata, és ugyanaz a neve, mint az eredeti kulcsnak.

Ismerje meg az Azure Cosmos DB automatikus biztonsági mentéseit:

https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore

Adatok visszaállítása az Azure Cosmos DB-ben:

https://docs.microsoft.com/azure/cosmos-db/how-to-backup-and-restore

Az Azure Key Vault-titkok visszaállítása:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Mivel a Cosmos DB-ben tárolt összes felhasználói adat titkosítva van nyugalmi és átvitelkor, nem kell semmilyen műveletet végrehajtania. Egy másik módja annak, hogy ez az, hogy a titkosítás inaktív "on" alapértelmezés szerint. Nincsenek ki- és bekapcsolási vezérlők. Az Azure Cosmos DB AES-256 titkosítást használ minden olyan régióban, ahol a fiók fut.

Engedélyezze a soft-delete engedélyezést a Key Vaultban, hogy megvédje a kulcsokat a véletlen vagy rosszindulatú törléstől.

Ismerje meg az adatok titkosítását az Azure Cosmos DB-ben:https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest

A soft-delete engedélyezése a Key Vaultban:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.

A NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja saját eseményreagálási tervének létrehozásához:https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy incidens anatómia:https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás:** A Biztonsági központ minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire bízik a riasztás keresésében, illetve a riasztás kiadásához használt elemzésben, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

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
