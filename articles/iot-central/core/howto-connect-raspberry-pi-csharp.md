---
title: Málna PI összekötése az Azure IoT Central alkalmazással (C#) | Microsoft Docs
description: Eszköz-fejlesztőként, hogyan csatlakoztatható a málna PI az Azure IoT Central-alkalmazáshoz a használatával C#.
author: viv-liu
ms.author: viviali
ms.date: 09/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: ba903d75707be91bb8af1271b52eb260ffcde306
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951236"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Málna PI összekötése az Azure IoT Central alkalmazással (C#)

[!INCLUDE [howto-raspberrypi-selector](../../../includes/iot-central-howto-raspberrypi-selector.md)]

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Ez a cikk azt ismerteti, hogyan lehet az eszköz fejlesztője a málna PI-t a Microsoft Azure IoT Central alkalmazáshoz a C# programozási nyelv használatával.

## <a name="before-you-begin"></a>Előzetes teendők

A cikkben szereplő lépések végrehajtásához a következő összetevőkre van szükség:

* A **mintául szolgáló Devkits** létrehozott Azure IoT Central-alkalmazás. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* A Raspbian operációs rendszert futtató málna PI-eszköz. A málna PI-nek képesnek kell lennie az internethez való kapcsolódásra. További információ: [a málna PI beállítása](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Példa Devkits** alkalmazásra

A **mintául szolgáló Devkits** létrehozott alkalmazások egy **málna PI** -eszközt tartalmaznak a következő jellemzőkkel:

* Telemetria, amely a következő méréseket tartalmazza, amelyeket az eszköz gyűjt:
  * Páratartalom
  * Hőmérséklet
  * Nyomás
  * Magnetometer (X, Y, Z)
  * Gyorsulásmérő (X, Y, Z)
  * Giroszkóp (X, Y, Z)
* Beállítások
  * Feszültség
  * Aktuális
  * Ventilátor sebessége
  * IR-váltógomb
* Tulajdonságok
  * Die Number Device tulajdonság
  * Location Cloud tulajdonság

Az eszköz sablon konfigurációjának részletes ismertetését lásd: [málna PI-eszköz sablonjának részletei](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central alkalmazásban vegyen fel egy valódi eszközt a **málna PI** -eszköz sablonból. Jegyezze fel az eszköz kapcsolatának részleteit (a**hatókör azonosítóját**, az **eszköz azonosítóját**és az **elsődleges kulcsot**). További információ: Real- [eszköz hozzáadása az Azure IoT Central alkalmazáshoz](tutorial-add-device.md).

### <a name="create-your-net-application"></a>.NET-alkalmazás létrehozása

Az eszköz alkalmazást az asztali gépen hozza létre és teszteli.

A következő lépések végrehajtásához használhatja a Visual Studio Code-ot. További információ: [Working with C# ](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Ha szeretné, a következő lépéseket elvégezheti egy másik Kódszerkesztő használatával.

1. A .NET-projekt inicializálásához és a szükséges NuGet-csomagok hozzáadásához futtassa a következő parancsokat:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Client
   dotnet add package Microsoft.Azure.Devices.Provisioning.Transport.Mqtt
   dotnet restore
   ```

1. Nyissa meg a `pisample` mappát a Visual Studio Code-ban. Ezután nyissa meg a **pisample. csproj** projektfájlt. Adja hozzá az alábbi kódrészletben látható `<RuntimeIdentifiers>` címkét:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.1</TargetFramework>
        <RootNamespace>pisample</RootNamespace>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.21.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Client" Version="1.4.0" />
        <PackageReference Include="Microsoft.Azure.Devices.Provisioning.Transport.Mqtt" Version="1.1.8" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > Előfordulhat, hogy a csomag verziószáma nagyobb, mint a megjelenített értékek.

1. Mentse a **pisample. csproj**. Ha a Visual Studio Code arra kéri, hogy hajtsa végre a Restore parancsot, válassza a **visszaállítás**lehetőséget.

1. Nyissa meg a **program.cs** , és cserélje le a tartalmát a következő kódra. Frissítse a `{your Scope ID}`, `{your Device ID}`és `{your Device Primary Key}` értékeket a korábban jegyzett értékekkel:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Microsoft.Azure.Devices.Provisioning.Client;
    using Microsoft.Azure.Devices.Provisioning.Client.Transport;

    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string ScopeID = "{your Scope ID}";
        static string DeviceID = "{your Device ID}";
        static string PrimaryKey = "{your Device Primary Key}";
        static string GlobalDeviceEndpoint = "global.azure-devices-provisioning.net";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;

        static async Task Main(string[] args)
        {
          Console.WriteLine("== Raspberry Pi Azure IoT Central example ==");

          try
          {

            using (var security = new SecurityProviderSymmetricKey(DeviceID, PrimaryKey, null))
            {
              DeviceRegistrationResult result = await RegisterDeviceAsync(security);
              if (result.Status != ProvisioningRegistrationStatusType.Assigned) {
                Console.WriteLine("Failed to register device");
                return;
              }
              IAuthenticationMethod auth = new DeviceAuthenticationWithRegistrySymmetricKey(result.DeviceId, (security as SecurityProviderSymmetricKey).GetPrimaryKey());
              Client = DeviceClient.Create(result.AssignedHub, auth, TransportType.Mqtt);
            }

            await SendDevicePropertiesAsync();

            Console.Write("Register settings changed handler...");
            await Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null);
            Console.WriteLine("Done");

            cts = new CancellationTokenSource();
            Task task = SendTelemetryAsync(cts.Token);

            Console.WriteLine("Press any key to exit...");
            Console.ReadKey();
            cts.Cancel();
            await task;
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine(ex.Message);
          }
        }

        public static async Task<DeviceRegistrationResult> RegisterDeviceAsync(SecurityProviderSymmetricKey security)
        {
            Console.WriteLine("Register device...");

            using (var transport = new ProvisioningTransportHandlerMqtt(TransportFallbackType.TcpOnly))
            {
              ProvisioningDeviceClient provClient =
                        ProvisioningDeviceClient.Create(GlobalDeviceEndpoint, ScopeID, security, transport);

              Console.WriteLine($"RegistrationID = {security.GetRegistrationID()}");


              Console.Write("ProvisioningClient RegisterAsync...");
              DeviceRegistrationResult result = await provClient.RegisterAsync();

              Console.WriteLine($"{result.Status}");
              Console.WriteLine($"ProvisioningClient AssignedHub: {result.AssignedHub}; DeviceID: {result.DeviceId}");

              return result;
            }
        }

        public static async Task SendDevicePropertiesAsync()
        {
            Console.WriteLine("Send device properties...");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static async Task SendTelemetryAsync(CancellationToken token)
        {
          Random rand = new Random();

          while (true)
          {
            double currentTemperature = baseTemperature + rand.NextDouble() * 20;
            double currentPressure = basePressure + rand.NextDouble() * 100;
            double currentHumidity = baseHumidity + rand.NextDouble() * 20;

            var telemetryDataPoint = new
            {
              humidity = currentHumidity,
              pressure = currentPressure,
              temp = currentTemperature
            };
            var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
            var message = new Message(Encoding.ASCII.GetBytes(messageString));

            token.ThrowIfCancellationRequested();
            await Client.SendEventAsync(message);

            Console.WriteLine("{0} > Sending telemetry: {1}", DateTime.Now, messageString);

            await Task.Delay(1000);
          }
        }


        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          Console.WriteLine("Received settings change...");
          Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

          string setting = "fanSpeed";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setVoltage";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "setCurrent";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          setting = "activateIR";
          if (desiredProperties.Contains(setting))
          {
            // Act on setting change, then
            BuildAcknowledgement(desiredProperties, setting);
          }
          Console.WriteLine("Send settings changed acknowledgement...");
          await Client.UpdateReportedPropertiesAsync(reportedProperties);
        }

        private static void BuildAcknowledgement(TwinCollection desiredProperties, string setting)
        {
          reportedProperties[setting] = new
          {
            value = desiredProperties[setting]["value"],
            status = "completed",
            desiredVersion = desiredProperties["$version"],
            message = "Processed"
          };
        }
      }
    }
    ```

## <a name="run-your-net-application"></a>.NET-alkalmazás futtatása

A minta alkalmazás létrehozása és futtatása:

1. Futtassa a következő parancsot a parancssori környezetben:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Másolja a `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` mappát a málna PI-eszközre. A **szolgáltatáskapcsolódási** pont parancs használatával másolhatja a fájlokat, például:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    További információ: [málna PI-távelérés](https://www.raspberrypi.org/documentation/remote-access/).

1. Jelentkezzen be a málna PI-eszközbe, és futtassa a következő parancsokat egy rendszerhéjban:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. A málna PI-ben futtassa a következő parancsokat:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![A program elindul](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Az Azure IoT Central alkalmazásban láthatja, hogy a málna PI-on futó kód hogyan működik együtt az alkalmazással:

   * A valós eszköz **mérések** lapján megtekintheti a telemetria.
   * A **Tulajdonságok** lapon látható a jelentett **Die Number** tulajdonság értéke.
   * A **Beállítások** lapon megváltoztathatja a málna PI különböző beállításait, például a feszültséget és a ventilátor sebességét.

     Az alábbi képernyőfelvételen a beállítás módosítását fogadó málna PI látható:

     ![A málna PI a beállítás módosítását fogadja](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Málna PI-eszköz sablonjának részletei

A **mintául szolgáló Devkits** létrehozott alkalmazások egy **málna PI** -eszközt tartalmaznak a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| ideiglenes           | °C     | – 40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | – 1000   | 1000    | 0              |
| magnetometerY  | mgauss | – 1000   | 1000    | 0              |
| magnetometerZ  | mgauss | – 1000   | 1000    | 0              |
| accelerometerX | mg     | – 2000   | 2000    | 0              |
| gyorsulásmérő | mg     | – 2000   | 2000    | 0              |
| accelerometerZ | mg     | – 2000   | 2000    | 0              |
| gyroscopeX     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeY     | mdps   | – 2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | – 2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállítások

| Megjelenített név | Mező neve | egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültség      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Erősítők  | 0              | 0       | 100     | 0       |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

Váltási beállítások

| Megjelenített név | Mező neve | Szövegen | Kikapcsolt szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | KIKAPCSOLÁSA      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Type (Típus)            | Megjelenített név | Mező neve | Data type                              |
| --------------- | ------------ | ---------- | -------------------------------------- |
| Eszköz tulajdonsága | Die száma   | dieNumber  | szám                                 |
| Földrajzi egység        | Földrajzi egység     | location   | {Lat: float, Long: float, Alt?: float} |

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan csatlakoztatható a málna PI az Azure IoT Central-alkalmazáshoz, a javasolt következő lépés az, hogy megtudja, hogyan [állíthat be egyéni](howto-set-up-template.md) IoT-eszközt a saját eszközökhöz.
