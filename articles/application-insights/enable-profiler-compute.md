---
title: "Application Insights Profilkészítő engedélyezése az alkalmazások Azure számítás |} Microsoft Docs"
description: "Megtudhatja, hogyan állíthat be az Application Insights Profilkészítő Azure számítási alkalmazás."
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
ms.openlocfilehash: 66ea24cfe9dd03ed62c06daa76ee043886ad7bcc
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Azure virtuális gépek, a Service Fabric Application Insights Profilkészítő engedélyezése és a felhőalapú szolgáltatások

Ez a cikk bemutatja, hogyan Azure Application Insights Profilkészítő engedélyezése az Azure számítási erőforrás által üzemeltetett ASP.NET-alkalmazások. 

Ebben a cikkben szereplő példák magukban foglalják az Azure virtuális gépek, a virtuálisgép-méretezési csoportok, az Azure Service Fabric és a Azure Cloud Services támogatását. A példák alapulnak, amely támogatja a sablonok a [Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) üzembe helyezési modellben.  


## <a name="overview"></a>Áttekintés

A következő kép bemutatja, hogyan az Application Insights Profilkészítő működik együtt a Azure-erőforrások. A kép Azure virtuális gép használ példaként.

  ![Áttekintés](./media/enable-profiler-compute/overview.png)

A Profilkészítő engedélyezni, módosítania kell a konfigurációt a három hely:

* Az Application Insights példány ablaktáblán az Azure portálon.
* Az alkalmazás forráskódjához (például egy ASP.NET webalkalmazás).
* A környezet központi telepítési definition forráskód (például egy virtuális gép központi telepítési sablon .JSON kiterjesztésű fájlt).


## <a name="set-up-the-application-insights-instance"></a>Az Application Insights-példány beállítása

Az Azure-portálon hozzon létre, vagy nyissa meg a használni kívánt Application Insights-példányra. Vegye figyelembe a Példánykulcs instrumentation. A további konfigurációs lépések a rendszerállapot-kulcsot használ.

  ![A kulcs instrumentation helye](./media/enable-profiler-compute/CopyAIKey.png)

Ez a példány ugyanaz, mint az alkalmazás kell lennie. Telemetriai adatokat küldjön az egyes kérelmek van konfigurálva.
Profilkészítő eredmények is érhetők el ehhez a példányhoz.  

Az Azure portálon, hajtsa végre a ismertetett lépéseit [engedélyezése a Profilkészítő](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) a profilkészítőt a az Application Insights-példány beállításának befejezéséhez. Nem kell a cikkben például webes alkalmazást. Csak győződjön meg arról, hogy a Profilkészítő engedélyezve van-e a portálon.


## <a name="set-up-the-application-source-code"></a>Az alkalmazás forráskódjához beállítása

Állítsa be az alkalmazás számára telemetrikus adatokat küldeni az Application Insights példányt minden egyes `Request` műveletet:  

1. Adja hozzá a [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) értékeként adja meg a projektet. Győződjön meg arról, hogy a NuGet csomag verziók a következők:  
  - Az ASP.NET-alkalmazások: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 vagy újabb.
  - Az ASP.NET Core alkalmazások: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 vagy újabb.
  - Az egyéb .NET és a .NET Core alkalmazások (például a Service Fabric állapotmentes szolgáltatások vagy Felhőszolgáltatások feldolgozói szerepkörök): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) vagy [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 vagy újabb.  

2. Ha az alkalmazás *nem* egy ASP.NET- vagy ASP.NET Core alkalmazás (például, ha a Felhőszolgáltatások feldolgozói szerepkör vagy a Service Fabric állapotmentes API-k), a következő további instrumentation telepítő szükség:  

  1. Korai szakaszában az alkalmazás élettartamának adja hozzá a következő kódot:  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  A globális instrumentation kulcs konfigurálásával kapcsolatban további információkért lásd: [használja a Service Fabric az Application insights szolgáltatással](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. A kód állíthatnak be, a hozzáadni kívánt minden olyan egy `StartOperation<RequestTelemetry>` **USING** utasítás köré, az alábbi példában látható módon:

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

A környezet, amelyben a Profilkészítő és az alkalmazás végrehajtási lehet egy virtuális gép, virtuálisgép-méretezési csoport, a Service Fabric-fürt vagy a Cloud Services példánya.  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>Virtuális gépek, a virtuálisgép-méretezési csoportok vagy a Service Fabric

Teljes példák:  
  * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtuálisgép-méretezési csoport](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric-fürt](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. Annak érdekében, hogy [.NET-keretrendszer 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb használatban van, győződjön meg arról, hogy a telepített operációs rendszer elegendő `Windows Server 2012 R2` vagy újabb.

2. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) bővítményt a központi telepítési sablont a fájlt, és adja meg a következő `SinksConfig` szakasz az alárendelt elem `WadCfg`. Cserélje le a `ApplicationInsightsProfiler` saját Application Insights instrumentation kulccsal tulajdonság értéke:  
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

  További információ a diagnosztika bővítmény felvétele a központi telepítési sablont: [használata figyelési és diagnosztika a Windows virtuális gép és az Azure Resource Manager-sablonok](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


### <a name="cloud-services"></a>Cloud Services

1. Annak érdekében, hogy [.NET-keretrendszer 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb használja, az elegendő győződjön meg arról is, hogy ServiceConfiguration.\*. szolgáltatáskonfigurációs séma fájlok egy `osFamily` értékének **"5"** vagy újabb.

2. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx fájlt az alkalmazás-szerepkör:  
  ![A diagnosztika konfigurációs fájl helye](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  A fájl nem található, ha engedélyezi a diagnosztika bővítményt a Felhőszolgáltatás-projekt, lásd: [diagnosztika beállítása az Azure Cloud Services és a virtuális gépek](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

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
> Ha a `diagnostics.wadcfgx` fájl is tartalmaz egy másik fogadó típusú `ApplicationInsights`, ezek a kulcsok instrumentation három egyeznie kell:  
>  * Az alkalmazás által használt instrumentation kulcsot.  
>  * A instrumentation által használt kulcsot a `ApplicationInsights` fogadó.  
>  * A instrumentation által használt kulcsot a `ApplicationInsightsProfiler` fogadó.  
>
> A tényleges instrumentation kulcsérték által használt található a `ApplicationInsights` gyűjtése a ServiceConfiguration.\*. szolgáltatáskonfigurációs séma fájlokat.  
> A Visual Studio 15.5 Azure SDK megjelenése után az alkalmazás által használt csak a instrumentation kulcsok és `ApplicationInsightsProfiler` fogadó kell egymással.


## <a name="environment-deployment-and-runtime-configurations"></a>Környezet központi telepítési és futásidejű konfigurációk

1. Telepítse a módosított környezet telepítési definícióval.  

  A módosítások alkalmazásához általában egy teljes sablon-üzembehelyezés vagy a cloud services közzététele PowerShell-parancsmagokkal vagy a Visual Studio van szó.  

  A következő egy meglévő virtuális gépek egy másik módjáról, amely csak az Azure Diagnostics kiegészítő koppint:  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. Ha a kívánt alkalmazás fut [IIS](https://www.microsoft.com/web/platform/server.aspx), engedélyezze a `IIS Http Tracing` Windows-szolgáltatás:  
  
  1. A környezet a távoli hozzáférést létrehozásához, és használja a [Windows-szolgáltatások hozzáadása]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) ablakban, vagy futtassa a következő parancsot a PowerShell (rendszergazdaként):  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. Ha létrehozó távelérési probléma, akkor használhatja [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) a következő parancsot:  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>A Profilkészítő a helyszíni kiszolgálók engedélyezése

A Profilkészítő egy helyszíni kiszolgálón engedélyezése is nevezik önálló (nem kötődik azonban Azure Diagnostics bővítmény módosításokat) módban futó Application Insights Profiler. 

A Profilkészítő hivatalosan támogatása a helyszíni kiszolgálók terv van. Ha érdekli a jelen forgatókönyvben kísérletezés, akkor [támogatási kód letöltése](https://github.com/ramach-msft/AIProfiler-Standalone). Folyamatban van, *nem* megőrzéséhez a kódot, vagy válaszol a problémák és a kód kapcsolódó funkciókérések felelős.

## <a name="next-steps"></a>Következő lépések

- Az alkalmazás felé irányuló forgalom előállításához (például elindíthatja egy [elérhetőségi teszt](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability)). Ezt követően várjon 10 – 15 percet nyomkövetések az Application Insights-példány küldendő elindításához.
- Lásd: [szolgáltatásprofil-elemzői adat](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler) az Azure portálon.
- Kérjen segítséget a Profilkészítő hibaelhárításával [hibaelhárítási Profilkészítő](app-insights-profiler.md#troubleshooting).
- További információk a Profilkészítő [Application Insights Profilkészítő](app-insights-profiler.md).
