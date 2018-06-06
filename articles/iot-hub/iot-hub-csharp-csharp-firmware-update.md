---
title: Eszköz belső vezérlőprogram frissítési Azure IoT Hub (.NET/.NET) |} Microsoft Docs
description: Hogyan használható az eszközkezelés Azure IoT hub eszköz vezérlőprogram-frissítés kezdeményezése. Az Azure IoT-eszközök a .NET SDK használatával valósítja meg a szimulált eszköz alkalmazások és az Azure IoT szolgáltatás SDK for .NET egy service-alkalmazást, amely elindítja a belső vezérlőprogram-frissítés végrehajtásához.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: cd669a9585ac5aecf935202a04065a828a2174be
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34736755"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Egy eszköz belső vezérlőprogram-frissítés (.NET/.NET) kezdeményezésére történő Eszközkezelés használata
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Bevezetés
Az a [Ismerkedés az eszközkezelés] [ lnk-dm-getstarted] oktatóanyag, megtudhatta, hogyan használható a [eszköz iker] [ lnk-devtwin] és [közvetlen módszerek ] [ lnk-c2dmethod] primitívek távolról az eszköz újraindítását. Ez az oktatóanyag az ugyanazon az IoT-központ primitívek használ, és bemutatja, hogyan hajtsa végre egy végpontok közötti szimulált belső vezérlőprogram-frissítés.  A belső vezérlőprogram frissítési implementációja szerepel ebben a mintában a [málna Pi eszköz megvalósítási minta][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy .NET-Konzolalkalmazás, amely behívja a **firmwareUpdate** közvetlen módszer a szimulált eszköz alkalmazásban az IoT hub keresztül.
* Hozzon létre egy szimulált eszköz alkalmazást, amely egy **firmwareUpdate** közvetlen módszer. Ez a módszer indít el egy többlépcsős folyamat, amely megvárja-e a belső vezérlőprogram lemezképet letölti, letölti a belső vezérlőprogram lemezképet és a belső vezérlőprogram kép végül vonatkozik. A frissítés egyes szakasza alatt az eszköz használatával a jelentésben szereplő tulajdonságok előrehaladásról.

Ez az oktatóanyag végén akkor egy .NET (C#) Konzolalkalmazás eszköz és a .NET (C#) háttér-Konzolalkalmazás:

* **SimulatedDeviceFwUpdate**, amely csatlakozik az IoT hub, korábban létrehozott eszköz identitású megkapja a **firmwareUpdate** közvetlen módszer, több államot folyamatot, ezzel szimulálva a belső vezérlőprogram-frissítés futtatásakor például: Várakozás a lemezkép letöltése a, az új lemezkép letöltése, és végül a kép alkalmazása.

* **TriggerFWUpdate**, az SDK szolgáltatás használó távolról meghívni a **firmwareUpdate** közvetlen módszer a szimulált eszköz jeleníti meg a választ, és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelentett tulajdonságok.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Kövesse a [Ismerkedés az eszközkezelés](iot-hub-csharp-csharp-device-management-get-started.md) cikk az IoT hub létrehozása, és az IoT-központ kapcsolati karakterláncot.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Indítás, az eszközön, a közvetlen módszer használatával távoli vezérlőprogram-frissítés
Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#), amely indít el egy távoli belső vezérlőprogram frissítése egy eszközön hoz létre. Az alkalmazás közvetlen módszer előtt használja a frissítés, és az eszköz iker lekérdezések rendszeres időközönként a aktív belső vezérlőprogram frissítése a állapot lekérdezése céljából.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **TriggerFWUpdate**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createserviceapp]

2. A Megoldáskezelőben kattintson a jobb gombbal a **TriggerFWUpdate** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a több helyőrző értékeket az előző szakaszban és az eszköz azonosítója a központ IoT-központ kapcsolati karakterlánccal.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Adja hozzá a **Program** osztályhoz a következő metódust. Ez a módszer kérdezze le a frissített állapota az eszköz a két minden 500 ezredmásodpercben. Ír a konzol csak akkor, ha az állapot ténylegesen változott. Leállítja az ebben a példában az előfizetéséhez, a felesleges IoT-központ üzenetek fel megelőzése érdekében lekérdezési, amikor az eszköz jelzi a **applyComplete** vagy hiba történt.  
   
    ```csharp   
    public static async Task QueryTwinFWUpdateReported(DateTime startTime)
    {
        DateTime lastUpdated = startTime;

        while (true)
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);

            if (twin.Properties.Reported.GetLastUpdated().ToUniversalTime() > lastUpdated.ToUniversalTime())
            {
                lastUpdated = twin.Properties.Reported.GetLastUpdated().ToUniversalTime();
                Console.WriteLine("\n" + twin.Properties.Reported["iothubDM"].ToJson());

                var status = twin.Properties.Reported["iothubDM"]["firmwareUpdate"]["status"].Value;
                if ((status == "downloadFailed") || (status == "applyFailed") || (status == "applyComplete"))
                {
                    Console.WriteLine("\nStop polling.");
                    return;
                }
            }
            await Task.Delay(500);
        }
    }
    ```
        
7. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp   
    public static async Task StartFirmwareUpdate()
    {
        client = ServiceClient.CreateFromConnectionString(connString);
        CloudToDeviceMethod method = new CloudToDeviceMethod("firmwareUpdate");
        method.ResponseTimeout = TimeSpan.FromSeconds(30);
        method.SetPayloadJson(
            @"{
               fwPackageUri : 'https://someurl'
            }");

        CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

        Console.WriteLine("Invoked firmware update on device.");
    }
    ```

8. Végül adja hozzá a következő sorokat a **fő** metódust. Létrehoz egy beállításjegyzék olvassa el az eszköz iker rendelkező, elindul a lekérdezési feladat egy munkavégző szál-kezelőt, és ezután elindítja a frissítést.
   
    ```csharp   
    registryManager = RegistryManager.CreateFromConnectionString(connString);

    Task queryTask = Task.Run(() => (QueryTwinFWUpdateReported(DateTime.Now)));

    StartFirmwareUpdate().Wait();
    Console.WriteLine("Press ENTER to exit.");
    Console.ReadLine();
    ```
        
9. Hozza létre a megoldást.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ebben a szakaszban:

* Amely válaszol a felhő által nevű közvetlen metódust a .NET-Konzolalkalmazás létrehozása
* Közvetlen módszerrel háttérszolgáltatás által indított vezérlőprogram-frissítés szimulálása
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és utolsó belsővezérlőprogram-frissítésük időpontjának megállapítására

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **SimulateDeviceFWUpdate**.
   
    ![Új Visual C# klasszikus Windows-eszköz alkalmazás][img-createdeviceapp]
    
2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceFWUpdate** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT-eszközök SDK] [ lnk-nuget-client-sdk] NuGet csomag és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét a eszköz kapcsolati karakterlánccal a **hozzon létre egy eszközidentitás** szakasz.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Vissza a felhőben, az eszköz iker az állapot jelentése érdekében adja hozzá a következő metódust: 

    ```csharp   
    static async Task reportFwUpdateThroughTwin(Twin twin, TwinCollection fwUpdateValue)
    {
        try
        {
            TwinCollection patch = new TwinCollection();
            TwinCollection iothubDM = new TwinCollection();

            iothubDM["firmwareUpdate"] = fwUpdateValue;
            patch["iothubDM"] = iothubDM;

            await Client.UpdateReportedPropertiesAsync(patch);
            Console.WriteLine("Twin state reported: {0}", fwUpdateValue["status"]);
        }
        catch 
        {
            Console.WriteLine("Error updating device twin");
            throw;
        }
    }
    ```

7. Adja hozzá a következő metódust, ezzel szimulálva a belső vezérlőprogram lemezkép letöltése:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Adja hozzá a következő metódust, ezzel szimulálva a belső vezérlőprogram kép alkalmazása az eszközre:
        
    ```csharp   
    static async Task simulateApplyImage(byte[] imageData)
    {
        if (imageData == null)
        {
            throw new ArgumentNullException();
        }

        await Task.Delay(4000);

    }
    ```
 
9.  Adja hozzá a következő metódust kell letölteni a belső vezérlőprogram kép szimulálásához. Az állapot frissítése **Várakozás** , és törölje a kettős más belső vezérlőprogram frissítési tulajdonságaiból. Ezeket a tulajdonságokat a meglévő értékeket eltávolítása előzetes belső vezérlőprogram-frissítésekre adatútvonalai törlődtek. Ez azért szükséges, mert a jelentett tulajdonságai, a JAVÍTÁSI művelet (különbözeti), és nem PUT művelet (a teljes adatkészletet tulajdonságait, amely a felváltja a korábbi értékei) küldött. Az eszközök általában értesülnek ez elérhető frissítésekről, és egy rendszergazdai házirendnek megfelelően elkezdik letölteni és alkalmazni a frissítést. Ebben a függvényben kell futnia a házirendet engedélyező logikának. 
        
    ```csharp   
    static async Task waitToDownload(Twin twin, string fwUpdateUri)
    {
        var now = DateTime.Now;
        TwinCollection status = new TwinCollection();
        status["fwPackageUri"] = fwUpdateUri;
        status["status"] = "waiting";
        status["error"] = null;
        status["startedWaitingTime"] = DateTime.Now;
        status["downloadCompleteTime"] = null;
        status["startedApplyingImage"] = null;
        status["lastFirmwareUpdate"] = null;

        await reportFwUpdateThroughTwin(twin, status);

        await Task.Delay(2000);
    }
    ```

10. Adja hozzá a következő metódust a letöltéshez. Az állapot frissíti **letöltése** keresztül eszköz iker meghívja a szimulálás letöltési metódust, és jelzi a **downloadComplete** vagy **downloadFailed** – a kettős attól függően, hogy a letöltési művelet eredménye. 
        
    ```csharp   
    static async Task<byte[]> downloadImage(Twin twin, string fwUpdateUri)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloading";
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            byte[] imageData = await simulateDownloadImage(fwUpdateUri);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadComplete";
            statusUpdate["downloadCompleteTime"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            return imageData;
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "downloadFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

11. Adja hozzá a következő metódust alkalmazza a lemezképet. Az állapot frissíti **alkalmazása** eszköz iker keresztül alkalmazza a szimulálás hívások kép metódust, és a frissítések állapotának **applyComplete** vagy **applyFailed** keresztül a a két attól függően, hogy az alkalmazási művelet eredményét. 
        
    ```csharp   
    static async Task applyImage(Twin twin, byte[] imageData)
    {
        try
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applying";
            statusUpdate["startedApplyingImage"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);

            await simulateApplyImage(imageData);

            statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyComplete";
            statusUpdate["lastFirmwareUpdate"] = DateTime.Now;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
        }
        catch (Exception ex)
        {
            TwinCollection statusUpdate = new TwinCollection();
            statusUpdate["status"] = "applyFailed";
            statusUpdate["error"] = new TwinCollection();
            statusUpdate["error"]["code"] = ex.GetType().ToString();
            statusUpdate["error"]["message"] = ex.Message;
            await reportFwUpdateThroughTwin(twin, statusUpdate);
            throw;
        }
    }
    ```

12. Adja hozzá a következő metódust letölti a lemezképet, a lemezkép alkalmazása az eszközre keresztül arra a belső vezérlőprogram frissítési művelet előkészítése:
        
    ```csharp   
    static async Task doUpdate(string fwUpdateUrl)
    {
        try
        {
            Twin twin = await Client.GetTwinAsync();
            await waitToDownload(twin, fwUpdateUrl);
            byte[] imageData = await downloadImage(twin, fwUpdateUrl);
            await applyImage(twin, imageData);
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error during update: {0}", ex.Message);
        }
    }
    ```

13. Adja hozzá a következő metódust a **updateFirmware** közvetlen módszer a felhőből. Azt az URL-cím kibontja a belső vezérlőprogram-frissítés a üzenetadatokat, és úgy, hogy átadja a **doUpdate** feladat, amely a szálkészlet egy másik szál megkezdése. Majd azonnal választ ad vissza a módszer a felhőbe.
        
    ```csharp   
    static Task<MethodResponse> onFirmwareUpdate(MethodRequest methodRequest, object userContext)
    {
        string fwUpdateUrl = (string)JObject.Parse(methodRequest.DataAsJson)["fwPackageUri"];
        Console.WriteLine("\nMethod: {0} triggered by service, URI is: {1}", methodRequest.Name, fwUpdateUrl);

        Task updateTask = Task.Run(() => (doUpdate(fwUpdateUrl)));

        string result = "'FirmwareUpdate started.'";
        return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
    }
    ```
> [!NOTE]
> Ez a módszer elindítja a szimulált frissítés rendszergazdaként futtatni a **feladat** és majd azonnal válaszol-e a metódus hívása, tájékoztatása a szolgáltatást, hogy elindult-e a frissítést. Az eszköz iker jelentett tulajdonságai a szolgáltatás frissítési állapot és a befejezési kapnak. Ha a frissítés elkezdése metódus hívásának, nem pedig a művelet befejezését követően válaszolunk mert:
> * A tényleges frissítési folyamat várhatóan nagyon tovább tart, mint a metódus hívása időtúllépés.
> * A tényleges frissítési folyamat nagyon valószínű, hogy újra kell indítani, amelyek volna el az alkalmazás elvégzése a **MethodRequest** objektum nem érhető el. (Jelentett tulajdonságainak frissítése azonban nem lehetséges újraindítása után is.) 

14. Végül adja hozzá az alábbi kódot a **fő** módszert, nyissa meg a kapcsolatot az IoT hub és a metódus figyelő inicializálása:
   
    ```csharp   
    try
    {
        Console.WriteLine("Connecting to hub");
        Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
        
        // setup callback for "firmware update" method
        Client.SetMethodHandlerAsync("firmwareUpdate", onFirmwareUpdate, null).Wait();
        Console.WriteLine("Waiting for firmware update direct method call\n Press enter to exit.");
        Console.ReadLine();

        Console.WriteLine("Exiting...");

        // as a good practice, remove the firmware update handler
        Client.SetMethodHandlerAsync("firmwareUpdate", null, null).Wait();
        Client.CloseAsync().Wait();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
15. Hozza létre a megoldást.       

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.
1. A .NET-eszköz alkalmazás futtatása **SimulatedDeviceFWUpdate**.  Kattintson a jobb gombbal a **SimulatedDeviceFWUpdate** projektre, válassza **Debug**, majd válassza ki **Start új példány**. Akkor kell megkezdeni a figyelést az IoT Hub a metódushívások: 

2. Ha az eszköz csatlakoztatva van, és várakozik a metódus meghívásához, futtassa a .NET **TriggerFWUpdate** alkalmazásnak, hogy a belső vezérlőprogram frissítési módszer a szimulált eszköz alkalmazás meghívni. Kattintson a jobb gombbal a **TriggerFWUpdate** projektre, válassza **Debug**, majd válassza ki **Start új példány**. Megtekintheti az írt feladatütemezési frissítése a **SimulatedDeviceFWUpdate** konzol, valamint a feladatütemezési jelentett az eszköz a jelentett tulajdonságai a **TriggerFWUpdate** konzol. Vegye figyelembe, hogy a a folyamat befejezéséhez néhány másodpercig tart. 
   
    ![Szolgáltatás és az eszköz alkalmazás futtatása][img-combinedrun]


## <a name="next-steps"></a>További lépések
Ez az oktatóanyag elindítása egy távoli belső vezérlőprogram frissítése egy eszközön a közvetlen módszer használatával, és a jelentésben szereplő tulajdonságok segítségével nyomon követheti a belső vezérlőprogram frissítése.

Megtudhatja, hogyan terjeszthető ki az IoT-megoldás és az ütemezések metódushívások több eszközön, tekintse meg a [ütemezés és a szórásos feladatok] [ lnk-tutorial-jobs] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-node-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
