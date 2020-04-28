---
title: fájl belefoglalása
description: fájl belefoglalása
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2019
ms.author: cynthn
ms.openlocfilehash: feea9696316723a2750be6fc1e13001224320324
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865629"
---
Az Azure-ban üzemeltetett virtuális gépek jelentős növekedésével fontos, hogy azonosítsa azokat a teljesítmény- és állapotproblémákat, amelyek hatással vannak az általuk támogatott alkalmazásokra és infrastruktúra-szolgáltatásokra. Az alapvető figyelést alapértelmezés szerint a CPU-használat, a lemezkihasználtság, a memóriakihasználtság és a gazdahipervizor által gyűjtött hálózati forgalom biztosítja az Azure-ban. További metrika- és naplóadatok gyűjthetők [bővítmények](../articles/virtual-machines/windows/extensions-features.md) használatával a vendég operációs rendszer ből származó virtuális gépek diagnosztika konfigurálásához.

A vendég operációs rendszer, a .NET alapú vagy a Virtuális gépen belül futó Java webalkalmazás-összetevők teljesítmény- és állapotproblémáinak észlelése és diagnosztizálása érdekében az Azure Monitor átfogó funkciókkal, például a virtuális gépek azure-figyelőjével és az Application Insights-mal biztosítja a központosított figyelést.

## <a name="diagnostics-and-metrics"></a>Diagnosztika és metrikák 

Beállíthatja és figyelheti a [diagnosztikai adatok gyűjtését](https://docs.microsoft.com/cli/azure/vm/diagnostics) az Azure Portalon, az Azure CLI, az Azure PowerShell és a programozási alkalmazások programozási felületein (API-k) [metrikák](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md) használatával. Megteheti például a következőt:

- **Vegye figyelembe a virtuális gép alapvető metrikák.** Az Azure Portal áttekintő képernyőjén a bemutatott alapvető metrikák közé tartozik a PROCESSZOR-használat, a hálózati használat, a lemezbájtok száma és a lemezműveletek másodpercenkénti száma.

- **Engedélyezze a rendszerindítási diagnosztika gyűjteményét, és tekintse meg az Azure Portalhasználatával.** Amikor saját lemezképet hoz az Azure-ba, vagy akár a platformrendszerképek egyikének indításakor számos oka lehet annak, hogy a virtuális gép nem indítható állapotba kerül. A rendszerindítási diagnosztika egyszerűen engedélyezhető, ha virtuális gép létrehozásakor az **Engedélyezve** a rendszerindítási diagnosztika elemre kattint a Beállítások képernyő Figyelés szakaszában.

    Virtuális gépindításakor a rendszerindító diagnosztikai ügynök rögzíti a rendszerindító kimenetet, és tárolja azt az Azure storage-ban. Ezek az adatok a virtuális gépek rendszerindítási problémáinak hibaelhárításához használhatóak. A rendszerindítási diagnosztika nem lesz automatikusan engedélyezve, ha parancssori eszközökből hoz létre virtuális gépet. Ahhoz, hogy a rendszerindítási diagnosztikát engedélyezni lehessen, tárfiókot kell létrehozni a rendszerindítási naplók tárolásához. Ha engedélyezi a rendszerindítási diagnosztikát az Azure Portalon, a rendszer automatikusan létrehoz egy tárfiókot.

    Ha a virtuális gép létrehozásakor nem engedélyezte a rendszerindítási diagnosztikát, később bármikor engedélyezheti azt [az Azure CLI](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics), [az Azure PowerShell](https://docs.microsoft.com/powershell/module/az.compute/set-azvmbootdiagnostic)vagy az [Azure Resource Manager-sablon](../articles/virtual-machines/windows/extensions-diagnostics-template.md)használatával.

- **Engedélyezze a vendég operációsrendszer diagnosztikai adatainak gyűjtését.** Virtuális gép létrehozásakor lehetősége van a beállítások képernyőn a vendég operációs rendszer diagnosztika engedélyezéséhez. Ha engedélyezi a diagnosztikai adatok gyűjtését, az [IaaSDiagnostics bővítmény Linux](../articles/virtual-machines/linux/diagnostic-extension.md) vagy az [IaaSDiagnostics bővítmény a Windows](../articles/virtual-machines/windows/ps-extensions-diagnostics.md) a virtuális gép, amely lehetővé teszi, hogy további lemez,CPU és a memória adatok gyűjtése.

    Az összegyűjtött diagnosztikai adatok használatával konfigurálhatja a virtuális gépek automatikus skálázás. Az Azure Monitor Naplók az adatok [tárolására](../articles/azure-monitor/platform/data-platform-logs.md) is konfigurálhatók, és riasztásokat állíthat be, amelyek értesítik, ha a teljesítmény nem megfelelő.

## <a name="alerts"></a>Riasztások

Riasztásokat [alerts](../articles/azure-monitor/platform/alerts-overview.md) adott teljesítménymutatók alapján hozhat létre. Példák a problémák, amelyeket figyelmeztetni lehet, például, ha az átlagos CPU-használat meghaladja egy bizonyos küszöbértéket, vagy a rendelkezésre álló szabad lemezterület egy bizonyos összeg alá csökken. A riasztások konfigurálhatók az [Azure Portalon](../articles/azure-monitor/platform/alerts-metric.md#create-with-azure-portal), [az Azure Resource Manager-sablonok](../articles/azure-monitor/platform/alerts-metric-create-templates.md)vagy [az Azure CLI](../articles/azure-monitor/platform/alerts-metric.md#with-azure-cli)használatával.

## <a name="azure-service-health"></a>Azure Service Health

[Az Azure Service Health](../articles/service-health/service-health-overview.md) személyre szabott útmutatást és támogatást nyújt, ha az Azure-szolgáltatásokban felmerülő problémák érintik Önt, és segít felkészülni a közelgő tervezett karbantartásra. Az Azure Service Health célzott és rugalmas értesítések használatával figyelmezteti Önt és csapatait.

## <a name="azure-resource-health"></a>Azure Resource Health

[Az Azure Resource állapotának](../articles/service-health/resource-health-overview.md) segítségével diagnosztizálhatja és támogatást kaphat, ha egy Azure-probléma hatással van az erőforrásokra. Tájékoztatja az erőforrásai aktuális és korábbi állapotáról, és segít a problémák kezelésében. A Resource Health műszaki támogatást nyújt, ha segítségre van szüksége az Azure szolgáltatásait érintő problémákkal kapcsolatban.

## <a name="azure-activity-log"></a>Azure-tevékenységnapló

Az [Azure-tevékenységnapló](../articles/azure-monitor/platform/platform-logs-overview.md) egy előfizetési napló, amely betekintést nyújt az Azure-ban bekövetkezett előfizetési szintű eseményekbe. A napló egy adattartományt tartalmaz, az Azure Resource Manager működési adataitól a Service Health-események frissítéseiig. Az Azure Portalon a Tevékenységnapló elemre kattintva megtekintheti a virtuális gép naplóját.

A tevékenységnaplóval többek között a következőket teheti:

- Hozzon létre [riasztást egy tevékenységnapló-eseményen.](../articles/azure-monitor/platform/platform-logs-overview.md)
- [Továbbítsa egy eseményközpontba,](../articles/azure-monitor/platform/activity-logs-stream-event-hubs.md) hogy egy külső szolgáltatás vagy egyéni elemzési megoldás, például a Power BI betöltést.
- Elemezze a Power BI-ban a [Power BI tartalomcsomagjával.](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/)
- [Mentse el egy tárfiókba](../articles/azure-monitor/platform/archive-activity-log.md) archiváláshoz vagy manuális ellenőrzéshez. Megadhatja a megőrzési időt (napokban) a naplóprofil használatával.

A tevékenységnapló-adatokat az [Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.insights/), az [Azure CLI](https://docs.microsoft.com/cli/azure/monitor)vagy a [REST API-k figyelése](https://docs.microsoft.com/rest/api/monitor/)segítségével is elérheti.

[Az Azure Resource Logs](../articles/azure-monitor/platform/platform-logs-overview.md) a virtuális gép által kibocsátott naplók, amelyek gazdag, gyakori adatokat biztosítanak a működéséről. Az erőforrásnaplók eltérnek a tevékenységnaplótól azáltal, hogy betekintést nyújtanak a virtuális gépen belül végrehajtott műveletekbe.

A diagnosztikai naplókkal többek között a következő műveleteket teheti meg:

- [Mentse őket egy tárfiókba](../articles/azure-monitor/platform/archive-diagnostic-logs.md) naplózásra vagy manuális ellenőrzésre. Megadhatja a megőrzési idő (napokban) az erőforrás-diagnosztikai beállítások használatával.
- [Továbbítsa őket az Event Hubs-ba](../articles/azure-monitor/platform/resource-logs-stream-event-hubs.md) egy külső szolgáltatás vagy egyéni elemzési megoldás, például a Power BI általi betöltéshez.
- Elemezze őket a [Log Analytics](../articles/log-analytics/log-analytics-azure-storage.md)segítségével.

## <a name="advanced-monitoring"></a>Speciális figyelés

Az Azure virtuális gép és a virtuális gép méretezési csoportja által támogatott alkalmazás vagy szolgáltatás láthatóságához, a vendég operációs rendszerrel vagy a virtuális gépen futó számítási feladattal kapcsolatos problémák azonosításához, hogy megállapítsa, ha az hatással van az alkalmazás rendelkezésre állására vagy teljesítményére, vagy az alkalmazással kapcsolatos probléma, engedélyezze az [Azure-figyelő virtuális gépekés](../articles/azure-monitor/insights/vminsights-overview.md) [az Application Insights](../articles/azure-monitor/app/app-insights-overview.md)számára.

Az Azure Monitor virtuális gépek figyeli az Azure virtuális gépek (VM) a Windows és a Linux virtuális gépek teljesítményének és állapotának elemzésével, beleértve a különböző folyamatok és az összekapcsolt függőségek más erőforrások és külső folyamatok általa felderített. Számos trendteljesítmény-diagramot tartalmaz, amelyek segítenek a problémák kivizsgálásában és a virtuális gépek kapacitásának felmérésében. A függőségi térkép a figyelt és nem figyelt gépeket, a folyamatok és a gépek közötti sikertelen és aktív hálózati kapcsolatokat jeleníti meg, és a szabványos hálózati kapcsolatmérőszámokkal rendelkező trenddiagramokat jeleníti meg. Az Application Insights segítségével figyelheti az alkalmazást, és rögzítheti a telemetriai adatokat, például a HTTP-kérelmeket, a kivételeket stb., így korrelálhatja a virtuális gépek és az alkalmazás közötti problémákat. Konfigurálja [az Azure Monitor riasztások riasztást](../articles/azure-monitor/platform/alerts-overview.md) a fontos körülmények észlelése az Azure Monitor virtuális gépekhez gyűjtött adatok figyelése.

## <a name="next-steps"></a>További lépések

- Az Azure PowerShell segítségével a [Windows virtuális gép figyelése](../articles/virtual-machines/windows/tutorial-monitoring.md) vagy [a Linux virtuális gép figyelése az Azure CLI segítségével](../articles/virtual-machines/linux/tutorial-monitoring.md)című lépések lépéseit.

- További információ a [figyelés és a diagnosztika gyakorlati tanácsairól.](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)
