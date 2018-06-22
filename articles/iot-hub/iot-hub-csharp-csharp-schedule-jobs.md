---
title: Azure IoT Hub (.NET/.NET) feladatok ütemezése |} Microsoft Docs
description: How to Schedule a több eszközre közvetlen metódus egy Azure IoT Hub-feladat ütemezése. Az Azure IoT-eszközök a .NET SDK használatával megvalósítható a szimulált eszköz alkalmazások és a service-alkalmazást, a feladat futtatásához.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: dobett
ms.openlocfilehash: beb1e1e166325cb41a5d4e4fa07565b1f3d4b3bb
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308584"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Ütemezés és a feladatok (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub használatával ütemezése, illetve követheti nyomon, hogy az eszközök millióira frissítése feladatok. Feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok

Egy feladat becsomagolja az alábbi műveletek egyikét, és nyomon követi a végrehajtás egy eszközt a két lekérdezést által meghatározott eszközök készlete alapján. Például egy háttér-alkalmazás segítségével egy feladat meghívni egy 10 000 közvetlen metódust, amely az eszköz újraindul. Adjon meg az eszközök eszköz iker lekérdezéssel, és a feladat egy későbbi időpontban futtatásának ütemezését. A feladat nyomon követi előrehaladását, mint a eszközökhöz kapja meg, és a rendszer újraindítása közvetlen metódus végrehajtására.

Az egyes képességek kapcsolatos további információkért lásd:

* A két eszköz és a tulajdonságok: [Ismerkedés az eszköz twins] [ lnk-get-started-twin] és [oktatóanyag: kettős eszköztulajdonságok használata][lnk-twin-props]
* A közvetlen módszer: [IoT Hub fejlesztői útmutató - közvetlen módszerek] [ lnk-dev-methods] és [oktatóanyag: közvetlen módszerekkel][lnk-c2d-methods]

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy eszköz-alkalmazást, amely egy közvetlen a hívott metódus **LockDoor** , hogy a háttér-alkalmazás által hívható.
* Hozzon létre egy háttér-alkalmazást, amely létrehoz egy feladatot, amely hívja az **LockDoor** közvetlen módszer több eszközön. Egy másik feladat kívánt tulajdonság frissítései több eszközre elküldi.

Ez az oktatóanyag végén két .NET (C#) konzol alkalmazások közül választhat:

**SimulateDeviceMethods** , amely csatlakozik az IoT-központ és megvalósítja az **LockDoor** közvetlen módszer.

**ScheduleJob** használó feladatok hívni a **LockDoor** közvetlen módszer, és frissítse a eszköztulajdonságok iker szükséges több eszközön.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]


## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely válaszol a végfelhasználók által a megoldás háttérrendszere nevű közvetlen metódust hoz létre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **SimulateDeviceMethods**.
   
    ![Új Visual C# klasszikus Windows-eszköz alkalmazás][img-createdeviceapp]
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceMethods** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT-eszközök SDK] [ lnk-nuget-client-sdk] NuGet csomag és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;

    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az eszköz kapcsolati karakterlánccal, amely az előző szakaszban feljegyzett:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a következőt valósítja meg a közvetlen az eszközön:

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

1. Adja hozzá a következő, az eszköz twins figyelő végrehajtásához az eszközön:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Végül adja hozzá az alábbi kódot a **fő** módszert, nyissa meg a kapcsolatot az IoT hub és a metódus figyelő inicializálása:
   
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
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban, meg kell valósítania újrapróbálkozási szabályzatok (például újrapróbálkozási), az MSDN-cikkben leírtak [átmeneti hiba kezelése][lnk-transient-faults].
> 


## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Közvetlen metódus hívása, és az eszköz iker frissítések küldése feladatok ütemezése

Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#) használó feladatok hívására hoz létre a **LockDoor** közvetlen módszer és a kívánt tulajdonság frissítések küldése több eszközre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **ScheduleJob**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]

1. A Megoldáskezelőben kattintson a jobb gombbal a **ScheduleJob** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez a lépés tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK] [ lnk-nuget-service-sdk] NuGet csomag és annak függőségeit.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adja hozzá a következő `using` utasítást, ha az alapértelmezett utasításokban még nincs letöltve.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrzőket cserélje le a az előző szakaszban és az eszköz nevét a hub IoT-központ kapcsolati karakterláncát.

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

1. Adja hozzá egy másik módszer a **Program** osztály:

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
    > Lekérdezési szintaxissal kapcsolatos további információkért lásd: [IoT-központ lekérdezési nyelv][lnk-query].
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

1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a megoldás, és kattintson **Build**. **Több kiindulási projektet**. Győződjön meg arról, hogy `SimulateDeviceMethods` követi a lista elején `ScheduleJob`. Mindkét a lépések beállítása **Start** kattintson **OK**.

1. Futtassa a projektek kattintva **Start** , vagy a **Debug** menüre, majd **Start Debugging**.

1. A kimenet az eszköz és a háttér-alkalmazások jelenik meg.

    ![Az alkalmazások ütemezett feladatok futtatása][img-schedulejobs]


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat ütemezése a közvetlen módszer egy eszköz és a két eszköz tulajdonságok frissítése használt.

Folytatja, a légkondicionáló frissítést keresztül Ismerkedés az IoT-központ és az eszköz felügyeleti minták például távolról, olvassa el a [oktatóanyag: a belső vezérlőprogram frissítésének menete][lnk-fwupdate].

AI peremhálózati eszközökre az Azure IoT peremhálózati központi telepítésével kapcsolatos további tudnivalókért lásd: [Ismerkedés az IoT-Edge][lnk-iot-edge].

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
[lnk-fwupdate]: iot-hub-csharp-csharp-firmware-update.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://docs.microsoft.com/azure/architecture/best-practices/transient-faults
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-query]: https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language
