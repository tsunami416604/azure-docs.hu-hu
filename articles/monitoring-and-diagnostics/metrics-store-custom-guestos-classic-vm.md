---
title: A vendég operációs rendszer mérőszámok küldése az Azure Monitor adattárba Windows virtuális gép (klasszikus)
description: A vendég operációs rendszer mérőszámok küldése az Azure Monitor adattárba Windows virtuális gép (klasszikus)
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.component: ''
ms.openlocfilehash: 06b3d97f4b2b7867f09a8c4e5fe974615e9b0c70
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50093420"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-data-store-for-a-windows-virtual-machine-classic"></a>A vendég operációs rendszer mérőszámok küldése az Azure Monitor adattárba Windows virtuális gép (klasszikus)

Az Azure Monitor [diagnosztikai bővítmény](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (úgynevezett "WAD" vagy "Diagnosztika") lehetővé teszi, hogy a metrikák és a egy virtuális gép, a felhőalapú szolgáltatás vagy a Service Fabric részeként a vendég operációs rendszerek (Guest OS) naplóinak gyűjtése a fürt. A bővítmény küldhet telemetriát [számos különböző helyeken.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Ez a cikk azt ismerteti, hogyan vendég operációs rendszer Windows virtuális gép (klasszikus) teljesítmény-mérőszámok küldése az Azure Monitor metrika tárolón. 1.11-es verzió diagnosztikai kezdve írhatja metrikák közvetlenül az Azure Monitor metrikák tárol, ahol már standard platform metrikákat gyűjt. 

Ezen a helyen tárolja őket lehetővé teszi ugyanazokat a műveleteket is platform metrikákat. Műveletek közé tartoznak a közel valós idejű riasztás, a diagram, útválasztás, a REST API-t, és a további hozzáférés. A múltban a diagnosztikai bővítmény írt, az Azure Storage, de nem az Azure Monitor adattár. 

Az ebben a cikkben a folyamat csak a klasszikus virtuális gépeket, a Windows operációs rendszert futtató működik.

## <a name="prerequisites"></a>Előfeltételek

- Kell egy [szolgáltatás-rendszergazdaként vagy társ-rendszergazdaként](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator.md) az Azure-előfizetésében. 

- Az előfizetés regisztrálva kell lenniük [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services#portal). 

- Rendelkeznie kell [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview?view=azurermps-6.8.1) vagy [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) telepítve.

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Egy klasszikus virtuális gép és tárfiók létrehozása

1. Egy klasszikus virtuális gép létrehozása az Azure portal használatával.
   ![Klasszikus virtuális gép létrehozása](./media/metrics-store-custom-guestos-classic-vm/create-classic-vm.png)

1. Ez a virtuális gép létrehozásakor választania, hozzon létre egy új klasszikus tárfiókot. A későbbi lépésekben használjuk ezt a tárfiókot.

1. Az Azure Portalon nyissa meg a **tárfiókok** erőforrás panelen. Válassza ki **kulcsok**, és jegyezze fel a tárfiók nevének és a tárfiók kulcsára. Ezt az információt későbbi lépésekben szüksége.
   ![Tárelérési kulcsok](./media/metrics-store-custom-guestos-classic-vm/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Egy egyszerű szolgáltatásnév létrehozása az Azure Active Directory-bérlőben található utasítások segítségével [egyszerű szolgáltatás létrehozása](../azure-resource-manager/resource-group-create-service-principal-portal.md). Megjegyzés: a folyamat során a következő: 
- Hozzon létre új titkos ügyfélkulcsot ehhez az alkalmazáshoz.
- Mentse a kulcsot, és használja az ügyfél-Azonosítóját a későbbi lépésekben.

Az erőforrás, amelyet szeretne gridre bocsáthatja ki az metrikákat az "Figyelési metrikákat közzétevő" engedélyeket biztosíthat a ezt az alkalmazást. Egy erőforráscsoport vagy egy teljes előfizetés is használhatja.  

> [!NOTE]
> A diagnosztikai bővítményt az egyszerű szolgáltatás hitelesítése az Azure Monitor és a metrikák küldik a klasszikus virtuális gép használja.

## <a name="author-diagnostics-extension-configuration"></a>Szerző diagnosztikai bővítmény konfigurációja

1. Készítse elő a diagnosztikai bővítmény konfigurációs fájlban. Ez a fájl szabja meg, melyik eseménynaplókat és teljesítményszámlálókat a diagnosztikai bővítményt a klasszikus virtuális gép kell gyűjteni. Következő példája:

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
1. A diagnosztika fájl a "SinksConfig" területen adja meg egy új Azure Monitor-fogadó módon:

    ```xml
    <SinksConfig>
        <Sink name="AzMonSink">
            <AzureMonitor>
                <ResourceId>Provide the resource ID of your classic VM </ResourceId>
                <Region>The region your VM is deployed in</Region>
            </AzureMonitor>
        </Sink>
    </SinksConfig>
    ```

1. A konfigurációs fájlban, ahol szerepel a gyűjtendő kérelemteljesítmény-számlálókat listája szakaszában a teljesítményszámlálók átirányítása az Azure Monitor-fogadó "AzMonSink".

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. A privát bővítménykonfiguráció határoz meg az Azure Monitor-fiókhoz. Majd adja hozzá a szolgáltatásnév adatait gridre bocsáthatja ki az metrikák használatával.

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

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A felhőszolgáltatásban üzembe helyezni a diagnosztikai bővítmény

1. Indítsa el a Powershellt, és jelentkezzen be.

    ```powershell
    Login-AzureRmAccount
    ```

1. Indítsa el a környezet beállítása a klasszikus virtuális gép.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. A klasszikus tárfiók, amely a virtuális gép létrehozásakor a környezet beállítása.

    ```powershell
    $StorageContext = New-AzureStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Állítsa be a diagnosztikai fájl elérési útját egy változóhoz a következő paranccsal:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Készítse elő a frissítést, a klasszikus virtuális gép, amely rendelkezik az Azure Monitor-fogadó konfigurált diagnostics-fájllal.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  A frissítés központi telepítését a virtuális gépre a következő parancs futtatásával:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Továbbra is kötelező, biztosít egy tárfiókot a diagnosztikai bővítmény telepítésének részeként. A megadott tárfiók naplóit, vagy a diagnosztika pluginconfig.JSON fájlban megadott teljesítményszámlálók lesz írva.

## <a name="plot-the-metrics-in-the-azure-portal"></a>A metrikák az Azure Portalon ábrázolása

1.  Nyissa meg az Azure Portalt. 

1.  A bal oldali menüben válassza ki a **figyelő.**

1.  Az a **figyelő** panelen válassza ki **metrikák**.

    ![Keresse meg a metrikák](./media/metrics-store-custom-guestos-classic-vm/navigate-metrics.png)

1. A források legördülő menüben válassza ki a klasszikus virtuális gép.

1. A névterek legördülő menüben válassza ki a **azure.vm.windows.guest**.

1. A metrikák legördülő menüben válassza ki a **használt lefoglalt bájtok**.
   ![Diagram-metrikák](./media/metrics-store-custom-guestos-classic-vm/plot-metrics.png)


## <a name="next-steps"></a>További lépések
- Tudjon meg többet [egyéni metrikákat](metrics-custom-overview.md).
