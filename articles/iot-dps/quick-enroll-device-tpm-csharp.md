---
title: TPM-eszköz regisztrálása az Azure Device kiépítési szolgáltatásba a használatávalC#
description: Gyors útmutató – TPM-eszköz regisztrálása az C# Azure-IoT hub Device Provisioning Service a Service SDK használatával. Ez a rövid útmutató egyéni regisztrációkat használ.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: a95a50e5931f42e442e11fe593a151dd273449e8
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423003"
---
# <a name="quickstart-enroll-tpm-device-to-iot-hub-device-provisioning-service-using-c-service-sdk"></a>Gyors útmutató: TPM-eszköz regisztrálása C# IoT hub Device Provisioning Service a Service SDK használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-tpm](../../includes/iot-dps-selector-quick-enroll-device-tpm.md)]

Ez a cikk bemutatja, hogyan hozhat létre egyéni regisztrációt TPM-eszközhöz az Azure IoT hub Device Provisioning Service a [ C# Service SDK](https://github.com/Azure/azure-iot-sdk-csharp) és egy minta C# .net Core-alkalmazás használatával. Ezt az egyéni beléptetési bejegyzést használva igény szerint regisztrálhat egy szimulált TPM-eszközt a kiépítési szolgáltatásba. Habár ezek a lépések a Windows és a Linux rendszerű számítógépeken is működnek, ez a cikk egy Windows rendszerű fejlesztési számítógépet használ.

## <a name="prepare-the-development-environment"></a>A fejlesztési környezet előkészítése

1. Győződjön meg arról, hogy a [Visual Studio 2019](https://www.visualstudio.com/vs/) telepítve van a számítógépen.

1. Ellenőrizze, hogy telepítve van-e a [.net Core SDK](https://www.microsoft.com/net/download/windows) a számítógépen.

1. A folytatás előtt végezze el a [IoT hub Device Provisioning Service beállítása a Azure Portalban](./quick-setup-auto-provision.md) című témakör lépéseit.

1. Választható Ha a rövid útmutató végén szeretne regisztrálni egy szimulált eszközt, kövesse a [szimulált TPM- C# eszköz létrehozása és kiépítése az eszköz SDK](quick-create-simulated-device-tpm-csharp.md) -val című szakaszt a következő lépésre, ahol az eszközhöz tartozó záradékot kap. Mentse a jóváhagyó kulcsot, a regisztrációs azonosítót, és opcionálisan az eszköz AZONOSÍTÓját, mert később a rövid útmutatóban kell használni őket.

   > [!NOTE]
   > Ne kövesse az egyéni regisztráció létrehozásához szükséges lépéseket a Azure Portal használatával.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Jelentkezzen be a Azure Portalba, válassza a **minden erőforrás**lehetőséget, majd az eszköz kiépítési szolgáltatását.

1. Válassza a **megosztott hozzáférési házirendek**lehetőséget, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. A **hozzáférési házirendben**másolja és mentse az elsődleges kulcs kapcsolati karakterláncát.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-tpm-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta létrehozása

Ebből a szakaszból megtudhatja, hogyan hozhat létre egy olyan .NET Core Console-alkalmazást, amely egy TPM-eszköz egyéni regisztrációját hozzáadja a kiépítési szolgáltatáshoz. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is az egyéni regisztráció hozzáadásához. Ha többet szeretne megtudni a IoT Core-fejlesztéssel kapcsolatban, tekintse meg a [Windows IoT Core fejlesztői dokumentációját](https://docs.microsoft.com/windows/iot-core/).

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget. A **create a New Project (új projekt létrehozása**) területen válassza ki a **Console app (.net Core)** projekt sablonját, C# és kattintson a **Tovább gombra**.

1. Nevezze el a projekt *CreateTpmEnrollment*, majd kattintson a **Létrehozás**gombra.

    ![A Visual C# Windows klasszikus asztali projekt konfigurálása](media/quick-enroll-device-tpm-csharp/configure-tpm-app-vs2019.png)

1. Amikor a megoldás megnyílik a Visual Studióban, a **megoldáskezelő** ablaktáblán kattintson a jobb gombbal a **CreateTpmEnrollment** projektre. Válassza a **NuGet-csomagok kezelése**lehetőséget.

1. A **NuGet csomagkezelő**területén válassza a **Tallózás**elemet, keresse meg és válassza a **Microsoft. Azure. Devices. kiépítés. szolgáltatás**elemet, majd kattintson a **telepítés**gombra.

   ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-tpm-csharp/add-nuget.png)

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT kiépítési szolgáltatás ÜGYFÉLOLDALI SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) -NuGet csomagjának és függőségeinek hivatkozását.

1. Adja hozzá a következő `using` utasításokat a `Program.cs`tetején található többi `using` utasítás után:
  
   ```csharp
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adja hozzá a következő mezőket a `Program` osztályhoz, így az alább felsorolt módosításokat.

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

   * Cserélje le a `ProvisioningServiceConnectionString` helyőrző értékét azon kiépítési szolgáltatáshoz tartozó kapcsolódási karakterláncra, amelyhez a regisztrációt létre szeretné hozni.

   * A regisztrációs azonosító, az ellenőrzőkulcs, az eszközazonosító és a regisztrációs állapot igény szerint módosítható.

   * Ha ezt a rövid útmutatót a [szimulált TPM- C# eszköz létrehozásával és](quick-create-simulated-device-tpm-csharp.md) kiépítésével együtt használja az eszközoldali SDK-val a szimulált eszköz kiépítéséhez, cserélje le a hátirat kulcsot és a regisztrációs azonosítót az ebben a rövid útmutatóban feljegyzett értékekre. Az eszköz AZONOSÍTÓját lecserélheti a rövid útmutatóban javasolt értékre, használhatja a saját értékét, vagy használhatja a minta alapértelmezett értékét.

1. Adja hozzá a következő metódust a `Program` osztályhoz.  Ez a kód egyéni beléptetési bejegyzést hoz létre, majd meghívja a `CreateOrUpdateIndividualEnrollmentAsync` metódust a `ProvisioningServiceClient`, hogy hozzáadja az egyéni regisztrációt a kiépítési szolgáltatáshoz.

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

1. Végül cserélje le a `Main` metódus törzsét a következő sorokra:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Hozza létre a megoldást.

## <a name="run-the-individual-enrollment-sample"></a>Az egyéni regisztrációs minta futtatása
  
Futtassa a mintát a Visual Studióban a TPM-eszköz egyéni regisztrációjának létrehozásához.

Ekkor megjelenik egy parancssori ablak, amely megerősítő üzeneteket jelenít meg. A sikeres létrehozáskor a parancssori ablak megjeleníti az új egyéni regisztráció tulajdonságait.

Ellenőrizheti, hogy létrejött-e az egyéni regisztráció. Nyissa meg az eszköz kiépítési szolgáltatásának összegzését, és válassza a **regisztrációk kezelése**, majd az **Egyéni regisztrációk**lehetőséget. Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-tpm-csharp/verify-enrollment-portal-vs2019.png)

Válassza ki a bejegyzést a jóváhagyás kulcsának és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

Ha követte a [szimulált TPM- C# eszköz létrehozása és kiépítése](quick-create-simulated-device-tpm-csharp.md) az eszközoldali SDK-val rövid útmutató lépéseit, folytassa a rövid útmutató további lépéseit a szimulált eszköz regisztrálásához. Hagyja ki azon lépéseket, amelyek az egyéni regisztrációk Azure Portallal való létrehozására vonatkoznak.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja C# meg a szolgáltatási mintát, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárjuk be C# a minta kimeneti ablakát a számítógépen.

1. Navigáljon a Azure Portal eszköz kiépítési szolgáltatásához, válassza a regisztrációk **kezelése**lehetőséget, majd válassza az **Egyéni regisztrációk** fület. jelölje be az ebben a rövid útmutatóban létrehozott beléptetési bejegyzéshez tartozó *regisztrációs azonosító* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

1. Ha követte a [szimulált TPM-eszköz létrehozása és kiépítése az ESZKÖZOLDALI C# SDK használatával](quick-create-simulated-device-tpm-csharp.md) című részt a szimulált TPM-eszköz létrehozásához, hajtsa végre a következő lépéseket:

    1. Zárja be a TPM-szimulátor ablakát és a szimulált eszköz mintakódkimeneti ablakát.

    1. Az Azure Portalon lépjen ahhoz az IoT Hubhoz, ahol regisztrálta az eszközt. Az **Intézők**alatt található menüben válassza a **IoT eszközök**elemet, jelölje be az ebben a rövid ÚTMUTATÓban regisztrált eszköz *azonosítójának* melletti jelölőnégyzetet, majd kattintson a panel tetején található **Törlés** gombra.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban programozott módon létrehozott egy egyéni beléptetési bejegyzést egy TPM-eszközhöz. Opcionálisan létrehozott egy TPM-mel szimulált eszközt a számítógépen, és kiépítheti azt az IoT hub-ra az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
