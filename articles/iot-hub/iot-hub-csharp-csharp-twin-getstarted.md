---
title: Ismerkedés az Azure IoT Hub device twins (.NET/.NET) |} A Microsoft Docs
description: Hogyan használható az Azure IoT Hub device twins címkéket adhat hozzá, majd az IoT Hub-lekérdezést. A szimulált eszközalkalmazás és az Azure IoT szolgáltatás SDK for .NET megvalósítása, amely hozzáadja a címkék és az IoT Hub-lekérdezést futtathat egy service-alkalmazás üzembe helyezéséhez használhatja az Azure IoT eszközoldali SDK a .NET-hez.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: robin.shahan
ms.openlocfilehash: 63ec161f2f0d8be4572acf456c81e19ca75bd856
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010735"
---
# <a name="get-started-with-device-twins-netnet"></a>Első lépések az ikereszközökhöz (.NET/.NET)
[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ez az oktatóanyag végén a .NET-konzolalkalmazással fog rendelkezni:

* **CreateDeviceIdentity**, a .NET-alkalmazás, amely egy eszközidentitást, valamint a szimulált eszközalkalmazás kapcsolódni a társított biztonsági kulcsot hoz létre.

* **AddTagsAndQuery**, amely címkét ad hozzá, és lekérdezi az ikereszközök .NET-háttérrendszer alkalmazás.

* **ReportConnectivity**, a .NET-eszközalkalmazást, amely szimulálja a olyan eszköz, amely az IoT hubhoz a korábban létrehozott eszközidentitással, és jelenti a kapcsolat állapotát.

> [!NOTE]
> A cikk [Azure IoT SDK-k](iot-hub-devguide-sdks.md) használható eszköz és a háttér-alkalmazásokat hozhat létre az Azure IoT SDK-kkal kapcsolatos információkat biztosít.
> 

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) mindössze néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-the-service-app"></a>Az alkalmazás létrehozása

Ebben a szakaszban konzolalkalmazást hoz létre egy .NET (C# használatával), amely a hely metaadatokat ad hozzá az ikereszköz társított **myDeviceId**. Ezután lekérdezi az ikereszközök, az IoT hub kiválasztása az eszközök, az Egyesült Államok, és amelyekre a mobilhálózati kapcsolat jelentett tárolja.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **AddTagsAndQuery**.
   
    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-twin-getstarted/createnetapp.png)

2. A Megoldáskezelőben kattintson a jobb gombbal a **AddTagsAndQuery** projektre, és kattintson a **NuGet-csomagok kezelése...** .

3. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **Microsoft.Azure.Devices**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT szolgáltatás SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagot és annak függőségeit.
   
    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-twin-getstarted/servicesdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati sztringre.

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
   
    A **RegistryManager** osztály tesz közzé az ikereszközökhöz, a szolgáltatás használatához szükséges összes módszert. Az előző kód először inicializálja a **registryManager** objektumot, majd lekéri az ikereszközön **myDeviceId**, és végül frissíti a címkéket a kívánt helyre adatokkal.
   
    Miután frissített, két lekérdezést hajt végre: az első kiválasztja a csak az ikereszközök található eszközök a **Redmond43** gépek és a második megjeleníthető a lekérdezést, válassza ki a keresztül mobilhálózati is csatlakozó eszközöket.
   
    Vegye figyelembe, hogy az előző kód, amikor létrehozza a **lekérdezés** objektumazonosító, a visszaadott dokumentumok maximális számát határozza meg. A **lekérdezés** az objektum tartalmaz egy **hasmoreresults használatával** logikai tulajdonság, amely segítségével meghívása a **GetNextAsTwinAsync** módszerek többször az összes eredmény beolvasása. A metódus hívása **GetNextAsJson** eredményeket nem ikereszközök, például összesítési lekérdezések eredményeit érhető el.

7. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

8. A megoldáskezelőben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **AddTagsAndQuery** projekt **Start**. Hozza létre a megoldást.

9. Az alkalmazás futtatásához kattintson a jobb gombbal a a **AddTagsAndQuery** projektet és kijelölése **Debug**, utána pedig **új példány indítása**. Megjelenik a lekérdezés feltevéséhez az eredmények között egy eszközön található összes eszköz **Redmond43** sem a lekérdezést, amely korlátozza az eredményeket a mobilhálózati használó eszközöket.
   
    ![Lekérdezési eredmények ablak](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

A következő szakaszban, hogy egy eszközalkalmazás létrehozása, amely jelent a kapcsolati adatokat, és módosítja az előző szakaszban a lekérdezés eredménye.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban egy .NET-konzolalkalmazást, amely csatlakozik a hubhoz, létrehozhat **myDeviceId**, majd frissíti a jelentett tulajdonságok alapján, hogy csatlakoztatva van mobilhálózat használata adatokat tartalmazzák.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Adja a projektnek **ReportConnectivity**.
   
    ![Új Visual C# Windows klasszikus eszközalkalmazás](./media/iot-hub-csharp-csharp-twin-getstarted/createdeviceapp.png)
    
2. A Megoldáskezelőben kattintson a jobb gombbal a **ReportConnectivity** projektre, és kattintson a **NuGet-csomagok kezelése...** .

3. Az a **NuGet-Csomagkezelő** ablakban válassza **Tallózás** és keressen rá a **Microsoft.Azure.Devices.Client**. Válassza ki **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Client** csomagot, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti, és hozzáad egy hivatkozást a [Azure IoT eszközoldali SDK-t](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagot és annak függőségeit.
   
    ![NuGet-Csomagkezelő ablak ügyfélalkalmazás](./media/iot-hub-csharp-csharp-twin-getstarted/clientsdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az eszközt az előző szakaszban feljegyzett kapcsolati karakterlánccal.

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

    A **ügyfél** vezérlőnek az ikereszközökhöz az eszközről való kommunikációhoz szükséges összes módszert. A fent látható kód inicializálja a **ügyfél** objektumot, és ezután lekéri az ikereszközön **myDeviceId**.

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

   A kód felett frissítések **myDeviceId**által jelentett tulajdonságot, amelyben a kapcsolati információkat.

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

9. A megoldáskezelőben nyissa meg a **állítsa be indítási projektek...**  , és győződjön meg arról, hogy a **művelet** a **ReportConnectivity** projekt **Start**. Hozza létre a megoldást.

10. Az alkalmazás futtatásához kattintson a jobb gombbal a a **ReportConnectivity** projektet és kijelölése **Debug**, utána pedig **új példány indítása**. Meg kell jelennie az ikereszköz-információk lekérése, és a kapcsolat, elküldi egy *jelentett tulajdonság*.
   
    ![A jelentés a kapcsolatot eszközalkalmazás futtatása](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)
       
11. Most, hogy az eszköz jelenik meg a kapcsolati információkat, akkor meg kell jelennie mindkét lekérdezést. Futtatás a .NET **AddTagsAndQuery** alkalmazásnak, hogy újra futtassa a lekérdezéseket. Ezúttal **myDeviceId** meg kell jelennie mindkét lekérdezés eredményeit.
   
    ![Eszköz csatlakoztatása sikeresen megtörtént a jelzett](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Címkeként, egy háttér-alkalmazásból hozzáadott eszközök metaadatait, és a egy szimulált eszközalkalmazás zapsáno do kapcsolat eszközadatokat a jelentés azokat az ikereszköz. Azt is megtanulta, hogyan kérdezhet le ezt az információt az SQL-szerű az IoT Hub lekérdezési nyelv segítségével.

Az alábbi forrásanyagokból megtudhatja, hogyan lehet:

* telemetriát az eszközökről a [telemetriát küldjön az eszközről az IoT hub](quickstart-send-telemetry-dotnet.md) oktatóanyagban

* konfigurálhatja az eszközöket használó eszköz ikereszköz kívánt tulajdonságait a a [használata kívánt tulajdonságok konfigurálhatja az eszközöket](tutorial-device-twins.md) oktatóanyagban

* Az eszközök, interaktív módon (például egy felhasználó által felügyelt alkalmazásból ventilátor bekapcsolása) szabályozhatja a [közvetlen metódusok használata](quickstart-control-device-dotnet.md) oktatóanyag.
