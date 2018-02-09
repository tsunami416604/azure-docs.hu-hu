## <a name="create-a-device-identity"></a>Eszközidentitás létrehozása

Ebben a szakaszban egy .NET-konzolalkalmazást fog létrehozni, amely egy eszközidentitást hoz létre az IoT Hub identitásjegyzékében. Egy eszköz csak akkor tud csatlakozni az IoT Hubhoz, ha be van jegyezve az identitásjegyzékbe. További információkért lásd az [IoT Hub fejlesztői útmutatójának][lnk-devguide-identity] „Identitásjegyzék” című szakaszát. A konzolalkalmazás egy egyedi eszközazonosítót és -kulcsot állít elő a futtatásakor. Ezekkel az értékekkel az eszköz azonosítani tudja magát, amikor az eszközről a felhőbe irányuló üzeneteket küld az IoT Hubnak. Az eszközazonosítók megkülönböztetik a kis- és nagybetűket.

1. A Visual Studióban adjon hozzá egy Visual C# Windows klasszikus asztalialkalmazás-projektet az új megoldáshoz a **Console App (.NET Framework)** (Konzolalkalmazás (.NET-keretrendszer)) projektsablonnal. A Microsoft .NET-keretrendszer 4.5.1-es vagy újabb verzióját használja. Adja a projektnek a **CreateDeviceIdentity** nevet, a megoldásnak pedig az **IoTHubGetStarted** nevet.

    ![Új Visual C# Windows klasszikus asztalialkalmazás-projekt][10]

1. A Solution Explorerben (Megoldáskezelőben) kattintson a jobb gombbal az **CreateDeviceIdentity** projektre, majd kattintson a **Manage NuGet Packages** (NuGet-csomagok kezelése) parancsra.

1. A **NuGet Package Manager** (NuGet-csomagkezelő) ablakban válassza a **Browse** (Tallózás) lehetőséget, keresse meg a **microsoft.azure.devices** csomagot, válassza a **Install** (Telepítés) lehetőséget a **Microsoft.Azure.Devices** csomag telepítéséhez, és fogadja el a használati feltételeket. Ez az eljárás letölti és telepíti az [Azure IoT Service SDK][lnk-nuget-service-sdk] (Azure IoT szolgáltatás SDK) NuGet-csomagot és annak függőségeit, valamint hozzáad egy rá mutató hivatkozást is.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak][11]

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:

    ```csharp
    using Microsoft.Azure.Devices;
    using Microsoft.Azure.Devices.Common.Exceptions;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét cserélje le az előző szakaszban létrehozott IoT Hub kapcsolati karakterláncra.

    ```csharp
    static RegistryManager registryManager;
    static string connectionString = "{iot hub connection string}";
    ```

1. Adja hozzá a **Program** osztályhoz a következő módszert:

    ```csharp
    private static async Task AddDeviceAsync()
    {
      string deviceId = "myFirstDevice";
      Device device;
      try
      {
          device = await registryManager.AddDeviceAsync(new Device(deviceId));
      }
      catch (DeviceAlreadyExistsException)
      {
          device = await registryManager.GetDeviceAsync(deviceId);
      }
      Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
    }
    ```

    Ez a metódus egy eszközidentitást hoz létre a **myFirstDevice** azonosítóval. (Ha ez az eszköz már létezik az identitásjegyzékben, a kód egyszerűen lekéri a meglévő eszközinformációkat.) Az alkalmazás ezután megjeleníti az identitáshoz tartozó elsődleges kulcsot. Ezt a kulcsot a szimulált eszközalkalmazásban használja az IoT Hubhoz való csatlakozáshoz.
[!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

1. Végül adja a következő sorokat a **Main** metódushoz:

    ```csharp
    registryManager = RegistryManager.CreateFromConnectionString(connectionString);
    AddDeviceAsync().Wait();
    Console.ReadLine();
    ```

1. Futtassa az alkalmazást, és jegyezze fel az eszköz kulcsát.

    ![Az alkalmazás által előállított eszközkulcs][12]

> [!NOTE]
> Az IoT Hub-identitásjegyzék csak az IoT Hub biztonságos elérésének biztosításához tárolja az eszközidentitásokat. Az identitásjegyzék tárolja az eszközazonosítókat és -kulcsot, és biztonsági hitelesítő adatokként használja őket. Az identitásjegyzék minden egyes eszközhöz tárol egy engedélyezve/letiltva jelzőt is, amellyel letilthatja az eszköz hozzáférését. Ha az alkalmazásnak más eszközspecifikus metaadatokat kell tárolnia, egy alkalmazásspecifikus tárolót kell használnia. További információkért lásd az [Azure IoT Hub fejlesztői útmutatóját][lnk-devguide-identity].

<!-- Images. -->
[10]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp1.png
[11]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp2.png
[12]: ./media/iot-hub-get-started-create-device-identity-csharp/create-identity-csharp3.png

<!-- Links -->
[lnk-devguide-identity]: ../articles/iot-hub/iot-hub-devguide-identity-registry.md
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
