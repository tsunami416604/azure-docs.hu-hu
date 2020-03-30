---
title: Élő Azure Service Fabric-alkalmazások profilba való profilja az Application Insights segítségével
description: Profiler engedélyezése service Fabric-alkalmazáshoz
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 664d6eb377185613a1a5670daf6747b482c79d9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671613"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Élő Azure Service Fabric-alkalmazások profila application insightsmal

Az Application Insights Profiler ezeket a szolgáltatásokat is telepítheti:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure virtuális gépek](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>A környezet központi telepítési definíciójának beállítása

Az Application Insights Profiler az Azure Diagnostics részét képezi. Az Azure Diagnostics bővítményt egy Azure Resource Manager-sablon használatával telepítheti a Service Fabric-fürthöz. Szerezzen be egy [sablont, amely telepíti az Azure Diagnostics szolgáltatást nyújtó hálófürtre.](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

A környezet beállításához tegye a következő műveleteket:

1. A Profiler támogatja a .NET Framework és a .Net Core programot. A . [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) Elegendő annak megerősítéséhez, hogy a `Windows Server 2012 R2` telepített operációs rendszer vagy később. A Profiler támogatja a .NET Core 2.1 és újabb alkalmazásokat.

1. Keresse meg az [Azure Diagnostics bővítményt](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) a központi telepítési sablonfájlban.

1. Adja hozzá `SinksConfig` a következő szakaszt a gyermekelemként. `WadCfg` Cserélje `ApplicationInsightsProfiler` le a tulajdonság értékét a saját Application Insights instrumentation kulcs:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      A Diagnosztikai bővítmény központi telepítési sablonhoz való hozzáadásáról a [Figyelés és diagnosztika használata Windows virtuális gépés az Azure Resource Manager-sablonokkal című témakörben](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)olvashat.

1. Telepítse a Service Fabric-fürtaz Azure Resource Manager-sablon használatával.  
  Ha a beállítások helyesek, az Application Insights Profiler lesz telepítve és engedélyezve, ha az Azure Diagnostics bővítmény telepítve van. 

1. Add hozzá az Application Insightsot a Service Fabric-alkalmazáshoz.  
  Ahhoz, hogy a Profiler profilokat gyűjtsön a kérésekhez, az alkalmazásnak nyomon kell követnie a műveleteket az Application Insights segítségével. Állapotnélküli API-k esetén a [profilkészítési kérelmek nyomon követésére](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)vonatkozó utasításokban tájékozódhat. A más típusú alkalmazások egyéni műveleteinek nyomon követéséről az Egyéni műveletek nyomon követése az [Application Insights .NET SDK segítségével](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)című témakörben talál további információt.

1. Telepítse újra az alkalmazást.


## <a name="next-steps"></a>További lépések

* Hozzon létre forgalmat az alkalmazásba (például indítson el egy [rendelkezésre állási tesztet).](monitor-web-app-availability.md) Ezután várjon 10–15 percet, amíg a nyomkövetések elindulnak az Application Insights-példányba.
* Tekintse meg [a Profiler-nyomkövetéseket](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
* A Profilozóval kapcsolatos problémák elhárításával kapcsolatos segítségért olvassa el a [Profilozó hibaelhárításcímű témakört.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)
