---
title: Azure-biztonsági alapkonfiguráció a MariaDB Azure-adatbázisához
description: Azure-biztonsági alapkonfiguráció a MariaDB Azure-adatbázisához
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: bca9c0e4c0695b6180775051d8b018930f8b808f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256467"
---
# <a name="azure-security-baseline-for-azure-database-for-mariadb"></a>Azure-biztonsági alapkonfiguráció a MariaDB Azure-adatbázisához

Az Azure Security Baseline for Azure Database for MariaDB olyan javaslatokat tartalmaz, amelyek segítenek a központi telepítés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Konfigurálja a privát kapcsolatot az Azure Database számára a MariaDB számára a privát végpontokkal. A Private Link lehetővé teszi, hogy egy privát végponton keresztül csatlakozzon különböző PaaS-szolgáltatásokhoz az Azure-ban. Az Azure Private Link lényegében a privát virtuális hálózaton (VNet) belül hozza az Azure-szolgáltatásokat. A virtuális hálózat és a MariaDB-példány közötti forgalom a Microsoft gerinchálózatát járja be.

Azt is megteheti, hogy virtuális hálózati szolgáltatás végpontok védelmére és korlátozza a hálózati hozzáférést az Azure-adatbázishoz A MariaDB megvalósítások. A virtuális hálózati szabályok egy tűzfalbiztonsági szolgáltatás, amely azt szabályozza, hogy a MariaDB-hez készült Azure Database elfogadja-e a virtuális hálózatok bizonyos alhálózataiból küldött kommunikációt.

Az Azure Database for MariaDB tűzfalszabályokkal is biztonságossá teheti. A kiszolgáló tűzfala mindaddig megakadályozza az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfalszabályokat a kiszolgáló szintjén hozhat létre.

A Privát hivatkozás konfigurálása az Azure Database for MariaDB számára:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Virtuálishálózati szolgáltatásvégpontok és virtuálishálózati szabályok létrehozása és kezelése az Azure Database for MariaDB-kiszolgálón:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

Az Azure Database beállítása a MariaDB tűzfalszabályokhoz:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Ha az Azure Database for MariaDB-kiszolgáló egy privát végponthoz van biztosítva, virtuális gépeket telepíthet ugyanabban a virtuális hálózatban. A hálózati biztonsági csoport (NSG) segítségével csökkentheti az adatok kiszivárgásának kockázatát. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

A Privát hivatkozás konfigurálása az Azure Database for MariaDB számára:https://docs.microsoft.com/azure/mariadb/howto-configure-privatelink-portal

Az NSG-folyamatnaplók engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal A Forgalomelemzés engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Speciális veszélyforrások elleni védelem használata a MariaDB Azure Database szolgáltatáshoz. A komplex veszélyforrások elleni védelem észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányuló kísérletekre utalnak.

Engedélyezze a DDoS Protection Standard ot az Azure Database-hez a MariaDB-példányokhoz társított virtuális hálózatokon a DDoS-támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A Speciális veszélyforrások elleni védelem beállítása az Azure Database for MariaDB-hez:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

A DDoS-védelem konfigurálása:https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Ha az Azure Database for MariaDB-kiszolgáló egy privát végponthoz van biztosítva, virtuális gépeket telepíthet ugyanabban a virtuális hálózatban. Ezután konfigurálhat egy hálózati biztonsági csoportot (NSG) az adatok kiszivárgási kockázatának csökkentése érdekében. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy tárfiókba a forgalomnaplózáshoz. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése: https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal A Forgalomelemzés engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Speciális veszélyforrások elleni védelem használata a MariaDB Azure Database szolgáltatáshoz. A komplex veszélyforrások elleni védelem észleli a rendellenes tevékenységeket, amelyek szokatlan és potenciálisan káros adatbázisok elérésére vagy kihasználására irányuló kísérletekre utalnak.
A Speciális veszélyforrások elleni védelem beállítása az Azure Database for MariaDB-hez:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Az erőforrások, amelyek hozzáférést igényelnek az Azure Database MariaDB-példányok, használja a virtuális hálózati szolgáltatás címkék et a hálózati hozzáférés-vezérlések a hálózati biztonsági csoportok vagy az Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének megadásával (pl. SQL. WestUs) a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.
Megjegyzés: Az Azure Database for MariaDB a "Microsoft.Sql" szolgáltatáscímkét használja.

További információ a szolgáltatáscímkék https://docs.microsoft.com/azure/virtual-network/service-tags-overview használatáról: A Szolgáltatáscímke használatának ismertetése a MariaDB-hez az Azure Database számára:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet#terminology-and-description



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása az Azure-adatbázishoz tartozó hálózati beállításokhoz és hálózati erőforrásokhoz az Azure-szabályzattal. A "Microsoft.DBforMariaDB" és a "Microsoft.Network" névterekben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre az Azure Database MariaDB-példányok hálózati konfigurációjának naplózásához vagy érvényesítéséhez. A hálózatkezeléshez kapcsolódó beépített szabályzatdefiníciókat vagy az Azure Database-t a MariaDB-példányokhoz is használhatja, például:

- A DDoS Protection Standard-ot engedélyezni kell

- A MariaDB-kiszolgálók privát végpontja engedélyezve van

- A MariaDB-kiszolgálónak virtuális hálózati szolgáltatásvégpontot kell használnia

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Policy-minták hálózatépítéshez:https://docs.microsoft.com/azure/governance/policy/samples/

Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** A MariaDB-példányok hálózati biztonsággal és forgalommal kapcsolatos erőforrásokcímkék használatával metaadatokat és logikai szervezeti műveleteket biztosít.

Használja a címkézéssel kapcsolatos beépített Azure-szabályzat-definíciók bármelyikét, például a "Címke és annak értéke" használatával győződjön meg arról, hogy minden erőforrás címkékkel jön létre, és értesítse a meglévő címkézetlen erőforrásokról.

Használhatja az Azure PowerShell vagy az Azure CLI a look-up, vagy műveleteket hajt végre az erőforrások alapján a címkéket.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure-adatbázishoz kapcsolódó változásokat a MariaDB-példányok hoz létre. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.
Az Azure-tevékenységnapló eseményeinek https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view megtekintése és beolvasása: Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft fenntartja az Azure-erőforrásokhoz használt időforrást, például a MariaDB-hez való Azure Database a naplókban lévő időbélyegek hez.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Engedélyezze a diagnosztikai beállításokat és a kiszolgálónaplókat, valamint a betöltési naplókat az Azure Database által a MariaDB-példányokhoz létrehozott biztonsági adatok összesítéséhez. Az Azure Monitoron belül a Log Analytics-munkaterület(ek) segítségével lekérdezheti és elvégezheti az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM.
A Kiszolgálónaplók konfigurálása és elérése a MariaDB Azure Database szolgáltatáshoz:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

Az Azure Database for MariaDB naplózási naplóinak konfigurálása és elérése: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Az Azure Sentinel alaplapja:https://docs.microsoft.com/azure/sentinel/quickstart-onboard



**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze a diagnosztikai beállításokat az Azure Database-ben a MariaDB-példányokhoz a naplózási, biztonsági és diagnosztikai naplók eléréséhez. Győződjön meg arról, hogy kifejezetten engedélyezi a MariaDB naplózási naplót. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek. Engedélyezheti az Azure-tevékenységnapló diagnosztikai beállításait is, és elküldheti a naplókat ugyanahhoz a Log Analytics-munkaterületre vagy storage-fiókba.

A Kiszolgálónaplók konfigurálása és elérése az https://docs.microsoft.com/azure/mariadb/concepts-server-logs Azure-adatbázishoz: Az Azure Database for MariaDB naplóinak konfigurálása és elérése: https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal Diagnosztikai beállítások konfigurálása az Azure-tevékenységnaplóhoz:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy



**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitoron belül a Log Analytics-munkaterület, amely az Azure-adatbázis tárolására szolgál a MariaDB-naplókhoz, állítsa be a megőrzési időszakot a szervezet megfelelőségi előírásainak megfelelően. Azure Storage-fiókok használata hosszú távú/archiválási tároláshoz.
A logelemzési munkaterületek naplómegőrzési https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period paramétereinek beállítása: erőforrásnaplók tárolása Azure Storage-fiókban:https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-storage



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Elemezze és figyelje a MariaDB-példányok naplóit a rendellenes viselkedéshez. Az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket a naplóadatokon. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM.

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

A Log Analytics-munkaterülettel kapcsolatos további tudnivalókért:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** A MariaDB speciális veszélyforrások elleni védelmének engedélyezése. A MariaDB-hez való komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.

Ezenkívül engedélyezheti a MariaDB kiszolgálónaplók és diagnosztikai beállítások szolgáltatást, és naplókat küldhet a Log Analytics-munkaterületre. A Log Analytics-munkaterület et az Azure Sentinelbe, mivel egy biztonsági vezénylési automatikus válasz (SZÁRNYALÁs) megoldást biztosít. Ez lehetővé teszi a forgatókönyvek (automatizált megoldások) létrehozását és a biztonsági problémák elhárítását.

Az Advanced Threat Protection engedélyezése a MariaDB számára:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

A MariDB kiszolgálói naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

A MariaDB naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutató**: N/A; A MariaDB nem dolgozza fel és nem készít kártevőirtó valamenyre vonatkozó naplókat.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutató**: N/A; A MariaDB nem dolgozza fel és nem készít DNS-alapú naplókat.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutató**: N/A; számítási erőforrásokra vonatkozó teljesítményteszt.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutató:** Leltárt tartanak a MariaDB-példányok felügyeleti hozzáféréssel rendelkező felhasználói fiókokról (Azure Portal/Azure Resource Manager). Emellett leltárt tartanak azon felügyeleti fiókokról, amelyek hozzáférnek a MariaDB-példányok adatsíkjához. (A MariaDB-kiszolgáló létrehozásakor hitelesítő adatokat ad meg egy rendszergazdai felhasználónak. Ezzel a rendszergazdával további MariaDB-felhasználókat hozhat létre.)

A MariaDB hozzáférés-kezelésének ismertetése:https://docs.microsoft.com/azure/mariadb/concepts-security#access-management

Ismerje meg az Azure-előfizetések beépített RBAC szerepköreit:https://docs.microsoft.com/azure/role-based-access-control/built-in-roles


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutató:** Az Azure Active Directory nem rendelkezik az alapértelmezett jelszavak fogalmát.

A MariaDB-erőforrás létrehozásakor az Azure kényszeríti egy rendszergazdai felhasználó létrehozását egy erős jelszóval. A MariaDB-példány létrehozása után azonban a létrehozott első kiszolgálófelügyeleti fiókkal további felhasználókat hozhat létre, és rendszergazdai hozzáférést biztosíthat hozzájuk. A fiókok létrehozásakor győződjön meg arról, hogy minden fiókhoz más-más, erős jelszót állít be.

További fiókok létrehozása a MariaDB-hez:https://docs.microsoft.com/azure/mariadb/howto-create-users


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató:** Hozzon létre szabványos működési eljárásokat a MariaDB-példányokhoz hozzáféréssel rendelkező dedikált felügyeleti fiókok használatával kapcsolatban. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát.

Ismerje meg az Azure Security Center identitását és hozzáférését:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutató:** A MariaDB-hez való adatsík-hozzáférést az adatbázisban tárolt identitások szabályozzák, és nem támogatja az SSO-t. A MariaDB vezérlősík-hozzáférése REST API-n keresztül érhető el, és támogatja az SSO-t. A hitelesítéshez állítsa be a kérelmek engedélyezési fejlécét egy JSON webtokenre, amelyet az Azure Active Directoryból szerez be.

Ismerje meg a MariaDB REST API Azure-adatbázisát:https://docs.microsoft.com/rest/api/mariadb/

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure AD MFA-t, és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Emelt szintű munkaállomások (kiemelt hozzáférésű munkaállomások) használata az MFA konfigurálva az Azure-erőforrásokba való bejelentkezéshez és konfigurálásához.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Engedélyezze a MariaDB komplex veszélyforrások elleni védelmét a gyanús tevékenységekriasztásának létrehozásához.

Emellett használhatja az Azure AD kiemelt identitáskezelés (PIM) a naplók és riasztások generálása, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure AD-kockázati észlelések használatával megtekintheti a riasztásokat és a kockázatos felhasználói viselkedésről szóló jelentéseket.

A MariaDB komplex veszélyforrások elleni védelmének beállítása:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Kiemelt identitáskezelés (PIM) telepítése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Ismerje meg az Azure AD-kockázatészleléseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáférés selellátott helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezheti a hozzáférést az Azure-erőforrásokhoz, például a MariaDB-hez való hozzáférés korlátozásához.

Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directoryt (AAD) központi hitelesítési és engedélyezési rendszerként. Az AAD az adatokat erős titkosítással védi az inaktív és az átvitel során tárolt adatokhoz. Az AAD a felhasználói hitelesítő adatokat is megsózta, kiigazítja és biztonságosan tárolja.

Az Azure AD-hitelesítés nem használható a MariaDB adatsíkhoz való közvetlen hozzáféréshez, azonban az Azure AD hitelesítő adatait a felügyeleti sík szintjén (például az Azure Portalon) a MariaDB felügyeleti fiókok vezérlésére lehet használni.

A MariaDB rendszergazdai jelszavának frissítése:https://docs.microsoft.com/azure/mariadb/howto-create-manage-server-portal#update-admin-password

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Tekintse át az Azure Active Directory-naplókat az elavult fiókok felderítéséhez, amelyek a MariaDB felügyeleti szerepkörökkel rendelkezőket is tartalmazhatják. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságokat, a MariaDB eléréséhez használható vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférést rendszeresen, például 90 naponta felül kell vizsgálni, hogy csak a megfelelő Felhasználók férhessenek hozzá.

Ismerje meg az Azure AD-jelentéseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity Access-vélemények használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Engedélyezze a Diagnosztikai beállításokat a MariaDB és az Azure Active Directory számára, és küldje el az összes naplót egy Log Analytics-munkaterületre. Konfigurálja a kívánt riasztásokat (például a sikertelen hitelesítési kísérleteket) a Log Analytics-munkaterületen belül.

A MariaDB kiszolgálói naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/mariadb/concepts-server-logs

A MariaDB naplóinak konfigurálása és elérése:https://docs.microsoft.com/azure/mariadb/howto-configure-audit-logs-portal

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A MariaDB speciális veszélyforrások elleni védelmének engedélyezése. A MariaDB-hez való komplex veszélyforrások elleni védelem észleli az adatbázisok elérésére vagy kihasználására irányuló szokatlan és potenciálisan káros kísérleteket jelző rendellenes tevékenységeket.

Az Azure Active Directory identitásvédelmi és kockázatészlelési szolgáltatásaival konfigurálhatja az észlelt gyanús műveletekre adott automatikus válaszokat. Az Azure Sentinelen keresztül engedélyezheti az automatikus válaszokat a szervezet biztonsági válaszainak megvalósításához.

Az Advanced Threat Protection engedélyezése a MariaDB számára:https://docs.microsoft.com/azure/mariadb/howto-database-threat-protection-portal

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure AD kockázatos bejelentkezései megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutatás**: Nem alkalmazható; Az ügyfélszéf még nem támogatott a MariaDB Azure Database számára.

Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Címkék használatával segítséget nyújt az Azure Database nyomon követéséhez a MariaDB-példányok vagy a kapcsolódó erőforrások, amelyek bizalmas adatokat tárolnak vagy dolgoznak fel.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. A Privát kapcsolat, a Szolgáltatásvégpontok és/vagy a MariaDB tűzfalszabályok kombinációjával elkülönítheti és korlátozhatja a MariaDB-példányok hálózati hozzáférését.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

A Privát hivatkozás konfigurálása az Azure Database for MariaDB számára:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

A Szolgáltatásvégpontok konfigurálása az Azure Database for MariaDB számára:https://docs.microsoft.com/azure/mariadb/howto-manage-vnet-portal

A MariaDB Azure Database tűzfalszabályainak beállítása:https://docs.microsoft.com/azure/mariadb/concepts-firewall-rules

**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** Ha az Azure virtuális gépek használatával érhető el a MariaDB-példányok, használja a Private Link, MariaDB hálózati konfigurációk, hálózati biztonsági csoportok és a szolgáltatás címkék az adatok kiszivárgásának lehetőségének csökkentése érdekében.

A Microsoft kezeli a MariaDB alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket hajtott végre az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az Azure Database for MariaDB adatainak kiszivárgásának csökkentése:https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató: Az**Azure Database for MariaDB támogatja az Azure Database for MariaDB kiszolgáló ügyfélalkalmazásokkal való csatlakoztatását a Secure Sockets Layer (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket. Az Azure Portalon győződjön meg arról, hogy az "SSL-kapcsolat kényszerítése" engedélyezve van az összes MariaDB-példányhoz.

A MariaDB átvitele során a titkosítás beállítása:https://docs.microsoft.com/azure/mariadb/howto-configure-ssl

**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az adatok azonosításával, besorolásával és a veszteségmegelőzési funkciókkal még nem érhetők el a MariaDB-hez való Azure Database számára. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure AD RBAC használatával szabályozhatja a MariaDB felügyeleti sík (Azure Portal/Azure Resource Manager) Azure-adatbázishoz való hozzáférést. Adatsík-hozzáférés esetén (magában az adatbázisban) SQL-lekérdezésekkel hozhat létre felhasználókat, és konfigurálhatja a felhasználói engedélyeket.

Az RBAC konfigurálása az Azure-ban:https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

A felhasználói hozzáférés konfigurálása az SQL for MariaDB segítségével:https://docs.microsoft.com/azure/mariadb/howto-create-users

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutató**: N/A; ez az iránymutatás számítási erőforrásokra szolgál.

A Microsoft kezeli a MariaDB alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket hajtott végre az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató:** Az Azure Database for MariaDB szolgáltatás a FIPS 140-2 érvényesített kriptográfiai modult használja az inaktív adatok tárolásának titkosításához. Az adatok, beleértve a biztonsági másolatokat is, titkosítva vannak a lemezen, kivéve a lekérdezések futtatása közben létrehozott ideiglenes fájlokat. A szolgáltatás az Azure storage titkosításában található AES 256 bites titkosítást használja, és a kulcsok rendszeráltal felügyeltek. A tárolótitkosítás mindig be van kapcsolva, és nem tiltható le.

A MariaDB inaktív titkosításának ismertetése:https://docs.microsoft.com/azure/mariadb/concepts-security

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Monitor és az Azure-tevékenységnapló használatával riasztásokat hozhat létre arra az esetekre, amikor a MariaDB-hez és más kritikus vagy kapcsolódó erőforrásokhoz tartozó Azure Database éles példányai módosulnak.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutató**: Jelenleg nem áll rendelkezésre; Az Azure Security Center még nem támogatja az Azure Database for MariaDB-kiszolgáló biztonsági résének felmérését.


**Az Azure Security Center figyelése:** Igen

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

**Útmutató: A**Microsoft biztonsági réskezelést hajt végre az Azure Database for MariaDB kiszolgálót támogató mögöttes rendszereken.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás: Az**Azure Resource Graph segítségével lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure Database for MariaDB servert is) az előfizetése(i)n belül. Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.

Lekérdezések létrehozása az Azure Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása az Azure Database mariadb-kiszolgálóhoz és más kapcsolódó erőforrásokhoz, amelyek metaadatokat adnak, hogy logikusan rendszerbe rendezzék őket.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Adott esetben tagging, felügyeleti csoportok és külön előfizetések használatával rendszerezheti és nyomon követheti az Azure Database for MariaDB server ta-kiszolgálót és a kapcsolódó erőforrásokat. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

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

### <a name="611-divlimit-users-ability-to-interact-with-azure-resources-manager-via-scriptsdiv"></a>6.11: <div>Korlátozza a felhasználók at az Azure Resources Manager parancsfájlokon keresztüli együttműködésére</div>

**Útmutató:** Az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók azon képességét, hogy az Azure Resource Manager rel való interakciót a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazás konfigurálásával. Ez megakadályozhatja a magas biztonsági szintű környezetben lévő erőforrások létrehozását és módosításait, például a bizalmas adatokat tartalmazó Azure Database for MariaDB-kiszolgáló.

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

**Útmutató:** Definiáljon és valósítson meg szabványos biztonsági konfigurációkat az Azure Database mariaDB-példányokhoz az Azure Policy használatával. A "Microsoft.DBforMariaDB" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre az Azure Database MariaDB-kiszolgálókhoz való hálózati konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure Database-hez kapcsolódó beépített szabályzatdefiníciókat is használhatja a MariaDB-kiszolgálókhoz, például:

- A georedundáns biztonsági mentést engedélyezni kell a MariaDB Azure Database számára

Az elérhető Azure-szabályzataliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

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

**Útmutató:** Ha egyéni Azure-szabályzatdefiníciókat használ az Azure Database-hez a MariaDB-kiszolgálókhoz és a kapcsolódó erőforrásokhoz, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** A "Microsoft.DBforMariaDB" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** A "Microsoft.DBforMariaDB" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a rendszerkonfigurációk riasztására, naplózására és kényszerítésére. Azure-szabályzat [naplózás], [megtagadás], és [üzembe helyezés, ha nem létezik] automatikusan kényszeríti a konfigurációk az Azure Database MariaDB-példányok és a kapcsolódó erőforrások.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure-alapú virtuális gépek hez vagy az Azure App Service-en futó webes alkalmazásokhoz, amelyek az Azure Database for MariaDB-kiszolgálók elérésére szolgálnak, használja a Felügyelt szolgáltatásidentitást az Azure Key Vaultszolgáltatással együtt a MariaDB-kiszolgáló titkos kezelésének egyszerűsítéséhez és biztonságossá tétele érdekében. Győződjön meg arról, hogy a Key Vault ideiglenes törlése engedélyezve van.

Integrálás az Azure felügyelt identitásaival:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:https://docs.microsoft.com/azure/key-vault/managed-identity 



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Az Azure Database for MariaDB server jelenleg nem támogatja az Azure Active Directory-hitelesítést az adatbázisok eléréséhez.  A MariaDB-kiszolgáló Azure-adatbázisának létrehozásakor hitelesítő adatokat adhat meg egy rendszergazdai felhasználónak. Ezzel a rendszergazdával további MariaDB-felhasználókat hozhat létre.  

Az Azure-adatbázis MariaDB-kiszolgálóhoz való hozzáféréshez használt Azure-alapú virtuális gépek vagy az Azure App Service szolgáltatáson futó webalkalmazások esetében az Azure Key Vault szolgáltatással együtt a Felügyelt szolgáltatás identitását használja a MariaDB-kiszolgáló Azure Database hitelesítő adatainak tárolásához és lekéréséhez.  Győződjön meg arról, hogy a Key Vault ideiglenes törlése engedélyezve van.

Felügyelt identitások használatával automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásoknak az Azure Active Directoryban (AD). Felügyelt identitások lehetővé teszi, hogy hitelesítse az Azure AD-hitelesítést támogató szolgáltatások, beleértve a Key Vault, hitelesítő adatok nélkül a kódot. Felügyelt identitások konfigurálása: https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm. Integrálás az Azure felügyelt https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identityidentitásaival: .



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

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköz e-alapú gazdagépen (például az Azure App Service- szolgáltatást) támogató gazdagépen engedélyezve van, azonban nem fut az ügyféltartalomon.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft kártevőirtó eszköze engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például az Azure Database for MariaDB-kiszolgálón), azonban nem fut az ügyféltartalomon.

A nem számítási Azure-erőforrásokba feltöltött tartalmak, például az App Service, a Data Lake Storage, a Blob Storage, az Azure Database for MariaDB-kiszolgáló stb. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköz e-alapú gazdagépen (például Az Azure Database for MariaDB-kiszolgáló) engedélyezve van, de nem fut az ügyféltartalomon.


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** Az Azure Database for MariaDB teljes, különbözeti és tranzakciós napló biztonsági mentést készít.  A MariaDB Azure Database automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja azokat a felhasználó által konfigurált helyileg redundáns vagy georedundáns tárolóban. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza. A biztonsági mentés és visszaállítás minden üzletmenet-folytonossági stratégia alapvető részét képezi, mivel megvédi az adatokat a véletlen sérüléstől vagy törléstől.  Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Igény szerint legfeljebb 35 napig konfigurálhatja. Minden biztonsági mentés a 256 bites AES titkosítással van titkosítva.

A MariaDB biztonsági mentései:https://docs.microsoft.com/azure/mariadb/concepts-backup

A MariaDB kezdeti konfigurációjának ismertetése:https://docs.microsoft.com/azure/mariadb/tutorial-design-database-using-portal



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutató: Az**Azure Database for MariaDB automatikusan létrehozza a kiszolgáló biztonsági másolatait, és tárolja azokat a felhasználó által konfigurált helyileg redundáns vagy georedundáns tárolásban. A biztonsági másolatokkal a kiszolgáló adott időpontnak megfelelő állapotra állítható vissza.  A biztonsági mentés és visszaállítás minden üzletmenet-folytonossági stratégia alapvető részét képezi, mivel megvédi az adatokat a véletlen sérüléstől vagy törléstől.

Ha a Key Vault segítségével az ügyféloldali adattitkosítás tárolt adatok a MariaDB-kiszolgálón, biztosítja a rendszeres automatikus biztonsági mentést a kulcsokat.

A MariaDB biztonsági mentései:https://docs.microsoft.com/azure/mariadb/concepts-backup

A kulcstároló kulcsainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Az Azure Database for MariaDB,hajtson végre egy visszaállítást az eredeti kiszolgáló biztonsági mentések rendszeres tesztelése biztonsági mentések. Kétféle visszaállítás érhető el: pont-in-time visszaállítás és geo-visszaállítás. Az időponthoz való intézés visszaállítása biztonsági mentési redundancia beállítással érhető el, és az eredeti kiszolgálóval azonos régióban hoz létre új kiszolgálót. A geo-visszaállítás csak akkor érhető el, ha a kiszolgálót georedundáns tárolásra konfigurálta, és lehetővé teszi a kiszolgáló visszaállítását egy másik régióba.

A helyreállítás becsült ideje több tényezőtől függ, többek között az adatbázis méretétől, a tranzakciós napló méretétől, a hálózati sávszélességtől és az ugyanabban a régióban egyidejűleg helyreállító adatbázisok teljes számától. A helyreállítási idő általában kevesebb, mint 12 óra.

A Biztonsági mentés és visszaállítás a MariaDB Azure Database szolgáltatásában:https://docs.microsoft.com/azure/mariadb/concepts-backup#restore


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Az Azure Database for MariaDB teljes, különbözeti és tranzakciós napló biztonsági mentést készít. Ezek a biztonsági mentések lehetővé teszik, hogy a konfigurált biztonsági mentési megőrzési időszakon belül bármikor visszaállítsa a kiszolgálót a beállított biztonsági mentési megőrzési időszakon belül. Az alapértelmezett biztonsági mentési megőrzési időszak hét nap. Igény szerint legfeljebb 35 napig konfigurálhatja. Minden biztonsági mentés a 256 bites AES titkosítással van titkosítva.

A Biztonsági mentés és visszaállítás a MariaDB Azure Database szolgáltatásában:https://docs.microsoft.com/azure/mariadb/concepts-backup


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.
    

    Guidance on building your own security incident response process: https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

    

    Microsoft Security Response Center's Anatomy of an Incident: https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/

    

    Customer may also leverage NIST's Computer Security Incident Handling Guide to aid in the creation of their own incident response plan: https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final 



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás:** A Biztonsági központ minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje. 
    

    Additionally, clearly mark subscriptions (for ex. production, non-prod) using tags and create a naming system to clearly identify and categorize Azure resources, especially those processing sensitive data.  It is your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

    

    Security alerts in Azure Security Center: https://docs.microsoft.com/azure/security-center/security-center-alerts-overview

    

Címkék használatával rendszerezheti azure-erőforrásait:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez az Azure-erőforrások védelme érdekében. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.
    

    Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az Ön adataihoz jogosulatlan vagy jogosulatlan fél fért hozzá. Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.
    

    How to set the Azure Security Center Security Contact: https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details



**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Biztonsági riasztások beépítése az incidenselhárítási rendszerbe

**Útmutató:** Exportálja az Azure Security Center-riasztásokat és javaslatokat a Folyamatos exportálás funkcióval az Azure-erőforrásokkal kapcsolatos kockázatok azonosításához. A Folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális exportálását, vagy folyamatos, folyamatos módon. Használhatja az Azure Security Center adatösszekötőt a riasztások streameléséhez az Azure Sentinel.
    

    How to configure continuous export: https://docs.microsoft.com/azure/security-center/continuous-export

    

    How to stream alerts into Azure Sentinel: https://docs.microsoft.com/azure/sentinel/connect-azure-security-center



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" szolgáltatáson keresztül a biztonsági riasztásokra és javaslatokra adott válaszokat az Azure-erőforrások védelme érdekében.
    

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása:https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelést végez az Azure-erőforrásokon, és 60 napon belül biztosítja az összes kritikus biztonsági megállapítás helyreállítását

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályzatát annak érdekében, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrák, szolgáltatások és alkalmazások terén itt olvashat bővebben:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
