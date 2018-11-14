---
title: Az Azure monitorozási ügynökök |} A Microsoft Docs
description: Ez a cikk részletes áttekintést nyújt az elérhető Azure-ügynökök mely támogatás Azure-ban vagy a hibrid környezetben futtatott virtuális gépek figyelése.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: ce6f40e580595e4f3fbf0519aa1ba8be0355ded1
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621905"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Az Azure monitorozási ügynökök 
A Microsoft Azure több módszert is biztosít a különböző típusú adatok gyűjtésére az Azure rendszerben vagy egyéb felhőszolgáltatók, Microsoft Windows és Linux rendszerű virtuális gépek. Ez a cikk segít a különbségek és képességek érhető el minden ügynök arra, hogy melyik támogatni fogja az IT service management sorrendben vagy általános figyelési követelmények leírása.  

## <a name="comparing-agents"></a>Ügynökök összehasonlítása
Még ma az Azure-ban három típusa van az ügynökök egy Azure-beli Virtuálisgép - figyelhető az Azure Diagnostics bővítmény, a függőségi ügynök és a Log Analytics-ügynököket a Linux és Windows.  Alapvetően az Azure függőségi bővítmény és a Log Analytics-ügynökök úgy tervezték, metrikák és naplók összegyűjtése, és továbbítsa a tárházhoz. Azonban, hogy, ahol azok Hasonlóságok vége.  

### <a name="azure-diagnostic-extension"></a>Az Azure diagnosztikai bővítmény
A [Azure Diagnostics bővítmény](../monitoring-and-diagnostics/azure-diagnostics.md) (más néven a Windows Azure diagnosztikai (WAD) vagy a Linux Azure diagnosztikai (LAD) kiterjesztéssel), amely megadva az Azure Cloud Services általánosan elérhető a 2010-vált, mivel diagnosztikai adatok egyszerű gyűjteménye biztosít a számítási Azure-erőforrások, például egy virtuális Gépet, és továbbra is fennáll az Azure storage-ügynök van. Miután a tároló úgy döntött, hogy az egyik számos elérhető eszköz, például megtekintheti [a Visual Studio Server Explorer](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) és [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md).

Ha szeretné, gyűjtése:

* Előre definiált operációs rendszer teljesítményszámlálók és az eseménynaplókat, vagy amelyek gyűjtésére is megadhat. 
* Összes kérelem és/vagy egy IIS-webkiszolgálón a sikertelen kérelmek
* .NET-alkalmazás nyomkövetési kimeneti naplók
* Esemény-nyomkövetés Windows (ETW)-események 
* Alkalmazásnapló-események gyűjtése a syslog  
* összeomlási memóriaképek, 

Az Azure Diagnostics-ügynök kell használni:

* Naplók és mérőszámok az Azure storage-archiválni kívánja
* Monitorozási adatok integrálása a harmadik felektől származó eszközök. Ezek az eszközök különféle eszközök, például a tárfiók továbbított lekérdezés használata [az Event Hubs](../event-hubs/event-hubs-about.md), vagy a lekérdezés a [Azure Monitoring REST API](../monitoring-and-diagnostics/monitoring-rest-api-walkthrough.md)
* Adatfeltöltés az Azure Monitor mérőszám-diagramok létrehozása az Azure Portalon, vagy hozzon létre, közel valós idejű [metrikákhoz kapcsolódó riasztások](../monitoring-and-diagnostics/alert-metric-overview.md). 
* Virtuálisgép-méretezési készletek automatikus és a klasszikus Felhőszolgáltatások vendég operációs rendszer metrikák alapján.
* Vizsgálja meg a virtuális gépek rendszerindítási problémáinak [a rendszerindítási diagnosztika](../virtual-machines/troubleshooting/boot-diagnostics.md).
* Megismerheti, hogyan az alkalmazások hajt végre, és proaktív módon azonosítja az őket érintő problémákat [Application Insights](../azure-monitor/overview.md).
* Metrikák importálhatja, és a Cloud Services, a klasszikus virtuális gépeket, gyűjtött adatok Log Analytics konfigurálása és a egy Azure storage-fiókban tárolt Service Fabric csomópontjaival.

### <a name="log-analytics-agent"></a>Log Analytics-ügynököket
Speciális monitorozás esetében van szüksége több mint gyűjtését metrikák és naplók egy részét, a Log Analytics-ügynököket Windows és Linux rendszerhez készült szükség. A Log Analytics-ügynököket kifejlesztett átfogó felügyeletét nyújtja a helyszíni fizikai és virtuális gépek, a System Center Operations Manager által felügyelt számítógépek, és más felhőkben futó virtuális gépeket. A Windows és Linux-ügynökök gyűjtéséhez monitorozási megoldás-alapú adatok és is egyéni azokat az adatforrásokat, konfigurálhat egy Log Analytics-munkaterület csatlakozni.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

A Log Analytics-ügynököket kell használni:

* A helyszíni vagy más felhőalapú környezetben futó gépek rendelkezik
* Az Azure Monitor figyelési megoldások például valamelyik [-beli virtuális gépek az Azure Monitor](../monitoring/monitoring-vminsights-overview.md?toc=%2fazure%2fmonitoring%2ftoc.json), [-tárolókhoz az Azure Monitor](../monitoring/monitoring-container-insights-overview.md?toc=%2fazure%2fmonitoring%2ftoc.json)stb.  
* Használjon egy olyan más Azure felügyeleti szolgáltatást, például [az Azure Security Center](../security-center/security-center-intro.md), [Azure Automation](../automation/automation-intro.md)stb.
* Napló-és/vagy metrikaadatok feltöltése az Azure Monitor.

Korábban, a több Azure-szolgáltatások kötegelve is a *Operations Management Suite*, és ennek eredményeképpen a Log Analytics-ügynököket közösen használja többek között az Azure Security Center és Azure Automation szolgáltatásokat.  Ez magában foglalja a kínálnak, továbbítása az Azure-beli virtuális életciklusuk átfogó felügyeleti funkciók teljes készletét. Az érintett műveletek közé tartoznak az alábbiak:

* [Az Azure Automation Update management](../automation/automation-update-management.md) operációsrendszer-frissítések.
* [Azure Automation Desired State Configuration](../automation/automation-dsc-overview.md) konzisztens konfiguráció-állapot karbantartásához.
* Konfigurációs változások követése a [Azure Automation Change Tracking and Inventory](../automation/automation-change-tracking.md).
* Az operációs rendszer és üzemeltetett alkalmazások, mint például az egyéni naplók gyűjtésének [FluentD](../log-analytics/log-analytics-data-sources-json.md), [egyéni naplók](../log-analytics/log-analytics-data-sources-custom-logs.md), és [MySQL és az Apache](../log-analytics/log-analytics-data-sources-linux-applications.md) a Log Analytics használatával.
* Azure-szolgáltatások például [Application Insights](https://docs.microsoft.com/azure/application-insights/) és [az Azure Security Center](https://docs.microsoft.com/azure/security-center/) natív módon tárolja az adatokat közvetlenül a Log Analyticsben.  

### <a name="dependency-agent"></a>Függőségi ügynök
A függőségi ügynök fejlesztette ki a Service Map megoldást, amely eredetileg kívülről a Microsoft részeként. [A Service Map](../monitoring/monitoring-service-map.md) és [-beli virtuális gépek az Azure Monitor](monitoring-vminsights-overview.md) használatához a függőségi ügynököt Windows és Linux rendszerű virtuális gépek és a Log Analytics-ügynök felderített összegyűjti az adatokat a virtuális futó folyamatok integrálható gép és a külső folyamatok függőségeit. Azt tárolja ezeket az adatokat a Log Analytics elérhetővé, és a felderített összekapcsolt összetevőket.

Ezeket az ügynököket a virtuális gép figyelése valamilyen kombinációját szükség lehet. Az ügynökök is telepíthető egymás mellett az Azure-bővítmény, azonban a Linux, a Log Analytics-ügynököket *kell* telepíthető telepítési ellenkező esetben az első sikertelen lesz. 

## <a name="next-steps"></a>További lépések

- Lásd: [áttekintése a Log Analytics-ügynököket](../log-analytics/log-analytics-agent-overview.md) követelmények és támogatott módszerek gépekhez az adatközpontban vagy egyéb felhőalapú környezetben üzemeltetett verziójától, az ügynök telepítése.

