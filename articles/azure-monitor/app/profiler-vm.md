---
title: Az Application Insights Profiler-beli virtuális gépen futó webes alkalmazásprofilok |} A Microsoft Docs
description: Profil webalkalmazások az Application Insights Profiler-beli virtuális gépen.
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
ms.openlocfilehash: 764088e7f463f0c249f176514d485944d9c9d76e
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/14/2019
ms.locfileid: "54264631"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Egy Azure virtuális gép vagy virtuálisgép-méretezési csoportot az Application Insights Profiler futó webalkalmazások profil
Ezek a szolgáltatások az Application Insights profiler is telepítheti:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Felhőszolgáltatások](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Profiler egy virtuális gép üzembe helyezése vagy a méretezési csoport
Ezen a lapon végigvezeti Önt a lépéseket szükség az Application Insights profiler az Azure virtuális Gépen vagy Azure virtuálisgép-méretezési csoport beolvasásához beállítása. Application Insights Profiler telepítve van a Windows Azure Diagnostics bővítményt a virtuális gépek. A bővítmény kell úgy kell beállítani, futtassa a profilkészítőt, és az App Insights SDK-t kell elkészíteni az alkalmazásba.

1. Adja hozzá az application Insights SDK-t, a [ASP.Net-alkalmazás](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) vagy rendszeres [.NET-alkalmazás.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Meg kell kell kérelem telemetria küldése az Application Insights lásd profilra a kérelmek.
1. Telepítse a Windows Azure Diagnostics bővítményt a virtuális Gépen. Teljes Resource Manager sablon példákért lásd:  
    * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Virtuálisgép-méretezési csoportot](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    A kulcs része a ApplicationInsightsProfilerSink a WadCfg a. Ebben a szakaszban kell tudniuk WAD adatokat küldeni a Rendszerállapotkulcsot a profiler engedélyezéséhez adja hozzá egy másik fogadó.
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. Helyezze üzembe a módosított környezetben üzemelő példány definíciója.  

   A alkalmazni a módosításokat, általában magában foglalja egy teljes körű sablonalapú telepítés vagy a PowerShell-parancsmagok vagy a Visual Studio közzététel-alapú felhőszolgáltatás.  

   A következő powershell-parancsok egy alternatív módszer meglévő virtuális gépek csak az Azure Diagnostics bővítmény érintő. Egyszerűen adja hozzá a Config a Get-AzureRmVMDiagnosticsExtension parancs által visszaadott a fentieknek megfelelően a ProfilerSink. Ezután adja meg a frissített konfiguráció a Set-AzureRmVMDiagnosticsExtension parancsot.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Ha az importálni kívánt alkalmazást futtató [IIS](https://www.microsoft.com/web/downloads/platform.aspx), engedélyezze a `IIS Http Tracing` Windows-szolgáltatás.

   a. Állítsa be a környezeti távoli elérését, és használja a [hozzáadása Windows-szolgáltatások]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ablakban, vagy futtassa a következő parancsot a PowerShellben (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha létrehozó távelérési probléma, akkor használhatja [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , futtassa a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Az alkalmazás üzembe helyezéséhez.

## <a name="can-profiler-run-on-on-premises-servers"></a>A profiler futhatnak a helyszíni kiszolgálók?
Nem tervezzük a helyszíni kiszolgálók általi támogatása Application Insights Profiler van.

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
- Lásd: [Profiler nyomkövetések](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- Segítség a profiler problémák elhárítása a [hibaelhárítási Profiler](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json).
