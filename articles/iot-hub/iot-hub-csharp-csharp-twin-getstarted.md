---
title: "Ismerkedés az Azure IoT Hub eszköz twins (.NET/.NET) |} Microsoft Docs"
description: "Hogyan használható az Azure IoT Hub eszköz twins címkéket, majd az IoT Hub-lekérdezést. Az Azure IoT-eszközök a .NET SDK használatával valósítja meg a szimulált eszköz alkalmazás és az Azure IoT szolgáltatás SDK for .NET egy szolgáltatás-alkalmazást, amely hozzáadja a címkéket és az IoT Hub-lekérdezés futtatása végrehajtásához."
services: iot-hub
documentationcenter: node
author: dsk-2015
manager: timlt
editor: 
ms.assetid: f7e23b6e-bfde-4fba-a6ec-dbb0f0e005f4
ms.service: iot-hub
ms.devlang: node
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/15/2017
ms.author: dkshir
ms.openlocfilehash: 6073d594117e69676b753a1e3af25fffa3583a2b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="get-started-with-device-twins-netnet"></a>Ismerkedés az eszköz twins (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén a konzol .NET alkalmazások lesz:

* **CreateDeviceIdentity**, ilyenkor létrejön egy eszközidentitás és a biztonsági kulcs a szimulált eszköz alkalmazás csatlakoztatása .NET-alkalmazás.
* **AddTagsAndQuery**, egy háttér-.NET-alkalmazás, amely címkét ad hozzá, és lekérdezi az eszköz twins.
* **ReportConnectivity**, egy eszköz .NET-alkalmazás, amely olyan eszköz, amely kapcsolódik a korábban létrehozott eszközidentitás az IoT hub szimulálja, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k] [ lnk-hub-sdks] használható eszközt és a háttér-alkalmazások az Azure IoT SDK-k információt nyújt.
> 
> 

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

[!INCLUDE [iot-hub-get-started-create-device-identity-portal](../../includes/iot-hub-get-started-create-device-identity-portal.md)]

Ha szeretne létrehozni az eszközidentitást programozott módon helyette, olvassa el a megfelelő részt a [a szimulált eszköz csatlakoztatása az IoT hub .NET használatával] [ lnk-device-identity-csharp] cikk.

## <a name="create-the-service-app"></a>A service-alkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás (használatával C#) a társított eszközök a két hely metaadatok hozzáadó létrehozása **myDeviceId**. Ezután lekérdezi az eszköz twins tárolja az IoT hub, az eszközök az Egyesült Államok, és a gazdarendszerhez a mobilhálózat kapcsolat jelentett kiválasztása.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **AddTagsAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][img-createapp]
1. A Megoldáskezelőben kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][img-servicenuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices;
1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.
   
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
   
    A **RegistryManager** osztály eszköz twins a szolgáltatás együttműködhet szükséges összes módszert mutatja. Az előző kód először inicializálja a **registryManager** objektumot, majd beolvassa az eszköz iker a **myDeviceId**, és végül frissíti a címkék a kívánt helyre információkkal.
   
    Miután frissített, két lekérdezést hajt végre: az első csak az eszköz twins található eszközök kiválasztja a **Redmond43** gépek és a második rendszerint a lekérdezést csak azokat az eszközöket is keresztül mobilhálózati kapcsolódó kiválasztásához.
   
    Vegye figyelembe, hogy az előző kód, amikor létrehozza a **lekérdezés** objektumazonosító, a visszaadott dokumentumok maximális számát határozza meg. A **lekérdezés** objektum tartalmaz egy **HasMoreResults** logikai tulajdonság, amely segítségével meghívni a **GetNextAsTwinAsync** módszerek több alkalommal fordult elő az összes eredmények beolvasásához. A metódus hívása **GetNextAsJson** eredmények, amelyek például nem eszköz twins, összesítési-lekérdezések eredményének érhető el.
1. Végül adja a következő sorokat a **Main** metódushoz:
   
        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **AddTagsAndQuery** projekt **Start**. A megoldás felépítéséhez.
1. Az alkalmazás futtatásához a jobb gombbal a **AddTagsAndQuery** projektet, majd válassza **Debug**, utána pedig **Start új példányt**. Megjelenik az eredmények között egy eszközön a lekérdezés kérni a minden eszköz a mappában lévő **Redmond43** és a lekérdezés, amely korlátozza az eredmények mobilhálózati használó eszközök sem.
   
    ![Lekérdezés eredményei ablakban][img-addtagapp]

A következő szakaszban hozzon létre egy eszköz alkalmazást, amely a kapcsolódási adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása
Ebben a szakaszban egy .NET-Konzolalkalmazás, amely kapcsolódik a hub, létrehozhat **myDeviceId**, majd frissíti a jelentett tulajdonságait, hogy mobilhálózat használata csatlakozik a információkat tartalmazzák.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevet a projektnek **ReportConnectivity**.
   
    ![Új Visual C# klasszikus Windows-eszköz alkalmazás][img-createdeviceapp]
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **ReportConnectivity** projektre, és kattintson a **NuGet-csomagok kezelése...** .
1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [Azure IoT-eszközök SDK] [ lnk-nuget-client-sdk] NuGet csomag és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás][img-clientnuget]
1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
        using Microsoft.Azure.Devices.Client;
        using Microsoft.Azure.Devices.Shared;
        using Newtonsoft.Json;

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz kapcsolati karakterlánccal.
   
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

    A **ügyfél** vezérlőnek az eszköz twins az eszközről interaktív szükséges összes módszert. A fent látható kód inicializálja a **ügyfél** objektumot, és majd beolvassa az eszköz iker a **myDeviceId**.

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

   A fenti frissítések kódot **myDeviceId**által jelentett tulajdonságot, amelyben a kapcsolati információ.

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

1. A Solution Explorerben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **ReportConnectivity** projekt **Start**. A megoldás felépítéséhez.
1. Az alkalmazás futtatásához a jobb gombbal a **ReportConnectivity** projektet, majd válassza **Debug**, utána pedig **Start új példányt**. A kettős adatainak lekérése, és elküldi a kapcsolattípust láthatja a *tulajdonság jelentett*.
   
    ![Eszköz-alkalmazás futtatása jelentés kapcsolatához][img-rundeviceapp]
    
    
1. Most, hogy az eszköz jelentette a kapcsolat adatait, akkor mindkét lekérdezések meg kell jelennie. Futtassa a .NET **AddTagsAndQuery** alkalmazásnak, hogy futtassa újból a lekérdezést. Most **myDeviceId** mindkét lekérdezési eredmények jelenjenek meg.
   
    ![Sikeresen jelentett eszközkapcsolatok][img-tagappsuccess]

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Fel van véve eszköz metaadatait címkék egy háttér-alkalmazásból, és a szimulált eszköz alkalmazásának megírt az eszköz a két jelentés eszköz kapcsolódási adatok. Megtudta, ezt az információt az SQL-szerű IoT Hub lekérdezési nyelv lekérdezése is.

A következő források segítségével megtudhatja, hogyan:

* telemetriai adatokat küldhet az eszközökről a [Ismerkedés az IoT-központ] [ lnk-iothub-getstarted] oktatóanyagban
* eszköz iker kívánt tulajdonságokkal rendelkező eszközök konfigurálása a [használata szükséges eszközök tulajdonságok] [ lnk-twin-how-to-configure] oktatóanyagban
* az interaktív (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) eszközeinek vezérléséhez a [közvetlen módszerekkel] [ lnk-methods-tutorial] oktatóanyag.

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

[lnk-device-identity-csharp]: iot-hub-csharp-csharp-getstarted.md#DeviceIdentity_csharp
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdk-node/blob/master/doc/node-devbox-setup.md

