---
title: Azure-biztonsági alapkonfiguráció eseményközpontokhoz
description: Azure-biztonsági alapkonfiguráció eseményközpontokhoz
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 10fc822f9f36512405dd4e3b5aba6270b53e163f
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549030"
---
# <a name="azure-security-baseline-for-event-hubs"></a>Azure-biztonsági alapkonfiguráció eseményközpontokhoz

Az Azure Security Baseline for Event Hubs olyan javaslatokat tartalmaz, amelyek segítenek javítani a központi telepítés biztonsági állapotát.

A szolgáltatás alapkonfigurációja az [Azure Security Benchmark 1.0-s verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tartalmaz arra vonatkozóan, hogy miként biztosíthatja felhőalapú megoldásait az Azure-ban az ajánlott eljárásokra vonatkozó útmutatónkkal.

További információ: [Azure Security Baselines overview](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információt a [Security Control: Network Security](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security)című témakörben talál.*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Erőforrások védelme hálózati biztonsági csoportokkal vagy az Azure tűzfallal a virtuális hálózaton

**Útmutatás**: Az eseményközpontok és a virtuális hálózati szolgáltatás végpontjainak integrációja lehetővé teszi a virtuális hálózatokhoz kötött számítási feladatok, például a virtuális hálózatokhoz kötött virtuális gépek üzenetkezelési képességeinek biztonságos elérését, és a hálózati forgalom elérési útja mindkét végén biztonságos.

Ha egyszer legalább egy virtuális hálózati alhálózati szolgáltatásvégponthoz van kötve, a megfelelő Event Hubs névtér már nem fogadja el a forgalmat bárhonnan, csak a virtuális hálózatok engedélyezett alhálózatai. A virtuális hálózati perspektíva, az Event Hubs névtér és a szolgáltatás végpontja konfigurálja egy elszigetelt hálózati alagút a virtuális hálózati alhálózat az üzenetküldő szolgáltatás. 

Létrehozhat egy privát végpontot is, amely egy olyan hálózati felület, amely az Azure Private Link szolgáltatás használatával privát és biztonságos an-kiszolgálót köt össze az Azure Event Hubs szolgáltatással. A privát végpont egy privát IP-címet használ a virtuális hálózatból, hatékonyan hozza a szolgáltatást a virtuális hálózatba. A szolgáltatáshoz irányuló összes forgalom átirányítható a privát végponton keresztül, így nincs szükség átjárókra, NAT-eszközökre, ExpressRoute- vagy VPN-kapcsolatokra vagy nyilvános IP-címekre. 

Az Azure Event Hubs névterét tűzfalak használatával is biztonságossá teheti. Az Azure Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfal támogatáshoz. Tűzfalszabályokat az Azure Portalon, az Azure Resource Manager-sablonokon, illetve az Azure CLI-n vagy az Azure PowerShellen keresztül állíthat be.

A virtuális hálózati szolgáltatás végpontjainak használata az Azure Event Hubs szolgáltatással:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

További információ: Az Azure Event Hubs integrálása az Azure Private Linkkel: https://docs.microsoft.com/azure/event-hubs/private-link-service.

Virtuális hálózatok integrációjának és tűzfalainak engedélyezése az Eseményközpontok névterén:https://docs.microsoft.com/azure/event-hubs/event-hubs-tutorial-virtual-networks-firewalls

Ip-tűzfalszabályok konfigurálása az Azure Event Hubs névterekhez:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: A virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató:** Használja az Azure Security Centert, és kövesse a hálózatvédelmi javaslatokat az Event Hubs-erőforrások azure-beli védelméhez. Ha az Azure virtuális gépek segítségével érheti el az eseményközpontokat, engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplóit, és küldjön naplókat egy tárfiókba a forgalom naplózása érdekében.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Az Azure Security Center által biztosított hálózati biztonság ismertetése:https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="13-protect-critical-web-applications"></a>1.3: Védje a kritikus webes alkalmazásokat

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Az ismert rosszindulatú IP-címekkel folytatott kommunikáció megtagadása

**Útmutató:** Engedélyezze a DDoS-védelmi szabványt az eseményközpontokhoz társított virtuális hálózatokon az elosztott szolgáltatásmegtagadási (DDoS) támadások elleni védelem érdekében. Az Azure Security Center integrált fenyegetésfelderítési szolgáltatásával megtagadhatja az ismert rosszindulatú vagy nem használt internetes IP-címekkel folytatott kommunikációt.

A DDoS-védelem konfigurálása:[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](/azure/virtual-network/manage-ddos-protection)

Az Azure Security Center integrált fenyegetésfelderítési szolgáltatással kapcsolatos további információkért:https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1.5: Hálózati csomagok és folyamatnaplók rögzítése

**Útmutató:** Ha az Azure virtuális gépek segítségével az eseményközpontok eléréséhez, engedélyezze a hálózati biztonsági csoport (NSG) folyamatnaplók at, és küldje el a naplókat egy tárfiókba a forgalom naplózása. NSG-folyamatnaplókat is küldhet a Log Analytics-munkaterületre, és a Traffic Analytics segítségével betekintést nyújthat az Azure-felhőben a forgalom áramlásába. A Traffic Analytics néhány előnye a hálózati tevékenység vizualizálása és a forró pontok azonosítása, a biztonsági fenyegetések azonosítása, a forgalomáramlási minták megértése és a hálózati helytelen konfigurációk azonosítása.

Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze a Network Watcher csomagrögzítését.

Az NSG-folyamatnaplók engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

A Traffic Analytics engedélyezése és használata:https://docs.microsoft.com/azure/network-watcher/traffic-analytics

A Network Watcher engedélyezése:https://docs.microsoft.com/azure/network-watcher/network-watcher-create

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Hálózati behatolásészlelő/behatolás-megelőző rendszerek (IDS/IPS) telepítése

**Útmutató:** Ha az Azure virtuális gépek segítségével az eseményközpontok eléréséhez, válasszon egy ajánlatot az Azure Marketplace-en, amely támogatja az IDS/IPS funkciók hasznos adatvizsgálati képességekkel. Ha a behatolásészlelés és/vagy a tartalomellenőrzésen alapuló megelőzés nem szükséges a szervezet számára, használhatja az Azure Event Hubs beépített tűzfalfunkcióját. Az Event Hubs névtérhez való hozzáférést az IP-címek korlátozott tartományára vagy egy adott IP-címre tűzfalszabályok használatával korlátozhatja.

Azure Piactér:

https://azuremarketplace.microsoft.com/marketplace/?term=Firewall

Tűzfalszabály hozzáadása az Event Hubs szolgáltatásban egy megadott IP-címhez:

 https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

**Az Azure Security Center figyelése:** Még nem érhető el

**Felelősség**: Ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1.7: A webes alkalmazások forgalmának kezelése

**Útmutatás**: Nem alkalmazható; ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: A hálózati biztonsági szabályok összetettségének és adminisztratív terhelésének minimalizálása

**Útmutató:** Nem alkalmazható, ez a javaslat az Azure App Service-en vagy számítási erőforrásokon futó webalkalmazásokra vonatkozik.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: A hálózati eszközök szabványos biztonsági konfigurációinak karbantartása

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása az Azure Event Hubs névtereihez társított hálózati erőforrásokhoz az Azure Policy használatával. A "Microsoft.EventHub" és a "Microsoft.Network" névterekben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre az Event Hubs-névterek hálózati konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure Event Hubs-hoz kapcsolódó beépített szabályzatdefiníciókat is igénybe vehet, például:

- Az Event Hubnak virtuális hálózati szolgáltatásvégpontot kell használnia.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Azure beépített szabályzat az Event Hubs névtérhez:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub



Azure Policy-minták hálózatépítéshez:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network



Azure Blueprint létrehozása:https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1.10: A forgalom konfigurációs szabályainak dokumentálása

**Útmutatás:** Címkék használata a virtuális hálózatok és egyéb, a hálózati biztonsághoz és a forgalomhoz kapcsolódó, az eseményközpontokhoz kapcsolódó erőforrásokhoz.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Automatizált eszközök használata a hálózati erőforrás-konfigurációk figyelésére és a változások észlelésére

**Útmutató:** Az Azure-tevékenységnapló segítségével figyelheti a hálózati erőforrás-konfigurációkat, és észlelheti az Azure Event Hubs-hoz kapcsolódó hálózati erőforrások változásait. Hozzon létre riasztásokat az Azure Monitoron belül, amelyek a kritikus hálózati erőforrások módosításakor aktiválódnak.

Az Azure-tevékenységnapló eseményeinek megtekintése és beolvasása:https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view

Riasztások létrehozása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ: [Security Control: Logging and Monitoring](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

**Útmutatás**: Nem alkalmazható; A Microsoft fenntartja az Azure-erőforrásokhoz, például az Azure Event Hubshoz használt időforrást a naplókban lévő időbélyegek hez.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

**Útmutató:** Az Azure Monitoron belül konfigurálja az eseményközpontokhoz kapcsolódó naplókat a tevékenységnapló és az Eseményközpont diagnosztikai beállításaiközött úgy, hogy naplókat küldjön egy Lekérdezett Naplóelemzési munkaterületre, vagy egy hosszú távú archiválási tárba.

Az Azure Event Hubs diagnosztikai beállításainak konfigurálása:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Az Azure-tevékenységnapló ismertetése:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

**Útmutató:** Diagnosztikai beállítások engedélyezése az Azure Event Hubs névtér. Az Azure Event Hubs diagnosztikai beállításainak három kategóriája van: archív naplók, operatív naplók és automatikus skálázási naplók. Engedélyezze az operatív naplók at, hogy rögzítsék az Eseményközpontok műveleteisorán zajló eseményekadatait, különösen a művelet típusát, beleértve az eseményközpont létrehozását, a használt erőforrásokat és a művelet állapotát.

Emellett engedélyezheti az Azure-tevékenységnapló diagnosztikai beállításait, és elküldheti őket egy Azure Storage-fiókba, eseményközpontba vagy egy Log Analytics-munkaterületre. A tevékenységnaplók betekintést nyújtanak az Azure Event Hubs és más erőforrásokon végrehajtott műveletekbe. A tevékenységnaplók használatával meghatározhatja az Azure Event Hubs-névtereken végzett írási műveletek (PUT, POST, DELETE) "mit, ki és mikor" című nevét.

Az Azure Event Hubs diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Az Azure-tevékenységnapló diagnosztikai beállításainak engedélyezése:https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

**Útmutató:** Az Azure Monitoron belül állítsa be a Log Analytics munkaterület-megőrzési időszakot a szervezet megfelelőségi szabályzatainak megfelelően az eseményközponttal kapcsolatos incidensek rögzítéséhez és áttekintéséhez.

A Log Analytics-munkaterületek naplómegőrzési paramétereinek beállítása:https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

**Útmutató:** Elemezze és figyelje a naplókat a rendellenes viselkedéshez, és rendszeresen tekintse át az eseményközpontokhoz kapcsolódó eredményeket. Az Azure Monitor Log Analytics szolgáltatásával áttekintheti a naplókat, és lekérdezéseket hajthat végre a naplóadatokon. Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM.
 

A Log Analytics munkaterülettel kapcsolatos további tudnivalókért:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása az Azure Monitorban:https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2.7: Riasztás engedélyezése rendellenes tevékenységre

**Útmutató:** Az Azure Monitoron belül konfigurálja az Azure Event Hubs-hoz kapcsolódó naplókat a tevékenységnaplóban, és az Event Hubs diagnosztikai beállításait, hogy naplókat küldjön egy Naplóanalytics-munkaterületre, amelyet le kell kérdezni, vagy egy hosszú távú archiválási tárfiókba. A Log Analytics-munkaterület használatával riasztásokat hozhat létre a biztonsági naplókban és eseményekben található rendellenes tevékenységekhez.

Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel. 

Ismerje meg az Azure-tevékenységnaplót:https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

Az Azure Event Hubs diagnosztikai beállításainak konfigurálása:https://docs.microsoft.com/azure/event-hubs/event-hubs-diagnostic-logs

Riasztás a Log Analytics munkaterületi naplóadatairól:https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Még nem érhető el

**Felelősség**: Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

**Útmutatás**: Nem alkalmazható; Az Event Hub nem dolgozza fel a kártevők elleni naplózást.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

**Útmutatás**: Nem alkalmazható; Az Event Hubs nem dolgozza fel és nem hoz létre DNS-alapú naplókat.

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

**Útmutató:** Az Event Hubs-hoz való hozzáférés vezérlése az Azure Active Directory (AD) segítségével történik. Az Azure AD nem rendelkezik az alapértelmezett jelszavak fogalmával.

Az Event Hubs-hoz való adatsík-hozzáférést az Azure AD-n keresztül felügyelt identitások vagy alkalmazásregisztrációk, valamint megosztott hozzáférési aláírások szabályozzák. A megosztott hozzáférésű aláírásokat az eseményközpontokhoz csatlakozó ügyfelek használják, és bármikor újragenerálhatók.

Az Event Hubs megosztott hozzáférési aláírásai ismertetése:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

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

**Útmutató: A**Microsoft Azure integrált hozzáférés-vezérlési felügyeletet biztosít az Azure Active Directory (AD) szolgáltatáson alapuló erőforrások és alkalmazások számára. Az Azure AD azure-beli Event Hubs használatával való használatának egyik fő előnye, hogy már nem kell tárolnia a hitelesítő adatait a kódban. Ehelyett kérhet egy OAuth 2.0 hozzáférési jogkivonatot a Microsoft Identity platformról. A jogkivonat ot kérő https://eventhubs.azure.net/erőforrás neve a. Az Azure AD hitelesíti az alkalmazást futtató rendszerbiztonsági tag (egy felhasználó, csoport vagy egyszerű szolgáltatás). Ha a hitelesítés sikeres, az Azure AD egy hozzáférési jogkivonatot ad vissza az alkalmazásnak, és az alkalmazás ezután használhatja a hozzáférési jogkivonatot az Azure Event Hubs-erőforrások kérésének engedélyezéséhez.

Alkalmazás hitelesítése az Azure AD-vel az Event Hubs-erőforrások eléréséhez:https://docs.microsoft.com/azure/event-hubs/authenticate-application

Az SSO ismertetése az Azure AD-vel:https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: Többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató:** Engedélyezze az Azure Active Directory többtényezős hitelesítést (MFA), és kövesse az Azure Security Center identitás- és hozzáférés-kezelési javaslatait az Event Hub-kompatibilis erőforrások védelme érdekében.

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

Az Azure Security Center identitásának és hozzáférésének figyelése:https://docs.microsoft.com/azure/security-center/security-center-identity-access

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Használjon dedikált gépeket (kiemelt hozzáférésű munkaállomásokat) minden adminisztratív feladathoz

**Útmutató:** Használja a kiemelt hozzáférésű munkaállomások (PAW) a többtényezős hitelesítés (MFA) konfigurálva, hogy jelentkezzen be, és konfigurálja az Event Hub-kompatibilis erőforrások.

Tudnivalók a kiemelt hozzáférésű munkaállomásokról:https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations

Az MFA engedélyezése az Azure-ban:https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3.7: A gyanús tevékenységek naplózása és riasztása az adminisztratív fiókokból

**Útmutató:** Használja az Azure Active Directory (AD) kiemelt identitáskezelés (PIM) a naplók és riasztások létrehozásához, ha gyanús vagy nem biztonságos tevékenység történik a környezetben. Az Azure AD-kockázatészlelések használatával megtekintheti a riasztásokat és a kockázatos felhasználói viselkedésről szóló jelentéseket. További naplózáshoz küldjön az Azure Security Center kockázatészlelési riasztásait az Azure Monitorba, és konfigurálja az egyéni riasztási/értesítési riasztásokat a műveletcsoportok használatával.

Kiemelt identitáskezelés (PIM) telepítése:https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-deployment-plan

Ismerje meg az Azure AD-kockázatészleléseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risk-events

A műveletcsoportok konfigurálása egyéni riasztáshoz és értesítéshez:https://docs.microsoft.com/azure/azure-monitor/platform/action-groups

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8: Az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató:** A feltételes hozzáféréssel elnevezett helyek használatával csak az IP-címtartományok vagy országok/régiók meghatározott logikai csoportjaiból engedélyezheti a hozzáférést.



Named-helyek konfigurálása az Azure-ban:https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="39-use-azure-active-directory"></a>3.9: Az Azure Active Directory használata

**Útmutató:** Használja az Azure Active Directory (AD) az Azure-erőforrások, például az Event Hubs központi hitelesítési és engedélyezési rendszereként. Ez lehetővé teszi a szerepköralapú hozzáférés-vezérlés (RBAC) a felügyeleti bizalmas erőforrások.

 Azure AD-példány létrehozása és konfigurálása:https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant

Ha többet szeretne tudni arról, hogy az Azure Event Hubs hogyan integrálódik az Azure Active Directoryval (AAD), olvassa el az Event Hubs-erőforrásokhoz való hozzáférés engedélyezése az Azure Active Directory használatával című témakört:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: Rendszeresen vizsgálja felül és egyeztetje össze a felhasználói hozzáférést

**Útmutató:** Az Azure Active Directory (AD) naplókat biztosít az elavult fiókok felderítéséhez. Emellett az Azure Identity Access-vélemények használatával hatékonyan kezelheti a csoporttagságokat, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen ellenőrizhető, hogy csak a megfelelő Felhasználók rendelkezhessenek folyamatos hozzáféréssel.

További, rendszeresen forgassa el az Event Hubs megosztott hozzáférési aláírások.

Ismerje meg az Azure AD-jelentéseket:https://docs.microsoft.com/azure/active-directory/reports-monitoring/

Az Azure Identity Access-vélemények használata:https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview

Az Event Hubs megosztott hozzáférési aláírásainak ismertetése:https://docs.microsoft.com/azure/event-hubs/authenticate-shared-access-signature

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3.11: Az inaktivált fiókok elérésére tett kísérletek figyelése

**Útmutató:** Hozzáféréssel rendelkezik az Azure Active Directory (AD) bejelentkezési tevékenységhez, a naplózáshoz és a kockázati eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely SIEM/Monitoring eszközzel való integrációt.

Ezt a folyamatot egyszerűsítheti az Azure AD felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplónaplók és a bejelentkezési naplók elküldésével a Log Analytics-munkaterületre. A Log Analytics szolgáltatáson belül konfigurálhatja a kívánt naplóriasztásokat.

Az Azure-tevékenységnaplók integrálása az Azure Monitorba:https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics

Hozzáférés engedélyezése az Event Hubs-erőforrásokhoz az Azure Active Directory használatával:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: Riasztás a fiók bejelentkezési viselkedésének eltéréséről

**Útmutató:** Az Azure Active Directory identitásvédelmi és kockázatészlelési szolgáltatásaival konfigurálhatja az Event Hubs-kompatibilis erőforrásokkal kapcsolatos észlelt gyanús műveletekre adott automatikus válaszokat. Az Automatikus válaszokat az Azure Sentinelen keresztül engedélyeznie kell a szervezet biztonsági válaszainak megvalósításához.

Az Azure AD kockázatos bejelentkezései megtekintése:https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins

Az identitásvédelem kockázati házirendjeinek konfigurálása és engedélyezése:https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies

Az Azure Sentinel fedélzeti szolgáltatása:https://docs.microsoft.com/azure/sentinel/quickstart-onboard

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: A Microsoft hozzáférésének biztosítása a releváns ügyféladatokhoz támogatási forgatókönyvek során

**Útmutató**: Jelenleg nem áll rendelkezésre; Az Ügyfélszéf még nem támogatott az Event Hubs esetében.

Az ügyfélszéfáltal támogatott szolgáltatások listája:https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview#supported-services-and-scenarios-in-general-availability

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Jelenleg nem érhető el

## <a name="data-protection"></a>Adatvédelem

*További információ: [Security Control: Data Protection](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Az érzékeny információk leltárának karbantartása

**Útmutató:** Az Event Hubs-hoz kapcsolódó erőforrások címkéivel segíthet a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követésében.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Érzékeny információkat tároló vagy feldolgozó rendszerek elkülönítése

**Útmutató:** Külön előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztéshez, teszteléshez és éles séghez. Az Event Hubs névtereket virtuális hálózatnak kell elválasztani, a szolgáltatásvégpontok engedélyezése és megfelelően címkézve.

Az Azure Event Hubs névterét tűzfalak használatával is biztonságossá teheti. Az Azure Event Hubs támogatja az IP-alapú hozzáférés-vezérlést a bejövő tűzfal támogatáshoz. Tűzfalszabályokat az Azure Portalon, az Azure Resource Manager-sablonokon, illetve az Azure CLI-n vagy az Azure PowerShellen keresztül állíthat be.

További Azure-előfizetések létrehozása:https://docs.microsoft.com/azure/billing/billing-create-subscription

Felügyeleti csoportok létrehozása:https://docs.microsoft.com/azure/governance/management-groups/create

IP-tűzfalszabályok konfigurálása az Azure Event Hubs névtereiben:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Hogyan hozzunk létre egy virtuális hálózat:https://docs.microsoft.com/azure/virtual-network/quick-create-portal

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: A bizalmas adatok jogosulatlan továbbításának figyelése és blokkolása

**Útmutató:** Ha virtuális gépeket használ az eseményközpontok eléréséhez, használja a virtuális hálózatokat, a szolgáltatásvégpontokat, az Event Hubs tűzfalat, a hálózati biztonsági csoportokat és a szolgáltatáscímkéket az adatok kiszivárgásának lehetőségének csökkentése érdekében.

A Microsoft kezeli az Azure Event Hubs alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

IP-tűzfalszabályok konfigurálása az Azure Event Hubs névtereiben:https://docs.microsoft.com/azure/event-hubs/event-hubs-ip-filtering

A virtuális hálózati szolgáltatás végpontjainak megismerése az Azure Event Hubs segítségével:https://docs.microsoft.com/azure/event-hubs/event-hubs-service-endpoints

Integrálja az Azure Event Hubs-ot az Azure Private Link:Integrate Azure Event Hubs with Azure Private Link:https://docs.microsoft.com/azure/event-hubs/private-link-service

A hálózati biztonsági csoportok és a szolgáltatáscímkék ismertetése:https://docs.microsoft.com/azure/virtual-network/security-overview

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Az összes bizalmas információ titkosítása szállítás közben

**Útmutatás: Az**Azure Event Hubs alapértelmezés szerint kikényszeríti a TLS-titkosítással titkosított kommunikációt. A TLS 1.0-s, 1.1-es és 1.2-es verziója jelenleg támogatott. A TLS 1.0 és az 1.1 azonban az egész iparágra kiterjedő eprecáció felé halad, ezért használja a TLS 1.2-t, ha egyáltalán lehetséges.

Az Event Hubs biztonsági szolgáltatásainak megismeréséről a Hálózati biztonság:https://docs.microsoft.com/azure/event-hubs/network-security

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató:** Az Azure Event Hubs számára még nem érhetők el az adatazonosítási, besorolási és veszteségmegelőzési funkciók. Harmadik féltől származó megoldás megvalósítása, ha a megfelelőségi célokból szükséges.

A Microsoft által kezelt mögöttes platform esetében a Microsoft minden ügyféltartalmat érzékenynek tekint, és mindent megtesz azért, hogy megvédje az ügyfelek adatvesztését és expozícióját. Annak érdekében, hogy az Azure-on belüli ügyféladatok biztonságban legyenek, a Microsoft hatékony adatvédelmi vezérlőket és képességeket vezetett be és tart fenn.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Megosztott

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6: Az Azure RBAC használatával szabályozhatja az erőforrásokhoz való hozzáférést

**Útmutató:** Az Azure Event Hubs támogatja az Azure Active Directory (AD) használatát az Event Hubs-erőforrásokra vonatkozó kérelmek engedélyezéséhez. Az Azure AD használatával szerepköralapú hozzáférés-vezérlés (RBAC) segítségével engedélyeket adhat egy rendszerbiztonsági tagnak, amely lehet felhasználó vagy egy egyszerű alkalmazásszolgáltatás.

Ismerje meg az Azure AD RBAC-ot és az Azure Event Hubs elérhető szerepköreit:https://docs.microsoft.com/azure/event-hubs/authorize-access-azure-active-directory

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: A hozzáférés-vezérlés kényszerítése gazdagépalapú adatveszteség-megelőzéssel

**Útmutatás**: Nem alkalmazható; ez az iránymutatás számítási erőforrásokra szolgál.

A Microsoft kezeli az Event Hubs mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megelőzése érdekében.

Az ügyfelek adatainak védelme az Azure-ban:https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Bizalmas információk titkosítása nyugalmi

**Útmutatás: Az**Azure Event Hubs támogatja az inaktív adatok microsoft által felügyelt vagy ügyfél által felügyelt kulcsokkal történő titkosításának lehetőségét. Ez a funkció lehetővé teszi az Azure Event Hubs-adatok titkosításához használt ügyfél által felügyelt kulcsokhoz való hozzáférés létrehozását, elforgatását, letiltását és visszavonását.

Ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs titkosításához:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Naplózza és figyelmeztesse a kritikus Azure-erőforrások változásait

**Útmutató:** Az Azure Monitor és az Azure-tevékenységnapló használatával riasztásokat hozhat létre az Azure Event Hubs és más kritikus vagy kapcsolódó erőforrások éles példányainak módosításakor.

Értesítések létrehozása az Azure-tevékenységnapló eseményeihez:https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információt a [Biztonsági ellenőrzés: Biztonsági rés kezelése című](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management)témakörben talál.*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1: Automatikus biztonsági rés-ellenőrző eszközök futtatása

**Útmutatás**: Nem alkalmazható; A Microsoft biztonsági réskezelést hajt végre az Eseményközpontokat támogató mögöttes rendszereken.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Az operációs rendszer automatikus javításkezelési megoldásának telepítése

**Útmutatás**: Nem alkalmazható; A Microsoft az Event Hubs-ot támogató mögöttes rendszereken végez javításkezelést.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5.3: Automatizált, harmadik féltől származó szoftverjavítás-kezelési megoldás telepítése

**Útmutatás**: Nem alkalmazható; számítási erőforrásokra vonatkozó teljesítményteszt.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4: Hasonlítsa össze a biztonsági rés vizsgálatait

**Útmutatás**: Nem alkalmazható; A Microsoft biztonsági réskezelést hajt végre az Eseményközpontokat támogató mögöttes rendszereken.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Kockázatminősítési eljárás használata a felfedezett biztonsági rések helyreállításának rangsorolására

**Útmutatás**: Nem alkalmazható; A Microsoft biztonsági réskezelést hajt végre az Eseményközpontokat támogató mögöttes rendszereken.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Microsoft

## <a name="inventory-and-asset-management"></a>Leltár-és eszközfelügyelet

*További információ: [Security Control: Inventory and Asset Management](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6.1: Az Azure Asset Discovery használata

**Útmutatás: Az**Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (beleértve az Azure Event Hubs névtereket is) az előfizetése(i)n belül. Győződjön meg arról, hogy rendelkezik a megfelelő (olvasási) engedélyekkel a bérlőben, és képes az összes Azure-előfizetések és az előfizetéseken belüli erőforrások számbavételét.

Lekérdezések létrehozása az Azure Graph segítségével:https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal

Az Azure-előfizetések megtekintése:https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0

Az Azure RBAC megismerése:https://docs.microsoft.com/azure/role-based-access-control/overview

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="62-maintain-asset-metadata"></a>6.2: Az eszközök metaadatainak karbantartása

**Útmutató:** Címkék alkalmazása az Azure-erőforrásokra, amelyek metaadatokat adnak, hogy logikusan rendszerezzék őket egy taxonómiába.

Címkék létrehozása és használata:https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Jogosulatlan Azure-erőforrások törlése

**Útmutatás:** Adott esetben tagging, felügyeleti csoportok és külön előfizetések használatával rendezheti és nyomon követheti az Azure Event Hubs névtereket és a kapcsolódó erőforrásokat. A készlet rendszeres egyeztetése és a jogosulatlan erőforrások időben történő törlése az előfizetésből.

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

- Nem engedélyezett erőforrástípusok

- Engedélyezett erőforrástípusok

Emellett az Azure Resource Graph használatával lekérdezheti/felderítheti az előfizetés(ek)en belüli erőforrásokat.

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

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6.11: <div>Korlátozza a felhasználók at az Azure Resource Manager parancsfájlokon keresztüli együttműködésére</div>

**Útmutató: Konfigurálja**az Azure feltételes hozzáférést, hogy korlátozza a felhasználók azOn képességét, hogy az Azure Resource Manager-rel kommunikáljanak a "Blokk hozzáférés" konfigurálásával a "Microsoft Azure Management" alkalmazáshoz.

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

**Útmutató:** Szabványos biztonsági konfigurációk definiálása és megvalósítása az Azure Event Hubs-telepítésekhez. A "Microsoft.EventHub" névtérben az Azure Policy aliasok használatával egyéni szabályzatokat hozhat létre a konfigurációk naplózásához vagy érvényesítéséhez. Az Azure Event Hubok beépített szabályzatdefinícióit is használhatja, például:

- Az Event Hub diagnosztikai naplóit engedélyezni kell

- Az Event Hubnak virtuális hálózati szolgáltatás végpontját kell használnia

Azure beépített szabályzat az Event Hubs névtérhez:https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#event-hub

Az elérhető Azure-szabályzataliasok megtekintése:https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7.2: Biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató:** Használja az Azure-szabályzat [megtagadás] és a [üzembe helyezés, ha nem létezik] a biztonságos beállítások kényszerítése az Event Hubs-kompatibilis erőforrások között. 

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

 
Az Azure policy effects-ről további információ:https://docs.microsoft.com/azure/governance/policy/concepts/effects

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Biztonságos operációsrendszer-konfigurációk karbantartása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Az Azure-erőforrások biztonságos tárolása

**Útmutató:** Ha egyéni Azure-szabályzat-definíciókat használ az Event Hubs-hoz vagy a kapcsolódó erőforrásokhoz, használja az Azure Repos-t a kód biztonságos tárolásához és kezeléséhez.

Kód tárolása az Azure DevOps-ban:https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops

Azure Repos dokumentáció:https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="76-securely-store-custom-operating-system-images"></a>7.6: Biztonságosan tárolhatja az operációs rendszer egyedi lemezképeit

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="77-deploy-system-configuration-management-tools"></a>7.7: Rendszerkonfiguráció-kezelő eszközök telepítése

**Útmutató:** Azure-szabályzat aliasok a "Microsoft.EventHub" névtérben egyéni szabályzatok riasztására, naplózására és kényszerítésére rendszerkonfigurációk létrehozásához. Emellett dolgozzon ki egy folyamatot és egy folyamatot a házirendkivételek kezeléséhez.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7.8: Rendszerkonfiguráció-kezelő eszközök telepítése operációs rendszerekhez

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7.9: Automatikus konfigurációfigyelés megvalósítása az Azure-szolgáltatásokhoz

**Útmutató:** Azure-szabályzat aliasok a "Microsoft.EventHub" névtérben egyéni szabályzatok riasztására, naplózására és kényszerítésére rendszerkonfigurációk létrehozásához. Az Azure Event Hubs-telepítések és a kapcsolódó erőforrások konfigurációinak automatikus kényszerítéséhez használja az Azure-szabályzat [naplózás], [megtagadás] és [üzembe helyezés, ha nem létezik] használatával.

Az Azure-szabályzat konfigurálása és kezelése:https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Az operációs rendszerek automatizált konfigurációs figyelésének megvalósítása

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7.11: Az Azure-titkok biztonságos kezelése

**Útmutató:** Az Azure-beli virtuális gépekhez vagy az Eseményközpontok eléréséhez használt Azure App Service-en futó webalkalmazásokhoz használja a Felügyelt szolgáltatásidentitást az Azure Key Vaulttal együtt, és egyszerűsítse és biztosítsa a megosztott hozzáférés-aláírás-kezelést az Azure Event Hubs-telepítésekhez. Győződjön meg arról, hogy a Key Vault helyreállítható annektív törlése engedélyezve van.

Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest

Ügyfél által kezelt kulcsok konfigurálása az Eseményközpontokhoz:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

Integrálás az Azure felügyelt identitásaival:https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity

Key Vault létrehozása:https://docs.microsoft.com/azure/key-vault/quick-create-portal

A Kulcstartó hitelesítése felügyelt identitással:https://docs.microsoft.com/azure/key-vault/managed-identity

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7.12: A személyazonosságok biztonságos és automatikus kezelése

**Útmutató:** Az Azure-beli virtuális gépekhez vagy az Azure App Service-en futó webes alkalmazásokhoz, amelyek az eseményközpontok elérésére szolgálnak, az Azure Key Vaulthasználatával együtt használja a Felügyelt szolgáltatás identitását az Azure Key Hubs egyszerűsítéséhez és védelméhez. Győződjön meg arról, hogy a Key Vault helyreállítható annektív törlése engedélyezve van.

Felügyelt identitások használatával automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásoknak az Azure Active Directoryban (AD). Felügyelt identitások lehetővé teszi, hogy hitelesítse minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, beleértve az Azure Key Vault, anélkül, hogy a kód hitelesítő adatokat.

Felügyelt identitás hitelesítése az Azure Active Directoryval az Event Hubs-erőforrások eléréséhez:https://docs.microsoft.com/azure/event-hubs/authenticate-managed-identity?tabs=latest 

Ügyfél által kezelt kulcsok konfigurálása az Eseményközpontokhoz:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key 

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

**Felelősség**: Nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2: A nem számítási Azure-erőforrásokba feltöltendő fájlok előzetes beszkéselése

**Útmutató:** A nem számítási Azure-erőforrásokba feltöltött tartalmak, például az Azure Event Hubs, az App Service, a Data Lake Storage, a Blob Storage, a PostgreSQL Azure Database stb. A Microsoft ezekben az esetekben nem fér hozzá az Ön adataihoz.

A Microsoft kártevőirtó eszköz e-alapú gazdagépen engedélyezve van az Azure-szolgáltatásokat támogató gazdagépen (például a Redis-hez az Azure Cache), azonban nem fut az ügyféltartalomon.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8.3: Gondoskodjon a kártevőirtó szoftverek és aláírások frissítéséről

**Útmutatás**: Nem alkalmazható; ez a javaslat számítási erőforrásokra szolgál.

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információ: [Security Control: Data Recovery](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1: Rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató:** Konfigurálja a geo-katasztrófa-helyreállítási Azure Event Hubs. Ha teljes Azure-régiók vagy adatközpontok (ha nincs rendelkezésre állási zónák) állásidőt tapasztal, fontos, hogy az adatfeldolgozás továbbra is egy másik régióban vagy adatközpontban működjön. Mint ilyen, a geo-katasztrófa-helyreállítási és georeplikáció fontos funkciók minden vállalat számára. Az Azure Event Hubs támogatja a földrajzi vész-helyreállítási és a georeplikáció, a névtér szintjén. 

Ismerje meg az Azure Event Hubs geokatasztrófa-utáni helyreállítását:https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones

**Az Azure Security Center figyelése:** Jelenleg nem érhető el

**Felelősség**: Ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: Teljes rendszerbiztonsági mentés végrehajtása és biztonsági mentés bármely ügyfél által kezelt kulcs ról

**Útmutatás: Az**Azure Event Hubs az Azure Storage Service Encryption (Azure SSE) segítségével biztosítja az inaktív adatok titkosítását. Az Event Hubs az Azure Storage-ra támaszkodik az adatok tárolásához, és alapértelmezés szerint az Azure Storage-szal tárolt összes adat microsoftáltal kezelt kulccsal van titkosítva. Ha az Azure Key Vault segítségével tárolja az ügyfél által felügyelt kulcsokat, biztosítsa a kulcsok rendszeres automatikus biztonsági mentését.

A key vault-titkos kulcsok rendszeres automatikus biztonsági mentése a következő PowerShell paranccsal biztosítható: Backup-AzKeyVaultSecret

Az ügyfél által felügyelt kulcsok konfigurálása az Azure Event Hubs-adatok inaktív titkosításához:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

A Key Vault titkainak biztonsági mentése:https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3: Az összes biztonsági mentés ellenőrzése, beleértve az ügyfél által kezelt kulcsokat is

**Útmutató**: Az ügyfél által felügyelt kulcsok biztonsági másolatot készülő kulcsainak tesztelése.

 

A kulcstároló kulcsainak visszaállítása az Azure-ban:https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: A biztonsági mentések és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató:** Engedélyezze a helyreállítható törlést a Key Vaultban a kulcsok véletlen vagy rosszindulatú törlés elleni védelme érdekében. Az Azure Event Hubs megköveteli az ügyfél által felügyelt kulcsokat, hogy a soft delete és a Ne ürítse konfigurálása.

Konfigurálja a helyreállítható törlést az Event Hubs-adatok rögzítésére használt Azure Storage-fiókhoz. Vegye figyelembe, hogy ez a funkció még nem támogatott az Azure Data Lake Storage Gen 2 esetében.

A soft-delete engedélyezése a Key Vaultban:https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

Kulcstartó beállítása kulcsokkal:https://docs.microsoft.com/azure/event-hubs/configure-customer-managed-key

Az Azure Storage-blobok ideiglenes törlése:https://docs.microsoft.com//azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ: [Security Control: Incident Response](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Incidenselhárítási útmutató létrehozása

**Útmutató:** Győződjön meg arról, hogy vannak olyan írásos eseményreagálási tervek, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének/kezelésének fázisait.

Munkafolyamat-automatizálások konfigurálása az Azure Security Centerben:https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide



**Az Azure Security Center figyelése:** Nem alkalmazható

**Felelősség**: Ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Incidenspontozási és rangsorolási eljárás létrehozása

**Útmutatás: A**Security Center súlyossági szintet rendel a riasztásokhoz, hogy segítsen rangsorolni az egyes riasztások hozadékát, így ha egy erőforrás biztonsága veszélybe kerül, azonnal hozzáérhet. A súlyosság attól függ, hogy a Security Center mennyire magabiztos a keresésben, vagy a riasztás kiadásához használt analitikus, valamint a riasztáshoz vezető tevékenység mögött rosszindulatú szándék megbízhatósági szintje.

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

### <a name="103-test-security-response-procedures"></a>10.3: A biztonsági reagálási eljárások tesztelése

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

**Útmutató:** Kérjük, kövesse a Microsoft kötelezettségvállalási szabályzatát, hogy https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1a behatolási tesztek ne sértsék meg a Microsoft irányelveit: .
A Microsoft stratégiájáról és a Red Teaming és az élő webhely behatolási tesztelésének végrehajtásáról a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos további információkat itt talál:https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Az Azure Security Center figyelése:** Igen

**Felelősség**: Ügyfél

## <a name="next-steps"></a>További lépések

- Tekintse meg az [Azure biztonsági referenciaértékét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alapkonfigurációiról](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
