---
title: Az Azure biztonsági alapterve a szinapszis Analyticshez
description: A szinapszis Analytics biztonsági alapterve eljárási útmutatást és erőforrásokat biztosít az Azure biztonsági Teljesítménytesztben meghatározott biztonsági javaslatok megvalósításához.
author: msmbaldwin
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 7f05e4fb0443107370f9182706bd35b45771e0f2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210896"
---
# <a name="azure-security-baseline-for-synapse-analytics"></a>Az Azure biztonsági alapterve a szinapszis Analyticshez

A szinapszis Analytics Azure biztonsági alapterve olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat tesz arra vonatkozóan, hogy miként védheti meg felhőalapú megoldásait az Azure-ban az ajánlott eljárásokat ismertető útmutató segítségével.

További információ: [Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)alapkonfigurációk áttekintése.

## <a name="network-security"></a>Hálózati biztonság

*További információkért lásd [: biztonsági ellenőrzés: hálózati biztonság](/azure/security/benchmarks/security-control-network-security).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: Azure-erőforrások biztosítása virtuális hálózatokon belül

**Útmutató**: az Azure-SQL Server védelme privát kapcsolaton keresztül virtuális hálózatra. Az Azure Private link lehetővé teszi, hogy a virtuális hálózat privát végpontján keresztül hozzáférjen az Azure Pásti-szolgáltatásokhoz. A virtuális hálózat és a szolgáltatás közötti forgalom a Microsoft gerinc hálózatán halad át.

Azt is megteheti, hogy a szinapszis SQL-készlethez való csatlakozáskor leszűkíti a kimenő kapcsolat hatókörét az SQL-adatbázishoz egy hálózati biztonsági csoport használatával. Az Azure-szolgáltatások kikapcsolásának engedélyezése beállítás megadásával tiltsa le az összes Azure-szolgáltatás forgalmát az SQL-adatbázishoz a nyilvános végponton keresztül. Győződjön meg arról, hogy a tűzfalszabályok nem engedélyezik nyilvános IP-címek beírását.

* [Az Azure privát hivatkozásának megismerése](https://docs.microsoft.com/azure/private-link/private-link-overview)

* [Az Azure szinapszis SQL privát hivatkozásának megismerése](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [Virtual Network létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [Biztonsági konfigurációval rendelkező NSG létrehozása](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1,2: a virtuális hálózatok, alhálózatok és hálózati adapterek konfigurációjának és forgalmának figyelése és naplózása

**Útmutató**: Ha az Azure szinapszis SQL-készlethez csatlakozik, és engedélyezte a hálózati biztonsági csoport (NSG) folyamatának naplóit, naplókat küldhet egy Azure Storage-fiókba a forgalom naplózásához.

NSG-naplókat is küldhet egy Log Analytics munkaterületre, és a Traffic Analytics használatával betekintést nyerhet az Azure-Felhőbeli forgalomba. A Traffic Analytics egyes előnyei lehetővé teszi a hálózati tevékenységek megjelenítését és a gyakori pontok azonosítását, a biztonsági fenyegetések azonosítását, a forgalomban rejlő minták értelmezését, valamint a hálózati helytelen konfigurációk meghatározását.

* [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

* [A Traffic Analytics engedélyezése és használata](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

* [A Azure Security Center által biztosított hálózati biztonság ismertetése](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="13-protect-critical-web-applications"></a>1,3: a kritikus webalkalmazások megóvása

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: az ismert kártevő IP-címekkel folytatott kommunikáció megtagadása

**Útmutató**: az Azure szinapszis SQL fejlett veszélyforrások elleni védelme (ATP) használata. Az ATP olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az adatbázisok elérésére vagy kiaknázására, és különböző riasztásokat indíthatnak, például a "lehetséges SQL-injektálást" és "a szokatlan helyről való hozzáférést". Az ATP a speciális adatbiztonsági (ADS) ajánlat része, és a központi SQL ADS portálon keresztül érhető el és kezelhető.

Az elosztott szolgáltatásmegtagadási támadások elleni védelem érdekében engedélyezze az Azure szinapszis SQL-hez társított virtuális hálózatokon DDoS Protection szabványt. A Azure Security Center integrált fenyegetési intelligencia használatával megtagadhatja a kommunikációt az ismert kártékony vagy nem használt internetes IP-címekkel.

* [Az Azure szinapszis SQL-hez készült ATP ismertetése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [A Azure SQL Database speciális adatbiztonságának engedélyezése](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [A hirdetések áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [A DDoS Protection konfigurálása](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

* [Az Azure Security Center integrált veszélyforrások felderítésének megismerése](https://docs.microsoft.com/azure/security-center/security-center-alerts-data-services)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="15-record-network-packets"></a>1,5: hálózati csomagok rögzítése

**Útmutató**: Ha az Azure szinapszis SQL-készlethez csatlakozik, és engedélyezte a hálózati biztonsági csoport (NSG) folyamatának naplóit, naplókat küldhet egy Azure Storage-fiókba a forgalom naplózásához. A flow-naplókat Log Analytics munkaterületre is küldheti, vagy továbbíthatja őket a Event Hubsba. Ha a rendellenes tevékenységek kivizsgálásához szükséges, engedélyezze Network Watcher csomagok rögzítését.

* [A NSG folyamat naplófájljainak engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

* [A Network Watcher engedélyezése](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: hálózati behatolás-észlelési/Behatolás-megelőzési rendszerek (AZONOSÍTÓk/IP-címek) üzembe helyezése

**Útmutató**: az Azure szinapszis SQL fejlett veszélyforrások elleni védelme (ATP) használata. Az ATP olyan rendellenes tevékenységeket észlel, amelyek szokatlan és potenciálisan ártalmas kísérleteket mutatnak az adatbázisok elérésére vagy kiaknázására, és különböző riasztásokat indíthatnak, például a "lehetséges SQL-injektálást" és "a szokatlan helyről való hozzáférést". Az ATP a speciális adatbiztonsági (ADS) ajánlat része, és a központi SQL ADS portálon keresztül érhető el és kezelhető. Az ATP a Azure Security Centerokkal is integrálja a riasztásokat.

* [Az Azure szinapszis SQL-hez készült ATP ismertetése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="17-manage-traffic-to-web-applications"></a>1,7: webalkalmazások forgalmának kezelése

**Útmutató**: nem alkalmazható; Ez a javaslat az Azure apps szolgáltatáshoz vagy webalkalmazásokat üzemeltető számítási erőforrásokhoz készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: a hálózati biztonsági szabályok bonyolultságának és adminisztratív terhelésének csökkentése

**Útmutató**: hálózati biztonsági csoportok vagy Azure Firewall hálózati hozzáférés-vezérlésének definiálásához használja a virtuális hálózati szolgáltatás címkéit. Biztonsági szabályok létrehozása során szolgáltatáscímkéket használhat bizonyos IP-címek helyett. Ha egy szabály megfelelő forrás vagy cél mezőjében megadja a szolgáltatási címke nevét (például ApiManagement), akkor engedélyezheti vagy megtagadhatja a megfelelő szolgáltatás forgalmát. A Microsoft kezeli a szolgáltatási címke által felölelt címek előtagjait, és automatikusan frissíti a szolgáltatási címkét a címek változásával.

Ha az Azure szinapszis SQL-készlethez szolgáltatási végpontot használ, az Azure SQL Database nyilvános IP-címeire való kimenő forgalomra van szükség: hálózati biztonsági csoportok (NSG) megnyitásához meg kell nyitni Azure SQL Database IP-címeket a kapcsolat engedélyezéséhez. Ezt a Azure SQL Database NSG-szolgáltatásának címkéi segítségével teheti meg.

* [A szolgáltatás-címkék ismertetése a Azure SQL Database szolgáltatás-végpontokkal](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview#limitations)

* [A szolgáltatási címkék megismerése és használata](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: a hálózati eszközök szabványos biztonsági konfigurációinak fenntartása

**Útmutató**: hálózati biztonsági konfigurációk definiálása és implementálása az SQL-készlethez kapcsolódó erőforrások Azure Policy használatával. A "Microsoft. SQL" névtér használatával egyéni szabályzat-definíciókat határozhat meg, illetve az Azure SQL Database/Server Network Protection szolgáltatáshoz tervezett beépített szabályzat-definíciókat is használhatja. A Azure SQL Database-kiszolgáló megfelelő beépített hálózati biztonsági házirendjének példája a következő: "SQL Server virtuális hálózati szolgáltatás-végpontot használjon".

Az Azure-tervezetek segítségével leegyszerűsítheti a nagyméretű Azure-környezetek nagy léptékű üzembe helyezését a főbb környezeti összetevők, például az Azure Resource Management-sablonok, a szerepköralapú hozzáférés-vezérlés (RBAC) és a házirendek egyetlen tervezet-definícióban való kicsomagolásával. Egyszerűen alkalmazhatja a tervrajzot új előfizetésekre és környezetekre, és részletesen beállíthatja a vezérlést és a felügyeletet a verziószámozással.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Blueprint létrehozása](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="110-document-traffic-configuration-rules"></a>1,10: a dokumentum forgalmának konfigurációs szabályai

**Útmutató**: használja a hálózati biztonsági csoportokhoz (NSG) és a hálózati biztonsággal és a forgalommal kapcsolatos egyéb erőforrásokhoz tartozó címkéket. Az egyes NSG-szabályok esetében a "Leírás" mezővel adhatja meg az üzleti igényeket és/vagy időtartamot (stb.) minden olyan szabályhoz, amely engedélyezi a hálózatra irányuló adatforgalmat.

A címkézéshez kapcsolódó beépített Azure Policy definíciók bármelyikét használhatja, például a "címke és az érték megkövetelése" beállítást, hogy az összes erőforrás címkével legyen létrehozva, és értesítse a meglévő címkézetlen erőforrásokról.

A Azure PowerShell vagy az Azure CLI használatával a címkék alapján kereshet vagy végezhet műveleteket az erőforrásokon.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: automatikus eszközök használata a hálózati erőforrások konfigurációjának figyelésére és a változások észlelésére

**Útmutató**: az Azure-tevékenység naplójának használata a hálózati erőforrás-konfigurációk figyelésére és az Azure szinapszis SQL-készlettel kapcsolatos hálózati erőforrások változásainak észlelésére. Hozzon létre riasztásokat Azure Monitoron belül, amelyek akkor lépnek életbe, amikor a kritikus hálózati erőforrásokra vonatkozó módosításokat végrehajtják

* [Azure-Tevékenységnaplók eseményeinek megtekintése és lekérése](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

* [Riasztások létrehozása a Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="logging-and-monitoring"></a>Naplózás és figyelés

*További információ [: Security Control: naplózás és figyelés](/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

**Útmutató**: a Microsoft fenntartja az Azure-erőforrások időforrásait. A számítási üzemelő példányok időszinkronizálása is frissíthető.

* [Az Azure számítási erőforrások időszinkronizálásának konfigurálása](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

**Útmutató**: a naplózási szabályzatok meghatározhatók egy adott adatbázishoz, vagy az Azure-ban alapértelmezett kiszolgálói házirendként (amely az Azure szinapszist tárolja). A kiszolgálói házirend a kiszolgálón lévő összes meglévő és újonnan létrehozott adatbázisra vonatkozik.

Ha a kiszolgáló naplózása engedélyezve van, az mindig az adatbázisra vonatkozik. A rendszer naplózza az adatbázist, az adatbázis naplózási beállításaitól függetlenül.

Ha engedélyezi a naplózást, megírhatja őket az Azure Storage-fiókjában, Log Analytics munkaterületen vagy Event Hubs.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Az Azure SQL-erőforrások naplózásának beállítása](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#server-vs-database-level)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

**Útmutató**: engedélyezze a naplózást az Azure SQL Server-szinten a szinapszis SQL-készlethez, és válassza ki a naplófájlok tárolási helyét (Azure storage, Log Analytics vagy Event Hubs).

A naplózás az adatbázison vagy a kiszolgáló szintjén is engedélyezhető, és azt javasoljuk, hogy csak a kiszolgáló szintjén engedélyezzen, csak akkor, ha külön adatfogadót vagy adatmegőrzést kíván konfigurálni egy adott adatbázis számára.

* [A Azure SQL Database naplózásának engedélyezése](https://docs.microsoft.com/azure/sql-database/sql-database-auditing)

* [A kiszolgáló naplózásának engedélyezése](https://docs.microsoft.com/azure/azure-sql/database/auditing-overview#setup-auditing)

* [Különbségek a kiszolgálói szintű és az adatbázis szintű naplózási házirendek között](https://docs.microsoft.com/azure/sql-database/sql-database-auditing#server-vs-database-level)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

**Útmutató**: nem alkalmazható; Ez a teljesítményteszt számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

**Útmutató**: Ha a szinapszis SQL-készlethez kapcsolódó naplókat tárol egy Storage-fiókban, log Analytics-munkaterületen vagy az Event hub-ban, állítsa be a napló megőrzési időszakát a szervezet megfelelőségi előírásai szerint.

* [Az Azure Blob Storage-életciklus felügyelete](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal)

* [Naplózási adatmegőrzési paraméterek beállítása Log Analytics munkaterületen](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

* [Folyamatos átviteli események rögzítése Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

**Útmutató**: a rendellenes viselkedésre vonatkozó naplók elemzése és figyelése, valamint az eredmények rendszeres áttekintése. Az összetett veszélyforrások elleni védelem használata a Azure SQL Database Azure Security Center az SQL-adatbázissal kapcsolatos szokatlan tevékenységekre vonatkozó riasztások esetén. Azt is megteheti, hogy a riasztásokat metrikus értékeken vagy az SQL-adatbázishoz kapcsolódó Azure-tevékenységi naplóbejegyzések alapján konfigurálja.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et.

* [Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [A Azure SQL Database speciális adatbiztonságának engedélyezése](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Egyéni riasztások konfigurálása Azure SQL Databasehoz](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal?view=azps-1.4.0)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="27-enable-alerts-for-anomalous-activities"></a>2,7: riasztások engedélyezése rendellenes tevékenységekhez

**Útmutató**: a komplex veszélyforrások elleni védelem (ATP) használata a rendellenes tevékenység figyelésére és riasztására Azure Security Centerekkel együtt Azure SQL Database. Az ATP a speciális adatbiztonsági (ADS) ajánlat része, amely a portálon elérhető központi SQL-hirdetések használatával érhető el és kezelhető. A hirdetések olyan funkciókat foglalnak magukban, mint a bizalmas adatok felderítése és besorolása, a felszínek és a lehetséges adatbázis-sebezhetőségek enyhítése, valamint a rendellenes tevékenységek észlelése, amelyek veszélyeztethetik az adatbázisát.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

* [Az összetett veszélyforrások elleni védelem és a riasztások megismerése Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview)

* [A Azure SQL Database speciális adatbiztonságának engedélyezése](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Riasztások kezelése Azure Security Centerban](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

* [Az Azure Sentinel előkészítése](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

**Útmutató**: nem alkalmazható; a szinapszis SQL-készlettel kapcsolatos erőforrásokhoz a kártevő szoftvereket a Microsoft felügyeli a mögöttes platformon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

**Útmutató**: nem alkalmazható; a szinapszis SQL-készlethez kapcsolódó erőforrások nem állítanak be DNS-naplókat.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

**Útmutató**: nem alkalmazható; a parancssori naplózás nem alkalmazható az Azure szinapszis SQL-re.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="identity-and-access-control"></a>Identitás- és hozzáférés-vezérlés

*További információkért lásd: a [biztonság szabályozása: identitás-és hozzáférés-vezérlés](/azure/security/benchmarks/security-control-identity-access-control).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3,1: a felügyeleti fiókok leltárának karbantartása

**Útmutató**: a felhasználók hitelesítése Azure Active Directory vagy SQL-hitelesítéssel történik.

Amikor először telepíti az Azure SQL-t, a bejelentkezéshez meg kell adnia egy rendszergazdai bejelentkezési azonosítót és egy hozzá tartozó jelszót. A rendszergazdai fiók neve kiszolgáló-rendszergazda. Az adatbázisok rendszergazdai fiókjainak azonosításához nyissa meg a Azure Portal, és navigáljon a kiszolgáló vagy a felügyelt példány tulajdonságok lapjára. Az Azure AD rendszergazdai fiókját teljes rendszergazdai engedélyekkel is konfigurálhatja, ez akkor szükséges, ha engedélyezni szeretné a Azure Active Directory hitelesítést.

Felügyeleti műveletek esetén használja az Azure beépített szerepköreit, amelyeket explicit módon hozzá kell rendelni. Az Azure AD PowerShell-modullal ad hoc lekérdezéseket hajthat végre a felügyeleti csoportok tagjait futtató fiókok felderítése érdekében.

* [SQL Database hitelesítése](https://docs.microsoft.com/azure/azure-sql/database/security-overview#authentication)

* [Fiókok létrehozása nem rendszergazda felhasználók számára](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-accounts-for-non-administrator-users)

* [Azure Active Directory-fiók használata a hitelesítéshez](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#create-additional-logins-and-users-having-administrative-permissions)

* [Címtárbeli szerepkör beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

* [Címtárbeli szerepkör tagjainak beszerzése az Azure AD-ben a PowerShell-lel](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

* [Meglévő bejelentkezések és rendszergazdai fiókok kezelése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

* [Beépített Azure-szerepkörök](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="32-change-default-passwords-where-applicable"></a>3,2: az alapértelmezett jelszavak módosítása, ha alkalmazható

**Útmutató**: a Azure Active Directory nem rendelkezik az alapértelmezett jelszavak fogalmával. Az Azure szinapszis SQL-készlet kiépítésekor javasolt a hitelesítés integrálása Azure Active Directory használatával. Ezzel a hitelesítési módszerrel a felhasználó elküld egy felhasználói fióknevet és kéréseket, hogy a szolgáltatás a Azure Active Directory (Azure AD) által tárolt hitelesítő adatokat használja.

* [Azure Active Directory hitelesítés konfigurálása és kezelése az Azure SQL használatával](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure?tabs=azure-powershell#active-directory-password-authentication)

* [Az Azure SQL-alapú hitelesítés ismertetése](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="33-use-dedicated-administrative-accounts"></a>3,3: dedikált rendszergazdai fiókok használata

**Útmutató**: szabályzatok és eljárások létrehozása a dedikált rendszergazdai fiókok használatával. A Azure Security Center identitás-és hozzáférés-kezelési szolgáltatással figyelheti a Azure Active Directory használatával bejelentkezett rendszergazdai fiókok számát.

Az adatbázishoz tartozó rendszergazdai fiókok azonosításához nyissa meg a Azure Portal, és keresse meg a kiszolgáló vagy a felügyelt példány Tulajdonságok lapját.

* [Azure Security Center identitás és hozzáférés ismertetése](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Meglévő bejelentkezések és rendszergazdai fiókok kezelése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage#existing-logins-and-user-accounts-after-creating-a-new-database)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3,4: Azure Active Directory egyszeri bejelentkezés (SSO) használata

**Útmutató**: az Azure-alkalmazás regisztrálása (egyszerű szolgáltatásnév) használatával lekérheti az adattárházat a vezérlési síkon (Azure Portal) API-hívásokon keresztül felhasználható jogkivonatot.

* [Az Azure REST API-k meghívása](https://docs.microsoft.com/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

* [Az ügyfélalkalmazás (egyszerű szolgáltatásnév) regisztrálása az Azure AD-vel](https://docs.microsoft.com/rest/api/azure/#register-your-client-application-with-azure-ad)

* [Azure szinapszis SQL REST API információk](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3,5: Multi-Factor Authentication használata az összes Azure Active Directory-alapú hozzáféréshez

**Útmutató**: a Azure Active Directory (AD) multi-Factor Authentication (MFA) engedélyezése és a Azure Security Center identitás-és hozzáférés-kezelési javaslatainak követése.

* [Az MFA engedélyezése az Azure-ban](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

* [Identitás és hozzáférés figyelése Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Az MFA megismerése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/authentication-mfa-ssms-overview)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3,6: biztonságos, Azure által felügyelt munkaállomások használata felügyeleti feladatokhoz

**Útmutató**: az Azure-erőforrások bevezetésére és konfigurálására konfigurált multi-Factor Authentication (MFA) rendelkező, rendszerjogosultságú hozzáférési munkaállomás (Paw) használata.

* [További tudnivalók a privilegizált hozzáférésű munkaállomásokról](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations)

* [Az MFA engedélyezése az Azure-ban](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3,7: naplózás és riasztás a gyanús tevékenységekről a rendszergazdai fiókoktól

**Útmutató**: Azure Active Directory biztonsági jelentések használata naplók és riasztások generálásához, ha a környezetben gyanús vagy nem biztonságos tevékenység történik.

A komplex veszélyforrások elleni védelem használata a Azure SQL Databasehoz együtt a Azure Security Center használatával észleli és figyelmezteti az olyan rendellenes tevékenységeket, amelyek szokatlan és potenciálisan ártalmas kísérleteket jeleznek az adatbázisok eléréséhez vagy kiaknázásához.

SQL Server naplózással kiszolgálói naplózásokat hozhat létre, amelyek tartalmazhatják a kiszolgálói naplózási specifikációkat a kiszolgálói szintű eseményekhez, valamint az adatbázis-szintű események adatbázis-naplózási specifikációit. A naplózott események az eseménynaplóba vagy a fájlok naplózására is írhatók.

* [A kockázatos tevékenységre megjelölt Azure AD-felhasználók azonosítása](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-user-at-risk)

* [A felhasználók identitás-és hozzáférési tevékenységének figyelése Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-identity-access)

* [Az összetett veszélyforrások elleni védelem és a lehetséges riasztások áttekintése](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview#advanced-threat-protection-alerts)

* [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

* [SQL Server naplózás ismertetése](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine?view=sql-server-ver15)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3,8: az Azure-erőforrások kezelése csak jóváhagyott helyekről

**Útmutató**: a feltételes hozzáférés elnevezett helyeivel lehetővé teheti a portál és az Azure erőforrás-kezelési hozzáférését az IP-címtartományok vagy országok/régiók adott logikai csoportjaiból.

* [Elnevezett helyszínek konfigurálása az Azure-ban](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="39-use-azure-active-directory"></a>3,9: a Azure Active Directory használata

**Útmutató**: hozzon létre egy Azure Active Directory (ad) rendszergazdát a Azure SQL Database-kiszolgálóhoz a szinapszis SQL-készletben.

* [Azure AD-hitelesítés konfigurálása és kezelése az Azure SQL használatával](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Azure AD-példány létrehozása és konfigurálása](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="310-regularly-review-and-reconcile-user-access"></a>3,10: a felhasználói hozzáférés rendszeres áttekintése és egyeztetése

**Útmutató**: a Azure Active Directory olyan naplókat biztosít, amelyek segítenek az elavult fiókok felderítésében. Emellett Azure Active Directory hozzáférési felülvizsgálatokat is használhat a csoporttagságok hatékony kezelésére, a vállalati alkalmazásokhoz való hozzáférésre és a szerepkör-hozzárendelésekre. A felhasználók hozzáférését rendszeresen áttekintheti, hogy csak a megfelelő felhasználók férhessenek hozzá.

SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

* [A hozzáférési felülvizsgálatok használata](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3,11: a figyelő megkísérli a deaktivált hitelesítő adatok elérését

**Útmutató**: Azure Active Directory (ad) hitelesítés konfigurálása az Azure SQL-sel és az Azure Active Directory felhasználói fiókok diagnosztikai beállításainak engedélyezése, a naplók és a bejelentkezési naplók elküldése egy log Analytics munkaterületre. A kívánt riasztások konfigurálása Log Analyticson belül.

SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

* [A hozzáférési felülvizsgálatok használata](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)

* [Azure AD-hitelesítés konfigurálása és kezelése a Azure SQL Database használatával](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication-configure)

* [Azure-beli tevékenység-naplók integrálása a Azure Monitorba](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

* [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3,12: riasztás a fiók bejelentkezési viselkedésének eltérése esetén

**Útmutató**: az Azure Active Directory (Azure ad) Identity Protection és a kockázati észlelési funkciók használatával konfigurálhatja a felhasználói identitásokkal kapcsolatos gyanús műveletekre vonatkozó automatizált válaszokat. Emellett további vizsgálat céljából az Azure Sentinelbe is betöltheti a táblát, és beolvashatja azokat.

SQL-hitelesítés használatakor hozzon létre tárolt adatbázis-felhasználókat az adatbázisban. Ügyeljen arra, hogy egy vagy több adatbázis-felhasználót egy egyéni adatbázis-szerepkörbe helyezzen, amely az adott felhasználói csoportnak megfelelő engedélyekkel rendelkezik.

* [Az Azure AD kockázati bejelentkezések megtekintése](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-risky-sign-ins)

* [Az Identity Protection kockázati házirendjeinek konfigurálása és engedélyezése](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policies)

* [A fedélzeti Azure Sentinel ismertetése](https://docs.microsoft.com/azure/sentinel/connect-data-sources)

* [A bejelentkezések és a felhasználói fiókok megismerése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3,13: a Microsoft számára elérhetővé teszi a megfelelő ügyféladatokat a támogatási forgatókönyvek során

**Útmutató**: olyan forgatókönyvek esetén, ahol a Microsoftnak a szinapszis SQL-készlet Azure SQL Database kapcsolódó adatokhoz kell hozzáférnie, az Azure Ügyfélszéf felületet biztosít az adatelérési kérelmek áttekintéséhez és jóváhagyásához vagy elutasításához.

* [A Ügyfélszéf megismerése](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

## <a name="data-protection"></a>Adatvédelem

*További információkért lásd [: biztonsági ellenőrzés:](/azure/security/benchmarks/security-control-data-protection)adatvédelem.*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4,1: bizalmas információk leltárának fenntartása

**Útmutató**: a címkék használatával segítheti a bizalmas adatokat tároló vagy feldolgozó Azure-erőforrások nyomon követését.

Az adatfelderítési &amp; besorolás az Azure SZINAPSZIS SQL-be van építve. Fejlett képességeket biztosít az adatbázisaiban található bizalmas adatok felfedéséhez, besorolásához, címkézéséhez és jelentéséhez.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Az adatfelderítési &amp; besorolás ismertetése](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4,2: bizalmas adatok tárolására vagy feldolgozására szolgáló rendszerek elkülönítése

**Útmutató**: különálló előfizetések és/vagy felügyeleti csoportok megvalósítása fejlesztési, tesztelési és éles környezetekhez. Az erőforrásokat virtuális hálózattal/alhálózattal kell elválasztani, megfelelően címkézve, és egy hálózati biztonsági csoporton vagy Azure Firewallon belül kell biztosítani. A bizalmas adatokat tároló vagy feldolgozó erőforrások elkülönítését el kell különíteni. Privát hivatkozás használata; Azure-SQL Server üzembe helyezése virtuális hálózaton belül, és biztonságos kapcsolódás a privát kapcsolat használatával.

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

* [Privát hivatkozás beállítása Azure SQL Databasehoz](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview#how-to-set-up-private-link-for-azure-sql-database)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4,3: a bizalmas adatok jogosulatlan átvitelének figyelése és letiltása

**Útmutató**: a szinapszis SQL-készlet bármely Azure SQL Database bizalmas adatokat tárol vagy dolgoz fel, az adatbázist és a kapcsolódó erőforrásokat bizalmasként jelöli meg a címkék használatával. Konfigurálja a privát hivatkozást a hálózati biztonsági csoport (NSG) szolgáltatásbeli címkékkel a Azure SQL Database példányokon, hogy megakadályozza a bizalmas adatok kiszűrése.

Emellett a Azure SQL Database komplex veszélyforrások elleni védelme, az Azure SQL felügyelt példánya és az Azure szinapszis rendellenes tevékenységeket észlel, amelyekben szokatlan és potenciálisan ártalmas próbálkozások érhetők el az adatbázisok eléréséhez vagy kiaknázásához.

A Microsoft által felügyelt mögöttes platform esetében a Microsoft az összes vásárlói tartalmat bizalmasként kezeli, és az ügyfelek adatvesztésével és a kitettséggel szembeni védelem érdekében nagy hosszúságú. Annak biztosítása érdekében, hogy az Azure-beli ügyféladatok biztonságban maradjanak, a Microsoft végrehajtotta és karbantartja a robusztus adatvédelmi szabályozást és képességeket.

* [Privát hivatkozás és NSG konfigurálása az Azure SQL Database-példányokon lévő adatkiszűréseek megelőzésére](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)

* [A Azure SQL Database komplex veszélyforrások elleni védelmének megismerése](https://docs.microsoft.com/azure/azure-sql/database/threat-detection-overview)

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: megosztott

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4,4: minden bizalmas adat titkosítása az átvitel során

**Útmutató**: a Azure SQL Database a Transport Layer Security használatával a mozgásban lévő adattitkosítással védi az adatait. SQL Server az összes kapcsolat esetében mindig kikényszeríti a titkosítást (SSL/TLS). Ez biztosítja, hogy az összes adatforgalom titkosítva legyen az ügyfél és a kiszolgáló között, függetlenül attól, hogy a titkosítás vagy a TrustServerCertificate a kapcsolati sztringben van-e beállítva.

* [Az Azure SQL-titkosítás ismertetése az átvitel során](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview#information-protection-and-encryption)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: Microsoft

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4,5: aktív felderítési eszköz használata a bizalmas adatok azonosítására

**Útmutató**: az Azure szinapszis SQL adatfelderítési &amp; besorolási funkciójának használata. Az adatfelderítési &amp; besorolás olyan speciális képességeket biztosít, amelyek a Azure SQL Databaseba vannak építve a &amp; bizalmas adatoknak az adatbázisokban való felderítéséhez, besorolásához, címkézéséhez.

Az adatfelderítési &amp; besorolás a speciális adatbiztonsági ajánlat részét képezi, amely a speciális SQL-alapú biztonsági funkciók egységes csomagja. Az adatfelderítési &amp; besorolás a központi SQL ADS portálon keresztül érhető el és kezelhető.

Emellett beállíthat egy dinamikus adatmaszkolási (DDM-) szabályzatot is a Azure Portal. A DDM-javaslatok motorja az adatbázis bizonyos mezőit olyan érzékeny mezőként adja meg, amely a maszkoláshoz jó választás lehet.

* [Adatfelderítés és besorolás használata az Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-data-discovery-and-classification)

* [Az Azure szinapszis SQL dinamikus adatmaszkolási szolgáltatásának ismertetése](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="46-use-role-based-access-control-to-control-access-to-resources"></a>4,6: szerepköralapú hozzáférés-vezérlés használata az erőforrásokhoz való hozzáférés szabályozásához

**Útmutató**: az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával kezelheti az Azure SQL-adatbázisokhoz való hozzáférést a szinapszis SQL-készletében.

Az engedélyezést a felhasználói fiók adatbázis-szerepköri tagsága és az objektum szintű engedélyek vezérlik. Ajánlott eljárásként csak a minimálisan szükséges engedélyeket adja meg a felhasználóknak.

* [Az Azure SQL Server és az Azure Active Directory integrálása hitelesítéshez](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication)

* [Hozzáférés vezérlése az Azure SQL Serverban](https://docs.microsoft.com/azure/sql-database/sql-database-control-access)

* [Az engedélyezés és a hitelesítés megismerése az Azure SQL-ben](https://docs.microsoft.com/azure/azure-sql/database/logins-create-manage)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4,7: a gazdagép-alapú adatvesztés-megelőzés használata a hozzáférés-vezérlés kikényszeríthető

**Útmutató**: nem alkalmazható; A Microsoft kezeli az Azure szinapszis SQL mögöttes infrastruktúráját, és szigorú ellenőrzéseket vezetett be az ügyféladatok elvesztésének vagy expozíciójának megakadályozására.

* [Az ügyfelek adatvédelmének megismerése az Azure-ban](https://docs.microsoft.com/azure/security/fundamentals/protection-customer-data)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="48-encrypt-sensitive-information-at-rest"></a>4,8: bizalmas adatok titkosítása a nyugalmi állapotban

**Útmutató**: a transzparens adattitkosítás (TDE) segít megvédeni az Azure szinapszis SQL-et a rosszindulatú offline tevékenységek fenyegetésével szemben, ha titkosítja az inaktív adatok mennyiségét. Valós időben titkosítja és fejti vissza az adatbázist, a hozzá tartozó biztonsági másolatokat és a tranzakciónapló-fájlokat anélkül, hogy ehhez módosítani kellene az alkalmazást. Az Azure-ban a TDE alapértelmezett beállítása az, hogy a ADATTITKOSÍTÁSI kulcsot egy beépített kiszolgálótanúsítvány védi. Alternatív megoldásként használhatja az ügyfél által felügyelt TDE, más néven a TDE-hez Bring Your Own Key (BYOK) támogatást. Ebben az esetben a ADATTITKOSÍTÁSI kulcsot titkosító TDE-védő egy ügyfél által felügyelt aszimmetrikus kulcs, amelyet az ügyfél és a felügyelt Azure Key Vault (az Azure felhőalapú külső kulcs-felügyeleti rendszere) tárol, és soha nem hagyja el a kulcstartót.

* [A szolgáltatás által kezelt transzparens adattitkosítás ismertetése](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal)

* [Az ügyfél által kezelt transzparens adattitkosítás ismertetése](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-tde-overview?tabs=azure-portal#customer-managed-transparent-data-encryption---bring-your-own-key)

* [A TDE bekapcsolása saját kulcs használatával](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure)

**Azure Security Center figyelés**: igen

**Felelősség**: megosztott

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4,9: a kritikus Azure-erőforrások változásainak naplózása és riasztása

**Útmutató**: a Azure monitor és az Azure-tevékenység naplójának használatával riasztásokat hozhat létre, amelyekkel a szinapszis SQL-készletek és egyéb kritikus vagy kapcsolódó erőforrások éles példányain végezheti el a módosításokat.

Emellett az SQL szinapszis-készletben lévő adatbázisokhoz is beállíthat riasztásokat az Azure Portal használatával. A riasztások e-mailt küldhetnek, vagy meghívhatnak egy webhookot, ha valamilyen metrika (például az adatbázis mérete vagy a CPU-használat) eléri a küszöbértéket.

* [Riasztások létrehozása az Azure-tevékenységek naplózási eseményeihez](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

* [Riasztások létrehozása az Azure SQL Szinapszishoz](https://docs.microsoft.com/azure/azure-sql/database/alerts-insights-configure-portal)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="vulnerability-management"></a>Biztonságirés-kezelés

*További információ [: Security Control: sebezhetőségi kezelés](/azure/security/benchmarks/security-control-vulnerability-management).*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5,1: automatikus biztonsági rések vizsgálatára szolgáló eszközök futtatása

**Útmutató**: engedélyezze a speciális adatbiztonságot, és kövesse az Azure Security Center a sebezhetőségi felmérések Azure SQL-adatbázisokon való végrehajtásáról szóló ajánlásokat.

* [Sebezhetőségi felmérések futtatása Azure SQL-adatbázisokon](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

* [A speciális adatbiztonság engedélyezése](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)

* [Azure Security Center sebezhetőségi felméréssel kapcsolatos javaslatok megvalósítása](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5,2: az operációs rendszer automatikus javításának felügyeleti megoldásának telepítése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5,3: a harmadik féltől származó szoftverek címeihez készült automatizált javítási megoldás üzembe helyezése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5,4: a biztonsági rések keresésének összehasonlítása

**Útmutató**: a sebezhetőségi felmérés egy Azure szinapszis SQL-ben beépített keresési szolgáltatás. A szolgáltatás a biztonsági réseket jelző szabályok tudásbázisát alkalmazza. Kiemeli az ajánlott eljárásoktól való eltéréseket, például a helytelen konfigurációkat, a túlzott engedélyeket és a nem védett bizalmas adatokat. A sebezhetőségi felmérés a központi SQL Advanced Security (ADS) portálon keresztül érhető el és kezelhető.

* [Sebezhetőség-felmérési vizsgálatok kezelése és exportálása az SQL ADS portálon](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5,5: kockázatértékelési folyamat használatával rangsorolhatja a felderített biztonsági rések szervizelését

**Útmutató**: a Azure Security Center által biztosított alapértelmezett kockázati minősítések (biztonságos pontszám) használata.

Az adatfelderítési &amp; besorolás az Azure SZINAPSZIS SQL-be van építve. Fejlett képességeket biztosít az adatbázisaiban található bizalmas adatok felfedéséhez, besorolásához, címkézéséhez és jelentéséhez.

* [A Azure Security Center biztonsági pontszámának megismerése](https://docs.microsoft.com/azure/security-center/security-center-secure-score)

* [Az adatfelderítési &amp; besorolás ismertetése](https://docs.microsoft.com/azure/azure-sql/database/data-discovery-and-classification-overview)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

## <a name="inventory-and-asset-management"></a>Leltár- és eszközfelügyelet

*További információkért lásd [: biztonsági vezérlés: leltár és eszközkezelés](/azure/security/benchmarks/security-control-inventory-asset-management).*

### <a name="61-use-automated-asset-discovery-solution"></a>6,1: automatikus eszköz-felderítési megoldás használata

**Útmutató**: az Azure Resource Graph használatával lekérdezheti és felderítheti a szinapszis SQL-készlethez kapcsolódó összes erőforrást az előfizetése (i) n belül. Győződjön meg arról, hogy megfelelő (olvasási) engedélyekkel rendelkezik a bérlőben, és képes felsorolni az összes Azure-előfizetést, valamint az előfizetésében lévő erőforrásokat.

Bár a klasszikus Azure-erőforrások felderíthető az Azure Resource Graph használatával, erősen ajánlott Azure Resource Manager erőforrások létrehozása és használata.

* [Lekérdezések létrehozása az Azure Resource Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

* [Azure-előfizetések megtekintése](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

* [Az Azure RBAC ismertetése](https://docs.microsoft.com/azure/role-based-access-control/overview)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="62-maintain-asset-metadata"></a>6,2: az eszköz metaadatainak fenntartása

**Útmutató**: címkéket alkalmazhat az Azure-erőforrásokra, így a metaadatok logikailag rendszerezve lesznek a besorolásban.

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="63-delete-unauthorized-azure-resources"></a>6,3: jogosulatlan Azure-erőforrások törlése

**Útmutató**: a címkézés, a felügyeleti csoportok és az elkülönített előfizetések használata, ha szükséges, az eszközök rendszerezéséhez és nyomon követéséhez. Rendszeres időközönként egyeztetheti a leltárt, és gondoskodhat arról, hogy a jogosulatlan erőforrások törlése az előfizetésből időben történjen.

* [További Azure-előfizetések létrehozása](https://docs.microsoft.com/azure/billing/billing-create-subscription)

* [Management Groups létrehozása](https://docs.microsoft.com/azure/governance/management-groups/create)

* [Címkék létrehozása és használata](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6,4: a jóváhagyott Azure-erőforrások leltárának meghatározása és karbantartása

**Útmutató**: a szinapszis SQL-készlethez kapcsolódó jóváhagyott Azure-erőforrások listájának meghatározása.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="65-monitor-for-unapproved-azure-resources"></a>6,5: a nem jóváhagyott Azure-erőforrások figyelése

**Útmutató**: a Azure Policy használatával korlátozásokat állíthat be az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetésében lévő erőforrásokat. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Lekérdezések létrehozása az Azure Resource Graph használatával](https://docs.microsoft.com/azure/governance/resource-graph/first-query-portal)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6,6: a nem jóváhagyott szoftveralkalmazások figyelése a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6,7: nem jóváhagyott Azure-erőforrások és szoftveralkalmazások eltávolítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="68-use-only-approved-applications"></a>6,8: csak jóváhagyott alkalmazások használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="69-use-only-approved-azure-services"></a>6,9: csak jóváhagyott Azure-szolgáltatások használata

**Útmutató**: a Azure Policy használatával korlátozásokat helyezhet el az ügyfél-előfizetésekben létrehozható erőforrások típusára a következő beépített szabályzat-definíciók használatával:
- Nem engedélyezett erőforrástípusok
- Engedélyezett erőforrástípusok

Az Azure Resource Graph használatával lekérdezheti vagy felderítheti az előfizetése (ke) n belüli erőforrásait. Győződjön meg arról, hogy a környezetben lévő összes Azure-erőforrás jóvá van hagyva.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Adott erőforrástípus megtagadása a következővel Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/not-allowed-resource-types)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6,10: a jóváhagyott szoftverek leltárának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat a számítási erőforrásokon futó alkalmazások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6,11: korlátozza a felhasználók képességét a Azure Resource Manager való interakcióra

**Útmutató**: az Azure feltételes hozzáférés használatával korlátozhatja a felhasználók a Azure Resource Manager interakcióját a "Microsoft Azure felügyelet" alkalmazás "hozzáférés tiltása" beállításával.

* [A feltételes hozzáférés konfigurálása a Azure Resource Managerhoz való hozzáférés blokkolásához](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6,12: korlátozza a felhasználók számára a parancsfájlok végrehajtásának lehetőségét a számítási erőforrásokon belül

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6,13: fizikailag vagy logikailag elkülöníthető a nagy kockázatú alkalmazások

**Útmutató**: az üzleti műveletekhez szükséges, a szinapszis SQL-készlethez kapcsolódó összes erőforrás, de nagyobb kockázatot jelenthet a szervezet számára, el kell különíteni a saját virtuális gépén és/vagy virtuális hálózatán belül, és megfelelően biztonságossá kell tennie egy Azure Firewall vagy hálózati biztonsági csoporttal.

* [Virtuális hálózat létrehozása](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

* [NSG létrehozása biztonsági konfigurációval](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="secure-configuration"></a>Biztonságos konfiguráció

*További információkért lásd [: biztonság-vezérlés: biztonságos konfiguráció](/azure/security/benchmarks/security-control-secure-configuration).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7,1: biztonságos konfigurációk létrehozása az összes Azure-erőforráshoz

**Útmutató**: Azure Policy aliasok használata a "Microsoft. SQL" névtérben egyéni szabályzatok létrehozásához a szinapszis SQL-készlethez kapcsolódó erőforrások konfigurációjának naplózásához vagy érvényesítéséhez. Az Azure Database/Server beépített szabályzat-definícióit is használhatja, például:
- Veszélyforrások észlelésének üzembe helyezése SQL-kiszolgálókon
- SQL Server virtuális hálózati szolgáltatás végpontját kell használnia

* [Az elérhető Azure Policy aliasok megtekintése](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="72-establish-secure-operating-system-configurations"></a>7,2: biztonságos operációsrendszer-konfigurációk létrehozása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="73-maintain-secure-azure-resource-configurations"></a>7,3: biztonságos Azure-erőforrás-konfigurációk karbantartása

**Útmutató**: az Azure-erőforrások biztonságos beállításainak betartatásához használja a Azure Policy [deny] és a [telepítés ha nem létezik] lehetőséget.

* [Azure Policy konfigurálása és kezelése](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

* [Azure Policy effektusok ismertetése](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="74-maintain-secure-operating-system-configurations"></a>7,4: az operációs rendszer biztonságos konfigurációjának fenntartása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="75-securely-store-configuration-of-azure-resources"></a>7,5: az Azure-erőforrások biztonságos tárolása

**Útmutató**: ha egyéni Azure Policy-definíciókat használ, az Azure DevOps vagy az Azure Repos segítségével biztonságosan tárolhatja és kezelheti a kódot.

* [Kód tárolása az Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

* [Az Azure Repos dokumentációja](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="76-securely-store-custom-operating-system-images"></a>7,6: az egyéni operációsrendszer-lemezképek biztonságos tárolása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7,7: az Azure-erőforrások konfigurációs felügyeleti eszközeinek üzembe helyezése

**Útmutató**: nem alkalmazható; Az Azure szinapszis SQL nem rendelkezik konfigurálható biztonsági beállításokkal.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7,8: konfigurációs felügyeleti eszközök telepítése operációs rendszerekhez

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7,9: automatikus konfigurációs monitorozás megvalósítása Azure-erőforrásokhoz

**Útmutató**: a Azure Security Center kihasználása a szinapszis SQL-készlethez kapcsolódó erőforrások alapkonfigurációjának vizsgálatához.

* [Javaslatok szervizelése Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7,10: az operációs rendszerek automatikus konfiguráció-figyelésének megvalósítása

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="711-manage-azure-secrets-securely"></a>7,11: az Azure-titkok biztonságos kezelése

**Útmutató**: TRANSZPARENS ADATTITKOSÍTÁS (TDE) az ügyfél által felügyelt kulcsokkal Azure Key Vault lehetővé teszi az automatikusan generált adatbázis-titkosítási kulcs (adattitkosítási kulcsot) titkosítását az ügyfél által felügyelt, TDE Protector nevű aszimmetrikus kulccsal. Ezt általában a transzparens adattitkosítás Bring Your Own Key (BYOK) támogatása is említi. A BYOK forgatókönyvben a TDE-védőt az ügyfél és a felügyelt Azure Key Vault tárolja. Továbbá győződjön meg arról, hogy a Soft delete engedélyezve van a Azure Key Vaultban.

* [A TDE engedélyezése az ügyfél által felügyelt kulccsal Azure Key Vault](https://docs.microsoft.com/azure/azure-sql/database/transparent-data-encryption-byok-configure?tabs=azure-powershell)

* [A Soft delete engedélyezése Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="712-manage-identities-securely-and-automatically"></a>7,12: az identitások biztonságos és automatikus kezelése

**Útmutató**: felügyelt identitások használata az automatikusan felügyelt identitással rendelkező Azure-szolgáltatások biztosításához Azure Active Directory (ad). A felügyelt identitások lehetővé teszik bármely olyan szolgáltatás hitelesítését, amely támogatja az Azure AD-hitelesítést, beleértve a Azure Key Vault is, a kódban szereplő hitelesítő adatok nélkül.

* [Oktatóanyag: Hozzáférés az Azure SQL-hez egy Windows VM rendszer által hozzárendelt felügyelt identitásával](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-sql)

* [Felügyelt identitások konfigurálása](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

**Azure Security Center figyelés**: jelenleg nem érhető el

**Felelősség**: ügyfél

### <a name="713-eliminate-unintended-credential-exposure"></a>7,13: a hitelesítő adatok nem szándékolt expozíciójának megszüntetése

**Útmutató**: hitelesítő adatok beolvasása a kódban a hitelesítő adatok azonosításához. A hitelesítő adatok beolvasása azt is javasolja, hogy a felderített hitelesítő adatokat biztonságosabb helyszínekre (például Azure Key Vault) helyezze.

* [A hitelesítő adatok beolvasójának beállítása](https://secdevtools.azurewebsites.net/helpcredscan.html)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="malware-defense"></a>Kártevők elleni védelem

*További információkért lásd [: biztonsági ellenőrzés: kártevők elleni védelem](/azure/security/benchmarks/security-control-malware-defense).*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8,1: központilag felügyelt kártevő szoftverek használata

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8,2: a nem számítási Azure-erőforrásokra feltöltött fájlok előzetes vizsgálata

**Útmutató**: a Microsoft kártevő szoftverrel való ellátása engedélyezve van az Azure-szolgáltatásokat támogató mögöttes gazdagépen (például Azure szinapszis SQL); azonban nem fut az ügyfél tartalmán.

A nem számítási Azure-erőforrásokra feltöltött tartalmak előzetes vizsgálata, például App Service, Data Lake Storage, Blob Storage, Azure SQL Server stb. A Microsoft nem fér hozzá az adataihoz ezekben a példányokban.

* [Megismerheti a Microsoft antimalware-t az Azure Cloud Services és Virtual Machines](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>8,3: a kártevő szoftverek és az aláírások frissítésének ellenőrzése

**Útmutató**: nem alkalmazható; Ez a javaslat számítási erőforrások számára készült. A Microsoft kezeli a kártevő szoftvereket a mögöttes platformon.

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: nem alkalmazható

## <a name="data-recovery"></a>Adat-helyreállítás

*További információkért lásd [: biztonsági ellenőrzés: adat-helyreállítás](/azure/security/benchmarks/security-control-data-recovery).*

### <a name="91-ensure-regular-automated-back-ups"></a>9,1: rendszeres automatizált biztonsági másolatok biztosítása

**Útmutató**: a szinapszis SQL-készlet pillanatképeit a rendszer a nap folyamán automatikusan elvégzi a hét napig elérhető visszaállítási pontok létrehozásakor. Ez a megőrzési időszak nem módosítható. Az SQL-készlet támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül. Vegye figyelembe, hogy szükség esetén manuálisan is aktiválhatja a pillanatképeket.

* [Biztonsági mentés és visszaállítás az Azure szinapszis SQL-készletben](/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9,2: teljes rendszerbiztonsági mentés és minden ügyfél által felügyelt kulcs biztonsági mentése

**Útmutató**: az adattárház pillanatképeit a rendszer a nap folyamán automatikusan hozza létre a hét napig elérhető visszaállítási pontok létrehozásához. Ez a megőrzési időszak nem módosítható. Az SQL-készlet támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül. Vegye figyelembe, hogy szükség esetén manuálisan is aktiválhatja a pillanatképeket.

Ha ügyfél által felügyelt kulcsot használ az adatbázis-titkosítási kulcs titkosításához, győződjön meg arról, hogy a kulcs biztonsági mentése folyamatban van.

* [Biztonsági mentés és visszaállítás az Azure szinapszis SQL-készletben](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Azure Key Vault kulcsok biztonsági mentése](https://docs.microsoft.com/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: megosztott

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9,3: az összes biztonsági másolat ellenőrzése, beleértve az ügyfél által felügyelt kulcsokat

**Útmutató**: a visszaállítási pontok rendszeres tesztelése a pillanatképek érvényességének biztosítása érdekében. Egy meglévő SQL-készlet visszaállítási pontról történő visszaállításához használhatja a Azure Portal vagy a PowerShellt. Tesztelje az ügyfél által felügyelt kulcsok biztonsági mentésének visszaállítását.

* [Azure Key Vault kulcsok visszaállítása](https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0)

* [Biztonsági mentés és visszaállítás az Azure szinapszis SQL-készletben](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/backup-and-restore)

* [Meglévő SQL-készlet visszaállítása](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/sql-data-warehouse-restore-active-paused-dw)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9,4: a biztonsági másolatok és az ügyfél által felügyelt kulcsok védelmének biztosítása

**Útmutató**: a Azure SQL Database egyetlen vagy készletezett adatbázist is beállíthat hosszú távú biztonsági mentési adatmegőrzési szabályzattal (ltr), amellyel az adatbázis biztonsági másolatait külön Azure Blob Storage-tárolókban, akár 10 évig is megtarthatja. Az Azure Storage-ban tárolt adatai a 256 bites AES-titkosítással vannak titkosítva és visszafejtve, és az egyik legerősebb blokk titkosítási algoritmus, amely az FIPS 140-2-kompatibilis.

Alapértelmezés szerint a Storage-fiókokban lévő adatforgalom a Microsoft által felügyelt kulcsokkal van titkosítva. Hivatkozhat a Microsoft által felügyelt kulcsokra az adatok titkosításához, vagy kezelheti a titkosítást a saját kulcsaival. Ha Key Vault használatával felügyeli a saját kulcsait, győződjön meg arról, hogy a helyreállítható törlés engedélyezve van.

* [A biztonsági másolatok hosszú távú megőrzésének Azure SQL Database kezelése](https://docs.microsoft.com/azure/sql-database/sql-database-long-term-backup-retention-configure)

* [Inaktív adatok Azure Storage-titkosítása](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

* [A Soft delete engedélyezése Key Vault](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal)

**Azure Security Center figyelés**: N/A

**Felelősség**: ügyfél

## <a name="incident-response"></a>Incidensmegoldás

*További információ [: Security Control: incidens válasza](/azure/security/benchmarks/security-control-incident-response).*

### <a name="101-create-an-incident-response-guide"></a>10,1: incidens-válaszi útmutató létrehozása

**Útmutató**: Ellenőrizze, hogy vannak-e írásos incidensekre vonatkozó válaszok, amelyek meghatározzák a személyzet szerepköreit, valamint az incidensek kezelésének és kezelésének fázisait.

* [Munkafolyamat-automatizálás konfigurálása Azure Security Centeron belül](https://docs.microsoft.com/azure/security-center/security-center-planning-and-operations-guide)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10,2: incidensek pontozásának és rangsorolási eljárásának létrehozása

**Útmutató**: a Security Center súlyosságot rendel a riasztásokhoz, így könnyebben rangsorolhatja az egyes riasztásokban való részvétel sorrendjét, hogy az erőforrások biztonsága esetén azonnal elérhető legyen. A súlyosság azon alapul, hogy az Security Center milyen mértékben szerepel a riasztás kibocsátásához használt elemzésben vagy elemzésben, valamint azt a megbízhatósági szintet, amely a riasztáshoz vezető tevékenység mögött rosszindulatú szándékú volt.

* [Biztonsági riasztások az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-alerts-overview)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="103-test-security-response-procedures"></a>10,3: biztonsági reagálási eljárások tesztelése

**Útmutató**: a rendszerek incidensek reagálási képességeinek rendszeres tesztelésére szolgáló gyakorlatok végrehajtása. Azonosítsa a gyenge pontokat és a réseket, és szükség szerint módosítsa a tervet.

* [Tekintse meg a NIST kiadványát: útmutató az IT-csomagok és-képességek teszteléséhez, betanításához és edzésprogramjának kidolgozásához](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10,4: biztonsági incidensek elérhetőségének biztosítása és riasztási értesítések konfigurálása biztonsági incidensekhez

**Útmutató**: a Microsoft a biztonsági incidensek elérhetőségi adatait arra használja fel, hogy felvegye Önnel a kapcsolatot, ha a Microsoft Security Response Center (MSRC) felfedi, hogy az adatokat egy törvénytelen vagy jogosulatlan fél is hozzáférte.

* [A Azure Security Center biztonsági kapcsolattartó beállítása](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details)

**Azure Security Center figyelés**: igen

**Felelősség**: ügyfél

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10,5: biztonsági riasztások beépítése az incidensek gyorsreagáló rendszerébe

**Útmutató**: az Azure Security Center-riasztások és-javaslatok exportálása a folyamatos exportálás funkció használatával. A folyamatos exportálás lehetővé teszi a riasztások és javaslatok manuális és folyamatos exportálását. Használhatja a Azure Security Center adatösszekötőt a riasztások a Sentinelbe való továbbításához.

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

*További információkért lásd [: biztonsági ellenőrzés: behatolási tesztek és Red Team-gyakorlatok](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11,1: az Azure-erőforrások rendszeres behatolásának tesztelése, valamint az összes kritikus biztonsági vizsgálat szervizelésének biztosítása

**Útmutató**: * [kérjük, kövesse a Microsoft részvételi szabályait, hogy a behatolási tesztek ne sértsék a Microsoft-házirendeket](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1.)

* [A Microsoft által felügyelt felhő-infrastruktúrával,-szolgáltatásokkal és-alkalmazásokkal kapcsolatos, a Microsoft által kezelt felhőalapú infrastruktúra, szolgáltatások](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Azure Security Center figyelés**: nem alkalmazható

**Felelősség**: ügyfél

## <a name="next-steps"></a>Következő lépések

- Lásd az [Azure biztonsági teljesítménytesztét](/azure/security/benchmarks/overview)
- További információ az [Azure biztonsági alaptervekről](/azure/security/benchmarks/security-baselines-overview)
