---
title: A Azure Diagnostics bővítmény áttekintése
description: Az Azure Diagnostics használata hibakereséshez, teljesítmény méréséhez, monitorozáshoz, Traffic Analysis in Cloud Services, Virtual Machines és Service Fabric
author: rboucher
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 02/13/2019
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: e0325a3bda912c95d8d27646bc1e80fff5ce10a8
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639425"
---
# <a name="what-is-azure-diagnostics-extension"></a>Mi az Azure Diagnostics bővítmény
Az Azure Diagnostics-bővítmény az Azure-ban található ügynök, amely lehetővé teszi a diagnosztikai adatgyűjtést egy telepített alkalmazáson. A diagnosztikai bővítményt számos különböző forrásból is használhatja. Jelenleg támogatott az Azure Cloud Service (klasszikus) webes és feldolgozói szerepkörök, Virtual Machines, virtuálisgép-méretezési csoportok és Service Fabric. Más Azure-szolgáltatások különböző diagnosztikai módszerekkel rendelkeznek. Lásd: [a monitorozás áttekintése az Azure-ban](../../azure-monitor/overview.md).

## <a name="linux-agent"></a>Linux-ügynök
A Linux rendszerű Virtual Machines a [bővítmény linuxos verziója](../../virtual-machines/extensions/diagnostics-linux.md) érhető el. A gyűjtött és a viselkedési statisztikák a Windows verziótól eltérőek.

## <a name="data-you-can-collect"></a>Gyűjtött adatok
A Azure Diagnostics bővítmény a következő típusú adatokat tudja gyűjteni:

| Adatforrás | Leírás |
| --- | --- |
| Teljesítményszámláló metrikái |Operációs rendszer és egyéni teljesítményszámlálók |
| Alkalmazásnaplók |Az alkalmazás által írt nyomkövetési üzenetek |
| Windows-eseménynaplók |A Windows-eseménynaplózási rendszernek eljuttatott információk |
| .NET EventSource-naplók |Események írása a .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) osztály használatával |
| IIS-naplók |AZ IIS-webhelyekkel kapcsolatos információk |
| [Jegyzékfájl-alapú ETW-naplók](https://docs.microsoft.com/windows/desktop/etw/about-event-tracing) |Windows esemény-nyomkövetés egy folyamat által generált eseményeket. 1 |
| Összeomlási memóriaképek (naplók) |Információ a folyamat állapotáról, ha egy alkalmazás összeomlik |
| Egyéni hibanaplók |Az alkalmazás vagy szolgáltatás által létrehozott naplók |
| Azure diagnosztikai infrastruktúra naplói |Információk a Azure Diagnosticsról |

(1) a ETW-szolgáltatók listájának lekéréséhez `c:\Windows\System32\logman.exe query providers` futtassa a konzol ablakban azt a gépet, amelyről adatokat szeretne gyűjteni.

## <a name="data-storage"></a>Adattárolás
A bővítmény az Ön által megadott [Azure Storage](diagnostics-extension-to-storage.md) -fiókban tárolja az adattárat.

Azt is elküldheti [Application Insightsnak](../../azure-monitor/app/cloudservices.md). 

Egy másik lehetőség, hogy továbbítsa az [Event hub](../../event-hubs/event-hubs-about.md)-nak, amely lehetővé teszi a nem Azure-beli figyelési szolgáltatásoknak való elküldését.

Lehetősége van arra is, hogy elküldje az adatait Azure Monitor metrikák idősorozat-adatbázisába. Jelenleg ez a fogadó csak a teljesítményszámlálók esetében alkalmazható. Lehetővé teszi, hogy a teljesítményszámlálók egyéni metrikaként legyenek elküldve. Ez a funkció előzetes verzióban érhető el. A Azure Monitor fogadó a következőket támogatja:
* A Azure Monitor eljuttatott teljesítményszámlálók beolvasása a [Azure monitor metrikák API](https://docs.microsoft.com/rest/api/monitor/) -kon keresztül.
* Riasztás a Azure Monitor elküldhető összes teljesítményszámlálók számára a [metrikai riasztások](../../azure-monitor/platform/alerts-overview.md) segítségével Azure monitor
* A helyettesítő karakterek kezelése a teljesítményszámlálók esetében a mérőszámban a "példány" dimenzió.  Ha például összegyűjtötte a "LogicalDisk (\*)/DiskWrites/sec" számlálót, akkor a "példány" dimenzióra kiszűrheti és eloszthatja a lemezre írás/mp-t a virtuális gépen lévő minden logikai lemezre (például C:).

A fogadó konfigurálásával kapcsolatos további információkért tekintse meg az [Azure Diagnostics-séma dokumentációját.](diagnostics-extension-schema-1dot3.md)

## <a name="costs"></a>Költségek
A fenti lehetőségek mindegyike költséget eredményezhet. Ügyeljen arra, hogy a váratlan számlák elkerülése érdekében megkeresse őket.  A Application Insights, az Event hub és az Azure Storage külön költségekkel jár a betöltéshez és a tárolt időponthoz képest. Az Azure Storage-ban az adatok örökre megmaradnak, ezért előfordulhat, hogy egy adott időszak után törölni szeretné a régebbi adatokat, hogy a költségek le legyenek tartva.    

## <a name="versioning-and-configuration-schema"></a>Verziószámozás és konfigurációs séma
Lásd: [Azure Diagnostics korábbi verziók és sémák](diagnostics-extension-schema.md).


## <a name="next-steps"></a>További lépések
Válassza ki, hogy melyik szolgáltatást szeretné összegyűjteni, és az alábbi cikkek megkezdéséhez használja a következő cikkeket. Az általános Azure diagnosztikai hivatkozásokat az adott feladatokra való hivatkozáshoz használhatja.

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services a Azure Diagnostics használatával
* Ha a Visual studiót használja, tekintse meg az első lépéseket a [Visual Studio használatával Cloud Services alkalmazás nyomon követéséhez](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) . Egyéb esetben lásd:
* [A Cloud Services figyelése Azure Diagnostics használatával](../../cloud-services/cloud-services-how-to-monitor.md)
* [Azure Diagnostics beállítása Cloud Services alkalmazásban](../../cloud-services/cloud-services-dotnet-diagnostics.md)

További speciális témakörök:

* [Azure Diagnostics használata a Application Insights használatával Cloud Services](../../azure-monitor/app/cloudservices.md)
* [Cloud Services alkalmazás folyamatának nyomon követése Azure Diagnostics](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [A Cloud Services diagnosztika beállítása a PowerShell használatával](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtuális gépek
* Ha a Visual studiót használja, tekintse meg a [Visual Studio használata az Azure Virtual Machines nyomkövetésének](/visualstudio/azure/vs-azure-tools-debug-cloud-services-virtual-machines) megkezdéséhez című témakört. Egyéb esetben lásd:
* [Azure Diagnostics beállítása Azure-beli virtuális gépen](/azure/virtual-machines/extensions/diagnostics-windows)

További speciális témakörök:

* [A PowerShell használata az Azure-beli diagnosztika beállításához Virtual Machines](../../virtual-machines/extensions/diagnostics-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Windows rendszerű virtuális gép létrehozása figyeléssel és diagnosztika Azure Resource Manager sablon használatával](../../virtual-machines/extensions/diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
Ismerkedjen meg [Service Fabric-alkalmazás figyelésével](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). A cikk elolvasása után a navigációs fában számos más Service Fabric diagnosztikai cikk is elérhető.

## <a name="general-articles"></a>Általános cikkek
* Ismerkedjen meg a [Azure Diagnostics teljesítményszámlálók használatával](../../cloud-services/diagnostics-performance-counters.md).
* Ha problémája van az Azure Storage-táblákban megjelenő vagy az adatait megtaláló diagnosztika szolgáltatással, olvassa el a [hibaelhárítási Azure Diagnostics](diagnostics-extension-troubleshooting.md)

