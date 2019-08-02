---
title: Feladatok ütemezhetnek az Azure IoT Hub (.NET/.NET) használatával | Microsoft Docs
description: Azure IoT Hub-feladatok ütemezésének beütemezés közvetlen metódus több eszközön való meghívásához. A .NET-hez készült Azure IoT eszközoldali SDK segítségével implementálhatja a szimulált eszköz alkalmazásait és egy szolgáltatási alkalmazást a feladatok futtatásához.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/06/2018
ms.author: robinsh
ms.openlocfilehash: c4f2994413fca07f4a168cf12ba7967b00b6b0e2
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668106"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Feladatok ütemezett és szórása (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub használatával több millió eszközt frissítő feladatokat ütemezhet és követhet nyomon. Feladatok használata a következőhöz:

* Eszköz kívánt tulajdonságainak frissítése
* Címkék frissítése
* Közvetlen metódusok meghívása

A feladatok az egyik műveletet betakarják, és nyomon követik a végrehajtást az eszköz kettős lekérdezése által meghatározott eszközök készletén. Például egy háttérbeli alkalmazás felhasználhat egy közvetlen metódust az 10 000-es eszközökön, amelyek újraindítják az eszközöket. Megadhatja az eszköz dupla lekérdezéssel rendelkező eszközök készletét, és ütemezheti a feladatot, hogy a későbbiekben fusson. A feladatok nyomon követik az előrehaladást, mivel minden eszköz megkapja és végrehajtja a közvetlen újraindítási módszert.

Ha többet szeretne megtudni ezekről a képességekről, tekintse meg a következőt:

* Eszköz Twin és tulajdonságai: [Ismerkedés az eszközök ikrekkel](iot-hub-csharp-csharp-twin-getstarted.md) és [oktatóanyaggal: Az eszköz Twin tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen metódusok: [IoT hub fejlesztői útmutató – közvetlen](iot-hub-devguide-direct-methods.md) metódusok [és oktatóanyag: Közvetlen metódusok használata](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy **LockDoor** nevű közvetlen metódust megvalósító eszközt, amely a háttérbeli alkalmazás által hívható.

* Hozzon létre egy háttérbeli alkalmazást, amely létrehoz egy feladatot a **LockDoor** Direct metódus több eszközön való meghívásához. Egy másik feladatot a kívánt tulajdonságok frissítései több eszközre küldenek.

Az oktatóanyag végén két .NET (C#) konzol-alkalmazás található:

Az IoT hub-hoz csatlakozó **SimulateDeviceMethods** és a **LockDoor** Direct metódus implementálása.

**ScheduleJob** , amely feladatokkal hívja meg a **LockDoor** Direct metódust, és frissíti az eszköz két kívánt tulajdonságát több eszközön.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio.
* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy olyan .NET-konzol alkalmazást hoz létre, amely a megoldás hátterében meghívott közvetlen metódusra válaszol.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevezze el a projekt **SimulateDeviceMethods**.
   
    ![Új Visual C# Windows klasszikus eszköz alkalmazás](./media/iot-hub-csharp-csharp-schedule-jobs/create-device-app.png)
    
2. Megoldáskezelő kattintson a jobb gombbal a **SimulateDeviceMethods** projektre, majd kattintson a **NuGet-csomagok kezelése..** . elemre.

3. A **NuGet csomagkezelő** ablakban válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices. Client**elemet. Válassza a **telepítés** lehetőséget a **Microsoft. Azure. Devices. Client** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti és hozzáadja az [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagra és annak függőségeire mutató hivatkozást.
   
    ![NuGet csomagkezelő ablak ügyfélalkalmazás](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz-összekapcsolási sztringre:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

6. Adja hozzá a következőt a közvetlen metódus megvalósításához az eszközön:

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

7. Adja hozzá a következőt az eszköz Twins-figyelő megvalósításához az eszközön:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

8. Végül adja hozzá a következő kódot a **Main** metódushoz a IoT hub-hoz való kapcsolódás megnyitásához és a metódus-figyelő inicializálásához:
   
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
        
9. Mentse a munkáját, és hozza létre a megoldását.         

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási házirendeket (például a kapcsolatok újrapróbálkozását) kell végrehajtania, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).
> 

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Feladatok ütemezhetnek közvetlen metódus hívására és az eszköz kettős frissítéseinek küldésére

Ebben a szakaszban olyan .NET-konzol alkalmazást hoz létre (a C#használatával), amely feladatokkal hívja meg a **LockDoor** Direct metódust, és több eszközre küldi el a kívánt tulajdonság-frissítéseket.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevezze el a projekt **ScheduleJob**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-schedule-jobs/createnetapp.png)

2. Megoldáskezelő kattintson a jobb gombbal a **ScheduleJob** projektre, majd kattintson a **NuGet-csomagok kezelése..** . elemre.

3. A **NuGet csomagkezelő** ablakban válassza a **Tallózás**lehetőséget, keresse meg a **Microsoft. Azure. Devices**elemet, válassza a **telepítés** lehetőséget a **Microsoft. Azure. Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagra és annak függőségeire mutató hivatkozást.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-schedule-jobs/servicesdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Ha még nem `using` szerepel az alapértelmezett utasításokban, adja hozzá a következő utasítást.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

6. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrzőket a korábban átmásolt IoT Hub-kapcsolatok karakterláncára a [IoT hub](#get-the-iot-hub-connection-string) -beli kapcsolatok karakterláncának és az eszköz nevének lekérése elemre.

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

9. Adjon hozzá egy másik metódust a **program** osztályhoz:

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
    > A lekérdezési szintaxissal kapcsolatos további információkért lásd: [IoT hub lekérdezési nyelv](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language).
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

11. Mentse a munkáját, és hozza létre a megoldását. 

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A Visual Studio Megoldáskezelő kattintson a jobb gombbal a megoldásra, majd kattintson a **Létrehozás**parancsra. **Több indítási projekt**. Győződjön meg `SimulateDeviceMethods` arról, hogy a lista tetején található, `ScheduleJob`majd a. Állítsa be mindkét műveletét a kezdéshez, majd kattintson **az OK**gombra.

2. A projektek futtatásához kattintson a **Start** gombra, vagy lépjen a **hibakeresés** menüre, és kattintson a **hibakeresés indítása**parancsra.

3. Az eszköz és a háttérbeli alkalmazások kimenete is megjelenik.

    ![Alkalmazások futtatása a feladatok ütemezett futtatásához](./media/iot-hub-csharp-csharp-schedule-jobs/schedulejobs.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy olyan feladatot használt, amely egy közvetlen metódust ütemez egy eszközre, és az eszköz Twin tulajdonságainak frissítését.

Ha továbbra is szeretné megkezdeni a IoT hub és az eszközkezelés mintáit, például a távoli belső [vezérlőprogram frissítését, olvassa el a következő oktatóanyagot: A belső vezérlőprogram frissítése](tutorial-firmware-update.md).

A mesterséges intelligenciát használó eszközök Azure IoT Edge használatával történő üzembe helyezésével kapcsolatos további információkért lásd: [Bevezetés a IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)használatába.