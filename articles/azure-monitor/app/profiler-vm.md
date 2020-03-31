---
title: Webalkalmazások profilprofil-szolgáltatása egy Azure virtuális gépen – Application Insights Profiler
description: Webalkalmazások profilegy Azure-virtuális gép az Application Insights Profiler használatával.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 11/08/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 7c5dfe6ed08df01f78346c76fd5a35e7d64ab520
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671579"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Azure-beli virtuális gépen vagy az Application Insights Profiler használatával beállított virtuálisgép-méretezési csoporton futó profilalapú webalkalmazások

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Application Insights Profiler-t ezeken a szolgáltatásokon is telepítheti:
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>A Profiler központi telepítése virtuális gépen vagy virtuálisgép-méretezési csoporton
Ez a cikk bemutatja, hogyan szerezheti be az Application Insights Profiler fut az Azure virtuális gépen (VM) vagy az Azure virtuálisgép-méretezési csoportban. Profiler telepítve van az Azure Diagnostics bővítmény virtuális gépekhez. Konfigurálja a bővítményt a Profiler futtatásához, és építse be az Application Insights SDK-t az alkalmazásba.

1. Adja hozzá az Application Insights SDK-t [a ASP.NET alkalmazáshoz.](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)

   A kérelmek profilok megtekintéséhez el kell küldenie a kérelem telemetriai adatokat application insights.

1. Telepítse az Azure Diagnostics bővítményt a virtuális gépre. A teljes Erőforrás-kezelő sablonpéldák:  
   * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
   * [Virtuálisgép-méretezési csoport](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
     A legfontosabb rész az ApplicationInsightsProfilerSink a WadCfg. Ha azt szeretné, hogy az Azure Diagnostics engedélyezze a Profiler számára az adatok iKey-re való küldését, adjon hozzá egy másik fogadót ehhez a szakaszhoz.
    
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

1. Telepítse a módosított környezet központi telepítési definícióját.  

   A módosítások alkalmazása általában magában foglalja a teljes sablon központi telepítése vagy a felhőszolgáltatás-alapú közzététel a PowerShell-parancsmagok vagy a Visual Studio.  

   A következő PowerShell-parancsok egy alternatív megközelítés a meglévő virtuális gépek, amelyek csak az Azure Diagnostics bővítményt érinti. Adja hozzá a korábban említett ProfilerSink-t a Get-AzVMDiagnosticsExtension parancs által visszaadott konfigurációhoz. Ezután adja át a frissített konfigurációt a Set-AzVMDiagnosticsExtension parancsnak.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Ha a tervezett alkalmazás az [IIS szolgáltatáson](https://www.microsoft.com/web/downloads/platform.aspx)keresztül fut, engedélyezze a `IIS Http Tracing` Windows szolgáltatást.

   a. Hozzon létre távoli hozzáférést a környezethez, majd használja a [Windows szolgáltatások hozzáadása]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ablakot. Vagy futtassa a következő parancsot a PowerShellben (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha a távelérés létrehozása problémát jelent, az [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) segítségével futtathatja a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Telepítse az alkalmazást.

## <a name="set-profiler-sink-using-azure-resource-explorer"></a>A Profiler Sink beállítása az Azure Resource Explorer használatával
Még nem tudjuk beállítani az Application Insights Profiler a portálról. A powershell használata helyett a fent leírt, az Azure Resource Explorer segítségével beállíthatja a fogadó. De vegye figyelembe, ha újra telepíti a virtuális gép, a fogadó elvész. A beállítás megőrzése érdekében frissítenie kell a virtuális gép üzembe helyezésekor használt konfigurációt.

1. Ellenőrizze, hogy a Windows Azure Diagnosztika bővítmény telepítve van-e a virtuális géphez telepített bővítmények megtekintésével.  

    ![Annak ellenőrzése, hogy a WAD-bővítmény telepítve van-e][wadextension]

2. Keresse meg a virtuális gép diagnosztikai bővítményét. Nyissa [https://resources.azure.com](https://resources.azure.com)meg a. Bővítse ki az erőforráscsoportot, a Microsoft.Compute virtuálisgépeket, a virtuális gép nevét és a bővítményeket.  

    ![Navigálás a WAD config szolgáltatásra az Azure Resource Explorerben][azureresourceexplorer]

3. Adja hozzá az Application Insights Profiler-fogadót a SinksConfig csomóponthoz a WadCfg alatt. Ha még nem rendelkezik sinksconfig szakasz, előfordulhat, hogy hozzá kell adnia egyet. Ügyeljen arra, hogy adja meg a megfelelő Application Insights iKey a beállításokban. A felfedezők módot a jobb felső sarokban olvasásra/írásra kell váltania, és meg kell nyomnia a kék "Szerkesztés" gombot.

    ![Application Insights-profilozó fogadó hozzáadása][resourceexplorersinksconfig]

4. Ha befejezte a konfiguráció szerkesztését, nyomja meg a "Put" gombot. Ha az elhelyező sikeres, zöld pipa jelenik meg a képernyő közepén.

    ![Küldési kérelem a módosítások alkalmazásához][resourceexplorerput]






## <a name="can-profiler-run-on-on-premises-servers"></a>A Profiler futtatható a helyszíni kiszolgálókon?
Nem tervezzük az Application Insights Profiler helyszíni kiszolgálókhoz való támogatását.

## <a name="next-steps"></a>További lépések

- Hozzon létre forgalmat az alkalmazásba (például indítson el egy [rendelkezésre állási tesztet).](monitor-web-app-availability.md) Ezután várjon 10–15 percet, amíg a nyomkövetések elindulnak az Application Insights-példányba.
- Tekintse meg [a Profiler-nyomkövetéseket](profiler-overview.md?toc=/azure/azure-monitor/toc.json) az Azure Portalon.
- A Profilozóval kapcsolatos problémák elhárításával kapcsolatos segítségért olvassa el a [Profilozó hibaelhárításcímű témakört.](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

[azureresourceexplorer]: ./media/profiler-vm/azure-resource-explorer.png
[resourceexplorerput]: ./media/profiler-vm/resource-explorer-put.png
[resourceexplorersinksconfig]: ./media/profiler-vm/resource-explorer-sinks-config.png
[wadextension]: ./media/profiler-vm/wad-extension.png
