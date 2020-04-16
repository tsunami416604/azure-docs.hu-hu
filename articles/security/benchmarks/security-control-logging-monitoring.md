---
title: Azure Security Control – naplózás és figyelés
description: Az Azure security control naplózása és figyelése
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: e58df0997c62a131a3c9987369f8e076a62d6654
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408345"
---
# <a name="security-control-logging-and-monitoring"></a>Biztonsági ellenőrzés: Naplózás és figyelés

A biztonsági naplózás és figyelés az Azure-szolgáltatások naplózási naplóinak engedélyezésével, beolvasásával és tárolásával kapcsolatos tevékenységekre összpontosít.

## <a name="21-use-approved-time-synchronization-sources"></a>2.1: Jóváhagyott időszinkronizációs források használata

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.1 | 6.1 | Microsoft |

A Microsoft fenntartja az Azure-erőforrások időforrásait, azonban lehetősége van a számítási erőforrások időszinkronizálási beállításainak kezelésére.

- [Az Azure Windows számítási erőforrásainak időszinkronizálásának konfigurálása](https://docs.microsoft.com/azure/virtual-machines/windows/time-sync)

- [Az Azure Linux számítási erőforrásainak időszinkronizálásának konfigurálása](https://docs.microsoft.com/azure/virtual-machines/linux/time-sync)

## <a name="22-configure-central-security-log-management"></a>2.2: A központi biztonsági napló kezelésének konfigurálása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2,2 | 6.5, 6.6 | Ügyfél |

A naplók betöltése az Azure Monitoron keresztül a végponteszközök, hálózati erőforrások és más biztonsági rendszerek által generált biztonsági adatok összesítéséhez. Az Azure Monitoron belül a Log Analytics-munkaterület(ek) segítségével lekérdezheti és elvégezheti az elemzéseket, és használhatja az Azure Storage-fiókokat a hosszú távú/archiválási tároláshoz.

Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM. 

- [Az Azure Sentinel fedélzeti szolgáltatása](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [Platformnaplók és mérőszámok gyűjtése az Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [Az Azure Virtual Machine belső gazdagép-naplóinak összegyűjtése az Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Az Azure Monitor és a külső SIEM-integráció első lépései](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

## <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Az Azure-erőforrások naplózásának engedélyezése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.3 | 6.2, 6.3 | Ügyfél |

Engedélyezze az Azure-erőforrások diagnosztikai beállításait a naplózási, biztonsági és diagnosztikai naplók eléréséhez. Az automatikusan elérhető tevékenységnaplók közé tartozik az eseményforrás, a dátum, a felhasználó, az időbélyeg, a forráscímek, a célcímek és egyéb hasznos elemek.

- [Platformnaplók és mérőszámok gyűjtése az Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings)

- [A naplózás és a különböző naplótípusok ismertetése az Azure-ban](https://docs.microsoft.com/azure/azure-monitor/platform/platform-logs-overview)

## <a name="24-collect-security-logs-from-operating-systems"></a>2.4: Biztonsági naplók gyűjtése az operációs rendszerekről

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.4 | 6.2, 6.3 | Ügyfél |

Ha a számítási erőforrás a Microsoft tulajdonában van, akkor a Microsoft felelős a figyelésért. Ha a számítási erőforrás a szervezet tulajdonában van, az Ön felelőssége, hogy figyelje azt. Az Azure Security Center segítségével figyelheti az operációs rendszert. A Security Center által az operációs rendszerből gyűjtött adatok közé tartozik az operációs rendszer típusa és verziója, az operációs rendszer (Windows eseménynaplók), a futó folyamatok, a számítógép neve, az IP-címek és a bejelentkezett felhasználó. A Log Analytics Ügynök összeomlási memóriaképfájlokat is gyűjt.

- [Az Azure Virtual Machine belső gazdagép-naplóinak összegyűjtése az Azure Monitor segítségével](https://docs.microsoft.com/azure/azure-monitor/learn/quick-collect-azurevm)

- [Az Azure Security Center adatgyűjtésének megismerése](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection)

## <a name="25-configure-security-log-storage-retention"></a>2.5: A biztonsági napló tárolásának megőrzése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.5 | 6.4 | Ügyfél |

Az Azure Monitoron belül állítsa be a Log Analytics-munkaterület-megőrzési időszakot a szervezet megfelelőségi előírásainak megfelelően. Azure Storage-fiókok használata hosszú távú/archiválási tároláshoz.

- [Az adatmegőrzési időszak módosítása a Log Analytics szolgáltatásban](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

- [Az Azure Storage-fiók naplóinak adatmegőrzési házirendjének konfigurálása](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account#configure-logging)

## <a name="26-monitor-and-review-logs"></a>2.6: Naplók figyelése és felülvizsgálata

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2,6 | 6.7 | Ügyfél |

Elemezze és figyelje a naplókat a rendellenes viselkedéshez, és rendszeresen tekintse át az eredményeket. Az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket a naplóadatokon.

Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel vagy egy harmadik fél SIEM. 

- [Az Azure Sentinel fedélzeti szolgáltatása](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [A Log Analytics-munkaterület ismertetése](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)

- [Egyéni lekérdezések végrehajtása az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

## <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Riasztás engedélyezése rendellenes tevékenységekhez

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.7 | 6.8 | Ügyfél |

Használja az Azure Security Center taloganalytics-munkaterület et a biztonsági naplókban és eseményekben található rendellenes tevékenységek figyelésére és riasztására.

Másik lehetőségként engedélyezheti és a fedélzeti adatok at Azure Sentinel.

- [Az Azure Sentinel fedélzeti szolgáltatása](https://docs.microsoft.com/azure/sentinel/quickstart-onboard)

- [A riasztások kezelése az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts)

- [Riasztás a naplóelemzési napló adatairól](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response)

## <a name="28-centralize-anti-malware-logging"></a>2.8: A kártevőirtó naplózás központosítása

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2,8 | 8.6 | Ügyfél |

Engedélyezze a kártevőirtó eseménygyűjteményt az Azure virtuális gépekhez és a felhőszolgáltatásokhoz.

- [A Microsoft Kártevőirtó szolgáltatás konfigurálása virtuális gépekhez](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azurevmmicrosoftantimalwareextension?view=azuresmps-4.0.0)

- [A Microsoft Kártevőirtó szolgáltatás konfigurálása a felhőszolgáltatásokhoz](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureserviceantimalwareextension?view=azuresmps-4.0.0)

- [A Microsoft kártevőirtó ismertetése](https://docs.microsoft.com/azure/security/fundamentals/antimalware)

## <a name="29-enable-dns-query-logging"></a>2.9: Dns-lekérdezésnaplózás engedélyezése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.9 | 8.7 | Ügyfél |

Harmadik féltől származó megoldást valósítson meg az Azure Marketplace-en a DNS-naplózási megoldáshoz, ahogy azt a szervezeteknek szükségük van rá.  

## <a name="210-enable-command-line-audit-logging"></a>2.10: Parancssori naplózás engedélyezése

| Azure-azonosító | CIS-azonosítók | Felelősség |
|--|--|--|
| 2.10 | 8.8 | Ügyfél |

A Microsoft Monitoring Agent használatával az összes támogatott Azure Windows virtuális gépek naplózni a folyamat létrehozása esemény és a CommandLine mező.   A támogatott Azure Linux virtuális gépek, manuálisan konfigurálhatja a konzol naplózása csomópontonkénti alapon, és a Syslog az adatok tárolására.  Emellett az Azure Monitor Log Analytics-munkaterületével tekintse át a naplókat, és hajtson végre lekérdezéseket az Azure virtuális gépekről naplózott adatokon. 

- [Adatgyűjtés az Azure Security Centerben](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

- [Egyéni lekérdezések végrehajtása az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries)

- [Rendszernapló-adatforrások az Azure Monitorban](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog)


## <a name="next-steps"></a>További lépések

- A következő biztonsági vezérlő: [Identitás- és hozzáférés-vezérlés](security-control-identity-access-control.md)