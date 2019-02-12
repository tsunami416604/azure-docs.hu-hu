---
title: Adatok gyűjtése részletei felügyeleti megoldások az Azure-ban |} A Microsoft Docs
description: Felügyeleti megoldások az Azure-ban problémakörrel kapcsolatos egy adott területre metrikákat logikai, megjelenítési és adatgyűjtési szabályok gyűjteményei.  Ez a cikk a Microsoft és a metódus és adatok gyűjtésének gyakorisága elérhető kezelési megoldások listáját tartalmazza.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: aac58cc0887c566c7377edf08f5a86e2d12cdf28
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55993233"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Adatok gyűjtése részletei felügyeleti megoldások az Azure-ban
Ez a cikk tartalma [felügyeleti megoldások](solutions.md) elérhető a Microsoft mutató hivatkozásokat tartalmaz a részletes dokumentációt.  A metódus és az Azure Monitor szolgáltatásba adatok gyűjtésének gyakorisága információkat is biztosít.  Használhatja az információkat ebben a cikkben, azonosíthatja a különféle elérhető megoldások és más felügyeleti megoldásokkal data flow és a kapcsolat követelményeinek megismeréséhez. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-management-solutions"></a>Felügyeleti megoldások listája

A következő táblázat felsorolja a [felügyeleti megoldások](solutions.md) a Microsoft által biztosított Azure-ban. Egy bejegyzés az oszlopban azt jelenti, hogy, hogy a megoldás gyűjti az adatokat az Azure Monitor szolgáltatásba a módszer használatával.  Ha a megoldás nem tartalmaz oszlopokat kiválasztva, majd ír közvetlenül az Azure Monitor egy másik Azure szolgáltatás. A hivatkozásra vonatkozó további információ a részletes dokumentációt.

Magyarázat az oszlopok a következők:

- **A Microsoft-figyelőügynök** -ügynök futtatható a Management pack SCOM és felügyeleti megoldásokat az Azure-ból a Windows és Linux rendszereken használható. Ebben a konfigurációban az ügynök közvetlenül kapcsolódik az Azure Monitor az Operations Manager felügyeleti csoporthoz való csatlakozás nélkül. 
- **Az Operations Manager** -azonos a Microsoft monitoring agent ügynök. Ebben a konfigurációban van [egy Operations Manager felügyeleti csoporthoz csatlakoztatott](../../azure-monitor/platform/om-agents.md) , amely csatlakozik az Azure Monitor. 
-  **Az Azure Storage** -megoldás gyűjti az adatokat egy Azure storage-fiókot. 
- **Az Operations Manager szükséges?** -Az Operations Manager csatlakoztatott felügyeleti csoport felügyeleti megoldás által adatgyűjtés szükség. 
- **A felügyeleti csoport Operations Manager-ügynök adatok küldött** – Ha az ügynök [egy SCOM felügyeleti csoporthoz csatlakoztatott](../../azure-monitor/platform/om-agents.md), majd az adatokat az Azure monitornak is küld a felügyeleti kiszolgáló. Ebben az esetben az ügynök közvetlenül kapcsolódik az Azure Monitor nem szükséges. Ha a jelölőnégyzet nincs bejelölve, tartó adatküldést követően az ügynök közvetlenül az Azure Monitor akkor is, ha az ügynök csatlakozik egy SCOM felügyeleti csoport. El kell az Azure monitornak keresztül kommunikálnak a [Log Analytics-átjáró](../../azure-monitor/platform/gateway.md).
- **Gyűjtés gyakorisága** – adja meg, hogy a felügyeleti megoldás által összegyűjtött adatok. 



| **Felügyeleti megoldások** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [A Log analytics tevékenység](../../azure-monitor/platform/collect-activity-logs.md) | Azure | | | | | | az értesítés |
| [AD-elemzés](../../azure-monitor/insights/ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [AD-replikáció állapota](../../azure-monitor/insights/ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 nap |
| [Az ügynök állapota](solution-agenthealth.md) | Windows és Linux | &#8226; | &#8226; | | | &#8226; | 1 perc |
| [Riasztás felügyeleti](../../azure-monitor/platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |a beérkezéskor |
| [Riasztás felügyeleti](../../azure-monitor/platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 perc |
| [Riasztás felügyeleti](../../azure-monitor/platform/alert-management-solution.md) (az Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 perc |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [(Elavult az) Application Insights-összekötő](../../azure-monitor/platform/app-insights-connector.md) | Azure | | | |  |  | az értesítés |
| [Automation hibrid feldolgozó](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Az Azure Application Gateway Analytics](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| **Felügyeleti megoldások** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| [Azure-beli hálózati biztonsági csoport Analytics (elavult)](../../azure-monitor/insights/azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| [Az Azure SQL Analytics (előzetes verzió)](../../azure-monitor/insights/azure-sql.md) | Windows | | | | | | 1 perc |
| [Biztonsági mentés](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | az értesítés |
| [Kapacitás és teljesítmény (előzetes verzió)](../../azure-monitor/insights/capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |a beérkezéskor |
| [Változáskövetés](../../automation/automation-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Változáskövetés](../../automation/automation-change-tracking.md) |Linux |&#8226; | | | | |óránként |
| [Containers](../../azure-monitor/insights/containers.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 3 perc |
| [Key Vault-elemzés](../../azure-monitor/insights/azure-key-vault.md) |Windows | | | | | |az értesítés |
| [Kártevőfelmérés](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Hálózati teljesítményfigyelő](../../azure-monitor/insights/network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-kézfogások 5 másodpercenként, adatokat küld át 3 percenként |
| [Az Office 365 Analytics (előzetes verzió)](solution-office-365.md) |Windows | | | | | |az értesítés |
| **Felügyeleti megoldások** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| [Service Fabric-elemzés](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 perc |
| [Szolgáltatástérkép](../../azure-monitor/insights/service-map.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 15 másodperc |
| [SQL-elemzés](../../azure-monitor/insights/sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [SurfaceHub](../../azure-monitor/insights/surface-hubs.md) |Windows |&#8226; | | | | |a beérkezéskor |
| [A System Center Operations Manager Assessment (előzetes verzió)](../../azure-monitor/insights/scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | hét napja |
| [Frissítéskezelés](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |legalább 2 alkalommal naponta és a egy frissítés telepítése után 15 perc |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 nap |
| [VMware Monitoring (elavult)](../../azure-monitor/insights/vmware.md) | Linux | &#8226; |  |  |  |  | 3 perc |
| [Wire Data 2.0 (előzetes verzió)](../../azure-monitor/insights/wire-data.md) |Windows (2012 R2 / 8.1-es vagy újabb) |&#8226; |&#8226; | | | | 1 perc |




## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [lekérdezések létrehozása](../../azure-monitor/log-query/log-query-overview.md) felügyeleti megoldások által gyűjtött adatok elemzéséhez.
