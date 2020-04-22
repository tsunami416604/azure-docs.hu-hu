---
title: Azure biztonsági alapkonfiguráció a Redis azure-gyorsítótárához
description: Azure biztonsági alapkonfiguráció a Redis azure-gyorsítótárához
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 172ac4e13201457f62d722236dff130a312cfdeb
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81755516"
---
# <a name="azure-security-baseline-for-azure-cache-for-redis"></a>Azure biztonsági alapkonfiguráció a Redis azure-gyorsítótárához

Az Azure Security Baseline for Azure Cache for Redis olyan javaslatokat tartalmaz, amelyek segítenek az üzembe helyezés biztonsági állapotának javításában.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutató:** Telepítse az Azure Cache redis példány egy virtuális hálózaton (VNet). A virtuális hálózat egy magánhálózat a felhőben. Ha egy Azure-gyorsítótár redis-példány van konfigurálva egy virtuális hálózat, nem nyilvánosan címezhető, és csak a virtuális gépek és alkalmazások a virtuális hálózaton belül érhető el.

Az IP-cím kezdő és záró tartományával rendelkező tűzfalszabályokat is megadhat. Ha a tűzfalszabályok konfigurálva vannak, csak a megadott IP-címtartományokból származó ügyfélkapcsolatok kapcsolódhatnak a gyorsítótárhoz.

A virtuális hálózati támogatás beállítása prémium szintű Azure-gyorsítótárhoz a Redis számára:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Az Azure Cache beállítása redis-i tűzfalszabályokhoz:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Ha a virtuális gépek üzembe helyezése ugyanabban a virtuális hálózatban, mint az Azure Cache for Redis példány, használhatja a hálózati biztonsági csoportok (NSG) az adatok kiszivárgásának kockázatának csökkentése érdekében. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Az Azure Virtual Network (VNet) központi telepítése fokozott biztonságot és elkülönítést biztosít az Azure Cache for Redis számára, valamint az alhálózatok, a hozzáférés-vezérlési szabályzatok és egyéb funkciók a hozzáférés további korlátozása érdekében. Virtuális hálózatban üzembe helyezve az Azure Cache for Redis nem nyilvánosan címezhető, és csak a virtuális gépek és a virtuális hálózaton belüli alkalmazások érhetők el.

Engedélyezze a DDoS Protection Standard ot az Azure-gyorsítótárhoz a Redis-példányokhoz társított virtuális hálózatokon, hogy megvédje az elosztott szolgáltatásmegtagadási (DDoS) támadásokat. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A virtuális hálózati támogatás beállítása prémium szintű Azure-gyorsítótárhoz a Redis számára:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Az Azure DDoS Protection Standard kezelése az Azure Portalhasználatával:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Ha a virtuális gépek üzembe helyezése ugyanabban a virtuális hálózatban, mint az Azure Cache for Redis példány, használhatja a hálózati biztonsági csoportok (NSG) az adatok kiszivárgásának kockázatának csökkentése érdekében. Engedélyezze az NSG-folyamatnaplókat, és küldjön naplókat egy Azure Storage-fiókba a forgalom naplózása érdekében. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Az NSG-folyamatnaplók engedélyezése:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:

https://docs.microsoft.com/azure/network-watcher/traffic-analytics

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Ha az Azure Cache for Redis az Azure App Service-en futó webalkalmazásokkal vagy számítási példányokkal használja, telepítse az összes erőforrást egy Azure virtuális hálózaton (VNet) belül, és biztonságos legyen egy Azure webalkalmazás-tűzfallal (WAF) a webalkalmazás-átjárón. Állítsa be úgy a WAF-ot, hogy "Megelőzési módban" fusson. A megelőzési mód blokkolja a szabályok által észlelt behatolásokat és támadásokat. A támadó "403-as jogosulatlan hozzáférés" kivételt kap, és a kapcsolat megszakad. A megelőzési mód rögzíti az ilyen támadásokat a WAF-naplókban.

Másik lehetőségként választhat egy ajánlatot az Azure Marketplace-en, amely támogatja az IDS/IPS funkciók hasznos terhelés ellenőrzése és/vagy anomáliaészlelési képességek.

Ismerje meg az Azure WAF képességeit:

https://docs.microsoft.com/azure/web-application-firewall/ag/ag-overview

Az Azure WAF telepítése:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

Azure Piactér:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** A virtuális hálózati szolgáltatás címkék segítségével határozza meg a hálózati hozzáférés-vezérlés a hálózati biztonsági csoportok (NSG) vagy az Azure Firewall. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. A szolgáltatáscímke nevének (pl. ApiManagement) megadásával a szabály megfelelő forrás- vagy célmezőjében engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatáscímke által felölelt címelőtagokat, és automatikusan frissíti a szolgáltatáscímkét a címek változásakor.

Az összetett biztonsági konfiguráció egyszerűsítéséhez alkalmazásbiztonsági csoportokat (ASG) is használhat. Az ASG-k lehetővé teszik a hálózati biztonság konfigurálását az alkalmazás szerkezetének természetes kiterjesztéseként, lehetővé téve a virtuális gépek csoportosítását és a hálózati biztonsági házirendek definiálását az adott csoportok alapján.

Virtuális hálózati szolgáltatás címkék:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

Alkalmazásbiztonsági csoportok:

https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Az Azure-gyorsítótárhoz a Redis-példányok hoz az Azure-szabályzattal szabványos biztonsági konfigurációk at definiálhat és valósíthat meg. Azure Policy aliasok a "Microsoft.Cache" és a "Microsoft.Network" névterek egyéni szabályzatok naplózására vagy érvényesítésére az Azure Cache redis példányok. A beépített házirend-definíciókat is használhatja, például:

Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni

A DDoS Protection Standard-ot engedélyezni kell

Azure Blueprints is használhatja a nagyméretű Azure-telepítések egyszerűsítésére a csomagolás kulcsfontosságú környezeti összetevők, például az Azure Resource Manager (ARM) sablonok, szerepköralapú hozzáférés-vezérlés (RBAC) és a szabályzatok, egyetlen tervezet definíciójában. Egyszerűen alkalmazhatja a tervezetet az új előfizetésekre és környezetekre, és finomíthatja a vezérlést és a felügyeletet a verziószámozással.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure Blueprint létrehozása:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutató:** Az Azure-gyorsítótárhoz társított hálózati erőforrások hoz használatával a Redis üzembe helyezéséhez logikusan rendszerezheti őket egy taxonómia.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure-gyorsítótárhoz a Redis-példányok hoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:

https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása az Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutató: A**Microsoft fenntartja az Azure-erőforrásokhoz, például a Redis-hez az időbélyegek hez használt időforrást a naplókban.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre, az Azure-eseményközpontba vagy az Azure storage-fiókba archiválási célokra. A tevékenységnaplók betekintést nyújtanak az Azure-gyorsítótárban a Redis-példányok a vezérlősík szintjén végrehajtott műveletekbe. Az Azure-tevékenységnapló adatai alapján meghatározhatja a "mit, ki és mikor" bármely írási műveletek (PUT, POST, DELETE) végzett a vezérlősík szintjén az Azure Cache redis példányok.

Az Azure-tevékenységnapló diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre, az Azure-eseményközpontba vagy az Azure storage-fiókba archiválási célokra. A tevékenységnaplók betekintést nyújtanak az Azure-gyorsítótárban a Redis-példányok a vezérlősík szintjén végrehajtott műveletekbe. Az Azure-tevékenységnapló adatai alapján meghatározhatja a "mit, ki és mikor" bármely írási műveletek (PUT, POST, DELETE) végzett a vezérlősík szintjén az Azure Cache redis példányok.

Míg a metrikák érhetők el a diagnosztikai beállítások engedélyezésével, az adatsíknaplózás még nem érhető el az Azure Cache for Redis.

Az Azure-tevékenységnapló diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitorban állítsa be a log-megőrzési időszakot az Azure-gyorsítótárhoz társított Log Analytics-munkaterületekhez a Redis-példányok hoz a szervezet megfelelőségi előírásainak megfelelően.

Vegye figyelembe, hogy az adatsíkon a naplózás még nem érhető el az Azure Cache for Redis.

A naplómegőrzési paraméterek beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Engedélyezze az Azure-tevékenységnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics-munkaterületre. Lekérdezéseket végezhet a Log Analytics szolgáltatásban a kifejezések kereséséhez, trendek azonosításához, minták elemzéséhez, és számos más elemzési adatot biztosíthat a Redis Azure-gyorsítótárához gyűjtött tevékenységnapló-adatok alapján.

Vegye figyelembe, hogy az adatsíkon a naplózás még nem érhető el az Azure Cache for Redis.

Az Azure-tevékenységnapló diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

Azure-tevékenységnaplók gyűjtése és elemzése az Azure-figyelő Naplóelemzési munkaterületén:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-collect

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Konfigurálhatja a redis-példányok azure-gyorsítótárához kapcsolódó metrikák és tevékenységnaplók alapján riasztások fogadására. Az Azure Monitor lehetővé teszi, hogy konfigurálja a riasztást, hogy küldjön egy e-mail értesítést, hívja meg a webhook, vagy egy Azure Logic-alkalmazás meghívása.

Míg a metrikák érhetők el a diagnosztikai beállítások engedélyezésével, az adatsíknaplózás még nem érhető el az Azure Cache for Redis.

Az Azure Cache for Redis riasztásainak konfigurálása:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#alerts

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutatás**: Nem alkalmazható; Az Azure Cache for Redis nem dolgozza fel és nem készít kártevők elleni kapcsolódó naplókat.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutatás**: Nem alkalmazható; Az Azure Cache for Redis nem dolgozza fel és nem hoz létre DNS-sel kapcsolatos naplókat.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információt a [Biztonságvezérlés: Identitás- és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control)című témakörben talál.*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Az adminisztratív számlák leltárának fenntartása

**Útmutatás:** Az Azure Active Directory (AD) beépített szerepköröket, amelyeket explicit módon hozzá kell rendelni, és lekérdezhető. Az Azure AD PowerShell modul használatával ad hoc lekérdezések végrehajtásával felügyeleti csoportok tagjaiként rendelkező fiókok felderítéséhez.

Címtárszerepkör beszereznie az Azure AD-ben a PowerShell használatával:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0

Címtárszerepkör tagjainak beszereznie az Azure AD-ben a PowerShell segítségével:https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3.2: Adott esetben változtassa meg az alapértelmezett jelszavakat

**Útmutató:** A redis-i Azure Cache-hez való síkhozzáférés vezérlése az Azure Active Directory (AD) szolgáltatáson keresztül történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával. 

Adatsík-hozzáférés az Azure Cache for Redis hozzáférés-kulcsok segítségével vezérelhető. Ezeket a kulcsokat a gyorsítótárhoz csatlakozó ügyfelek használják, és bármikor újragenerálhatók.

Nem ajánlott alapértelmezett jelszavakat építeni az alkalmazásba. Ehelyett tárolhatja a jelszavakat az Azure Key Vaultban, majd az Azure Active Directory segítségével lekérheti őket.

Az Azure Cache redis-hozzáférési kulcsok újragenerálása:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#settings

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Dedikált felügyeleti fiókok használata

**Útmutató**: Szabványos működési eljárások létrehozása a kijelölt adminisztratív számlák használata körül. Az Azure Security Center identitás- és hozzáférés-kezelése segítségével figyelheti a felügyeleti fiókok számát.

Emellett a dedikált felügyeleti fiókok nyomon követéséhez az Azure Security Center vagy a beépített Azure-szabályzatok ajánlásait is használhatja, például a következőket:

- Az előfizetéshez egynél több tulajdonosnak kell hozzárendelnie

- A tulajdonosi engedélyekkel rendelkező elavult fiókokat el kell távolítani az előfizetésből

- A tulajdonosi engedélyekkel rendelkező külső fiókokat el kell távolítani az előfizetésből

Az Azure Security Center használata az identitás és a hozzáférés figyelésére (előzetes verzió):https://docs.microsoft.com/azure/security-center/security-center-identity-access

Az Azure-szabályzat használata:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Egyszeri bejelentkezés (SSO) használata az Azure Active Directoryval

**Útmutató:** Az Azure Cache for Redis hozzáférési kulcsokat használ a felhasználók hitelesítéséhez, és nem támogatja az egyszeri bejelentkezést (SSO) az adatsík szintjén. Az Azure Cache for Redis vezérlősíkjához való hozzáférés rest API-n keresztül érhető el, és támogatja az SSO-t. A hitelesítéshez állítsa be a kérelmek engedélyezési fejlécét egy JSON webtokenre, amelyet az Azure Active Directoryból szerez be.

Ismerje meg az Azure Cache for Redis REST API-t:https://docs.microsoft.com/rest/api/redis/

Az SSO ismertetése az Azure AD-vel:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on


**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory (AD) többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Használja a kiemelt hozzáférésű munkaállomások (PAW) a többtényezős hitelesítés (MFA) konfigurálva, hogy jelentkezzen be, és konfigurálja az Azure-erőforrásokat.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:

https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:

https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Használja az Azure Active Directory (AD) kiemelt identitáskezelés (PIM) a naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben.

Emellett az Azure AD-kockázatészlelések használatával megtekintheti a riasztásokat és a kockázatos felhasználói viselkedésről szóló jelentéseket.

Kiemelt identitáskezelés (PIM) telepítése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Ismerje meg az Azure AD-kockázatészleléseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** Konfigurálja az elnevezett helyeket az Azure Active Directory (AD) feltételes hozzáférésben, hogy csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezze a hozzáférést.

Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directory (AD) a központi hitelesítési és engedélyezési rendszer. Az Azure AD védi az adatokat azáltal, hogy erős titkosítást használ az inaktív és az átvitel során. Az Azure AD is sók, kihegedések, és biztonságosan tárolja a felhasználói hitelesítő adatokat.

Az Azure AD-hitelesítés nem használható az Azure Cache-hez való közvetlen hozzáféréshez a Redis adatsíkjához, azonban az Azure AD hitelesítő adatait a vezérlősík szintjén (azaz az Azure Portalon) lehet használni az Azure Cache for Redis hozzáférési kulcsok vezérléséhez.


**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory (AD) naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access-vélemények használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel. 

Ismerje meg az Azure AD-jelentéseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity Access-vélemények használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik az Azure Active Directory (AD) bejelentkezési tevékenységhez, a naplózási és kockázati eseménynapló-forrásokhoz, amelyek lehetővé teszik az Azure Sentinel vagy egy külső SIEM integrálását.

Ezt a folyamatot egyszerűsítheti az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplónaplók és a bejelentkezési naplók elküldésével a Log Analytics-munkaterületre. A Log Analytics szolgáltatáson belül konfigurálhatja a kívánt naplóriasztásokat.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

A fedélzeti Azure Sentinel:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** A fiók bejelentkezési viselkedéseltérésa a vezérlősíkon, az Azure Active Directory (AD) identitásvédelem és a kockázatészlelési funkciók konfigurálásához automatikus válaszok észlelt gyanús műveletek felhasználói identitások kapcsolatos. További vizsgálat céljából adatokat is bevihet az Azure Sentinelbe.

Az Azure AD kockázatos bejelentkezései megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató**: Még nem áll rendelkezésre; Az ügyfélszéf még nem támogatott a Redis-gyorsítótárban.

Az ügyfélszéfáltal támogatott szolgáltatások listája:

https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Nem alkalmazható

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Címkék használatával segítséget nyújt a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követéséhez.

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. A Redis-példányok Azure-gyorsítótárát virtuális hálózatnak/alhálózatnak kell elválasztani, és megfelelően címkézni kell. Szükség esetén használja az Azure Cache for Redis tűzfal szabályok at, hogy csak a megadott IP-címtartományokból származó ügyfélkapcsolatok kapcsolódhassanak a gyorsítótárhoz.

További Azure-előfizetések létrehozása:

https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:

https://docs.microsoft.com/azure/governance/management-groups/create

Az Azure Cache for Redis telepítése virtuális hálózatba:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet

Az Azure Cache beállítása redis-i tűzfalszabályokhoz:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall

Címkék létrehozása és használata:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató**: Még nem áll rendelkezésre; adatazonosítási, besorolási és veszteségmegelőzési funkciók még nem érhetők el az Azure Cache for Redis számára.

A Microsoft kezeli az Azure Cache for Redis alapjául szolgáló infrastruktúrát, és szigorú vezérlőket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutató:** Az Azure Cache for Redis alapértelmezés szerint TLS-titkosítással titkosított kommunikációt igényel. A TLS 1.0-s, 1.1-es és 1.2-es verziója jelenleg támogatott. A TLS 1.0 és az 1.1 azonban az egész iparágra kiterjedő eprecáció felé halad, ezért használja a TLS 1.2-t, ha egyáltalán lehetséges. Ha az ügyfélkönyvtár vagy eszköz nem támogatja a TLS-t, akkor a titkosítatlan kapcsolatok engedélyezése az Azure Portalon vagy a felügyeleti API-kon keresztül végezhető el. Ilyen esetekben, amikor a titkosított kapcsolatok nem lehetséges, a gyorsítótár és az ügyfélalkalmazás virtuális hálózatba helyezése ajánlott lenne.

A Redis Azure-gyorsítótárának átvitele során a titkosítás megismerése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-best-practices

A virtuális hálózatok gyorsítótár-forgatókönyveiben használt szükséges portok ismertetése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-vnet#outbound-port-requirements

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az adatok azonosításával, besorolással és veszteségmegelőzési funkciókkal még nem érhetők el az Azure Cache for Redis számára. A bizalmas adatokat tartalmazó példányokat, és harmadik féltől származó megoldást valósíthat meg, ha megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure Active Directory (AAD) szerepköralapú hozzáférés-vezérlés (RBAC) használatával szabályozhatja az Azure Cache for Redis vezérlősíkhoz (azaz az Azure Portalhoz) való hozzáférést. 

Az RBAC konfigurálása az Azure-ban:

https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kezeli az Azure Cache for Redis alapjául szolgáló infrastruktúrát, és szigorú vezérlőket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:

https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutató: Az**Azure Cache for Redis tárolja az ügyféladatokat a memóriában, és bár erősen védett számos, a Microsoft által végrehajtott vezérlők, a memória alapértelmezés szerint nem titkosított. Ha a szervezet megköveteli, titkosítsa a tartalmat, mielőtt az Azure Cache for Redis tárolja.

Ha az Azure Cache for Redis "Redis data persistence" funkciót használja, az adatokat egy Azure Storage-fiókba küldi el, amely az Ön tulajdonában van és kezeli. A gyorsítótár létrehozása során az "Új Azure-gyorsítótár redishez" panelről konfigurálhatja az adatmegőrzést, és a meglévő prémium szintű gyorsítótárak Erőforrás menüjében.

Az Azure Storage-ban tárolt adatok titkosítása és visszafejtése transzparens módon történik 256 bites AES titkosítással, amely az egyik legerősebb elérhető blokktitkosítás, és fips 140-2-kompatibilis. Az Azure Storage titkosítása nem tiltható le. A tárfiók titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy saját kulcsokkal kezelheti a titkosítást.

Az adatmegőrzés konfigurálása az Azure Cache for Redis-ben:https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Az Azure Storage-fiókok titkosításának ismertetése:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

Az Azure-ügyfelek adatainak védelme:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Monitor az Azure-tevékenységnapló használatával riasztásokat hozhat létre, amikor az Azure Cache for Redis és más kritikus vagy kapcsolódó erőforrások éles példányai módosulnak.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:

https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutatás:** Kövesse az Azure Security Center ajánlásait az Azure-gyorsítótár redis-példányok és a kapcsolódó erőforrások védelmével kapcsolatban.

A Microsoft biztonsági réskezelést hajt végre az Azure Cache for Redis szolgáltatást támogató mögöttes rendszereken.

Ismerje meg az Azure Security Center rekedéseit:https://docs.microsoft.com/azure/security-center/recommendations-reference

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Megosztott

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

**Útmutató: A**Microsoft biztonsági réskezelést hajt végre az Azure Cache for Redis szolgáltatást támogató mögöttes rendszereken.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás:** Az Azure Resource Graph használatával lekérdezheti/felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés(ek)en belül).  Biztosítsa a megfelelő (olvasási) engedélyeket a bérlőben, és számba vesse az összes Azure-előfizetést, valamint az előfizetéseken belüli erőforrásokat.

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

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutató:** Szükség esetén tagging, felügyeleti csoportok és külön előfizetések használatával rendszerezheti és nyomon követheti az Azure Cache-t a Redis-példányok és a kapcsolódó erőforrások számára. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

Emellett az Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="64-maintain-an-inventory-of-approved-azure-resources-and-software-titles"></a>6.4: A jóváhagyott Azure-erőforrások és szoftvercímek leltárának karbantartása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra és az Azure egészére szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: A nem jóváhagyott Azure-erőforrások figyelése

**Útmutató: Az**Azure-szabályzat használatával korlátozásokat helyezhet el az ügyfél-előfizetés(ek)ben létrehozható erőforrások típusára vonatkozóan a következő beépített szabályzatdefiníciók használatával:

Nem engedélyezett erőforrástípusok

Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetés(ek)en belüli erőforrásokat.

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Lekérdezések létrehozása az Azure Graph segítségével:

https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

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

Nem engedélyezett erőforrástípusok

Engedélyezett erőforrástípusok

Az Azure-szabályzat konfigurálása és kezelése:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Adott erőforrástípus megtagadása az Azure-szabályzattal:

https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="610-implement-approved-application-list"></a>6.10: A jóváhagyott pályázati lista végrehajtása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resources-manager-via-scripts"></a>6.11: Korlátozza a felhasználók azon képességét, hogy parancsfájlokon keresztül kommunikáljanak az Azure Resources Managerrel

**Útmutató: Konfigurálja**az Azure feltételes hozzáférést, hogy korlátozza a felhasználók azon képességét, hogy az Azure Resource Manager (ARM) konfigurálásával "Blokk hozzáférés" a "Microsoft Azure Management" app.

A feltételes hozzáférés beállítása az ARM-hoz való hozzáférés letiltására:

https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management

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

**Útmutató:** Definiálja és valósítsa meg az Azure Cache for Redis-példányok szabványos biztonsági konfigurációit az Azure Policy használatával. Azure Policy-aliasok a "Microsoft.Cache" névtérben egyéni szabályzatok létrehozásához az Azure-gyorsítótár Redis-példányok naplózásához vagy kényszerítéséhez. Az Azure-gyorsítótárhoz a Redis-példányokhoz kapcsolódó beépített szabályzatdefiníciókat is használhatja, például:

Csak a Redis cache-hez való biztonságos kapcsolatokat kell engedélyezni

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

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat vagy Azure Resource Manager-sablonokat használ az Azure-gyorsítótárhoz a Redis-példányok és a kapcsolódó erőforrások számára, az Azure Repos használatával biztonságosan tárolhatja és kezelheti a kódot.

Kód tárolása az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** Azure-szabályzat aliasok a "Microsoft.Cache" névtérben egyéni szabályzatok riasztására, naplózására és kényszerítésére rendszerkonfigurációk létrehozásához. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** Azure-szabályzat aliasok a "Microsoft.Cache" névtérben egyéni szabályzatok riasztására, naplózására és kényszerítésére rendszerkonfigurációk létrehozásához. Azure-szabályzat [naplózás], [megtagadás], és [üzembe helyezés, ha nem létezik] automatikusan kényszeríti a redis-példányok és a kapcsolódó erőforrások Azure-gyorsítótárkonfigurációit.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure virtuális gépek vagy az Azure App Service-en futó webalkalmazások, amelyek a Redis-példányok Azure-gyorsítótárának elérésére szolgálnak, az Azure Key Vaultszolgáltatással együtt használja a felügyelt szolgáltatásidentitást a Redis titkos kezelésének egyszerűsítéséhez és biztonságossá tétele érdekében. Győződjön meg arról, hogy a Key Vault helyreállítható annektív törlése engedélyezve van.

Integrálás az Azure felügyelt identitásaival:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:

https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:

https://docs.microsoft.com/azure/key-vault/managed-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Az Azure virtuális gépek vagy az Azure App Service-en futó webalkalmazások, amelyek a Redis-példányok Azure-gyorsítótárának elérésére szolgálnak, az Azure Key Vaultszolgáltatással együtt használja a felügyelt szolgáltatásidentitást a Redis titkos kezelésének egyszerűsítéséhez és biztonságossá tétele érdekében. Győződjön meg arról, hogy a Key Vault ideiglenes törlése engedélyezve van.

Felügyelt identitások használatával biztosíthassa az Azure-szolgáltatásokat automatikusan felügyelt identitással az Azure Active Directoryban. Felügyelt identitások lehetővé teszi, hogy hitelesítse minden olyan szolgáltatás, amely támogatja az AAD-hitelesítés, beleértve az Azure Key Vault, anélkül, hogy a kód hitelesítő adatokat.

Felügyelt identitások konfigurálása:

https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm

Integrálás az Azure felügyelt identitásaival:

https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

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

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató: A**Microsoft kártevőirtó eszköz e-alapú gazdagépen engedélyezve van az Azure-szolgáltatásokat támogató gazdagépen (például a Redis-hez az Azure Cache), azonban nem fut az ügyféltartalomon.

A nem számítási Azure-erőforrásokba feltöltött tartalmak, például az App Service, a Data Lake Storage, a Blob Storage, az Azure Database for PostgreSQL stb. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

A Microsoft kártevőirtó eszköz e-alapú gazdagépen engedélyezve van az Azure-szolgáltatásokat támogató gazdagépen (például a Redis-hez az Azure Cache), azonban nem fut az ügyféltartalomon.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: Redis perzisztenciájának engedélyezése. A Redis perzisztenciája lehetővé teszi a Redis-ben tárolt adatok megőrzését. Pillanatképeket is készíthet, és biztonsági másolatot készíthet az adatokról, amelyeket hardverhiba esetén betölthet. Ez óriási előnye az alapszintű vagy standard szintű, ahol az összes adatot tárolja a memóriában, és nem lehet lehetséges adatvesztés hiba esetén, ahol a gyorsítótár csomópontok nem.

Használhatja az Azure Cache for Redis export. Exportálás lehetővé teszi, hogy exportálja a tárolt adatokat az Azure Cache for Redis a Redis kompatibilis RDB fájl(ok). Ezzel a funkcióval áthelyezheti az adatokat az egyik Azure-gyorsítótárból a Redis-példányegy másik vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gépen, amely az Azure Cache for Redis kiszolgálópéldány, és a fájl feltöltése a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

A Redis perzisztenciájának engedélyezése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Az Azure Cache használata a Redis exportálásához:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutató**: Redis perzisztenciájának engedélyezése. A Redis perzisztenciája lehetővé teszi a Redis-ben tárolt adatok megőrzését. Pillanatképeket is készíthet, és biztonsági másolatot készíthet az adatokról, amelyeket hardverhiba esetén betölthet. Ez óriási előnye az alapszintű vagy standard szintű, ahol az összes adatot tárolja a memóriában, és nem lehet lehetséges adatvesztés hiba esetén, ahol a gyorsítótár csomópontok nem.

Használhatja az Azure Cache for Redis export. Exportálás lehetővé teszi, hogy exportálja a tárolt adatokat az Azure Cache for Redis a Redis kompatibilis RDB fájl(ok). Ezzel a funkcióval áthelyezheti az adatokat az egyik Azure-gyorsítótárból a Redis-példányegy másik vagy egy másik Redis-kiszolgálóra. Az exportálási folyamat során egy ideiglenes fájl jön létre a virtuális gépen, amely az Azure Cache for Redis kiszolgálópéldány, és a fájl feltöltése a kijelölt tárfiókba. Ha az exportálási művelet sikeres vagy sikertelen állapottal fejeződik be, az ideiglenes fájl törlődik.

Ha az Azure Key Vault használatával tárolja az Azure Cache redis-példányok hitelesítő adatait, biztosítsa a kulcsok rendszeres automatikus biztonsági mentését.

A Redis perzisztenciájának engedélyezése:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-premium-persistence

Az Azure Cache használata a Redis exportálásához:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

A kulcstároló kulcsainak biztonsági mentése:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató:** Azure-gyorsítótár használata a Redis importálásához. Az importálás segítségével Redis-kompatibilis RDB fájlokat hozhat létre bármely Redis szerverről, amely bármilyen felhőben vagy környezetben fut, beleértve a Linuxon, Windowsrendszeren vagy bármely felhőszolgáltatón, például az Amazon Web Services-en és másokon futó Redis-t is. Az adatok importálásával egyszerűen hozhat létre gyorsítótárat előre kitöltött adatokkal. Az importálási folyamat során az Azure Cache for Redis betölti az RDB-fájlokat az Azure storage-ból a memóriába, majd beszúrja a kulcsokat a gyorsítótárba.

Rendszeresen tesztelje az Azure Key Vault-titkos kulcsok adat-visszaállítása.

Az Azure Cache használata a Redis importálásához:

https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-import-export-data

A Key Vault titkos kulcsainak visszaállítása:

https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultsecret?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutatás: Az**Azure Cache redis-exportálási és Redis-adatmegőrzési mentéseket a kiválasztott Azure Storage-fiókban tárolja. Az Azure Storage-ban tárolt adatok titkosítása és visszafejtése transzparens módon történik 256 bites AES titkosítással, amely az egyik legerősebb elérhető blokktitkosítás, és fips 140-2-kompatibilis. Az Azure Storage titkosítása nem tiltható le. A tárfiók titkosításához a Microsoft által felügyelt kulcsokra támaszkodhat, vagy saját kulcsokkal kezelheti a titkosítást.

Az Azure Storage-fiókok titkosításának ismertetése:https://docs.microsoft.com/azure/storage/common/storage-service-encryption

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Microsoft

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Hozzon létre egy incidens-elhárítási útmutatót a szervezet számára. Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet összes szerepkörét, valamint az incidenskezelés/-kezelés fázisait az észleléstől az incidens utáni felülvizsgálatig.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:

https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide

Útmutató a saját biztonsági incidensek reválaszolási folyamatának felépítéséhez:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

A Microsoft Security Response Center egy incidens anatómia:

https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/

Az Ügyfél a NIST számítógépes biztonsági incidenskezelési útmutatóját is felhasználhatja, hogy segítse saját eseményreagálási tervének létrehozását:

https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás:** A Biztonsági központ minden riasztáshoz súlyossági szintet rendel, hogy segítsen rangsorolni, mely riasztásokat kell először kivizsgálni. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

Ezenkívül egyértelműen jelölje meg az előfizetéseket (pl. termelés, nem prod), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosítására és kategorizálására.

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A tesztelési biztonsági reagálási eljárások

**Útmutató:** Végezzen gyakorlatokat a rendszerek incidenselhárítási képességeinek rendszeres ütemben való teszteléséhez. Azonosítsa a gyenge pontokat és hiányosságokat, és szükség szerint vizsgálja felül a tervet.

Tekintse meg a NIST kiadványát: Útmutató az informatikai tervekés képességek tesztelési, képzési és edzésprogramjaihoz:

https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Adja meg a biztonsági incidensek elérhetőségét, és állítsa be a biztonsági incidensek riasztási értesítéseit

**Útmutató: A**Microsoft a biztonsági incidensek kapcsolattartási adatait arra használja fel, hogy kapcsolatba lépjen Önnel, ha a Microsoft Security Response Center (MSRC) azt észleli, hogy az ügyfél adataihoz jogellenes vagy jogosulatlan fél fért hozzá.  Tekintse át az incidenseket a problémák megoldásának biztosítása érdekében.

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

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: A biztonsági riasztásokra adott válasz automatizálása

**Útmutató:** Az Azure Security Center munkafolyamat-automatizálási szolgáltatásával automatikusan elindíthatja a "Logic Apps" biztonsági riasztásokra és javaslatokra vonatkozó válaszokat.

A munkafolyamat-automatizálás és a logikai alkalmazások konfigurálása:

https://docs.microsoft.com/azure/security-center/workflow-automation

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="penetration-tests-and-red-team-exercises"></a>Behatolási tesztek és Red Team-gyakorlatok

*További információ: [Security Control: Penetration Tests and Red Team Exercises](https://docs.microsoft.com/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11.1: Rendszeres behatolási tesztelést végez az Azure-erőforrásokon, és 60 napon belül biztosítja az összes kritikus biztonsági megállapítás helyreállítását

**Útmutató:** Kövesse a Microsoft kötelezettségvállalási szabályzatát annak érdekében, hogy a behatolási tesztek ne sértsék meg a Microsoft irányelveit:

https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1

A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrák, szolgáltatások és alkalmazások terén itt olvashat bővebben: 

https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Megosztott

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
