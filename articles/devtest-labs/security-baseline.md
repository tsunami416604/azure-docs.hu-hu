---
title: Azure DevTest Labs Azure biztonsági alapterve
description: Azure DevTest Labs Azure biztonsági alapterve
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 4cdfab5df2e97c2bd39c1c104e9552fb940f24d9
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098515"
---
# <a name="azure-security-baseline-for-azure-devtest-labs"></a>Azure DevTest Labs Azure biztonsági alapterve

Az Azure DevTest Labs Azure biztonsági alapkonfigurációja olyan javaslatokat tartalmaz, amelyek segítségével javíthatja az üzemelő példány biztonsági állapotát.

A szolgáltatás alapterve az [Azure Security Benchmark 1,0-es verziójából](https://docs.microsoft.com/azure/security/benchmarks/overview)származik, amely javaslatokat nyújt az Azure-beli felhőalapú megoldások biztonságossá tételéhez.

További információ: [Azure Security](https://docs.microsoft.com/azure/security/benchmarks/security-baselines-overview)alapkonfigurációk áttekintése.

## <a name="logging-and-monitoring"></a>Naplózás és monitorozás

*További információ [: Security Control: naplózás és figyelés](https://docs.microsoft.com/azure/security/benchmarks/security-control-logging-monitoring).*

### <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata
**Útmutató:** A Microsoft az Azure-erőforrások időforrásait is fenntartja. Azonban a számítási erőforrások időszinkronizálási beállításait is kezelheti.

A következő cikkből megtudhatja, hogyan konfigurálhatja az időszinkronizálást az Azure számítási erőforrásaihoz: [a Windows rendszerű virtuális gépek időszinkronizálása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync). 

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Microsoft

### <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása
**Útmutató:** Engedélyezze az Azure-műveletnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. A Tevékenységnaplók betekintést nyújtanak a Azure DevTest Labs példányain végrehajtott műveletekre a felügyeleti sík szintjén. Az Azure-tevékenység naplójának adatai alapján meghatározhatja a "mit, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az DevTest Labs-példányok felügyeleti síkja szintjén.

További információ: [diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése
**Útmutató:** Engedélyezze az Azure-műveletnapló diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre, az Azure Event hub vagy az Azure Storage-fiókba az archívumhoz. A Tevékenységnaplók betekintést nyújtanak a Azure DevTest Labs példányain végrehajtott műveletekre a felügyeleti sík szintjén. Az Azure-tevékenység naplójának adatai alapján meghatározhatja a "mit, ki és mikor" típusú írási műveleteket (PUT, POST, DELETE) az DevTest Labs-példányok felügyeleti síkja szintjén.

További információ: [diagnosztikai beállítások létrehozása a platform naplófájljainak és metrikáinak különböző célhelyekre küldéséhez](../azure-monitor/platform/diagnostic-settings.md).

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből
**Útmutató:** Azure DevTest Labs virtuális gépeket az ügyfél hozza létre és birtokolja. Tehát a szervezet feladata a figyelés. A számítási operációs rendszer figyeléséhez Azure Security Center is használhatja. Az operációs rendszer Security Center által összegyűjtött adatok közé tartozik az operációs rendszer típusa és verziója, az operációs rendszer (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is gyűjti.

További információkért tekintse át a következő cikkeket: 

- [Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)
- [Az Azure Security Center adatgyűjtés ismertetése](../security-center/security-center-enable-data-collection.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

### <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása
***Útmutató:** A Azure Monitor a szervezet megfelelőségi előírásai szerint állítsa be a Azure DevTest Labs példányokhoz társított Log Analytics-munkaterületek naplózásának megőrzési időtartamát.

További információ: a [napló megőrzési paramétereinek beállítása](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period) .

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése
**Útmutató:** Engedélyezze az Azure-tevékenység naplójának diagnosztikai beállításait, és küldje el a naplókat egy Log Analytics munkaterületre. Log Analytics lekérdezéseket futtathat a kifejezésekben, azonosíthatja a trendeket, elemezheti a mintákat, és számos más elemzést is elvégezhet a Azure DevTest Labs számára összegyűjtött tevékenységi naplók alapján.

További információkért tekintse át a következő cikkeket:

- [Diagnosztikai beállítások engedélyezése az Azure-beli tevékenység naplójában](../azure-monitor/platform/diagnostic-settings.md)
- [Azure-Tevékenységnaplók összegyűjtése és elemzése Log Analytics munkaterületen Azure Monitor](../azure-monitor/platform/activity-log.md)

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez
**Útmutató:** Az Azure Log Analytics munkaterület segítségével figyelheti és riasztást tehet a rendellenes tevékenységekről a biztonsági naplókban és a Azure DevTest Labs kapcsolatos eseményekben.

További információkért tekintse meg a következő cikket: [riasztás a log Analytics-naplózási adatokról](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center figyelés:** Jelenleg nem érhető el

**Felelősség:** Ügyfél

### <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása
**Útmutató:** Nem alkalmazható. Azure DevTest Labs nem dolgoz fel kártevő szoftverrel kapcsolatos naplókat, illetve nem hoz létre.

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése
**Útmutató:** Nem alkalmazható. A Azure DevTest Labs nem dolgozza fel a DNS-sel kapcsolatos naplókat, és nem hoz létre.

**Azure Security Center figyelés:** Nem alkalmazható

**Felelősség:** Ügyfél

### <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése
**Útmutató:** A Azure DevTest Labs az ügyfél által birtokolt és felügyelt Azure számítási gépeket hoz létre. A folyamat-létrehozási esemény és a parancssori mező naplózásához használja a Microsoft monitoring agentet az összes támogatott Azure Windows-beli virtuális gépen. A támogatott Azure Linux rendszerű virtuális gépek esetében a konzol naplózását manuálisan is konfigurálhatja a csomópontok alapján, és a syslog használatával tárolhatja az adattárakat. Emellett a Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket futtathat az Azure Virtual Machines szolgáltatásból származó naplózott adatokon.

- [Adatgyűjtés az Azure Security Centerben](../security-center/security-center-enable-data-collection.md#data-collection-tier)
- [Egyéni lekérdezések futtatása a Azure Monitorban](../azure-monitor/log-query/get-started-queries.md)
- [Rendszernapló-adatforrások az Azure Monitorban](../azure-monitor/platform/data-sources-syslog.md)

**Azure Security Center figyelés:** igen

**Felelősség:** Ügyfél

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikket:

- [Biztonsági riasztások a Azure DevTest Labs környezetekben](environment-security-alerts.md)