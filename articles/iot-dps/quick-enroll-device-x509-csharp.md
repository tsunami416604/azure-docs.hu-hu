---
title: Enroll X.509 device to Azure Device Provisioning Service using C#
description: Ez a rövid útmutató csoportos regisztrációkat használ. In this quickstart, enroll X.509 devices to the Azure IoT Hub Device Provisioning Service using C#.
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

Ez a rövid útmutató bemutatja, hogyan hozhat létre a C# segítségével programozott módon egy [regisztrációs csoportot](concepts-service.md#enrollment-group), amely köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítványokat használ. The enrollment group is created by using the [Microsoft Azure IoT SDK for .NET](https://github.com/Azure/azure-iot-sdk-csharp) and a sample C# .NET Core application. Egy regisztrációs csoport a tanúsítványláncukban ugyanazon aláíró tanúsítvánnyal rendelkező eszközök kiépítési szolgáltatáshoz való hozzáférését szabályozza. További tudnivalókért lásd: [Eszközök kiépítési szolgáltatáshoz való hozzáférésének szabályozása X.509-tanúsítványokkal](./concepts-security.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates). További információ az X.509-tanúsítványon alapuló nyilvánoskulcs-infrastruktúra (PKI) az Azure IoT Hubbal és a Device Provisioning Service-szel való használatáról: [X.509 hitelesítésszolgáltatói tanúsítványok biztonsági áttekintése](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview). 

This quickstart expects you've already created an IoT hub and Device Provisioning Service instance. If you haven't already created these resources, complete the [Set up IoT Hub Device Provisioning Service with the Azure portal](./quick-setup-auto-provision.md) quickstart before you continue with this article.

Although the steps in this article work on both Windows and Linux computers, this article uses a Windows development computer.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* Install [Visual Studio 2019](https://www.visualstudio.com/vs/).
* Install [.NET Core SDK](https://www.microsoft.com/net/download/windows).
* Telepítse a [Git](https://git-scm.com/download/) szoftvert.

## <a name="prepare-test-certificates"></a>Teszttanúsítványok előkészítése

A rövid útmutatóhoz szükség van egy .pem vagy .cer fájlra, amely tartalmazza egy köztes vagy legfelső szintű hitelesítésszolgáltatói X.509-tanúsítvány nyilvános részét. A tanúsítványnak a kiépítési szolgáltatásba feltöltöttnek és a szolgáltatás által ellenőrzöttnek kell lennie.

The [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) contains test tooling that can help you create an X.509 certificate chain, upload a root or intermediate certificate from that chain, and do proof-of-possession with the service to verify the certificate.

> [!CAUTION]
> Use certificates created with the SDK tooling for development testing only.
> Do not use these certificates in production.
> They contain hard-coded passwords, such as *1234*, that expire after 30 days.
> A termelési használathoz megfelelő tanúsítványok beszerzésével kapcsolatos további információt az Azure IoT Hub dokumentációjának [X.509 hitelesítésszolgáltatói tanúsítvány beszerzése](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) című részében talál.
>

To use this test tooling to generate certificates, do the following steps:

1. Open a Command Prompt window or Git Bash shell, and change to a working folder on your computer. Run the following command to clone the [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub repository:

   ```cmd/sh
   git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive
   ```

   Ez a művelet várhatóan több percig is eltarthat.

   A teszteszköz a klónozott adattár *azure-iot-sdk-c/tools/CACertificates* mappájában található.

1. Kövesse a [mintákhoz és oktatóanyagokhoz készült hitelesítésszolgáltatói tanúsítványok kezeléséről](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) szóló cikk lépéseit.

In addition to the tooling in the C SDK, the [Group certificate verification sample](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample) in the *Microsoft Azure IoT SDK for .NET* shows how to do proof-of-possession in C# with an existing X.509 intermediate or root CA certificate.

## <a name="get-the-connection-string-for-your-provisioning-service"></a>A kiépítési szolgáltatás kapcsolati sztringjének lekérése

A rövid útmutatóban lévő mintához szüksége lesz a kiépítési szolgáltatás kapcsolati sztringjére.

1. Sign in to the Azure portal, select **All resources**, and then your Device Provisioning Service.

1. Select **Shared access policies**, then choose the access policy you want to use to open its properties. In **Access Policy**, copy and save the primary key connection string.

    ![A kiépítési szolgáltatás kapcsolati sztringjének lekérése a portálról](media/quick-enroll-device-x509-csharp/get-service-connection-string-vs2019.png)

## <a name="create-the-enrollment-group-sample"></a>A regisztrációs csoport mintájának létrehozása 

This section shows how to create a .NET Core console app that adds an enrollment group to your provisioning service. Néhány módosítással ezeket a lépéseket követve létrehozhat egy [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot)-konzolalkalmazást is a regisztrációs csoport hozzáadásához. További információk az IoT Core használatával való fejlesztésről: [Windows IoT Core – fejlesztői dokumentáció](https://docs.microsoft.com/windows/iot-core/).

1. Open Visual Studio and select **Create a new project**. In **Create a new project**, choose the **Console App (.NET Core)** for C# project template and select **Next**.

1. Name the project *CreateEnrollmentGroup*, and then press **Create**.

    ![Configure Visual C# Windows Classic Desktop project](media//quick-enroll-device-x509-csharp/configure-app-vs2019.png)

1. When the solution opens in Visual Studio, in the **Solution Explorer** pane, right-click the **CreateEnrollmentGroup** project, and then select **Manage NuGet Packages**.

1. In **NuGet Package Manager**, select **Browse**, search for and choose **Microsoft.Azure.Devices.Provisioning.Service**, and then press **Install**.

    ![NuGet Package Manager (NuGet-csomagkezelő) ablak](media//quick-enroll-device-x509-csharp/add-nuget.png)

   This step downloads, installs, and adds a reference to the [Azure IoT Provisioning Service Client SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet package and its dependencies.

1. Add the following `using` statements after the other `using` statements at the top of `Program.cs`:

   ```csharp
   using System.Security.Cryptography.X509Certificates;
   using System.Threading.Tasks;
   using Microsoft.Azure.Devices.Provisioning.Service;
   ```

1. Add the following fields to the `Program` class, and make the listed changes.  

   ```csharp
   private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
   private static string EnrollmentGroupId = "enrollmentgrouptest";
   private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
   ```

   * Replace the `ProvisioningServiceConnectionString` placeholder value with the connection string of the provisioning service that you want to create the enrollment for.

   * Replace the `X509RootCertPath` placeholder value with the path to a .pem or .cer file. This file represents the public part of an intermediate or root CA X.509 certificate that has been previously uploaded and verified with your provisioning service.

   * You may optionally change the `EnrollmentGroupId` value. A sztring csak kisbetűs karaktereket és kötőjelet tartalmazhat.

   > [!IMPORTANT]
   > Az éles kódban vegye figyelembe a következő biztonsági szempontokat:
   >
   > * A kapcsolati sztring fix kódolása a kiépítési szolgáltatás rendszergazdája esetében nem felel meg az ajánlott biztonsági eljárásoknak. Ehelyett biztonságosan kell tárolni a sztringet, például egy biztonságos konfigurációs fájlban vagy a beállításjegyzékben.
   > * Ügyeljen arra, hogy az aláíró tanúsítványnak csak a nyilvános részét töltse fel. Soha ne töltse fel a kiépítési szolgáltatás titkos kulcsait tartalmazó .pfx (PKCS12) vagy .pem fájlt.

1. Add the following method to the `Program` class. This code creates an enrollment group entry and then calls the `CreateOrUpdateEnrollmentGroupAsync` method on `ProvisioningServiceClient` to add the enrollment group to the provisioning service.

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

1. Finally, replace the body of the `Main` method with the following lines:

   ```csharp
   RunSample().GetAwaiter().GetResult();
   Console.WriteLine("\nHit <Enter> to exit ...");
   Console.ReadLine();
   ```

1. Hozza létre a megoldást.

## <a name="run-the-enrollment-group-sample"></a>A regisztrációs mintacsoport futtatása
  
Futtassa a mintát a Visual Studióban a regisztrációs csoport létrehozásához. A Command Prompt window will appear and start showing confirmation messages. On successful creation, the Command Prompt window displays the properties of the new enrollment group.

You can verify that the enrollment group has been created. Go to the Device Provisioning Service summary, and select **Manage enrollments**, then select **Enrollment Groups**. Meg kell jelennie egy új regisztrációs bejegyzésnek, amely megfelel a mintában használt regisztrációs azonosítónak is.

![A regisztráció tulajdonságai a portálon](media/quick-enroll-device-x509-csharp/verify-enrollment-portal-vs2019.png)

Select the entry to verify the certificate thumbprint and other properties for the entry.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

If you plan to explore the C# service sample, don't clean up the resources created in this quickstart. Otherwise, use the following steps to delete all resources created by this quickstart.

1. Close the C# sample output window on your computer.

1. Navigate to your Device Provisioning service in the Azure portal, select **Manage enrollments**, and then select **Enrollment Groups**. Select the *Registration ID* for the enrollment entry you created using this quickstart and press **Delete**.

1. From your Device Provisioning service in the Azure portal, select **Certificates**, choose the certificate you uploaded for this quickstart, and press **Delete** at the top of **Certificate Details**.  

## <a name="next-steps"></a>Következő lépések

In this quickstart, you created an enrollment group for an X.509 intermediate or root CA certificate using the Azure IoT Hub Device Provisioning Service. Ha mélyebben szeretné megismerni az eszközkiépítést, folytassa az Azure Portalon az eszközkiépítési szolgáltatás beállításának oktatóanyagával.

> [!div class="nextstepaction"]
> [Azure IoT Hub eszközkiépítési szolgáltatás oktatóanyagai](./tutorial-set-up-cloud.md)
