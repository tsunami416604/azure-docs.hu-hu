---
title: Kapcsolat egy málna Pi az Azure IoT központi alkalmazáshoz (C#) |} Microsoft Docs
description: Egy eszköz fejlesztőjeként egy málna Pi csatlakoztatása az Azure IoT központi alkalmazás használatával C#.
author: dominicbetts
ms.author: dobett
ms.date: 01/22/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: timlt
ms.openlocfilehash: 58f363c522f3e5abe6bf49a2aebafe4e953e00df
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34628589"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Egy málna Pi csatlakozni az Azure IoT központi alkalmazás (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Ez a cikk ismerteti, hogyan eszköz fejlesztőként egy málna Pi kapcsolódni a Microsoft Azure IoT központi alkalmazáshoz, a C# programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A cikkben leírt lépések elvégzéséhez a következőkre lesz szüksége:

* [A .NET core 2](https://www.microsoft.com/net) a fejlesztői gépen telepítve van. A megfelelő kód szerkesztése is rendelkezik, mint [Visual Studio Code](https://code.visualstudio.com/).
* A létrehozott Azure IoT központi alkalmazást a **minta Devkits** alkalmazássablon. További információkért lásd: [létrehozása az Azure IoT központi alkalmazás](howto-create-application.md).
* A Raspbian operációs rendszert futtató málna Pi eszköz.

A létrehozott alkalmazást, a **minta Devkits** alkalmazás sablon tartalmaz egy **málna Pi** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| nedvességtartalma       | %      | 0       | 100     | 0              |
| TEMP           | ° C     | tartsuk ott -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | az mg     | -2000   | 2000    | 0              |
| accelerometerY | az mg     | -2000   | 2000    | 0              |
| accelerometerZ | az mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Ventilátor sebessége    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mező neve | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| INFRAVÖRÖS           | activateIR | ON      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Típus            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköz tulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | –       |

### <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT központi-alkalmazás hozzáadása a valódi eszközről a **málna Pi** eszköz sablont, és jegyezze fel az eszköz kapcsolati karakterlánc. További információkért lásd: [valós eszköz hozzáadása az Azure IoT központi alkalmazás](tutorial-add-device.md).

## <a name="create-your-net-application"></a>A .NET-alkalmazás létrehozása

Hozzon létre, és az eszköz alkalmazás tesztelése a asztali számítógépen.

Az alábbi lépésekkel, a Visual Studio Code is használhat. További információkért lásd: [működik-e a C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Ha jobban szeret, befejezheti az alábbi lépéseket egy másik kódot szerkesztő segítségével.

1. A .NET-projekt inicializálása, és adja hozzá a szükséges NuGet-csomagokat, a következő parancsokat:

  ```cmd/sh
  mkdir pisample
  cd pisample
  dotnet new console
  dotnet add package Microsoft.Azure.Devices.Client
  dotnet restore
  ```

1. Nyissa meg a `pisample` Visual Studio Code mappájában. Nyissa meg a **pisample.csproj** projektfájlt. Adja hozzá a `<RuntimeIdentifiers>` címke a következő kódrészletben látható:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.5.2" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > A **Microsoft.Azure.Devices.Client** lehet, hogy a csomag verziószáma magasabb, mint a fentit is.

1. Mentés **pisample.csproj**. Ha a Visual Studio Code kéri, hogy a visszaállítási parancs hajtható végre, válassza a **visszaállítása**.

1. Nyissa meg **Program.cs** , és cserélje ki annak tartalmát az alábbira:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;

    using Microsoft.Azure.Devices.Client;
    using Microsoft.Azure.Devices.Shared;
    using Newtonsoft.Json;

    namespace pisample
    {
      class Program
      {
        static string DeviceConnectionString = "{your device connection string}";
        static DeviceClient Client = null;
        static TwinCollection reportedProperties = new TwinCollection();
        static CancellationTokenSource cts;
        static double baseTemperature = 60;
        static double basePressure = 500;
        static double baseHumidity = 50;
        static void Main(string[] args)
        {
          Console.WriteLine("Raspberry Pi Azure IoT Central example");

          try
          {
            InitClient();
            SendDeviceProperties();

            cts = new CancellationTokenSource();
            SendTelemetryAsync(cts.Token);

            Console.WriteLine("Wait for settings update...");
            Client.SetDesiredPropertyUpdateCallbackAsync(HandleSettingChanged, null).Wait();
            Console.ReadKey();
            cts.Cancel();
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static void InitClient()
        {
          try
          {
            Console.WriteLine("Connecting to hub");
            Client = DeviceClient.CreateFromConnectionString(DeviceConnectionString, TransportType.Mqtt);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        public static async void SendDeviceProperties()
        {
          try
          {
            Console.WriteLine("Sending device properties:");
            Random random = new Random();
            TwinCollection telemetryConfig = new TwinCollection();
            reportedProperties["dieNumber"] = random.Next(1, 6);
            Console.WriteLine(JsonConvert.SerializeObject(reportedProperties));

            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static async void SendTelemetryAsync(CancellationToken token)
        {
          try
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
          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Intentional shutdown: {0}", ex.Message);
          }
        }

        private static async Task HandleSettingChanged(TwinCollection desiredProperties, object userContext)
        {
          try
          {
            Console.WriteLine("Received settings change...");
            Console.WriteLine(JsonConvert.SerializeObject(desiredProperties));

            string setting = "fanSpeed";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setVoltage";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "setCurrent";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            setting = "activateIR";
            if (desiredProperties.Contains(setting))
            {
              // Act on setting change, then
              AcknowledgeSettingChange(desiredProperties, setting);
            }
            await Client.UpdateReportedPropertiesAsync(reportedProperties);
          }

          catch (Exception ex)
          {
            Console.WriteLine();
            Console.WriteLine("Error in sample: {0}", ex.Message);
          }
        }

        private static void AcknowledgeSettingChange(TwinCollection desiredProperties, string setting)
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

    > [!NOTE]
    > A helyőrző frissítenie `{your device connection string}` a következő lépésben.

## <a name="run-your-net-application"></a>A .NET-alkalmazás futtatása

Az eszközre vonatkozó kapcsolati karakterlánc hozzáadása a kódot az eszköz az Azure IoT központi való hitelesítéshez szükséges. Feljegyezte ezt a kapcsolati karakterláncot, a valós eszközt az Azure IoT központi alkalmazáshoz való hozzáadásakor.

1. Cserélje le `{your device connection string}` a a **Program.cs** korábban feljegyzett kapcsolati karakterlánccal rendelkező fájl.

1. A következő parancsot a parancssori környezetben:

  ```cmd/sh
  dotnet restore
  dotnet publish -r linux-arm
  ```

1. Másolás a `pisample\bin\Debug\netcoreapp2.0\linux-arm\publish` málna Pi eszközét mappát. Használhatja a **scp** parancs a másolást, például:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    További információkért lásd: [málna Pi távelérési](https://www.raspberrypi.org/documentation/remote-access/).

1. Jelentkezzen be az málna Pi eszközt, és futtassa az alábbi parancsokat a rendszerhéj:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. A málna Pi futtassa a következő parancsokat:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Megkezdése](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Az Azure IoT központi alkalmazás láthatja, hogy a kód a málna Pi futó hogyan működjön együtt az alkalmazás:

    * Az a **mérések** lap az valós eszközhöz, lásd: a telemetriai adatokat.
    * A a **tulajdonságok** lapon megtekintheti a jelentett értékének **Die szám** tulajdonság.
    * Az a **beállítások** lapon módosíthatja a málna Pi feszültség és ventilátor sebesség például különböző beállításait.

    Az alábbi képernyőfelvételen látható a málna Pi fogadása a beállítás módosítása:

    ![Raspberry Pi fogad beállítás módosítása](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="next-steps"></a>További lépések

Most, hogy rendelkezik megismerte a málna Pi csatlakoztatása az Azure IoT központi alkalmazás, az alábbiakban a javasolt lépéseket:

* [Általános Node.js ügyfélalkalmazást Azure IoT központi csatlakozás](howto-connect-nodejs.md)