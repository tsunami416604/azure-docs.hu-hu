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
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 10cd05bd40262815e3b27c861982debc18e5b4f3
ms.sourcegitcommit: 0f54b9dbcf82346417ad69cbef266bc7804a5f0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50142424"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Egy Azure virtuális gép vagy virtuálisgép-méretezési csoportot az Application Insights Profiler futó webalkalmazások profil
Ezek a szolgáltatások az Application Insights profiler is telepítheti:
* [Azure Web Apps](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Felhőszolgáltatások](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Profiler egy virtuális gép üzembe helyezése vagy a méretezési csoport
Ezen a lapon végigvezeti Önt a lépéseket szükség az Application Insights profiler az Azure virtuális Gépen vagy Azure virtuálisgép-méretezési csoport beolvasásához beállítása. Application Insights Profiler telepítve van a Windows Azure Diagnostics bővítményt a virtuális gépek. Az App Insights SDK-t az alkalmazások profilok a virtuális gépen futó webalkalmazásokhoz készült, és futtassa a profilkészítőt a bővítmény konfigurálása kell.

1. Adja hozzá az application Insights SDK-t, a [ASP.Net-alkalmazás](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) vagy rendszeres [.NET-alkalmazás.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Meg kell kell kérelem telemetria küldése az Application Insights lásd profilra a kérelmek.
1. Telepítse a Windows Azure Diagnostics bővítményt a virtuális Gépen. Teljes Resource Manager sablon példákért lásd:  
    * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Virtuálisgép-méretezési csoportot](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. Helyezze üzembe a módosított környezetben üzemelő példány definíciója.  

   A módosítások alkalmazásához általában magában foglalja egy teljes körű sablonalapú telepítés vagy a felhő alapú szolgáltatás közzététele a PowerShell-parancsmagok vagy a Visual Studio.  

   A következő powershell-parancsok egy alternatív módszer meglévő virtuális gépek csak az Azure Diagnostics bővítmény érintő:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Ha az importálni kívánt alkalmazást futtató [IIS](https://www.microsoft.com/web/downloads/platform.aspx), engedélyezze a `IIS Http Tracing` Windows-szolgáltatást az alábbiak szerint:  

   a. Állítsa be a környezeti távoli elérését, és használja a [hozzáadása Windows-szolgáltatások]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ablakban, vagy futtassa a következő parancsot a PowerShellben (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha létrehozó távelérési probléma, akkor használhatja [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , futtassa a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Az alkalmazás üzembe helyezéséhez.

## <a name="enable-profiler-on-on-premises-servers"></a>Profiler engedélyezése a helyszíni kiszolgálók

Profiler engedélyezése egy helyszíni kiszolgálón is nevezik önálló módban futó Application Insights Profiler. Azure Diagnostics bővítmény módosításai nem kötődik.

Profiler támogatást nyújthasson a helyszíni kiszolgálók terv van. Ha az Önt érdeklő kísérleteztek az ebben a forgatókönyvben, [támogatási kód letöltése](https://github.com/ramach-msft/AIProfiler-Standalone). Tudjuk *nem* fenntartása a kódot, vagy a problémákra és a funkciókérések a kód kapcsolódó válaszol felelős.

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
- Lásd: [Profiler nyomkövetések](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- Segítség a profiler problémák elhárítása a [hibaelhárítási Profiler](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
