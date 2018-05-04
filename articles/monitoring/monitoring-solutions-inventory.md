---
title: Az gyűjtemény adatait az Azure-ban a felügyeleti megoldásokra |} Microsoft Docs
description: Az Azure-ban megoldások programot, a képi megjelenítés és az adatokat, amelyek egy adott probléma terület körül átalakítani metrikák biztosítanak beszerzési szabályok gyűjteménye.  Ez a cikk a Microsoft és a metódus és használatra vonatkozó adatok gyűjtésének gyakorisága elérhető megoldások listáját tartalmazza.
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
ms.date: 04/20/2018
ms.author: bwren
ms.openlocfilehash: 2a8313640d2d0bd678d0e10da39c87de8cfce00b
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="data-collection-details-for-management-solutions-in-azure"></a>Az gyűjtemény adatait a felügyeleti megoldások Azure-ban
Ez a cikk felsorolja a [megoldások](monitoring-solutions.md) a Microsoft által kínált mutató hivatkozásokat tartalmaz a részletes dokumentációt.  A metódus és a Log Analyticshez való használatra vonatkozó adatok gyűjtésének gyakorisága információkat is biztosít.  Ez a cikk a információt elérhető különböző megoldások azonosítására és a különböző felügyeleti megoldások adatok megismeréséhez és kapcsolat követelmények megértése érdekében használhatja. 

## <a name="list-of-management-solutions"></a>Megoldások listáját

A következő táblázat felsorolja a [megoldások](monitoring-solutions.md) a Microsoft által biztosított Azure-ban. Az oszlop bejegyzése azt jelenti, hogy, hogy a megoldás gyűjti az adatokat a Naplóelemzési módszerrel.  Ha a megoldás nincs kijelölt oszlop, majd ír közvetlenül Naplóelemzési egy másik Azure szolgáltatás. További információ a részletes dokumentációt mindegyiknél hivatkozásra.

Az oszlopok magyarázata a következők:

- **A Microsoft figyelési ügynök** -ügynök SCOM és felügyeleti megoldásokat az Azure-ból a Management pack futtatásához a Windows és Linux használt. Ebben a konfigurációban az ügynököt közvetlenül kapcsolódik a Naplóelemzési nem kapcsolódik az Operations Manager felügyeleti csoport. 
- **Az Operations Manager** -azonos a Microsoft monitoring agent ügynököt. Ebben a konfigurációban van [egy Operations Manager felügyeleti csoporthoz csatlakoztatott](../log-analytics/log-analytics-om-agents.md) Naplóelemzési csatlakozó. 
-  **Az Azure Storage** -megoldás adatokat gyűjti össze az Azure storage-fiók. 
- **Az Operations Manager szükséges?** -A csatlakoztatott Operations Manager felügyeleti csoport MDM-megoldás által adatgyűjtés szükség. 
- **Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött** – Ha az ügynök [SCOM felügyeleti csoport csatlakoztatva](../log-analytics/log-analytics-om-agents.md), akkor küld adatokat a Naplóelemzési a felügyeleti kiszolgálóról. Ebben az esetben az ügynököt nem szükséges közvetlenül a Log Analyticshez való csatlakozáshoz. Ha ez a mező nincs bejelölve, adatküldést követően az ügynök közvetlenül a Naplóelemzési akkor is, ha az ügynök csatlakozik-e a SCOM felügyeleti csoport. akkor kell tudni kommunikálni a Log Analyticshez keresztül egy [OMS átjáró](../log-analytics/log-analytics-oms-gateway.md).
- **Gyűjtési gyakoriságát** -adja meg, hogy gyűjti össze az adatokat a felügyeleti megoldás. 



| **Felügyeleti megoldás** | **Platform** | **A Microsoft figyelési ügynök** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött** | **Gyűjtemény gyakorisága** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity Log Analytics](../log-analytics/log-analytics-activity.md) | Azure | | | | | | az értesítés |
| [AD-elemzés](../log-analytics/log-analytics-ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [AD-replikáció állapota](../log-analytics/log-analytics-ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 nap |
| [Az ügynök állapota](../operations-management-suite/oms-solution-agenthealth.md) | Windows és Linux | &#8226; | &#8226; | | | &#8226; | 1 perc |
| [Kezelési riasztási](../log-analytics/log-analytics-solution-alert-management.md) (Nagios) |Linux |&#8226; | | | | |érkezésükkor |
| [Kezelési riasztási](../log-analytics/log-analytics-solution-alert-management.md) (Zabbix) |Linux |&#8226; | | | | |1 perc |
| [Kezelési riasztási](../log-analytics/log-analytics-solution-alert-management.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 perc |
| [Azure Site Recovery](../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [Application Insights-összekötő (előzetes verzió)](../log-analytics/log-analytics-app-insights-connector.md) | Azure | | | |  |  | az értesítés |
| [Automatizálási hibrid feldolgozók](../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Az Azure alkalmazás átjáró elemzés](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| **Felügyeleti megoldás** | **Platform** | **A Microsoft figyelési ügynök** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött** | **Gyűjtemény gyakorisága** |
| [Azure hálózati biztonsági csoport elemzés](../log-analytics/log-analytics-azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| [Az Azure SQL elemzés (előzetes verzió)](../log-analytics/log-analytics-azure-sql.md) | Windows | | | | | | 10 perc |
| [Biztonsági mentés](../backup/backup-introduction-to-azure-backup.md) | Azure |  |  |  |  |  | n/a |
| [Kapacitást és teljesítményt (előzetes verzió)](../log-analytics/log-analytics-capacity.md) |Windows |&#8226; |&#8226; | | |&#8226; |érkezésükkor |
| [Változáskövetés](../log-analytics/log-analytics-change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Változáskövetés](../log-analytics/log-analytics-change-tracking.md) |Linux |&#8226; | | | | |óránként |
| [Containers](../log-analytics/log-analytics-containers.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 3 perc |
| [Key Vault-elemzés](../log-analytics/log-analytics-azure-key-vault.md) |Windows | | | | | |az értesítés |
| [Kártevőfelmérés](../log-analytics/log-analytics-malware.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Hálózati teljesítményfigyelő](../log-analytics/log-analytics-network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP kézfogások 5 másodperces, adatokat küld át 3 percenként |
| [Az Office 365 Analytics (előzetes verzió)](../operations-management-suite/oms-solution-office-365.md) |Windows | | | | | |az értesítés |
| **Felügyeleti megoldás** | **Platform** | **A Microsoft figyelési ügynök** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **Az Operations Manager ügynök adatait a felügyeleti csoport keresztül küldött** | **Gyűjtemény gyakorisága** |
| [Biztonság és a naplózási](../operations-management-suite/oms-security-getting-started.md) (Syslog) | Linux | &#8226; | | |  |  | érkezésükkor |
| [Biztonság és a naplózási](../operations-management-suite/oms-security-getting-started.md) (and Security event logs) |Windows |&#8226; |&#8226; |&#8226; |  |&#8226; | érkezésükkor |
| [Biztonság és a naplózási](../operations-management-suite/oms-security-getting-started.md) (tűzfal-naplók) |Windows |&#8226; |&#8226; |  |  |  |érkezésükkor |
| [Service Fabric Analytics (előzetes verzió)](../log-analytics/log-analytics-service-fabric.md) |Windows | | |&#8226; | | |5 perc |
| [Szolgáltatástérkép](../operations-management-suite/operations-management-suite-service-map.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 15 másodperc |
| [SQL-elemzés](../log-analytics/log-analytics-sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [SurfaceHub](../log-analytics/log-analytics-surface-hubs.md) |Windows |&#8226; | | | | |érkezésükkor |
| [A System Center Operations Manager Assessment (előzetes verzió)](../log-analytics/log-analytics-scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | hét napja |
| [Frissítéskezelés](../operations-management-suite/oms-solution-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |legalább 2 alkalommal nap és 15 perc után a frissítés telepítése |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 nap |
| [VMware-figyelési (előzetes verzió)](../log-analytics/log-analytics-vmware.md) | Linux | &#8226; |  |  |  |  | 3 perc |
| [Átviteli adatokat 2.0 (előzetes verzió)](../log-analytics/log-analytics-wire-data.md) |Windows (2012 R2 / 8.1-es vagy újabb) |&#8226; |&#8226; | | | | 1 perc |




## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [létrehozhat olyan lekérdezéseket,](../log-analytics/log-analytics-log-searches.md) megoldások által gyűjtött adatok elemzésére.
