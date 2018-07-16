---
title: Az Azure IoT Hub (.NET/.NET) feladatok ütemezéséhez |} A Microsoft Docs
description: Hogyan lehet a több eszközre közvetlen metódus meghívása egy Azure IoT Hub-feladat ütemezése. Az Azure IoT eszközoldali SDK for .NET használatával valósítható meg a szimulált eszközalkalmazások és a egy service-alkalmazás a feladat futtatásához.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: 71de0a86594737f5f581f9a1f638bab0f7b06e8e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056742"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Feladatok ütemezése és kiküldése (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub használatával ütemezheti és nyomon követheti a feladatok, amely több millió eszköz frissítése. Feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

Egy feladat burkolja az alábbi műveletek egyikét, és nyomon követi a végrehajtás egy eszköz ikereszköz-lekérdezés által meghatározott eszközök készlete alapján. Például egy háttér-alkalmazás használatával egy feladat közvetlen metódus meghívása az 10 000 eszköz, amely az eszköz újraindul. Adjon meg az eszközök device twin lekérdezéssel, és a feladat futtatásához egy későbbi időpontban ütemezése. Mivel minden egyes, az eszköz előrehaladását nyomon követi kap, és hajtsa végre az újraindítás közvetlen metódus.

Az egyes képességek kapcsolatos további információkért lásd:

* Ikereszköz és tulajdonságok: [ikereszközök – első lépések] [ lnk-get-started-twin] és [oktatóanyag: eszköz-ikertulajdonságok használata][lnk-twin-props]
* Közvetlen metódusok: [az IoT Hub fejlesztői útmutató – közvetlen metódusok] [ lnk-dev-methods] és [oktatóanyag: közvetlen metódusok használata][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Egy eszközalkalmazás létrehozása, amely megvalósítja a meghívott közvetlen metódusra **LockDoor** , amely a háttéralkalmazás meghívható.
* Hozzon létre egy háttér-alkalmazást, amely létrehoz egy feladatot, amely hívja az **LockDoor** közvetlen metódus több eszközön. Egy másik feladat több eszközre elküldi a kívánt tulajdonság frissítéseket.

Ez az oktatóanyag végén két .NET (C#)-konzolalkalmazással fog rendelkezni:

**SimulateDeviceMethods** , amely csatlakozik az IoT hub és valósítja meg a **LockDoor** közvetlen metódust.

**ScheduleJob** feladatok használatával, amely meghívja a **LockDoor** közvetlen metódust, és több eszközön az ikereszköz kívánt tulajdonságait frissíti.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy .NET-konzolalkalmazást, amely teljes vissza a megoldás által meghívott közvetlen metódusra válaszol hoz létre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **SimulateDeviceMethods**.
   
    ![Új Visual C# Windows klasszikus eszközalkalmazás][img-createdeviceapp]
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceMethods** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT eszközoldali SDK-t] [ lnk-nuget-client-sdk] NuGet csomagot és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az eszköz, amely az előző szakaszban feljegyzett kapcsolati karakterláncát:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a következő, a közvetlen metódus megvalósításához az eszközön:

    ```csharp
    static Task<MethodResponse> LockDoor(MethodRequest methodRequest, object userContext)
    {
        Console.WriteLine();
        Console.WriteLine("Locking Door!");
        Console.WriteLine("\nReturning response for method {0}", methodRequest.Name);
            
        string result = "'Door was locked.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```

1. Adja hozzá a következő, a device twins figyelő megvalósításához az eszközön:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Végül adja hozzá a következő kódot a **fő** metódus az IoT hub-kapcsolat megnyitásához, és a metódus figyelő inicializálása:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

        Client.SetMethodHandlerAsync("LockDoor", LockDoor, null);
        Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null);

        Console.WriteLine("Waiting for direct method call and device twin update\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        Client.SetMethodHandlerAsync("LockDoor", null, null);
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. Mentse a munkáját, és a megoldás felépítéséhez.         

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például az újrapróbálkozási), az MSDN-cikkben leírtak implementálandó [átmeneti hibák kezelésével][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Feladatok ütemezése a közvetlen metódus meghívása, és az ikereszköz-frissítések küldéséhez

Ebben a szakaszban konzolalkalmazást hoz létre egy .NET (C# használatával), amely a feladatok használatával meghívja a **LockDoor** közvetlen metódust, és a kívánt tulajdonság frissítések küldése több eszközre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **ScheduleJob**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]

1. A Megoldáskezelőben kattintson a jobb gombbal a **ScheduleJob** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ezzel a lépéssel letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK] [ lnk-nuget-service-sdk] NuGet csomagot és annak függőségeit.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adja hozzá a következő `using` utasítást, ha még nincs az alapértelmezett utasításokban.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrzőket cserélje le a hub, az előző szakaszban, és az eszköz nevét, amelyet az IoT Hub kapcsolati karakterláncára.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && (result.Status != JobStatus.Failed));
    }
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

1. Adjon meg egy másik metódust a **Program** osztály:

    ```csharp
    public static async Task StartTwinUpdateJob(string jobId)
    {
        Twin twin = new Twin(deviceId);
        twin.Tags = new TwinCollection();
        twin.Tags["Building"] = "43";
        twin.Tags["Floor"] = "3";
        twin.ETag = "*";

        twin.Properties.Desired["LocationUpdate"] = DateTime.UtcNow;

        JobResponse createJobResponse = jobClient.ScheduleTwinUpdateAsync(
            jobId,
            $"DeviceId IN ['{deviceId}']", 
            twin, 
            DateTime.UtcNow, 
            (long)TimeSpan.FromMinutes(2).TotalSeconds).Result;

        Console.WriteLine("Started Twin Update Job");
    }
    ```

    > [!NOTE]
    > A lekérdezési szintaxissal kapcsolatos további információkért lásd: [IoT Hub lekérdezési nyelv][lnk-query].
    > 

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    Console.WriteLine("Press ENTER to start running jobs.");
    Console.ReadLine();

    jobClient = JobClient.CreateFromConnectionString(connString);

    string methodJobId = Guid.NewGuid().ToString();

    StartMethodJob(methodJobId);
    MonitorJob(methodJobId).Wait();
    Console.WriteLine("Press ENTER to run the next job.");
    Console.ReadLine();

    string twinUpdateJobId = Guid.NewGuid().ToString();

    StartTwinUpdateJob(twinUpdateJobId);
    MonitorJob(twinUpdateJobId).Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```

1. Mentse a munkáját, és a megoldás felépítéséhez. 


## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a megoldás, és kattintson **összeállítása**. **Több kezdőprojekt**. Győződjön meg arról, hogy `SimulateDeviceMethods` követ a lista tetején van `ScheduleJob`. Mindkét azok művelet beállítása **Start** kattintson **OK**.

1. Futtassa a projektek kattintva **Start** , vagy a **hibakeresése** menüben, majd kattintson **hibakeresés indítása**.

1. Az eszköz és a háttér-alkalmazások a kimenet jelenik meg.

    ![Feladatok ütemezése az alkalmazások futtatása][img-schedulejobs]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat ütemezése és eszköz az ikereszköz tulajdonságok frissítése egy közvetlen metódus használt.

A folytatáshoz, a vezeték nélküli belső vezérlőprogram frissítésének keresztül Ismerkedés az IoT Hub és az eszközfelügyeleti minták például távolról, olvassa el a [oktatóanyag: hogyan belső vezérlőprogram frissítése][lnk-fwupdate].

Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással kapcsolatos további információkért lásd: [Ismerkedés az IoT Edge szolgáltatással][lnk-iot-edge].

<!-- images -->
[img-createdeviceapp]: ./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png
[img-clientnuget]: ./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png
[img-servicenuget]: media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png
[img-schedulejobs]: media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png

[lnk-get-started-twin]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-twin-props]: iot-hub-csharp-csharp-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-csharp-csharp-direct-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: tutorial-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
