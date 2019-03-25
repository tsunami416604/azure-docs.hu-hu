---
title: Az Application Insights élő Azure Service Fabric-alkalmazások profil |} A Microsoft Docs
description: Profiler engedélyezése a Service Fabric-alkalmazás
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 0b1a06d181fc4d2a44d389d47d1f9480c2fdcb40
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58401102"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profil élő Azure Service Fabric-alkalmazásokat az Application insights segítségével

Ezek a szolgáltatások az Application Insights Profiler is telepítheti:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>Állítsa be a környezet üzemelő példány definíciója

Application Insights Profiler az Azure Diagnostics részét képezi. Az Azure Diagnostics bővítmény is telepítheti a Service Fabric-fürthöz tartozó Azure Resource Manager-sablon használatával. Get- [sablont, amely telepíti a Service Fabric-fürt Azure Diagnostics](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json).

Állítsa be a környezetet, hajtsa végre a következő műveleteket:

1. Győződjön meg arról, hogy használja a [.NET-keretrendszer 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb, elegendő ahhoz, hogy erősítse meg, hogy a telepített operációs rendszer `Windows Server 2012 R2` vagy újabb.

1. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) a Rendszertelepítési sablonfájl-bővítményt.

1. Adja hozzá a következő `SinksConfig` szegmensben, mint a gyermekelemet `WadCfg`. Cserélje le a `ApplicationInsightsProfiler` tulajdonság értékét a saját Application Insights-kialakítási kulcsot:  

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

      A diagnosztikai bővítményt a központi telepítési sablont történő hozzáadásával kapcsolatos további információkért lásd: [használható monitorozási és diagnosztikai egy Windows virtuális gép és az Azure Resource Manager-sablonokkal](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

1. A Service Fabric-fürt telepítése az Azure Resource Manager-sablon használatával.  
  Ha a beállítások helyesek, Application Insights Profiler telepítve, és engedélyezve van az Azure Diagnostics bővítmény telepítésekor. 

1. Az Application Insights hozzáadása a Service Fabric-alkalmazásokat.  
  A Profiler gyűjtéséhez profilok számára a kérelmek az alkalmazás kell kell nyomon követéséhez az Application Insights-műveletek. Útmutatásért olvassa el az állapot nélküli API-k [kérelmek követésének profilkészítés](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json). Egyéb típusú alkalmazások egyéni műveletek követése kapcsolatos további információkért tekintse meg a [Application Insights .NET SDK-val egyéni műveletek követése](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json).

1. Az alkalmazás újbóli üzembe helyezése.

> [TIPP] Virtuális gépek, a fenti JSON-alapú lépések helyett, hogy az Azure Portalon lépjen **virtuális gépek** > **diagnosztikai beállítások** > **Fogadók** > **beállítása diagnosztikai adatok küldése az Application Insights engedélyezve** , és válassza ki az Application Insights-fiókot vagy egy adott rendszerállapotkulcsot.

## <a name="next-steps"></a>További lépések

* Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](monitor-web-app-availability.md)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
* Lásd: [Profiler nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
* Segítség a Profiler kapcsolatos hibák elhárítása: [hibaelhárítási Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
