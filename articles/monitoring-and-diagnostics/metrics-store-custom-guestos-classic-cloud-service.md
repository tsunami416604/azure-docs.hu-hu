---
title: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolására klasszikus Cloud Services
description: Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolja a Cloud Services
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 30b08062aa360c4a43dc1bfe9f574447b58521f5
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095211"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Küldés a vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolására klasszikus Cloud Services 
Az Azure Monitor szolgáltatással [diagnosztikai bővítmény](azure-diagnostics.md), gyűjthet metrikák és a egy virtuális gép, a felhőalapú szolgáltatás vagy a Service Fabric-fürt részeként futó a vendég operációs rendszerek (Guest OS) származó naplók. A bővítmény küldhet telemetriát [számos különböző helyeken.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Ez a cikk ismerteti a folyamatot a vendég operációs rendszer teljesítmény-mérőszámok az Azure klasszikus Felhőszolgáltatás küldését a metrika az Azure Monitor-tároló. 1.11-es verzió diagnosztikai kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tárol, ahol már standard platform metrikákat gyűjt. 

Ezen a helyen tárolja őket lehetővé teszi ugyanazokat a műveleteket, platform metrikákat is elérheti. Műveletek közé tartoznak a közel valós idejű riasztás, a diagram, útválasztás, a REST API-t, és a további hozzáférés.  A múltban a diagnosztikai bővítmény írt, az Azure Storage, de nem az Azure Monitor adattár.  

Ez a cikk csak működik az Azure Cloud Servicesben teljesítményszámlálók a folyamata. Az egyéb egyéni metrika nem működik. 
   

## <a name="prerequisites"></a>Előfeltételek

- Kell egy [szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) az Azure-előfizetésében. 

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) telepítve.


## <a name="provision-a-cloud-service-and-storage-account"></a>A felhő és a tárolási fiók kiépítése 

1. Hozzon létre és telepíthet egy klasszikus felhőszolgáltatást. Egy minta klasszikus Cloud Services-alkalmazás és központi telepítési fürtpéldány [Ismerkedés az Azure Cloud Services és ASP.NET](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Használjon egy meglévő tárfiókot, vagy üzembe helyezhet egy új tárfiókot. Emellett akkor ajánlott, ha a tárfiók a klasszikus felhőszolgáltatásáról létrehozott ugyanabban a régióban van. Az Azure Portalon nyissa meg a **tárfiókok** erőforráspanel, és válassza ki **kulcsok**. Jegyezze fel a tárfiók nevét és a tárfiók-kulcsot. Ezt az információt későbbi lépésekben szüksége.

   ![Tárfiókkulcsok](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 

Egy egyszerű szolgáltatásnév létrehozása az Azure Active Directory-bérlőben található utasítások segítségével [egy Azure Active Directory-alkalmazás és -erőforrások eléréséhez szolgáltatásnév létrehozása a portálon](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Amíg a folyamat során fog, vegye figyelembe a következőket: 

  - A bejelentkezési URL-címéhez helyezheti bármely URL-címben.  
  - Hozzon létre új titkos ügyfélkulcsot ehhez az alkalmazáshoz.  
  - Mentse a kulcsot, és használja az ügyfél-Azonosítóját a későbbi lépésekben.  

Adjon meg az alkalmazáshoz, az előző lépésben létrehozott *figyelési metrikákat közzétevő* engedélyekkel a metrikákat az kibocsátható kívánt erőforrást. Ha azt tervezi, az alkalmazás használatát, gridre bocsáthatja ki az egyéni metrikákat az erőforrások száma, adja meg ezeket az engedélyeket az erőforrás-csoportba vagy előfizetésbe szinten.  

> [!NOTE]
> A diagnosztikai bővítményt használja az egyszerű szolgáltatás hitelesítése az Azure Monitor és a felhőszolgáltatásokhoz tartozó mérőszámok küldik.

## <a name="author-diagnostics-extension-configuration"></a>Szerző diagnosztikai bővítmény konfigurációja 

Készítse elő a diagnosztikai bővítmény konfigurációs fájlban. Ez a fájl szabja meg, melyik eseménynaplókat és teljesítményszámlálókat a diagnosztikai bővítményt a felhőszolgáltatásokhoz tartozó kell gyűjteni. Következő mintafájl diagnosztikai konfigurációs:  

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

A diagnosztika fájl a "SinksConfig" területen adja meg egy új Azure Monitor-fogadó: 

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

A konfigurációs fájlban, ahol a gyűjtendő teljesítményszámlálókat listázza az szakaszban adja hozzá az Azure Monitor-fogadó. Ez a bejegyzés biztosítja, hogy az Azure Monitor mérőszámokként megadott összes teljesítményszámlálójához legyenek irányítva. Adja hozzá, vagy távolítsa el a teljesítményszámlálók igény szerint. 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

Végül a privát konfigurációjának hozzáadása egy *Azure Monitor-fiókhoz* szakaszban. Adja meg a szolgáltatáspéldány ügyfél-azonosítója és a titkos kulcsot, amelyet korábban hozott létre. 

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
 
Mentse helyileg a diagnosztikai fájlt.  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A felhőszolgáltatásban üzembe helyezni a diagnosztikai bővítmény 

Indítsa el a Powershellt, és jelentkezzen be az Azure-bA. 

```PowerShell
Login-AzureRmAccount 
```

A következő parancsokat használja a korábban létrehozott tárfiók adatait tárolja. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
Hasonlóképpen állítsa be a diagnosztikai fájl elérési útja változóhoz a következő paranccsal:

```PowerShell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Üzembe helyezés a diagnosztikai bővítményt a felhőszolgáltatáshoz a diagnostics-fájllal az Azure Monitor-fogadó konfigurálva, a következő paranccsal:  

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Továbbra is kötelező, biztosít egy tárfiókot a diagnosztikai bővítmény telepítésének részeként. A megadott tárfiók naplóit, vagy a diagnosztika pluginconfig.JSON fájlban megadott teljesítményszámlálók kerüljenek.  

## <a name="plot-metrics-in-the-azure-portal"></a>Diagram mérőszámok az Azure Portalon 

1. Nyissa meg az Azure Portalt. 

   ![Metrikák az Azure Portalon](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

2. A bal oldali menüben válassza ki a **figyelő.**

3. Az a **figyelő** panelen válassza ki a **metrikák előzetes** fülre.

4. A források legördülő menüben válassza ki a klasszikus felhőszolgáltatásáról.

5. A névterek legördülő menüben válassza ki a **azure.vm.windows.guest**. 

6. A metrikák legördülő menüben válassza ki a **használt lefoglalt bájtok**. 

A dimenzió szűrést, és a felosztás képességek segítségével megtekintheti egy adott szerepkör vagy a szerepkör-példány által használt a teljes memória. 

 ![Metrikák az Azure Portalon](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).



