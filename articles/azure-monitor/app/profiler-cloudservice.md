---
title: Profil élő Azure-Cloud Services a Application Insightskal | Microsoft Docs
description: Az Azure Cloud Services Application Insights Profiler engedélyezése.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: eba250f3ca6a7af8480d24a99e6c3e8278009b82
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86499443"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil élő Azure Cloud Services a Application Insights

A Application Insights Profiler a következő szolgáltatásokon is üzembe helyezhető:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric alkalmazások](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler telepítve van a Azure Diagnostics bővítménnyel. Csak konfigurálnia kell Azure Diagnostics a Profiler telepítéséhez és profilok küldéséhez a Application Insights-erőforráshoz.

## <a name="enable-profiler-for-azure-cloud-services"></a>A Profiler engedélyezése az Azure Cloud Services
1. Győződjön meg arról, hogy a .net- [keretrendszer 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -es vagy újabb verziója van használatban. Ha a 4-es operációsrendszer-családot használja, [indítási feladattal](../../cloud-services/cloud-services-dotnet-install-dotnet.md)kell telepítenie a .NET-keretrendszer 4.6.1-es vagy újabb verzióját. Az operációsrendszer-család 5 alapértelmezés szerint a .NET-keretrendszer kompatibilis verzióját tartalmazza. 

1. [Application INSIGHTS SDK hozzáadása az Azure Cloud Serviceshoz](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **A Profilerben szereplő hiba, amely a Cloud Services WAD-ban található.** A Cloud Services WAD (1.12.2.0) legújabb verziója az App ininsights SDK legújabb verzióival működik. A Cloud Service-gazdagépek automatikusan frissítik a WAD-t, de nem azonnal. A frissítés kényszerítéséhez újra üzembe helyezheti a szolgáltatást, vagy újraindíthatja a csomópontot.

1. Kérelmek nyomon követése Application Insightsekkel:

    * A ASP.NET webes szerepkörök esetében Application Insights automatikusan nyomon követheti a kérelmeket.

    * Feldolgozói szerepköröknél [adjon hozzá kódot a kérelmek nyomon követéséhez](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Konfigurálja a Azure Diagnostics bővítményt a Profiler engedélyezéséhez:

    a. Keresse meg az alkalmazás szerepköréhez tartozó [Azure Diagnostics](../platform/diagnostics-extension-overview.md) *Diagnostics. wadcfgx* fájlt, ahogy az itt látható:  

      ![A diagnosztikai konfigurációs fájl helye](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Ha nem találja a fájlt, olvassa el a [diagnosztika beállítása az Azure-Cloud Services és Virtual Machines](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Adja hozzá a következő `SinksConfig` szakaszt a gyermek elemeként `WadCfg` :  

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
    > Ha a *Diagnostics. wadcfgx* fájl egy másik, ApplicationInsights típusú gyűjtőt is tartalmaz, a következő rendszerállapot-kulcsok mindhárom elemének meg kell egyeznie:  
    > * Az alkalmazás által használt kulcs. 
    > * A ApplicationInsights-fogadó által használt kulcs. 
    > * A ApplicationInsightsProfiler-fogadó által használt kulcs. 
    >
    > Megkeresheti a fogadó által a ServiceConfiguration használt tényleges kialakítási kulcs értékét `ApplicationInsights` *. \* cscfg* -fájlok. 
    > A Visual Studio 15,5 Azure SDK kiadása után csak az alkalmazás által használt rendszerállapot-kulcsok és a ApplicationInsightsProfiler kell egyezniük egymással.

1. Telepítse a szolgáltatást az új diagnosztikai konfigurációval, és Application Insights Profiler konfigurálva van a szolgáltatáson való futtatásra.
 
## <a name="next-steps"></a>Következő lépések

* Adatforgalom létrehozása az alkalmazáshoz (például egy [rendelkezésre állási teszt](monitor-web-app-availability.md)elindításához). Ezután várjon 10 – 15 percet, amíg a Nyomkövetések el nem jutnak a Application Insights példányba.
* Lásd: [Profiler-Nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) a Azure Portalban.
* A Profiler problémáinak elhárításához tekintse meg a [Profiler hibaelhárítását](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)ismertető témakört.
