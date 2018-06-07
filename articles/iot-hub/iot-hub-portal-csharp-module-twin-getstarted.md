---
title: Ismerkedés az Azure IoT Hub-modulidentitással és -modulikerrel (portál és .NET) | Microsoft Docs
description: Megtudhatja, hogyan hozhat létre modulidentitást és frissíthet modulikreket a portál és a .NET használatával.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 04/26/2018
ms.author: dobett
ms.openlocfilehash: b4502dfc8f856516989326c8d748a5d13fdba02b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634590"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Ismerkedés az IoT Hub-modulidentitással és -modulikerrel a portál és a .NET eszköz használata mellett

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Amíg az Azure IoT Hub eszközidentitása és eszközikre lehetővé teszi a háttéralkalmazás számára az eszköz konfigurálását, és rálátást nyújt az eszköz feltételeire, a modulidentitás és a moduliker az eszköz egyes összetevőihez biztosítja ezeket a lehetőségeket. A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.

Az oktatóanyag során a következőket fogja elsajátítani: 
1. Modulidentitás létrehozása a portálon; 
2. Moduliker frissítése az eszközről a .NET eszköz SDK-jának használatával.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről további információkért lásd az [Azure IoT SDK-kkal][lnk-hub-sdks] kapcsolatos témakört.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].)

[!INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

## <a name="create-a-device-identity-in-the-portal"></a>Eszközidentitás létrehozása a portálon

Ezzel az IoT Hub készen áll. Nyissa meg a [portált](https://portal.azure.com), és keresse meg az IoT Hubot. Kattintson az IoT-eszközök lehetőségre, majd a Hozzáadás gombra az eszközidentitás létrehozásához. Nevezze el a következőképpen: **MyFirstDevice**. 

![Eszközidentitás létrehozása][8]

A mentést követően az eszközidentitási listában láthatja, hogy a MyFirstDevice identitás sikeresen létrejött.

![Eszközazonosító létrehozva][11]

Ezután kattintson a sorra. Ekkor megjelennek az eszközadatok.

![Eszközadatok][10]

## <a name="create-a-module-identity-in-the-portal"></a>Modulidentitás létrehozása a portálon

Egy eszközidentitáson belül legfeljebb 20 modulidentitás hozható létre. Kattintson a fenti **Modulidentitás hozzáadása** gombra az első, **myFirstModule** nevű modulidentitás létrehozásához. 

![Eszközadatok][9]

Mentse a frissen létrehozott modulidentitást, és kattintson rá. Megjelennek a modulidentitás adatai. Mentse el a kapcsolati sztring elsődleges kulcsát. Ezt a következő szakaszban fogjuk használni, amikor beállítjuk a modult az eszközön.

![Eszközadatok][12]

<a id="D2C_csharp"></a>
## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Sikeresen létrehozta a modulidentitást az IoT Hubban. Ideje megkísérelni a felhővel való kommunikációt a szimulált eszközről. A modulidentitás létrehozása után egy moduliker is implicit módon létrejön az IoT Hubban. Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

1. **Visual Studio-projekt létrehozása** – A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet a meglévő megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja. Adja a projektnek az **UpdateModuleTwinReportedProperties** nevet.

    ![Visual Studio-projekt létrehozása][13]

2. **Telepítse a legújabb Azure IoT Hub .NET SDK eszköz** -modul identitás- és modul két nyilvános előzetes verziójában van. Ez csak az IoT Hub előzetes verziójú eszközoldali SDK-iban érhető el. A Visual Studióban válassza a Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) elemet. Keresse meg a Microsoft.Azure.Devices.Client csomagot. Győződjön meg arról, hogy az előzetes verzió jelölőnégyzete be van jelölve. Válassza ki a legújabb verziót, és telepítse. Most már az összes modulfunkcióhoz rendelkezik hozzáféréssel. 

    ![Az Azure IoT Hub .NET szolgáltatási SDK 1.16.0-preview-005-ös verziójának telepítése][14]

3. 
  **Szerezze be a modul kapcsolati sztringjét** – ezt most megteheti, ha bejelentkezik az [Azure Portalra][lnk-portal]. Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresse meg a myFirstDevice elemet, nyissa meg, és győződjön meg arról, hogy a myFirstModule sikeresen létrejött. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

    ![Az Azure Portal moduladatai][15]

4. **Hozza létre az UpdateModuleTwinReportedProperties konzolalkalmazást.** Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

    Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>“;
    private static ModuleClient Client = null;
    ```

    Adja hozzá a **Program** osztályhoz a következő, **OnDesiredPropertyChanged** metódust:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, object userContext)
        {
            Console.WriteLine("desired property change:");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));
            Console.WriteLine("Sending current time as reported property");
            TwinCollection reportedProperties = new TwinCollection
            {
                ["DateTimeLastDesiredPropertyChangeReceived"] = DateTime.Now
            };

            await Client.UpdateReportedPropertiesAsync(reportedProperties).ConfigureAwait(false);
        }
    ```

    Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin));

            Console.WriteLine("Sending app start time as reported property");
            TwinCollection reportedProperties = new TwinCollection();
            reportedProperties["DateTimeLastAppLaunch"] = DateTime.Now;

            Client.UpdateReportedPropertiesAsync(reportedProperties);
        }
        catch (AggregateException ex)
        {
            Console.WriteLine("Error in sample: {0}", ex);
        }

        Console.WriteLine("Waiting for Events.  Press enter to exit...");
        Client.CloseAsync().Wait();
    }
    ```

    A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. A nyilvános előzetes verzióban az AMQP csak a moduliker-műveletek esetében támogatott.
    ```

## Run the apps

You are now ready to run the apps. In Visual Studio, in Solution Explorer, right-click your solution, and then click **Set StartUp projects**. Select **Multiple startup projects**, and then select **Start** as the action for the console app. And then press F5 to start both apps running. 

## Next steps

To continue getting started with IoT Hub and to explore other IoT scenarios, see:

* [Get started with IoT Hub module identity and module twin using .NET backup and .NET device][lnk-csharp-csharp-getstarted]
* [Getting started with IoT Edge][lnk-iot-edge]


<!-- Images. -->
[8]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-device-id.JPG
[9]:./media\iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[10]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-details.JPG
[11]:./media\iot-hub-portal-csharp-module-twin-getstarted/device-id-created.JPG
[12]:./media\iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media\iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
