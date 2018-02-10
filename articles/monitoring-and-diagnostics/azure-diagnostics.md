---
title: "Az Azure Diagnostics áttekintése |} Microsoft Docs"
description: "Az Azure diagnostics használjon hibakeresés, méri a teljesítményt, figyelés, a forgalom elemzése a felhőszolgáltatások, virtuális gépek és a service fabric"
services: multiple
documentationcenter: .net
author: rboucher
manager: 
editor: 
ms.assetid: baad40d8-c915-4f93-b486-8b160bf33463
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2017
ms.author: robb
ms.openlocfilehash: fbeacd2acfd2fba9d26e7cbc94d8660d15c75642
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="what-is-azure-diagnostics"></a>Mi az Azure Diagnostics
Az Azure Diagnostics a funkció, amely lehetővé teszi a telepített alkalmazás diagnosztikai adatok gyűjtésére Azure belül. Számos különböző forrásokból származó használja a diagnosztika bővítményét. Azure Cloud Service webes és feldolgozói szerepkörök, Microsoft Windows és a Service Fabric rendszert futtató Azure virtuális gépek jelenleg támogatott vannak. Más Azure-szolgáltatásokkal rendelkezik saját külön diagnosztika.

## <a name="data-you-can-collect"></a>Begyűjtheti adatok
Az Azure Diagnostics össze tudják gyűjteni a következő típusú adatok:

| Adatforrás | Leírás |
| --- | --- |
| Teljesítményszámlálók |Operációs rendszer és az egyéni teljesítményszámlálói |
| Alkalmazás-naplók |Az alkalmazás nyomkövetési üzenetek |
| Windows-Eseménynapló |A Windows-esemény naplózása rendszerben küldött információk |
| .NET eseményforrás |Kód írása a .NET használatával események [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) osztály |
| IIS-napló |IIS-webhelyek kapcsolatos információk |
| Jegyzékfájl alapú ETW |A folyamat által generált Windows nyomkövetési eseményeinek |
| Összeomlási memóriaképek |Egy alkalmazás összeomlása esetén a folyamat állapotával kapcsolatos információk |
| Egyéni hibanaplókat |Az alkalmazás vagy szolgáltatás által létrehozott naplók |
| Azure-infrastruktúra diagnosztikai naplók |Maga diagnosztikai információk |

Az Azure diagnostics-bővítmény, az adatok átviteléhez az Azure storage-fiók, vagy elküldi a hasonló szolgáltatások [Application Insights](../application-insights/app-insights-cloudservices.md). Az adatok a Hibakeresés és hibaelhárítás, méri a teljesítményt, erőforrás-használat, a forgalom elemzése és a kapacitástervezés figyelés és naplózás is használhatja.

## <a name="versioning"></a>Verziókezelés
Lásd: [Azure Diagnostics Rendszerverzió-előzmények](azure-diagnostics-versioning-history.md).

## <a name="next-steps"></a>További lépések
Válassza ki a diagnosztikai adatok gyűjtéséhez és használja a következő cikkeket a kezdéshez kívánt melyik szolgáltatás. Az általános Azure diagnostics hivatkozásokkal az egyes feladatok esetében.

## <a name="web-apps"></a>Web Apps
Vegye figyelembe, hogy nem használó webalkalmazások Azure Diagnostics. Keresse meg a megfelelő adatokat: [webalkalmazások](../app-service/web-sites-enable-diagnostic-log.md)

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services Azure Diagnostics használatával
* Ha a Visual Studio használatával, lásd: [nyomon követésére, a Cloud Services alkalmazás használja a Visual Studio](../vs-azure-tools-debug-cloud-services-virtual-machines.md) a kezdéshez. Egyéb esetben lásd:
* [Felhőszolgáltatások használata az Azure Diagnostics figyelése](../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services alkalmazásokban Azure Diagnostics beállítása](../cloud-services/cloud-services-dotnet-diagnostics.md)

Az összetettebb témákra lásd:

* [Az Application insights szolgáltatással használ az Azure Diagnostics Cloud Services csomag](../application-insights/app-insights-cloudservices.md)
* [A folyamatot az Azure Diagnostics Felhőszolgáltatások alkalmazás nyomkövetési](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [A Cloud Services diagnosztika beállítása a PowerShell használatával](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines-using-azure-diagnostics"></a>Virtuális gépek Azure Diagnostics használatával
* Ha a Visual Studio használatával, lásd: [Visual Studio használata az Azure virtuális gépek nyomkövetési](../vs-azure-tools-debug-cloud-services-virtual-machines.md) a kezdéshez. Egyéb esetben lásd:
* [Állítsa be az Azure virtuális gép Azure Diagnostics](../virtual-machines-dotnet-diagnostics.md)

Az összetettebb témákra lásd:

* [Azure virtuális gépeken diagnosztika beállítása a PowerShell használatával](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Windows virtuális gép létrehozása a figyelési és -diagnosztika Azure Resource Manager-sablon](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric-using-azure-diagnostics"></a>A Service Fabric Azure Diagnostics használatával
A kezdéshez [a Service Fabric-alkalmazás figyelése](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Más Service Fabric diagnosztika cikkeket érhetők el a bal oldali navigációs fában, miután a cikkhez.

## <a name="general-azure-diagnostics-articles"></a>Általános Azure Diagnostics cikkek
* [Az Azure Diagnostics séma konfigurációs](https://msdn.microsoft.com/library/azure/mt634524.aspx) -útmutató a fájl az adatgyűjtésre és útvonal-diagnosztikai adatok módosításához. Vegye figyelembe, hogy is használhatja a Visual Studio a fájl módosítása.
* [Az Azure Storage Azure diagnosztikai adatok tárolási módjára](../cloud-services/cloud-services-dotnet-diagnostics-storage.md) -tábla és blobokat, ha a diagnosztikai adatok írása a nevét.
* Ismerje meg, hogy [teljesítményszámlálók használata az Azure Diagnostics](../cloud-services/diagnostics-performance-counters.md).
* Ismerje meg, hogy [útvonal Azure diagnosztikai adatokat az Application Insights részére](azure-diagnostics-configure-application-insights.md)
* Ha problémája van diagnosztika indítása, vagy hogy a rendszer az adatok Azure Storage-táblákat, lásd: [Azure Diagnostics hibaelhárítása](azure-diagnostics-troubleshooting.md)
