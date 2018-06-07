---
title: Ismerkedés az Azure IoT Hub kezelés (.NET/.NET) |} Microsoft Docs
description: Hogyan használható az Azure IoT Hub kezelés távoli eszköz újraindítás kezdeményezése. Az Azure IoT-eszközök a .NET SDK használatával valósítja meg a szimulált eszköz alkalmazást, amely közvetlen módszer és a Azure IoT szolgáltatást a service-alkalmazást, amely hívja meg a közvetlen módszer végrehajtásához .NET SDK tartalmazza.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: c1cee32e3ee60ce229308055cca7f0e9832ddc49
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34633825"
---
# <a name="get-started-with-device-management-netnet"></a>Eszközkezelés (.NET/.NET) az első lépései

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure-portál használatával létrehoz egy IoT-központot, és az IoT hub hozzon létre egy eszközidentitás.
* Létrehoz egy szimulált eszköz alkalmazást, amely tartalmazza a közvetlen módszer, amely az eszköz újraindul. Közvetlen módszerek a felhőből hívják.
* A .NET-Konzolalkalmazás, amely behívja az újraindítás közvetlen módszer a szimulált eszköz alkalmazás keresztül az IoT hub létrehozása.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* **SimulateManagedDevice**, amely kapcsolódik az IoT hub korábban létrehozott eszköz identitású kap egy újraindítás közvetlen módszer, szimulálja a fizikai számítógép újraindítása, és jelentést az idő az utolsó újraindítás.
* **TriggerReboot**, amely közvetlen metódus meghívja a szimulált eszköz alkalmazás jeleníti meg a választ, és jeleníti meg a frissített jelentett tulajdonságok.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

<a id="DeviceIdentity_csharp"></a>
[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Az eszközön, a közvetlen módszer használatával távoli újraindítás eseményindító
Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#) közvetlen metódussal eszköz távoli újraindítást kezdeményezett hoz létre. Az alkalmazás számára az eszköz legutóbbi újraindítás eszköz iker lekérdezések használ.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az új megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Nevet a projektnek **TriggerReboot**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createserviceapp]

2. A Megoldáskezelőben kattintson a jobb gombbal a **TriggerReboot** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét "Hozzon létre egy IoT-központot." szakaszban létrehozott központ IoT-központ kapcsolati karakterlánccal 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Adja hozzá a **Program** osztályhoz a következő metódust.  Ezt a kódot az eszköz iker lekérdezi az újraindítás eszköz, és kiírja a jelentésben szereplő tulajdonságok.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Adja hozzá a **Program** osztályhoz a következő metódust.  Ez a kód indít el az újraindítást követően az eszközön, a közvetlen módszer használatával.

        public static async Task StartReboot()
        {
            client = ServiceClient.CreateFromConnectionString(connString);
            CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
            method.ResponseTimeout = TimeSpan.FromSeconds(30);

            CloudToDeviceMethodResult result = await client.InvokeDeviceMethodAsync(targetDevice, method);

            Console.WriteLine("Invoked firmware update on device.");
        }

7. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connString);
        StartReboot().Wait();
        QueryTwinRebootReported().Wait();
        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
        
8. Hozza létre a megoldást.

> [!NOTE]
> Ez az oktatóanyag csak egyetlen lekérdezés jelentett eszköztulajdonságok hajt végre. Az éles kódban ajánlott lekérdezési változások észleléséhez a jelentett tulajdonságai.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz tartalma

* Amely válaszol a felhő által nevű közvetlen metódust a .NET-Konzolalkalmazás létrehozása
* A szimulált eszköz újraindítását eseményindító
* A jelentésben szereplő tulajdonságok használatával engedélyezhető az eszköz iker lekérdezések azonosíthatja azokat az eszközöket, és ha tartanak újraindítása után

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **SimulateManagedDevice**.
   
    ![Új Visual C# klasszikus Windows-eszköz alkalmazás][img-createdeviceapp]
    
2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateManagedDevice** projektre, és kattintson a **NuGet-csomagok kezelése...** .
3. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT-eszközök SDK] [ lnk-nuget-client-sdk] NuGet csomag és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz kapcsolati karakterlánccal.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Adja hozzá a következőt valósítja meg a közvetlen az eszközön:

        static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
        {
            // In a production device, you would trigger a reboot scheduled to start after this method returns
            // For this sample, we simulate the reboot by writing to the console and updating the reported properties 
            try
            {
                Console.WriteLine("Rebooting!");

                // Update device twin with reboot time. 
                TwinCollection reportedProperties, reboot, lastReboot;
                lastReboot = new TwinCollection();
                reboot = new TwinCollection();
                reportedProperties = new TwinCollection();
                lastReboot["lastReboot"] = DateTime.Now;
                reboot["reboot"] = lastReboot;
                reportedProperties["iothubDM"] = reboot;
                Client.UpdateReportedPropertiesAsync(reportedProperties).Wait();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }

            string result = "'Reboot started.'";
            return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
        }

7. Végül adja hozzá az alábbi kódot a **fő** módszert, nyissa meg a kapcsolatot az IoT hub és a metódus figyelő inicializálása:
   
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);

            // setup callback for "reboot" method
            Client.SetMethodHandlerAsync("reboot", onReboot, null).Wait();
            Console.WriteLine("Waiting for reboot method\n Press enter to exit.");
            Console.ReadLine();

            Console.WriteLine("Exiting...");

            // as a good practice, remove the "reboot" handler
            Client.SetMethodHandlerAsync("reboot", null, null).Wait();
            Client.CloseAsync().Wait();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
        
8. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd kattintson a **Kezdőprojektek beállítása** parancsra. Válassza ki **egyetlen kezdőprojekt**, majd válassza ki a **SimulateManagedDevice** projekt legördülő menüből. Hozza létre a megoldást.       

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.
1. A .NET-eszköz alkalmazás futtatása **SimulateManagedDevice**.  Kattintson a jobb gombbal a **SimulateManagedDevice** projektre, válassza **Debug**, majd válassza ki **Start új példány**. Akkor kell megkezdeni a figyelést az IoT Hub a metódushívások: 

2. Most, hogy az eszköz csatlakoztatva van, és várakozik a metódus meghívásához, futtassa a .NET **TriggerReboot** alkalmazásnak, hogy a rendszer újraindítása metódus a szimulált eszköz alkalmazás indításához. Kattintson a jobb gombbal a **TriggerReboot** projektre, válassza **Debug**, majd válassza ki **Start új példány**. Megtekintheti az "Rebooting!" Az oktatóprogram a **SimulatedManagedDevice** konzol és az eszköz a jelentett tulajdonságaiban, amely az utolsó újraindítás időpontja, írt a **TriggerReboot** konzol.
   
    ![Szolgáltatás és az eszköz alkalmazás futtatása][img-combinedrun]

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]

<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png
[img-servicenuget]: media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png
[img-createserviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png
[img-combinedrun]: media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
