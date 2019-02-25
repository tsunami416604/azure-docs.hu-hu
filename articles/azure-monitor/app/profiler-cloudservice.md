---
title: Profil élő Azure Cloud Services az Application insights segítségével |} A Microsoft Docs
description: Engedélyezze az Application Insights Profiler az Azure Cloud Servicesben.
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
ms.openlocfilehash: 32604c06c6a4325f7ae6cb45930de902a1366480
ms.sourcegitcommit: e88188bc015525d5bead239ed562067d3fae9822
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/24/2019
ms.locfileid: "56750327"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil élő Azure Cloud Services az Application insights segítségével

Ezek a szolgáltatások az Application Insights Profiler is telepítheti:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Az Azure Service Fabric-alkalmazások](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler az Azure Diagnostics bővítmény telepítve van. Csak akkor kell Profiler telepítéséhez, és a profilok küldése az Application Insights-erőforrást az Azure Diagnostics konfigurálása.

## <a name="enable-profiler-for-azure-cloud-services"></a>Az Azure Cloud Servicesben Profiler engedélyezése
1. Győződjön meg arról, hogy használ, [.NET-keretrendszer 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb. Ellenőrizze, hogy elegendő a *ServiceConfiguration.\*.cscfg* fájlok egy `osFamily` érték "5" vagy újabb verzió.

1. Adjon hozzá [az Application Insights SDK-t az Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

  >**A profiler WAD legújabb verziójában a Cloud Services részét képező programhiba van.** Annak érdekében, hogy a profiler használni egy felhőalapú szolgáltatás, csak a támogatott AI SDK 2.7.2 verzióra. Az AI SDK újabb verziója használatakor kell vissza a 2.7.2 annak érdekében, hogy a profiler használja.

1. Az Application Insights kérések nyomon követése:

    * ASP.NET webes szerepkörök esetében az Application Insights nyomon követheti a kérelmek automatikusan.

    * A feldolgozói szerepkörök esetében [adja hozzá a kódot a kérések nyomon követésére](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Adja meg az Azure Diagnostics bővítmény Profiler engedélyezése az alábbiak szerint:

    a. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* fájlt az alkalmazás-szerepkörökhöz az itt látható módon:  

      ![A diagnosztika konfigurációs fájl helye](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Ha nem találja a fájlt, tekintse meg [diagnosztika beállítása az Azure Cloud Services és Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Adja hozzá a következő `SinksConfig` szegmensben, mint a gyermekelemet `WadCfg`:  

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

    > [!NOTE]
    > Ha a *diagnostics.wadcfgx* fájlt is tartalmaz egy másik fogadó ApplicationInsights típusú, a következő kialakítási kulcs mindhárom egyeznie kell:  
    > * Az alkalmazás által használt kulcs. 
    > * A kulcs, amelyet a ApplicationInsights fogadó. 
    > * A kulcs, amelyet a ApplicationInsightsProfiler fogadó. 
    >
    > Az által használt tényleges kialakítási kulcs értéke annak a `ApplicationInsights` gyűjtése a *ServiceConfiguration.\*.cscfg* fájlokat. 
    > A Visual Studio 15.5 Azure SDK kiadása után csak a kialakítási kulcsot használunk az alkalmazás és a ApplicationInsightsProfiler fogadó kell egymással.

1. Az új konfigurációval diagnosztika a szolgáltatás üzembe helyezése, és az Application Insights Profiler az szolgáltatáson futtatásra van konfigurálva.
 
## <a name="next-steps"></a>További lépések

* Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](monitor-web-app-availability.md)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
* Lásd: [Profiler nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
* Profiler-problémák hibaelhárítása: [hibaelhárítási Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
