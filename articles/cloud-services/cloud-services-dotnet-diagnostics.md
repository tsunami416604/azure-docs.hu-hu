---
title: Azure-diagnosztika (.NET) használata a Cloud Serviceshez |} A Microsoft Docs
description: Az Azure diagnostics használatával gyűjthet velük adatokat az Azure cloud Services, a hibakeresés, a teljesítmény, figyelés, forgalomelemzéshez és egyéb mérési.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: jeconnoc
ms.openlocfilehash: ba69a5aaffb39c26731ffd209587a8c8223b032a
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58915357"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Azure Diagnostics használatának engedélyezésével az Azure Cloud Servicesben
Lásd: [Azure Diagnostics – áttekintés](../azure-diagnostics.md) Azure Diagnostics háttér számára.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Feldolgozói szerepkör-diagnosztika engedélyezése
Ez a forgatókönyv ismerteti, hogyan valósíthat meg egy Azure feldolgozói szerepkörnek, amely telemetriai adatokat a .NET EventSource osztállyal bocsát ki. Az Azure Diagnostics telemetriai adatok gyűjtése, és tárolja az Azure storage-fiók szolgál. Feldolgozói szerepkör létrehozásakor, a Visual Studio automatikusan engedélyezi a diagnosztikai 1.0 az Azure SDK for .NET 2.4 és a korábbi megoldás részeként. Az alábbi utasításokat a feldolgozói szerepkör a diagnosztikai 1.0 letiltása a megoldást, és üzembe helyezése diagnosztikai 1.2-es vagy 1.3 dolgozó munkaköre létrehozásának folyamatát ismertetik.

### <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy egy Azure-előfizetés és a Visual Studio használata az Azure SDK-t. Ha nem rendelkezik Azure-előfizetéssel, akkor Regisztráljon a [ingyenes próbaverzió][Free Trial]. Ügyeljen arra, hogy [telepítse és konfigurálja az Azure Powershellt 0.8.7 verzió vagy újabb][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>1. lépés: Feldolgozói szerepkör létrehozása
1. Indítsa el a **Visual Studiót**.
2. Hozzon létre egy **Azure Cloud Service** -projektet a **felhőalapú** sablont, amely a .NET-keretrendszer 4.5 célozza.  Adja a projektnek "WadExample", és kattintson az OK gombra.
3. Válassza ki **feldolgozói szerepkör** , és kattintson az OK gombra. A projekt létrejön.
4. A **Megoldáskezelőben**, kattintson duplán a **WorkerRole1** tulajdonságok fájlt.
5. Az a **konfigurációs** lapon törölje **diagnosztika engedélyezése** diagnosztikai 1.0 (az Azure SDK 2.4-es és korábbi verziók) letiltása.
6. Hozhat létre egy megoldást, hogy ellenőrizze, hogy rendelkezik-e hibák.

### <a name="step-2-instrument-your-code"></a>2. lépés: A kód alkalmazásáról
Cserélje le a WorkerRole.cs tartalmát az alábbira. A SampleEventSourceWriter, osztály örökli a [EventSource osztály][EventSource Class], négy naplózási metódusokat valósít meg: **SendEnums**, **MessageMethod**, **SetOther** és **HighFreq**. Az első paraméterként a **WriteEvent** metódus határozza meg a megfelelő esemény azonosítója. A Run metódus valósítja meg, amely meghívja a naplózás módszer megvalósított végtelen ciklust a **SampleEventSourceWriter** osztály 10 másodpercenként.

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


### <a name="step-3-deploy-your-worker-role"></a>3. lépés: A feldolgozói szerepkör telepítése

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. A feldolgozói szerepkör kiválasztásával helyezze üzembe az Azure a Visual Studión belül a **WadExample** projektben a Megoldáskezelőre majd **közzététel** származó a **hozhat létre** menü.
2. Válassza ki az előfizetését.
3. Az a **a Microsoft Azure közzétételi beállítások** párbeszédablakban válassza **új létrehozása...** .
4. Az a **Felhőszolgáltatás létrehozása és a Storage-fiók** párbeszédpanelen adja meg egy **neve** (például "WadExample"), és válasszon ki egy régiót vagy affinitáscsoport.
5. Állítsa be a **környezet** való **átmeneti**.
6. Módosíthatja bármely más **beállítások** , a megfelelő, és kattintson a **közzététel**.
7. A telepítés befejezése után ellenőrizze az Azure Portalon, amely a felhőszolgáltatás egy **futó** állapota.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>4. lépés: A diagnosztika konfigurációs fájl létrehozása és a bővítmény telepítése
1. Töltse le a nyilvános bővítménykonfiguráció sémája fájldefiníciót a következő PowerShell-parancs végrehajtásával:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Adja hozzá a kívánt XML-fájlt a **WorkerRole1** kattintson a jobb gombbal a projekt a **WorkerRole1** projektre, és válassza **Hozzáadás** -> **új elem...** -> **Visual C#-elemek** -> **adatok** -> **XML-fájl**. A fájl "WadExample.xml" nevet.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. A WadConfig.xsd társítani a konfigurációs fájlban. Ellenőrizze, hogy a WadExample.xml-szerkesztő ablakban az aktív ablak. Nyomja meg **F4** megnyitásához a **tulajdonságok** ablak. Kattintson a **sémák** tulajdonságot a **tulajdonságok** ablak. Kattintson a **...** az a **sémák** tulajdonság. Kattintson a **Hozzáadás...** gombra, és keresse meg a helyet, ahová mentette az XSD-fájlt, és válassza ki a fájlt WadConfig.xsd. Kattintson az **OK** gombra.

4. Cserélje le a WadExample.xml konfigurációs fájl tartalmát a következő XML formátumú, és mentse a fájlt. A konfigurációs fájl határozza meg, néhány gyűjtendő teljesítményszámlálókat: egy CPU-kihasználtság, egy, a memóriahasználat. Majd a konfiguráció megfelelő a SampleEventSourceWriter osztály módszerek négy események határozza meg.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>5. lépés: Diagnosztika a feldolgozói szerepkör telepítése
Egy webes vagy feldolgozói szerepkör diagnosztikai kezelése a PowerShell-parancsmagok a következők: Set-AzureServiceDiagnosticsExtension, Get-AzureServiceDiagnosticsExtension, and Remove-AzureServiceDiagnosticsExtension.

1. Nyissa meg az Azure Powershellt.
2. Hajtsa végre a parancsfájl diagnosztikai telepíthető a feldolgozói szerepkör (cserélje le *StorageAccountKey* a wadexample tárfiókhoz a tárfiók-kulcsot az és *config_path* az elérési útját a  *WadExample.xml* fájl):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>6. lépés: Tekintse meg a telemetriai adatok
A Visual Studio **Server Explorer**, lépjen a wadexample tárfiókhoz. A felhőszolgáltatás nagyjából öt (5) perces futott, miután megjelenik-e a táblák **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** és **WADSetOtherTable**. Kattintson duplán az egyik táblázat a gyűjtött telemetria megtekintéséhez.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Konfigurációs fájl séma
A diagnosztika konfigurációs fájljának inicializálása diagnosztikai beállításait, a diagnosztikai ügynök indulásakor használt értékeket határozza meg. Tekintse meg a [legújabb sémaleírás](/azure/azure-monitor/platform/diagnostics-extension-schema) az érvényes értékek és a példákat.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha gondja van, tekintse meg [Azure Diagnostics hibaelhárítása](../azure-diagnostics-troubleshooting.md) segítséget a gyakori problémák megoldásához.

## <a name="next-steps"></a>További lépések
[Kapcsolódó Azure virtuális gép diagnosztikai cikkek listája](../azure-monitor/platform/diagnostics-extension-overview.md#cloud-services-using-azure-diagnostics) gyűjtött adatok módosításához kapcsolatos hibák elhárítása, vagy tudjon meg többet a diagnosztikai általában.

[EventSource Class]: https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: https://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/
