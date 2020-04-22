---
title: Első lépések az Azure IoT Hub modulidentitásával & ikermodullal (.NET)
description: Megtudhatja, hogyan hozhat létre modulidentitást és frissíthet modulikert a .NET-hez készült IoT SDK-k használatával.
author: chrissie926
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: menchi
ms.custom: amqp
ms.openlocfilehash: 919d1e37e6066c78e83d58be4fe4667ec67e45ad
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733395"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-net"></a>Ismerkedés az IoT Hub modulidentitásával és az ikermodullal (.NET)

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Míg az Azure IoT Hub-eszköz identitása és az ikereszköz lehetővé teszi, hogy a háttéralkalmazás konfiguráljon egy eszközt, és láthatóvá tegye az eszköz feltételeit, a modulidentitás és a modul ikermodul biztosítja ezeket a képességeket az eszköz egyes összetevőiszámára. A több összetevőből álló, például operációsrendszer-alapú eszközökön vagy belső vezérlőprogram-eszközökkel rendelkező eszközökön a modulidentitások és a moduliker-eszközök lehetővé teszik az egyes összetevők elkülönített konfigurációját és feltételeit.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* **CreateIdentities**. Ez az alkalmazás létrehoz egy eszköz identitását, a modul identitását és a kapcsolódó biztonsági kulcsot az eszköz és a modulügyfelek csatlakoztatásához.

* **UpdateModuleTwinReportedProperties**. Ez az alkalmazás elküldi a frissített modul iker jelentett tulajdonságokat az IoT hub.

> [!NOTE]
> Az Azure IoT SDK-kat használhatja az eszközökön és a megoldás háttérrendszerén futó alkalmazások összeállításához egyaránt. Ezekről az [Azure IoT SDK-k](iot-hub-devguide-sdks.md) című témakörben talál további információt.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-hub"></a>Elosztó létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-module-twin-shared-access-policy-text](../../includes/iot-hub-howto-module-twin-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-registryrw-connection-string](../../includes/iot-hub-include-find-registryrw-connection-string.md)]

[!INCLUDE [iot-hub-get-started-create-module-identity-csharp](../../includes/iot-hub-get-started-create-module-identity-csharp.md)]

## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

Mielőtt elkezdené, a modul kapcsolati karakterláncát. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/). Nyissa meg a központot, és válassza az **IoT-eszközök lehetőséget.** Keresse **myFirstDevice**. Válassza a **myFirstDevice lehetőséget** a megnyitásához, majd a **myFirstModule** lehetőséget a megnyitásához. A **Modul identitásának részletei mezőben**másolja a **kapcsolati karakterláncot (elsődleges kulcsot),** ha szükséges az alábbi eljárásban.

   ![Az Azure Portal moduladatai](./media/iot-hub-csharp-csharp-module-twin-getstarted/module-identity-detail.png)

1. A Visual Studióban vegyen fel egy új projektet a megoldásba az**Új** > **projekt** **fájlja** > lehetőséget választva. Az Új projekt létrehozása csoportban válassza a **Console App (.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Adja a projektnek az *UpdateModuleTwinReportedProperties* nevet. **Megoldás esetén**válassza **a Hozzáadás a megoldáshoz**lehetőséget. A Microsoft .NET-keretrendszer 4.6.1-es vagy újabb verzióját használja.

    ![Visual Studio-projekt létrehozása](./media/iot-hub-csharp-csharp-module-twin-getstarted/configure-update-twins-csharp1.png)

1. A projekt létrehozásához válassza a **Létrehozás** gombot.

1. A Visual Studióban nyissa meg az **Eszközök** > **NuGet csomagkezelő** > **a Megoldáshoz csomagkezelése segédprogramot.** Válassza ki a **Browse** (Tallózás) lapot.

1. Keresse meg és válassza a **Microsoft.Azure.Devices.Client**elemet, majd válassza a **Telepítés**lehetőséget.

    ![Az Azure IoT Hub .NET szolgáltatás SDK aktuális verziójának telepítése](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-client-sdk.png)

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using System.Threading.Tasks;
    using Newtonsoft.Json;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

    ```csharp
    private const string ModuleConnectionString = "<Your module connection string>";
    private static ModuleClient Client = null;
    static void ConnectionStatusChangeHandler(ConnectionStatus status, 
      ConnectionStatusChangeReason reason)
    {
        Console.WriteLine("Connection Status Changed to {0}; the reason is {1}", 
          status, reason);
    }
    ```

1. Adja hozzá a **Program** osztályhoz a következő, **OnDesiredPropertyChanged** metódust:

    ```csharp
    private static async Task OnDesiredPropertyChanged(TwinCollection desiredProperties, 
      object userContext)
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

1. Adja hozzá a következő sorokat a **fő** metódushoz:

    ```csharp
    static void Main(string[] args)
    {
        Microsoft.Azure.Devices.Client.TransportType transport = 
          Microsoft.Azure.Devices.Client.TransportType.Amqp;

        try
        {
            Client = 
              ModuleClient.CreateFromConnectionString(ModuleConnectionString, transport);
            Client.SetConnectionStatusChangesHandler(ConnectionStatusChangeHandler);
            Client.SetDesiredPropertyUpdateCallbackAsync(OnDesiredPropertyChanged, null).Wait();

            Console.WriteLine("Retrieving twin");
            var twinTask = Client.GetTwinAsync();
            twinTask.Wait();
            var twin = twinTask.Result;
            Console.WriteLine(JsonConvert.SerializeObject(twin.Properties)); 

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
        Console.ReadLine();
        Client.CloseAsync().Wait();
    }
    ```

    A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. A nyilvános előzetes verzióban az AMQP csak a moduliker-műveletek esetében támogatott.

1. Szükség esetén hozzáadhatja ezeket az állításokat a **fő** metódushoz, hogy egy eseményt küldjön az IoT Hubnak a modulból. Helyezze ezeket `try catch` a vonalakat a blokk alá.

    ```csharp
    Byte[] bytes = new Byte[2];
    bytes[0] = 0;
    bytes[1] = 1;
    var sendEventsTask = Client.SendEventAsync(new Message(bytes));
    sendEventsTask.Wait();
    Console.WriteLine("Event sent to IoT Hub.");
    ```

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már futtathatja az alkalmazásokat.

1. A Visual Studio **Megoldáskezelőjében**kattintson a jobb gombbal a megoldásra, majd válassza **az Indítási projektek beállítása parancsot.**

1. A **Közös tulajdonságok csoportban**válassza az **Indítási projekt lehetőséget.**

1. Válassza **a Több indítási projekt**lehetőséget, majd az **Indítás** lehetőséget az alkalmazások műveleteként, és a módosítások elfogadásához az **OK gombot.**

1. Az alkalmazások elindításához nyomja le az **F5** billentyűt.

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Eszközfelügyelet – első lépések](iot-hub-node-node-device-management-get-started.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
