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
ms.openlocfilehash: 4089ee74b44bc17324c7cd33b1bbd734a651d75f
ms.sourcegitcommit: 5a1d601f01444be7d9f405df18c57be0316a1c79
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2018
ms.locfileid: "51514808"
---
# <a name="schedule-and-broadcast-jobs-netnet"></a>Feladatok ütemezése és kiküldése (.NET/.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Azure IoT Hub használatával ütemezheti és nyomon követheti a feladatok, amely több millió eszköz frissítése. Feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése
* A címkék frissítése
* Közvetlen metódusok meghívása

Egy feladat burkolja az alábbi műveletek egyikét, és nyomon követi a végrehajtás egy eszköz ikereszköz-lekérdezés által meghatározott eszközök készlete alapján. Például egy háttér-alkalmazás használatával egy feladat közvetlen metódus meghívása az 10 000 eszköz, amely az eszköz újraindul. Adjon meg az eszközök device twin lekérdezéssel, és a feladat futtatásához egy későbbi időpontban ütemezése. Mivel minden egyes, az eszköz előrehaladását nyomon követi kap, és hajtsa végre az újraindítás közvetlen metódus.

Az egyes képességek kapcsolatos további információkért lásd:

* Ikereszköz és tulajdonságok: [ikereszközök – első lépések](iot-hub-csharp-csharp-twin-getstarted.md) és [oktatóanyag: eszköz-ikertulajdonságok használata](tutorial-device-twins.md)

* Közvetlen metódusok: [az IoT Hub fejlesztői útmutató – közvetlen metódusok](iot-hub-devguide-direct-methods.md) és [oktatóanyag: közvetlen metódusok használata](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Egy eszközalkalmazás létrehozása, amely megvalósítja a meghívott közvetlen metódusra **LockDoor** , amely a háttéralkalmazás meghívható.

* Hozzon létre egy háttér-alkalmazást, amely létrehoz egy feladatot, amely hívja az **LockDoor** közvetlen metódus több eszközön. Egy másik feladat több eszközre elküldi a kívánt tulajdonság frissítéseket.

Ez az oktatóanyag végén két .NET (C#)-konzolalkalmazással fog rendelkezni:

**SimulateDeviceMethods** , amely csatlakozik az IoT hub és valósítja meg a **LockDoor** közvetlen metódust.

**ScheduleJob** feladatok használatával, amely meghívja a **LockDoor** közvetlen metódust, és több eszközön az ikereszköz kívánt tulajdonságait frissíti.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2017.
* Aktív Azure-fiók. Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy .NET-konzolalkalmazást, amely teljes vissza a megoldás által meghívott közvetlen metódusra válaszol hoz létre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **SimulateDeviceMethods**.
   
    ![Új Visual C# Windows klasszikus eszközalkalmazás](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceMethods** projektre, és kattintson a **NuGet-csomagok kezelése...** .

3. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **Microsoft.Azure.Devices.Client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT eszközoldali SDK-t](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagot és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az eszköz, amely az előző szakaszban feljegyzett kapcsolati karakterláncát:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Adja hozzá a következő, a közvetlen metódus megvalósításához az eszközön:

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

7. Adja hozzá a következő, a device twins figyelő megvalósításához az eszközön:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Végül adja hozzá a következő kódot a **fő** metódus az IoT hub-kapcsolat megnyitásához, és a metódus figyelő inicializálása:
   
    ```csharp
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
          TransportType.Mqtt);

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
        
9. Mentse a munkáját, és a megoldás felépítéséhez.         

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például az újrapróbálkozási), a cikkben leírtak implementálandó [átmeneti hibák kezelésével](/azure/architecture/best-practices/transient-faults).
> 

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Feladatok ütemezése a közvetlen metódus meghívása, és az ikereszköz-frissítések küldéséhez

Ebben a szakaszban konzolalkalmazást hoz létre egy .NET (C# használatával), amely a feladatok használatával meghívja a **LockDoor** közvetlen metódust, és a kívánt tulajdonság frissítések küldése több eszközre.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **ScheduleJob**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. A Megoldáskezelőben kattintson a jobb gombbal a **ScheduleJob** projektre, és kattintson a **NuGet-csomagok kezelése...** .

3. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás**, keresse meg **Microsoft.Azure.Devices**, jelölje be **telepítése** telepítéséhez a **Microsoft.Azure.Devices** csomagot, és fogadja el a használati feltételeket. Ezzel a lépéssel letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagot és annak függőségeit.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adja hozzá a következő `using` utasítást, ha még nincs az alapértelmezett utasításokban.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrzőket cserélje le a hub, az előző szakaszban, és az eszköz nevét, amelyet az IoT Hub kapcsolati karakterláncára.

    ```csharp
    static JobClient jobClient;
    static string connString = "<yourIotHubConnectionString>";
    static string deviceId = "<yourDeviceId>";
    ```

7. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task MonitorJob(string jobId)
    {
        JobResponse result;
        do
        {
            result = await jobClient.GetJobAsync(jobId);
            Console.WriteLine("Job Status : " + result.Status.ToString());
            Thread.Sleep(2000);
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

8. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async Task StartMethodJob(string jobId)
    {
        CloudToDeviceMethod directMethod = 
          new CloudToDeviceMethod("LockDoor", TimeSpan.FromSeconds(5), 
          TimeSpan.FromSeconds(5));
       
        JobResponse result = await jobClient.ScheduleDeviceMethodAsync(jobId,
            $"DeviceId IN ['{deviceId}']",
            directMethod,
            DateTime.UtcNow,
            (long)TimeSpan.FromMinutes(2).TotalSeconds);

        Console.WriteLine("Started Method Job");
    }
    ```

9. Adjon meg egy másik metódust a **Program** osztály:

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
    > A lekérdezési szintaxissal kapcsolatos további információkért lásd: [IoT Hub lekérdezési nyelv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
    > 

10. Végül adja a következő sorokat a **Main** metódushoz:

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

11. Mentse a munkáját, és a megoldás felépítéséhez. 

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a megoldás, és kattintson **összeállítása**. **Több kezdőprojekt**. Győződjön meg arról, hogy `SimulateDeviceMethods` követ a lista tetején van `ScheduleJob`. Mindkét azok művelet beállítása **Start** kattintson **OK**.

2. Futtassa a projektek kattintva **Start** , vagy a **hibakeresése** menüben, majd kattintson **hibakeresés indítása**.

3. Az eszköz és a háttér-alkalmazások a kimenet jelenik meg.

    ![Feladatok ütemezése az alkalmazások futtatása](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat ütemezése és eszköz az ikereszköz tulajdonságok frissítése egy közvetlen metódus használt.

A folytatáshoz, a vezeték nélküli belső vezérlőprogram frissítésének keresztül Ismerkedés az IoT Hub és az eszközfelügyeleti minták például távolról, olvassa el a [oktatóanyag: hogyan belső vezérlőprogram frissítése](tutorial-firmware-update.md).

Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással kapcsolatos további információkért lásd: [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md).