---
title: Ismerkedés az Azure IoT Hub-eszközök felügyeletével (.NET/.NET) | Microsoft Docs
description: Az Azure IoT Hub-eszközök felügyeletének használata távoli eszköz újraindításának elindításához. A .NET-hez készült Azure IoT Device SDK használatával olyan szimulált eszköz alkalmazást alkalmazhat, amely közvetlen metódust és a .NET-hez készült Azure IoT Service SDK-t használja a közvetlen metódust meghívó szolgáltatásalkalmazás megvalósításához.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 09/15/2017
ms.author: robinsh
ms.openlocfilehash: 544c29505d9bf34622963dfac8987b7eae4f8bc5
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668193"
---
# <a name="get-started-with-device-management-net"></a>Ismerkedés az eszközkezelés (.NET) szolgáltatással

[!INCLUDE [iot-hub-selector-dm-getstarted](../../includes/iot-hub-selector-dm-getstarted.md)]

Ez az oktatóanyag a következőket mutatja be:

* A Azure Portal használatával hozzon létre egy IoT Hub, és hozzon létre egy eszköz-identitást az IoT hub-ban.

* Hozzon létre egy szimulált eszköz alkalmazást, amely egy közvetlen metódust tartalmaz, amely újraindítja az eszközt. A közvetlen metódusok meghívása a felhőből történik.

* Hozzon létre egy olyan .NET-konzol alkalmazást, amely a szimulált eszközön a IoT hub használatával meghívja a közvetlen újraindítási módszert.

Az oktatóanyag végén két .NET-konzolalkalmazással fog rendelkezni:

* A korábban létrehozott **SimulateManagedDevice**az IoT hubhoz csatlakozik, és a rendszer újraindítási közvetlen módszert kap, szimulálja a fizikai újraindítást, és jelentést készít az utolsó újraindítás idejéről.

* A **TriggerReboot**, amely közvetlen metódust hív meg a szimulált eszköz alkalmazásban, megjeleníti a választ, és megjeleníti a frissített jelentett tulajdonságokat.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio.

* Aktív Azure-fiók. (Ha nincs fiókja, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/) .)

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Új eszköz regisztrálása az IoT hub-ban

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="get-the-iot-hub-connection-string"></a>Az IoT hub-beli kapcsolatok karakterláncának beolvasása

[!INCLUDE [iot-hub-howto-device-management-shared-access-policy-text](../../includes/iot-hub-howto-device-management-shared-access-policy-text.md)]

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Távoli újraindítás indítása az eszközön közvetlen módszer használatával

Ebben a szakaszban egy olyan .NET-konzol alkalmazást hoz létre ( C#a használatával), amely egy távoli újraindítást kezdeményez egy eszközön egy közvetlen metódus használatával. Az alkalmazás az eszköz kettős lekérdezéseit használva észleli az adott eszköz utolsó újraindításának idejét.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az új megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Nevezze el a projekt **TriggerReboot**.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt](./media/iot-hub-csharp-csharp-device-management-get-started/createserviceapp.png)

2. Megoldáskezelő kattintson a jobb gombbal a **TriggerReboot** projektre, majd kattintson a **NuGet-csomagok kezelése**elemre.

3. A **NuGet csomagkezelő** ablakban válassza a **Tallózás**lehetőséget, keresse meg a **Microsoft. Azure. Devices**elemet, válassza a **telepítés** lehetőséget a **Microsoft. Azure. Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti és hozzáadja az [Azure IoT Service SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices/) NuGet csomagra és annak függőségeire mutató hivatkozást.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](./media/iot-hub-csharp-csharp-device-management-get-started/servicesdknuget.png)

4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
   ```csharp
   using Microsoft.Azure.Devices;
   using Microsoft.Azure.Devices.Shared;
   ```
        
5. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le a korábban a [IoT hub](#get-the-iot-hub-connection-string)-beli kapcsolatok karakterláncának lekérése során másolt IoT hubi kapcsolatok karakterlánccá.
   
   ```csharp
   static RegistryManager registryManager;
   static string connString = "{iot hub connection string}";
   static ServiceClient client;
   static string targetDevice = "myDeviceId";
   ```

6. Adja hozzá a **Program** osztályhoz a következő metódust.  Ez a kód beolvassa az eszközhöz tartozó Twin eszközt az újraindításhoz, és kiírja a jelentett tulajdonságokat.
   
   ```csharp
   public static async Task QueryTwinRebootReported()
   {
       Twin twin = await registryManager.GetTwinAsync(targetDevice);
       Console.WriteLine(twin.Properties.Reported.ToJson());
   }
   ```
        
7. Adja hozzá a **Program** osztályhoz a következő metódust.  Ez a kód közvetlen módszer használatával indítja el az újraindítást az eszközön.

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

7. Végül adja a következő sorokat a **Main** metódushoz:
   
   ```csharp
   registryManager = RegistryManager.CreateFromConnectionString(connString);
   StartReboot().Wait();
   QueryTwinRebootReported().Wait();
   Console.WriteLine("Press ENTER to exit.");
   Console.ReadLine();
   ```

8. Hozza létre a megoldást.

> [!NOTE]
> Ez az oktatóanyag csak egyetlen lekérdezést hajt végre az eszköz jelentett tulajdonságaihoz. Az éles kódban javasolt a lekérdezés a jelentett tulajdonságok változásainak észlelésére.

## <a name="create-a-simulated-device-app"></a>Szimulált eszközalkalmazás létrehozása

Ebben a szakaszban a következőket hajtja végre:

* Hozzon létre egy .NET-konzol alkalmazást, amely válaszol a felhő által meghívott közvetlen metódusra.

* Szimulált eszköz újraindításának indítása.

* A jelentett tulajdonságok használatával engedélyezheti az eszköz kettős lekérdezéseit az eszközök azonosításához és az utolsó újraindításkor.

1. A Visual Studióban adjon hozzá egy Visual C# nyelvű Windows klasszikus asztalialkalmazás-projektet az aktuális megoldáshoz a **Console Application** (Konzolalkalmazás) projektsablonnal. Nevezze el a projekt **SimulateManagedDevice**.
   
    ![Új Visual C# Windows klasszikus eszköz alkalmazás](./media/iot-hub-csharp-csharp-device-management-get-started/createdeviceapp.png)
    
2. Megoldáskezelő kattintson a jobb gombbal a **SimulateManagedDevice** projektre, majd kattintson a **NuGet-csomagok kezelése..** . elemre.

3. A **NuGet csomagkezelő** ablakban válassza a **Tallózás** lehetőséget, és keresse meg a **Microsoft. Azure. Devices. Client**elemet. Válassza a **telepítés** lehetőséget a **Microsoft. Azure. Devices. Client** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti, telepíti és hozzáadja az [Azure IoT Device SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/) NuGet csomagra és annak függőségeire mutató hivatkozást.
   
    ![NuGet csomagkezelő ablak ügyfélalkalmazás](./media/iot-hub-csharp-csharp-device-management-get-started/clientsdknuget.png)
    
4. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    ```

5. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban feljegyzett eszköz-összekapcsolási sztringre.

    ```csharp
    static string DeviceConnectionString = 
      "HostName=<yourIotHubName>.azure-devices.net;DeviceId=<yourIotDeviceName>;SharedAccessKey=<yourIotDeviceAccessKey>";
    static DeviceClient Client = null;
    ```
6. Adja hozzá a következőt a közvetlen metódus megvalósításához az eszközön:

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

       string result = "'Reboot started.'";
       return Task.FromResult(new MethodResponse(Encoding.UTF8.GetBytes(result), 200));
   }
   ```

7. Végül adja hozzá a következő kódot a **Main** metódushoz a IoT hub-hoz való kapcsolódás megnyitásához és a metódus-figyelő inicializálásához:

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
        
8. A Visual Studio Megoldáskezelőben kattintson a jobb gombbal a megoldásra, majd kattintson a **Kezdőprojektek beállítása** parancsra. Válassza az **egyetlen indítási projekt**lehetőséget, majd a legördülő menüben válassza ki a **SimulateManagedDevice** projektet. Hozza létre a megoldást.       

> [!NOTE]
> Az egyszerűség kedvéért ez az oktatóanyag nem valósít meg semmilyen újrapróbálkozási házirendet. Az éles kódban az újrapróbálkozási szabályzatokat (például egy exponenciális leállítási) kell megvalósítani, ahogy azt a cikkben is ismertetjük, az [átmeneti hibák kezelésére](/azure/architecture/best-practices/transient-faults).

## <a name="run-the-apps"></a>Az alkalmazások futtatása

Most már készen áll az alkalmazások futtatására.

1. A .NET-eszköz alkalmazás **SimulateManagedDevice**futtatása. kattintson a jobb gombbal a **SimulateManagedDevice** projektre, válassza a **hibakeresés**lehetőséget, majd válassza az **új példány indítása**lehetőséget. Meg kell kezdenie az IoT hub metódus-hívásainak figyelését. 

2. Most, hogy az eszköz csatlakoztatva van, és a metódus meghívására vár, futtassa a .NET **TriggerReboot** alkalmazást a szimulált eszköz alkalmazás újraindítási módszerének meghívásához. Ehhez kattintson a jobb gombbal a **TriggerReboot** projektre, válassza a **hibakeresés**lehetőséget, majd válassza az **új példány indítása**lehetőséget. A "újraindítás!" üzenetnek kell megjelennie. a **SimulatedManagedDevice** -konzolban és az eszköz jelentett tulajdonságaiban szerepel, amely tartalmazza a **TriggerReboot** -konzolon írt utolsó újraindítási időt.
   
    ![Szolgáltatás-és eszköz-alkalmazás futtatása](./media/iot-hub-csharp-csharp-device-management-get-started/combinedrun.png)

[!INCLUDE [iot-hub-dm-followup](../../includes/iot-hub-dm-followup.md)]
