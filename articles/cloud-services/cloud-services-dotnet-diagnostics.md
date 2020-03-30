---
title: Az Azure diagnosztika (.NET) használata a Felhőszolgáltatásokkal | Microsoft dokumentumok
description: Az Azure-diagnosztika használatával adatokat gyűjthet az Azure felhőszolgáltatásokból a hibakereséshez, a teljesítmény méréséhez, a figyeléshez, a forgalomelemzéshez és egyebekhez.
services: cloud-services
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: tagore
ms.openlocfilehash: 1e49a0935a70a2470267e5458fa1f55e3059e965
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77469765"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Az Azure-diagnosztika engedélyezése az Azure Felhőszolgáltatásokban
Tekintse meg [az Azure Diagnosztika áttekintése](../azure-diagnostics.md) az Azure Diagnosztika hátterét.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>A diagnosztika engedélyezése feldolgozói szerepkörben
Ez a forgatókönyv bemutatja, hogyan valósítható meg egy Azure-feldolgozói szerepkör, amely telemetriai adatokat bocsát ki a .NET EventSource osztály használatával. Az Azure Diagnostics a telemetriai adatok gyűjtésére és egy Azure-tárfiókban való tárolására szolgál. Feldolgozói szerepkör létrehozásakor a Visual Studio automatikusan engedélyezi a Diagnostics 1.0-s készletét a megoldás részeként az Azure SDK-kban a .NET 2.4-es és korábbi műveletekhez. Az alábbi utasítások ismertetik a feldolgozói szerepkör létrehozásának folyamatát, a Diagnostics 1.0 letiltását a megoldásból, és üzembe helyezi a Diagnosztika 1.2-es vagy 1.3-as folyamatát a feldolgozói szerepkörben.

### <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy rendelkezik egy Azure-előfizetéssel, és a Visual Studio az Azure SDK-val. Ha nem rendelkezik Azure-előfizetéssel, regisztrálhat az [ingyenes próbaverzióra.][Free Trial] Győződjön meg arról, hogy [telepíti és konfigurálja az Azure PowerShell 0.8.7-es vagy újabb verzióját.][Install and configure Azure PowerShell version 0.8.7 or later]

### <a name="step-1-create-a-worker-role"></a>1. lépés: Dolgozói szerepkör létrehozása
1. Indítsa el a **Visual Studiót**.
2. Hozzon létre egy **Azure Cloud Service-projektet** a .NET Framework 4.5-öt megcélozó **Felhősablonból.**  Nevezze el a projektet "WadExample" néven, és kattintson az Ok gombra.
3. Válassza a **Dolgozói szerepkör lehetőséget,** és kattintson az Ok gombra. A projekt létrejön.
4. A **Solution Explorer**ben kattintson duplán a **WorkerRole1** tulajdonságfájlra.
5. A **Konfiguráció** lapon törölje a jelet a **Diagnosztika engedélyezése** a Diagnosztika 1.0 letiltásához (Az Azure SDK 2.4-es és korábbi) letiltása jelölőnégyzetből.
6. A megoldás összeállításához ellenőrizze, hogy nincsenek-e hibák.

### <a name="step-2-instrument-your-code"></a>2. lépés: A kód eszköze
Cserélje le WorkerRole.cs tartalmát a következő kódra. Az [EventSource osztályból][EventSource Class]örökölt SampleEventSourceWriter osztály négy naplózási módszert valósít meg: **SendEnums**, **MessageMethod**, **SetOther** és **HighFreq**. A **WriteEvent** metódus első paramétere határozza meg az adott esemény azonosítóját. A Futtatás metódus egy végtelen ciklust valósít meg, amely 10 másodpercenként meghívja a **SampleEventSourceWriter** osztályban megvalósított naplózási metódusokat.

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>3. lépés: A dolgozói szerepkör telepítése

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. Üzembe helyezheti feldolgozói szerepkörét az Azure-ban a Visual Studio-n belül, válassza ki a **WadExample** projektet a Solution Explorer, majd **a Közzététel** **menüben.**
2. Válassza ki az előfizetését.
3. A **Microsoft Azure közzétételi beállításai** párbeszédpanelen válassza az **Új létrehozása...** lehetőséget.
4. A **Felhőszolgáltatás és a tárfiók létrehozása** párbeszédpanelen adjon meg egy **nevet** (például "WadExample"), és válasszon ki egy régiót vagy affinitáscsoportot.
5. Állítsa a **környezetet** **átmeneti állapotra.**
6. Szükség szerint módosítsa a többi **beállítást,** és kattintson **a Közzététel gombra.**
7. A központi telepítés befejezése után ellenőrizze az Azure Portalon, hogy a felhőszolgáltatás futó állapotban **van-e.**

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>4. lépés: A diagnosztika konfigurációs fájllétrehozása és a bővítmény telepítése
1. Töltse le a nyilvános konfigurációs fájl sémadefinícióját a következő PowerShell-parancs végrehajtásával:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. XML-fájl hozzáadása a **WorkerRole1** projekthez úgy, hogy a jobb gombbal a **WorkerRole1** projektre kattint, és válassza az Új elem **hozzáadása** -> **parancsot...** -> **Visual C# elemek** -> **Adat** -> **XML-fájl**. Nevezze el a fájlt "WadExample.xml".

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. Társítsa a WadConfig.xsd fájlt a konfigurációs fájlhoz. Győződjön meg arról, hogy a WadExample.xml szerkesztő ablak az aktív ablak. Nyomja **le az F4 billentyűt** a **Tulajdonságok** ablak megnyitásához. Kattintson a **Séma** tulajdonságra a **Tulajdonságok** ablakban. Kattintson a **...** a **Séma** tulajdonságban. Kattintson a **Hozzáadás...** gombot, és keresse meg azt a helyet, ahol az XSD-fájlt mentette, és válassza ki a WadConfig.xsd fájlt. Kattintson az **OK** gombra.

4. Cserélje le a WadExample.xml konfigurációs fájl tartalmát a következő XML-re, és mentse a fájlt. Ez a konfigurációs fájl néhány teljesítményszámlálót határoz meg: egyet a PROCESSZOR-kihasználtsághoz, egyet pedig a memóriakihasználtsághoz. Ezután a konfiguráció határozza meg a sampleeventsourcewriter osztály metódusainak megfelelő négy eseményt.

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>5. lépés: Diagnosztika telepítése a dolgozói szerepkörre
A PowerShell-parancsmagok a diagnosztika kezelésére egy webes vagy feldolgozói szerepkör: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension és Remove-AzureServiceDiagnosticsExtension.

1. Nyissa meg az Azure PowerShellt.
2. A parancsfájl végrehajtása a diagnosztikai műveletek nek a feldolgozói szerepkörre történő telepítéséhez (cserélje le a *StorageAccountKey-t* a wadexample tárfiók tárfiókának tárfiókkulcsára, és *config_path* a *WadExample.xml* fájl elérési útját):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>6. lépés: Nézze meg a telemetriai adatokat
A Visual Studio **Server Explorer**ben keresse meg a wadexample tárfiókot. Miután a felhőszolgáltatás körülbelül öt (5) percet futott, látnia kell a **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** és **WADSetOtherTable**táblázatokat. Kattintson duplán az egyik táblára az összegyűjtött telemetriai adatok megtekintéséhez.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Konfigurációs fájl sémája
A Diagnosztika konfigurációs fájl olyan értékeket határoz meg, amelyek a diagnosztikai konfigurációs beállítások inicializálására szolgálnak a diagnosztikai ügynök indításakor. Tekintse meg a [legújabb séma referencia](/azure/azure-monitor/platform/diagnostics-extension-schema) érvényes értékek et és példákat.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha problémája van, olvassa [el az Azure-diagnosztika hibaelhárítása](../azure-diagnostics-troubleshooting.md) című témakört a gyakori problémák megoldásához.

## <a name="next-steps"></a>Következő lépések
[Tekintse meg a kapcsolódó Azure virtuálisgép-diagnosztikai cikkek listáját](../azure-monitor/platform/diagnostics-extension-overview.md) az összegyűjtött adatok módosításához, a problémák elhárításához vagy általában a diagnosztika további információhoz.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/



