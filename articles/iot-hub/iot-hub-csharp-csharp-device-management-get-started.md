---
title: Az Azure IoT Hub-Eszközfelügyelet (.NET/.NET) – első lépések |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub-Eszközfelügyelet egy távoli eszköz-újraindítás kezdeményezése. Az Azure IoT eszközoldali SDK for .NET segítségével megvalósítani egy szimulált eszközalkalmazás, amely tartalmazza a közvetlen metódus és az Azure IoT szolgáltatás SDK for .NET megvalósítása a service-alkalmazás, amely a közvetlen metódust hív meg.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: dobett
ms.openlocfilehash: 28078d1917126af0281dacf263dbc92c16111200
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42057553"
---
# <a name="get-started-with-device-management-netnet"></a>Ismerkedés az eszközfelügyelettel (.NET/.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure portal használatával hozzon létre egy IoT hubot, és hozzon létre egy új eszközidentitást az IoT hubban.
* Egy szimulált eszközalkalmazás létrehozása, amely közvetlen metódus, amely az eszköz újraindul. Közvetlen metódusok a felhő kerül meghívásra.
* Hozzon létre egy .NET-konzolalkalmazást, amely meghívja ezt az újraindítást közvetlen metódus a szimulált eszközalkalmazásnak, az IoT hub segítségével a.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* **SimulateManagedDevice**, csatlakozik az IoT hubhoz a korábban létrehozott eszközidentitással újraindítás közvetlen metódus kap, szimulálja a fizikai számítógép újraindítása és az utolsó újraindítás időpontja jelenti.
* **TriggerReboot**, közvetlen metódus a szimulált eszközalkalmazásnak, amely hívások jeleníti meg a választ, és megjeleníti a frissített jelentett tulajdonságokként.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>A távoli közvetlen metódus az eszközön újra kell indítani az eseményindító
Ebben a szakaszban konzolalkalmazást hoz létre egy .NET (C# használatával), amely közvetlen metódus használó eszközök távoli újraindítást kezdeményez. Az alkalmazás számára az eszköz legutóbbi újraindítás ikereszköz-lekérdezések használja.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az új megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek **TriggerReboot**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createserviceapp]

2. A Megoldáskezelőben kattintson a jobb gombbal a **TriggerReboot** projektre, és kattintson a **NuGet-csomagok kezelése**.
3. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Shared;
        
5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le a hub, az "IoT hub létrehozása." szakaszban létrehozott IoT Hub kapcsolati karakterláncára 
   
        static RegistryManager registryManager;
        static string connString = "{iot hub connection string}";
        static ServiceClient client;
        static string targetDevice = "myDeviceId";
        
6. Adja hozzá a **Program** osztályhoz a következő metódust.  Ez a kód beolvassa az éppen újraindul eszköz az ikereszköz, és kiírja a jelentett tulajdonságokat.
   
        public static async Task QueryTwinRebootReported()
        {
            Twin twin = await registryManager.GetTwinAsync(targetDevice);
            Console.WriteLine(twin.Properties.Reported.ToJson());
        }
        
7. Adja hozzá a **Program** osztályhoz a következő metódust.  Ez a kód közvetlen metódus az eszközön az újraindítás kezdeményezése.

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
> Ebben az oktatóanyagban az eszköz a jelentett tulajdonságokat csak egyetlen lekérdezést hajt végre. Az éles kódban javasoljuk, hogy lekérdezési változások észleléséhez a jelentett tulajdonságokat.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása
Ez a szakasz tartalma

* Hozzon létre egy .NET-konzolalkalmazást, amely a felhő által meghívott közvetlen metódusra válaszol
* Az eseményindító egy szimulált eszköz-újraindítás
* A jelentett tulajdonságok használatával ikereszköz-lekérdezéseket engedélyez az eszközök azonosítására és ha azok utolsó újraindítása

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **SimulateManagedDevice**.
   
    ![Új Visual C# Windows klasszikus eszközalkalmazás][img-createdeviceapp]
    
2. A Megoldáskezelőben kattintson a jobb gombbal a **SimulateManagedDevice** projektre, és kattintson a **NuGet-csomagok kezelése...** .
3. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT eszközoldali SDK-t] [ lnk-nuget-client-sdk] NuGet csomagot és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az eszközt az előző szakaszban feljegyzett kapcsolati karakterlánccal.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

6. Adja hozzá a következő, a közvetlen metódus megvalósításához az eszközön:

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

7. Végül adja hozzá a következő kódot a **fő** metódus az IoT hub-kapcsolat megnyitásához, és a metódus figyelő inicializálása:
   
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
        
8. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd kattintson a **Kezdőprojektek beállítása** parancsra. Válassza ki **egyetlen kezdőprojekt**, majd válassza ki a **SimulateManagedDevice** projekt a legördülő menüből. Hozza létre a megoldást.       

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket is meg kell valósítania (például egy exponenciális leállítást) a [tranziens hibakezelést][lnk-transient-faults] ismertető MSDN-cikkben leírtak szerint.


## <a name="run-the-apps"></a>Az alkalmazások futtatása
Most már készen áll az alkalmazások futtatására.
1. Futtatás a .NET-eszközalkalmazást **SimulateManagedDevice**.  Kattintson a jobb gombbal a **SimulateManagedDevice** projektre, válassza **Debug**, majd válassza ki **új példány indítása**. Azt el kell indulnia a metódust hívja az IoT hub figyeli: 

2. Most, hogy az eszköz csatlakoztatva van, és megpróbálkozni, Várakozás futtatása a .NET **TriggerReboot** alkalmazás a szimulált eszközalkalmazás újraindítás metódus meghívásához. Kattintson a jobb gombbal a **TriggerReboot** projektre, válassza **Debug**, majd válassza ki **új példány indítása**. Megtekintheti az "Újraindítás!" írt a **SimulatedManagedDevice** konzol és az eszköz a jelentett tulajdonságok, többek között az utolsó újraindítás időpontja, nyelven írt a **TriggerReboot** konzolon.
   
    ![Szolgáltatás és eszköz alkalmazás futtatása][img-combinedrun]

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
