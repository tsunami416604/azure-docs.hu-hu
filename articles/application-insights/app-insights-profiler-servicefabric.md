---
title: Az Application Insights élő Azure Service Fabric-alkalmazások profil |} A Microsoft Docs
description: A Service Fabric-alkalmazás profiler engedélyezése
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
ms.openlocfilehash: 3808b3e93ed7e3ad374054c3c32fd54930f50972
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606571"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Profil élő Azure Service Fabric-alkalmazásokat az Application insights segítségével

Ezek a szolgáltatások az Application Insights profiler is telepítheti:
* [Azure App Service](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Felhőszolgáltatások](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Állítsa be a környezet üzemelő példány definíciója

Application Insights Profiler megtalálható a Windows Azure Diagnostics (WAD). A WAD-bővítmény a Service Fabric-fürt számára egy Azure erőforrás-kezelő sablon használatával is telepíthető. Van itt egy példa sablon: [**A sablon, amely WAD telepíti a Service Fabric-fürtön.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Állítsa be a környezetet, hajtsa végre a következő műveleteket:
1. Győződjön meg arról, hogy használja a [.NET-keretrendszer 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb, elegendő ahhoz, hogy erősítse meg, hogy a telepített operációs rendszer `Windows Server 2012 R2` vagy újabb.

1. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) kiterjesztése a központi telepítési sablont a fájlt, és ezután adja hozzá a következő `SinksConfig` szegmensben, mint a gyermekelemet `WadCfg`. Cserélje le a `ApplicationInsightsProfiler` tulajdonság értékét a saját Application Insights-kialakítási kulcsot:  

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
1. Telepítse a Service Fabric-fürtön az Azure Resource Manager-sablon használatával. Ha a beállítások helyesek, Application Insights Profiler telepítve van, és engedélyezve van, amikor a WAD-bővítmény telepítve van. 
1. Az Application Insights hozzáadása a Service Fabric-alkalmazásokat. Az alkalmazás kell kell kérelem adatok küldését az application insights ahhoz, hogy a profiler a kérelmek profilok gyűjtéséhez. Talál útmutatást [itt.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)
1. Az alkalmazás újbóli üzembe helyezése.

> [TIPP] Virtuális gépek a fenti lépéseket a json-alapú alternatívát, hogy az Azure Portalon lépjen **virtuális gépek** > **diagnosztikai beállítások** >  **Fogadók** > beállítása diagnosztikai adatok küldése az Application Insights **engedélyezve** és a egy Application Insights-fiókot vagy egy adott rendszerállapotkulcsot jelölje ki.

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
- Lásd: [Profiler nyomkövetések](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- Segítség a profiler problémák elhárítása a [hibaelhárítási Profiler](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).