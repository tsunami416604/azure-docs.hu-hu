---
title: Ismerkedés az Azure IoT Hub device twins (.NET/.NET) |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub device twins címkéket adhat hozzá, majd az IoT Hub-lekérdezést. A szimulált eszközalkalmazás és az Azure IoT szolgáltatás SDK for .NET megvalósítása, amely hozzáadja a címkék és az IoT Hub-lekérdezést futtathat egy service-alkalmazás üzembe helyezéséhez használhatja az Azure IoT eszközoldali SDK a .NET-hez.
author: dsk-2015
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 5048d5a04b5185efb97320d9f2227f78398e7713
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185101"
---
# <a name="get-started-with-device-twins-netnet"></a>Első lépések az ikereszközökhöz (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén a .NET-konzolalkalmazással fog rendelkezni:

* **CreateDeviceIdentity**, a .NET-alkalmazás, amely egy eszközidentitást, valamint a szimulált eszközalkalmazás kapcsolódni a társított biztonsági kulcsot hoz létre.
* **AddTagsAndQuery**, amely címkét ad hozzá, és lekérdezi az ikereszközök .NET-háttérrendszer alkalmazás.
* **ReportConnectivity**, a .NET-eszközalkalmazást, amely szimulálja a olyan eszköz, amely az IoT hubhoz a korábban létrehozott eszközidentitással, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszköz és a háttér-alkalmazásokat hozhat létre az Azure IoT SDK-kkal kapcsolatos információkat biztosít.
> 
> 

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

## <a name="create-the-service-app"></a>Az alkalmazás létrehozása
Ebben a szakaszban konzolalkalmazást hoz létre egy .NET (C# használatával), amely a hely metaadatokat ad hozzá az ikereszköz társított **myDeviceId**. Ezután lekérdezi az ikereszközök, az IoT hub kiválasztása az eszközök, az Egyesült Államok, és amelyekre a mobilhálózati kapcsolat jelentett tárolja.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **AddTagsAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]
1. A Megoldáskezelőben kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **microsoft.azure.devices**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati sztringre.
   
        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";
1. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);
   
            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }
   
    A **RegistryManager** osztály tesz közzé az ikereszközökhöz, a szolgáltatás használatához szükséges összes módszert. Az előző kód először inicializálja a **registryManager** objektumot, majd lekéri az ikereszközön **myDeviceId**, és végül frissíti a címkéket a kívánt helyre adatokkal.
   
    Miután frissített, két lekérdezést hajt végre: az első kiválasztja a csak az ikereszközök található eszközök a **Redmond43** gépek és a második megjeleníthető a lekérdezést, válassza ki a keresztül mobilhálózati is csatlakozó eszközöket.
   
    Vegye figyelembe, hogy az előző kód, amikor létrehozza a **lekérdezés** objektumazonosító, a visszaadott dokumentumok maximális számát határozza meg. A **lekérdezés** az objektum tartalmaz egy **hasmoreresults használatával** logikai tulajdonság, amely segítségével meghívása a **GetNextAsTwinAsync** módszerek többször az összes eredmény beolvasása. A metódus hívása **GetNextAsJson** eredményeket nem ikereszközök, például összesítési lekérdezések eredményeit érhető el.
1. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. A megoldáskezelőben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **AddTagsAndQuery** projekt **Start**. Hozza létre a megoldást.
1. Az alkalmazás futtatásához kattintson a jobb gombbal a a **AddTagsAndQuery** projektet és kijelölése **Debug**, utána pedig **új példány indítása**. Megjelenik a lekérdezés feltevéséhez az eredmények között egy eszközön található összes eszköz **Redmond43** sem a lekérdezést, amely korlátozza az eredményeket a mobilhálózati használó eszközöket.
   
    ![Lekérdezési eredmények ablak][img-addtagapp]

A következő szakaszban, hogy egy eszközalkalmazás létrehozása, amely jelent a kapcsolati adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása
Ebben a szakaszban egy .NET-konzolalkalmazást, amely csatlakozik a hubhoz, létrehozhat **myDeviceId**, majd frissíti a jelentett tulajdonságok alapján, hogy csatlakoztatva van mobilhálózat használata adatokat tartalmazzák.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **ReportConnectivity**.
   
    ![Új Visual C# Windows klasszikus eszközalkalmazás][img-createdeviceapp]
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **ReportConnectivity** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT eszközoldali SDK-t] [ lnk-nuget-client-sdk] NuGet csomagot és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az eszközt az előző szakaszban feljegyzett kapcsolati karakterlánccal.
   
        static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
        static DeviceClient Client = null;

1. Adja hozzá a **Program** osztályhoz a következő módszert:

       public static async void InitClient()
        {
            try
            {
                Console.WriteLine("Connecting to hub");
                Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
                Console.WriteLine("Retrieving twin");
                await Client.GetTwinAsync();
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

    A **ügyfél** vezérlőnek az ikereszközökhöz az eszközről való kommunikációhoz szükséges összes módszert. A fent látható kód inicializálja a **ügyfél** objektumot, és ezután lekéri az ikereszközön **myDeviceId**.

1. Adja hozzá a **Program** osztályhoz a következő módszert:
   
        public static async void ReportConnectivity()
        {
            try
            {
                Console.WriteLine("Sending connectivity data as reported property");
                
                TwinCollection reportedProperties, connectivity;
                reportedProperties = new TwinCollection();
                connectivity = new TwinCollection();
                connectivity["type"] = "cellular";
                reportedProperties["connectivity"] = connectivity;
                await Client.UpdateReportedPropertiesAsync(reportedProperties);
            }
            catch (Exception ex)
            {
                Console.WriteLine();
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
        }

   A kód felett frissítések **myDeviceId**által jelentett tulajdonságot, amelyben a kapcsolati információkat.

1. Végül adja a következő sorokat a **Main** metódushoz:
   
       try
       {
            InitClient();
            ReportConnectivity();
       }
       catch (Exception ex)
       {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
       }
       Console.WriteLine("Press Enter to exit.");
       Console.ReadLine();

1. A megoldáskezelőben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **ReportConnectivity** projekt **Start**. Hozza létre a megoldást.
1. Az alkalmazás futtatásához kattintson a jobb gombbal a a **ReportConnectivity** projektet és kijelölése **Debug**, utána pedig **új példány indítása**. Meg kell jelennie az ikereszköz-információk lekérése, és a kapcsolat, elküldi egy *jelentett tulajdonság*.
   
    ![A jelentés a kapcsolatot eszközalkalmazás futtatása][img-rundeviceapp]
    
    
1. Most, hogy az eszköz jelenik meg a kapcsolati információkat, akkor meg kell jelennie mindkét lekérdezést. Futtatás a .NET **AddTagsAndQuery** alkalmazásnak, hogy újra futtassa a lekérdezéseket. Ezúttal **myDeviceId** meg kell jelennie mindkét lekérdezés eredményeit.
   
    ![Eszköz csatlakoztatása sikeresen megtörtént a jelzett][img-tagappsuccess]

## <a name="next-steps"></a>További lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Címkeként, egy háttér-alkalmazásból hozzáadott eszközök metaadatait, és a egy szimulált eszközalkalmazás zapsáno do kapcsolat eszközadatokat a jelentés azokat az ikereszköz. Azt is megtanulta, hogyan kérdezhet le ezt az információt az SQL-szerű az IoT Hub lekérdezési nyelv segítségével.

Az alábbi forrásanyagokból megtudhatja, hogyan lehet:

* telemetriát az eszközökről a [IoT Hub használatának első lépései] [ lnk-iothub-getstarted] oktatóanyagban
* konfigurálhatja az eszközöket használó eszköz ikereszköz kívánt tulajdonságait a a [használata kívánt tulajdonságok konfigurálhatja az eszközöket] [ lnk-twin-how-to-configure] oktatóanyagban
* az eszközök, interaktív módon (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) szabályozhatja a [közvetlen metódusok használata] [ lnk-methods-tutorial] oktatóanyag.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png
[img-createdeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png
[img-clientnuget]: media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png
[img-rundeviceapp]: media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png
[img-tagappsuccess]: media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-nuget-client-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/

[lnk-device-identity-csharp]: quickstart-send-telemetry-dotnet.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: quickstart-send-telemetry-dotnet.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-how-to-configure]: tutorial-device-twins.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

