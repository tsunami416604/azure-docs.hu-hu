---
title: Azure Security Control – naplózás és figyelés
description: Biztonsági vezérlés naplózása és figyelése
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: ae9c678d9dfca895ec74ed92bcb1b541db6b134e
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76545500"
---
# <a name="security-control-logging-and-monitoring"></a>Biztonsági ellenőrzés: naplózás és figyelés

A biztonsági naplózás és a monitorozás az Azure-szolgáltatások naplófájljainak engedélyezésével, beszerzésével és tárolásával kapcsolatos tevékenységekre koncentrál.

## <a name="21-use-approved-time-synchronization-sources"></a>2,1: a jóváhagyott idő-szinkronizálási források használata

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft az Azure-erőforrások időforrásait is fenntartja, azonban lehetősége van a számítási erőforrások időszinkronizálási beállításainak kezelésére.

Az Azure számítási erőforrások időszinkronizálásának konfigurálása:

https://docs.microsoft.com/azure/virtual-machines/windows/time-sync

## <a name="22-configure-central-security-log-management"></a>2,2: a központi biztonsági naplók felügyeletének konfigurálása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2,2 | 6,5, 6,6 | Ügyfél |

Naplók betöltése Azure Monitor használatával a végponti eszközök, a hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatokat összesítve. A Azure Monitoron belül Log Analytics munkaterület (ek) használatával kérdezheti le és végezheti el az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik féltől származó SIEM-et. Az Azure Sentinel előkészítése:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Platform-naplók és-metrikák összegyűjtése a Azure Monitor használatával:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése a Azure Monitor használatával:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Ismerkedés a Azure Monitor és a harmadik féltől származó SIEM-integrációval:

https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/

## <a name="23-enable-audit-logging-for-azure-resources"></a>2,3: az Azure-erőforrások naplózásának engedélyezése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2.3 | 6,2, 6,3 | Ügyfél |

Az Azure-erőforrások diagnosztikai beállításainak engedélyezése a naplózási, biztonsági és diagnosztikai naplókhoz való hozzáféréshez. A automatikusan elérhető tevékenység-naplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscím, a célcím és más hasznos elemek.

Platform-naplók és-metrikák összegyűjtése a Azure Monitor használatával:

https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings

A naplózás és a különböző naplózási típusok ismertetése az Azure-ban:

https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview

## <a name="24-collect-security-logs-from-operating-systems"></a>2,4: biztonsági naplók gyűjtése az operációs rendszerekből

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2.4 | 6,2, 6,3 | Ügyfél |

Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft feladata a figyelése. Ha a számítási erőforrás a szervezet tulajdonában van, akkor az Ön felelőssége, hogy figyelje. A Azure Security Center az operációs rendszer figyelésére használható. Az operációs rendszer Security Center által összegyűjtött adatok közé tartozik az operációs rendszer típusa és verziója, az operációsrendszer-naplók (Windows-eseménynaplók), a futó folyamatok, a gép neve, az IP-címek és a bejelentkezett felhasználó. A Log Analytics ügynök az összeomlási memóriaképek fájljait is gyűjti.

Az Azure-beli virtuális gépek belső gazdagép-naplóinak összegyűjtése a Azure Monitor használatával:

https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm

Azure Security Center adatgyűjtés ismertetése:

https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection

## <a name="25-configure-security-log-storage-retention"></a>2,5: a biztonsági napló tárolási adatmegőrzésének konfigurálása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2,5 | 6.4 | Ügyfél |

Azure Monitor belül állítsa be a Log Analytics munkaterület megőrzési időszakát a szervezet megfelelőségi szabályainak megfelelően. Használja az Azure Storage-fiókokat hosszú távú/archiválási tároláshoz.

Log Analytics-munkaterületek naplózási megőrzési paramétereinek beállítása:

https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period

## <a name="26-monitor-and-review-logs"></a>2,6: naplók figyelése és áttekintése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2,6 | 6.7 | Ügyfél |

Elemezheti és figyelheti a rendellenes viselkedésre és a rendszeres eredményekre vonatkozó naplókat. Az Azure Monitor Log Analytics munkaterülete segítségével tekintheti át a naplókat, és lekérdezéseket végezhet a naplózási adatokon.

Alternatív megoldásként engedélyezheti és elvégezheti az Azure Sentinel vagy egy harmadik fél SIEM-nek. 

Az Azure Sentinel előkészítése:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Log Analytics munkaterület ismertetése:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal

Egyéni lekérdezések végrehajtása a Azure Monitorban:

https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries

## <a name="27-enable-alerts-for-anomalous-activity"></a>2,7: riasztások engedélyezése rendellenes tevékenységhez

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2,7 | 6.8 | Ügyfél |

A biztonsági naplókban és eseményekben talált rendellenes tevékenységekkel kapcsolatos figyeléshez és riasztáshoz használja a Azure Security Center Log Analytics munkaterülettel.

Alternatív megoldásként engedélyezheti és elvégezheti a fedélzeti adatfeldolgozást az Azure Sentinel szolgáltatásban.

Az Azure Sentinel előkészítése:

https://docs.microsoft.com/azure/sentinel/quickstart-onboard

Riasztások kezelése Azure Security Centerban:

https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts

Riasztás a log Analytics-naplófájlok adatkezeléséről:

https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response

## <a name="28-centralize-anti-malware-logging"></a>2,8: kártevő szoftverek közötti naplózás központosítása

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2,8 | 8,6 | Ügyfél |

Az Azure-Virtual Machines és Cloud Services a kártevők elleni események gyűjtésének engedélyezése.

A Microsoft antimalware konfigurálása a Virtual Machineshoz:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0

A Microsoft antimalware konfigurálása a Cloud Serviceshoz:

https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0

A Microsoft antimalware megismerése:

https://docs.microsoft.com/azure/security/fundamentals/antimalware

## <a name="29-enable-dns-query-logging"></a>2,9: DNS-lekérdezések naplózásának engedélyezése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2.9 | 8.7 | Ügyfél |

Külső gyártótól származó megoldás implementálása a DNS-naplózáshoz.

## <a name="210-enable-command-line-audit-logging"></a>2,10: parancssori naplózás engedélyezése

| Azure-azonosító | CIS-azonosítók | Feladatkörök |
|--|--|--|
| 2.1 | 8.8 | Ügyfél |

Konfigurálja manuálisan a konzol naplózását és a PowerShell átírását a csomópontok alapján.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő biztonsági vezérlőt: [identitás és Access Control](security-control-identity-access-control.md)
