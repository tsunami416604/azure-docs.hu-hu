---
title: Azure Databricks Azure biztonsági alapterve
description: Azure Databricks Azure biztonsági alapterve
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 05/04/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 66043ae4f569e4151be829ca361232accb2f0ebd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219379"
---
# <a name="azure-security-baseline-for-azure-databricks"></a>Azure Databricks Azure biztonsági alapterve

Az Azure Databricks Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: az [Azure biztonsági alaptervek áttekintése](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview).

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](https://docs.microsoft.com/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: az erőforrások védelme hálózati biztonsági csoportokkal vagy Azure Firewall a Virtual Network

**Útmutató**: Azure Databricks üzembe helyezése saját Azure-beli virtuális hálózaton (VNet). Az Azure Databricks alapértelmezett központi telepítése egy teljes körűen felügyelt szolgáltatás az Azure-ban: minden adatsík-erőforrás, beleértve a VNet, amelyekhez az összes fürt társítva lesz, egy zárolt erőforráscsoporthoz van telepítve. Ha azonban hálózati testreszabásra van szüksége, a saját virtuális hálózatban (VNet injekcióban) üzembe helyezheti Azure Databricks adatsík-erőforrásokat, így egyéni hálózati konfigurációk valósíthatók meg. Saját hálózati biztonsági csoportot (NSG) is alkalmazhat egyéni szabályokkal a kimenő forgalomra vonatkozó korlátozásokra.

Emellett a NSG szabályokat is konfigurálhatja a kimenő forgalom korlátozásának megadásához azon az alhálózaton, amelyen a Azure Databricks példánya telepítve van. Azure Firewall konfigurálható VNET befecskendezett munkaterületekhez.

* [Azure Databricks üzembe helyezése a saját Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [A NSG kezelése](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: Azure Databricks üzembe helyezése saját Azure-beli virtuális hálózaton (VNet). A hálózati biztonsági csoport (NSG) nem jön létre automatikusan. Csak alapértelmezett Azure-szabályokkal rendelkező alapkonfiguráció-NSG kell létrehoznia. Munkaterület telepítésekor a Databricks által igényelt szabályok hozzáadódnak. Egy üres NSG is elindítható, a megfelelő szabályok pedig automatikusan hozzáadódnak. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalmi auditok számára. A flow-naplókat Log Analytics munkaterületre is elküldheti, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

* [Azure Databricks üzembe helyezése a saját Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [A Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártékony IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: a Azure Databricks-példányokhoz társított Azure-beli virtuális hálózatok Azure DDoS Protection szabványának engedélyezése az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt nyilvános IP-címekkel.

* [Azure DDoS Protection standard kezelése a Azure Portal használatával](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Az Azure hálózati réteg veszélyforrások elleni védelmének megismerése Azure Security Center](https://docs.microsoft.com/azure/security-center/threat-protection#threat-protection-for-azure-network-layer-)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="15-record-network-packets-and-flow-logs"></a>1,5: hálózati csomagok és adatforgalmi naplók rögzítése

**Útmutató**: Azure Databricks üzembe helyezése saját Azure-beli virtuális hálózaton (VNet). A hálózati biztonsági csoport (NSG) nem jön létre automatikusan. Csak alapértelmezett Azure-szabályokkal rendelkező alapkonfiguráció-NSG kell létrehoznia. Munkaterület telepítésekor a Databricks által igényelt szabályok hozzáadódnak. Engedélyezze a NSG folyamat naplóit, és küldje el a naplókat egy Storage-fiókba a forgalom naplózása érdekében. A flow-naplókat Log Analytics munkaterületre is elküldheti, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

* [Azure Databricks üzembe helyezése a saját Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [A Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati alapú behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: IDENTITÁSSZOLGÁLTATÓ/IP-kompatibilis hálózati virtuális berendezés (NVA) implementálása az Azure Marketplace-ről egy olyan virtuális hálózattal integrált munkaterület létrehozásához, amelyben az összes Azure Databricks-fürt egyetlen IP-cím kimenő címmel rendelkezik. Az egyetlen IP-cím használható további biztonsági rétegként más Azure-szolgáltatásokkal és-alkalmazásokkal, amelyek adott IP-címek alapján engedélyezik a hozzáférést. A bejövő és a kimenő forgalom kezeléséhez Azure-tűzfalat vagy más külső féltől származó eszközöket (például NVA) is használhat.

Ha a betörési észlelés és/vagy a tartalom-ellenőrzésen alapuló megelőzés nem követelmény, akkor az Azure Firewall a veszélyforrások felderítésére használható. Azure Firewall fenyegetés intelligencián alapuló szűréssel riasztást kaphat, és megtagadhatja az ismert kártékony IP-címek és tartományok felé irányuló forgalmat. Az IP-címek és tartományok forrása a Microsoft Threat Intelligence-hírcsatorna.

* [Egyetlen nyilvános IP-cím hozzárendelése virtuális hálózattal injektált munkaterületekhez az Azure Firewall használatával](https://docs.microsoft.com/azure/databricks/kb/cloud/azure-vnet-single-ip)

* [NVA létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal)

* [Azure Piactér](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat Azure App Service vagy számítási erőforrásokon futó webalkalmazásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: a szolgáltatás-címkék használatával definiálhatja a hálózati biztonsági csoportokhoz tartozó hálózati hozzáférés-vezérléseket, amelyek az Azure Databricks-példányhoz társított alhálózatokhoz vannak társítva. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával. A szolgáltatás címkéi nem támogatottak a nem befecskendezett VNET-munkaterületek esetében.

* [A szolgáltatáshoz tartozó címkék ismertetése](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati biztonsági konfigurációk definiálása és implementálása a Azure Databricks-példányokhoz Azure Policy használatával. A "Microsoft. Databricks" névtérben Azure Policy aliasokat használhat egyéni szabályzat-definíciók definiálásához. A házirendek nem lesznek érvényesek a felügyelt erőforráscsoporthoz tartozó erőforrásokra.

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését, például az Azure erőforrás-kezelési sablonokat, a szerepköralapú hozzáférés-vezérlést (RBAC) és a szabályzatokat egyetlen terv definíciójában. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy aliasok és definíciós struktúra ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure)

* [Azure Blueprint létrehozása](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: címkék használata a NSG és a Azure Databricks-példányhoz társított hálózati biztonsággal és adatforgalommal kapcsolatos egyéb erőforrásokhoz. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és a Azure Databricks-példányokhoz kapcsolódó hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Riasztások létrehozása a Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait, azonban lehetősége van a számítási erőforrások időszinkronizálási beállításainak kezelésére. Mivel Azure Databricks egy Pásti szolgáltatás, nem rendelkezik közvetlen hozzáféréssel a virtuális gépekhez egy Azure Databricks-fürtben, és nem állíthatja be az időszinkronizálási beállításokat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: naplók beolvasása Azure monitor segítségével a Azure Databricks által generált biztonsági adatokat összesítve. Azure Monitoron belül lekérdezheti a Log Analytics munkaterületet, amely a Databricks és a diagnosztikai naplók fogadására van konfigurálva. Az Azure Storage-fiókokat használhatja hosszú távú/archiválási naplók tárolására, illetve az olyan esemény-hubokra, amelyek más rendszerekre exportálják az adatexportálást. Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó biztonsági incidensek és események felügyeletét (SIEM).

Megjegyzés: Azure Databricks a diagnosztikai naplókhoz a prémium szintű Azure Databricks terv szükséges

* [Diagnosztikai beállítások konfigurálása](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings#create-diagnostic-settings-in-azure-portal)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

* [Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: az Azure-műveletnapló diagnosztikai beállításainak engedélyezése és a naplók elküldése egy log Analytics-munkaterületre, Azure Event hub vagy Azure Storage-fiókba az archívumhoz. Az Azure-tevékenység naplójának adatai alapján meghatározhatja az Azure-erőforrások vezérlési síkja szintjén végrehajtott írási műveletek (PUT, közzététel, törlés) esetében a "mi, ki és mikor" lehetőséget.

A naplózási naplózáshoz a Azure Databricks átfogó, teljes körű diagnosztikai naplókat biztosít Azure Databricks felhasználók által végzett tevékenységekhez, így a vállalata megfigyelheti a részletes Azure Databricks használati mintákat.

Megjegyzés: Azure Databricks a diagnosztikai naplókhoz a prémium szintű Azure Databricks terv szükséges

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [A Azure Databricks diagnosztikai beállításainak engedélyezése](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: a Azure Databricks teljes körű diagnosztikai naplókat biztosít Azure Databricks felhasználók által végzett tevékenységekhez, így a vállalata megfigyelheti a részletes Azure Databricks használati mintákat.

Megjegyzés: Azure Databricks a diagnosztikai naplókhoz a prémium szintű Azure Databricks terv szükséges. Az operációs rendszer biztonsági naplózása nem érhető el.

* [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings-legacy)

* [A Azure Databricks diagnosztikai beállításainak engedélyezése](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: a Azure Databricks diagnosztikai beállításainak engedélyezése. Ha úgy dönt, hogy egy Log Analytics munkaterületen tárolja a naplókat, akkor a szervezet megfelelőségi előírásai szerint állítsa be a Log Analytics munkaterület megőrzési időszakát. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz. A biztonsággal kapcsolatos tevékenységek nyomon követése a Databricks-naplókban történik.

Megjegyzés: Azure Databricks a diagnosztikai naplókhoz a prémium szintű Azure Databricks terv szükséges

* [Diagnosztikai beállítások engedélyezése a Azure Databricksban](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a Azure Databricks diagnosztikai beállításainak engedélyezése és naplók elküldése egy log Analytics-munkaterületre. A Log Analytics munkaterületen elemezheti és figyelheti a rendellenes viselkedést okozó Azure Databricks naplókat, és rendszeresen áttekintheti az eredményeket.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

Megjegyzés: Azure Databricks a diagnosztikai naplókhoz a prémium szintű Azure Databricks terv szükséges

* [Diagnosztikai beállítások engedélyezése a Azure Databricksban](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs)

* [Log Analytics munkaterületre eljuttatott naplók lekérdezése Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#analyze-diagnostic-logs)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

**Útmutató**: a Azure Databricks-példány diagnosztikai beállításainak konfigurálása és naplók küldése log Analytics munkaterületre. A Log Analytics munkaterületen beállíthatja, hogy a riasztások az előre definiált feltételek betartása esetén kerüljenek érvénybe.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek.

Megjegyzés: Azure Databricks a diagnosztikai naplókhoz a prémium szintű Azure Databricks terv szükséges

* [Azure Databricks naplók küldése Log Analytics munkaterületre](https://docs.microsoft.com/azure/databricks/administration-guide/account-settings/azure-diagnostic-logs#configure-diagnostic-log-delivery)

* [Riasztások konfigurálása Log Analytics munkaterületen](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; A Azure Databricks nem dolgozza fel és nem hozza létre a felhasználók számára elérhető DNS-naplókat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; Ez az útmutató számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](https://docs.microsoft.com/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: Azure Databricks scim API-k segítségével kezelheti Azure Databricks munkaterületen lévő felhasználókat, és rendszergazdai jogosultságokat biztosíthat a kijelölt felhasználók számára. Ezeket a Azure Databricks felhasználói felületén is kezelheti.

* [A SCIM API-k használata](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Felhasználók hozzáadása és eltávolítása Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Databricks Azure Active Directory (ad) használatával biztosít hozzáférést a Azure Portalhoz és a Azure Databricks felügyeleti konzolhoz. Az Azure AD nem rendelkezik az alapértelmezett jelszavakkal, de az egyéni vagy külső alkalmazások alapértelmezett jelszavainak módosítása vagy nem engedélyezett.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: Azure Databricks scim API-k használatával adhat hozzá felhasználókat rendszergazdai jogosultságokkal egy Azure Databricks. Ezeket a Azure Databricks felhasználói felületén is kezelheti.

* [A SCIM API-k használata](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Felhasználók hozzáadása és eltávolítása Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3,4: egyszeri bejelentkezés (SSO) használata Azure Active Directory

**Útmutató**: a Azure Databricks automatikusan beállítható Azure Active Directory egyszeri bejelentkezés használatára a felhasználók hitelesítéséhez. A szervezeten kívüli felhasználóknak be kell fejezniük a Meghívási folyamatot, és hozzá kell adni a Active Directory bérlőhöz, mielőtt be tudnak jelentkezni a Azure Databricksba az egyszeri bejelentkezés használatával. A SCIM alkalmazásával automatizálhatja az üzembe helyezést és a kiépített felhasználókat a munkaterületekről.

* [A SCIM API-k használata](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Az egyszeri bejelentkezés ismertetése a Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Felhasználók meghívása az Azure AD-bérlőbe](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: többtényezős hitelesítés használata az összes Azure Active Directory-alapú hozzáféréshez.

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

### <a name="37-log-and-alert-on-suspicious-activity-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik. A Azure Security Center használatával figyelheti az identitás-és hozzáférési tevékenységeket. Emellett kihasználhatja a Azure Databricks diagnosztikai naplókat is.

* [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel engedélyezheti, hogy a hozzáférés csak az IP-címtartományok vagy országok/régiók adott logikai csoportjaihoz legyen elérhető.

* [Elnevezett helyszínek konfigurálása az Azure-ban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: a Azure Databricks automatikusan beállítható Azure Active Directory egyszeri bejelentkezés használatára a felhasználók hitelesítéséhez. A szervezeten kívüli felhasználóknak be kell fejezniük a Meghívási folyamatot, és hozzá kell adni a Active Directory bérlőhöz, mielőtt be tudnak jelentkezni a Azure Databricksba az egyszeri bejelentkezés használatával.

* [Az egyszeri bejelentkezés ismertetése a Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/single-sign-on/)

* [Felhasználók meghívása az Azure AD-bérlőbe](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-external-users)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: az Azure ad olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett az Azure Identity Access Reviews használatával hatékonyan kezelheti a csoporttagságok kezelését, a vállalati alkalmazásokhoz való hozzáférést és a szerepkör-hozzárendeléseket. A felhasználói hozzáférés rendszeresen felülvizsgálható, hogy csak a megfelelő felhasználók férhessenek hozzá. A felhasználói hozzáférés áttekintéséhez SCIM API-kat és Azure Databricks diagnosztikai naplókat is alkalmazhat.

Emellett rendszeresen áttekintheti és kezelheti a felhasználói hozzáférést a Azure Databricks felügyeleti konzolon.

* [Azure AD-jelentéskészítés](https://docs.microsoft.com/azure/active-directory/reports-monitoring/)

* [Az Azure Identity hozzáférési felülvizsgálatok használata](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Felhasználói hozzáférés kezelése a Azure Databricks felügyeleti konzolon](https://docs.microsoft.com/azure/databricks/administration-guide/users-groups/users)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-accounts"></a>3,11: az inaktivált fiókok elérésére irányuló kísérletek figyelése

**Útmutató**: hozzáférhet az Azure ad bejelentkezési tevékenységeihez, a naplózási és a kockázati Eseménynapló-forrásokhoz, amelyek lehetővé teszik bármely Siem/monitoring eszköz integrálását. Emellett Azure Databricks diagnosztikai naplókat is használhat a felhasználói hozzáférési tevékenységek áttekintéséhez.

Ezt a folyamatot leegyszerűsítheti Azure Active Directory felhasználói fiókok diagnosztikai beállításainak létrehozásával, valamint a naplók és a bejelentkezési naplók Log Analytics munkaterületre való elküldésével. Log Analytics munkaterületen belül konfigurálhatja a kívánt riasztásokat.

* [A SCIM API-k használata](https://docs.microsoft.com/azure/databricks/dev-tools/api/latest/scim/)

* [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="312-alert-on-account-login-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure ad kockázatkezelési és identitás-védelmi funkcióival konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. További vizsgálat céljából az Azure Sentinelbe is betöltheti az adatmennyiséget. Emellett Azure Databricks diagnosztikai naplókat is használhat a felhasználói hozzáférési tevékenységek áttekintéséhez.

* [Az Azure AD kockázatos bejelentkezések megtekintése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: Ha a támogatási jegyek nyitva vannak, az ügyfélszolgálat és a támogatási szakemberek beleegyeznek a releváns ügyféladatok eléréséhez.

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-protection)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a bizalmas adatokat feldolgozó Azure Databricks-példányok nyomon követésében segítő címkék használata.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Azure Databricks alapértelmezett központi telepítése egy teljes körűen felügyelt szolgáltatás, amelyet a saját virtuális hálózatán belül telepítenek. Ha a hálózat testreszabására van szükség, a saját virtuális hálózatában Azure Databricks is telepíthet. Az ajánlott eljárás az, ha külön Azure Databricks munkaterületeket hoz létre különböző üzleti csapatokhoz vagy részlegekhez.

* [Azure Databricks üzembe helyezése a saját Virtual Network](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Virtual Network létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és blokkolása.

**Útmutató**: a Databricks kiszűrése védelmi architektúrájának követésével csökkentheti az adatkiszűrése lehetőségét.

* [Az kiszűrése védelme Azure Databricks](https://databricks.com/blog/2020/03/27/data-exfiltration-protection-with-azure-databricks.html)

A Microsoft kezeli a Azure Databricks alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Microsoft a Azure Databricks-példánynak a Azure Portal vagy Azure Databricks konzolon keresztüli felügyeletéhez alapértelmezés szerint a TLS 1,2-et fogja egyeztetni. A Databricks Web App támogatja a TLS 1,3-et.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: jelenleg nem érhető el; az adatazonosítási, besorolási és veszteség-megelőzési funkciók jelenleg nem érhetők el Azure Databricks. Címkézse Azure Databricks példányokat és a kapcsolódó erőforrásokat, amelyek a bizalmas adatokat feldolgozzák, és ha a megfelelőség szempontjából szükséges, harmadik féltől származó megoldást is megvalósítanak.

A Databricks platform csak számítási kapacitás, és az összes adatok tárolása más Azure-adatszolgáltatásokban történik. A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4,6: az erőforrásokhoz való hozzáférés szabályozása az Azure RBAC

**Útmutató**: Azure Databricks a hozzáférés-vezérlési listák (ACL-ek) segítségével konfigurálhatja az adattáblák, fürtök, készletek, feladatok és munkaterület-objektumok, például jegyzetfüzetek, kísérletek és mappák elérésére vonatkozó engedélyeket. Az összes rendszergazda felhasználó kezelheti a hozzáférés-vezérlési listát, mint a hozzáférés-vezérlési listák kezeléséhez delegált engedélyeket kapott felhasználók. Az Azure RBAC-t használhatja a Azure Databricks munkaterület engedélyeinek beállításához.

Megjegyzés: a tábla, a fürt, a készlet, a feladatok és a munkaterület-hozzáférés-vezérlés csak a prémium szintű Azure Databricks csomagban érhető el.

* [A Azure Databricks hozzáférés-vezérlésének kezelése](https://docs.microsoft.com/azure/databricks/administration-guide/access-control/)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

A Microsoft kezeli a Azure Databricks alapjául szolgáló infrastruktúrát, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy kihatásának megelőzésére.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: egy Azure Databricks munkaterület egy Azure Databricks felügyelt virtuális hálózaton és egy ügyfél által felügyelt virtuális hálózaton üzembe helyezett adatsíkon található felügyeleti síkon áll. A vezérlő síkja az összes felhasználó jegyzetfüzetét és a hozzá tartozó jegyzetfüzetet egy adatbázisban tárolja. Alapértelmezés szerint az összes jegyzetfüzet és eredmény titkosítva marad a többi titkosítási kulccsal.

A Databricks File System (DBFS) egy elosztott fájlrendszer, amely egy Azure Databricks munkaterületre van csatlakoztatva, és Azure Databricks-fürtökön érhető el. A DBFS a Azure Databricks munkaterület felügyelt erőforráscsoporthoz tartozó Storage-fiókként van megvalósítva. Alapértelmezés szerint a Storage-fiók a Microsoft által felügyelt kulcsokkal van titkosítva. A rendszer az Ön tulajdonában lévő Azure-adatszolgáltatásokban tárolja az adatait, és Ön dönthet úgy, hogy titkosítja. A DBFS használata éles adattárolásra nem ajánlott

Megjegyzés: ezek a szolgáltatások az összes Azure Databricks-előfizetéshez nem érhetők el. Hozzáférés kéréséhez forduljon a Microsoft vagy a Databricks-fiók képviselőjéhez.

* [Az ügyfél által felügyelt kulcsok engedélyezése Azure Databricks notebookokhoz](https://docs.microsoft.com/azure/databricks/security/customer-managed-key-notebook)

* [Az ügyfél által felügyelt kulcsok engedélyezése Azure Databricks fájlrendszerhez](https://docs.microsoft.com/azure/databricks/security/customer-managed-keys-dbfs)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amikor a módosítások a kritikus Azure Databricks munkaterületekre változnak.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: harmadik féltől származó sebezhetőségi felügyeleti megoldás implementálása.

Ha a biztonsági rések kezelése platform előfizetéssel rendelkezik, Azure Databricks inicializálási parancsfájlokkal telepítheti a sebezhetőség-felmérési ügynököket a Azure Databricks fürtcsomópontokon, és felügyelheti a csomópontokat a megfelelő portálon. Vegye figyelembe, hogy minden harmadik féltől származó megoldás eltérően működik.

* [A Rapid7-ügynök manuális telepítése](https://insightagent.help.rapid7.com/docs/install)

* [A Qualys-ügynök manuális telepítése](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks inicializálási parancsfájlok](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: a Microsoft fenntartja a Azure Databricks fürtcsomópont alaplemezképeit, azonban Ön felelős azért, hogy a fürtcsomópontok továbbra is legyenek javítva. Egy meglévő futó fürt karbantartási frissítésének hozzáadásához újra kell indítania a fürtöt.

* [A Azure Databricks futtatókörnyezet-karbantartási frissítéseinek ismertetése](https://docs.microsoft.com/azure/databricks/release-notes/runtime/maintenance-updates)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="53-deploy-automated-third-party-software-patch-management-solution"></a>5,3: automatikus, külső gyártótól származó szoftverfrissítési megoldás üzembe helyezése

**Útmutató**: a Microsoft karbantartja a Azure Databricks fürtcsomópont alaplemezképeit, azonban Ön felelős azért, hogy az Ön által telepített külső alkalmazások is megmaradjanak.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: külső gyártótól származó biztonsági rések kezelésére szolgáló megoldás implementálása, amely lehetővé teszi a sebezhetőségi vizsgálatok időbeli összevetését. Ha a biztonsági rések kezelésére vonatkozó előfizetéssel rendelkezik, használhatja a gyártó portálját a biztonsági rések vizsgálatának megtekintésére és összehasonlítására. Vegye figyelembe, hogy minden harmadik féltől származó megoldás eltérően működik.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését.

**Útmutató**: általános kockázati pontozási programot (például gyakori sebezhetőségi pontozási rendszer) vagy a harmadik féltől származó ellenőrzési eszköz által biztosított alapértelmezett kockázati minősítéseket használjon.

**Azure Security Center figyelés**: N/A

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-azure-asset-discovery"></a>6,1: az Azure Asset Discovery használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti vagy felderítheti az összes erőforrást (például számítási, tárolási, hálózati, portok és protokollok stb.) az előfizetés (ok) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyek vannak a bérlőben, és enumerálja az összes Azure-előfizetést, valamint az előfizetésekben található erőforrásokat.

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

### <a name="64-maintain-inventory-of-approved-azure-resources-and-software-titles"></a>6,4: a jóváhagyott Azure-erőforrások és-szoftverek leltárának karbantartása.

**Útmutató**: jóváhagyott Azure-erőforrások és jóváhagyott szoftverek definiálása számítási erőforrásokhoz.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: az Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph segítségével lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Azure Databricks egy Pásti szolgáltatás, az ügyfeleknek nincs közvetlen hozzáférésük egy Azure Databricks-fürtben lévő virtuális gépekhez.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti az összes erőforrást (például a számítási, tárolási, hálózati, portok és protokollok stb.), beleértve a Azure Databricks példányokat az előfizetésében. Távolítsa el a felderített nem jóváhagyott Azure-erőforrásokat. Azure Databricks fürtcsomópontok esetében hozzon létre egy harmadik féltől származó megoldást a nem jóváhagyott szoftverek eltávolítására vagy riasztására.

* [Lekérdezések létrehozása az Azure Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Azure Databricks egy Pásti szolgáltatás, nem rendelkezik közvetlen hozzáféréssel a virtuális gépekhez egy Azure Databricks-fürtben.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="610-implement-approved-application-list"></a>6,10: jóváhagyott alkalmazások listájának implementálása

**Útmutató**: nem alkalmazható; Azure Databricks egy Pásti szolgáltatás, nem rendelkezik közvetlen hozzáféréssel a virtuális gépekhez egy Azure Databricks-fürtben.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="611-divlimit-users-ability-to-interact-with-azure-resource-manager-via-scriptsdiv"></a>6,11: <div>A felhasználók Azure Resource Manager interakciójának korlátozása parancsfájlok használatával</div>

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez nem alkalmazható Azure Databricksre, mert a fürt felhasználóinak (nem rendszergazdáknak) nincs szükségük az egyes csomópontokhoz való hozzáférésre a feladatok futtatásához. Alapértelmezés szerint a fürt rendszergazdája rendelkezik az összes fürtcsomópont gyökérszintű hozzáféréssel.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: nem alkalmazható; a teljesítményteszt az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](https://docs.microsoft.com/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: a Azure Databricks példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Databricks" névtérben egyéni szabályzatok létrehozásához a Azure Databricks példányok konfigurációjának naplózásához vagy érvényesítéséhez. Vegye figyelembe, hogy az alkalmazott szabályzatok nem működnek a Databricks felügyelt erőforráscsoporthoz.

* [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Azure Databricks egy Pásti szolgáltatás, nem rendelkezik közvetlen hozzáféréssel a virtuális gépekhez egy Azure Databricks-fürtben.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: a Azure Databricks példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Databricks" névtérben egyéni szabályzatok létrehozásához a Azure Databricks példányok konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure-szabályzat [deny] és a [telepítés, ha nem létezik] használatával kényszerítheti ki az Azure-erőforrások biztonságos beállításait.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy effektusok ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: a Microsoft által kezelt és karbantartott operációsrendszer-lemezképek Azure Databricks. Az operációs rendszer szintű állapot konfigurációjának végrehajtásáért felelős.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: Ha egyéni Azure-szabályzat-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

* [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: Ha egyéni rendszerképeket használ a Azure Databricks fürtcsomópontok esetében, küldje le az egyéni alaplemezképet egy Docker-beállításjegyzékbe, például Azure Container Registry (ACR).

* [Tárolók testreszabása a Databricks Container Services használatával](https://docs.microsoft.com/azure/databricks/clusters/custom-containers)

* [A Azure Container Registry megismerése](https://docs.microsoft.com/azure/container-registry/container-registry-intro)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="77-deploy-system-configuration-management-tools"></a>7,7: rendszerkonfiguráció-felügyeleti eszközök telepítése

**Útmutató**: a Azure Databricks példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Databricks" névtérben egyéni szabályzatok létrehozásához a Azure Databricks példányok konfigurációjának naplózásához vagy érvényesítéséhez.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="78-deploy-system-configuration-management-tools-for-operating-systems"></a>7,8: rendszerkonfiguráció-felügyeleti eszközök központi telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Azure Databricks egy Pásti szolgáltatás, nem rendelkezik közvetlen hozzáféréssel a virtuális gépekhez egy Azure Databricks-fürtben.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="79-implement-automated-configuration-monitoring-for-azure-services"></a>7,9: az Azure-szolgáltatások automatizált konfigurációs figyelésének megvalósítása

**Útmutató**: a Azure Databricks példányok szabványos biztonsági konfigurációinak meghatározása és implementálása Azure Policy használatával. Használjon Azure Policy aliasokat a "Microsoft. Databricks" névtérben egyéni szabályzatok létrehozásához a Azure Databricks példányok konfigurációjának naplózásához vagy érvényesítéséhez.

* [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: külső gyártótól származó megoldás implementálása a Azure Databricks fürtcsomópontok operációs rendszerei állapotának figyeléséhez.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: a Azure Key Vault használata Azure Databricks titkos hatókörrel a titkok biztonságos kezeléséhez és használatához.

* [A Azure Key Vault használata a Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/store-secrets-azure-key-vault)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: jelenleg nem érhető el; A felügyelt identitások jelenleg nem érhetők el Azure Databricks

* [Az Azure-erőforrások felügyelt identitásait támogató szolgáltatások](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/services-support-managed-identities)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: jelenleg nem érhető el

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a programkódon belül a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](https://docs.microsoft.com/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: Ha a biztonsági rések kezelése platform előfizetéssel rendelkezik, Azure Databricks inicializálási parancsfájlok segítségével telepítheti a sebezhetőség-felmérési ügynököket a Azure Databricks fürtcsomópontokon, és felügyelheti a csomópontokat a megfelelő portálon. Vegye figyelembe, hogy minden harmadik féltől származó megoldás eltérően működik.

* [A Rapid7-ügynök manuális telepítése](https://insightagent.help.rapid7.com/docs/install)

* [A Qualys-ügynök manuális telepítése](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks inicializálási parancsfájlok](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft antimalware engedélyezve van a mögöttes gazdagépen, amely támogatja az Azure-szolgáltatásokat (például Azure app Service), de nem fut a tartalomon.

A Azure Databricks fürtcsomóponton vagy a kapcsolódó erőforrásokra feltöltött fájlok előzetes vizsgálata.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: Ha a biztonsági rések kezelése platform előfizetéssel rendelkezik, Azure Databricks inicializálási parancsfájlok segítségével telepítheti a sebezhetőség-felmérési ügynököket a Azure Databricks fürtcsomópontokon, és felügyelheti a csomópontokat a megfelelő portálon. Vegye figyelembe, hogy minden harmadik féltől származó megoldás eltérően működik.

* [A Rapid7-ügynök manuális telepítése](https://insightagent.help.rapid7.com/docs/install)

* [A Qualys-ügynök manuális telepítése](https://www.qualys.com/docs/qualys-cloud-agent-linux-install-guide.pdf)

* [Azure Databricks inicializálási parancsfájlok](https://docs.microsoft.com/azure/databricks/clusters/init-scripts)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](https://docs.microsoft.com/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a Azure Databricks adatforrások esetében győződjön meg arról, hogy megfelelő szintű adatredundanciát konfigurált a használati esethez. Ha például egy Azure Storage-fiókot használ a Azure Databricks adattárhoz, válassza ki a megfelelő redundancia-beállítást (LRS, ZRS, GRS, RA-GRS).

* [Azure Databricks adatforrásai](https://docs.microsoft.com/azure/azure-databricks/databricks-connect-to-data-sources#data-sources-for-azure-databricks)

* [Regionális vész-helyreállítás Azure Databricks-fürtökhöz](https://docs.microsoft.com/azure/azure-databricks/howto-regional-disaster-recovery)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és biztonsági másolat készítése bármely ügyfél által felügyelt kulcsról

**Útmutató**: a Azure Databricks-megvalósításokhoz kapcsolódó ügyfelek által felügyelt kulcsok Azure Key Vaultn belüli biztonsági mentése. A Databricks konfigurációk napi biztonsági mentését REST API és/vagy CLI használatával is létrehozhatja.

* [Key Vault-kulcsok biztonsági mentése az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a Azure Databricks-implementációhoz kapcsolódó, biztonsági mentéssel rendelkező ügyfelek által felügyelt kulcsok visszaállításának tesztelése.

* [Key Vault-kulcsok visszaállítása az Azure-ban](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a kulcsok véletlen vagy rosszindulatú törléssel szembeni védelemmel való ellátásához engedélyezze a törlést a Key Vaultban.

* [A Soft delete engedélyezése Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](https://docs.microsoft.com/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: az incidensekre adott válaszokra vonatkozó útmutató kiépítése a szervezet számára. Győződjön meg arról, hogy van olyan írásos incidens-válasz, amely meghatározza a személyzet összes szerepkörét, valamint az incidensek kezelésének és kezelésének fázisait az észleléstől az incidens utáni felülvizsgálatig.

* [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

* [Útmutató a saját biztonsági incidensek megoldási folyamatának létrehozásához](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [Microsoft Security Response Center – incidens anatómiája](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

* [A NIST számítógép-biztonsági incidensek kezelési útmutatóját is kihasználhatja a saját incidens-válasz tervének létrehozásával kapcsolatos támogatáshoz](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel az egyes riasztásokhoz, hogy a prioritások alapján ki lehessen deríteni, hogy mely riasztásokat kell először megvizsgálni. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

Emellett egyértelműen megjelölheti az előfizetéseket (pl.: éles környezetben nem termelt), és hozzon létre egy elnevezési rendszert az Azure-erőforrások egyértelmű azonosításához és kategorizálásához.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

* [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az ügyféladatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte. A problémák megoldása érdekében tekintse át az incidenseket a tény után.

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

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings-within-60-days"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági eredmény szervizelésének biztosítása a 60 napon belül.

**Útmutató**: * [kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

* [További információt a Microsoft által felügyelt felhőalapú infrastruktúrával, szolgáltatásokkal és alkalmazásokkal kapcsolatos, a Microsoft által felügyelt felhő-infrastruktúra,-szolgáltatások és-alkalmazások fejlesztésére vonatkozó stratégiáról és végrehajtásáról itt talál.](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

## <a name="next-steps"></a>További lépések

- Lásd az [Azure biztonsági teljesítménytesztét](https://docs.microsoft.com/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alaptervekről](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)
