---
title: Az Application Insights élő Azure Service Fabric-alkalmazások profil |} A Microsoft Docs
description: Profiler engedélyezése a Service Fabric-alkalmazás
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 7d47310217c06689a6088aa5c908223b41aadbfa
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435481"
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
  A Profiler gyűjtéséhez a kérelmek profilok, az alkalmazás kell kell kérelem adatok küldését az Application Insights. További információkért látogasson el a [Service Fabric-projektekkel készült Application Insights SDK](https://github.com/Microsoft/ApplicationInsights-ServiceFabric) lapot.

1. Az alkalmazás újbóli üzembe helyezése.

> [TIPP] Virtuális gépek, a fenti JSON-alapú lépések helyett, hogy az Azure Portalon lépjen **virtuális gépek** > **diagnosztikai beállítások** > **Fogadók** > **beállítása diagnosztikai adatok küldése az Application Insights engedélyezve** , és válassza ki az Application Insights-fiókot vagy egy adott rendszerállapotkulcsot.

## <a name="next-steps"></a>További lépések

* Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](monitor-web-app-availability.md)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
* Lásd: [Profiler nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
* Segítség a Profiler kapcsolatos hibák elhárítása: [hibaelhárítási Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
