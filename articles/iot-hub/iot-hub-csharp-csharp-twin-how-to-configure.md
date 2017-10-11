---
title: "Azure IoT Hub eszköz iker tulajdonságait (.NET/.NET) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub eszköz twins eszközök konfigurálásához. Az Azure IoT-eszközök a .NET SDK használatával valósítja meg a szimulált eszköz alkalmazások és az Azure IoT szolgáltatás SDK for .NET egy szolgáltatás-alkalmazást, amely módosítja a használatával egy eszközt a két eszköz konfigurációs végrehajtásához."
services: iot-hub
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: 3c627476-f982-43c9-bd17-e0698c5d236d
ms.service: iot-hub
ms.devlang: csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/10/2017
ms.author: dkshir
ms.openlocfilehash: 679cda28bf3ce9fb207fe3693a3453b355f1de15
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2017
---
# <a name="use-desired-properties-to-configure-devices"></a>Használni kívánt tulajdonságokat eszközök konfigurálása
[!INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

Ez az oktatóanyag végén hogy két .NET konzol alkalmazások:

* **SimulateDeviceConfiguration**, a szimulált eszköz alkalmazás, amely megvárja-e a szükséges konfiguráció frissítése a jelentést készít egy szimulált konfigurációs frissítési folyamat állapotát.
* **SetDesiredConfigurationAndQuery**, egy háttér-alkalmazást, amely beállítja a kívánt konfiguráció egy eszközön, és lekérdezi a konfigurációs frissítési folyamat.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].

Ha követte a [Ismerkedés az eszköz twins] [ lnk-twin-tutorial] oktatóanyagban már rendelkezik egy IoT-központot, és egy eszközidentitás nevű **myDeviceId**. Ebben az esetben ugorjon a [a szimulált eszköz-alkalmazás létrehozása] [ lnk-how-to-configure-createapp] szakasz.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

<a id="#create-the-simulated-device-app"></a>
## <a name="create-the-simulated-device-app"></a>A szimulált eszköz-alkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**megvárja-e a szükséges konfiguráció frissítése a, majd jelentést készít a frissítések szimulált konfigurációs frissítési folyamat.

1. A Visual Studio, hozzon létre egy új Visual C# klasszikus Windows asztal projektet használatával a **Konzolalkalmazás** projektsablon. Nevet a projektnek **SimulateDeviceConfiguration**.
   
    ![Új Visual C# klasszikus Windows-eszköz alkalmazás][img-createdeviceapp]

1. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceConfiguration** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT-eszközök SDK] [ lnk-nuget-client-sdk] NuGet csomag és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz kapcsolati karakterlánccal.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();

1. Adja hozzá a **Program** osztályhoz a következő módszert:
 
        public static void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }
    A **ügyfél** vezérlőnek az eszköz twins az eszközről interaktív szükséges összes módszert. A fent látható kód inicializálja a **ügyfél** objektumot, és majd beolvassa az eszköz iker a **myDeviceId**.

1. Adja hozzá a következő metódust a **Program** osztály. Ez a módszer telemetriai adatot a kezdeti értékekre állítja be a helyi eszközön, és frissíti az eszköz iker.

        public static async void InitTelemetry()
        {
            try
            {
                Console.WriteLine("Report initial telemetry config:");
                TwinCollection telemetryConfig = new TwinCollection();
                
                telemetryConfig["configId"] = "0";
                telemetryConfig["sendFrequency"] = "24h";
                reportedProperties["telemetryConfig"] = telemetryConfig;
                Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Adja hozzá a következő metódust a **Program** osztály. Ez egy visszahívást, amely észleli a változást az *tulajdonságok szükséges* az eszköz iker a.

        private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            try
            {
                Console.WriteLine("Desired property change:");
                Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

                var currentTelemetryConfig = reportedProperties["telemetryConfig"];
                var desiredTelemetryConfig = desiredProperties["telemetryConfig"];

                if ((desiredTelemetryConfig != null) && (desiredTelemetryConfig["configId"] != currentTelemetryConfig["configId"]))
                {
                    Console.WriteLine("\nInitiating config change");
                    currentTelemetryConfig["status"] = "Pending";
                    currentTelemetryConfig["pendingConfig"] = desiredTelemetryConfig;

                    await Client.UpdateReportedPropertiesAsync(reportedProperties);

                    CompleteConfigChange();
                }
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    Ez a módszer a jelentett a helyi eszközön a két objektum tulajdonságainak frissítése a konfigurációs frissítési kérelmet az állapota, **függőben lévő**, majd frissíti az eszköz a két szolgáltatás. Miután sikeresen frissített a két eszköz, a metódus meghívásával befejezné a konfiguráció módosításakor `CompleteConfigChange` leírása a következő az.

1. Adja hozzá a következő metódust a **Program** osztály. Ez a módszer szimulálja egy eszköz alaphelyzetbe állítása, majd a frissítéseket a helyi jelentett az állapot tulajdonságok **sikeres** , és eltávolítja a **pendingConfig** elemet. Ezután frissíti az eszköz a két szolgáltatás. 

        public static async void CompleteConfigChange()
        {
            try
            {
                var currentTelemetryConfig = reportedProperties["telemetryConfig"];

                Console.WriteLine("\nSimulating device reset");
                await Task.Delay(30000); 

                Console.WriteLine("\nCompleting config change");
                currentTelemetryConfig["configId"] = currentTelemetryConfig["pendingConfig"]["configId"];
                currentTelemetryConfig["sendFrequency"] = currentTelemetryConfig["pendingConfig"]["sendFrequency"];
                currentTelemetryConfig["status"] = "Success";
                currentTelemetryConfig["pendingConfig"] = null;

                await Client.UpdateReportedPropertiesAsync(reportedProperties);
                Console.WriteLine("Config change complete \nPress any key to exit.");
            }
            catch (AggregateException ex)
            {
                foreach (Exception exception in ex.InnerExceptions)
                {
                    Console.WriteLine();
                    Console.WriteLine("Error in sample: {0}", exception);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

1. Végül adja hozzá a következő sorokat a **fő** módszert:

        try
        {
            InitClient();
            InitTelemetry();

            Console.WriteLine("Wait for desired telemetry...");
            Client.SetDesiredPropertyUpdateCallback(OnDesiredPropertyChanged, null).Wait();
            Console.ReadKey();
        }
        catch (AggregateException ex)
        {
            foreach (Exception exception in ex.InnerExceptions)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", exception);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }

   > [!NOTE]
   > Ez az oktatóanyag nem szimulálása egyidejű keresni minden olyan esetben. Néhány konfigurációs frissítési folyamat közben fut-e a frissítés, néhány lehet a várólistába helyezni őket, és néhány sikerült hibát elutasítása a célként megadott konfigurációs módosítások befogadásához lehet. Ügyeljen arra, hogy fontolja meg a kívánt viselkedés, a konfigurációs folyamat, és adja hozzá a megfelelő logikai kezdeményezése a konfiguráció módosítása előtt.
   > 
   > 
1. A megoldás felépítéséhez, és futtassa az eszköz alkalmazást a Visual Studio eszközből kattintva **F5**. A kimeneti konzolon megtekintheti az üzeneteket, jelezve, hogy a szimulált eszköz beolvassa az eszköz a két, telemetriai adatok beállításával és a kívánt tulajdonság módosításának várakozás. Tartsa meg az alkalmazás futását.

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely frissíti hoz létre a *szükséges tulajdonságok* meg az eszköz a két társított **myDeviceId** új telemetriai configuration objektummal. Ezután lekérdezi az eszköz twins az IoT hub tárolja, és a kívánt és jelentett konfigurációkat, az eszköz közötti különbséget szemlélteti.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **SetDesiredConfigurationAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]
1. A Megoldáskezelőben kattintson a jobb gombbal a **SetDesiredConfigurationAndQuery** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using System.Threading;
        using Newtonsoft.Json;
1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        static private async Task SetDesiredConfigurationAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch = new {
                    properties = new {
                        desired = new {
                            telemetryConfig = new {
                                configId = Guid.NewGuid().ToString(),
                                sendFrequency = "5m"
                            }
                        }
                    }
                };
   
            await registryManager.UpdateTwinAsync(twin.DeviceId, JsonConvert.SerializeObject(patch), twin.ETag);
            Console.WriteLine("Updated desired configuration");
   
            while (true)
            {
                var query = registryManager.CreateQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'");
                var results = await query.GetNextAsTwinAsync();
                foreach (var result in results)
                {
                    Console.WriteLine("Config report for: {0}", result.DeviceId);
                    Console.WriteLine("Desired telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Desired["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine("Reported telemetryConfig: {0}", JsonConvert.SerializeObject(result.Properties.Reported["telemetryConfig"], Formatting.Indented));
                    Console.WriteLine();
                }
                Thread.Sleep(10000);
            }
        }
   
    A **beállításjegyzék** vezérlőnek eszköz twins a szolgáltatás együttműködhet szükséges összes módszert. Ezzel a kóddal inicializálja a **beállításjegyzék** objektumazonosító, beolvassa az eszköz iker a **myDeviceId**, majd frissíti a kívánt tulajdonságát egy új telemetriai configuration objektummal.
    Ezt követően azt lekérdezi az eszköz twins tárolja az IoT hub 10 másodpercenként, és kiírja a kívánt és jelentett telemetriai konfigurációkat. Tekintse meg a [IoT-központ lekérdezési nyelv] [ lnk-query] további információt az eszközök közötti hatékony jelentések létrehozásához.
   
   > [!IMPORTANT]
   > Ez az alkalmazás lekérdezi az IoT-központ 10 másodpercenként szemléltetési célokat szolgál. Lekérdezésekkel számos eszközön keresztül a felhasználók számára is elérhető jelentések létrehozásához, és nem észleli a módosításokat. Ha a megoldás a valós idejű értesítések eszköz események van szüksége, [iker értesítések][lnk-twin-notifications].
   > 
   > 
1. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        SetDesiredConfigurationAndQuery();
        Console.WriteLine("Press any key to quit.");
        Console.ReadLine();
1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **SetDesiredConfigurationAndQuery** projekt **Start**. A megoldás felépítéséhez.
1. A **SimulateDeviceConfiguration** futtató eszköznek az alkalmazás, szolgáltatás-alkalmazás futtatása a Visual Studio használatával **F5**. Módosítsa a jelentésben szereplő konfigurációs kell megjelennie **függőben lévő** való **sikeres** az új aktív a Küldés 24 óra helyett öt perces gyakoriságot.

 ![Eszköz sikeresen konfigurálva][img-deviceconfigured]
   
   > [!IMPORTANT]
   > Nincs késleltetést legfeljebb egy percet, az eszköz jelentés művelet és a lekérdezési eredmények között. Ez a nagyon nagy méretekben működéséhez a lekérdezés infrastruktúra engedélyezéséhez. Egy egyetlen eszközt iker használati konzisztens nézetek beolvasása a **getDeviceTwin** metódust a **beállításjegyzék** osztály.
   > 
   > 

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy szabványoskonfiguráció mint beállítása *szükséges tulajdonságok* a megoldásban való háttér, és egy eszköz alkalmazás észleli a változást, és egy többlépéses frissítési folyamat állapotának jelentett tulajdonságai reporting szimulálása megírt.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* ütemezett vagy műveleteket hajtson végre a nagy mennyiségű eszközök lásd: a [ütemezés és a szórásos feladatok] [ lnk-schedule-jobs] oktatóanyag.
* az interaktív (például bekapcsolásával a felhasználó által felügyelt alkalmazásból ventilátor), eszközök szabályozásának a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createnetapp.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-how-to-configure/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-how-to-configure/devicesdknuget.png
[img-deviceconfigured]: media/iot-hub-csharp-csharp-twin-how-to-configure/deviceconfigured.png


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0/

[lnk-query]: iot-hub-devguide-query-language.md
[lnk-twin-notifications]: iot-hub-devguide-device-twins.md#back-end-operations
[lnk-twin-tutorial]: iot-hub-csharp-csharp-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-node-node-schedule-jobs.md
[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-how-to-configure-createapp]: iot-hub-csharp-csharp-twin-how-to-configure.md#create-the-simulated-device-app
