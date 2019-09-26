---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/27/2019
ms.author: cynthn
ms.openlocfilehash: 11c9b2ea3ea054415f25f864651df28288aa0025
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266840"
---
Számos lehetőséget kihasználhat a virtuális gépek figyelésére a diagnosztikai és naplózási adatok összegyűjtésével, megtekintésével és elemzésével. A virtuális gép [](../articles/azure-monitor/overview.md) egyszerű figyeléséhez a Azure Portalban használhatja a virtuális gép áttekintő képernyőjét. A bővítmények [](../articles/virtual-machines/windows/extensions-features.md) használatával konfigurálhatja a virtuális gépek diagnosztikai szolgáltatásait további metrikai adatok gyűjtésére. Speciális figyelési lehetőségeket is használhat, például [Application Insights](../articles/azure-monitor/app/app-insights-overview.md) és [log Analytics](../articles/azure-monitor/log-query/log-query-overview.md).

## <a name="diagnostics-and-metrics"></a>Diagnosztika és mérőszámok 

A [diagnosztikai adatok](https://docs.microsoft.com/cli/azure/vm/diagnostics) gyűjteményét beállíthatja és figyelheti a Azure Portal [](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) , az Azure CLI, a Azure PowerShell és a programozási alkalmazások API-k mérőszámait használva. Megteheti például a következőt:

- **Figyelje meg a virtuális gép alapvető mérőszámait.** A Azure Portal áttekintés képernyőjén az alapszintű mérőszámok tartalmazzák a CPU-használatot, a hálózati használatot, a lemez bájtjainak teljes számát és a lemezes műveletek másodpercenkénti számát.

- **Engedélyezze a rendszerindítási diagnosztika gyűjteményét, és tekintse meg a Azure Portal használatával.** Ha a saját lemezképét az Azure-ba hozza, vagy akár a platform rendszerképeinek rendszerindítását is, számos oka lehet annak, hogy a virtuális gép nem indítható állapotba kerül. A virtuális gép létrehozásakor egyszerűen engedélyezheti a rendszerindítási diagnosztika **szolgáltatást** , ha a beállítások képernyő figyelés szakaszában a rendszerindítási diagnosztika lehetőségre kattint.

    A virtuális gépek indításakor a rendszerindítási diagnosztikai ügynök rögzíti a rendszerindítási kimenetet, és tárolja azt az Azure Storage-ban. Ezek az adatok a virtuális gépek rendszerindítási problémáinak hibaelhárításához használhatóak. A rendszerindítási diagnosztika nem lesz automatikusan engedélyezve, amikor parancssori eszközökről hoz létre virtuális gépet. Ahhoz, hogy a rendszerindítási diagnosztikát engedélyezni lehessen, tárfiókot kell létrehozni a rendszerindítási naplók tárolásához. Ha engedélyezi a rendszerindítási diagnosztikát a Azure Portal, automatikusan létrejön egy Storage-fiók.

    Ha nem engedélyezte a rendszerindítási diagnosztika használatát a virtuális gép létrehozásakor, később bármikor engedélyezheti az [Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)vagy egy [Azure Resource Manager sablon](../articles/virtual-machines/windows/extensions-diagnostics-template.md)használatával.

- **A vendég operációs rendszer diagnosztikai adatgyűjtési szolgáltatásának engedélyezése.** Amikor létrehoz egy virtuális gépet, lehetősége van arra, hogy a beállítások képernyőn engedélyezze a vendég operációs rendszer diagnosztikát. Ha engedélyezi a diagnosztikai adatok gyűjtését, a [Linux IaaSDiagnostics-bővítményét](../articles/virtual-machines/linux/diagnostic-extension.md) vagy a [Windows IaaSDiagnostics-bővítményét a rendszer](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) hozzáadja a virtuális géphez, amely lehetővé teszi további lemez-, processzor-és memória-adatok gyűjtését.

    Az összegyűjtött diagnosztikai adatok használatával konfigurálhatja a virtuális gépek automatikus skálázását. A naplókat is konfigurálhatja az adattároláshoz és riasztások beállításához, amelyekkel megtudhatja, hogy a teljesítmény nem egészen megfelelő-e.

## <a name="alerts"></a>Riasztások

A riasztásokat [](../articles/azure-monitor/platform/alerts-overview.md) meghatározott teljesítmény-mérőszámok alapján hozhatja létre. Példák azokra a problémákra, amelyekről értesítést kaphat, ha az átlagos CPU-használat meghaladja a megadott küszöbértéket, vagy a rendelkezésre álló szabad lemezterület egy bizonyos mennyiség alá csökken. A riasztásokat a [Azure Portal](../articles/azure-monitor/platform/alerts-classic-portal.md) [Azure PowerShell](../articles/azure-monitor/platform/alerts-classic-portal.md#with-powershell)vagy az [Azure CLI](../articles/azure-monitor/platform/alerts-classic-portal.md#with-azure-cli)használatával lehet konfigurálni.

## <a name="azure-service-health"></a>Azure Service Health

A [Azure Service Health](../articles/service-health/service-health-overview.md) személyre szabott útmutatást és támogatást nyújt, ha az Azure-szolgáltatások problémái vannak, és segít felkészülni a közelgő tervezett karbantartásra. Azure Service Health riasztást küld Önnek és csapatának a megcélzó és rugalmas értesítések használatával.

## <a name="azure-resource-health"></a>Azure Resource Health

Az [Azure Resource Health](../articles/service-health/resource-health-overview.md) segítséget nyújt a diagnosztizálásban és a támogatásban, ha egy Azure-probléma hatással van az erőforrásaira. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

## <a name="azure-activity-log"></a>Azure-tevékenységnapló

Az [Azure-tevékenység naplója](../articles/azure-monitor/platform/activity-logs-overview.md) egy előfizetési napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekre. A napló számos adattartományt tartalmaz, Azure Resource Manager operatív adatokból Service Health események frissítéseire. A Azure Portalban található műveletnapló elemre kattintva megtekintheti a virtuális gép naplóját.

A műveletnapló többek között a következőkre használható:

- Hozzon létre egy [riasztást egy tevékenység napló eseményén](../articles/azure-monitor/platform/activity-logs-overview.md).
- Egy külső szolgáltatás vagy egyéni elemzési megoldás (például a PowerBI) betöltéséhez [továbbítsa azt egy Event hub](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) számára.
- Elemezze a PowerBI a [PowerBI Content Pack](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)használatával.
- [Mentse azt egy Storage-fiókba](../articles/azure-monitor/platform/archive-activity-log.md) archiválás vagy manuális ellenőrzés céljából. Az adatmegőrzési időt (nap) megadhatja a log profil használatával.

A műveletnapló adatai [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)használatával vagy a [REST API-k figyelésével](https://docs.microsoft.com/rest/api/monitor/)is elérhetők.

Az [Azure-erőforrás-naplók](../articles/azure-monitor/platform/resource-logs-overview.md) a virtuális gép által kibocsátott naplók, amelyek a működésével kapcsolatos gazdag és gyakori információkat biztosítanak. Az erőforrás-naplók különböznek a tevékenység naplójától azáltal, hogy betekintést adnak a virtuális gépen végrehajtott műveletekre.

A diagnosztikai naplókhoz többek között a következőket teheti:

- A naplózáshoz vagy a manuális ellenőrzéshez [mentse őket egy Storage-fiókba](../articles/azure-monitor/platform/archive-diagnostic-logs.md) . Megadhatja a megőrzési időt (napokban) az erőforrás-diagnosztikai beállítások használatával.
- [Továbbíthatja őket a Event Hubs](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) számára külső szolgáltatás vagy egyéni elemzési megoldás (például PowerBI) betöltéséhez.
- Elemezze azokat [log Analyticsokkal](../articles/log-analytics/log-analytics-azure-storage.md).

## <a name="advanced-monitoring"></a>Speciális figyelés

- [Azure monitor](../articles/azure-monitor/overview.md) egy olyan szolgáltatás, amely figyeli a Felhőbeli és a helyszíni környezeteket, hogy fenntartsa a rendelkezésre állást és a teljesítményt. Átfogó megoldást kínál a Felhőbeli és a helyszíni környezetek telemetria gyűjtésére, elemzésére és működésének elvégzésére. Ez a szolgáltatás segít megérteni azt, hogy az alkalmazásai hogyan teljesítenek, valamint proaktív módon azonosítja a működésüket befolyásoló problémákat és azokat az erőforrásokat, amelyektől függenek. Telepíthet egy bővítményt egy Linux rendszerű [virtuális gépre](../articles/virtual-machines/linux/extensions-oms.md) vagy egy [Windows rendszerű virtuális gépre](../articles/virtual-machines/windows/extensions-oms.md) , amely telepíti a log Analytics-ügynököt a naplófájlok gyűjtésére és tárolására egy log Analytics munkaterületen.

    Windows és Linux rendszerű virtuális gépek esetén a naplók gyűjtésének ajánlott módja a Log Analytics-ügynök telepítése. A Log Analytics-ügynök virtuális gépen való telepítésének legegyszerűbb módja a Log Analytics virtuálisgép- [bővítmény](../articles/log-analytics/log-analytics-azure-vm-extension.md). A bővítmény használata leegyszerűsíti a telepítés folyamatát és automatikusan konfigurálja az ügynököt, hogy elküldje az adatokat a megadott Log Analytics-munkaterületre. Az ügynök automatikusan frissül, hogy mindig a legújabb funkciókkal és javításokkal bővüljön.

- A [Network Watcher](../articles/network-watcher/network-watcher-monitoring-overview.md) lehetővé teszi a virtuális gép és a hozzá tartozó erőforrások figyelését, ahogy azok a hálózathoz kapcsolódnak. A Network Watcher Agent bővítményt Linux rendszerű [virtuális gépre](../articles/virtual-machines/linux/extensions-nwa.md) vagy [Windowsos virtuális gépre](../articles/virtual-machines/windows/extensions-nwa.md)is telepítheti.

- [Azure monitor for VMS](../articles/azure-monitor/insights/vminsights-overview.md) az Azure-beli virtuális gépeket (VM) a Windows-és Linux-alapú virtuális gépek teljesítményének és állapotának elemzésével, beleértve a különböző folyamatokat és a más erőforrásokkal és külső folyamatok. 

## <a name="next-steps"></a>További lépések
- Végigvezeti a [Windows rendszerű virtuális gép figyelésének](../articles/virtual-machines/windows/tutorial-monitoring.md) lépésein Azure PowerShell vagy [egy linuxos virtuális gép figyelése az Azure CLI-vel](../articles/virtual-machines/linux/tutorial-monitoring.md).
- További információ a [monitorozás és a diagnosztika](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)szolgáltatással kapcsolatos ajánlott eljárásokról.
