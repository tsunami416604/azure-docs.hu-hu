---
title: Klasszikus Felhőszolgáltatások-mérőszámok küldése az Azure Monitor metrika-adatbázisába
description: Ismerteti a folyamat ot az Azure klasszikus felhőszolgáltatások vendég operációs rendszer teljesítménymutatóinak küldésének folyamatát az Azure Monitor metrikatárolóba.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3b390ffa20cf3cf79b8fb6311ad05b2978bd5d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655794"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Vendég operációsrendszer-mérőszámok küldése az Azure Monitor metrikatárklasszikus felhőszolgáltatásaiba 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor Diagnosztikai bővítmény segítségével metrikákat és naplókat gyűjthet a vendég operációs rendszerből (Vendég operációs rendszer) egy virtuális gép, felhőszolgáltatás vagy service fabric-fürt részeként futó vendég operációs rendszerből.With the Azure Monitor [Diagnostics extension](diagnostics-extension-overview.md)you can collects and logs from the guest operating system (Guest OS) running as part of a virtual machine, cloud service, or Service Fabric cluster. A bővítmény telemetriai adatokat küldhet [számos különböző helyre.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Ez a cikk ismerteti a folyamat küldése vendég operációs rendszer teljesítmény metrikák az Azure klasszikus felhőszolgáltatások az Azure Monitor metrika áruház. Az 1.11-es diagnosztikai verzióval kezdve metrikákat írhat közvetlenül az Azure Monitor metrikatárolóba, ahol a standard platformmetrikák már összegyűjtöttek. 

Ezen a helyen való tárolásuk lehetővé teszi, hogy ugyanazokat a műveleteket érje el, mint a platform metrikáihoz. A műveletek közé tartozik a közel valós idejű riasztás, a diagramkészítés, az útválasztás, a REST API-ból való hozzáférés és egyebek.  A múltban a Diagnosztika bővítmény írt az Azure Storage, de nem az Azure Monitor adattárba.  

A jelen cikkben ismertetett folyamat csak az Azure Cloud Services teljesítményszámlálóinál működik. Más egyéni metrikák esetében nem működik. 

## <a name="prerequisites"></a>Előfeltételek

- [Az Azure-előfizetésben szolgáltatás-rendszergazdának vagy társrendszergazdának](../../cost-management-billing/manage/add-change-subscription-administrator.md) kell lennie. 

- Előfizetését regisztrálni kell a [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Az [Azure PowerShellt](/powershell/azure) vagy az [Azure Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview) telepítenie kell.

- A felhőszolgáltatásnak olyan régióban kell [lennie, amely támogatja az egyéni metrikákat.](metrics-custom-overview.md#supported-regions)

## <a name="provision-a-cloud-service-and-storage-account"></a>Felhőalapú szolgáltatás- és tárfiók kiépítése 

1. Hozzon létre és telepítsen egy klasszikus felhőszolgáltatást. A klasszikus Felhőszolgáltatások mintaalkalmazása és üzembe helyezése az Azure Cloud Services és a ASP.NET című [ismerkedés](../../cloud-services/cloud-services-dotnet-get-started.md)című webhelyen található. 

2. Használhatja a meglévő tárfiókot, vagy üzembe helyezhet egy új tárfiókot. A legjobb, ha a tárfiók ugyanabban a régióban van, mint a klasszikus felhőszolgáltatás, amely et létrehozott. Az Azure Portalon nyissa meg a **Storage-fiókok** erőforrás panel, és válassza **a kulcsok lehetőséget.** Vegye figyelembe a tárfiók nevét és a tárfiók kulcsát. Erre az információra a későbbi lépésekben lesz szüksége.

   ![Tárfiókkulcsok](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 

Hozzon létre egy szolgáltatási elvet az Azure Active Directory-bérlőben a Portal használata a portál használatával [az erőforrásokhoz hozzáférő Azure Active Directory-alkalmazás és egyszerű szolgáltatás létrehozásához](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)című útmutató használatával. A folyamat során vegye figyelembe az alábbiakat: 

- A bejelentkezési URL-cím hez bármilyen URL-címet megadhat.  
- Hozzon létre új ügyféltitkát ehhez az alkalmazáshoz.  
- Mentse a kulcsot és az ügyfélazonosítót későbbi lépésekben való használatra.  

Adja meg az alkalmazás létrehozott az előző lépésben *monitoring metrikák közzétevőengedélyeket* az erőforrás takar metrikák at. Ha azt tervezi, hogy az alkalmazás segítségével egyéni metrikákat bocsát ki számos erőforrással szemben, ezeket az engedélyeket az erőforráscsoport vagy az előfizetés szintjén adhatja meg.  

> [!NOTE]
> A Diagnosztika bővítmény az egyszerű szolgáltatás használatával hitelesíti az Azure Monitor t, és metrikákat bocsát ki a felhőszolgáltatáshoz.

## <a name="author-diagnostics-extension-configuration"></a>A Szerző diagnosztika bővítményének konfigurációja 

Készítse elő a Diagnosztika kiterjesztés konfigurációs fájlját. Ez a fájl azt diktálja, hogy mely naplókat és teljesítményszámlálókat kell gyűjtenie a Diagnosztika bővítménynek a felhőszolgáltatáshoz. A következőkben a diagnosztikai konfigurációs mintafájl látható:  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

A diagnosztikai fájl "SinksConfig" szakaszában definiáljon egy új Azure Monitor-fogadót: 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

A konfigurációs fájl azon szakaszában, ahol felsorolja a teljesítményszámlálók gyűjteni, adja hozzá az Azure Monitor fogadó. Ez a bejegyzés biztosítja, hogy a megadott összes teljesítményszámláló metrikaként az Azure Monitorra továbbítva legyen. A teljesítményszámlálókat az igényeinek megfelelően adhat hozzá vagy távolíthatja el. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Végül a privát konfigurációban adjon hozzá egy *Azure Monitor-fiók* szakaszt. Adja meg az egyszerű szolgáltatásügyfél-azonosítót és a korábban létrehozott titkos kulcsot. 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

Mentse a diagnosztikai fájlt helyileg.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A Diagnosztika bővítmény üzembe helyezése a felhőszolgáltatásban 

Indítsa el a PowerShellt, és jelentkezzen be az Azure-ba. 

```powershell
Login-AzAccount 
```

A következő parancsokkal tárolja a korábban létrehozott tárfiók adatait. 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

Hasonlóképpen állítsa be a diagnosztikai fájl elérési útját egy változóra a következő paranccsal:

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

Telepítse a Diagnosztika bővítményt a felhőszolgáltatásba a diagnosztikai fájllal az Azure Monitor-fogadóval konfigurálva a következő paranccsal:  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> Továbbra is kötelező a tárfiók biztosítása a Diagnosztika bővítmény telepítésének részeként. A diagnosztikai konfigurációs fájlban megadott naplók vagy teljesítményszámlálók a megadott tárfiókba kerülnek.  

## <a name="plot-metrics-in-the-azure-portal"></a>Metrikák nyomtatása az Azure Portalon 

1. Nyissa meg az Azure Portalt. 

   ![Metrikák Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. A bal oldali menüben válassza a **Monitor lehetőséget.**

3. A **Figyelő** lapon válassza a **Metrikák előnézete** lapot.

4. Az erőforrások legördülő menüben válassza ki a klasszikus felhőszolgáltatást.

5. A névterek legördülő menüben válassza az **azure.vm.windows.guest**lehetőséget. 

6. A metrikák legördülő **menüben válassza a Használatban lévő Memória\Véglegesített bájtok lehetőséget.** 

A dimenziószűrési és -felosztási lehetőségek segítségével megtekintheti az adott szerepkör vagy szerepkörpéldány által használt teljes memóriát. 

 ![Metrikák Azure portal](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>További lépések

- További információ az [egyéni mutatókról.](metrics-custom-overview.md)

