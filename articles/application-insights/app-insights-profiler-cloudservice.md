---
title: Profil élő Azure-felhőszolgáltatásokat az Application insights segítségével |} A Microsoft Docs
description: A Cloud Services Application Insights Profiler engedélyezése.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 445e607b6b0a21f840ab633b3a5a3779f49fdd98
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142428"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil élő az Application insights szolgáltatással az Azure cloud services

Ezek a szolgáltatások az Application Insights profiler is telepítheti:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric-alkalmazások](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuális gépek](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler telepítve van a Windows Azure Diagnostics (WAD) bővítmény. Csak a profiler telepítéséhez, és a profilok küldése az Application Insights-erőforrásra WAD konfigurálásához kell.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Az Azure Cloud Service profiler engedélyezése
1. Ellenőrizze, hogy Önnek használatával [.NET-keretrendszer 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb.  Ellenőrizze, hogy elegendő a *ServiceConfiguration.\*. cscfg* fájlok egy `osFamily` érték "5" vagy újabb verzió.
1. Adjon hozzá [felhőalapú szolgáltatás, Application Insights SDK](app-insights-cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Az Application Insights kérések nyomon követése:

    ASP.Net webes szerepkörök esetében az Application Insights nyomon követheti a kérelmek automatikusan.

    A feldolgozói szerepkörök esetében [adja hozzá a kódot a kérések nyomon követésére.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. A profiler engedélyezéséhez a Windows Azure Diagnostics (WAD) bővítmény konfigurálása.



    1. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* fájlt az alkalmazás-szerepkörökhöz az itt látható módon:  

       ![A diagnosztika konfigurációs fájl helye](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        Ha a fájl nem található, további információk az Azure Cloud Services-projektben a diagnosztikai bővítmény engedélyezése: [diagnosztika beállítása az Azure Cloud Services és virtual machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Adja hozzá a következő `SinksConfig` szegmensben, mint a gyermekelemet `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    >   **Megjegyzés:** Ha a *diagnostics.wadcfgx* fájlt is tartalmaz egy másik fogadó típusa `ApplicationInsights`, a következő kialakítási kulcs mindhárom egyeznie kell:  
    >  * Az alkalmazás által használt kulcs.  
    >  * A kulcs, amelyet a `ApplicationInsights` fogadó.  
    >  * A kulcs, amelyet a `ApplicationInsightsProfiler` fogadó.  
    >
    > Az által használt tényleges kialakítási kulcs értéke annak a `ApplicationInsights` gyűjtése a *ServiceConfiguration.\*. cscfg* fájlokat.  
    > A Visual Studio 15.5 Azure SDK-verzió, csak az alkalmazás által használt kialakítási kulcs után és a `ApplicationInsightsProfiler` fogadó kell egymással.
1. Az új diagnosztikai konfigurációval a szolgáltatás üzembe helyezése és Application Insights Profiler lesz konfigurálva, a szolgáltatás futtatásához.
 
## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
- Lásd: [Profiler nyomkövetések](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- Segítség a profiler problémák elhárítása a [hibaelhárítási Profiler](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).