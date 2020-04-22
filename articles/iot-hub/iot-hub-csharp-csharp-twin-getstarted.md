---
title: Ismerkedés az Azure IoT Hub-eszközök twins (.NET/.NET) | Microsoft dokumentumok
description: Az Azure IoT Hub-eszközök twins használata címkék hozzáadásához, majd egy IoT Hub-lekérdezés használatához. Az Azure IoT-eszköz SDK for .NET a szimulált eszközalkalmazás és az Azure IoT szolgáltatás SDK.NET valósítja meg a címkéket, és futtatja az IoT Hub-lekérdezést.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: robinsh
ms.custom: mqtt
ms.openlocfilehash: 620e0213733d278a28ec1bcad4b031f5764ccda9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733137"
---
# <a name="get-started-with-device-twins-net"></a>Az ikereszközök első lépései (.NET)

[!INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

Ebben az oktatóanyagban hozza létre a .NET konzolalkalmazásokat:

* **CreateDeviceIdentity**. Ez az alkalmazás létrehoz egy eszköz identitásés a kapcsolódó biztonsági kulcsot, hogy csatlakoztassa a szimulált eszköz app.

* **AddTagsAndQuery**. Ez a háttéralkalmazás címkéket és lekérdezéseket ad hozzá az ikereszközökhez.

* **ReportConnectivity**. Ez az eszközalkalmazás szimulálja az eszközt, amely csatlakozik az IoT hub az eszköz identitása korábban létrehozott, és jelenti a kapcsolati állapotát.

> [!NOTE]
> Az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) az Azure IoT SDK-k, amelyek segítségével eszköz- és háttéralkalmazásokat hozhat létre.
>

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-twin-shared-access-policy-text](../../includes/iot-hub-howto-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-custom-connection-string](../../includes/iot-hub-include-find-custom-connection-string.md)]

## <a name="create-the-service-app"></a>A szolgáltatásalkalmazás létrehozása

Ebben a szakaszban egy C#-szal létrehozott .NET konzolalkalmazást hoz létre, amely helymetaadatokat ad hozzá a **myDeviceId-hoz**társított ikereszközhöz. Ezután lekérdezi az IT hubban tárolt ikereszközök az Egyesült Államokban található eszközök kiválasztását, majd azokat, amelyek mobilhálózati kapcsolatot jelentettek.

1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Az **Új projekt konfigurálása**területen nevezze el a projektet **AddTagsAndQuery**néven.

    ![Az AddTagsAndQuery projekt konfigurálása](./media/iot-hub-csharp-csharp-twin-getstarted/config-addtagsandquery-app.png)

1. A Megoldáskezelőben kattintson a jobb gombbal az **AddTagsAndQuery** projektre, majd válassza **a NuGet csomagok kezelése parancsot.**

1. Válassza **a Tallózás** és keresés lehetőséget, és válassza a **Microsoft.Azure.Devices**lehetőséget. Válassza az **Install** (Telepítés) lehetőséget.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-twin-getstarted/nuget-package-addtagsandquery-app.png)

   Ez a lépés letölti, telepíti, és hozzáadja az [Azure IoT szolgáltatás SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagés annak függőségeit.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje `{iot hub connection string}` le az IoT Hub kapcsolati karakterlánc, amely másolt [az IoT hub kapcsolati karakterlánc beszerezni.](#get-the-iot-hub-connection-string)

    ```csharp  
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

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

    A **RegistryManager** osztály elérhetővé teszi az összes olyan módszert, amely a szolgáltatásból származó ikereszközök kelemi-kapcsolatához szükséges. Az előző kód először inicializálja a **registryManager** objektumot, majd lekéri az ikereszközt a **myDeviceId**számára, és végül frissíti a címkéket a kívánt helyadatokkal.

    A frissítés után két lekérdezést hajt végre: az első csak a **Redmond43-üzemben** található eszközök ikereszközeit választja ki, a második pedig finomítja a lekérdezést, hogy csak azokat az eszközöket válassza ki, amelyek szintén mobilhálózaton keresztül csatlakoznak.

    Az előző kód a **lekérdezési** objektum létrehozásakor megadja a visszaküldött dokumentumok maximális számát. A **lekérdezésobjektum** tartalmaz egy **HasMoreResults** logikai tulajdonságot, amellyel többször is meghívhatja a **GetNextAsTwinAsync** metódusokat az összes eredmény beolvasásához. A **GetNextAsJson** nevű metódus érhető el olyan eredményekhez, amelyek nem eszköztwins, például aggregációs lekérdezések eredményei.

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp  
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddTagsAndQuery().Wait();
    Console.WriteLine("Press Enter to exit.");
    Console.ReadLine();
    ```

1. Futtassa ezt az alkalmazást úgy, hogy a jobb gombbal az **AddTagsAndQuery** projektre kattint, és a **Debug parancsot választja,** majd **az Új példány indítása parancsot.** Meg kell jelennie egy eszköz az eredmények a lekérdezés tanusítandó összes eszköz **redmond43** és nincs a lekérdezés, amely korlátozza az eredményeket a mobilhálózatot használó eszközök.

    ![Lekérdezési eredmények az ablakban](./media/iot-hub-csharp-csharp-twin-getstarted/addtagapp.png)

A következő szakaszban létrehoz egy eszközalkalmazást, amely jelenti a kapcsolódási adatokat, és módosítja a lekérdezés eredményét az előző szakaszban.

## <a name="create-the-device-app"></a>Az eszközalkalmazás létrehozása

Ebben a szakaszban hozzon létre egy .NET konzolalkalmazást, amely **myDeviceId**néven csatlakozik a hubhoz, majd frissíti a jelentett tulajdonságait, hogy tartalmazza a mobilhálózaton keresztül csatlakoztatott információkat.

1. A Visual Studióban válassza az**Új** > **projekt** **fájlja** > lehetőséget. Az **Új projekt létrehozása**csoportban válassza a Console App **(.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Az **Új projekt konfigurálása**területen nevezze el a **ReportConnectivity projektet.** A **Megoldás**csoportban válassza **a Hozzáadás a megoldáshoz**lehetőséget, majd a **Create (Létrehozás)** lehetőséget.

1. A Megoldáskezelőben kattintson a jobb gombbal a **ReportConnectivity** projektre, majd válassza **a NuGet-csomagok kezelése parancsot.**

1. Válassza **a Tallózás** gombot, és keresse meg a **Microsoft.Azure.Devices.Client**elemet. Válassza az **Install** (Telepítés) lehetőséget.

   Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az [Azure IoT-eszköz SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomag és a függőségek.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp  
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje `{device connection string}` le az [IOt hubban egy új eszköz regisztrálása](#register-a-new-device-in-the-iot-hub)című részben megjegyzett eszközkapcsolati karakterláncra.

    ```csharp  
    static string DeviceConnectionString = "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

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

    Az **ügyfélobjektum** minden olyan módszert elérhetővé tesz, amely az eszköz twins-i eszköztwins-ekkel való interakcióhoz szükséges. A fenti kód inicializálja az **Ügyfél** objektumot, majd lekéri a **myDeviceId ikereszközét.**

1. Adja hozzá a **Program** osztályhoz a következő módszert:

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

   A fenti kód frissíti a **myDeviceId** jelentett tulajdonságát a kapcsolódási adatokkal.

1. Végül adja a következő sorokat a **Main** metódushoz:

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

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, és válassza **az Indítási projektek beállítása parancsot.**

1. A **Közös tulajdonságok** > **indítási projekt ben**válassza a Több **indítási projekt**lehetőséget. A **ReportConnectivity (ReportConnectivity)** csoportban válassza **a** **Start** műveletként lehetőséget. A módosítások mentéséhez kattintson az **OK** gombra.  

1. Futtassa ezt az alkalmazást úgy, hogy a jobb gombbal a **ReportConnectivity** projektre kattint, és a **Debug parancsot választja,** majd **indítsa el az új példányt.** Látnia kell, hogy az alkalmazás megkapja az ikeradatokat, majd a kapcsolódást ***jelentett tulajdonságként***küldi el.

    ![Eszközalkalmazás futtatása a kapcsolódás jelentéséhez](./media/iot-hub-csharp-csharp-twin-getstarted/rundeviceapp.png)

   Miután az eszköz jelentette a kapcsolódási adatait, mindkét lekérdezésben meg kell jelennie.

1. Kattintson a jobb gombbal az **AddTagsAndQuery** projektre, és válassza a **Debug** > **Start új példányt** a lekérdezések ismételt futtatásához. Ezúttal **a myDeviceId** mindkét lekérdezési eredményben meg kell jelennie.

    ![Az eszközkapcsolat sikeresen történt](./media/iot-hub-csharp-csharp-twin-getstarted/tagappsuccess.png)

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy új IoT Hubot konfigurált az Azure-portálon, majd létrehozott egy eszközidentitást az IoT Hub identitásjegyzékében. Az eszköz metaadatait címkékként adta hozzá egy háttéralkalmazásból, és írt egy szimulált eszközalkalmazást az eszközkapcsolati adatok jelentéséhez az ikereszközben. Azt is megtanulta, hogyan lehet lekérdezni ezeket az információkat az SQL-szerű IoT Hub lekérdezési nyelv használatával.

A következő forrásokból tudhat meg többet:

* A telemetriai adatok eszközökről történő küldéséről tekintse meg a [Telemetriai adatok küldése az eszközről egy IoT-központ oktatóanyagba](quickstart-send-telemetry-dotnet.md) című témakört.

* Ha tudni szeretné, hogyan konfigurálhatja az eszközöket az ikereszköz kívánt tulajdonságaival, olvassa el a Kívánt tulajdonságok használata eszközök [konfigurálásához](tutorial-device-twins.md) oktatóanyag című témakört.

* Az eszközök interaktív vezérléséről, például egy ventilátor felhasználó által vezérelt alkalmazásból történő bekapcsolásáról a [Közvetlen módszerek használata](quickstart-control-device-dotnet.md) oktatóanyag című témakörben olvashat.
