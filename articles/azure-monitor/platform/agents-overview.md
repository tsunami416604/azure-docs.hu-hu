---
title: Az Azure monitoring Agent áttekintése | Microsoft Docs
description: Ez a cikk részletes áttekintést nyújt az Azure-ban vagy hibrid környezetben üzemeltetett virtuális gépek figyelését támogató Azure-ügynökökről.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: c6aea3be5782c967c5816a1e40dc5443306671b3
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87445297"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor ügynökök áttekintése

A virtuális gépeknek és egyéb számítási erőforrásoknak egy ügynökkel kell rendelkezniük a figyelési adatok gyűjtéséhez, hogy a vendég operációs rendszer és a munkaterhelések teljesítménye és rendelkezésre állása mérhető legyen. Ez a cikk az Azure Monitor által használt ügynököket ismerteti, és segít megállapítani, hogy mely igényeknek kell megfelelnie az adott környezet követelményeinek.

> [!NOTE]
> Azure Monitor jelenleg több ügynököt tartalmaz a Azure Monitor és a Log Analytics legutóbbi összevonása miatt. Előfordulhat, hogy a funkciók átfedésben vannak, és mindegyik egyedi képességgel rendelkezik. A követelményektől függően szükség lehet egy vagy több ügynökre a virtuális gépeken. 

Előfordulhat, hogy egy adott virtuális gép egyetlen ügynökével nem lehet teljes mértékben kielégíteni a követelmények meghatározott készletét. Előfordulhat például, hogy olyan metrikai riasztásokat szeretne használni, amelyek az Azure Diagnostics bővítményt igénylik, de a Azure Monitor for VMs funkcióit is szeretné kihasználni, amelyekhez szükség van a Log Analytics ügynökre és a függőségi ügynökre. Ilyen esetekben több ügynököt is használhat, és ez egy gyakori forgatókönyv azon ügyfelek számára, akik a funkcióit igénylik.

## <a name="summary-of-agents"></a>Ügynökök összefoglalása

Az alábbi táblázatok a Windows és a Linux rendszerhez készült Azure Monitor ügynökök gyors összehasonlítását ismertetik. A további részleteket az alábbi szakaszban találja. 

### <a name="windows-agents"></a>Windows-ügynökök

| | Diagnosztika<br>kiterjesztés (WAD) | Log Analytics<br>ügynök | Függőség<br>ügynök |
|:---|:---|:---|:---|
| **Támogatott környezetek** | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni | 
| **Ügynökre vonatkozó követelmények**  | Nincsenek | Nincsenek | Log Analytics-ügynököt igényel |
| **Összegyűjtött adatok** | Eseménynaplók<br>ETW események<br>Teljesítmény<br>Fájl alapú naplók<br>IIS-naplók<br>.NET-alkalmazás naplói<br>Összeomlási memóriaképek<br>Ügynök diagnosztikai naplói | Eseménynaplók<br>Teljesítmény<IIS logs><br>Fájl alapú naplók<br>Bepillantást és megoldásokat<br>Egyéb szolgáltatások | Folyamat részletei és függőségei<br>Hálózati kapcsolatok metrikái |
| **Adatküldés** | Azure Storage<br>Azure Monitor metrikák<br>Eseményközpont | Azure Monitor-naplók | Azure Monitor-naplók |


### <a name="linux-agents"></a>Linux-ügynökök

| | Diagnosztika<br>bővítmény (LAD) | Telegraf<br>ügynök | Log Analytics<br>ügynök | Függőség<br>ügynök |
|:---|:---|:---|:---|:---|
| **Támogatott környezetek** | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni |
| **Ügynökre vonatkozó követelmények**  | Nincsenek | Nincsenek | Nincsenek | Log Analytics-ügynököt igényel |
| **Összegyűjtött adatok** | Rendszernapló<br>Teljesítmény | Teljesítmény | Rendszernapló<br>Teljesítmény| Folyamat részletei és függőségei<br>Hálózati kapcsolatok metrikái |
| **Adatküldés** | Azure Storage<br>Eseményközpont | Azure Monitor metrikák | Azure Monitor-naplók | Azure Monitor-naplók |

## <a name="log-analytics-agent"></a>Log Analytics-ügynök

A [log Analytics ügynök](log-analytics-agent.md) a vendég operációs rendszer és a virtuális gépek számítási feladatait az Azure-ban, más felhőalapú szolgáltatókban és a helyszínen is gyűjti. Adatokat gyűjt egy Log Analytics munkaterületre. A Log Analytics ügynök ugyanaz az ügynök, amelyet az System Center Operations Manager használ, és a többkiszolgálós ügynökök számítógépei kommunikálhatnak a felügyeleti csoporttal, és Azure Monitor egyidejűleg. Ezt az ügynököt a Azure Monitor bizonyos bepillantást és megoldásokra is szükség van.


> [!NOTE]
> A Windows Log Analytics ügynöke gyakran a Microsoft monitoring Agent (MMA) néven is ismert. A Linux Log Analytics-ügynökét gyakran nevezik OMS-ügynöknek.



Ha a következőkre van szüksége, használja a Log Analytics-ügynököt:

* Naplókat és teljesítményadatokat gyűjthet az Azure-on kívüli virtuális vagy fizikai gépekről. 
* Adatküldés egy Log Analytics munkaterületre, hogy kihasználhassa a [Azure monitor naplók](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) , például a [naplózási lekérdezések](../log-query/log-query-overview.md)által támogatott funkciókat.
* A [Azure monitor for VMS](../insights/vminsights-overview.md) használata lehetővé teszi a virtuális gépek méretezését, és figyeli a folyamatokat és a függőségeket más erőforrásokra és külső folyamatokra.  
* A virtuális gépek biztonságának kezelése [Azure Security Center](../../security-center/security-center-intro.md) vagy [Azure Sentinel](../../sentinel/overview.md)használatával.
* Az Azure-beli virtuális gépek teljes körű felügyeletének biztosításához használja [Azure Automation Update managementet](../../automation/update-management/update-mgmt-overview.md), [Azure Automation az állapot konfigurációját](../../automation/automation-dsc-overview.md), vagy [Azure Automation Change Tracking és leltárt](../../automation/change-tracking.md) .
* Különböző [megoldásokat](../monitor-reference.md#insights-and-core-solutions) használhat egy adott szolgáltatás vagy alkalmazás figyelésére.

A Log Analytics-ügynök korlátai a következők:

- Az adatok nem küldhetők Azure Monitor metrikába, Azure Storage-ba vagy Azure-Event Hubsba.

## <a name="azure-diagnostics-extension"></a>Azure diagnosztikai bővítmény

A [Azure Diagnostics-bővítmény](diagnostics-extension-overview.md) a vendég operációs rendszer és az Azure-beli virtuális gépek számítási feladatainak, valamint más számítási erőforrások munkaterhelésének figyelési adatait gyűjti. Elsősorban adatokat gyűjt az Azure Storage-ba, de lehetővé teszi az adattárolók definiálását is, így adatokat is küldhet más célhelyekre, például Azure Monitor metrikára és az Azure Event Hubsra.

Ha a következőkre van szüksége, használja az Azure diagnosztikai bővítményt:

- Adatküldés az Azure Storage-ba archiválásra vagy elemzésre olyan eszközökkel, mint például a [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).
- Adatokat küldhet [Azure monitor mérőszámoknak](data-platform-metrics.md) , hogy elemezze a [metrikák Explorerrel](metrics-getting-started.md) , és kihasználja az olyan funkciók előnyeit, mint a közel valós idejű [metrikus riasztások](./alerts-metric-overview.md) és az [autoscale](autoscale-overview.md) (csak Windows).
- Adatküldés harmadik féltől származó eszközökre az [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)használatával.
- [Rendszerindítási diagnosztika](../../virtual-machines/troubleshooting/boot-diagnostics.md) gyűjtése a virtuális gépek rendszerindítási problémáinak vizsgálatához.

Az Azure Diagnostics bővítmény korlátai a következők:

- Csak az Azure-erőforrásokkal használható.
- Az Azure Monitor naplókba való adatküldésre korlátozott képesség.

## <a name="telegraf-agent"></a>A Grafi ügynök

A [InfluxData-Grafi ügynök](collect-custom-metrics-linux-telegraf.md) a Linux rendszerű számítógépek teljesítményadatokat gyűjti a Azure monitor metrikák használatával.

Ha a következőkre van szüksége, használja a-Graf Agent ügynököt:

* Adatokat küldhet [Azure monitor mérőszámoknak](data-platform-metrics.md) , hogy elemezze a [metrikák Explorerrel](metrics-getting-started.md) , és kihasználja az olyan funkciók előnyeit, mint a közel valós idejű [metrikus riasztások](./alerts-metric-overview.md) és az [autoscale](autoscale-overview.md) (csak Linux). 



## <a name="dependency-agent"></a>Függőségi ügynök

A függőségi ügynök felderített adatokat gyűjt a virtuális gépen és a külső folyamatok függőségein futó folyamatokról. 

Ha a következőkre van szüksége, használja a függőségi ügynököt:

* A Térkép funkció [Azure monitor for VMS](../insights/vminsights-overview.md) vagy a [Service Map](../insights/service-map.md) megoldás használata.

A függőségi ügynök használatakor vegye figyelembe a következőket:

- A függőségi ügynök megköveteli, hogy a Log Analytics ügynök ugyanarra a virtuális gépre legyen telepítve.
- Linux rendszerű virtuális gépeken az Log Analytics-ügynököt az Azure diagnosztikai bővítmény előtt kell telepíteni.

## <a name="extensions-compared-to-agents"></a>Az ügynökökkel összehasonlított bővítmények

A [Windows](../../virtual-machines/extensions/oms-windows.md) és a [Linux](../../virtual-machines/extensions/oms-linux.md) log Analytics bővítménye telepíti az log Analytics ügynököt az Azure Virtual Machines szolgáltatásban. A Windows és a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) [rendszerhez](../../virtual-machines/extensions/agent-dependency-windows.md) készült Azure monitor függőségi ügynök telepíti a függőségi ügynököt az Azure Virtual Machines szolgáltatásban. Ezek ugyanazok a fent ismertetett ügynökök, de lehetővé teszik a [virtuális gépek bővítményein](../../virtual-machines/extensions/overview.md)keresztüli felügyeletét. Amikor csak lehet, használja a bővítményeket az ügynökök telepítéséhez és kezeléséhez.


## <a name="next-steps"></a>További lépések

További részleteket az egyes ügynökökről a következő címen talál:

- [A Log Analytics ügynök áttekintése](log-analytics-agent.md)
- [Azure Diagnostics bővítmény áttekintése](diagnostics-extension-overview.md)
- [Egyéni metrikák gyűjtése Linux rendszerű virtuális gépekhez a InfluxData-ben-Graf ügynökkel](collect-custom-metrics-linux-telegraf.md)

