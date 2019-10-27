---
title: Monitorozási megoldások leltára az Azure-ban | Microsoft Docs
description: A Azure Monitor figyelési megoldásai olyan logikai, vizualizációs és adatgyűjtési szabályok gyűjteményei, amelyek egy adott problémás terület körüli metrikákat biztosítanak.  Ez a cikk a Microsoft által elérhető figyelési megoldások listáját, valamint az adatgyűjtési módszerekkel és gyakorisággal kapcsolatos adatokat tartalmazza.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: bwren
ms.date: 06/26/2018
ms.openlocfilehash: 6012a74c00b64c818434ea1744d86c6cf67dd463
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931332"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Leltár-és adatgyűjtési részletek az Azure-beli monitorozási megoldásokhoz
A [monitorozási megoldások](solutions.md) kihasználják az Azure szolgáltatásait, hogy további információkat szolgáltassanak egy adott alkalmazás vagy szolgáltatás működéséről. A figyelési megoldások jellemzően gyűjtik a naplózási adatokat, és lekérdezéseket és nézeteket biztosítanak az összegyűjtött adatok elemzéséhez. A Azure Monitor a használt alkalmazásokhoz és szolgáltatásokhoz is hozzáadhat figyelési megoldásokat. Ezek általában díjmentesen érhetők el, de a használati díjakat meghívó adatokat gyűjtenek.

Ez a cikk a Microsoft által a részletes dokumentációra mutató hivatkozásokkal elérhető [montioring-megoldások](solutions.md) listáját tartalmazza.  Emellett információkat nyújt a módszerekről és az adatgyűjtés gyakoriságáról Azure Monitor.  A cikkben található információk alapján azonosíthatja az elérhető különböző megoldásokat, valamint megismerheti a különböző figyelési megoldások adatáramlási és kapcsolódási követelményeit.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>A figyelési megoldások listája

A következő táblázat felsorolja az Azure-ban a Microsoft által biztosított [monitorozási megoldásokat](solutions.md) . Az oszlop egyik bejegyzése azt jelenti, hogy a megoldás adatokat gyűjt Azure Monitor az adott metódus használatával.  Ha egy megoldás nem jelölt ki oszlopokat, a rendszer közvetlenül egy másik Azure-szolgáltatásból Azure Monitor ír. További információért kövesse a hivatkozásokat a részletes dokumentációhoz.

Az oszlopok magyarázatai a következők:

- **Microsoft monitoring Agent** – Windows és Linux rendszeren használt ügynök, amely az Azure-ból származó SCOM és monitorozási megoldásokból származó felügyeleti csomagokat futtat. Ebben a konfigurációban az ügynök közvetlenül csatlakozik Azure Monitorhoz anélkül, hogy Operations Manager felügyeleti csoporthoz kellene csatlakoznia. 
- **Operations Manager** – azonos ügynök, mint a Microsoft monitoring Agent. Ebben a konfigurációban egy Azure Monitorhoz csatlakoztatott [Operations Manager felügyeleti csoporthoz csatlakozik](../platform/om-agents.md) . 
-  **Azure Storage** – a megoldás adatokat gyűjt egy Azure Storage-fiókból. 
- **Operations Manager kötelező?** -Egy csatlakoztatott Operations Manager felügyeleti csoport szükséges a figyelési megoldás által végzett adatgyűjtéshez. 
- **Operations Manager a felügyeleti csoporton keresztül továbbított ügynök adatait** – ha az ügynök [egy SCOM-felügyeleti csoporthoz csatlakozik](../platform/om-agents.md), akkor a rendszer az adatok elküldését a felügyeleti kiszolgálótól a Azure monitor. Ebben az esetben az ügynöknek nem kell közvetlenül kapcsolódnia Azure Monitorhoz. Ha ez a jelölőnégyzet nincs bejelölve, akkor a rendszer közvetlenül az ügynöktől küldi el az adatok Azure Monitor akkor is, ha az ügynök SCOM-felügyeleti csoporthoz csatlakozik. A [log Analytics átjárón](../platform/gateway.md)keresztül képesnek kell lennie kommunikálni Azure monitor.
- **Gyűjtés gyakorisága** – meghatározza, hogy a figyelési megoldás milyen gyakorisággal gyűjti az adatokat. 



| **Figyelési megoldás** | **Platform** | **Microsoft monitoring Agent** | **Operations Manager ügynök** | **Azure Storage** | **Operations Manager kötelező?** | **A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés** | **Gyűjtés gyakorisága** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [Activity log Analytics](../platform/activity-log-collect.md) | Azure | | | | | | értesítéskor |
| [AD-elemzés](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [AD-replikáció állapota](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 nap |
| [Az ügynök állapota](solution-agenthealth.md) | Windows és Linux | &#8226; | &#8226; | | | &#8226; | 1 perc |
| [Alert Management](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |érkezéskor |
| [Alert Management](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 perc |
| [Alert Management](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 perc |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | – |
| [Application Insights Connector (elavult)](../platform/app-insights-connector.md) | Azure | | | |  |  | értesítéskor |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | – |
| [Azure Application Gateway Analytics](azure-networking-analytics.md) | Azure |  |  |  |  |  | értesítéskor |
| **Figyelési megoldás** | **Platform** | **Microsoft monitoring Agent** | **Operations Manager ügynök** | **Azure Storage** | **Operations Manager kötelező?** | **A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés** | **Gyűjtés gyakorisága** |
| [Azure Network Security Group Analytics (elavult)](azure-networking-analytics.md) | Azure |  |  |  |  |  | értesítéskor |
| [Azure SQL Analytics (előzetes verzió)](azure-sql.md) | Windows | | | | | | 1 perc |
| [Biztonsági mentés](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | értesítéskor |
| [Capacity and Performance (előzetes verzió)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |érkezéskor |
| [Változáskövetés](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[változik](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Változáskövetés](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[változik](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 3 perc |
| [Key Vault-elemzés](azure-key-vault.md) |Windows | | | | | |értesítéskor |
| [Kártevőfelmérés](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |óránként |
| [Hálózati teljesítményfigyelő](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | 5 másodpercenkénti TCP-kézfogások, az adatküldés 3 percenként |
| [Office 365 Analytics (előzetes verzió)](solution-office-365.md) |Windows | | | | | |értesítéskor |
| **Figyelési megoldás** | **Platform** | **Microsoft monitoring Agent** | **Operations Manager ügynök** | **Azure Storage** | **Operations Manager kötelező?** | **A felügyeleti csoporton keresztül elküldett Operations Manager ügynöki adatkezelés** | **Gyűjtés gyakorisága** |
| [Service Fabric-elemzés](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 perc |
| [Szolgáltatástérkép](service-map.md) | Windows és Linux | &#8226; | &#8226; |  |  |  | 15 másodperc |
| [SQL-elemzés](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 nap |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |érkezéskor |
| [System Center Operations Manager Assessment (előzetes verzió)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | hét nap |
| [Frissítéskezelés](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |naponta legalább 2 alkalommal és 15 perccel a frissítés telepítése után |
| [Frissítési készültség](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 nap |
| [VMware Monitoring (elavult)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 perc |
| [Wire Data 2.0 (előzetes verzió)](wire-data.md) |Windows (2012 R2/8,1 vagy újabb) |&#8226; |&#8226; | | | | 1 perc |




## <a name="next-steps"></a>Következő lépések
* Ismerje meg a [figyelési megoldások telepítését és használatát](solutions.md).
* Megtudhatja, hogyan [hozhat létre lekérdezéseket](../log-query/log-query-overview.md) a figyelési megoldások által gyűjtött adatok elemzéséhez.
