---
title: Application Insights Profiler engedélyezése az Azure Cloud Services-erőforrások tárolt alkalmazások |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be Application Insights Profiler az Azure Cloud Servicesben futó alkalmazások.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2017
ms.reviewer: ramach
ms.author: mbullwin
ms.openlocfilehash: 9eb99ecea8efbbce322e61ac281cd534a112728b
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37950670"
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-azure-cloud-services"></a>Application Insights Profiler engedélyezése az Azure virtuális gépek, Service Fabric és az Azure Cloud Services

Ez a cikk bemutatja az Azure Cloud Services erőforrás által üzemeltetett ASP.NET-alkalmazások az Azure Application Insights Profiler engedélyezése.

Ebben a cikkben szereplő példák az Azure Virtual Machines, a virtual machine scale sets, az Azure Service Fabric és Azure Cloud Services támogatását. A példák támaszkodik, amely támogatja a sablonok a [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) üzemi modellt.  


## <a name="overview"></a>Áttekintés

Az alábbi képen látható az Application Insights Profiler működése tárolt alkalmazások az Azure Cloud Services-erőforrások. Az Azure Cloud Services-erőforrások közé tartoznak a Virtual Machines, a méretezési csoportok, a cloud services és a Service Fabric-fürtök. A kép egy Azure virtuális gép használja példaként.  

  ![Application Insights Profiler az Azure Cloud Services-erőforrások működését bemutató ábra.](./media/enable-profiler-compute/overview.png)

Teljes mértékű engedélyezéséhez Profiler, módosítania kell a három hely konfigurációt:

* Az Application Insights példány panel az Azure Portalon.
* Az alkalmazás forráskódja (például egy ASP.NET-alkalmazás).
* A környezet üzembe helyezési definíció forráskód (például egy Azure Resource Manager-sablon a .JSON kiterjesztésű fájlt).


## <a name="set-up-the-application-insights-instance"></a>Az Application Insights-példány beállítása

1. [Hozzon létre egy új Application Insights-erőforrást](https://docs.microsoft.com/azure/application-insights/app-insights-create-new-resource), vagy válasszon ki egy meglévőt. 

2. Nyissa meg az Application Insights-erőforrást, és másolja a kialakítási kulcsot.

   ![A kialakítási kulcs helyét](./media/enable-profiler-compute/CopyAIKey.png)

3. A Profiler az Application Insights-példány beállításának befejezéséhez, hajtsa végre az eljárást, itt olvashat [Profiler engedélyezése. Nem kell összekapcsolni a webalkalmazásokat, mivel az app services-erőforrás a lépéseket. Győződjön meg arról, hogy a Profiler engedélyezve van az a **Profiler konfigurálása** ablaktáblán.


## <a name="set-up-the-application-source-code"></a>Az alkalmazás forráskódja beállítása

### <a name="aspnet-web-applications-azure-cloud-services-web-roles-or-the-service-fabric-aspnet-web-front-end"></a>ASP.NET-webalkalmazások, Azure Cloud Services webes szerepkörök vagy a Service Fabric ASP.NET webes kezelőfelület
Állítsa be az alkalmazás telemetriai adatokat küldeni egy Application Insights-példányt az egyes `Request` műveletet.  

Adja hozzá a [Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) a projekthez. Győződjön meg arról, hogy a NuGet-csomag verziók a következők:  
  - Az ASP.NET-alkalmazások: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0-át vagy újabb.
  - Az ASP.NET Core-alkalmazások: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 vagy újabb.
  - Az egyéb .NET és .NET Core alkalmazásokat (például egy Service Fabric állapotmentes szolgáltatás vagy a Cloud Services feldolgozói szerepkör): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) vagy [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0-át vagy újabb.  

### <a name="azure-cloud-services-worker-roles-or-the-service-fabric-stateless-back-end"></a>Az Azure Cloud Services feldolgozói szerepkörei, vagy a Service Fabric állapotmentes háttér
Az előző lépés végrehajtása, ha az alkalmazás mellett *nem* egy ASP.NET- vagy ASP.NET Core alkalmazás (például, ha az Azure Cloud Services feldolgozói szerepkör vagy a Service Fabric állapotmentes API-k), tegye a következőket:  

  1. Az alkalmazás élettartamának korai szakaszában adja meg a következő kódot:  

        ```csharp
        using Microsoft.ApplicationInsights.Extensibility;
        ...
        // Replace with your own Application Insights instrumentation key.
        TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
        ```
      A globális kialakítási kulcs konfigurálásával kapcsolatban további információkért lásd: [az Application Insights használata a Service Fabric](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md).  

  2. A bármely alakítsa ki, a hozzáadni kívánt kódrészleteket egy `StartOperation<RequestTelemetry>` **USING** utasítás körülötte, az alábbi példában látható módon:

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

        Hívó `StartOperation<RequestTelemetry>` belül egy másik `StartOperation<RequestTelemetry>` hatókör nem támogatott. Használhat `StartOperation<DependencyTelemetry>` beágyazott hatókör helyette. Példa:  
        
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

## <a name="set-up-the-environment-deployment-definition"></a>Állítsa be a környezet üzemelő példány definíciója

A környezet, amelyben Profiler és az alkalmazás végrehajtási lehet egy virtuális gép, virtuálisgép-méretezési csoportot, Service Fabric-fürtön, vagy egy felhőszolgáltatás-példányok.  

### <a name="virtual-machines-scale-sets-or-service-fabric"></a>Virtuális gépek, a méretezési csoportok vagy a Service Fabric

Teljes példákért lásd:  
  * [Virtuális gép](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtuálisgép-méretezési csoportot](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric-fürt](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Állítsa be a környezetet, tegye a következőket:
1. Győződjön meg arról, hogy használja a [.NET-keretrendszer 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) vagy újabb, elegendő ahhoz, hogy erősítse meg, hogy a telepített operációs rendszer `Windows Server 2012 R2` vagy újabb.

2. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) kiterjesztése a központi telepítési sablont a fájlt, és ezután adja hozzá a következő `SinksConfig` szegmensben, mint a gyermekelemet `WadCfg`. Cserélje le a `ApplicationInsightsProfiler` tulajdonság értékét a saját Application Insights-kialakítási kulcsot:  

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

      A diagnosztikai bővítményt a központi telepítési sablont történő hozzáadásával kapcsolatos további információkért lásd: [használható monitorozási és diagnosztikai egy Windows virtuális gép és az Azure Resource Manager-sablonokkal](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

> [!TIP]
> Virtuális gépek a fenti lépéseket a json-alapú alternatívát, hogy az Azure Portalon lépjen **virtuális gépek** > **diagnosztikai beállítások**  >   **Fogadók** > beállítása diagnosztikai adatok küldése az Application Insights **engedélyezve** és a egy Application Insights-fiókot vagy egy adott rendszerállapotkulcsot jelölje ki.

### <a name="azure-cloud-services"></a>Azure Cloud Services

1. Győződjön meg arról, hogy használja a [.NET-keretrendszer 4.6.1-es](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) , vagy később ellenőrizheti, hogy elegendő a *ServiceConfiguration.\*. cscfg* fájlok egy `osFamily` érték "5" vagy újabb verzió.

2. Keresse meg a [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) *diagnostics.wadcfgx* fájlt az alkalmazás-szerepkörökhöz az itt látható módon:  

   ![A diagnosztika konfigurációs fájl helye](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

   Ha a fájl nem található, további információk az Azure Cloud Services-projektben a diagnosztikai bővítmény engedélyezése: [diagnosztika beállítása az Azure Cloud Services és virtual machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

3. Adja hozzá a következő `SinksConfig` szegmensben, mint a gyermekelemet `WadCfg`:  

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
> Ha a *diagnostics.wadcfgx* fájlt is tartalmaz egy másik fogadó típusa `ApplicationInsights`, a következő kialakítási kulcs mindhárom egyeznie kell:  
>  * Az alkalmazás által használt kulcs.  
>  * A kulcs, amelyet a `ApplicationInsights` fogadó.  
>  * A kulcs, amelyet a `ApplicationInsightsProfiler` fogadó.  
>
> Az által használt tényleges kialakítási kulcs értéke annak a `ApplicationInsights` gyűjtése a *ServiceConfiguration.\*. cscfg* fájlokat.  
> A Visual Studio 15.5 Azure SDK-verzió, csak az alkalmazás által használt kialakítási kulcs után és a `ApplicationInsightsProfiler` fogadó kell egymással.


## <a name="configure-environment-deployment-and-runtime"></a>Környezet üzembe helyezés és a futtatókörnyezet konfigurálása

1. Helyezze üzembe a módosított környezetben üzemelő példány definíciója.  

   A módosítások alkalmazásához általában magában foglalja egy teljes körű sablonalapú telepítés vagy a felhő alapú szolgáltatások közzététele a PowerShell-parancsmagokkal vagy a Visual Studio használatával.  

   Az alábbiakban látható egy alternatív módszer meglévő virtuális gépek csak az Azure Diagnostics bővítmény érintő:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

2. Ha az importálni kívánt alkalmazást futtató [IIS](https://www.microsoft.com/web/downloads/platform.aspx), engedélyezze a `IIS Http Tracing` Windows-szolgáltatást az alábbiak szerint:  

   a. Állítsa be a környezeti távoli elérését, és használja a [hozzáadása Windows-szolgáltatások]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ablakban, vagy futtassa a következő parancsot a PowerShellben (rendszergazdaként):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Ha létrehozó távelérési probléma, akkor használhatja [Azure CLI-vel](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) , futtassa a következő parancsot:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-profiler-on-on-premises-servers"></a>Profiler engedélyezése a helyszíni kiszolgálók

Profiler engedélyezése egy helyszíni kiszolgálón is nevezik önálló módban futó Application Insights Profiler. Azure Diagnostics bővítmény módosításai nem kötődik.

Profiler támogatást nyújthasson a helyszíni kiszolgálók terv van. Ha érdekli kísérleteztek az ebben a forgatókönyvben, [támogatási kód letöltése](https://github.com/ramach-msft/AIProfiler-Standalone). Tudjuk *nem* fenntartása a kódot, vagy a problémákra és a funkciókérések a kód kapcsolódó válaszol felelős.

## <a name="next-steps"></a>További lépések

- Hozzon létre a forgalmat az alkalmazásához (például indítása egy [rendelkezésre állási teszt](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Várjon 10 – 15 percet nyomok kell küldeni az Application Insights-példány elindításához.
- Lásd: [Profiler nyomkövetések](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler) az Azure Portalon.
- Segítség a profiler problémák elhárítása a [hibaelhárítási Profiler](app-insights-profiler.md#troubleshooting).
- További információ a Profiler [Application Insights Profiler](app-insights-profiler.md).
