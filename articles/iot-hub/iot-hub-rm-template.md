---
title: Hozzon létre egy Azure IoT Hub sablonhasználatával (.NET) | Microsoft dokumentumok
description: Azure Resource Manager-sablon használata Egy IoT Hub C# programmal történő létrehozásához.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 02e814a9da320d688fe57edf3a3fe0640b8f5a47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976740"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>IoT-központ létrehozása az Azure Resource Manager-sablonnal (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Az Azure Resource Manager segítségével programozott módon hozhat létre és kezelhet Azure IoT-központokat. Ez az oktatóanyag bemutatja, hogyan azure Resource Manager-sablon használatával hozzon létre egy IoT hub egy C# programból.

> [!NOTE]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és az erőforrásokkal való munkához: [az Azure Resource Manager és a klasszikus.](../azure-resource-manager/management/deployment-models.md)  Ez a cikk az Azure Resource Manager központi telepítési modelljének használatával foglalkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio.
* Aktív Azure-fiók. <br/>Ha nem rendelkezik fiókkal, néhány perc alatt létrehozhat egy [ingyenes fiókot.][lnk-free-trial]
* Egy [Azure Storage-fiók,][lnk-storage-account] ahol tárolhatja az Azure Resource Manager sablonfájlokat.
* [Azure PowerShell 1.0-s][lnk-powershell-install] vagy újabb verzió.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>A Visual Studio-projekt előkészítése

1. A Visual Studio-ban hozzon létre egy Visual C# Windows Classic Desktop projektet a **Console App (.NET Framework)** projektsablon használatával. Nevezze el a **projectet CreateIoTHub**.

2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, majd kattintson **a NuGet-csomagok kezelése parancsra.**

3. A NuGet csomagkezelőben jelölje **be az Előzetes kiadás felvétele**jelölőnégyzetet, és a **Tallózás** lapon keresse meg a **Microsoft.Azure.Management.ResourceManager programot.** Jelölje ki a csomagot, kattintson a **Telepítés**gombra a **Módosítások áttekintése** csoportban **kattintson az OK**gombra, majd a licencek elfogadásához kattintson az **Elfogadás** parancsra.

4. A NuGet csomagkezelőben keresse meg a **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kattintson a **Telepítés**gombra a **Módosítások áttekintése** területen kattintson **az OK**gombra, majd a licenc elfogadásához kattintson az **Elfogadom** parancsra.

5. A Program.cs cserélje le a meglévő **ketreneket** a következő kódra:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. A Program.cs adja hozzá a következő statikus változókat a helyőrző értékek helyett. Az oktatóanyag korábbi részében feljegyezte **az ApplicationId**, **SubscriptionId**, **TenantId**és **Password** azonosítót. **Az Azure Storage-fiók neve** annak az Azure Storage-fióknak a neve, amelyben az Azure Resource Manager-sablonfájlokat tárolja. **Az erőforráscsoport neve** az IoT hub létrehozásakor használt erőforráscsoport neve. A név lehet egy már meglévő vagy új erőforráscsoport. **A központi telepítés neve** a központi telepítés neve, például **Deployment_01.**

    ```csharp
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId}";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    static string storageAddress = "https://{Your storage account name}.blob.core.windows.net";
    static string rgName = "{Resource group name}";
    static string deploymentName = "{Deployment name}";
    ```

[!INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## <a name="submit-a-template-to-create-an-iot-hub"></a>Sablon küldése IoT-központ létrehozásához

JSON-sablon és paraméterfájl használatával hozzon létre egy IoT hubot az erőforráscsoportban. Egy Azure Resource Manager-sablon használatával is módosíthatja a meglévő IoT-központot.

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**parancsra, majd az **Új elem parancsra.** Vegyen fel egy **template.json** nevű JSON-fájlt a projektbe.

2. Ha hozzá szeretne adni egy szabványos IoT-központot az **USA keleti régiójához,** cserélje le a **template.json** tartalmát a következő erőforrás-definícióra. Az IoT Hubot támogató régiók aktuális listáját az Azure Állapota című témakörben [tetthet:][lnk-status]

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

3. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**parancsra, majd az **Új elem parancsra.** Adjon hozzá egy **parameters.json** nevű JSON-fájlt a projekthez.

4. Cserélje le a **parameters.json** tartalmát a következő paraméteradatokra, amelyek beállítja az új IoT hub nevét, például **{a monogramját}mynewiothub**. Az IoT hub nevének globálisan egyedinek kell lennie, ezért tartalmaznia kell a nevét vagy monogramját:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": { "value": "mynewiothub" }
      }
    }
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

5. A **Server Explorerben**csatlakozzon az Azure-előfizetéséhez, és az Azure Storage-fiókjában hozzon létre egy sablonoknak nevezett **tárolót.** A **Tulajdonságok** panelen állítsa a sablonok **tárolójának nyilvános** **olvasási** hozzáférési engedélyeit **Blob (Blob)** beállításra.

6. A **Kiszolgálókezelőben**kattintson a jobb gombbal a **sablontárolóra,** majd kattintson a **Blob-tároló megtekintése parancsra.** Kattintson a **Blob feltöltése** gombra, jelölje ki a két fájlt, a **parameters.json** t és a **templates.json fájlt,** majd a **Megnyitás** gombra kattintva töltse fel a JSON-fájlokat a **sablontárolóba.** A JSON-adatokat tartalmazó blobok URL-címei a következők:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. A Program.cs a következő módszerrel egészítse ki:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz a sablon- és paraméterfájlok Azure Resource Manager beküldéséhez:

    ```csharp
    var createResponse = client.Deployments.CreateOrUpdate(
        rgName,
        deploymentName,
        new Deployment()
        {
          Properties = new DeploymentProperties
          {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink
            {
              Uri = storageAddress + "/templates/template.json"
            },
            ParametersLink = new ParametersLink
            {
              Uri = storageAddress + "/templates/parameters.json"
            }
          }
        });
    ```

9. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz, amely megjeleníti az új IoT hub állapotát és kulcsait:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Az alkalmazás befejezése és futtatása

Most már befejezheti az alkalmazást a **CreateIoTHub** metódus meghívásával, mielőtt létrehozna és futtatna.

1. Adja hozzá a következő kódot a **fő** módszer végéhez:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kattintson **a Build,** majd **a Build Solution (Megoldás készítése) elemre.** Javítsa ki a hibákat.

3. Kattintson **a Hibakeresés gombra,** majd az alkalmazás futtatásához indítsa el a **hibakeresést.** A központi telepítés futtatása több percig is eltarthat.

4. Ellenőrizze, hogy az alkalmazás hozzá adta az új IoT-központot, keresse fel az [Azure Portalt,][lnk-azure-portal] és tekintse meg az erőforrások listáját. Másik lehetőségként használja a **Get-AzResource** PowerShell parancsmag.

> [!NOTE]
> Ez a példaalkalmazás hozzáad egy S1 standard IoT Hub, amelynek számlázása. Törölheti az IoT hub ot az [Azure Portalon][lnk-azure-portal] keresztül, vagy az **Eltávolítás-AzResource** PowerShell-parancsmag használatával, ha elkészült.

## <a name="next-steps"></a>További lépések
Most már üzembe helyezett egy IoT-központot egy C# programmal rendelkező Azure Resource Manager-sablon használatával, érdemes lehet további anamnézist is feltárnia:

* Az [IoT Hub-erőforrás-szolgáltató REST API][lnk-rest-api]képességeiről olvashat.
* Olvassa el [az Azure Resource Manager áttekintését,][lnk-azure-rm-overview] ha többet szeretne megtudni az Azure Resource Manager képességeiről.
* A JSON szintaxisát és a sablonokban használandó tulajdonságokat a [Microsoft.Devices erőforrástípusok](/azure/templates/microsoft.devices/iothub-allversions)című témakörben olvashat.

Ha többet szeretne tudni az IoT Hub fejlesztéseiről, olvassa el az alábbi cikkeket:

* [Bevezetés a C SDK-ba][lnk-c-sdk]
* [Azure IoT SDK-k][lnk-sdks]

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/management/overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
