---
title: Monitoring megoldások leltára az Azure-ban | Microsoft dokumentumok
description: Az Azure Monitor figyelési megoldásai logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körül elforgatott metrikákat biztosítanak.  Ez a cikk a Microsoft által elérhető figyelési megoldások listáját, valamint az adatgyűjtés módjának és gyakoriságának részleteit tartalmazza.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 7b88d957bce45bf518fc77584f1691de8010459a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663130"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Leltár- és adatgyűjtési részletek az Azure-beli figyelési megoldásokhoz
[A figyelési megoldások](solutions.md) az Azure-beli szolgáltatásokat használják ki, hogy további betekintést nyújtsanak egy adott alkalmazás vagy szolgáltatás működésébe. A figyelési megoldások általában naplóadatokat gyűjtenek, és lekérdezéseket és nézeteket biztosítanak az összegyűjtött adatok elemzéséhez. Figyelési megoldásokat adhat hozzá az Azure Monitorhoz a használt alkalmazásokhoz és szolgáltatásokhoz. Ezek általában ingyenesen elérhetők, de olyan adatokat gyűjtenek, amelyek használati díjakat hívhatnak meg.

Ez a cikk a Microsoft által elérhető [montioring megoldások](solutions.md) listáját tartalmazza, részletes dokumentációjukra mutató hivatkozásokat tartalmaz.  Emellett az Azure Monitorba történő adatgyűjtés módjáról és gyakoriságáról is tartalmaz információkat.  A cikkben található információk segítségével azonosíthatja a rendelkezésre álló különböző megoldásokat, és megismerheti a különböző figyelési megoldások adatfolyam- és kapcsolati követelményeit.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>A felügyeleti megoldások listája

Az alábbi táblázat a Microsoft által biztosított [Azure-figyelési megoldásokat](solutions.md) sorolja fel. Az oszlopban egy bejegyzés azt jelenti, hogy a megoldás adatokat gyűjt az Azure Monitor ezzel a módszerrel.  Ha egy megoldás nem rendelkezik kiválasztott oszlopokkal, majd közvetlenül az Azure Monitorba ír egy másik Azure-szolgáltatásból. További információkért kövesse az egyes hivatkozásokat a részletes dokumentációhoz.

Az oszlopok magyarázataa a következő:

- **Microsoft monitoring ügynök** – Ügynök használt Windows és Linux kezelésére felügyeleti csomag SCOM és figyelési megoldások az Azure-ból. Ebben a konfigurációban az ügynök közvetlenül csatlakozik az Azure Monitorhoz anélkül, hogy egy Operations Manager felügyeleti csoporthoz csatlakozna. 
- **Operations Manager** – azonos ügynök, mint a Microsoft figyelési ügynöke. Ebben a [konfigurációban egy Azure Monitorhoz csatlakoztatott Operations Manager felügyeleti csoporthoz csatlakozik.](../platform/om-agents.md) 
-  **Azure Storage** – A megoldás adatokat gyűjt egy Azure-tárfiókból. 
- **A műveleti vezetőre van szükség?** - A figyelési megoldás általvégzett adatgyűjtéshez csatlakoztatott Operations Manager felügyeleti csoport szükséges. 
- **Operations Manager ügynök adatok at felügyeleti csoporton keresztül küldött** – Ha az ügynök [csatlakozik egy SCOM felügyeleti csoport,](../platform/om-agents.md)majd az adatok at az Azure Monitor a felügyeleti kiszolgálóról. Ebben az esetben az ügynök nem kell közvetlenül csatlakoznia az Azure Monitorhoz. Ha ez a jelölőnégyzet nincs bejelölve, akkor az adatokat az ügynök közvetlenül az Azure Monitorra küldi, még akkor is, ha az ügynök egy SCOM felügyeleti csoporthoz csatlakozik. Képesnek kell lennie az Azure Monitornak a [Log Analytics-átjárón](../platform/gateway.md)keresztül történő kommunikációra.
- **A gyűjtés gyakorisága** – Megadja a felügyeleti megoldás által gyűjtött adatok gyakoriságát. 



| **Felügyeleti megoldás** | **Platform** | **Microsoft figyelési ügynök** | **Operations Manager-ügynök** | **Azure-tárhely** | **A műveleti vezetőre van szükség?** | **Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül** | **A gyűjtés gyakorisága** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Tevékenységnapló-elemzés](../platform/activity-log-collect.md) | Azure | | | | | | a bejelentésről |
| [AD-elemzés](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [AD Replication Status](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 nap |
| [Az ügynök állapota](solution-agenthealth.md) | Windows és Linux | &#8226; | &#8226; | | | &#8226; | 1 perc |
| [Riasztáskezelés](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |érkezéskor |
| [Riasztáskezelés](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 perc |
| [Riasztáskezelés](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 perc |
| [Az Azure webhely helyreállítása](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights-összekötő (elavult)](../platform/app-insights-connector.md) | Azure | | | |  |  | a bejelentésről |
| [Automatizáláshibrid dolgozó](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | a bejelentésről |
| **Felügyeleti megoldás** | **Platform** | **Microsoft figyelési ügynök** | **Operations Manager-ügynök** | **Azure-tárhely** | **A műveleti vezetőre van szükség?** | **Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül** | **A gyűjtés gyakorisága** |
| [Az Azure Network Security Group Analytics (elavult)](azure-networking-analytics.md) | Azure |  |  |  |  |  | a bejelentésről |
| [Azure SQL Analytics (előzetes verzió)](azure-sql.md) | Windows | | | | | | 1 perc |
| [Biztonsági mentés](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | a bejelentésről |
| [Kapacitás és teljesítmény (előzetes verzió)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |érkezéskor |
| [Változáskövetés](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Változik](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Változáskövetés](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Változik](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Tárolók](containers.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 3 perc |
| [Key Vault-elemzés](azure-key-vault.md) |Windows | | | | | |a bejelentésről |
| [Kártevőfelmérés](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |Óránkénti |
| [Hálózati teljesítményfigyelő](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP kézfogás 5 másodpercenként, az adatok küldött 3 percenként |
| [Office 365 Analytics (előzetes verzió)](solution-office-365.md) |Windows | | | | | |a bejelentésről |
| **Felügyeleti megoldás** | **Platform** | **Microsoft figyelési ügynök** | **Operations Manager-ügynök** | **Azure-tárhely** | **A műveleti vezetőre van szükség?** | **Az Operations Manager ügynöke által küldött adatok a felügyeleti csoporton keresztül** | **A gyűjtés gyakorisága** |
| [Service Fabric-elemzés](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 perc |
| [Szolgáltatástérkép](service-map.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 15 másodperc |
| [SQL-elemzés](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |érkezéskor |
| [System Center Operations Manager értékelése (előzetes verzió)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | hét nap |
| [Frissítéskezelés](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |legalább naponta kétszer és 15 perccel a frissítés telepítése után |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 nap |
| [VMware monitoring (elavult)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 perc |
| [Vezetékadatok 2.0 (előzetes verzió)](wire-data.md) |Windows (2012 R2 / 8.1 vagy újabb) |&#8226; |&#8226; | | | | 1 perc |




## <a name="next-steps"></a>További lépések
* További információ a [figyelési megoldások telepítéséről és használatáról.](solutions.md)
* Ismerje meg, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.
