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
ms.openlocfilehash: 6048ddbcfed4ab929d521693175c456ad91f22e2
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447101"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Ismerkedés az IoT Hub-modulidentitással és -modulikerrel a portál és a .NET eszköz használata mellett

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Amíg az Azure IoT Hub eszközidentitása és eszközikre lehetővé teszi a háttéralkalmazás számára az eszköz konfigurálását, és rálátást nyújt az eszköz feltételeire, a modulidentitás és a moduliker az eszköz egyes összetevőihez biztosítja ezeket a lehetőségeket. A megfelelő, több összetevős eszközök, például az operációs rendszeren vagy a belső vezérlőprogramon alapuló eszközök esetében lehetővé teszi az elkülönített konfigurációk és feltételek beállítását az egyes összetevőkhöz.
>

Az oktatóanyag során a következőket fogja elsajátítani:

1. Hogyan modul identitás létrehozása a portálon. 

2. hogyan használható a .NET SDK eszközfrissítés ikermodulja az eszközről.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k][lnk-hub-sdks] című témakörben talál további információt.
>

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. (Ha nincs fiókja, létrehozhat egy [ingyenes fiókot][lnk-free-trial] néhány perc alatt.)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Az IoT hub kapcsolati karakterlánc

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Modulidentitás létrehozása a portálon

Egy eszközidentitáson belül legfeljebb 20 modulidentitás hozható létre. Kattintson a fenti **Modulidentitás hozzáadása** gombra az első, **myFirstModule** nevű modulidentitás létrehozásához. 

  ![Eszköz adatai][9]

Mentse a frissen létrehozott modulidentitást, és kattintson rá. Megjelennek a modulidentitás adatai. Mentse el a kapcsolati sztring elsődleges kulcsát. Ezt a következő szakaszban fogjuk használni, amikor beállítjuk a modult az eszközön.

  ![Eszköz adatai][12]

## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Sikeresen létrehozta a modulidentitást az IoT Hubban. Ideje megkísérelni a felhővel való kommunikációt a szimulált eszközről. A modulidentitás létrehozása után egy moduliker is implicit módon létrejön az IoT Hubban. Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

## <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus Asztalialkalmazás-projektet a meglévő megoldáshoz használatával a **Console App (.NET Framework)** projektsablonnal. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja. Adja a projektnek az **UpdateModuleTwinReportedProperties** nevet.

  ![Hozzon létre egy Visual Studio-projektet.][13]

## <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>A legújabb Azure IoT hubhoz .NET eszközoldali SDK telepítése

Identitás- és modul ikermodul jelenleg nyilvános előzetes verzióban. Csak akkor érhető el az IoT Hub SDK-k előzetes eszközön. A Visual Studióban válassza a Tools (Eszközök) > NuGet Package Manager (NuGet-csomagkezelő) > Manage NuGet Packages for Solution (NuGet-csomagok kezelése a megoldáshoz) elemet. Keresse meg a Microsoft.Azure.Devices.Client csomagot. Győződjön meg arról, hogy az előzetes verzió jelölőnégyzete be van jelölve. Válassza ki a legújabb verziót, és telepítse. Most már az összes modulfunkcióhoz rendelkezik hozzáféréssel. 

  ![Az Azure IoT Hub .NET szolgáltatási SDK 1.16.0-preview-005-ös verziójának telepítése][14]

## <a name="get-your-module-connection-string"></a>A modul kapcsolati sztring lekérése

Jelentkezzen be [az Azure portal][lnk-portal]. Keresse meg az IoT Hubot, és kattintson az IoT-eszközök elemre. Keresés myFirstDevice, nyissa meg azt, és tekintse meg a myFirstModule sikeresen létrejött. Másolja ki a modul kapcsolati sztringjét. A következő lépés során szükség lesz rá.

  ![Az Azure Portal moduladatai][15]

## <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties Konzolalkalmazás létrehozása

Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

```csharp
using Microsoft.Azure.Devices.Client;
using Microsoft.Azure.Devices.Shared;
```

Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

```csharp
private const string ModuleConnectionString = "<Your module connection string>";
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
    Console.ReadKey();
    Client.CloseAsync().Wait();
}

private static void ConnectionStatusChangeHandler(ConnectionStatus status, ConnectionStatusChangeReason reason)
{
    Console.WriteLine($"Status {status} changed: {reason}");
}
```

A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. A nyilvános előzetes verzióban az AMQP csak a moduliker-műveletek esetében támogatott.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására. A Visual Studióban a Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal a megoldásra, majd kattintson a **Set StartUp projects** (Indítási projektek beállítása) parancsra. Válassza a **Multiple startup projects** (Több indítási projekt) lehetőséget, majd válassza a **Start** (Indítás) elemet a konzolalkalmazás műveleteként. És nyomja le az F5 billentyűt, indítsa el a két alkalmazás futtatásához. 

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az IoT Hub identitás- és modul ikermodul .NET biztonsági mentési és a .NET-eszköz][lnk-csharp-csharp-getstarted]
* [Ismerkedés az IoT Edge szolgáltatással][lnk-iot-edge]


<!-- Images. -->
[9]:./media/iot-hub-portal-csharp-module-twin-getstarted/create-module-id.JPG
[12]:./media/iot-hub-portal-csharp-module-twin-getstarted/module-details.JPG
[13]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/update-twins-csharp1.JPG
[14]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png
[15]: ./media/iot-hub-csharp-csharp-module-twin-getstarted/module-detail.JPG
<!-- Links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-csharp-csharp-getstarted]: iot-hub-csharp-csharp-module-twin-getstarted.md
[lnk-iot-edge]: ../iot-edge/tutorial-simulate-device-linux.md
