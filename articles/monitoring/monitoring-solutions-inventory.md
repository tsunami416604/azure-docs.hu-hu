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
ms.devlang: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 3154a2f8b283f68ec3e10ba621ccba3ee6d77de2
ms.sourcegitcommit: 465ae78cc22eeafb5dfafe4da4b8b2138daf5082
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2018
ms.locfileid: "44324750"
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Adatok gyűjtése részletei felügyeleti megoldások az Azure-ban
Ez a cikk tartalma [felügyeleti megoldások](monitoring-solutions.md) elérhető a Microsoft mutató hivatkozásokat tartalmaz a részletes dokumentációt.  A módszer és a Log analyticsbe adatok gyűjtésének gyakorisága információkat is biztosít.  Használhatja az információkat ebben a cikkben, azonosíthatja a különféle elérhető megoldások és más felügyeleti megoldásokkal data flow és a kapcsolat követelményeinek megismeréséhez. 

## <a name="list-of-management-solutions"></a>Felügyeleti megoldások listája

A következő táblázat felsorolja a [felügyeleti megoldások](monitoring-solutions.md) a Microsoft által biztosított Azure-ban. Egy bejegyzés az oszlopban azt jelenti, hogy, hogy a megoldás a Log Analytics az adatokat gyűjt a módszer használatával.  Ha a megoldás nem tartalmaz oszlopokat kiválasztva, majd ír közvetlenül a Log Analytics egy másik Azure szolgáltatás. A hivatkozásra vonatkozó további információ a részletes dokumentációt.

Magyarázat az oszlopok a következők:

- **A Microsoft-figyelőügynök** -ügynök futtatható a Management pack SCOM és felügyeleti megoldásokat az Azure-ból a Windows és Linux rendszereken használható. Ebben a konfigurációban az ügynök közvetlenül kapcsolódik a Log Analytics az Operations Manager felügyeleti csoporthoz való csatlakozás nélkül. 
- **Az Operations Manager** -azonos a Microsoft monitoring agent ügynök. Ebben a konfigurációban van [egy Operations Manager felügyeleti csoporthoz csatlakoztatott](../log-analytics/log-analytics-om-agents.md) a Log Analyticshez csatlakoztatva van. 
-  **Az Azure Storage** -megoldás gyűjti az adatokat egy Azure storage-fiókot. 
- **Az Operations Manager szükséges?** -Az Operations Manager csatlakoztatott felügyeleti csoport felügyeleti megoldás által adatgyűjtés szükség. 
- **Felügyeleti csoport Operations Manager-ügynök adatok küldött** – Ha az ügynök [egy SCOM felügyeleti csoporthoz csatlakoztatott](../log-analytics/log-analytics-om-agents.md), majd az adatokat küld a Log Analytics a felügyeleti kiszolgálóról. Ebben az esetben az ügynököt nem szükséges közvetlen csatlakoztatása a Log Analytics. Ha a jelölőnégyzet nincs bejelölve, tartó adatküldést követően az ügynök közvetlenül a Log Analytics akkor is, ha az ügynök csatlakozik egy SCOM felügyeleti csoport. el vagy kell képesnek lennie kommunikálni a Log Analytics segítségével egy [OMS-átjáró](../log-analytics/log-analytics-oms-gateway.md).
- **Gyűjtés gyakorisága** – adja meg, hogy a felügyeleti megoldás által összegyűjtött adatok. 



| **Felügyeleti megoldások** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | az értesítés |
| [AD-elemzés](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [AD-replikáció állapota](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 nap |
| [Az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md) | Windows és Linux | &#8226; | &#8226; | | | &#8226; | 1 perc |
| [Riasztás felügyeleti](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |a beérkezéskor |
| [Riasztás felügyeleti](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 perc |
| [Riasztás felügyeleti](../log-analytics/log-analytics-solution-alert-management.md) (az Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 perc |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights-összekötő (előzetes verzió)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | az értesítés |
| [Automation hibrid feldolgozó](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Az Azure Application Gateway Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| **Felügyeleti megoldások** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| [Azure-beli hálózati biztonsági csoport Analytics (elavult)](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| [Az Azure SQL Analytics (előzetes verzió)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 1 perc |
| [Biztonsági mentés](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | az értesítés |
| [Kapacitás és teljesítmény (előzetes verzió)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |a beérkezéskor |
| [Változáskövetés](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Változáskövetés](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |óránként |
| [Containers](../log-analytics/log-analytics-containers.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 3 perc |
| [Key Vault-elemzés](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |az értesítés |
| [Kártevőfelmérés](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Hálózati teljesítményfigyelő](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-kézfogások 5 másodpercenként, adatokat küld át 3 percenként |
| [Az Office 365 Analytics (előzetes verzió)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |az értesítés |
| **Felügyeleti megoldások** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| [Service Fabric-elemzés](../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 perc |
| [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 15 másodperc |
| [SQL-elemzés](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |a beérkezéskor |
| [A System Center Operations Manager Assessment (előzetes verzió)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | hét napja |
| [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |legalább 2 alkalommal naponta és a egy frissítés telepítése után 15 perc |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 nap |
| [VMware Monitoring (elavult)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 perc |
| [Wire Data 2.0 (előzetes verzió)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1-es vagy újabb) |&#8226; |&#8226; | | | | 1 perc |




## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [lekérdezések létrehozása](../log-analytics/log-analytics-log-searches.md) felügyeleti megoldások által gyűjtött adatok elemzéséhez.
