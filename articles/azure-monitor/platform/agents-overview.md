---
title: Az Azure monitoring Agent áttekintése | Microsoft Docs
description: Ez a cikk részletes áttekintést nyújt az Azure-ban vagy hibrid környezetben üzemeltetett virtuális gépek figyelését támogató Azure-ügynökökről.
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: ae799e9a852b8700399ef695c54b3348174b560c
ms.sourcegitcommit: a460fdc19d6d7af6d2b5a4527e1b5c4e0c49942f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2020
ms.locfileid: "77069404"
---
# <a name="overview-of-the-azure-monitor-agents"></a>A Azure Monitor-ügynökök áttekintése 
A számítási erőforrások, például a virtuális gépek adatok létrehozásával figyelik a teljesítményt és a rendelkezésre állást ugyanúgy, mint [más Felhőbeli erőforrásokat](../insights/monitor-azure-resource.md). A számítási erőforrások esetében azonban a vendég operációs rendszer és a figyelni kívánt munkaterhelések is rendelkezésre állnak. A monitorozási adatoknak az erőforráson belüli összegyűjtéséhez ügynökre van szükség. Ez a cikk az Azure Monitor által használt ügynököket ismerteti, és segít megállapítani, hogy mely igényeknek kell megfelelnie az adott környezet követelményeinek.

## <a name="summary-of-agents"></a>Ügynökök összefoglalása

> [!NOTE]
> Azure Monitor jelenleg több ügynököt tartalmaz a Azure Monitor és a Log Analytics legutóbbi összevonása miatt. Mindkét ügynök megoszt bizonyos képességeket, míg más funkciók egyediek egy adott ügynök számára. A követelményektől függően előfordulhat, hogy az egyik ügynököt vagy mindkettőt kell használnia. 

Azure Monitor három ügynököt tartalmaz, amelyek mindegyike konkrét funkciókat biztosít. A követelményektől függően előfordulhat, hogy a virtuális gépeken vagy más számítási erőforrásokon egyetlen vagy több ügynököt telepít.

* [Azure Diagnostics bővítmény](#azure-diagnostic-extension)
* [Log Analytics ügynök](#log-analytics-agent)
* [Függőségi ügynök](#dependency-agent)

Az alábbi táblázat a különböző ügynökök gyors összehasonlítását tartalmazza. További részletekért tekintse meg a cikk további részeit.

| | Azure diagnosztikai bővítmény | Log Analytics-ügynök | Függőségi ügynök |
|:---|:---|:---|:---|
| Támogatott környezetek | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni követelmények |
| Operációs rendszerek | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| Ügynök függőségei  | Nincs | Nincs | Log Analytics-ügynököt igényel |
| Összegyűjtött adatok | Eseménynaplók<br>ETW események<br>Rendszernapló<br>Teljesítmény<br>IIS-naplók<br>A .NET-alkalmazások nyomkövetésének kimeneti naplói<br>összeomlási memóriaképek, | Eseménynaplók<br>Rendszernapló<br>Teljesítmény<br>IIS-naplók<br>Egyéni naplók<br>A megoldásokból származó adatok | Folyamat részletei és függőségei<br>Hálózati kapcsolatok metrikái |
| Adatküldés | Azure Storage<br>Azure Monitor metrikák<br>Eseményközpont | Azure Monitor-naplók | Azure Monitor-naplók |



## <a name="azure-diagnostic-extension"></a>Azure diagnosztikai bővítmény
A [Azure Diagnostics-bővítmény](../../azure-monitor/platform/diagnostics-extension-overview.md) a vendég operációs rendszer és az Azure számítási erőforrások munkaterhelései alapján gyűjti a figyelési adatokat. Elsősorban adatokat gyűjt az Azure Storage-ba. A Azure Monitor konfigurálható úgy, hogy az adatok a tárterületről egy Log Analytics munkaterületre legyenek átmásolva. A vendég teljesítményadatokat Azure Monitor metrikába is gyűjtheti.

Az Azure diagnosztikai bővítményt gyakran a Windows Azure diagnosztikai (WAD) vagy a Linux Azure diagnosztikai (LAD) bővítménynek nevezzük.


### <a name="scenarios-supported"></a>Támogatott forgatókönyvek

A Azure Diagnostics bővítmény által támogatott forgatókönyvek a következők:

* Naplókat és teljesítményadatokat gyűjthet az Azure számítási erőforrásaiból.
* Archiválja a naplókat és a teljesítményadatokat a vendég operációs rendszerről az Azure Storage szolgáltatásba.
* Megtekintheti a tárolóban lévő figyelési adattárakat egy eszköz, például [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)használatával.
* Teljesítményadatokat gyűjthet a mérőszámok adatbázisban, hogy kihasználja [Azure monitor mérőszámok](data-platform-metrics.md) által támogatott funkciók előnyeit, például a közel valós idejű [metrikai riasztásokat](../../azure-monitor/platform/alerts-metric-overview.md) és az [autoskálázást](autoscale-overview.md). 
* Gyűjtsön megfigyelési adatokat a [tárterületről egy log Analytics munkaterületre](azure-storage-iis-table.md) , hogy kihasználhassa a [Azure monitor naplók](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , például a [naplózási lekérdezések](../log-query/log-query-overview.md)által támogatott funkciókat.
* Figyelési adatküldés harmadik féltől származó eszközökre az [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)használatával.
* A virtuális gépek rendszerindítási hibáinak vizsgálata a [rendszerindítási diagnosztika](../../virtual-machines/troubleshooting/boot-diagnostics.md)során
* Adatok másolása a virtuális gépen futó alkalmazásokból [Application Insights](diagnostics-extension-to-application-insights.md) , hogy integrálható legyen más alkalmazás-figyeléssel.

## <a name="log-analytics-agent"></a>Log Analytics-ügynök
A [log Analytics ügynök](log-analytics-agent.md) a vendég operációs rendszer és a virtuális gépek számítási feladatait az Azure-ban, más felhőalapú szolgáltatókban és a helyszínen is gyűjti. Adatokat gyűjt egy Log Analytics munkaterületre.

A Log Analytics ügynök ugyanaz az ügynök, amelyet az System Center Operations Manager használ, és a többkiszolgálós ügynök számítógépek kommunikálnak a felügyeleti csoporttal, és Azure Monitor egyidejűleg. Ezt az ügynököt a Azure Monitor bizonyos megoldásai is megkövetelik.

A Windows Log Analytics ügynökét gyakran Microsoft Management agent (MMA) néven említik. A Linux Log Analytics-ügynökét gyakran nevezik OMS-ügynöknek.


### <a name="scenarios-supported"></a>Támogatott forgatókönyvek

A Log Analytics-ügynök által támogatott forgatókönyvek a következők:

* Naplókat és teljesítményadatokat gyűjthet a virtuális gépekről az Azure-ban, más felhőalapú szolgáltatókon és a helyszínen. 
* A Log Analytics munkaterületre gyűjti a figyelési adatokat, így kihasználhatja [Azure monitor naplók](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , például a [napló lekérdezések](../log-query/log-query-overview.md)által támogatott funkciókat.
* Olyan Azure Monitor figyelési megoldásokat használhat, mint például a [Azure monitor for VMS](../insights/vminsights-overview.md), a [tárolók Azure monitor](../insights/container-insights-overview.md)stb.  
* A virtuális gépek biztonságának kezelése az [Azure Sentinel](../../sentinel/overview.md) használatával, amelyhez az log Analytics ügynök szükséges.
* A Log Analytics-ügynököt igénylő [Azure Security Center](../../security-center/security-center-intro.md) használatával felügyelheti a virtuális gépek biztonságát.
* A [Azure Automation](../../automation/automation-intro.md) funkcióinak használatával az Azure-beli virtuális gépek teljes körű felügyeletét közvetítheti életciklusa során.  A Log Analytics-ügynököt igénylő funkciók például a következők:
  * Az operációs rendszer frissítéseinek [Azure Automation frissítésének kezelése](../../automation/automation-update-management.md) .
  * [Azure Automation az állapot konfigurációját](../../automation/automation-dsc-overview.md) a konzisztens konfiguráció állapotának fenntartása érdekében.
  * A konfigurációs változások követése [Azure Automation Change Tracking és leltárral](../../automation/change-tracking.md).

## <a name="dependency-agent"></a>Függőségi ügynök
A függőségi ügynök felderített adatokat gyűjt a virtuális gépen és a külső folyamatok függőségein futó folyamatokról. Ez az ügynök szükséges a [Service Map](../insights/service-map.md) és a térkép funkció [Azure monitor for VMsához](../insights/vminsights-overview.md). A függőségi ügynöknek a Log Analytics-ügynökre van szüksége, és a Azure Monitor Log Analytics munkaterületére kell írnia az adatot.


## <a name="using-multiple-agents"></a>Több ügynök használata
Előfordulhat, hogy konkrét követelményekkel rendelkezik az Azure diagnosztikai bővítmény vagy Log Analytics ügynök használata egy adott virtuális géphez. Előfordulhat például, hogy az Azure diagnosztikai bővítményt igénylő metrikai riasztásokat szeretne használni. Azonban érdemes lehet a Azure Monitor for VMs Térkép funkciójával is használni, amelyhez a függőségi ügynök és a Log Analytics ügynök szükséges. Ebben az esetben több ügynököt is használhat, és ez egy gyakori forgatókönyv azon ügyfelek számára, akik a funkcióit igénylik.

### <a name="considerations"></a>Megfontolások

- A függőségi ügynök megköveteli, hogy a Log Analytics ügynök ugyanarra a virtuális gépre legyen telepítve.
- Linux rendszerű virtuális gépeken az Log Analytics-ügynököt az Azure diagnosztikai bővítmény előtt kell telepíteni.


## <a name="next-steps"></a>Következő lépések

- Tekintse át a [log Analytics ügynök áttekintését](../../azure-monitor/platform/log-analytics-agent.md) a követelmények és a támogatott módszerek áttekintéséhez az ügynök üzembe helyezéséhez az Azure-ban, az adatközpontban vagy más felhőalapú környezetben üzemeltetett gépeken.

