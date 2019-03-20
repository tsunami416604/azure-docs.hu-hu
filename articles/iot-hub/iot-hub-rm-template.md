---
title: Hozzon létre egy Azure IoT Hub (.NET) sablon használatával |} A Microsoft Docs
description: Hogyan lehet egy Azure Resource Manager-sablon használatával hozzon létre egy IoT hubra C#-program.
author: robinsh
manager: philmea
ms.author: robin.shahan
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: 4acf7a8bbc02a10468004092152b1d7c5c4d621f
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58075918"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Hozzon létre egy IoT hub (.NET) az Azure Resource Manager-sablon használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Használhatja az Azure Resource Manager hozhat létre, és az Azure IoT hubs programozással felügyelheti. Az oktatóanyag bemutatja, hogyan hozzon létre egy IoT hubra a C#-program Azure Resource Manager-sablon használatával.

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik:  [Az Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk ismerteti az Azure Resource Manager üzemi modell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* Egy [Azure Storage-fiók] [ lnk-storage-account] az Azure Resource Manager-sablon fájlok tárolására szolgál.
* [Az Azure PowerShell 1.0] [ lnk-powershell-install] vagy újabb.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>A Visual Studio-projektek előkészítése

1. A Visual Studióban hozzon létre egy Visual C# Windows klasszikus Asztalialkalmazás-projektet az a **Console App (.NET Framework)** projektsablonnal. Adja a projektnek **CreateIoTHub**.

2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és kattintson a **NuGet-csomagok kezelése**.

3. Ellenőrizze a NuGet Package Manager **Include prerelease**, és az a **Tallózás** lapon keresse meg **Microsoft.Azure.Management.ResourceManager**. Válassza ki a csomagot, kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson **elfogadom** fogadására a licenceket.

4. A NuGet-Csomagkezelőt, keressen **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson a **elfogadom** elfogadásához.

5. A program.cs fájlban cserélje le a meglévő **használatával** utasítások a következő kóddal:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. A program.cs fájlban adja hozzá a következő statikus változókat, és cserélje le a helyőrző értékeket. Jegyezze fel végzett **ApplicationId**, **SubscriptionId**, **TenantId**, és **jelszó** Ez az oktatóanyag korábbi. **Az Azure Storage-fiók neve** az Azure Storage-fiók neve, ahol az Azure Resource Manager-sablon fájlokat tárolja. **Erőforráscsoport neve** használhatja az IoT hub létrehozásakor az erőforráscsoport neve. A név lehet egy már meglévő vagy új erőforráscsoportot. **A központi telepítés neve** el például egy nevet a központi telepítéshez **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>IoT hub létrehozása sablon beküldése

Egy JSON-sablonnal és paraméterfájlokkal fájl használatával hozzon létre egy IoT hubot az erőforráscsoportban. Az Azure Resource Manager-sablonok segítségével módosíthatja egy meglévő IoT hubbal.

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, és kattintson a **új elem**. Adjon hozzá egy JSON-fájlt nevű **template.json** a projekthez.

2. Egy standard szintű IoT hubot az hozzáadása a **USA keleti Régiójában** régió, cserélje le a tartalmát **template.json** a következő erőforrás-definícióval. A régiók, amelyek támogatják az IoT Hub aktuális listáját lásd: [Azure állapotlapján][lnk-status]:

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

3. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, és kattintson a **új elem**. Adjon hozzá egy JSON-fájlt nevű **parameters.json** a projekthez.

4. Cserélje le a tartalmát **parameters.json** a következő adatokkal paraméter, amely egy nevet az új IoT hub például **{saját monogramjával} mynewiothub**. Az IoT hub nevének globálisan egyedinek kell lennie, így tartalmaznia kell a név vagy initials:

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

5. A **Server Explorer**, az Azure-előfizetéshez csatlakozzon, és nevű tároló létrehozása az Azure Storage-fiókban **sablonok**. Az a **tulajdonságok** panelen, és állítsa a **nyilvános olvasási hozzáférés** engedélyeit a **sablonok** tároló **Blob**.

6. A **Server Explorer**, kattintson a jobb gombbal a **sablonok** tárolót, majd kattintson **nézet Blobtároló**. Kattintson a **Blob feltöltése** gombra, válassza ki a két fájlt **parameters.json** és **templates.json**, és kattintson a **nyílt** feltölteni a JSON-fájlokat a **sablonok** tároló. A blobok JSON-adatokat tartalmazó URL-címei a következők:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. A program.cs fájlhoz adja hozzá a következő metódust:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adja hozzá a következő kódot a **CreateIoTHub** metódust az Azure Resource Manager sablonnal és paraméterfájlokkal fájlok küldje el:

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

9. Adja hozzá a következő kódot a **CreateIoTHub** metódushoz, amely az állapot és a kulcsokat, új IoT hub jeleníti meg:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Írja be az alkalmazás futtatása

Az alkalmazás meghívásával már végrehajthatók a **CreateIoTHub** módszer előtt felépíti és futtatja azt.

1. Adja hozzá a következő kódot végéhez a **fő** módszer:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kattintson a **összeállítása** , majd **megoldás**. Javítsa az esetleges hibákat.

3. Kattintson a **Debug** , majd **Start Debugging** az alkalmazás futtatásához. Futtassa az üzembe helyezés több percig is eltarthat.

4. Annak ellenőrzéséhez, hogy az alkalmazás hozzá az új IoT hubot, látogasson el a [az Azure portal] [ lnk-azure-portal] és erőforrások listájának megtekintése. Másik megoldásként használhatja a **Get-AzResource** PowerShell-parancsmagot.

> [!NOTE]
> Ez a példa az alkalmazás hozzáadása egy S1 Standard IoT hubot, amelyhez számítunk fel díjat. Törölheti az IoT hubon keresztül a [az Azure portal] [ lnk-azure-portal] vagy a **Remove-AzResource** PowerShell-parancsmagot, ha elkészült.

## <a name="next-steps"></a>További lépések
Most egy IoT hubra C#-program az Azure Resource Manager-sablon használatával telepített, akkor érdemes vizsgálódáshoz:

* Olvassa el a képességeit a [az IoT Hub erőforrás-szolgáltató REST API-val][lnk-rest-api].
* Olvasási [Azure Resource Manager áttekintése] [ lnk-azure-rm-overview] további információ ezekről a képességekről az Azure Resource Manager.
* A JSON-szintaxist és a sablonok tulajdonságait: [Microsoft.Devices erőforrástípusok](/azure/templates/microsoft.devices/iothub-allversions).

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Bevezetés a C SDK-t][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
