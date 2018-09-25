---
title: Vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolására klasszikus Felhőszolgáltatás küldése
description: Vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolására klasszikus Felhőszolgáltatás küldése
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: be27ff3f8dda3209a011c3ad79d1a7a1f1d259fe
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986914"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-service"></a>Vendég operációs rendszer mérőszámok az Azure Monitor metrika tárolására klasszikus Felhőszolgáltatás küldése

Az Azure Monitor [Windows Azure Diagnostics bővítmény](azure-diagnostics.md) (WAD) lehetővé teszi, hogy a metrikák és naplók összegyűjtése a vendég operációs rendszer (guestOS) egy virtuális gép, a felhőalapú szolgáltatás vagy a Service Fabric-fürt részeként futó.  A bővítmény telemetriát küldhessen a korábban hivatkozott cikk felsorolt számos különböző helyeken.  

Ez a cikk ismerteti a folyamatot a vendég operációs rendszer teljesítmény-mérőszámok küldése az Azure Monitor metrika áruház az Azure klasszikus Cloud Services. WAD 1.11-es verzió kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tároló, ahol már standard platform metrikákat gyűjt. Ezen a helyen tárolja őket lehetővé teszi tartozó platform metrikák elérhető ugyanazokat a műveleteket.  Műveletek közé tartozik a közel valós idejű riasztások, diagramkészítési, útválasztás, REST API-t és egyéb elérését.  A múltban a WAD-bővítmény írt Azure Storage, de nem az Azure Monitor adattár.  

Ebben a cikkben leírtak szerint csak működik teljesítményszámlálókkal, az Azure Cloud Services. Az egyéb egyéni metrika nem működik. 
   

## <a name="pre-requisites"></a>Előfeltételek

- Kell egy [szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) az Azure-előfizetése 

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) telepítve, vagy használhat [az Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 


## <a name="provision-cloud-service-and-storage-account"></a>Felhőszolgáltatás és a Storage-fiók kiépítése 

1. Létrehozása és üzembe helyezése a klasszikus Felhőszolgáltatás (egy minta klasszikus cloud service-alkalmazás és központi telepítési található [Itt](../cloud-services/cloud-services-dotnet-get-started.md). 

2. Használjon egy meglévő tárfiókot, vagy üzembe helyezhet egy új tárfiókot. Emellett akkor ajánlott, ha a tárfiók a klasszikus Felhőszolgáltatás az imént létrehozott és ugyanabban a régióban van. Az Azure Portalon keresse meg a Tárfiók-erőforrás paneljének, és válassza ki a **kulcsok**. Jegyezze fel a tárfiók nevét és a tárolási fiók kulcsát, frissítenie kell ezeket a későbbi lépésekben.

   ![Tárfiókkulcsok](./media/metrics-store-custom-guestos-classic-cloud-service/storage-keys.png)



## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása 

Az Azure Active Directory-bérlőben címen található utasításokat követve egy egyszerű szolgáltatásnév létrehozása... / azure/azure-resource-manager/resource-group-create-service-principal-portal. Megjegyzés: a folyamat során a következő: 
  - A bejelentkezési URL-címéhez helyezheti bármely URL-címben.  
  - Új titkos ügyfélkulcsot az alkalmazás létrehozása  
  - Mentse a kulcsot, és használja az ügyfél-azonosítóját a későbbi lépésekben.  

Adjon meg az alkalmazáshoz, az előző lépésben létrehozott *figyelési metrikákat közzétevő* az erőforrás kívánt gridre bocsáthatja ki az metrikákat az engedélyeket. Ha azt tervezi, az alkalmazás használatát, gridre bocsáthatja ki az egyéni metrikákat az erőforrások száma, adja meg ezeket az engedélyeket az erőforrás-csoportba vagy előfizetésbe szinten.  

> [!NOTE]
> A diagnosztikai bővítményt használja az egyszerű szolgáltatás hitelesítése az Azure Monitor és küldik a felhőszolgáltatásokhoz tartozó mérőszámok 

## <a name="author-diagnostics-extension-configuration"></a>Szerző diagnosztikai bővítmény konfigurációja 

Készítse elő a WAD diagnosztikai bővítmény konfigurációs fájlban. Ez a fájl előírja, hogy mely naplókat és teljesítményszámlálókat a diagnosztikai bővítményt a felhőszolgáltatásokhoz tartozó kell gyűjteni. Alul látható egy minta diagnosztikai konfigurációs fájlt.  

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

A diagnosztika fájl "SinksConfig" szakaszában adja meg egy új Azure Monitor-fogadó: 

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

A konfigurációs fájlban, ahol a lista teljesítmény gyűjtendő teljesítményszámlálók szakaszában az Azure Monitor-fogadó hozzáadása. Ez a bejegyzés biztosítja, hogy az Azure Monitor mérőszámokként legyenek átirányítva a megadott teljesítményszámlálók. Nyugodtan az igényeinek megfelelően teljesítményszámlálók hozzáadása/eltávolítása. 

```XML
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink"> 
 <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
  … 
</PerformanceCounters> 
```
Végül a privát konfigurációjának hozzáadása egy *Azure Monitor-fiókhoz* szakaszban. Adja meg a szolgáltatáspéldány ügyfél-azonosítója és a titkos kulcs az előző lépésben létrehozott. 

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A Felhőszolgáltatásban üzembe helyezni a diagnosztikai bővítmény 

Indítsa el a PowerShell és a bejelentkezés az Azure-bA 

```PowerShell
Login-AzureRmAccount 
```

Store változók a következő parancsokat a korábbi lépésben létrehozott Tárfiók részleteit részleteit. 

```PowerShell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```
 
Ehhez hasonlóan állítsa be a diagnosztikai fájl elérési útját egy változó használatával az alábbi parancsot. 

```PowerShell
$diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>” 
```
 
Üzembe helyezés a diagnosztikai bővítményt a felhőszolgáltatáshoz a diagnostics-fájllal az Azure Monitor-fogadó konfigurálva, az alábbi paranccsal 

```PowerShell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```
 
> [!NOTE] 
> Továbbra is kötelező, biztosít egy Tárfiókot a diagnosztikai bővítmény telepítésének részeként. A megadott tárfiók naplók és/vagy a diagnosztika pluginconfig.JSON fájlban megadott teljesítményszámlálók lesz írva.  

## <a name="plot-metrics-in-the-azure-portal"></a>Diagram mérőszámok az Azure Portalon 

Keresse meg az Azure Portalon 

 ![Metrikák az Azure Portalon](./media/metrics-store-custom-guestos-classic-cloud-service/navigate-metrics.png)

1. A bal oldali menüben kattintson a képernyő 

1. A metrikák előzetes lapon kattintson a Monitor panelen 

1. Az erőforrás legördülő menüben válassza a klasszikus Felhőszolgáltatás 

1. Válassza ki a legördülő névterek **azure.vm.windows.guest** 

1. A mérőszámok a legördülő menüben válassza ki *használt lefoglalt bájtok* 

Kiválaszthatja, hogy a dimenzió szűrési és képességek felosztása egy tetszőleges szerepre, és minden egyes szerepkör-példány által használt a teljes memória megtekintéséhez. 

 ![Metrikák az Azure Portalon](./media/metrics-store-custom-guestos-classic-cloud-service/metrics-graph.png)

## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).



