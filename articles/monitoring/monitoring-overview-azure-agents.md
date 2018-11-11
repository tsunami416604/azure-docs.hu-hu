---
title: Az Azure monitorozási ügynökök |} A Microsoft Docs
description: Ez a cikk részletes áttekintést nyújt az Azure-ügynökök érhető el, amely támogatja az Azure virtuális gépek figyelése.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2018
ms.author: magoedte
ms.openlocfilehash: 81db6720422de111cc5b390c58e9020d7c19f90a
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282033"
---
# <a name="overview-of-the-azure-agents-to-monitor-azure-virtual-machines"></a>Az Azure-ügynökök figyeléséhez az Azure-beli virtuális gépek áttekintése
A Microsoft Azure több módszert is biztosít a különböző típusú adatok gyűjtésére az Azure rendszerben vagy egyéb felhőszolgáltatók, Microsoft Windows és Linux rendszerű virtuális gépek.  Ez a cikk segít a különbségek és képességek érhető el minden ügynök arra, hogy melyik támogatni fogja a service management sorrendben vagy általános figyelési követelmények leírása.  

## <a name="comparing-azure-diagnostic-and-log-analytics-agent"></a>Azure diagnosztikai és a Log Analytics-ügynök összehasonlítása
Még ma az Azure-ban két típusa van az ügynökök egy Azure-beli Virtuálisgép - figyelhető az Azure Diagnostics bővítmény és a Log Analytics-ügynököket a Linux és Windows.  Alapvetően ezeket az ügynököket úgy tervezték, metrikák és naplók összegyűjtése, és továbbítsa a tárházhoz. Azonban, hogy, ahol azok Hasonlóságok vége.  

A [Azure Diagnostics bővítmény](../monitoring-and-diagnostics/azure-diagnostics.md), amely megadva az Azure Cloud Services 2010, általánosan elérhető vált, mert olyan ügynök, amely egy Azure IaaS-erőforrás, például egy virtuális Gépet, a diagnosztikai adatok egyszerű gyűjteménye biztosít, és azt az Azure storage-megmarad.  Miután a tároló úgy döntött, hogy az egyik számos elérhető eszköz, például megtekintheti [a Visual Studio Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) és [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Ha szeretné, gyűjtése:

* Előre definiált operációs rendszer teljesítményszámlálók és az eseménynaplókat, vagy amelyek gyűjtésére is megadhat. 
* Összes kérelem és/vagy egy IIS-webkiszolgálón a sikertelen kérelmek
* .NET-alkalmazás nyomkövetési kimeneti naplók
* Esemény-nyomkövetés Windows (ETW)-események 
* Alkalmazásnapló-események gyűjtése a syslog  
* összeomlási memóriaképek, 

Adatok is továbbítható [Application Insights](../application-insights/app-insights-cloudservices.md), [Log Analytics](../log-analytics/log-analytics-queries.md), vagy az-Azure szolgáltatások használatával [Eseményközpont](../event-hubs/event-hubs-about.md). 

Speciális monitorozás esetében van szüksége több mint gyűjtését metrikák és naplók egy részét, a Log Analytics-ügynököket Windows és Linux rendszerhez készült szükség.  Ez az ügynök az Ön Azure-szolgáltatások például Automation és a Log Analyticsben, az általuk kínált, hogy az Azure-beli virtuális életciklusuk átfogó felügyeleti funkciók teljes készletét használhatja. Az érintett műveletek közé tartoznak az alábbiak:

* [Az Azure Automation Update management](../automation/automation-update-management.md) operációsrendszer-frissítések
* [Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) konzisztens konfiguráció-állapot karbantartásához
* Konfigurációs változások követése a [Azure Automation Change Tracking and Inventory](../automation/automation-change-tracking.md)
* Az operációs rendszer és üzemeltetett alkalmazások, mint például az egyéni naplók gyűjtésének [FluentD](../log-analytics/log-analytics-data-sources-json.md), [egyéni naplók](../log-analytics/log-analytics-data-sources-custom-logs.md), [MySQL és az Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) a Log Analytics használatával
* Azure-szolgáltatások például [Application Insights](https://docs.microsoft.com/azure/application-insights/) és [az Azure Security Center](https://docs.microsoft.com/azure/security-center/) natív módon tárolja az adatokat közvetlenül a Log Analyticsben.  

## <a name="next-steps"></a>További lépések

- Lásd: [adatok gyűjtése a Log Analytics szolgáltatással környezetében számítógépekről](../log-analytics/log-analytics-concept-hybrid.md) , tekintse át a követelményeket és a választható módszerek az ügynök telepítése a számítógépekre az adatközpontban vagy egyéb felhőalapú környezetben.
- Az adatgyűjtés Azure-beli virtuális gépekről való konfigurálásáról lásd: [Adatgyűjtés Azure-beli virtuális gépekről](../log-analytics/log-analytics-quick-collect-azurevm.md). 
