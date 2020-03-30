---
title: Klasszikus Windows virtuális gép-metrikák küldése az Azure Monitor metrika-adatbázisába
description: Vendég operációsrendszer-metrikák küldése az Azure Monitor adattárába egy Windows virtuális géphez (klasszikus)
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: ''
ms.openlocfilehash: 65bb1a3915ece384974da12b4e7a1ad0c1e08133
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655815"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metrics-database-for-a-windows-virtual-machine-classic"></a>Vendég operációsrendszer-metrikák küldése az Azure Monitor metrikák adatbázisegy Windows virtuális gép (klasszikus)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor [diagnosztikai bővítmény](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) (más néven "WAD" vagy "Diagnosztika") lehetővé teszi, hogy egy virtuális gép, felhőszolgáltatás vagy service fabric-fürt részeként futó vendég operációs rendszerből (Vendég operációs rendszer) metrikákat és naplókat gyűjtsön. A bővítmény telemetriai adatokat küldhet [számos különböző helyre.](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json)

Ez a cikk ismerteti a folyamat vendég operációs rendszer teljesítménymetrikák küldése egy Windows virtuális gép (klasszikus) az Azure Monitor metrika-adatbázis. Az 1.11-es diagnosztikai verzióval kezdve metrikákat írhat közvetlenül az Azure Monitor metrikatárolóba, ahol a standard platformmetrikák már összegyűjtöttek. 

Ezen a helyen való tárolásuk lehetővé teszi, hogy ugyanazokat a műveleteket érje el, mint a platformmetrikák esetében. A műveletek közé tartozik a közel valós idejű riasztás, a diagramkészítés, az útválasztás, a REST API-ból való hozzáférés és egyebek. A múltban a Diagnosztika bővítmény írt az Azure Storage, de nem az Azure Monitor adattárba. 

A cikkben ismertetett folyamat csak a Windows operációs rendszert futtató klasszikus virtuális gépeken működik.

## <a name="prerequisites"></a>Előfeltételek

- [Az Azure-előfizetésben szolgáltatás-rendszergazdának vagy társrendszergazdának](../../cost-management-billing/manage/add-change-subscription-administrator.md) kell lennie. 

- Előfizetését regisztrálni kell a [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services). 

- Az [Azure PowerShellt](/powershell/azure) vagy az [Azure Cloud Shellt](https://docs.microsoft.com/azure/cloud-shell/overview) telepítenie kell.

- A virtuálisgép-erőforrásnak [olyan régióban kell lennie, amely támogatja az egyéni metrikákat.](metrics-custom-overview.md#supported-regions)

## <a name="create-a-classic-virtual-machine-and-storage-account"></a>Klasszikus virtuális gép- és tárfiók létrehozása

1. Hozzon létre egy klasszikus virtuális gép az Azure Portalhasználatával.
   ![Klasszikus virtuális gép létrehozása](./media/collect-custom-metrics-guestos-vm-classic/create-classic-vm.png)

1. A virtuális gép létrehozásakor válassza ki az új klasszikus tárfiók létrehozásához. Ezt a tárfiókot a későbbi lépésekben használjuk.

1. Az Azure Portalon nyissa meg a **Storage-fiókok** erőforrás panel. Válassza a **Kulcsok**lehetőséget, és vegye figyelembe a tárfiók nevét és a tárfiók kulcsát. Erre az információra későbbi lépésekben van szüksége.
   ![Tárolási hozzáférési kulcsok](./media/collect-custom-metrics-guestos-vm-classic/storage-access-keys.png)

## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Hozzon létre egy szolgáltatáselvet az Azure Active Directory-bérlőben az [egyszerű szolgáltatás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md)című útmutató utasításaival. A folyamat során vegye figyelembe a következőket: 
- Hozzon létre új ügyféltitkát ehhez az alkalmazáshoz.
- Mentse a kulcsot és az ügyfélazonosítót későbbi lépésekben való használatra.

Adja meg az alkalmazás "Monitoring Metrics Publisher" engedélyeket az erőforrás, amely a metrikák at szeretne kibocsátni. Használhatja az erőforráscsoportot vagy egy teljes előfizetést.  

> [!NOTE]
> A Diagnosztika bővítmény az egyszerű szolgáltatás használatával hitelesíti az Azure Monitor és a klasszikus virtuális gép metrikák at bocsátanak ki.

## <a name="author-diagnostics-extension-configuration"></a>A Szerző diagnosztika bővítményének konfigurációja

1. Készítse elő a Diagnosztika kiterjesztés konfigurációs fájlját. Ez a fájl azt diktálja, hogy mely naplók és teljesítményszámlálók a diagnosztika bővítmény kell gyűjteni a klasszikus virtuális gép. Íme egy példa:

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
1. A diagnosztikai fájl "SinksConfig" szakaszában definiáljon egy új Azure Monitor-fogadót az alábbiak szerint:

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

1. A konfigurációs fájl azon szakaszában, ahol a teljesítményszámlálók listáját felsorolják, a teljesítményszámlálók az Azure Monitor "AzMonSink" fogadóba irányítsa.

    ```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
    ```

1. A privát konfigurációban adja meg az Azure Monitor-fiókot. Ezután adja hozzá a szolgáltatás egyszerű adatokat metrikák kibocsátására.

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

1. Mentse a fájlt helyileg.

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>A Diagnosztika bővítmény üzembe helyezése a felhőszolgáltatásban

1. Indítsa el a PowerShellt, és jelentkezzen be.

    ```powershell
    Login-AzAccount
    ```

1. Kezdje a klasszikus virtuális gép környezetének beállításával.

    ```powershell
    $VM = Get-AzureVM -ServiceName <VM’s Service_Name> -Name <VM Name>
    ```

1. Állítsa be a virtuális géptel létrehozott klasszikus tárfiók környezetét.

    ```powershell
    $StorageContext = New-AzStorageContext -StorageAccountName <name of your storage account from earlier steps> -storageaccountkey "<storage account key from earlier steps>"
    ```

1.  Állítsa a Diagnosztikai fájl elérési útját egy változóra a következő paranccsal:

    ```powershell
    $diagconfig = “<path of the diagnostics configuration file with the Azure Monitor sink configured>”
    ```

1.  Készítse elő a frissítést a klasszikus virtuális gép a diagnosztikai fájl, amely az Azure Monitor fogadó konfigurálva van.

    ```powershell
    $VM_Update = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $diagconfig -VM $VM -StorageContext $Storage_Context
    ```

1.  Telepítse a frissítést a virtuális gépre a következő parancs futtatásával:

    ```powershell
    Update-AzureVM -ServiceName "ClassicVMWAD7216" -Name "ClassicVMWAD" -VM $VM_Update.VM
    ```

> [!NOTE]
> Továbbra is kötelező a tárfiók biztosítása a Diagnosztika bővítmény telepítésének részeként. A Diagnosztika konfigurációs fájlban megadott naplók vagy teljesítményszámlálók a megadott tárfiókba lesznek írva.

## <a name="plot-the-metrics-in-the-azure-portal"></a>A metrikák nyomtatása az Azure Portalon

1.  Nyissa meg az Azure Portalt. 

1.  A bal oldali menüben válassza a **Monitor lehetőséget.**

1.  A **Figyelő** panelen válassza a **Metrikák**lehetőséget.

    ![Navigálás metrikáiközött](./media/collect-custom-metrics-guestos-vm-classic/navigate-metrics.png)

1. Az erőforrások legördülő menüben válassza ki a klasszikus virtuális gép.

1. A névterek legördülő menüben válassza az **azure.vm.windows.guest**lehetőséget.

1. A metrikák legördülő **menüben válassza a Használatban lévő Memória\Véglegesített bájtok lehetőséget.**
   ![Metrikák nyomtatása](./media/collect-custom-metrics-guestos-vm-classic/plot-metrics.png)


## <a name="next-steps"></a>További lépések
- További információ az [egyéni mutatókról.](metrics-custom-overview.md)

