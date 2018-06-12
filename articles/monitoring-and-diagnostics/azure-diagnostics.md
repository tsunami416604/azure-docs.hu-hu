---
title: Az Azure Diagnostics-bővítmény áttekintése
description: Az Azure diagnostics használjon hibakeresés, méri a teljesítményt, figyelés, a forgalom elemzése a felhőszolgáltatások, virtuális gépek és a service fabric
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/01/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: b4ec82112ea0defcea4f687abaad7d96627cb902
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35267679"
---
# <a name="what-is-azure-diagnostics-extension"></a>Mi az Azure Diagnostics bővítményt
Az Azure Diagnostics-bővítmény egy olyan ügynök, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure-ban. Számos különböző forrásokból származó használja a diagnosztika bővítményét. Azure Cloud Service (klasszikus) webes és feldolgozói szerepkörök, virtuális gépek esetében támogatott vannak, és a Service Fabric virtuálisgép-méretezési készlet. Más Azure-szolgáltatásokkal rendelkezik különböző diagnosztika módszerek. Lásd: [figyelőeszközök az Azure-ban](monitoring-overview.md). 

## <a name="linux-agent"></a>Linux-ügynök
A [Linux verzió](../virtual-machines/linux/diagnostic-extension.md) futó Linux virtuális gépek érhető el. A gyűjtött és viselkedés érdekében eltérőek lehetnek a Windows-verzió. 

## <a name="data-you-can-collect"></a>Begyűjtheti adatok
Az Azure Diagnostics bővítmény össze tudják gyűjteni a következő típusú adatok:

| Adatforrás | Leírás |
| --- | --- |
| Teljesítményszámlálók |Operációs rendszer és az egyéni teljesítményszámlálói |
| Alkalmazás-naplók |Az alkalmazás nyomkövetési üzenetek |
| Windows-eseménynaplók |A Windows-esemény naplózása rendszerben küldött információk |
| .NET eseményforrás |Kód írása a .NET használatával események [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) osztály |
| IIS-naplók |IIS-webhelyek kapcsolatos információk |
| Jegyzékfájl alapú ETW |A folyamat által generált Windows nyomkövetési eseményeinek |
| összeomlási memóriaképek, |Egy alkalmazás összeomlása esetén a folyamat állapotával kapcsolatos információk |
| egyéni hibanaplók, |Az alkalmazás vagy szolgáltatás által létrehozott naplók |
| Azure-infrastruktúra diagnosztikai naplók |Maga diagnosztikai információk |

## <a name="data-storage"></a>Adattárolás
A bővítmény tárolja az adatokat a egy [Azure Storage-fiók](azure-diagnostics-storage.md) megadott. 

Is küldhetünk, hogy [Application Insights](../application-insights/app-insights-cloudservices.md). Lehetősége úgy, hogy adatfolyamként történő [Eseményközpont](../event-hubs/event-hubs-what-is-event-hubs.md), amely ezután lehetővé teszi a montoring-Azure szolgáltatásokhoz való küldéshez. 


## <a name="versioning-and-configuration-schema"></a>Verziókövetés és a konfigurációs séma
Lásd: [Azure Diagnostics verzióelőzmények és séma](azure-diagnostics-versioning-history.md).


## <a name="next-steps"></a>További lépések
Válassza ki a diagnosztikai adatok gyűjtéséhez és használja a következő cikkeket a kezdéshez kívánt melyik szolgáltatás. Az általános Azure diagnostics hivatkozásokkal az egyes feladatok esetében.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services Azure Diagnostics használatával
* Ha a Visual Studio használatával, lásd: [nyomon követésére, a Cloud Services alkalmazás használja a Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md) a kezdéshez. Egyéb esetben lásd:
* [Felhőszolgáltatások használata az Azure Diagnostics figyelése](../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services alkalmazásokban Azure Diagnostics beállítása](../cloud-services/cloud-services-dotnet-diagnostics.md)

Az összetettebb témákra lásd:

* [Az Application insights szolgáltatással használ az Azure Diagnostics Cloud Services csomag](../application-insights/app-insights-cloudservices.md)
* [A folyamatot az Azure Diagnostics Felhőszolgáltatások alkalmazás nyomkövetési](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [A Cloud Services diagnosztika beállítása a PowerShell használatával](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuális gépek
* Ha a Visual Studio használatával, lásd: [Visual Studio használata az Azure virtuális gépek nyomkövetési](../vs-azure-tools-debug-cloud-services-virtual-machines.md) a kezdéshez. Egyéb esetben lásd:
* [Állítsa be az Azure virtuális gép Azure Diagnostics](../virtual-machines-dotnet-diagnostics.md)

Az összetettebb témákra lásd:

* [Azure virtuális gépeken diagnosztika beállítása a PowerShell használatával](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Windows virtuális gép létrehozása a figyelési és -diagnosztika Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
A kezdéshez [a Service Fabric-alkalmazás figyelése](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Más Service Fabric diagnosztika cikkeket érhetők el a bal oldali navigációs fában, miután a cikkhez.

## <a name="general-articles"></a>Általános cikkek
* Ismerje meg, hogy [teljesítményszámlálók használata az Azure Diagnostics](../cloud-services/diagnostics-performance-counters.md).
* Ha problémája van diagnosztika indítása, vagy hogy a rendszer az adatok Azure storage-táblákat, lásd: [Azure Diagnostics hibaelhárítása](azure-diagnostics-troubleshooting.md)
