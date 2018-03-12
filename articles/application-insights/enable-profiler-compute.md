---
title: "Application Insights Profilkészítő engedélyezése az alkalmazások az Azure Felhőszolgáltatások erőforrások |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthat be az Application Insights Profilkészítő Azure Felhőszolgáltatások futó alkalmazást."
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: a24695f7bbb5fb0546e27c934319a60a3418b9e1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Application Insights Profilkészítő engedélyezése az Azure virtuális gépek, a Service Fabric és az Azure Cloud Services csomag

Ez a cikk bemutatja, hogyan Azure Application Insights Profilkészítő engedélyezése az Azure Felhőszolgáltatások erőforrás által üzemeltetett ASP.NET-alkalmazások.

Ebben a cikkben szereplő példák magukban foglalják az Azure virtuális gépek, a virtuálisgép-méretezési csoportok, az Azure Service Fabric és a Azure Cloud Services támogatását. A példák alapulnak, amely támogatja a sablonok a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) üzembe helyezési modellben.  


## <a name="overview"></a>Áttekintés

A következő kép bemutatja, hogyan Application Insights Profilkészítő működik együtt tárolt alkalmazások Azure Felhőszolgáltatások erőforrástól függ. Azure Cloud Services erőforrások közé tartoznak a virtuális gépek, a méretezési csoportok, a felhőszolgáltatások és a Service Fabric-fürtök. A kép Azure virtuális gép használ példaként.  

  ![Application Insights Profilkészítő működése Azure Felhőszolgáltatások erőforrások bemutató ábra](./media/enable-profiler-compute/overview.png)

Engedélyezni Profilkészítő, módosítania kell a konfigurációt a három hely:

* Az Application Insights példány ablaktáblán az Azure portálon.
* Az alkalmazás forráskódjához (például egy ASP.NET webalkalmazás).
* A környezet központi telepítési definition forráskód (például az Azure Resource Manager sablon .JSON kiterjesztésű fájl).


## <a name="set-up-the-application-insights-instance"></a>Az Application Insights-példány beállítása

1. [Hozzon létre egy új Application Insights-erőforrást](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource), vagy válasszon egy meglévőt. 

2. Nyissa meg az Application Insights-erőforrást, és másolja a instrumentation kulcsot.

   ![A rendszerállapot-kulcs helyét](./media/enable-profiler-compute/CopyAIKey.png)

3. A Profilkészítő az Application Insights-példány beállításának befejezéséhez, hajtsa végre az eljárást, ismertetett [engedélyezni Profilkészítő. Nem kell a webes alkalmazást, mert a lépéseket csak az adott app services-erőforráshoz. Győződjön meg arról, hogy Profilkészítő engedélyezve van a **konfigurálása Profilkészítő** ablaktáblán.


## <a name="set-up-the-application-source-code"></a>Az alkalmazás forráskódjához beállítása

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>ASP.NET webes alkalmazásokhoz, Azure Cloud Services webes szerepkörök vagy a Service Fabric ASP.NET webes kezelőfelületből
Állítsa be az alkalmazás számára telemetrikus adatokat küldeni az Application Insights példányt minden egyes `Request` műveletet.  

Adja hozzá a [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) értékeként adja meg a projektet. Győződjön meg arról, hogy a NuGet csomag verziók a következők:  
  - Az ASP.NET-alkalmazások: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 vagy újabb.
  - Az ASP.NET Core alkalmazások: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 vagy újabb.
  - Az egyéb .NET és a .NET Core alkalmazások (például a Service Fabric állapotmentes szolgáltatások vagy Felhőszolgáltatások feldolgozói szerepkörök): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) vagy [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 vagy újabb.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Azure Cloud Services feldolgozói szerepkörök vagy a Service Fabric állapotmentes háttér
Az előző lépést elvégezte, ha az alkalmazás mellett *nem* egy ASP.NET- vagy ASP.NET Core alkalmazás (például, ha az Azure Felhőszolgáltatások feldolgozói szerepkör vagy a Service Fabric állapotmentes API-k), tegye a következőket:  

  1. Az alkalmazás élettartamának korai szakaszában adja hozzá a következő kódot:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      A globális instrumentation kulcs konfigurálásával kapcsolatban további információkért lásd: [használja a Service Fabric az Application insights szolgáltatással](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. A kód állíthatnak be, a hozzáadni kívánt minden olyan egy `StartOperation<RequestTelemetry>` **USING** utasítás köré, a következő példában látható módon:

        ```csharp
        using Microsoft.ApplicationInsights;
        using Microsoft.ApplicationInsights.DataContracts;
        ...
        var client = new TelemetryClient();
        ...
        using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
        {
          // ... Code I want to profile.
        }
        ```

        Hívása `StartOperation<RequestTelemetry>` belül egy másik `StartOperation<RequestTelemetry>` hatókör nem támogatott. Használhat `StartOperation<DependencyTelemetry>` a beágyazott a hatókör helyette. Példa:  
        
        ```csharp
        using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
        {
        try
        {
          ProductDetail details = new ProductDetail() { Id = productId };
          getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();
        
          // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
          using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
          {
              double price = await _priceDataBase.GetAsync(productId);
              if (IsTooCheap(price))
              {
                  throw new PriceTooLowException(productId);
              }
              details.Price = price;
          }
        
          // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
          using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
          {
              details.Reviews = await _reviewDataBase.GetAsync(productId);
          }
        
          getDetailsOperation.Telemetry.Success = true;
          return details;
        }
        catch(Exception ex)
        {
          getDetailsOperation.Telemetry.Success = false;
        
          // This exception gets linked to the 'GetProductDetails' request telemetry.
          client.TrackException(ex);
          throw;
        }
        }
        ```

## <a name="set-up-the-environment-deployment-definition"></a>A környezet telepítési definícióval beállítása

A Profilkészítő és az alkalmazás-végrehajtási lehet, a virtuális gép, virtuálisgép-méretezési csoport, a Service Fabric-fürt, és a felhőalapú szolgáltatások példány környezete.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Virtuális gépek, a méretezési készlet vagy a Service Fabric

Teljes példákért lásd:  
  * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtuálisgép-méretezési csoport](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric-fürt](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Állítsa be a környezetet, tegye a következőket:
1. Győződjön meg arról, hogy használja a [.NET-keretrendszer 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb, győződjön meg arról, hogy a telepített operációs rendszer elegendő `Windows Server 2012 R2` vagy újabb.

2. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) bővítményt a központi telepítési sablont a fájlt, és adja meg a következő `SinksConfig` szakasz az alárendelt elem `WadCfg`. Cserélje le a `ApplicationInsightsProfiler` saját Application Insights instrumentation kulccsal tulajdonság értéke:  

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

      További információ a diagnosztika bővítmény felvétele a központi telepítési sablont: [használata figyelési és diagnosztika a Windows virtuális gép és az Azure Resource Manager-sablonok](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Virtuális gépek a fenti lépéseket a json-alapú helyett, hogy az Azure-portálon lépjen **virtuális gépek** > **diagnosztikai beállítások**  >  ** Fogadók esetében** > Set diagnosztikai adatok küldése az Application Insights- **engedélyezve** , és válassza az Application Insights-fiókkal vagy egy adott ikey.

### <a name="azure-cloud-services"></a>Azure Cloud Services

1. Győződjön meg arról, hogy használja a [.NET-keretrendszer 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb, annak ellenőrzésére, hogy elegendő a *ServiceConfiguration.\*. szolgáltatáskonfigurációs séma* fájlok egy `osFamily` érték az "5" vagy újabb.

2. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* fájlt az alkalmazás-szerepkör, ahogy az itt látható:  

   ![A diagnosztika konfigurációs fájl helye](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   A fájl nem található, ha engedélyezi a diagnosztika bővítményt Azure Felhőszolgáltatások célverzióját, lásd: [diagnosztika beállítása az Azure Cloud Services és a virtuális gépek](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Adja hozzá a következő `SinksConfig` szakasz az alárendelt elem `WadCfg`:  

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
> Ha a *diagnostics.wadcfgx* fájl is tartalmaz egy másik fogadó típusú `ApplicationInsights`, a következő instrumentation kulcsok három egyezniük kell:  
>  * Az alkalmazás által használt kulcs.  
>  * A által használt kulcsot a `ApplicationInsights` fogadó.  
>  * A által használt kulcsot a `ApplicationInsightsProfiler` fogadó.  
>
> A tényleges instrumentation kulcsérték által használt megtalálhatja a `ApplicationInsights` gyűjtése a *ServiceConfiguration.\*. szolgáltatáskonfigurációs séma* fájlokat.  
> A Visual Studio 15.5 Azure SDK-verzió, csak az alkalmazás által használt instrumentation kulcsok után és a `ApplicationInsightsProfiler` fogadó kell egymással.


## <a name="configure-environment-deployment-and-runtime"></a>Környezet központi telepítési és a futtatókörnyezetek konfigurálása

1. Telepítse a módosított környezet telepítési definícióval.  

   A módosítások alkalmazásához általában magában foglalja a teljes sablon üzembe helyezése vagy CA felhő alapú szolgáltatások közzététele PowerShell-parancsmagokkal vagy a Visual Studio használatával.  

   A következő egy meglévő virtuális gépek egy másik módjáról, amely csak az Azure Diagnostics bővítmény koppint:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Ha a kívánt alkalmazás fut [IIS](https://www.microsoft.com/web/platform/server.aspx), engedélyezze a `IIS Http Tracing` Windows-szolgáltatás a következő tevékenységek végrehajtásával:  

   a. A környezet a távoli hozzáférést létrehozásához, és használja a [hozzáadása Windows-szolgáltatások]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ablakban, vagy futtassa a következő parancsot a PowerShell (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha létrehozó távelérési probléma, akkor használhatja [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Engedélyezze a Profilkészítő a helyszíni kiszolgálók

Profilkészítő engedélyezése egy helyszíni kiszolgálón is nevezik önálló módban futó Application Insights Profiler. Nem kötődik azonban Azure Diagnostics bővítmény módosításokat.

Hivatalos támogatást a helyszíni kiszolgálók Profilkészítő terv van. Ha érdekli a jelen forgatókönyvben kísérletezés, akkor [támogatási kód letöltése](https://github.com/ramach-msft/AIProfiler-Standalone). Folyamatban van, *nem* megőrzéséhez a kódot, vagy válaszol a problémák és a kód kapcsolódó funkciókérések felelős.

## <a name="next-steps"></a>További lépések

- Az alkalmazás felé irányuló forgalom előállításához (például elindíthatja egy [elérhetőségi teszt](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Ezt követően várjon 10 – 15 percet nyomkövetések az Application Insights-példány küldendő elindításához.
- Lásd: [szolgáltatásprofil-elemzői adat](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) az Azure portálon.
- Kérjen segítséget a Profilkészítő hibaelhárításával [hibaelhárítási Profilkészítő](app-insights-profiler.md#troubleshooting).
- További információk a Profilkészítő [Application Insights Profilkészítő](app-insights-profiler.md).
