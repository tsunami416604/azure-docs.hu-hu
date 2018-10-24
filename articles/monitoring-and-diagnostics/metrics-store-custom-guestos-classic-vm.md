---
title: Vendég operációs rendszer mérőszámok küldése az Azure Monitor-adattárat a Windows virtuális gép (klasszikus)
description: Vendég operációs rendszer mérőszámok küldése az Azure Monitor-adattárat a Windows virtuális gép (klasszikus)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: 235eda231dfb0f936bf55c7c8d93a8f709fdf9bc
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954849"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>Vendég operációs rendszer mérőszámok küldése az Azure Monitor-adattárat a Windows virtuális gép (klasszikus)

Az Azure Monitor [Windows Azure Diagnostics bővítmény](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (WAD) lehetővé teszi, hogy a vendég operációs rendszer (vendég operációs rendszer) futó virtuális gépek, Cloud Service vagy Service Fabric-fürt részeként metrikák és naplók gyűjtését. A bővítmény telemetriát küldhessen a korábban hivatkozott cikk felsorolt számos különböző helyeken.

Ez a cikk ismerteti a vendég operációs rendszer teljesítmény-mérőszámok küldése a folyamat a Windows virtuális gép (klasszikus) az Azure Monitor metrika tárolón. WAD 1.11-es verzió kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tároló, ahol már standard platform metrikákat gyűjt. Ezen a helyen tárolja őket lehetővé teszi tartozó platform metrikák elérhető ugyanazokat a műveleteket.  Műveletek közé tartozik a közel valós idejű riasztások, diagramkészítési, útválasztás, REST API-t és egyéb elérését.  A múltban a WAD-bővítmény írt Azure Storage, de nem az Azure Monitor adattár. 

Ebben a cikkben leírtak szerint csak a klasszikus virtuális gépeket a Windows operációs rendszert futtató működik.

## <a name="pre-requisites"></a>Előfeltételek

- Kell egy [szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) az Azure-előfizetése 

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) telepítve, vagy használhat [az Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview.md) 

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Egy klasszikus virtuális gép és Tárfiók létrehozása

1. Az Azure Portalon klasszikus virtuális gép létrehozása ![létrehozása a klasszikus virtuális gép](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. Ez a virtuális gép létrehozásakor válassza ki, hozzon létre egy új klasszikus tárfiókot. A későbbi lépésekben használjuk ezt a tárfiókot.

1. Az Azure Portalon keresse meg a Tárfiók-erőforrás paneljének, és válassza ki a **kulcsok** és jegyezze fel a tárfiók nevének és a tárfiók kulcsára. Ezeket a kulcsokat, a későbbi lépésekben szüksége ![tárelérési kulcsok](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egy egyszerű szolgáltatásnév létrehozása az Azure Active Directory-bérlőben címen található utasításokat követve [egyszerű szolgáltatás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md). Megjegyzés: a folyamat során a következő: 
- Új titkos ügyfélkulcsot az alkalmazás létrehozása  
- Mentse a kulcsot, és használja az ügyfél-azonosítóját a későbbi lépésekben.

Ezt az alkalmazást a kívánt gridre bocsáthatja ki az metrikákat az erőforrás "Figyelési metrikákat közzétevő" engedélyeket biztosíthat. Használhatja egy erőforráscsoportot, vagy egy teljes előfizetés.  

> [!NOTE]
> A diagnosztikai bővítményt az egyszerű szolgáltatás használatával hitelesítése az Azure Monitor és a metrikák küldik a klasszikus virtuális gép.

## <a name="author-diagnostics-extension-configuration"></a>Szerző diagnosztikai bővítmény konfigurációja

1. Készítse elő a WAD diagnosztikai bővítmény konfigurációs fájlban. Ez a fájl előírja, hogy mely naplókat és teljesítményszámlálókat a diagnosztikai bővítményt a klasszikus virtuális gép kell gyűjteni. Alul látható egy minta.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        <WadCfg>
        <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
            <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
            <Directories scheduledTransferPeriod="PT1M">
                <IISLogs containerName="wad-iis-logfiles" />
                <FailedRequestLogs containerName="wad-failedrequestlogs" />
            </Directories>
            <PerformanceCounters scheduledTransferPeriod="PT1M">
                <PerformanceCounterConfiguration counterSpecifier="\Processor(*)\% Processor Time" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\Memory\% Committed Bytes" sampleRate="PT15S" />
                <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(*)\Disk Read Bytes/sec" sampleRate="PT15S" />
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
            <Metrics resourceId="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicCompute/virtualMachines/MyClassicVM">
                <MetricAggregation scheduledTransferPeriod="PT1M" />
                <MetricAggregation scheduledTransferPeriod="PT1H" />
            </Metrics>
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
1. A diagnosztika fájl "SinksConfig" szakaszában adja meg egy új Azure Monitor-fogadó:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide your Classic VM’s Resource ID </ResourceId>
                <Region>Region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. A konfigurációs fájlban, ahol szerepel a gyűjtendő kérelemteljesítmény-számlálókat listája szakaszában a teljesítményszámlálók átirányítása az Azure Monitor fogadó "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. A privát konfigurációjának megadása az Azure Monitor-fiókhoz, és adja hozzá a szolgáltatásnév adatait gridre bocsáthatja ki az metrikák használatával.

    ```xml
    <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="" endpoint="" />
        <AzureMonitorAccount>
            <ServicePrincipalMeta>
                <PrincipalId>clientId for your service principal</PrincipalId>
                <Secret>client secret of your service principal</Secret>
            </ServicePrincipalMeta>
        </AzureMonitorAccount>
    </PrivateConfig>
    ```

1. Mentse helyileg a fájlt.

## <a name="deploy-diagnostics-extension-to-your-cloud-service"></a>A Felhőszolgáltatásban üzembe helyezni a diagnosztikai bővítmény

1. Indítsa el a Powershellt, és jelentkezzen be

    ```powershell
    Login-AzureRmAccount
    ```

1. Úgy a környezetet, hogy a klasszikus virtuális gép indítása

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. A klasszikus tárfiók, amely a virtuális gép létrehozásakor a környezet beállítása.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Állítsa be a diagnosztikai fájl elérési útját egy változó használatával az alábbi parancsot.

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Készítsen elő a klasszikus virtuális gép diagnostics-fájllal való frissítése az Azure Monitor-fogadó konfigurálva

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  A frissítés telepítése a virtuális géphez az alábbi parancs futtatásával

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Továbbra is kötelező, biztosít egy Tárfiókot a diagnosztikai bővítmény telepítésének részeként. A megadott tárfiók naplók és/vagy a diagnosztika pluginconfig.JSON fájlban megadott teljesítményszámlálók lesz írva.

## <a name="plot-the-metrics-in-the-azure-portal"></a>A metrikák az Azure Portalon ábrázolása

1.  Keresse meg az Azure Portalon

1.  A bal oldali menüben kattintson a Monitor

1.  A Monitor panelen kattintson a a **metrikák**
   ![keresse meg a metrikák](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. Az erőforrás listából válassza ki a klasszikus virtuális gép

1. Válassza ki az áttelepítendő névterek legördülő **azure.vm.windows.guest**

1. A mérőszámok a legördülő menüben válassza ki **használt lefoglalt bájtok**
   ![metrikákat jeleníti meg](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).
