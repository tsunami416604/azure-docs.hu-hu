---
title: Feladatok ütemezése az Azure IoT Hubbal (.NET/.NET) | Microsoft dokumentumok
description: Egy Azure IoT Hub-feladat ütemezése egy közvetlen metódus meghívására több eszközön. Az Azure IoT-eszköz SDK a .NET a szimulált eszköz alkalmazások és egy szolgáltatásalkalmazás a feladat futtatásához.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 7925ca5c69d01b098764ff744fb832eaa43118d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77108968"
---
# <a name="schedule-and-broadcast-jobs-net"></a>Feladatok ütemezése és közvetítése (.NET)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

Az Azure IoT Hub használatával több millió eszközt frissítő feladatokat ütemezheti és követheti nyomon. A feladatok használata:

* Eszköz kívánt tulajdonságainak frissítése

* Címkék frissítése

* Közvetlen metódusok meghívása

A feladat becsomagolja az egyik ilyen műveleteket, és nyomon követi a végrehajtást egy eszköz ikerlekérdezés által meghatározott eszközök körén. Például egy háttéralkalmazás egy feladat segítségével meghívhat egy közvetlen metódust 10 000 eszközön, amely újraindítja az eszközöket. Megadhatja az eszközök egy eszköz iker lekérdezést, és ütemezi a feladat futtatásához egy későbbi időpontban. A feladat nyomon követi a folyamatot, ahogy az egyes eszközök megkapják és végrehajtják az újraindításközvetlen metódust.

Az egyes képességekről az alábbi témakörben olvashat bővebben:

* Ikereszköz és tulajdonságok: [Ismerkedés az ikereszközökkel](iot-hub-csharp-csharp-twin-getstarted.md) és [az oktatóanyaggal: Az ikereszköz tulajdonságainak használata](tutorial-device-twins.md)

* Közvetlen módszerek: [IoT Hub fejlesztői útmutató - közvetlen módszerek](iot-hub-devguide-direct-methods.md) és [oktatóanyag: Közvetlen módszerek használata](quickstart-control-device-dotnet.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy eszközalkalmazást, amely egy **LockDoor**nevű közvetlen metódust valósít meg, amelyet a háttéralkalmazás hívhat meg.

* Hozzon létre egy háttéralkalmazást, amely létrehoz egy feladatot a **LockDoor** közvetlen metódus több eszközön való hívásához. Egy másik feladat több eszközre küldi a kívánt tulajdonságfrissítéseket.

Az oktatóanyag végén két .NET (C#) konzolalkalmazás található:

* **DeviceMethods szimulálása**. Ez az alkalmazás csatlakozik az IoT hubhoz, és megvalósítja a **LockDoor** közvetlen metódust.

* **Feladat ütemezése**. Ez az alkalmazás feladatokat használ a **LockDoor** közvetlen metódus hívásához és az eszköz iker kívánt tulajdonságainak frissítéséhez több eszközön.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban egy .NET konzolalkalmazást hoz létre, amely a megoldás háttérrendszeráltal megnevezett közvetlen metódusra reagál.

1. A Visual Studio alkalmazásban válassza **az Új projekt létrehozása**lehetőséget, majd válassza a Console App **(.NET Framework)** projektsablont. A folytatáshoz kattintson a **Tovább** gombra.

1. Az **Új projekt konfigurálása**csoportban nevezze el a project *EtióMia metódusait,* majd válassza **a Create (Create) lehetőséget.**

    ![A SimulateDeviceMethods projekt konfigurálása](./media/iot-hub-csharp-csharp-schedule-jobs/configure-device-app.png)

1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceMethods** projektre, majd válassza **a NuGet csomagok kezelése parancsot.**

1. A **NuGet csomagkezelőben**válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft.Azure.Devices.Client**elemet. Válassza az **Install** (Telepítés) lehetőséget.

    ![A NuGet csomagkezelő ablak ügyfélalkalmazása](./media/iot-hub-csharp-csharp-schedule-jobs/device-app-nuget.png)

    Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az [Azure IoT-eszköz SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomag és a függőségek.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értéket az előző szakaszban említett eszközkapcsolati karakterláncra:

    ```csharp
    static string DeviceConnectionString = "<yourDeviceConnectionString>";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a következő kódot a közvetlen metódus eszközre való megvalósításához:

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

1. Adja hozzá a következő módszert az eszköz twins figyelőjének megvalósításához az eszközön:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
    {
        Console.WriteLine("Desired property change:");
        Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
    }
    ```

1. Végül adja hozzá a következő kódot a **fő** metódushoz az IoT hubhoz való kapcsolat megnyitásához és a metódusfigyelő inicializálásához:

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

1. Mentse el munkáját, és építse fel a megoldást.

> [!NOTE]
> A dolgok egyszerűseérdekében ez az oktatóanyag nem valósít meg újrapróbálkozási házirendeket. Az éles kódban újrapróbálkozási házirendeket (például a kapcsolat újrapróbálkozását) kell megvalósítania, ahogy azt az [Átmeneti hibakezelés javasolta.](/azure/architecture/best-practices/transient-faults)
>

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-schedule-jobs-shared-access-policy-text](../../includes/iot-hub-howto-schedule-jobs-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

## <a name="schedule-jobs-for-calling-a-direct-method-and-sending-device-twin-updates"></a>Feladatok ütemezése közvetlen metódus hívásához és ikereszköz-frissítések küldéséhez

Ebben a szakaszban egy .NET konzolalkalmazást hoz létre (C#használatával), amely feladatokat használ a **LockDoor** közvetlen metódus hívásához és a kívánt tulajdonságfrissítések több eszközre küldéséhez.

1. A Visual Studióban válassza az**Új** > **projekt** **fájlja** > lehetőséget. Az **Új projekt létrehozása**csoportban válassza a Console App **(.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Az **Új projekt konfigurálása**területen nevezze el a *ScheduleJob projektet.* A **Megoldás**csoportban válassza **a Hozzáadás a megoldáshoz**lehetőséget, majd a **Create (Létrehozás)** lehetőséget.

    ![A ScheduleJob projekt elnevezése és konfigurálása](./media/iot-hub-csharp-csharp-schedule-jobs/config-schedule-job-app.png)

1. A Megoldáskezelőben kattintson a jobb gombbal a **ScheduleJob** projektre, majd válassza **a NuGet-csomagok kezelése parancsot.**

1. A **NuGet csomagkezelőben**válassza a **Tallózás**gombot, és válassza a **Microsoft.Azure.Devices**elemet, majd a **Telepítés**lehetőséget.

   Ez a lépés letölti, telepíti, és hozzáadja az [Azure IoT szolgáltatás SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagés annak függőségeit.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adja hozzá `using` a következő utasítást, ha még nincs meg az alapértelmezett utasításokban.

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrzőket az IoT Hub kapcsolati karakterlánc, amely et korábban másolt [az IoT hub kapcsolati karakterlánc és](#get-the-iot-hub-connection-string) az eszköz nevét.

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
        } while ((result.Status != JobStatus.Completed) && 
          (result.Status != JobStatus.Failed));
    }
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

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

1. Adjon hozzá egy másik módszert a **Program** osztályhoz:

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
    > A lekérdezés szintaxisáról az [IoT Hub lekérdezési nyelve](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-query-language)című témakörben talál további információt.
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

1. Mentse el munkáját, és építse fel a megoldást.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A Visual Studio Solution Explorer programban kattintson a jobb gombbal a megoldásra, majd válassza **az Indítási projektek beállítása parancsot.**

1. Válassza a Common Properties Startup Project **(Általános tulajdonságok** > **indítási projekt)** lehetőséget, majd a **Több indítási projekt**lehetőséget.

1. Győződjön `SimulateDeviceMethods` meg arról, hogy `ScheduleJob`a lista tetején található, majd a. Állítsa mindkét műveletet **a Kezdőképernyőre,** és válassza **az OK gombot.**

1. Futtassa a projekteket a **Start** menü re kattintva, vagy lépjen a **Hibakeresés** menüre, majd kattintson a **Hibakeresés indítása parancsra.**

   A kimenet az eszköz és a háttéralkalmazások.

    ![Az alkalmazások futtatása feladatok ütemezéséhez](./media/iot-hub-csharp-csharp-schedule-jobs/schedule-jobs-console-results.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy feladat segítségével ütemezte a közvetlen metódust egy eszközre, és az ikereszköz tulajdonságainak frissítését.

* Az IoT Hub és az eszközkezelési minták , például a távoli belső vezérlőprogram frissítésének megkezdéséhez olvassa el az [Oktatóanyag: Belső vezérlőprogram-frissítés .](tutorial-firmware-update.md)

* Az AI azure IoT Edge-el való üzembe helyezéséről az [IoT Edge – Első lépések.](../iot-edge/tutorial-simulate-device-linux.md)
