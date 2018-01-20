---
title: "Egy Azure IoT Hub eszköz kiépítése szolgáltatáshoz (.NET) eszközt kiépítése |} Microsoft Docs"
description: "Az eszköz a Azure IoT Hub eszköz kiépítése szolgáltatáshoz (.NET) használatával egyetlen IoT-központ telepítéséhez"
services: iot-dps
keywords: 
author: msebolt
ms.author: v-masebo
ms.date: 09/05/2017
ms.topic: tutorial
ms.service: iot-dps
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 6919f962d853faa572ee7ad5d0cb9aeacd3bd2b6
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/20/2018
---
# <a name="enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Regisztrálja az eszközt az Azure IoT Hub kiépítés szolgáltatás ügyfél (.NET) használatával az IoT hubhoz

Az előző oktatóanyag megtudta, hogyan eszköz beállítása az eszköz kiépítése szolgáltatáshoz való kapcsolódáshoz. Ebben az oktatóanyagban elsajátíthatja, hogyan a szolgáltatás használatához az eszközt egy IoT-központ kiépítéséhez mindkét  **_egyes beléptetési_**  és  **_beléptetési csoportok_**. Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Regisztrálja az eszközt
> * Indítsa el az eszközt
> * Ellenőrizze, hogy az eszköz regisztrálva van

## <a name="prerequisites"></a>Előfeltételek

Mielőtt továbblépne, győződjön meg arról, hogy az eszköz konfigurálása és a *hardveres biztonsági modul* lásd az oktatóanyag a [eszköz beállítása az Azure IoT Hub eszköz kiépítése szolgáltatáshoz használatával kiépítéséhez](./tutorial-set-up-device.md).

* Visual Studio 2015-öt vagy a Visual Studio 2017

> [!NOTE]
> A Visual Studio nincs szükség. A telepítés [.NET](https://www.microsoft.com/net) elegendő és a fejlesztők az előnyben részesített szerkesztő Windows vagy Linux.  

Ez az oktatóanyag az időtartam alatt vagy után a gyártási a folyamat, ha eszközadatokat hozzá lett adva a létesítési szolgáltatás hardver szimulálja. Ez a kód általában egy számítógépen vagy futtatható a .NET-kódot, és nem vehető fel a eszközöktől gyári eszköz futtatása


## <a name="enroll-the-device"></a>Regisztrálja az eszközt

Ezt a lépést foglal magában, az eszköz egyedi biztonsági összetevők hozzáadása az kiépítése szolgáltatáshoz. Ezen biztonsági összetevők a következők:

- TPM-alapú eszközök esetén:
    - A *ellenőrzőkulccsal* egyedi minden TPM lapka vagy szimulátor. Olvassa el a [megértéséhez TPM ellenőrzőkulccsal](https://technet.microsoft.com/library/cc770443.aspx) további információt.
    - A *regisztrációs Azonosítót* névtér/hatókörében eszköz egyedi azonosítására szolgál. Ez lehet, vagy nem lehet ugyanaz, mint az eszközazonosító. Az azonosító megadása kötelező, minden eszközhöz. A TPM-alapú eszközök esetén a regisztrációs Azonosítót a platformmegbízhatósági modul, például egy SHA-256 kivonatoló, a TPM ellenőrzőkulcs kell származnia.

- X.509-alapú eszközök esetén:
    - A [X.509 tanúsítvány az eszköz tulajdonosa](https://msdn.microsoft.com/library/windows/desktop/bb540819.aspx), formájában, vagy egy *.pem* vagy egy *.cer* fájlt. Az egyes beléptetési kell használnia a *levéltanúsítvány* a X.509 rendszerhez, a beléptetési csoportok kell használnia a *legfelső szintű tanúsítvány* vagy egy azzal egyenértékű *aláíró tanúsítvány*.
    - A *regisztrációs Azonosítót* névtér/hatókörében eszköz egyedi azonosítására szolgál. Ez lehet, vagy nem lehet ugyanaz, mint az eszközazonosító. Az azonosító megadása kötelező, minden eszközhöz. X.509-alapú eszközökhöz regisztrációs Azonosítót a tanúsítvány köznapi nevének (CN) származik. Ezek a követelmények további információk: [eszköz fogalmak](https://docs.microsoft.com/en-us/azure/iot-dps/concepts-device).

Regisztrálja az eszközt, az eszköz kiépítése szolgáltatáshoz két módja van:

- **Egyes regisztrációkat** egyetlen eszközt, amely előfordulhat, hogy regisztrálja az eszköz kiépítése szolgáltatáshoz tartozó bejegyzés jelképez. Egyes regisztrációkat használhatja X.509-tanúsítványokat vagy a SAS-tokenje (a valós vagy virtuális TPM) igazolás mechanizmusokat. Azt javasoljuk, egyes regisztrációkat eszközök, amelyek külön kezdeti konfigurációt igényelnek, vagy használó eszközöket is csak TPM keresztül SAS-tokenje igazoló mechanizmusként. Előfordulhat, hogy az egyes regisztrációkat a kívánt IoT hub eszköz-azonosító van megadva.

- **Beléptetési csoportok** , amelyek egy adott tanúsítvány mechanizmus egy eszközcsoportra jelképez. Azt javasoljuk, egy beléptetési csoport az eszközök, amelyek kezdeti szükségeskonfiguráció megosztásához sok vagy eszközök valamennyi amelyet ugyanannak a bérlőnek. Beléptetési csoportok X.509 csak, és az összes az X.509 tanúsítványlánc egy aláíró tanúsítványt.

### <a name="enroll-the-device-using-individual-enrollments"></a>Az egyes regisztrációkat használt eszköz regisztrálása

1. A Visual Studio, a Visual C# Konzolalkalmazás projekt létrehozása használatával a **Konzolalkalmazás** projektsablon. Nevet a projektnek **DeviceProvisioning**.
    
1. A Megoldáskezelőben kattintson a jobb gombbal a **DeviceProvisioning** projektre, és kattintson a **NuGet-csomagok kezelése...** .

1. Az a **NuGet-Csomagkezelő** ablakban válassza ki **Tallózás** keresse meg a **microsoft.azure.devices.provisioning.service**. Válassza ki a bejegyzést, és kattintson a **telepítése** telepítéséhez a **Microsoft.Azure.Devices.Provisioning.Service** csomagot, majd fogadja el a használati feltételeket. Ez az eljárás tölti le, telepíti, és hozzáad egy hivatkozást a [SDK szolgáltatás kiépítését Azure IoT-eszközök](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet csomag és annak függőségeit.

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: Cserélje le a helyőrző értékét az előző szakaszban az áttelepítés előtt feljegyzett terjesztési pontok kapcsolati karakterlánccal.
   
    ```csharp
    static readonly string ServiceConnectionString = "{DPS connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Adja hozzá a következő, az eszköz regisztrációjának megvalósításához:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Végül adja hozzá az alábbi kódot a **fő** módszert, nyissa meg a kapcsolatot az IoT hub és a regisztráció megkezdéséhez:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```
        
1. A Visual Studio Solution Explorerben kattintson a jobb gombbal a megoldás, és kattintson **indítási projektek beállítása...** . Válassza ki **egyetlen kezdőprojekt**, majd válassza ki a **DeviceProvisioning** projekt legördülő menüből.  

1. A .NET-eszköz alkalmazás futtatása **DeviceProvisiong**. Érdemes azt beállítania az eszköz kiépítése: 

    ![Az egyes regisztrációs futtatása](./media/tutorial-net-provision-device-to-hub/individual.png)

Ha sikeresen regisztrálta az eszközét, kell megjelennie, megjelenik a portálon, mint a következőket:

   ![Sikeres regisztráció a portálon](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Regisztrálja az eszközt a beléptetési csoportok használata

> [!NOTE]
> A beléptetési csoport minta igényli egy X.509 tanúsítvány.

1. A Visual Studio Solution Explorerben nyissa meg a **DeviceProvisioning** a fenti létrehozott projekt. 

1. Adja hozzá a következő `using` utasításokat a **Program.cs** fájl elejéhez:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Adja hozzá a **Program** osztályhoz a következő mezőket: A helyőrző értékét lecserélheti a X509 tanúsítványhely.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Adja hozzá a következőt a **Program.cs** valósítja meg a regisztrációt a csoporthoz:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Végül, cserélje le az alábbi kódot a **fő** módszert, nyissa meg a kapcsolatot az IoT hub és a csoport regisztráció megkezdéséhez:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. A .NET-eszköz alkalmazás futtatása **DeviceProvisiong**. Érdemes azt beállítania az eszköz kiépítése csoport: 

    ![Csoport regisztrációs futtatása](./media/tutorial-net-provision-device-to-hub/group.png)

    Sikeresen regisztrált az eszközcsoport, az megjelenik a portálon, mint a következő megjelennie:

   ![Sikeres csoport beléptetési a portálon](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Indítsa el az eszközt

Ezen a ponton a következő telepítő készen áll a eszközregisztráció:

1. Az eszköz kiépítése szolgáltatáshoz regisztrált az eszközt vagy eszközöket és 
2. Az eszköz áll készen a biztonsági konfigurált és az eszköz kiépítése szolgáltatáshoz ügyfél SDK használata alkalmazás keresztül érhető el.

Indítsa el az eszközöket, hogy az ügyfélalkalmazás számára az eszköz kiépítése szolgáltatáshoz a regisztrációs elindításához.  


## <a name="verify-the-device-is-registered"></a>Ellenőrizze, hogy az eszköz regisztrálva van

Egyszer az eszköz indítása, a következő műveleteket kell végrehajtani. Tekintse meg a TPM-szimulátor mintaalkalmazás [dps_client_sample](https://github.com/Azure/azure-iot-device-auth/blob/master/dps_client/samples/dps_client_sample/dps_client_sample.c) további részleteket. 

1. Az eszköz regisztrációs kérelmet küld az eszköz kiépítése szolgáltatáshoz.
2. TPM-eszközök esetén az eszköz kiépítése szolgáltatáshoz küld vissza a regisztrációs kihívás, amelyhez az eszköz válaszol. 
3. A sikeres regisztráció az eszköz kiépítése szolgáltatáshoz elküldi az eszközt az IoT hub URI, eszköz-Azonosítóját és a titkosított kulcs biztonsági. 
4. Az IoT-központ ügyfélalkalmazás az eszköz ezután csatlakozik a központ. 
5. A sikeres kapcsolat szeretne az elosztóhoz, kell megjelennie az eszköz jelennek meg az IoT-központ **eszköz Explorer**. 

    ![Sikeres kapcsolatot a portálon](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>További lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Regisztrálja az eszközt
> * Indítsa el az eszközt
> * Ellenőrizze, hogy az eszköz regisztrálva van

A következő oktatóanyag áttekintésével megismerheti, hogyan több különböző terhelésű hubok központilag továbblépés. 

> [!div class="nextstepaction"]
> [Különböző terhelésű IoT-központok eszközök beállításához](./tutorial-provision-multiple-hubs.md)
