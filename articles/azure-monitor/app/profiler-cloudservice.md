---
title: Élő Azure Felhőszolgáltatások profila application insightsmal | Microsoft dokumentumok
description: Engedélyezze az Application Insights Profiler szolgáltatást az Azure Cloud Services hez.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 3fbeb1120e97a884135cd4622a49ef97fd43e58e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671664"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Élő Azure Felhőszolgáltatások profila application insightssegítségével

Az Application Insights Profiler ezeket a szolgáltatásokat is telepítheti:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric-alkalmazások](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure virtuális gépek](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Az Application Insights Profiler az Azure Diagnostics bővítményrel van telepítve. Csak konfigurálnia kell az Azure Diagnostics-t a Profiler telepítéséhez és profilok küldéséhez az Application Insights-erőforrásba.

## <a name="enable-profiler-for-azure-cloud-services"></a>Profilozó engedélyezése az Azure Felhőszolgáltatásokhoz
1. Ellenőrizze, hogy a [.NET Framework 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb rendszert használja-e. Ha a 4-es operációsrendszer-családot használja, telepítenie kell a .NET Framework 4.6.1-es vagy újabb [rendszert egy indítási feladattal.](https://docs.microsoft.com/azure/cloud-services/cloud-services-dotnet-install-dotnet) Az OS Family 5 alapértelmezés szerint a .NET Framework kompatibilis verzióját tartalmazza. 

1. Adja hozzá [az Application Insights SDK-t az Azure Cloud Services szolgáltatáshoz.](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json)

    **A hiba a profiler, hogy a hajók a WAD a Cloud Services ki lett javítva.** A WAD (1.12.2.0) felhőszolgáltatásokhoz készült legújabb verziója az App Insights SDK összes legújabb verziójával működik. A Cloud Service-üzemeltetés gazdagépei automatikusan frissítik a WAD-t, de az nem azonnali. A frissítés kényszerítéséhez újratelepítheti a szolgáltatást, vagy újraindíthatja a csomópontot.

1. A kérelmek nyomon követése az Application Insights segítségével:

    * A webes szerepkörök ASP.NET az Application Insights automatikusan nyomon követheti a kérelmeket.

    * Feldolgozói szerepkörök esetén [adjon hozzá kódot a kérelmek nyomon követéséhez.](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

1. Konfigurálja az Azure Diagnostics bővítményt a Profiler engedélyezéséhez:

    a. Keresse meg az [Azure Diagnostics diagnostics.wadcfgx](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* fájlt az alkalmazásszerepkörhöz, ahogy az itt látható:  

      ![A diagnosztikai konfigurációs fájl helye](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Ha nem találja a fájlt, olvassa [el az Azure Cloud Services és a virtuális gépek diagnosztika beállítása című témakört.](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

    b. Adja hozzá `SinksConfig` a következő szakaszt a következő `WadCfg`gyermekelemként:  

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
    > Ha a *diagnostics.wadcfgx* fájl egy másik ApplicationInsights típusú fogadót is tartalmaz, az alábbi instrumentation kulcsok mindháromnak egyeznie kell:  
    > * Az alkalmazás által használt kulcs. 
    > * Az ApplicationInsights-fogadó által használt kulcs. 
    > * Az ApplicationInsightsProfiler fogadóáltal használt kulcs. 
    >
    > A szervizelési csoportban megtalálhatja a `ApplicationInsights` fogadó által használt tényleges instrumentation kulcsértéket. *\*. . cscfg* fájlokat. 
    > A Visual Studio 15.5 Azure SDK-kiadása után csak az alkalmazás és az ApplicationInsightsProfiler-fogadó által használt instrumentation kulcsoknak kell egyeznie.

1. Telepítse a szolgáltatást az új diagnosztikai konfigurációval, és az Application Insights Profiler úgy van konfigurálva, hogy futtassa a szolgáltatáson.
 
## <a name="next-steps"></a>További lépések

* Hozzon létre forgalmat az alkalmazásba (például indítson el egy [rendelkezésre állási tesztet).](monitor-web-app-availability.md) Ezután várjon 10–15 percet, amíg a nyomkövetések elindulnak az Application Insights-példányba.
* Tekintse meg [a Profiler-nyomkövetéseket](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
* A Profilozóval kapcsolatos problémák elhárításához olvassa el a [Profilozó hibaelhárítás című témakört.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)
