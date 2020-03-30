---
title: 'X.509-es eszköz regisztrálása az Azure-eszközkiépítési szolgáltatásba C használatával #'
description: Ez a rövid útmutató csoportos regisztrációkat használ. Ebben a rövid útmutatóban az X.509-es eszközöket regisztrálja az Azure IoT Hub-eszközkiépítési szolgáltatásra (DPS) a C# használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 64bc3921a606ab3211173b46b268ded53952c8bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75434658"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Rövid útmutató: X.509-eszközök regisztrációja a Device Provisioning Service-be a C# használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre a C# segítségével programozott módon egy [regisztrációs csoportot](concepts-service.md#enrollment-group), amely köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítványokat használ. A regisztrációs csoport a [Microsoft Azure IoT SDK for .NET](https://github.com/Azure/azure-iot-sdk-csharp) és egy c# .NET Core alkalmazás használatával jön létre. Egy regisztrációs csoport a tanúsítványláncukban ugyanazon aláíró tanúsítvánnyal rendelkező eszközök kiépítési szolgáltatáshoz való hozzáférését szabályozza. További tudnivalókért lásd: [Eszközök kiépítési szolgáltatáshoz való hozzáférésének szabályozása X.509-tanúsítványokkal](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). További információ az X.509-tanúsítványon alapuló nyilvánoskulcs-infrastruktúra (PKI) az Azure IoT Hubbal és a Device Provisioning Service-szel való használatáról: [X.509 hitelesítésszolgáltatói tanúsítványok biztonsági áttekintése](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Ez a rövid útmutató elvárja, hogy már létrehozott egy IoT hub és az eszközkiépítési szolgáltatás példánya. Ha még nem hozta létre ezeket az erőforrásokat, töltse ki az [IoT Hub-eszközkiépítési szolgáltatás beállítása az Azure Portal](./quick-setup-auto-provision.md) rövid útmutató, mielőtt folytatnák ezt a cikket.

Bár a cikkben leírt lépések Windows és Linux rendszerű számítógépeken is működnek, ez a cikk Windows fejlesztői számítógépet használ.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2019-et.](https://www.visualstudio.com/vs/)
* Telepítse [a .NET Core SDK-t](https://www.microsoft.com/net/download/windows).
* Telepítse [a Git](https://git-scm.com/download/)- alkalmazást.

## <a name="prepare-test-certificates"></a>Teszttanúsítványok előkészítése

A rövid útmutatóhoz szükség van egy .pem vagy .cer fájlra, amely tartalmazza egy köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítvány nyilvános részét. A tanúsítványnak a kiépítési szolgáltatásba feltöltöttnek és a szolgáltatás által ellenőrzöttnek kell lennie.

Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) olyan teszteszközt tartalmaz, amely segít az X.509 tanúsítványlánc létrehozásában, a láncból származó legfelső szintű vagy köztes tanúsítvány feltöltésében, valamint a tanúsítvány ellenőrzéséhez a szolgáltatás birtoklásának igazolásában.

> [!CAUTION]
> Az SDK-eszközzel létrehozott tanúsítványokat csak fejlesztési tesztelésre használhatja.
> Ne használja ezeket a tanúsítványokat éles környezetben.
> Ezek olyan kódolt jelszavakat tartalmaznak, mint például *az 1234*, amelyek 30 nap után lejárnak.
> A termelési használathoz megfelelő tanúsítványok beszerzésével kapcsolatos további információt az Azure IoT Hub dokumentációjának [X.509 hitelesítésszolgáltatói tanúsítvány beszerzése](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) című részében talál.
>

A teszteszköz használatával tanúsítványokat hozhat létre, tegye a következő lépéseket:

1. Keresse meg a címke nevét az Azure IoT C SDK [legújabb kiadásának.](https://github.com/Azure/azure-iot-sdk-c/releases/latest)

2. Nyisson meg egy parancssort vagy a Git Bash-felületet, és lépjen egy, a gépen található munkamappába. Futtassa a következő parancsokat az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház legújabb kiadásának klónozásához. Használja az előző lépésben található címkét `-b` a paraméter értékeként:

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Ez a művelet várhatóan több percig is eltarthat.

   A teszteszköz a klónozott adattár *azure-iot-sdk-c/tools/CACertificates* mappájában található.

3. Kövesse a [mintákhoz és oktatóanyagokhoz készült hitelesítésszolgáltatói tanúsítványok kezeléséről](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) szóló cikk lépéseit.

A C SDK-ban található eszközmellett a *Microsoft Azure IoT SDK for .NET* [csoporttanúsítvány-ellenőrzési mintája bemutatja,](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) hogyan lehet a birtoklás igazolását C# nyelven elvégezni egy meglévő X.509 köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Jelentkezzen be az Azure Portalon, válassza az **Összes erőforrást,** majd az eszközkiépítési szolgáltatást.

1. Válassza **a Megosztott hozzáférési házirendek**lehetőséget, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési házirendet. Az **Access Policy programban**másolja és mentse az elsődleges kulcs kapcsolati karakterláncát.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>A regisztrációs csoport mintájának létrehozása 

Ez a szakasz bemutatja, hogyan hozhat létre egy .NET Core konzolalkalmazást, amely egy regisztrációs csoportot ad hozzá a létesítési szolgáltatáshoz. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is a regisztrációs csoport hozzáadásához. További információk az IoT Core használatával való fejlesztésről: [Windows IoT Core – fejlesztői dokumentáció](https://docs.microsoft.com/windows/iot-core/).

1. Nyissa meg a Visual Studio alkalmazást, és válassza **az Új projekt létrehozása**lehetőséget. Az **Új projekt létrehozása**csoportban válassza a **Konzolalkalmazás (.NET Core)** elemet a C# projektsablonhoz, és válassza a **Tovább**gombot.

1. Nevezze el a *Project CreateEnrollmentGroup*nevet, majd nyomja le a **Create billentyűt.**

    ![Visual C# Windows Classic Desktop projekt konfigurálása](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Amikor a megoldás megnyílik a Visual Studióban, a **Megoldáskezelő** ablaktáblán kattintson a jobb gombbal a **CreateEnrollmentGroup** projektre, majd válassza **a NuGet-csomagok kezelése parancsot.**

1. A **NuGet csomagkezelőben**válassza a **Tallózás**lehetőséget, keresse meg a **Microsoft.Azure.Devices.Provisioning.Service**elemet, majd nyomja le a **Telepítés**gombot.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Ez a lépés letölti, telepíti, és hozzáadja a hivatkozást az [Azure IoT-kiépítési szolgáltatás ügyfél SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet csomag és a függőségek.

1. A többi `using` `using` állítás után a következő állításokat `Program.cs`kell felsorasztani:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adja hozzá a `Program` következő mezőket az osztályhoz, és hajtsa végre a felsorolt módosításokat.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Cserélje `ProvisioningServiceConnectionString` le a helyőrző értéket annak a létesítési szolgáltatásnak a kapcsolati karakterláncára, amelyhez a regisztrációt létre kívánja hozni.

   * Cserélje `X509RootCertPath` le a helyőrző értéket a .pem vagy .cer fájl elérési útjának. Ez a fájl egy köztes vagy legfelső szintű CA X.509-es tanúsítvány nyilvános részét képviseli, amelyet korábban feltöltöttek és ellenőriztek a létesítési szolgáltatással.

   * Az `EnrollmentGroupId` értéket tetszés szerint módosíthatja. A sztring csak kisbetűs karaktereket és kötőjelet tartalmazhat.

   > [!IMPORTANT]
   > Az éles kódban vegye figyelembe a következő biztonsági szempontokat:
   >
   > * A kapcsolati sztring fix kódolása a kiépítési szolgáltatás rendszergazdája esetében nem felel meg az ajánlott biztonsági eljárásoknak. Ehelyett biztonságosan kell tárolni a sztringet, például egy biztonságos konfigurációs fájlban vagy a beállításjegyzékben.
   > * Ügyeljen arra, hogy az aláíró tanúsítványnak csak a nyilvános részét töltse fel. Soha ne töltse fel a kiépítési szolgáltatás titkos kulcsait tartalmazó .pfx (PKCS12) vagy .pem fájlt.

1. Adja hozzá a `Program` következő módszert az osztályhoz. Ez a kód létrehoz egy regisztrációs `CreateOrUpdateEnrollmentGroupAsync` csoport `ProvisioningServiceClient` bejegyzést, majd meghívja a metódust, hogy hozzáadja a regisztrációs csoportot a létesítési szolgáltatáshoz.

   ```csharp
   public static async Task RunSample()
   {
       Console.WriteLine("Starting sample...");
 
       using (ProvisioningServiceClient provisioningServiceClient =
               ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
       {
           #region Create a new enrollmentGroup config
           Console.WriteLine("\nCreating a new enrollmentGroup...");
           var certificate = new X509Certificate2(X509RootCertPath);
           Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
           EnrollmentGroup enrollmentGroup =
                   new EnrollmentGroup(
                           EnrollmentGroupId,
                           attestation)
                   {
                       ProvisioningStatus = ProvisioningStatus.Enabled
                   };
           Console.WriteLine(enrollmentGroup);
           #endregion
 
           #region Create the enrollmentGroup
           Console.WriteLine("\nAdding new enrollmentGroup...");
           EnrollmentGroup enrollmentGroupResult =
               await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
           Console.WriteLine("\nEnrollmentGroup created with success.");
           Console.WriteLine(enrollmentGroupResult);
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

## <a name="run-the-enrollment-group-sample"></a>A regisztrációs mintacsoport futtatása
  
Futtassa a mintát a Visual Studióban a regisztrációs csoport létrehozásához. Megjelenik egy parancssori ablak, és elkezdi megjeleníteni a megerősítő üzeneteket. Sikeres létrehozáskor a Parancssor ablak megjeleníti az új regisztrációs csoport tulajdonságait.

Ellenőrizheti, hogy a regisztrációs csoport létrejött-e. Nyissa meg az Eszközkiépítési szolgáltatás összegzését, és válassza **a Regisztrációk kezelése**lehetőséget, majd válassza a **Regisztrációs csoportok lehetőséget.** Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Válassza ki a bejegyzést a tanúsítvány ujjlenyomatának és a bejegyzés egyéb tulajdonságainak ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja meg a C# szolgáltatás minta, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben az alábbi lépésekkel törölje a rövid útmutató által létrehozott összes erőforrást.

1. Zárja be a C# minta kimeneti ablakot a számítógépen.

1. Nyissa meg az Eszközkiépítési szolgáltatást az Azure Portalon, válassza **a Regisztrációk kezelése**lehetőséget, és válassza a **Regisztrációs csoportok**lehetőséget. Válassza ki a *regisztrációs azonosítót* a rövid útmutatóval létrehozott regisztrációs bejegyzéshez, és nyomja le a **Delete billentyűt.**

1. Az Azure Portalon az Eszközkiépítési szolgáltatásban válassza a **Tanúsítványok**lehetőséget, válassza ki a rövid útmutatóhoz feltöltött tanúsítványt, és nyomja le a **Delete** billentyűt a **Tanúsítvány részletei**csoport tetején.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy regisztrációs csoportot egy X.509 köztes vagy legfelső szintű hitelesítésszolgáltató-tanúsítványhoz az Azure IoT Hub-eszközkiépítési szolgáltatás használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
