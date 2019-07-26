---
title: Ismerkedés az Azure IoT Hub Device ikrek szolgáltatással (.NET/.NET) | Microsoft Docs
description: Az Azure IoT Hub-eszközök ikrek használata címkék hozzáadásához és IoT Hub-lekérdezés használatához. A .NET-hez készült Azure IoT eszközoldali SDK segítségével megvalósíthatja a szimulált eszköz alkalmazást és a .NET-hez készült Azure IoT Service SDK-t egy olyan szolgáltatásalkalmazás megvalósításához, amely hozzáadja a címkéket és futtatja a IoT Hub lekérdezést.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robinsh
ms.openlocfilehash: c1b8f60fd155cf9bce0b999da7459299b6f3c7aa
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68404527"
---
# <a name="get-started-with-device-twins-netnet"></a>Ismerkedés az eszközökhöz készült ikrekkel (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Az oktatóanyag végén a következő .NET-konzolos alkalmazásokkal fog rendelkezni:

* **CreateDeviceIdentity**, egy .NET-alkalmazás, amely létrehozza az eszköz identitását és a hozzá tartozó biztonsági kulcsot a szimulált eszköz alkalmazás csatlakoztatásához.

* **AddTagsAndQuery**, egy olyan .net háttérbeli alkalmazás, amely címkéket és lekérdezéseket is hozzáad az eszközökhöz.

* **ReportConnectivity**, egy .net-eszköz alkalmazás, amely olyan eszközt szimulál, amely az IoT hubhoz a korábban létrehozott eszköz identitásával csatlakozik, és a kapcsolati feltételét jelzi.

> [!NOTE]
> Az [Azure IoT SDK](iot-hub-devguide-sdks.md) -k cikke olyan Azure IoT SDK-kat tartalmaz, amelyek segítségével mind az eszközök, mind a háttérbeli alkalmazások készíthetők.
> 

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Visual Studio.
* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban létrehoz egy .NET-konzol alkalmazást (a használatával C#), amely a hely metaadatainak hozzáadását teszi elérhetővé a **myDeviceId**társított eszközökhöz. Ezután lekérdezi az IoT hub-ban tárolt, az USA-ban található eszközöket, majd a mobil kapcsolatról jelentést tartalmazó eszközt.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevezze el a projekt **AddTagsAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. Megoldáskezelő kattintson a jobb gombbal a **AddTagsAndQuery** projektre, majd kattintson a **NuGet-csomagok kezelése..** . elemre.

3. A **NuGet csomagkezelő** ablakban válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices**elemet. Válassza a **telepítés** lehetőséget a **Microsoft. Azure. Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti és hozzáadja az [Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagra és annak függőségeire mutató hivatkozást.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le a korábban a [IoT hub](#get-the-iot-hub-connection-string)-beli kapcsolatok karakterláncának lekérése során másolt IoT hubi kapcsolatok karakterlánccá.

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

6. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp  
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
   
        var query = registryManager.CreateQuery(
          "SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
        var twinsInRedmond43 = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43: {0}", 
          string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));
   
        query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
        var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
        Console.WriteLine("Devices in Redmond43 using cellular network: {0}", 
          string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
    }
    ```
   
    A **RegistryManager** osztály minden olyan metódust elérhetővé tesz, amely az eszközök ikrek szolgáltatásból való interakcióhoz szükséges. Az előző kód először inicializálja a **registryManager** objektumot, majd lekéri az **myDeviceId**-t, és végül frissíti a címkéket a kívánt hely adataival.
   
    A frissítés után két lekérdezést hajt végre: az első kiválasztja csak a **Redmond43** -üzemben található eszközökből álló ikreket, a második pedig csak azokat az eszközöket jelöli ki, amelyek a mobil hálózaton keresztül is csatlakoztatva vannak.
   
    Vegye figyelembe, hogy az előző kód a lekérdezési  objektum létrehozásakor megadja a visszaadott dokumentumok maximális számát. A **lekérdezési** objektum egy **HasMoreResults** logikai tulajdonságot tartalmaz, amelyet a **GetNextAsTwinAsync** metódusok többszöri meghívására használhat az összes eredmény lekéréséhez. A **GetNextAsJson** nevű metódus olyan eredményekhez érhető el, amelyek nem az eszközök ikrek, például az összesítési lekérdezések eredményei.

7. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. A Megoldáskezelő nyissa meg az **indítási projektek beállítása... beállítást** , és győződjön meg arról, hogy a  **AddTagsAndQuery** projekthez tartozó **művelet** elindult. Hozza létre a megoldást.

9. Az alkalmazás futtatásához kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és válassza a **hibakeresés**lehetőséget, majd az **új példány elindítása**parancsot. A lekérdezés eredményei között egy eszközt kell látnia, amely a **Redmond43** -ban található összes eszközt kéri, a nem pedig a lekérdezést, amely a mobil hálózatot használó eszközökre korlátozza az eredményeket.
   
    ![Lekérdezés eredményei az ablakban](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

A következő szakaszban létrehoz egy olyan eszközt, amely jelentést készít a kapcsolati adatokról, és megváltoztatja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszköz-alkalmazás létrehozása

Ebben a szakaszban egy olyan .NET-konzol alkalmazást hoz létre, amely a **myDeviceId**-hez csatlakozik a központhoz, majd frissíti a jelentett tulajdonságokat, hogy tartalmazza a mobil hálózaton keresztül csatlakoztatott adatokat.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevezze el a projekt **ReportConnectivity**.
   
    ![Új Visual C# Windows klasszikus eszköz alkalmazás](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. Megoldáskezelő kattintson a jobb gombbal a **ReportConnectivity** projektre, majd kattintson a **NuGet-csomagok kezelése..** . elemre.

3. A **NuGet csomagkezelő** ablakban válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices. Client**elemet. Válassza a **telepítés** lehetőséget a **Microsoft. Azure. Devices. Client** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti és hozzáadja az [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagra és annak függőségeire mutató hivatkozást.
   
    ![NuGet csomagkezelő ablak ügyfélalkalmazás](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz-összekapcsolási sztringre.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;
      DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

6. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    public static async void InitClient()
    {
        try
        {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
              TransportType.Mqtt);
            Console.WriteLine("Retrieving twin");
            await Client.GetTwinAsync();
        }
        catch (Exception ex)
        {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
        }
    }
    ```

    Az **ügyfél** -objektum minden olyan módszert feltesz, amely az eszközön található ikrekkel való interakcióhoz szükséges. A fent látható kód inicializálja az **ügyfél** objektumot, majd lekéri a **myDeviceId**-hez tartozó különálló eszközt.

7. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp  
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
    ```

   A fenti kód frissíti a **myDeviceId**jelentett tulajdonságát a kapcsolódási adatokkal.

8. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
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
    ```

9. A Megoldáskezelő nyissa meg az **indítási projektek beállítása... beállítást** , és győződjön meg arról, hogy a  **ReportConnectivity** projekthez tartozó **művelet** elindult. Hozza létre a megoldást.

10. Az alkalmazás futtatásához kattintson a jobb gombbal a **ReportConnectivity** projektre, és válassza a **hibakeresés**lehetőséget, majd az **új példány elindítása**parancsot. Látnia kell, hogy a Twin adatokat kapja meg, majd *jelentett tulajdonságként*küldi el a kapcsolatot.
   
    ![Az eszköz alkalmazásának futtatása a kapcsolat jelentéséhez](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Most, hogy az eszköz bejelentette a kapcsolati adatait, mindkét lekérdezésben szerepelnie kell. Futtassa a .NET **AddTagsAndQuery** alkalmazást a lekérdezések ismételt futtatásához. Ez az idő **myDeviceId** mindkét lekérdezési eredményben szerepelnie kell.
   
    ![Az eszköz kapcsolata sikeresen jelezve](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttérbeli alkalmazáshoz, és írt egy szimulált eszközt, amely az eszköz kapcsolati adatait jelenti a Twin-ben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az adatokat az SQL-Like IoT Hub lekérdezési nyelv használatával.

Az alábbi források segítségével megismerheti a következőket:

* telemetria küldése az eszközökről a [telemetria küldése az eszközről egy IoT hub](quickstart-send-telemetry-dotnet.md) -oktatóanyagba

* eszközök konfigurálása a kívánt tulajdonságokkal a kívánt tulajdonságok használatával az [eszközök konfigurálásához](tutorial-device-twins.md) oktatóanyag,

* vezérelheti az eszközöket interaktív módon (például egy felhasználó által vezérelt alkalmazásból származó ventilátor bekapcsolásával) a [Direct Methods oktatóanyag használatával](quickstart-control-device-dotnet.md) .
