---
title: Ismerkedés az Azure IoT Hub eszközfelügyelettel (.NET/.NET) | Microsoft dokumentumok
description: Az Azure IoT Hub eszközfelügyeleti használata távoli eszköz újraindításának kezdeményezéséhez. Az Azure IoT-eszköz SDK for .NET egy szimulált eszközalkalmazás megvalósításához, amely magában foglalja a közvetlen metódusés az Azure IoT szolgáltatás SDK a .NET valósítja meg a közvetlen metódust meghívó szolgáltatásalkalmazás.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: robinsh
ms.openlocfilehash: 3b37d7e049e7daabbbb4fe1a7b49feb654e8accc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110256"
---
# <a name="get-started-with-device-management-net"></a>Az eszközkezelés első lépései (.NET)

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* Az Azure Portal használatával hozzon létre egy IoT-központot, és hozzon létre egy eszközidentitást az IoT-központban.

* Hozzon létre egy szimulált eszközalkalmazást, amely közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy .NET konzolalkalmazást, amely meghívja a szimulált eszközalkalmazásban az újraindításközvetlen metódust az IoT-központon keresztül.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* **SimulateManagedDevice**. Ez az alkalmazás csatlakozik az IoT hub az eszköz identitása korábban létrehozott, kap egy újraindítás közvetlen metódust, szimulálja a fizikai újraindítás, és jelenti az idő az utolsó újraindítás.

* **TriggerReboot**. Ez az alkalmazás közvetlen metódust hív meg a szimulált eszközalkalmazásban, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

## <a name="prerequisites"></a>Előfeltételek

* Visual Studio.

* Aktív Azure-fiók. Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.](https://azure.microsoft.com/pricing/free-trial/)

* Győződjön meg arról, hogy a 8883-as port nyitva van a tűzfalon. A cikkben szereplő eszközminta az MQTT protokollt használja, amely a 8883-as porton keresztül kommunikál. Előfordulhat, hogy ez a port bizonyos vállalati és oktatási hálózati környezetekben le van tiltva. A probléma megoldásáról további információt és a probléma megoldásáról a [Csatlakozás az IoT Hubhoz (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)című témakörben talál.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hubban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub kapcsolati karakterláncának beszereznie

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás aktiválása az eszközön közvetlen módszerrel

Ebben a szakaszban egy .NET konzolalkalmazást hoz létre a C#használatával, amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz ikerlekérdezéseit használja az eszköz utolsó újraindítási idejének felderítéséhez.

1. A Visual Studióban válassza **az Új projekt létrehozása**lehetőséget.

1. Az **Új projekt létrehozása**csoportban keresse meg és jelölje ki a Console App **(.NET Framework)** projektsablont, majd válassza a **Tovább**gombot.

1. Az **új projekt konfigurálása**területen nevezze el a *projectet TriggerReboot*, és válassza a .NET Framework 4.5.1-es vagy újabb verzióját. Kattintson a **Létrehozás** gombra.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-configure.png)

1. A **Megoldáskezelőben**kattintson a jobb gombbal az **TriggerReboot** projektre, majd válassza **a NuGet csomagok kezelése parancsot.**

1. Válassza a **Tallózás**lehetőséget, majd keresse meg és válassza a **Microsoft.Azure.Devices**lehetőséget. A **Microsoft.Azure.Devices** csomag telepítéséhez válassza a **Telepítés** lehetőséget.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-device-management-get-started/create-trigger-reboot-nuget-devices.png)

   Ez a lépés letölti, telepíti, és hozzáadja az [Azure IoT szolgáltatás SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagés annak függőségeit.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje `{iot hub connection string}` le a helyőrző értéket az IoT Hub kapcsolati karakterláncára, amelyet korábban másolt [az IoT hub kapcsolati karakterláncának beszerzése című részben.](#get-the-iot-hub-connection-string)

   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

1. Adja hozzá a következő módszert a **Program** osztályhoz.  Ez a kód lekéri az eszközt iker az újrafutóeszköz höz, és a jelentett tulajdonságok kimenetére.

   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```

1. Adja hozzá a következő módszert a **Program** osztályhoz.  Ez a kód közvetlen módszerrel indítja el az újraindítást az eszközön.

   ```csharp
   public static async Task StartReboot()
   {
       client = ServiceClient.CreateFromConnectionString(connString);
       CloudToDeviceMethod method = new CloudToDeviceMethod("reboot");
       method.ResponseTimeout = TimeSpan.FromSeconds(30);

       CloudToDeviceMethodResult result = await 
         client.InvokeDeviceMethodAsync(targetDevice, method);

       Console.WriteLine("Invoked firmware update on device.");
   }
   ```

1. Végül adja a következő sorokat a **Main** metódushoz:

   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

1. Válassza a > **Build-megoldás lehetőséget.** **Build**

> [!NOTE]
> Ez az oktatóanyag csak egyetlen lekérdezést hajt végre az eszköz jelentett tulajdonságaihoz. Az éles kódban azt javasoljuk, hogy a jelentett tulajdonságok változásainak észleléséhez lekérdezési.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban:

* Hozzon létre egy .NET konzolalkalmazást, amely a felhő által megnevezett közvetlen metódusra reagál.

* Szimulált eszköz újraindítása.

* A jelentett tulajdonságok segítségével engedélyezze az eszközök ikerlekérdezéseit az eszközök azonosítására, és hogy mikor újraindultak utoljára.

A szimulált eszközalkalmazás létrehozásához hajtsa végre az alábbi lépéseket:

1. A Visual Studio programban a már létrehozott TriggerReboot megoldásban válassza az**Új** > **projekt** **fájlja** > lehetőséget. Az **Új projekt létrehozása**csoportban keresse meg és jelölje ki a Console App **(.NET Framework)** projektsablont, majd válassza a **Tovább**gombot.

1. Az **Új projekt konfigurálása**területen nevezze el a *simulateManageddevice*projektet, a **Megoldás**esetében pedig válassza **a Hozzáadás a megoldáshoz**lehetőséget. Kattintson a **Létrehozás** gombra.

    ![Projekt elnevezése és hozzáadása a megoldáshoz](./media/iot-hub-csharp-csharp-device-management-get-started/configure-device-app.png)

1. A Megoldáskezelőben kattintson a jobb gombbal az új **SimulateManagedDevice** projektre, majd válassza **a NuGet csomagok kezelése parancsot.**

1. Válassza a **Tallózás**lehetőséget, majd keresse meg és válassza a **Microsoft.Azure.Devices.Client**elemet. Válassza az **Install** (Telepítés) lehetőséget.

    ![A NuGet csomagkezelő ablak ügyfélalkalmazása](./media/iot-hub-csharp-csharp-device-management-get-started/create-device-nuget-devices-client.png)

   Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az [Azure IoT-eszköz SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomag és a függőségek.

1. Adja hozzá a következő `using`utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje `{device connection string}` le a helyőrző értéket az eszköz kapcsolati karakterláncára, amelyet korábban az [IoT hubban új eszköz regisztrálása című](#register-a-new-device-in-the-iot-hub)részben írt fel.

    ```csharp
    static string DeviceConnectionString = "{device connection string}";
    static DeviceClient Client = null;
    ```

1. Adja hozzá a következőket a közvetlen módszer eszközre való megvalósításához:

   ```csharp
   static Task<MethodResponse> onReboot(MethodRequest methodRequest, object userContext)
   {
       // In a production device, you would trigger a reboot 
       //   scheduled to start after this method returns.
       // For this sample, we simulate the reboot by writing to the console
       //   and updating the reported properties.
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

       string result = @"{""result"":""Reboot started.""}";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

1. Végül adja hozzá a következő kódot a **fő** metódushoz az IoT hubhoz való kapcsolat megnyitásához és a metódusfigyelő inicializálásához:

   ```csharp
   try
   {
       Console.WriteLine("Connecting to hub");
       Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, 
         TransportType.Mqtt);

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
   ```

1. A Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd válassza **az Indítási projektek beállítása parancsot.**

1. A Common Properties Startup Project **(Közös tulajdonságok** > **indítási projekt)** területen válassza az **Egyetlen indítási projekt**lehetőséget, majd válassza a **SimulateManagedDevice projektet.** A módosítások mentéséhez kattintson az **OK** gombra.

1. Válassza a > **Build-megoldás lehetőséget.** **Build**

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban újrapróbálkozási házirendeket (például exponenciális visszamaradást) kell [megvalósítania,](/azure/architecture/best-practices/transient-faults)ahogy azt az átmeneti hibakezelés javasolta.

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A .NET eszközalkalmazás **SimulateManagedDevice**eszközének futtatásához a Megoldáskezelőben kattintson a jobb gombbal a **SimulateManagedDevice** projektre, válassza a **Hibakeresés parancsot,** majd válassza **az Új példány indítása parancsot.** Az alkalmazásnak meg kell kezdenie az IoT hubról érkező metódushívások figyelését.

1. Ezt követően az eszköz csatlakoztatva van, és a metódusmeghívásokra vár, kattintson a jobb gombbal a **TriggerReboot** projektre, válassza a **Debug parancsot,** majd válassza **az Új példány indítása parancsot.**

   Látnia kell a "Rebooting!" a **SimulatedManagedDevice** konzolon és az eszköz jelentett tulajdonságaiban, amelyek tartalmazzák az utolsó újraindítási időt, a **TriggerReboot** konzolon.

    ![A szolgáltatás és az eszközalkalmazás futtatása](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
