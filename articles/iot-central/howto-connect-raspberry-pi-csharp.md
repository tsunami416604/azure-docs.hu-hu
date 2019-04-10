---
title: Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (C#) |} A Microsoft Docs
description: Eszköz a fejlesztők hogyan Raspberry Pi csatlakozhat az Azure IoT Central alkalmazást a C#.
author: viv-liu
ms.author: viviali
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 341b4d23664900cdf1f9a209df663ad4e6e96fe4
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426358"
---
# <a name="connect-a-raspberry-pi-to-your-azure-iot-central-application-c"></a>Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz (C#)

[!INCLUDE [howto-raspberrypi-selector](../../includes/iot-central-howto-raspberrypi-selector.md)]

Ez a cikk azt ismerteti, hogyan eszköz a fejlesztők Raspberry Pi kapcsolódni a Microsoft Azure IoT Central alkalmazáshoz C# programozási nyelv használatával.

## <a name="before-you-begin"></a>Előkészületek

A jelen cikkben ismertetett lépések végrehajtásához szüksége van a következő összetevők:

* A létrehozott Azure IoT Central alkalmazáshoz a **minta Devkits** alkalmazássablon. További információért lásd az [alkalmazás létrehozását bemutató rövid útmutatót](quick-deploy-iot-central.md).
* Raspberry Pi eszköz a Raspbian operációs rendszert. A Raspberry Pi kell rendelkezniük az internethez való kapcsolódáshoz. További információkért lásd: [beállítása a Raspberry Pi](https://projects.raspberrypi.org/en/projects/raspberry-pi-setting-up/3).

## <a name="sample-devkits-application"></a>**Minta Devkits** alkalmazás

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel:

- Telemetriai adatokat, amely tartalmazza az eszköz összegyűjti a következő mérési:
  - Páratartalom
  - Hőmérséklet
  - Pressure
  - Magnetométer (X, Y, a – Z)
  - Érzékelőből (X, Y, a – Z)
  - Giroszkóp (X, Y, a – Z)
- Beállítások
  - Feszültségérzékelő
  - Aktuális
  - Sebesség ventilátor
  - Integrációs modul váltógombot.
- Tulajdonságok
  - Eszköz-tulajdonságot die
  - Hely felhőalapú tulajdonság

A konfigurációs eszköz-sablon teljes részletekért lásd: a [Raspberry Pi eszköz sablon részleteinek](#raspberry-pi-device-template-details).

## <a name="add-a-real-device"></a>Valós eszköz hozzáadása

Az Azure IoT Central-alkalmazás hozzáadása a valós eszközöknek a **Raspberry Pi** eszköz sablont. Jegyezze fel az eszköz kapcsolati adatok (**hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs**). További információkért lásd: [valós eszköz hozzáadása az Azure IoT Central alkalmazásnak](tutorial-add-device.md).

### <a name="create-your-net-application"></a>A .NET-alkalmazás létrehozása

Hozzon létre, és az eszköz alkalmazás tesztelése a asztali gépén.

A következő lépéseket, használhatja a Visual Studio Code-ot. További információkért lásd: [használata a C#](https://code.visualstudio.com/docs/languages/csharp).

> [!NOTE]
> Ha szeretné, az alábbi lépéseket egy másik kódot-szerkesztő használatával is elvégezheti.

1. A .NET projekt inicializálása, és adja hozzá a szükséges NuGet-csomagok, futtassa a következő parancsokat:

   ```cmd/sh
   mkdir pisample
   cd pisample
   dotnet new console
   dotnet add package Microsoft.Azure.Devices.Client
   dotnet restore
   ```

1. Nyissa meg a `pisample` mappát a Visual Studio Code-ban. Nyissa meg a **pisample.csproj** soubor projektu. Adja hozzá a `<RuntimeIdentifiers>` címke az alábbi kódrészletben látható módon:

    ```xml
    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
        <RuntimeIdentifiers>win-arm;linux-arm</RuntimeIdentifiers>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Devices.Client" Version="1.19.0" />
      </ItemGroup>
    </Project>
    ```

    > [!NOTE]
    > A **Microsoft.Azure.Devices.Client** lehet, hogy a csomag verziószáma magasabb, mint meg.

1. Mentés **pisample.csproj**. Ha a Visual Studio Code kéri, hogy a restore parancs végrehajtása, válassza a **visszaállítása**.

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

Adja hozzá a kódot az eszköz hitelesítéséhez az Azure IoT Central eszközspecifikus kapcsolati karakterláncra. Ezeket az utasításokat követve [az eszköz kapcsolati karakterláncának létrehozása](howto-generate-connection-string.md) használatával a **hatókör azonosítója**, **Eszközazonosító**, és **elsődleges kulcs** történtek egy korábban vegye figyelembe a.

1. Cserélje le `{your device connection string}` a a **Program.cs** fájl létrehozott kapcsolati karakterlánccal.

1. Futtassa a következő parancsot a parancssori környezetben:

   ```cmd/sh
   dotnet restore
   dotnet publish -r linux-arm
   ```

1. Másolás a `pisample\bin\Debug\netcoreapp2.1\linux-arm\publish` mappát a Raspberry Pi-eszközre. Használhatja a **scp** parancs használatával másolja ki a fájlokat, például:

    ```cmd/sh
    scp -r publish pi@192.168.0.40:publish
    ```

    További információkért lásd: [Raspberry Pi távelérési](https://www.raspberrypi.org/documentation/remote-access/).

1. Jelentkezzen be a Raspberry Pi-eszközét, és futtassa a következő parancsokat egy rendszerhéjból a:

    ```cmd/sh
    sudo apt-get update
    sudo apt-get install libc6 libcurl3 libgcc1 libgssapi-krb5-2 liblttng-ust0 libstdc++6 libunwind8 libuuid1 zlib1g
    ```

1. A Raspberry Pi futtassa a következő parancsokat:

    ```cmd/sh
    cd publish
    chmod 777 pisample
    ./pisample
    ```

    ![Program elkezd](./media/howto-connect-raspberry-pi-csharp/device_begin.png)

1. Az Azure IoT Central-alkalmazás láthatja, hogy a kód a Raspberry Pi-on futó hogyan működjön együtt az alkalmazás:

   * Az a **mérések** lap a valós eszközhöz, tekintse meg a telemetriát.
   * Az a **tulajdonságok** lapon láthatja a jelentett értékét **Die szám** tulajdonság.
   * Az a **beállítások** lapon módosíthatja a Raspberry Pi feszültség és ventilátor sebesség például a különböző beállításait.

     Az alábbi képernyőfelvételen a Raspberry Pi fogad beállítás változása:

     ![Raspberry Pi fogad beállítás változása](./media/howto-connect-raspberry-pi-csharp/device_switch.png)

## <a name="raspberry-pi-device-template-details"></a>Raspberry Pi eszköz sablon részletei

A létrehozott alkalmazáshoz a **minta Devkits** alkalmazást sablon tartalmaz egy **Raspberry Pi** eszköz sablon a következő jellemzőkkel:

### <a name="telemetry-measurements"></a>Telemetria mérések

| Mező neve     | Egység  | Minimális | Maximum | Tizedeshelyek |
| -------------- | ------ | ------- | ------- | -------------- |
| páratartalom       | %      | 0       | 100     | 0              |
| TEMP           | °C     | -40     | 120     | 0              |
| pressure       | hPa    | 260     | 1260    | 0              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| accelerometerX | mg     | -2000   | 2000    | 0              |
| accelerometerY | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | mdps   | -2000   | 2000    | 0              |
| gyroscopeY     | mdps   | -2000   | 2000    | 0              |
| gyroscopeZ     | mdps   | -2000   | 2000    | 0              |

### <a name="settings"></a>Beállítások

Numerikus beállításai

| Megjelenített név | Mező neve | Egység | Tizedeshelyek | Minimális | Maximum | Kezdeti |
| ------------ | ---------- | ----- | -------------- | ------- | ------- | ------- |
| Feszültségérzékelő      | setVoltage | V | 0              | 0       | 240     | 0       |
| Aktuális      | setCurrent | Teljesítménytényező  | 0              | 0       | 100     | 0       |
| Sebesség ventilátor    | fanSpeed   | RPM   | 0              | 0       | 1000    | 0       |

A beállítások ki-/ bekapcsolása

| Megjelenített név | Mező neve | A szöveg | Ki a szöveg | Kezdeti |
| ------------ | ---------- | ------- | -------- | ------- |
| IR           | activateIR | ON      | KI      | Ki     |

### <a name="properties"></a>Tulajdonságok

| Typo            | Megjelenített név | Mező neve | Adattípus |
| --------------- | ------------ | ---------- | --------- |
| Eszköztulajdonság | Die száma   | dieNumber  | szám    |
| Szöveg            | Hely     | location   | –       |

## <a name="next-steps"></a>További lépések

Most, hogy megismerte a Raspberry Pi csatlakoztatása az Azure IoT Central alkalmazáshoz, Íme a javasolt következő lépések:

* [Egy általános Node.js ügyfél-alkalmazás csatlakoztatása az Azure IoT Central](howto-connect-nodejs.md)
