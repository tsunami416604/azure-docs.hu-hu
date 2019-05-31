---
title: Figyelési megoldások az Azure-ban leltára |} A Microsoft Docs
description: Monitorozás az Azure Monitor problémakörrel kapcsolatos egy adott területre metrikákat logikai, megjelenítési és adatgyűjtési szabályok gyűjteményei.  Ez a cikk a Microsoft és a metódus és adatok gyűjtésének gyakorisága elérhető megoldások monitorozása listáját tartalmazza.
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
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234020"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Szoftverleltár és az adatok gyűjtemény adatait figyelési megoldások az Azure-ban
[Figyelési megoldások](solutions.md) nyújt további betekintést a művelet egy adott alkalmazás vagy szolgáltatás Azure-szolgáltatások használhatja. Figyelési megoldások általában a naplóadatokat gyűjthet, és adja meg a lekérdezések és nézetek, elemezheti az összegyűjtött adatokat. Figyelési megoldások az Azure monitornak alkalmazások és szolgáltatások hozzáadása. Általában érhetők el, nem tudta meghívni a használati díjak költség azonban gyűjt adatokat.

Ez a cikk tartalma [montioring megoldások](solutions.md) elérhető a Microsoft mutató hivatkozásokat tartalmaz a részletes dokumentációt.  A metódus és az Azure Monitor szolgáltatásba adatok gyűjtésének gyakorisága információkat is biztosít.  Használhatja az információkat ebben a cikkben, azonosíthatja a különféle elérhető megoldások és más figyelési megoldások data flow és a kapcsolat követelményeinek megismeréséhez.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>Figyelési megoldások listája

A következő táblázat felsorolja a [figyelési megoldások](solutions.md) a Microsoft által biztosított Azure-ban. Egy bejegyzés az oszlopban azt jelenti, hogy, hogy a megoldás gyűjti az adatokat az Azure Monitor szolgáltatásba a módszer használatával.  Ha a megoldás nem tartalmaz oszlopokat kiválasztva, majd ír közvetlenül az Azure Monitor egy másik Azure szolgáltatás. A hivatkozásra vonatkozó további információ a részletes dokumentációt.

Magyarázat az oszlopok a következők:

- **A Microsoft-figyelőügynök** – az ügynököt az SCOM-ről futtatható a Management pack a Windows és Linux rendszereken használt, és figyelési megoldások az Azure-ból. Ebben a konfigurációban az ügynök közvetlenül kapcsolódik az Azure Monitor az Operations Manager felügyeleti csoporthoz való csatlakozás nélkül. 
- **Az Operations Manager** -azonos a Microsoft monitoring agent ügynök. Ebben a konfigurációban van [egy Operations Manager felügyeleti csoporthoz csatlakoztatott](../platform/om-agents.md) , amely csatlakozik az Azure Monitor. 
-  **Az Azure Storage** -megoldás gyűjti az adatokat egy Azure storage-fiókot. 
- **Az Operations Manager szükséges?** -Az Operations Manager csatlakoztatott felügyeleti csoport figyelési megoldás által adatgyűjtés szükség. 
- **A felügyeleti csoport Operations Manager-ügynök adatok küldött** – Ha az ügynök [egy SCOM felügyeleti csoporthoz csatlakoztatott](../platform/om-agents.md), majd az adatokat az Azure monitornak is küld a felügyeleti kiszolgáló. Ebben az esetben az ügynök közvetlenül kapcsolódik az Azure Monitor nem szükséges. Ha a jelölőnégyzet nincs bejelölve, tartó adatküldést követően az ügynök közvetlenül az Azure Monitor akkor is, ha az ügynök csatlakozik egy SCOM felügyeleti csoport. El kell az Azure monitornak keresztül kommunikálnak a [Log Analytics-átjáró](../platform/gateway.md).
- **Gyűjtés gyakorisága** – adja meg, hogy gyűjti össze az adatokat a megoldáshoz. 



| **Figyelési megoldás** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [A Log analytics tevékenység](../platform/activity-log-collect.md) | Azure | | | | | | az értesítés |
| [AD-elemzés](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [AD-replikáció állapota](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 nap |
| [Az ügynök állapota](solution-agenthealth.md) | Windows és Linux | &#8226; | &#8226; | | | &#8226; | 1 perc |
| [Riasztás felügyeleti](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |a beérkezéskor |
| [Riasztás felügyeleti](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 perc |
| [Riasztás felügyeleti](../platform/alert-management-solution.md) (az Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 perc |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | n/a |
| [(Elavult az) Application Insights-összekötő](../platform/app-insights-connector.md) | Azure | | | |  |  | az értesítés |
| [Automation hibrid feldolgozó](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | n/a |
| [Az Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| **Figyelési megoldás** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| [Azure-beli hálózati biztonsági csoport Analytics (elavult)](azure-networking-analytics.md) | Azure |  |  |  |  |  | az értesítés |
| [Az Azure SQL Analytics (előzetes verzió)](azure-sql.md) | Windows | | | | | | 1 perc |
| [Biztonsági mentés](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | az értesítés |
| [Kapacitás és teljesítmény (előzetes verzió)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |a beérkezéskor |
| [Változáskövetés](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[Változó](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Változáskövetés](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[Változó](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 3 perc |
| [Key Vault-elemzés](azure-key-vault.md) |Windows | | | | | |az értesítés |
| [Kártevőfelmérés](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Hálózati teljesítményfigyelő](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP-kézfogások 5 másodpercenként, adatokat küld át 3 percenként |
| [Az Office 365 Analytics (előzetes verzió)](solution-office-365.md) |Windows | | | | | |az értesítés |
| **Figyelési megoldás** | **Platform** | **A Microsoft-figyelőügynököt** | **Operations Manager-ügynök** | **Azure Storage** | **Az Operations Manager szükséges?** | **A felügyeleti csoport Operations Manager-ügynök adatok küldött** | **Gyűjtés gyakorisága** |
| [Service Fabric-elemzés](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 perc |
| [Szolgáltatástérkép](service-map.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 15 másodperc |
| [SQL-elemzés](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |a beérkezéskor |
| [A System Center Operations Manager Assessment (előzetes verzió)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | hét napja |
| [Frissítéskezelés](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |legalább 2 alkalommal naponta és a egy frissítés telepítése után 15 perc |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 nap |
| [VMware Monitoring (elavult)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 perc |
| [Wire Data 2.0 (előzetes verzió)](wire-data.md) |Windows (2012 R2 / 8.1-es vagy újabb) |&#8226; |&#8226; | | | | 1 perc |




## <a name="next-steps"></a>További lépések
* Ismerje meg, hogyan [telepítési és figyelési megoldások használata](solutions.md).
* Ismerje meg, hogyan [lekérdezések létrehozása](../log-query/log-query-overview.md) figyelési megoldások által gyűjtött adatok elemzéséhez.
