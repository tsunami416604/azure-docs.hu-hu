---
title: Az Azure IoT Hub modul identitása & ikermodul (portál és .NET)
description: Megtudhatja, hogyan hozhat létre modulidentitást és frissíthet modulikreket a portál és a .NET használatával.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.custom: amqp
ms.openlocfilehash: a3258de2ed7269ab50e6feca3c421d55de5a9d91
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759779"
---
# <a name="get-started-with-iot-hub-module-identity-and-module-twin-using-the-portal-and-net-device"></a>Ismerkedés az IoT Hub-modulidentitással és -modulikerrel a portál és a .NET eszköz használata mellett

[!INCLUDE [iot-hub-selector-module-twin-getstarted](../../includes/iot-hub-selector-module-twin-getstarted.md)]

> [!NOTE]
> [A modulidentitások és modulikrek](iot-hub-devguide-module-twins.md) az Azure IoT Hub eszközidentitásához és eszközikeréhez hasonlók, de nagyobb részletességet biztosítanak. Míg az Azure IoT Hub-eszköz identitása és az ikereszköz lehetővé teszi, hogy a háttéralkalmazás konfiguráljon egy eszközt, és láthatóvá tegye az eszköz feltételeit, a modulidentitás és a modul ikermodul biztosítja ezeket a képességeket az eszköz egyes összetevőiszámára. A több összetevőből álló, például operációsrendszer-alapú eszközökön vagy belső vezérlőprogram-eszközökkel rendelkező eszközökön a modulidentitások és a moduliker-eszközök lehetővé teszik az egyes összetevők elkülönített konfigurációját és feltételeit.
>

Az oktatóanyag során a következőket fogja elsajátítani:

* Modulidentitás létrehozása a portálon.

* A .NET-eszköz SDK használata a modul ikerelem frissítéséhez az eszközről.

> [!NOTE]
> Az Azure IoT SDK-król, amelyek segítségével az eszközökön és a megoldás háttér-tartalékrendszerein futtatható alkalmazásokat is létrehozhat, az [Azure IoT SDK-k](iot-hub-devguide-sdks.md)című témakörben talál további információt.
>

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="create-a-hub"></a>Elosztó létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-hub"></a>Új eszköz regisztrálása a központban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-module-identity-in-the-portal"></a>Modulidentitás létrehozása a portálon

Egy eszközidentitáson belül legfeljebb 20 modulidentitás hozható létre. Identitás hozzáadásához kövesse az alábbi lépéseket:

1. Az előző szakaszban létrehozott eszköz esetében válassza a **Modulidentitás hozzáadása** lehetőséget az első modulidentitás létrehozásához.

1. Írja be a *myFirstModule*nevet . Mentse a modul identitását.

    ![Modulidentitás hozzáadása](./media/iot-hub-portal-csharp-module-twin-getstarted/add-module-identity.png)

    Az új modulidentitás a képernyő alján jelenik meg. Jelölje ki a modul identitásának részleteinek megtekintéséhez.

    ![A modul identitásának részleteinek megtekintése](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

Mentse a **Connect karakterláncot - elsődleges kulcsot**. Használja a következő szakaszban, hogy állítsa be a modult az eszközön.

## <a name="update-the-module-twin-using-net-device-sdk"></a>A moduliker frissítése a .NET eszközoldali SDK-val

Sikeresen létrehozta a modulidentitást az IoT Hubban. Ideje megkísérelni a felhővel való kommunikációt a szimulált eszközről. A modulidentitás létrehozása után egy moduliker is implicit módon létrejön az IoT Hubban. Ebben a szakaszban egy .NET-konzolalkalmazást hoz létre a szimulált eszközön a moduliker jelentett tulajdonságainak frissítéséhez.

### <a name="create-a-visual-studio-project"></a>Visual Studio-projekt létrehozása

Ha olyan alkalmazást szeretne létrehozni, amely frissíti a modul ikerjelentett tulajdonságait, kövesse az alábbi lépéseket:

1. A Visual Studio alkalmazásban válassza **az Új projekt létrehozása**lehetőséget, majd a Console App **(.NET Framework)** lehetőséget, majd a **Tovább**gombot.

1. Az **új projekt konfigurálása**párbeszédpanelen adja meg az *UpdateModuleTwinReportedProperties értéket* **projektnévként.** A folytatáshoz válassza a **Létrehozás** gombot.

    ![Ha konfigurálni szeretné, hogy egy vizuális stúdió projekt](./media/iot-hub-portal-csharp-module-twin-getstarted/configure-twins-project.png)

### <a name="install-the-latest-azure-iot-hub-net-device-sdk"></a>A legújabb Azure IoT Hub .NET-eszköz SDK telepítése

A modul identitása és az ikermodul nyilvános előzetes verzióban érhető el. Csak az IoT Hub kiadás előtti eszköz SDK-kban érhető el. A telepítéshez kövesse az alábbi lépéseket:

1. A Visual Studióban nyissa meg az **Eszközök** > **NuGet csomagkezelő** > **a Megoldáshoz csomagkezelése segédprogramot.**

1. Válassza a **Tallózás**gombot, majd az **Előzetes kiadást is**beleértve lehetőséget. Keresse meg a *Microsoft.Azure.Devices.Client*. Válassza ki a legújabb verziót, és telepítse.

    ![Az Azure IoT Hub .NET szolgáltatás SDK előzetes verziójának telepítése](./media/iot-hub-csharp-csharp-module-twin-getstarted/install-sdk.png)

    Most már az összes modulfunkcióhoz rendelkezik hozzáféréssel.

### <a name="get-your-module-connection-string"></a>A modul kapcsolati karakterláncának beszereznie

A konzolalkalmazás modulkapcsolati karakterláncára van szükség. Kövesse az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Keresse meg az IoT-központot, és válassza az **IoT-eszközök**lehetőséget. Nyissa meg **a myFirstDevice-t,** és láthatja, hogy **a myFirstModule** sikeresen létrejött.

1. Válassza a **MyFirstModule** lehetőséget a **Modulidentitások csoportban.** A **Modulidentitás részletei mezőben**másolja a **kapcsolati karakterláncot (elsődleges kulcs).**

    ![Az Azure Portal moduladatai](./media/iot-hub-portal-csharp-module-twin-getstarted/module-identity-details.png)

### <a name="create-updatemoduletwinreportedproperties-console-app"></a>UpdateModuleTwinReportedProperties konzolalkalmazás létrehozása

Az alkalmazás létrehozásához kövesse az alábbi lépéseket:

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

  ```csharp
  using Microsoft.Azure.Devices.Client;
  using Microsoft.Azure.Devices.Shared;
  using Newtonsoft.Json;
  ```

2. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét írja át a modul kapcsolati sztringjére.

  ```csharp
  private const string ModuleConnectionString = "<Your module connection string>";
  private static ModuleClient Client = null;
  ```

3. Adja hozzá a **Program** osztályhoz a következő, **OnDesiredPropertyChanged** metódust:

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

4. Végül cserélje ki a **fő** módszert a következő kódra:

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
  
  Az **F5**segítségével létrehozhatja és futtathatja ezt az alkalmazást.

A kódminta segítségével megtudhatja, hogyan kérheti le a modulikret és frissítheti a jelentett tulajdonságokat az AMQP-protokollal. A nyilvános előzetes verzióban az AMQP csak a moduliker-műveletek esetében támogatott.

## <a name="next-steps"></a>További lépések

További bevezetés az IoT Hub használatába, valamint egyéb IoT-forgatókönyvek megismerése:

* [Ismerkedés az IoT Hub-modulidentitással és -modulikerrel .NET biztonsági mentés és .NET eszköz használata mellett](iot-hub-csharp-csharp-module-twin-getstarted.md)

* [Ismerkedés az IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
