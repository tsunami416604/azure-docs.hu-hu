---
title: Az Azure figyelési ügynökök áttekintése| Microsoft dokumentumok
description: Ez a cikk részletes áttekintést nyújt az Azure-ügynökök elérhető, amelyek támogatják az Azure-ban vagy hibrid környezetben üzemeltetett virtuális gépek figyelése.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: a7e6a3a299df8112fe4fbcf457516894c1766b8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249086"
---
# <a name="overview-of-azure-monitor-agents"></a>Az Azure Monitor-ügynökök áttekintése

Virtuális gépek és más számítási erőforrások hoz egy ügynök, hogy monitoring adatokat gyűjtsön a teljesítmény és a rendelkezésre álló vendég operációs rendszer és a számítási feladatok. Ez a cikk ismerteti az Azure Monitor által használt ügynökök, és segít meghatározni, hogy melyik kell az adott környezet követelményeinek teljesítéséhez.

> [!NOTE]
> Az Azure Monitor jelenleg több ügynökkel rendelkezik az Azure Monitor és a Log Analytics legutóbbi összesítése miatt. Bár lehet, hogy átfedés van a szolgáltatásaikban, mindegyik egyedi képességekkel rendelkezik. Attól függően, hogy a követelmények, szükség lehet egy vagy több ügynökök a virtuális gépeken. 

Előfordulhat, hogy egy adott követelmények, amelyek nem teljes mértékben egy adott virtuális gép egyetlen ügynöke. Például érdemes lehet metrikariasztásokat használni, amelyek az Azure diagnosztikai bővítményt igényelnek, de szeretné kihasználni az Azure Monitor virtuális gépekhez funkcióit, amelyhez a Log Analytics-ügynök és a függőségi ügynök szükséges. Az ilyen esetekben több ügyintézőt is használhat, és ez egy gyakori forgatókönyv azoknak az ügyfeleknek, akik mindegyiktől funkciókat igényelnek.

## <a name="summary-of-agents"></a>Az ügynökök összefoglalása

Az alábbi táblázatok az Azure Monitor-ügynökök windowsos és Linuxos gyors összehasonlítását biztosítják. Az egyes részletekről az alábbi szakaszban olvashat. 

### <a name="windows-agents"></a>Windows-ügynökök

| | Diagnosztika<br>kiterjesztés (WAD) | Log Analytics<br>Ügynök | Függőség<br>Ügynök |
|:---|:---|:---|:---|
| Támogatott környezetek | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni követelmények | 
| Ügynöki követelmények  | None | None | Log Analytics-ügynök szükséges |
| Összegyűjtött adatok | Eseménynaplók<br>ETW események<br>Teljesítmény<br>Fájlalapú naplók<br>IIS-naplók<br>.NET alkalmazásnaplók<br>összeomlási memóriaképek,<br>Ügynökdiagnosztikai naplók | Eseménynaplók<br>Teljesítmény<IIS logs><br>Fájlalapú naplók<br>Betekintések és megoldások<br>Egyéb szolgáltatások | Részletek és függőségek feldolgozása<br>Hálózati kapcsolat metrikák |
| A rendszernek küldött adatok | Azure Storage<br>Az Azure Monitor metrikák<br>Eseményközpont | Azure Monitor-naplók | Azure Monitor-naplók |


### <a name="linux-agents"></a>Linux-ügynökök

| | Diagnosztika<br>kiterjesztés (LAD) | Telegraf<br>Ügynök | Log Analytics<br>Ügynök | Függőség<br>Ügynök |
|:---|:---|:---|:---|:---|
| Támogatott környezetek | Azure | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni követelmények | Azure<br>Egyéb felhő<br>Helyszíni követelmények |
| Ügynöki követelmények  | None | None | None | Log Analytics-ügynök szükséges |
| Összegyűjtött adatok | Rendszernapló<br>Teljesítmény | Teljesítmény | Rendszernapló<br>Teljesítmény| Részletek és függőségek feldolgozása<br>Hálózati kapcsolat metrikák |
| A rendszernek küldött adatok | Azure Storage<br>Eseményközpont | Az Azure Monitor metrikák | Azure Monitor-naplók | Azure Monitor-naplók |

## <a name="log-analytics-agent"></a>Log Analytics-ügynök

A [Log Analytics-ügynök](log-analytics-agent.md) a vendég operációs rendszerből és az Azure-ban, más felhőszolgáltatókban és a helyszíni virtuális gépek munkaterheléseiből gyűjtfigyelési adatokat. Adatokat gyűjt egy Log Analytics-munkaterületre. A Log Analytics-ügynök ugyanaz az ügynök, amelyet a System Center Operations Manager használ, és többotthoni ügynökszámítógépek kommunikálhatnak a felügyeleti csoporttal és az Azure Monitorral egyszerre. Ez az ügynök is szükség van bizonyos elemzések és megoldások az Azure Monitorban.


> [!NOTE]
> A Windows Log Analytics-ügynökét gyakran nevezik Microsoft Monitoring Agent (MMA) néven. A Log Analytics ügynök Linux gyakran nevezik OMS-ügynök.



Használja a Log Analytics-ügynököt, ha a következőkre van szüksége:

* Naplók és teljesítményadatok gyűjtése az Azure-on kívüli virtuális vagy fizikai gépekről. 
* Adatok küldése a Log Analytics-munkaterületre az [Azure Monitor naplók](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) által támogatott funkciók, például a [naplólekérdezések](../log-query/log-query-overview.md)előnyeinek kihasználásához.
* Használja [az Azure Monitor virtuális gépek,](../insights/vminsights-overview.md) amely lehetővé teszi, hogy a virtuális gépek nagy méretekben figyelheti, és figyeli a folyamatok és a függőségek más erőforrások és külső folyamatok..  
* Az [Azure Security Center](../../security-center/security-center-intro.md) vagy az [Azure Sentinel](../../sentinel/overview.md)használatával kezelheti virtuális gépei biztonságát.
* Az [Azure Automation Update management,](../../automation/automation-update-management.md)az Azure Automation State [Configuration](../../automation/automation-dsc-overview.md)vagy az Azure Automation Change Tracking [and Inventory](../../automation/change-tracking.md) használatával átfogó felügyeletet biztosíthat az Azure-beli virtuális gépeken
* Használjon különböző [megoldásokat](../monitor-reference.md#insights-and-core-solutions) egy adott szolgáltatás vagy alkalmazás figyelésére.

A Log Analytics-ügynök korlátai a következők:

- Nem lehet adatokat küldeni az Azure Monitor metrikák, az Azure Storage vagy az Azure Event Hubs.

## <a name="azure-diagnostics-extension"></a>Azure diagnosztikai bővítmény

Az [Azure Diagnostics bővítmény](diagnostics-extension-overview.md) a vendég operációs rendszerből, valamint az Azure virtuális gépek és más számítási erőforrások munkaterheléseiből gyűjtfigyelési adatokat. Elsősorban adatokat gyűjt az Azure Storage-ba, de lehetővé teszi, hogy adatgyűjtők definiálása is adatokat küldeni más célhelyekre, például az Azure Monitor metrikák és az Azure Event Hubs.

Használja az Azure diagnosztikai bővítményt, ha szükséges:

- Adatokat küldhet az Azure Storage-ba archiválás vagy elemzés céljából olyan eszközökkel, mint például [az Azure Storage Explorer.](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
- Adatok küldése az [Azure Monitor metrikák](data-platform-metrics.md) elemzéséhez [metrikák explorer](metrics-getting-started.md) és kihasználni a funkciók, például a közel valós idejű [metrika riasztások](../../azure-monitor/platform/alerts-metric-overview.md) és automatikus [skálázás](autoscale-overview.md) (csak Windows).
- Adatok küldése külső eszközöknek az [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)használatával.
- Gyűjtse össze [a rendszerindítási diagnosztikát](../../virtual-machines/troubleshooting/boot-diagnostics.md) a virtuális gép rendszerindítási problémáinak kivizsgálásához.

Az Azure diagnosztikai bővítményének korlátai a következők:

- Csak Azure-erőforrásokkal használható.
- Korlátozott képesség az Azure Monitor-naplókba való adatküldésre.

## <a name="telegraf-agent"></a>Telegraf ügynök

Az [InfluxData Telegraf ügynök](collect-custom-metrics-linux-telegraf.md) teljesítményadatok gyűjtésére szolgál linuxos számítógépekről az Azure Monitor metrics.

Használja a Telegraf ügynököt, ha:

* Adatok küldése az [Azure Monitor metrikák](data-platform-metrics.md) elemzéséhez [metrikák explorer](metrics-getting-started.md) és kihasználni a funkciók, például a közel valós idejű [metrika riasztások](../../azure-monitor/platform/alerts-metric-overview.md) és automatikus [skálázás](autoscale-overview.md) (csak Linux). 



## <a name="dependency-agent"></a>Függőségi ügynök

A függőségi ügynök a virtuális gépen futó folyamatokfelderített adatait és a külső folyamatfüggőségeket gyűjti. 

Használja a függőségi ügynököt, ha szükséges:

* Használja a leképezési funkció [Azure Monitor virtuális gépekhez](../insights/vminsights-overview.md) vagy a [Szolgáltatásleképezési](../insights/service-map.md) megoldás.

A függőségi ügynök használatakor vegye figyelembe a következőket:

- A függőségi ügynök megköveteli, hogy a Log Analytics-ügynök telepítve kell lennie ugyanarra a virtuális gépre.
- Linuxos virtuális gépeken a Log Analytics-ügynök kell telepíteni az Azure diagnosztikai bővítmény előtt.

## <a name="extensions-compared-to-agents"></a>Kiterjesztések az ügynökökhöz képest

A Windows [és](../../virtual-machines/extensions/oms-windows.md) [Linux](../../virtual-machines/extensions/oms-linux.md) Log Analytics bővítménye telepíti a Log Analytics-ügynököt az Azure virtuális gépekre. Az Azure Monitor függőségi bővítmény [a Windows](../../virtual-machines/extensions/agent-dependency-windows.md) és a [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) telepítse a függőségi ügynök az Azure virtuális gépeken. Ezek ugyanazok az ügynökök a fent leírt, de lehetővé teszi, hogy kezelje őket [a virtuális gép kiterjesztések](../../virtual-machines/extensions/overview.md). Amikor csak lehetséges, bővítményeket kell használnia az ügynökök telepítéséhez és kezeléséhez.


## <a name="next-steps"></a>További lépések

További részletek az egyes ügynökök a következő:

- [A Log Analytics-ügynök áttekintése](log-analytics-agent.md)
- [Az Azure Diagnostics bővítmény áttekintése](diagnostics-extension-overview.md)
- [Egyéni metrikák gyűjtése egy Linux virtuális géphez az InfluxData Telegraf ügynökkel](collect-custom-metrics-linux-telegraf.md)
