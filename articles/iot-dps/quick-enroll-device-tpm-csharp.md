---
title: 'TPM-eszköz regisztrálása az Azure-eszközkiépítési szolgáltatásba C használatával #'
description: Rövid útmutató – TPM-eszköz regisztrálása az Azure IoT Hub-eszközlétesítési szolgáltatás (DPS) c# szolgáltatás SDK használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: ee1b803459e0c81b86021b617a29e0b29ee19909
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "74976842"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Rövid útmutató: TPM-eszköz igénylése az IoT Hub-eszközkiépítési szolgáltatásba a C# szolgáltatás SDK használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ez a cikk bemutatja, hogyan hozhat létre programozott módon egy TPM-eszköz egyéni regisztrációját az Azure IoT Hub-eszközkiépítési szolgáltatásban a [C# Service SDK](https://github.com/Azure/azure-iot-sdk-csharp) és egy minta C# .NET Core alkalmazás használatával. Ehhez az egyéni regisztrációs bejegyzéshez szükség esetén regisztrálhat egy szimulált TPM-eszközt a létesítési szolgáltatásba. Bár ezek a lépések Windows és Linux rendszerű számítógépeken is működnek, ez a cikk Windows fejlesztői számítógépet használ.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

1. Ellenőrizze, hogy telepítve van-e a [Visual Studio 2019](https://www.visualstudio.com/vs/) a számítógépen.

1. Ellenőrizze, hogy telepítve van-e a [.NET Core SDK](https://www.microsoft.com/net/download/windows) a számítógépen.

1. A folytatás előtt [hajtsa végre az IoT Hub-eszközkiépítési szolgáltatás](./quick-setup-auto-provision.md) beállítása az Azure Portalon című lépéseit.

1. (Nem kötelező) Ha egy szimulált eszközt szeretne felvenni a rövid útmutató végén, kövesse a [Szimulált TPM-eszköz létrehozása és üzembe létesítése C# eszköz SDK használatával](quick-create-simulated-device-tpm-csharp.md) című részt, egészen a lépésig, ahol az eszköz ellenőrzőkulcsot kap. Mentse az ellenőrzőkulcsot, a regisztrációs azonosítót és adott esetben az eszközazonosítót, mert a rövid útmutató későbbi részében használnia kell őket.

   > [!NOTE]
   > Ne kövesse az egyéni regisztráció hozlétre egyéni regisztráció az Azure Portal használatával.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Jelentkezzen be az Azure Portalon, válassza az **Összes erőforrást,** majd az eszközkiépítési szolgáltatást.

1. Válassza **a Megosztott hozzáférési házirendek**lehetőséget, majd jelölje ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. Az **Access Policy programban**másolja és mentse az elsődleges kulcs kapcsolati karakterláncát.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre egy .NET Core konzolalkalmazást, amely hozzáadja a TPM-eszköz egyéni regisztrációját a létesítési szolgáltatáshoz. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is az egyéni regisztráció hozzáadásához. Ha többet szeretne tudni az IoT Core-ral való fejlesztésről, olvassa el a [Windows IoT Core fejlesztői dokumentációját.](https://docs.microsoft.com/windows/iot-core/)

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Core)** projektsablont a C# számára, és válassza a **Tovább**gombot.

1. Nevezze el a *projectCreateTpmEnrollment*projektet, és nyomja **le a Create billentyűt.**

    ![Visual C# Windows Classic Desktop projekt konfigurálása](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Amikor a megoldás megnyílik a Visual Studióban, a **Megoldáskezelő** ablaktáblán kattintson a jobb gombbal a **CreateTpmEnrollment** projektre. Válassza **a NuGet-csomagok kezelése lehetőséget.**

1. A **NuGet csomagkezelőben**válassza a **Tallózás**lehetőséget, keresse meg a **Microsoft.Azure.Devices.Provisioning.Service**elemet, majd nyomja le a **Telepítés**gombot.

   ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az [Azure IoT-kiépítési szolgáltatás ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet csomag és a függőségek.

1. A többi `using` `using` állítás után a következő állításokat `Program.cs`kell felsorasztani:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adja hozzá a `Program` következő mezőket az osztályhoz az alábbi módosítások végrehajtása érdekében.

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private const string RegistrationId = "sample-registrationid-csharp";
   private const string TpmEndorsementKey =
       "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
       "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
       "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
       "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
       "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
       
   // Optional parameters
   private const string OptionalDeviceId = "myCSharpDevice";
   private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
   ```

   * Cserélje `ProvisioningServiceConnectionString` le a helyőrző értéket annak a létesítési szolgáltatásnak a kapcsolati karakterláncára, amelyhez a regisztrációt létre kívánja hozni.

   * A regisztrációs azonosító, az ellenőrzőkulcs, az eszközazonosító és a regisztrációs állapot igény szerint módosítható.

   * Ha ezt a rövid útmutatót használja a [Létrehozása és kiépítése egy szimulált TPM-eszköz C# eszköz SDK](quick-create-simulated-device-tpm-csharp.md) rövid útmutató használatával egy szimulált eszköz kiépítése, cserélje le az ellenőrző kulcsot és a regisztrációs azonosítót a rövid útmutatóban megadott értékeket. Lecserélheti az eszközazonosítót a rövid útmutatóban javasolt értékre, használhatja a saját értékét, vagy használhatja a mintában szereplő alapértelmezett értéket.

1. Adja hozzá a `Program` következő módszert az osztályhoz.  Ez a kód létrehozza az `CreateOrUpdateIndividualEnrollmentAsync` egyéni beléptetési bejegyzést, majd meghívja a metódust, `ProvisioningServiceClient` hogy hozzáadja az egyéni beléptetést a létesítési szolgáltatáshoz.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");

       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new individualEnrollment config
           Console.WriteLine("\nCreating a new individualEnrollment...");
           Attestation attestation = new TpmAttestation(TpmEndorsementKey);
           IndividualEnrollment individualEnrollment =
                   new IndividualEnrollment(
                           RegistrationId,
                           attestation);

           // The following parameters are optional. Remove them if you don't need them.
           individualEnrollment.DeviceId = OptionalDeviceId;
           individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
           #endregion

           #region Create the individualEnrollment
           Console.WriteLine("\nAdding new individualEnrollment...");
           IndividualEnrollment individualEnrollmentResult =
               await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
           Console.WriteLine("\nIndividualEnrollment created with success.");
           Console.WriteLine(individualEnrollmentResult);
           #endregion
        
       }
   }
   ```

1. Végül cserélje ki a `Main` módszer törzsét a következő sorokra:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Hozza létre a megoldást.

## <a name="run-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta futtatása
  
Futtassa a mintát a Visual Studióban a TPM-eszköz egyéni regisztrációjának létrehozásához.

Megjelenik egy parancssori ablak, és elkezdi megjeleníteni a megerősítő üzeneteket. A sikeres létrehozáskor a Parancssor ablak megjeleníti az új egyéni regisztráció tulajdonságait.

Ellenőrizheti, hogy az egyéni regisztráció létrejött-e. Nyissa meg az Eszközkiépítési szolgáltatás összegzését, és válassza **a Regisztrációk kezelése**lehetőséget, majd válassza az **Egyéni beléptetések**lehetőséget. Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Válassza ki a bejegyzést az ellenőrzőkulcs és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

Ha követte a lépéseket a [létrehozása és kiépítése egy szimulált TPM-eszköz c# eszköz SDK](quick-create-simulated-device-tpm-csharp.md) rövid útmutató használatával, folytathatja a további lépéseket, hogy a rövid útmutató a szimulált eszköz regisztrálása. Hagyja ki azon lépéseket, amelyek az egyéni regisztrációk Azure Portallal való létrehozására vonatkoznak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja meg a C# szolgáltatás minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben az alábbi lépésekkel törölje a rövid útmutató által létrehozott összes erőforrást.

1. Zárja be a C# minta kimeneti ablakot a számítógépen.

1. Keresse meg az Eszközkiépítési szolgáltatást az Azure Portalon, válassza a **Regisztrációk kezelése**lehetőséget, majd *Registration ID* jelölje be az **Egyéni beléptetések** lapot. **Delete**

1. Ha követte a [Szimulált TPM-eszköz létrehozása és üzembe létesítése c# sdk-eszközzel](quick-create-simulated-device-tpm-csharp.md) szimulált TPM-eszköz létrehozásához című lépéseit, tegye a következő lépéseket:

    1. Zárja be a TPM-szimulátor ablakát és a szimulált eszköz mintakódkimeneti ablakát.

    1. Az Azure Portalon lépjen ahhoz az IoT Hubhoz, ahol regisztrálta az eszközt. Az **Intézők**menüben jelölje be az **IoT-eszközök**lehetőséget, jelölje be a rövid útmutatóban regisztrált eszköz *eszközazonosítója* melletti jelölőnégyzetet, majd nyomja meg a **Törlés** gombot az ablaktábla tetején.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban programozott módon létrehozott egy egyéni regisztrációs bejegyzést egy TPM-eszközhöz. Szükség esetén létrehozott egy TPM-szimulált eszközt a számítógépén, és kiépítette azt az IoT hubba az Azure IoT Hub-eszközkiépítési szolgáltatás használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
