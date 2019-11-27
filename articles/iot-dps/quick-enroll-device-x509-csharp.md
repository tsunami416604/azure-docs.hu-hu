---
title: X. 509 eszköz regisztrálása az Azure Device kiépítési szolgáltatásba a használatávalC#
description: Ez a rövid útmutató csoportos regisztrációkat használ. Ebben a rövid útmutatóban az X. 509 eszközöket regisztrálja az Azure C#IoT hub Device Provisioning Service a használatával.
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
ms.openlocfilehash: 3df9afa35b3ae9f7360a5d4b890d3fce209a4b12
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/23/2019
ms.locfileid: "74423348"
---
# <a name="quickstart-enroll-x509-devices-to-the-device-provisioning-service-using-c"></a>Rövid útmutató: X.509-eszközök regisztrációja a Device Provisioning Service-be a C# használatával

[!INCLUDE [iot-dps-selector-quick-enroll-device-x509](../../includes/iot-dps-selector-quick-enroll-device-x509.md)]

Ez a rövid útmutató bemutatja, hogyan hozhat létre a C# segítségével programozott módon egy [regisztrációs csoportot](concepts-service.md#enrollment-group), amely köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítványokat használ. A beléptetési csoport a .NET- [hez készült Microsoft Azure IOT SDK](https://github.com/Azure/azure-iot-sdk-csharp) -val és C# egy minta .net Core-alkalmazással jön létre. Egy regisztrációs csoport a tanúsítványláncukban ugyanazon aláíró tanúsítvánnyal rendelkező eszközök kiépítési szolgáltatáshoz való hozzáférését szabályozza. További tudnivalókért lásd: [Eszközök kiépítési szolgáltatáshoz való hozzáférésének szabályozása X.509-tanúsítványokkal](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). További információ az X.509-tanúsítványon alapuló nyilvánoskulcs-infrastruktúra (PKI) az Azure IoT Hubbal és a Device Provisioning Service-szel való használatáról: [X.509 hitelesítésszolgáltatói tanúsítványok biztonsági áttekintése](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

Ez a rövid útmutató azt várja, hogy már létrehozott egy IoT hub és egy eszköz kiépítési szolgáltatási példányát. Ha még nem hozta létre ezeket az erőforrásokat, hajtsa végre a [IoT hub Device Provisioning Service beállítása a Azure Portal](./quick-setup-auto-provision.md) rövid útmutatóval, mielőtt folytatja ezt a cikket.

Bár a cikkben ismertetett lépések Windows és Linux rendszerű számítógépeken is működnek, ez a cikk egy Windows rendszerű fejlesztési számítógépet használ.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Telepítse a [Visual Studio 2019](https://www.visualstudio.com/vs/)alkalmazást.
* Telepítse a [.net Core SDK](https://www.microsoft.com/net/download/windows).
* Telepítse a [Git](https://git-scm.com/download/) szoftvert.

## <a name="prepare-test-certificates"></a>Teszttanúsítványok előkészítése

A rövid útmutatóhoz szükség van egy .pem vagy .cer fájlra, amely tartalmazza egy köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítvány nyilvános részét. A tanúsítványnak a kiépítési szolgáltatásba feltöltöttnek és a szolgáltatás által ellenőrzöttnek kell lennie.

Az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) olyan tesztelési eszközöket tartalmaz, amelyek segítséget nyújtanak egy X. 509 tanúsítványlánc létrehozásához, egy gyökér-vagy köztes tanúsítvány feltöltéséhez a láncból, és a tanúsítvány igazolása a szolgáltatással.

> [!CAUTION]
> Az SDK-eszközökkel létrehozott tanúsítványokat csak fejlesztési teszteléshez használhatja.
> Ezeket a tanúsítványokat ne használja éles környezetben.
> A kód olyan rögzített jelszavakat tartalmaz, mint a *1234*, amelyek 30 nap elteltével lejárnak.
> A termelési használathoz megfelelő tanúsítványok beszerzésével kapcsolatos további információt az Azure IoT Hub dokumentációjának [X.509 hitelesítésszolgáltatói tanúsítvány beszerzése](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) című részében talál.
>

Ha ezt a tesztelési eszközt szeretné használni a tanúsítványok létrehozásához, hajtsa végre a következő lépéseket:

1. Nyisson meg egy parancssorablakot vagy a git bash rendszerhéjt, és váltson a számítógép egyik munkamappájára. Futtassa az alábbi parancsot az [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-tárház klónozásához:

   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Ez a művelet várhatóan több percig is eltarthat.

   A teszteszköz a klónozott adattár *azure-iot-sdk-c/tools/CACertificates* mappájában található.

1. Kövesse a [mintákhoz és oktatóanyagokhoz készült hitelesítésszolgáltatói tanúsítványok kezeléséről](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) szóló cikk lépéseit.

A C SDK-ban található eszközökön kívül a *Microsoft Azure IOT SDK for .net* -ben a tanúsítvány- C# [ellenőrzési minta](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) azt is bemutatja, hogyan lehet a meglévő X. 509 köztes vagy legfelső szintű hitelesítésszolgáltatói tanúsítvánnyal igazolni.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Jelentkezzen be a Azure Portalba, válassza a **minden erőforrás**lehetőséget, majd az eszköz kiépítési szolgáltatását.

1. Válassza a **megosztott hozzáférési házirendek**lehetőséget, majd válassza ki a tulajdonságok megnyitásához használni kívánt hozzáférési szabályzatot. A **hozzáférési házirendben**másolja és mentse az elsődleges kulcs kapcsolati karakterláncát.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>A regisztrációs csoport mintájának létrehozása 

Ez a szakasz bemutatja, hogyan hozhat létre egy olyan .NET Core Console-alkalmazást, amely beléptetési csoportot telepít a kiépítési szolgáltatáshoz. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is a regisztrációs csoport hozzáadásához. További információk az IoT Core használatával való fejlesztésről: [Windows IoT Core – fejlesztői dokumentáció](https://docs.microsoft.com/windows/iot-core/).

1. Nyissa meg a Visual studiót, és válassza **az új projekt létrehozása**lehetőséget. Az **új projekt létrehozása**területen válassza ki a Project sablonhoz tartozó C# **Console alkalmazást (.net Core)** , majd kattintson a **Tovább gombra**.

1. Nevezze el a projekt *CreateEnrollmentGroup*, majd kattintson a **Létrehozás**gombra.

    ![A Visual C# Windows klasszikus asztali projekt konfigurálása](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. Amikor a megoldás megnyílik a Visual Studióban, a **megoldáskezelő** ablaktáblán kattintson a jobb gombbal a **CreateEnrollmentGroup** projektre, majd válassza a **NuGet-csomagok kezelése**lehetőséget.

1. A **NuGet csomagkezelő**területén válassza a **Tallózás**elemet, keresse meg és válassza a **Microsoft. Azure. Devices. kiépítés. szolgáltatás**elemet, majd kattintson a **telepítés**gombra.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-x509-csharp/add-nuget.png)

   Ez a lépés letölti, telepíti és hozzáadja az [Azure IoT kiépítési szolgáltatás ÜGYFÉLOLDALI SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) -NuGet csomagjának és függőségeinek hivatkozását.

1. Adja hozzá a következő `using` utasításokat a `Program.cs`tetején található többi `using` utasítás után:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Adja hozzá a következő mezőket a `Program` osztályhoz, és végezze el a felsorolt módosításokat.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Cserélje le a `ProvisioningServiceConnectionString` helyőrző értékét azon kiépítési szolgáltatáshoz tartozó kapcsolódási karakterláncra, amelyhez a regisztrációt létre szeretné hozni.

   * Cserélje le a `X509RootCertPath` helyőrző értékét egy. PEM vagy. cer fájl elérési útjára. Ez a fájl egy olyan közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI X. 509 tanúsítvány nyilvános részét képezi, amelyet előzőleg töltöttek fel és ellenőriztek a kiépítési szolgáltatással.

   * Szükség esetén módosíthatja a `EnrollmentGroupId` értéket. A sztring csak kisbetűs karaktereket és kötőjelet tartalmazhat.

   > [!IMPORTANT]
   > Az éles kódban vegye figyelembe a következő biztonsági szempontokat:
   >
   > * A kapcsolati sztring fix kódolása a kiépítési szolgáltatás rendszergazdája esetében nem felel meg az ajánlott biztonsági eljárásoknak. Ehelyett biztonságosan kell tárolni a sztringet, például egy biztonságos konfigurációs fájlban vagy a beállításjegyzékben.
   > * Ügyeljen arra, hogy az aláíró tanúsítványnak csak a nyilvános részét töltse fel. Soha ne töltse fel a kiépítési szolgáltatás titkos kulcsait tartalmazó .pfx (PKCS12) vagy .pem fájlt.

1. Adja hozzá a következő metódust a `Program` osztályhoz. Ez a kód egy regisztrációs csoport bejegyzést hoz létre, majd meghívja a `CreateOrUpdateEnrollmentGroupAsync` metódust `ProvisioningServiceClient` a regisztrációs csoport hozzáadásához a kiépítési szolgáltatáshoz.

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

1. Végül cserélje le a `Main` metódus törzsét a következő sorokra:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Hozza létre a megoldást.

## <a name="run-the-enrollment-group-sample"></a>A regisztrációs mintacsoport futtatása
  
Futtassa a mintát a Visual Studióban a regisztrációs csoport létrehozásához. Ekkor megjelenik egy parancssori ablak, amely megerősítő üzeneteket jelenít meg. A sikeres létrehozáskor a parancssori ablak az új beléptetési csoport tulajdonságait jeleníti meg.

Ellenőrizheti, hogy létrejött-e a beléptetési csoport. Nyissa meg az eszköz kiépítési szolgáltatásának összegzését, és válassza a regisztrációk **kezelése**, majd a **beléptetési csoportok**lehetőséget. Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Válassza ki a bejegyzést a tanúsítvány ujjlenyomatának és egyéb tulajdonságainak ellenőrzéséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy vizsgálja C# meg a szolgáltatási mintát, ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ellenkező esetben a következő lépésekkel törölheti az ebben a rövid útmutatóban létrehozott összes erőforrást.

1. Zárjuk be C# a minta kimeneti ablakát a számítógépen.

1. Navigáljon a Azure Portal eszköz kiépítési szolgáltatásához, válassza a **regisztrációk kezelése**, majd a **beléptetési csoportok**lehetőséget. Válassza ki a rövid útmutató segítségével létrehozott beléptetési bejegyzés *regisztrációs azonosítóját* , majd nyomja le a **delete**billentyűt.

1. A Azure Portal eszköz kiépítési szolgáltatásában válassza ki a **tanúsítványok**lehetőséget, válassza ki a rövid útmutatóhoz feltöltött tanúsítványt, és a **tanúsítvány részleteinek**tetején kattintson a **Törlés** gombra.  

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta az X. 509 közbenső vagy legfelső szintű HITELESÍTÉSSZOLGÁLTATÓI tanúsítvány beléptetési csoportját az Azure IoT Hub Device Provisioning Service használatával. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
