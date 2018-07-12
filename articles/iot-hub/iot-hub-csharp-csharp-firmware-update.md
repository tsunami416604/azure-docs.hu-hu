---
title: Eszköz belső vezérlőprogramjának frissítése az Azure IoT Hub (.NET/.NET) |} A Microsoft Docs
description: Hogyan lehet Azure IoT Hub eszközfelügyeleti használatával kezdeményezheti az eszköz belső vezérlőprogram frissítése. Az Azure IoT eszközoldali SDK for .NET használatával egy szimulált eszközalkalmazás és az Azure IoT service SDK for .NET-service-alkalmazás, amely elindítja a belső vezérlőprogram-frissítés megvalósítása valósítanak meg.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 10/19/2017
ms.author: dobett
ms.openlocfilehash: 1bf7a647ab2fdc175231133b0cfd8abdd51b6d43
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723930"
---
# <a name="use-device-management-to-initiate-a-device-firmware-update-netnet"></a>Kezdeményezheti az eszköz belső vezérlőprogram frissítése (.NET/.NET) device management használatával
[!INCLUDE [iot-hub-selector-firmware-update](../../includes/iot-hub-selector-firmware-update.md)]

## <a name="introduction"></a>Bevezetés
Az a [Eszközfelügyelet – első lépések] [ lnk-dm-getstarted] oktatóanyag bemutatta, hogyan használható a [ikereszköz] [ lnk-devtwin] és [közvetlen metódusok ] [ lnk-c2dmethod] primitívek távolról újraindítja az eszközt. Ebben az oktatóanyagban használja az ugyanazon az IoT Hub primitívek, és bemutatja, hogyan teheti egy teljes körű szimulált belsővezérlőprogram-frissítést.  Ez a minta a belső vezérlőprogram frissítési megvalósítása szerepel a [Raspberry Pi eszköz megvalósítási minta][lnk-rpi-implementation].

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Ez az oktatóanyag a következőket mutatja be:

* Hozzon létre egy .NET-konzolalkalmazást, amely meghívja a **firmwareUpdate** a közvetlen metódus a szimulált eszközalkalmazásnak, az IoT hub segítségével.
* Egy szimulált eszközalkalmazás létrehozása, amely valósít meg egy **firmwareUpdate** közvetlen metódust. Ez a módszer folyamatot kezdeményez, többlépcsős, a belső vezérlőprogram rendszerképének letöltésére vár, a belső vezérlőprogram rendszerképének letöltése és végül alkalmazza a belsővezérlőprogram-lemezképet. A frissítés minden egyes fázisa során az eszköz jelentett tulajdonságait felhasználva jelenti az állapotot.

Ez az oktatóanyag végén rendelkezésére a .NET (C#) Konzolalkalmazás-eszköz és egy .NET (C#) háttér-konzolalkalmazást:

* **SimulatedDeviceFwUpdate**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással kap a **firmwareUpdate** közvetlen metódus, szimulálja a belső vezérlőprogram frissítése több államot folyamat fut például: Várakozás a lemezkép letöltése a, az új lemezkép letöltése, és végül a lemezkép alkalmazása.

* **TriggerFWUpdate**, amely használatával a Szolgáltatásoldali SDK-val távolról meghívása a **firmwareUpdate** közvetlen metódus a szimulált eszközt, megjeleníti a választ, és rendszeres időközönként (minden 500ms) jeleníti meg a frissített jelentett tulajdonságok.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

Kövesse a [Eszközfelügyelet – első lépések](iot-hub-csharp-csharp-device-management-get-started.md) a cikk az IoT hub létrehozása és az IoT Hub kapcsolati karakterláncának beszerzése.

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Aktiválja az eszközön, a közvetlen metódus használatával távoli belső vezérlőprogram frissítése
Ebben a szakaszban konzolalkalmazást hoz létre egy .NET (C# használatával), amely kezdeményezi egy távoli belső vezérlőprogram frissítése egy eszközön. Az alkalmazás közvetlen metódus kezdeményezni a frissítést, és használja az ikereszköz-lekérdezések rendszeres időközönként beolvasni az aktív belső vezérlőprogram frissítésének állapotát.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **TriggerFWUpdate**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createserviceapp]

2. A Megoldáskezelőben kattintson a jobb gombbal a **TriggerFWUpdate** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp   
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A több helyőrző értékeket cserélje le a hub, az eszköz azonosítója, és az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncára.
   
    ```csharp   
    static RegistryManager registryManager;
    static string connString = "{iot hub connection string}";
    static ServiceClient client;
    static string targetDevice = "{deviceIdForTargetDevice}";
    ```
        
6. Adja hozzá a **Program** osztályhoz a következő metódust. Ez a módszer az ikereszközön frissített állapota minden 500 ezredmásodperc kérdezi le. Ír a konzol csak akkor, ha az állapot ténylegesen változott. Leáll az előfizetésében, további IoT Hub-üzenetek felhasználása elkerülése érdekében ebben a példában a lekérdezési, amikor az eszköz jelenti az állapotot **applyComplete** vagy hiba történt.  
   
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

8. Végül adja hozzá a következő sorokat a **fő** metódust. Ez létrehoz egy beállításjegyzék manager olvasni az ikereszköz az útmutatókkal a lekérdezési feladatot egy munkavégző szálon, és majd elindítják a belső vezérlőprogram frissítését.
   
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

* Hozzon létre egy .NET-konzolalkalmazást, amely a felhő által meghívott közvetlen metódusra válaszol
* A belső vezérlőprogram frissítésének közvetlen módszerrel háttérszolgáltatás által aktivált szimulálása
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és utolsó belsővezérlőprogram-frissítésük időpontjának megállapítására

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **SimulateDeviceFWUpdate**.
   
    ![Új Visual C# Windows klasszikus eszközalkalmazás][img-createdeviceapp]
    
2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateDeviceFWUpdate** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT eszközoldali SDK-t] [ lnk-nuget-client-sdk] NuGet csomagot és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp   
    using Newtonsoft.Json.Linq;
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az eszköz kapcsolati karakterláncának részben megadott a **eszközidentitás létrehozása** szakaszban.
   
    ```csharp   
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. A felhőbe az ikereszköz keresztül állapot jelentése érdekében adja hozzá a következő metódust: 

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

7. Adja hozzá a következő metódust, hogy szimulálja a belső vezérlőprogram rendszerképének letöltése:
        
    ```csharp   
    static async Task<byte[]> simulateDownloadImage(string imageUrl)
    {
        var image = "[fake image data]";

        Console.WriteLine("Downloading image from " + imageUrl);

        await Task.Delay(4000);

        return Encoding.ASCII.GetBytes(image);
            
    }
    ```

8. Adja hozzá a belsővezérlőprogram-lemezkép alkalmazása az eszköz szimulálása a következő metódust:
        
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
 
9.  Adja hozzá a következő metódust szimulálja a belső vezérlőprogram rendszerképének letöltésére vár. Az állapot frissítése **Várakozás** és más belső vezérlőprogram frissítési tulajdonságainak az ikereszköz törölje. Ezek a Tulajdonságok törlődik minden meglévő érték eltávolítása előzetes lemezfirmware-frissítések. Erre akkor szükség, mert a JAVÍTÁSI művelet (különbözeti) és a egy PUT művelet nem (teljes tulajdonságai készletét, amely lecseréli az összes korábbi értéket) küldött jelentett tulajdonságok. Az eszközök általában értesülnek ez elérhető frissítésekről, és egy rendszergazdai házirendnek megfelelően elkezdik letölteni és alkalmazni a frissítést. Ebben a függvényben kell futnia a házirendet engedélyező logikának. 
        
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

10. Adja hozzá a következő metódust a letöltéshez. Frissíti az állapot **letöltése** az ikereszközök használatával meghívja a szimulálás letöltésének módját, és a egy állapotjelentést az **downloadComplete** vagy **downloadFailed** keresztül az ikereszköz függően a letöltési művelet eredményét. 
        
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

11. Adja hozzá a következő metódust a alkalmazni a lemezképet. Frissíti az állapot **alkalmazása** keresztül az ikereszköz hívások szimulálás a alkalmazni képen metódust, és a frissítések állapota **applyComplete** vagy **applyFailed** keresztül a ikereszköz függően az alkalmazási művelet eredményét. 
        
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

12. Adja hozzá a következő metódust a belső vezérlőprogram frissítési műveletet kell letölteni a képet, a lemezkép alkalmazása az eszközön keresztül előkészítése:
        
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

13. Adja hozzá a következő metódust a **updateFirmware** közvetlen metódus a felhőből. Ez az URL-címet a belső vezérlőprogram frissítési kigyűjti az üzenet hasznos adattartalmából, és azt a **doUpdate** feladat a szálkészlet egy másik szál megkezdése. Majd azonnal választ ad vissza a mód a felhőbe.
        
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
> Ez a metódus a szimulált frissítést futtató elindít egy **feladat** és ezután azonnal reagál a metódus meghívása, a szolgáltatás arról értesíti, hogy a belső vezérlőprogram frissítése megkezdődött. A szolgáltatás révén az ikereszköz jelentett tulajdonságait frissítési állapot és -befejezési kapnak. A metódus meghívása a frissítés indításakor, nem pedig a művelet befejezését követően reagálunk mert:
> * Egy valódi frissítési folyamat nagyon valószínű, mint az a metódus hívása időtúllépés hosszabb időt vesz igénybe.
> * Egy valódi frissítési folyamat nagyon valószínű, hogy újra kell indítani, amely akkor indítsa újra a alkalmazáskészítés a **MethodRequest** objektum nem érhető el. (Jelentett tulajdonságok frissítése azonban nem lehetséges a számítógép újraindítása után is.) 

14. Végül adja hozzá a következő kódot a **fő** metódus az IoT hub-kapcsolat megnyitásához, és a metódus figyelő inicializálása:
   
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
1. Futtatás a .NET-eszközalkalmazást **SimulatedDeviceFWUpdate**.  Kattintson a jobb gombbal a **SimulatedDeviceFWUpdate** projektre, válassza **Debug**, majd válassza ki **új példány indítása**. Azt el kell indulnia a metódust hívja az IoT hub figyeli: 

2. Miután az eszköz csatlakoztatva van, és megpróbálkozni, Várakozás futtatása a .NET **TriggerFWUpdate** meghívni a belső vezérlőprogram frissítési metódus a szimulált eszközalkalmazás az alkalmazást. Kattintson a jobb gombbal a **TriggerFWUpdate** projektre, válassza **Debug**, majd válassza ki **új példány indítása**. Megtekintheti az update-ben írt feladatütemezési a **SimulatedDeviceFWUpdate** a jelentett tulajdonságok az eszköz a jelentett konzolt, és a feladatütemezés a **TriggerFWUpdate** konzolon. Vegye figyelembe, hogy a folyamat eltarthat néhány másodpercig. 
   
    ![Szolgáltatás és eszköz alkalmazás futtatása][img-combinedrun]


## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban használt közvetlen metódus aktiválhat egy távoli belső vezérlőprogram frissítése egy eszközön, és hajtsa végre a belső vezérlőprogram frissítésének állapotát a jelentett tulajdonságok segítségével.

Ismerje meg, hogyan bővítheti az IoT-megoldás és az ütemezés metódus meghívja a több eszközre, tekintse meg a [feladatok ütemezése és szórása] [ lnk-tutorial-jobs] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-firmware-update/servicesdknuget.png
[img-clientnuget]: media/iot-hub-csharp-csharp-firmware-update/clientsdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-firmware-update/createserviceapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-firmware-update/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-firmware-update/combinedrun.png

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-csharp-csharp-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-csharp-csharp-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-rpi-implementation]: https://github.com/Azure/azure-iot-sdk-c/tree/master/iothub_client/samples/iothub_client_sample_mqtt_dm/pi_device
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
