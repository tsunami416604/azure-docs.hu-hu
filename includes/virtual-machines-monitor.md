---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: 93a2554b5d3cc24e1b5fc1e3d0f18ed1bfe0579c
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/30/2019
ms.locfileid: "71692035"
---
Az Azure-ban üzemeltetett virtuális gépek jelentős növekedésével fontos, hogy azonosítsa azokat a teljesítménnyel és állapottal kapcsolatos problémákat, amelyek hatással vannak az alkalmazások és az infrastruktúra által nyújtott szolgáltatásokra. Az alapszintű figyelés alapértelmezés szerint az Azure-ban a CPU-használat, a lemez kihasználtsága, a memória kihasználtsága és a gazdagép hypervisora által gyűjtött hálózati forgalom mérőszámait írja le. A további metrikai és naplózási adatok összegyűjthetők a [bővítmények](../articles/virtual-machines/windows/extensions-features.md) használatával, hogy a diagnosztika konfigurálható legyen a virtuális gépeken a vendég operációs rendszerből.

A virtuális gépen futó .NET-alapú vagy Java-webalkalmazás-összetevőkkel kapcsolatos teljesítmény-és állapotadatok észleléséhez és diagnosztizálásához Azure Monitor központosított figyelést nyújt olyan átfogó funkciókkal, mint például a Azure Monitor for VMs és Application Insights.

## <a name="diagnostics-and-metrics"></a>Diagnosztika és mérőszámok 

A [diagnosztikai adatok](https://docs.microsoft.com/cli/azure/vm/diagnostics) gyűjteményét beállíthatja és figyelheti a Azure Portal [](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) , az Azure CLI, a Azure PowerShell és a programozási alkalmazások API-k mérőszámait használva. Megteheti például a következőt:

- **Figyelje meg a virtuális gép alapvető mérőszámait.** A Azure Portal áttekintés képernyőjén az alapszintű mérőszámok tartalmazzák a CPU-használatot, a hálózati használatot, a lemez bájtjainak teljes számát és a lemezes műveletek másodpercenkénti számát.

- **Engedélyezze a rendszerindítási diagnosztika gyűjteményét, és tekintse meg a Azure Portal használatával.** Ha a saját lemezképét az Azure-ba hozza, vagy akár a platform rendszerképeinek rendszerindítását is, számos oka lehet annak, hogy a virtuális gép nem indítható állapotba kerül. A virtuális gép létrehozásakor egyszerűen engedélyezheti a rendszerindítási diagnosztika **szolgáltatást** , ha a beállítások képernyő figyelés szakaszában a rendszerindítási diagnosztika lehetőségre kattint.

    A virtuális gépek indításakor a rendszerindítási diagnosztikai ügynök rögzíti a rendszerindítási kimenetet, és tárolja azt az Azure Storage-ban. Ezek az adatok a virtuális gépek rendszerindítási problémáinak hibaelhárításához használhatóak. A rendszerindítási diagnosztika nem lesz automatikusan engedélyezve, amikor parancssori eszközökről hoz létre virtuális gépet. Ahhoz, hogy a rendszerindítási diagnosztikát engedélyezni lehessen, tárfiókot kell létrehozni a rendszerindítási naplók tárolásához. Ha engedélyezi a rendszerindítási diagnosztikát a Azure Portal, automatikusan létrejön egy Storage-fiók.

    Ha nem engedélyezte a rendszerindítási diagnosztika használatát a virtuális gép létrehozásakor, később bármikor engedélyezheti az [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)vagy egy [Azure Resource Manager sablon](../articles/virtual-machines/windows/extensions-diagnostics-template.md)használatával.

- **A vendég operációs rendszer diagnosztikai adatgyűjtési szolgáltatásának engedélyezése.** Amikor létrehoz egy virtuális gépet, lehetősége van arra, hogy a beállítások képernyőn engedélyezze a vendég operációs rendszer diagnosztikát. Ha engedélyezi a diagnosztikai adatok gyűjtését, a [Linux IaaSDiagnostics-bővítményét](../articles/virtual-machines/linux/diagnostic-extension.md) vagy a [Windows IaaSDiagnostics-bővítményét a rendszer](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) hozzáadja a virtuális géphez, amely lehetővé teszi további lemez-, processzor-és memória-adatok gyűjtését.

    Az összegyűjtött diagnosztikai adatok használatával konfigurálhatja a virtuális gépek automatikus skálázását. [Azure monitor naplókat](../articles/azure-monitor/platform/data-platform-logs.md) is konfigurálhat az adattároláshoz, és riasztásokat állíthat be, amelyekkel megtudhatja, ha a teljesítmény nem megfelelő.

## <a name="alerts"></a>Riasztások

A riasztásokat [](../articles/azure-monitor/platform/alerts-overview.md) meghatározott teljesítmény-mérőszámok alapján hozhatja létre. Példák azokra a problémákra, amelyekről értesítést kaphat, ha az átlagos CPU-használat meghaladja a megadott küszöbértéket, vagy a rendelkezésre álló szabad lemezterület egy bizonyos mennyiség alá csökken. A riasztásokat a [Azure Portal](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal) [Azure Resource Manager sablonok](../articles/azure-monitor/platform/alerts-metric-create-templates.md)vagy az [Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)használatával lehet konfigurálni.

## <a name="azure-service-health"></a>Azure Service Health

A [Azure Service Health](../articles/service-health/service-health-overview.md) személyre szabott útmutatást és támogatást nyújt, ha az Azure-szolgáltatások problémái vannak, és segít felkészülni a közelgő tervezett karbantartásra. Azure Service Health riasztást küld Önnek és csapatának a megcélzó és rugalmas értesítések használatával.

## <a name="azure-resource-health"></a>Azure Resource Health

Az [Azure Resource Health](../articles/service-health/resource-health-overview.md) segítséget nyújt a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

## <a name="azure-activity-log"></a>Azure-tevékenységnapló

Az [Azure-tevékenység naplója](../articles/azure-monitor/platform/activity-logs-overview.md) egy előfizetési napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. A napló számos adattartományt tartalmaz, Azure Resource Manager operatív adatokból Service Health események frissítéseire. A Azure Portalban található műveletnapló elemre kattintva megtekintheti a virtuális gép naplóját.

A műveletnapló többek között a következőkre használható:

- Hozzon létre egy [riasztást egy tevékenység napló eseményén](../articles/azure-monitor/platform/activity-logs-overview.md).
- [Továbbítsa azt egy Event hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) -nak egy harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás (például Power BI) betöltéséhez.
- Elemezze Power BI a [Power bi Content Pack](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)használatával.
- [Mentse azt egy Storage-fiókba](../articles/azure-monitor/platform/archive-activity-log.md) archiválás vagy manuális ellenőrzés céljából. Az adatmegőrzési időt (nap) megadhatja a log profil használatával.

A műveletnapló adatai [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)használatával vagy a [REST API-k figyelésével](https://docs.microsoft.com/rest/api/monitor/)is elérhetők.

Az [Azure-erőforrás-naplók](../articles/azure-monitor/platform/resource-logs-overview.md) a virtuális gép által kibocsátott naplók, amelyek a működésével kapcsolatos gazdag és gyakori információkat biztosítanak. Az erőforrás-naplók különböznek a tevékenység naplójától azáltal, hogy betekintést adnak a virtuális gépen végrehajtott műveletekre.

A diagnosztikai naplókhoz többek között a következőket teheti:

- A naplózáshoz vagy a manuális ellenőrzéshez [mentse őket egy Storage-fiókba](../articles/azure-monitor/platform/archive-diagnostic-logs.md) . Megadhatja a megőrzési időt (napokban) az erőforrás-diagnosztikai beállítások használatával.
- A harmadik féltől származó szolgáltatás vagy egyéni elemzési megoldás (például a Power BI) betöltéséhez [továbbíthatja őket Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) .
- Elemezze azokat [log Analyticsokkal](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Speciális figyelés

Az Azure-beli virtuális gép és a virtuálisgép-méretezési csoportok által támogatott alkalmazások vagy szolgáltatások láthatósága érdekében a virtuális gépen futó vendég operációs rendszer vagy munkaterhelések problémáinak azonosítása annak megismeréséhez, hogy az hatással van-e az alkalmazás rendelkezésre állására vagy teljesítményére, vagy az alkalmazással kapcsolatos probléma esetén engedélyezze a [Azure monitor for VMS](../articles/azure-monitor/insights/vminsights-overview.md) és a [Application Insights](../articles/azure-monitor/app/app-insights-overview.md)is.

A Azure Monitor for VMs az Azure Virtual Machines (VM) méretezését a Windows-és Linux-alapú virtuális gépek teljesítményének és állapotának elemzésével, beleértve a különböző folyamatokat és az egyéb erőforrásokkal és a külső folyamatokkal kapcsolatos eltérő függőségeket is. felderíti. Számos trend Performance diagramot tartalmaz, amelyek segítenek a problémák kivizsgálásában és a virtuális gépek kapacitásának felmérésében. A függőségi Térkép megjeleníti a felügyelt és a nem figyelt gépeket, a sikertelen és az aktív hálózati kapcsolatokat a folyamatok és a számítógépek között, és a szabványos hálózati kapcsolati metrikákkal rendelkező trend-diagramokat jeleníti meg. A Application Insightsával kombinálva figyelheti az alkalmazást, és rögzítheti a telemetria, például a HTTP-kérelmeket, kivételeket stb., így a virtuális gépek és az alkalmazás közötti problémák is összekapcsolhatók. [Azure monitor riasztásokat](../articles/azure-monitor/platform/alerts-overview.md) konfigurálhat, hogy riasztást kapjon a Azure monitor for VMS által gyűjtött figyelési adatokból észlelt fontos feltételekről.

## <a name="next-steps"></a>További lépések

- Végigvezeti a [Windows rendszerű virtuális gép figyelésének](../articles/virtual-machines/windows/tutorial-monitoring.md) lépésein Azure PowerShell vagy [egy linuxos virtuális gép figyelése az Azure CLI-vel](../articles/virtual-machines/linux/tutorial-monitoring.md).

- További információ a [monitorozás és a diagnosztika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)szolgáltatással kapcsolatos ajánlott eljárásokról.
