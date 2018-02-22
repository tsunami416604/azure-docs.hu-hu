---
title: "Cloud Services Azure diagnostics (.NET) használata |} Microsoft Docs"
description: "Az Azure diagnostics használatával az adatok gyűjtését a hibakeresést, méri a teljesítményt, figyelés, forgalom elemzése és további Azure Felhőszolgáltatások."
services: cloud-services
documentationcenter: .net
author: thraka
manager: timlt
editor: 
ms.assetid: 89623a0e-4e78-4b67-a446-7d19a35a44be
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/22/2017
ms.author: adegeo
ms.openlocfilehash: a8d6b16fa363062e06d48bfc5af2ca37697d5cd8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/22/2018
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services"></a>Azure Cloud Services Azure Diagnostics engedélyezése
Lásd: [Azure Diagnostics áttekintése](../azure-diagnostics.md) az Azure Diagnostics háttérként.

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Diagnosztika a feldolgozói szerepkörök engedélyezése
Ez a bemutató ismerteti, hogyan megvalósításához egy Azure feldolgozói szerepkör, amely bocsát ki a .NET EventSource osztály használatával telemetriai adatokat. Az Azure Diagnostics szolgál a telemetriai adatokat gyűjt, és tárolja az Azure storage-fiók. A feldolgozói szerepkör létrehozásakor a Visual Studio automatikusan engedélyezi a diagnosztika 1.0-s .NET 2.4 és korábbi Azure SDK-k a megoldás részeként. A következő útmutatások mutatják be, a feldolgozói szerepkör diagnosztika 1.0 letiltása a megoldást, és a központi telepítés diagnosztika 1.2-es vagy 1.3 a feldolgozói szerepkör lehet létrehozni.

### <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy az Azure-előfizetéssel rendelkezik, és a Visual Studio Azure SDK-t használ. Ha még nem rendelkezik Azure-előfizetéssel, regisztrálhat az a [ingyenes][Free Trial]. Ügyeljen arra, hogy [telepítse és konfigurálja az Azure Powershellt 0.8.7 verzió vagy újabb][Install and configure Azure PowerShell version 0.8.7 or later].

### <a name="step-1-create-a-worker-role"></a>1. lépés: A feldolgozói szerepkör létrehozása
1. Indítsa el a **Visual Studiót**.
2. Hozzon létre egy **Azure Cloud Service** -projektet a **felhő** sablon, amelynek célpontja a .NET-keretrendszer 4.5.  A projekt "WadExample" nevet, és kattintson az OK gombra.
3. Válassza ki **feldolgozói szerepkör** kattintson az OK gombra. A projekt létrehozása.
4. A **Megoldáskezelőben**, kattintson duplán a **WorkerRole1** tulajdonságok fájlt.
5. A a **konfigurációs** lap, un-ellenőrzés **engedélyezése diagnosztikai** a diagnosztika 1.0 (az Azure SDK 2.4-es és korábbi) le van tiltva.
6. Ellenőrizze, hogy rendelkezik-e hibák a megoldás felépítéséhez.

### <a name="step-2-instrument-your-code"></a>2. lépés: Állíthatnak be a kódot
Cserélje le a WorkerRole.cs tartalmát az alábbira. Az osztály SampleEventSourceWriter, öröklődik a [EventSource osztály][EventSource Class], négy naplózási módszerek megvalósítja: **SendEnums**, **MessageMethod** , **SetOther** és **HighFreq**. Az első paraméterben a **WriteEvent** módszer határozza meg a megfelelő Azonosítóját. A Run metódus valósítja meg, amely behívja a naplózási módszer valósult meg végtelen hurkot a **SampleEventSourceWriter** 10 másodpercenként osztályban.

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
            // see the MSDN topic at http://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>3. lépés: A feldolgozói szerepkör telepítése

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. A feldolgozói szerepkör telepítése a Visual Studio Azure kiválasztásával a **WadExample** projektben a Megoldáskezelőre majd **közzététel** a a **Build** menü.
2. Válassza ki az előfizetését.
3. Az a **Microsoft Azure közzétételi beállítási** párbeszédablakban válassza **új létrehozása...** .
4. Az a **felhőalapú szolgáltatás létrehozása és a Tárfiók** párbeszédpanelen adja meg egy **neve** (például "WadExample"), és válasszon régiót vagy affinitáscsoportot.
5. Állítsa be a **környezet** való **átmeneti**.
6. Módosíthatja bármely más **beállítások** regisztrációja, mivel a megfelelő, és kattintson a **közzététel**.
7. Miután a telepítés befejeződése után ellenőrizze az Azure portálon, amely a felhőszolgáltatás egy **futtató** állapota.

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>4. lépés: A diagnosztika konfigurációs fájl létrehozása és a-kiterjesztés telepítése
1. Töltse le a nyilvános konfigurációs fájl sémadefiníciót hajtja végre a következő PowerShell-parancsot:

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. Az XML-fájl hozzáadása a **WorkerRole1** kattintson a jobb gombbal a projekt a **WorkerRole1** projektre, és válassza ki **Hozzáadás** -> **új elem...** -> **Visual C# elemek** -> **adatok** -> **XML-fájl**. A fájl "WadExample.xml" nevet.

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. A WadConfig.xsd társítani a konfigurációs fájlban. Ellenőrizze, hogy a WadExample.xml-szerkesztő ablakban az aktív ablak. Nyomja le az **F4** megnyitásához a **tulajdonságok** ablak. Kattintson a **sémák** tulajdonságot a **tulajdonságok** ablak. Kattintson a **...** az a **sémák** tulajdonság. Kattintson a **Hozzáadás...** gombra, és keresse meg a helyet, ahová mentette az XSD-fájlt, és válassza ki a fájlt WadConfig.xsd. Kattintson az **OK** gombra.

4. Cserélje le a WadExample.xml konfigurációs fájl tartalmát az alábbi XML-fájl, és mentse a fájlt. A konfigurációs fájl határozza meg néhány gyűjtendő kérelemteljesítmény-számlálókat: egy CPU-felhasználás, egy, a memória-felhasználás. A konfigurációs majd SampleEventSourceWriter osztályban módszerekhez megfelelő négy események határozza meg.

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

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>5. lépés: Telepítse a feldolgozói szerepkör diagnosztika
A diagnosztika webes vagy feldolgozói szerepkör kezelése a PowerShell-parancsmagok érhetők: Set-AzureServiceDiagnosticsExtension, a Get-AzureServiceDiagnosticsExtension és a Remove-AzureServiceDiagnosticsExtension.

1. Nyissa meg az Azure PowerShell.
2. Diagnosztika a feldolgozói szerepkör telepítéséhez futtassa a parancsfájlt (csere *StorageAccountKey* wadexample tárfiók a tárolási fiók kulccsal és *config_path* az elérési útját a  *WadExample.xml* fájl):

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>6. lépés: Nézze meg a telemetriai adatok
A Visual Studio **Server Explorer**, lépjen a wadexample tárfiókhoz. Miután a felhőalapú szolgáltatás körülbelül öt (5) percig futott, megjelenik a táblák **WADEnumsTable**, **WADHighFreqTable**, **WADMessageTable**, **WADPerformanceCountersTable** és **WADSetOtherTable**. Kattintson duplán a táblák gyűjtött telemetriai adatok megtekintéséhez.

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>Konfigurációs fájl séma
A diagnosztika konfigurációs fájl határozza meg a diagnosztikai konfigurációs beállítások inicializálása a diagnosztikai ügynök indulásakor használt értékek. Tekintse meg a [legújabb sémareferenciája](https://msdn.microsoft.com/library/azure/mt634524.aspx) az érvényes értékek és a példákat.

## <a name="troubleshooting"></a>Hibaelhárítás
Ha gondja támad, lásd: [hibaelhárítási Azure Diagnostics](../azure-diagnostics-troubleshooting.md) segítséget a gyakori problémák megoldásához.

## <a name="next-steps"></a>További lépések
[A kapcsolódó Azure virtuális gép diagnosztikai cikkek megtekintéséhez](../monitoring-and-diagnostics/azure-diagnostics.md#cloud-services-using-azure-diagnostics) kapcsolatos problémák elhárítása a gyűjtött adatok módosításához vagy kapcsolatos további diagnosztikai általában.

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
