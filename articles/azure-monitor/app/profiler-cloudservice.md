---
title: Profil élő Azure-Cloud Services a Application Insightskal | Microsoft Docs
description: Az Azure Cloud Services Application Insights Profiler engedélyezése.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 69a9ba316a1fda14a2e85d4d981321d4b2f9f289
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88930315"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Profil élő Azure Cloud Services a Application Insights

A Application Insights Profiler a következő szolgáltatásokon is üzembe helyezhető:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric alkalmazások](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler telepítve van a Azure Diagnostics bővítménnyel. Csak konfigurálnia kell Azure Diagnostics a Profiler telepítéséhez és profilok küldéséhez a Application Insights-erőforráshoz.

## <a name="enable-profiler-for-azure-cloud-services"></a>A Profiler engedélyezése az Azure Cloud Services
1. Győződjön meg arról, hogy a .net- [keretrendszer 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) -es vagy újabb verziója van használatban. Ha a 4-es operációsrendszer-családot használja, [indítási feladattal](../../cloud-services/cloud-services-dotnet-install-dotnet.md)kell telepítenie a .NET-keretrendszer 4.6.1-es vagy újabb verzióját. Az operációsrendszer-család 5 alapértelmezés szerint a .NET-keretrendszer kompatibilis verzióját tartalmazza. 

1. [Application INSIGHTS SDK hozzáadása az Azure Cloud Serviceshoz](./cloudservices.md?toc=%2fazure%2fazure-monitor%2ftoc.json).

    **A Profilerben szereplő hiba, amely a Cloud Services WAD-ban található.** A Cloud Services WAD (1.12.2.0) legújabb verziója az App ininsights SDK legújabb verzióival működik. A Cloud Service-gazdagépek automatikusan frissítik a WAD-t, de nem azonnal. A frissítés kényszerítéséhez újra üzembe helyezheti a szolgáltatást, vagy újraindíthatja a csomópontot.

1. Kérelmek nyomon követése Application Insightsekkel:

    * A ASP.NET webes szerepkörök esetében Application Insights automatikusan nyomon követheti a kérelmeket.

    * Feldolgozói szerepköröknél [adjon hozzá kódot a kérelmek nyomon követéséhez](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Konfigurálja a Azure Diagnostics bővítményt a Profiler engedélyezéséhez:

    a. Keresse meg az alkalmazás szerepköréhez tartozó [Azure Diagnostics](../platform/diagnostics-extension-overview.md) *Diagnostics. wadcfgx* fájlt, ahogy az itt látható:  

      ![A diagnosztikai konfigurációs fájl helye](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Ha nem találja a fájlt, olvassa el a [diagnosztika beállítása az Azure-Cloud Services és Virtual Machines](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?view=vs-2019).

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
    > Megkeresheti a fogadó által a ServiceConfiguration használt tényleges kialakítási kulcs értékét `ApplicationInsights`     *. \* cscfg* -fájlok. 
    > A Visual Studio 15,5 Azure SDK kiadása után csak az alkalmazás által használt rendszerállapot-kulcsok és a ApplicationInsightsProfiler kell egyezniük egymással.

1. Telepítse a szolgáltatást az új diagnosztikai konfigurációval, és Application Insights Profiler konfigurálva van a szolgáltatáson való futtatásra.
 
## <a name="next-steps"></a>További lépések

* Adatforgalom létrehozása az alkalmazáshoz (például egy [rendelkezésre állási teszt](monitor-web-app-availability.md)elindításához). Ezután várjon 10 – 15 percet, amíg a Nyomkövetések el nem jutnak a Application Insights példányba.
* Lásd: [Profiler-Nyomkövetések](profiler-overview.md?toc=/azure/azure-monitor/toc.json) a Azure Portalban.
* A Profiler problémáinak elhárításához tekintse meg a [Profiler hibaelhárítását](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)ismertető témakört.

