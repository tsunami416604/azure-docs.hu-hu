---
title: Hozzon létre egy Azure IoT Hub sablonnal (.NET) |} Microsoft Docs
description: Hogyan használható az Azure Resource Manager-sablonok az IoT-központ létrehozása a C# programban.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.devlang: csharp
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 1a64749b7218fccfdad6b6eeebfac39a44aa0522
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34635542"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-net"></a>Létrehoz egy IoT-központot, Azure Resource Manager sablonnal (.NET)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager hozhat létre és kezelhet programozott módon Azure IoT-központok. Az oktatóanyag bemutatja, hogyan használható az Azure Resource Manager-sablon IoT hub létrehozása a C# programban.

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk az Azure Resource Manager telepítési modell használatát bemutatja.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Visual Studio 2015 vagy Visual Studio 2017.
* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* Egy [Azure Storage-fiók] [ lnk-storage-account] az Azure Resource Manager sablon fájlok tárolására.
* [Az Azure PowerShell 1.0] [ lnk-powershell-install] vagy újabb.

[!INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## <a name="prepare-your-visual-studio-project"></a>Készítse elő a Visual Studio-projekt

1. A Visual Studio project using Visual C# klasszikus Windows asztal létrehozása a **Konzolalkalmazás (.NET-keretrendszer)** projektsablon. Nevet a projektnek **CreateIoTHub**.

2. A Megoldáskezelőben kattintson a jobb gombbal a projektre, és kattintson a **NuGet-csomagok kezelése**.

3. Ellenőrizze a NuGet Package Manager **Include prerelease**, és a a **Tallózás** lapon keresse meg **Microsoft.Azure.Management.ResourceManager**. Válassza ki a csomagot, kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson a **elfogadom** fogadására a licencek.

4. Keressen rá a NuGet Package Manager **Microsoft.IdentityModel.Clients.ActiveDirectory**.  Kattintson a **telepítése**, a **változások áttekintése** kattintson **OK**, majd kattintson a **elfogadom** elfogadja a licencfeltételeket.

5. A productscontract.cs fájlban cserélje le a meglévő **használatával** utasítások a következő kóddal:

    ```csharp
    using System;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```

6. A program.cs fájlban adja hozzá a következő statikus változó a helyőrző értékeket. A feljegyezte **ApplicationId**, **SubscriptionId**, **TenantId**, és **jelszó** az oktatóanyag korábbi. **Az Azure Storage-fiók neve** van az Azure Storage-fiók nevét, az Azure Resource Manager sablon fájlokat tárolja. **Az erőforráscsoport neve** az IoT hub létrehozása során használ az erőforráscsoport neve. A név lehet egy már meglévő vagy új erőforráscsoportot. **A központi telepítés neve** van, mint a központi telepítés nevét **Deployment_01**.

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

## <a name="submit-a-template-to-create-an-iot-hub"></a>Az IoT-központ létrehozása sablon küldése

A JSON-sablonnal és paraméterfájlokkal fájl segítségével létrehoz egy IoT-központot az erőforráscsoportban. Az Azure Resource Manager-sablon segítségével módosíthatja egy meglévő IoT-központot.

1. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, és kattintson a **új elem**. Adja hozzá a JSON-fájl neve **template.json** a projekthez.

2. A szabványos IoT-központ hozzáadása a **USA keleti régiója** régió, cserélje ki a tartalmát **template.json** a következő erőforrás-definícióval. Az aktuális IoT Hub-t támogató régiók listáját lásd: [Azure állapot][lnk-status]:

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

3. A Megoldáskezelőben kattintson a jobb gombbal a projektre, kattintson a **Hozzáadás**, és kattintson a **új elem**. Adja hozzá a JSON-fájl neve **parameters.json** a projekthez.

4. Cserélje le a tartalmát **parameters.json** a következő paraméter információkat, például az új IoT hub nevét megadó **{saját monogramjával} mynewiothub**. Az IoT-központnév globálisan egyedinek kell lennie, a név vagy initials tartalmaznia kell:

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

5. A **Server Explorer**, az Azure-előfizetéshez csatlakozni és az Azure Storage-fiókban létrehozni egy tárolót, úgynevezett **sablonok**. Az a **tulajdonságok** panelen, állítsa be a **nyilvános olvasási hozzáférés** engedélyeit a **sablonok** tárolót, hogy **Blob**.

6. A **Server Explorer**, kattintson a jobb gombbal a **sablonok** tárolót, majd kattintson **nézet Blob tároló**. Kattintson a **Blob feltöltése** gombra, válassza ki a két fájl **parameters.json** és **templates.json**, és kattintson a **nyissa meg** a JSON-fájlok feltöltése a **sablonok** tároló. A blobok JSON-adatokat tartalmazó URL-címei a következők:

    ```csharp
    https://{Your storage account name}.blob.core.windows.net/templates/parameters.json
    https://{Your storage account name}.blob.core.windows.net/templates/template.json
    ```
7. Adja hozzá a következő metódust a program.cs fájlt:

    ```csharp
    static void CreateIoTHub(ResourceManagementClient client)
    {

    }
    ```

8. Adja hozzá a következő kódot a **CreateIoTHub** elküldeni a sablonnal és paraméterfájlokkal fájlok az Azure erőforrás-kezelő metódus:

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

9. Adja hozzá a következő kódot a **CreateIoTHub** módszer, amelynek állapota és a kulcsokat az új IoT hub jeleníti meg:

    ```csharp
    string state = createResponse.Properties.ProvisioningState;
    Console.WriteLine("Deployment state: {0}", state);

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
    }
    Console.WriteLine(createResponse.Properties.Outputs);
    ```

## <a name="complete-and-run-the-application"></a>Végezze el, és futtassa az alkalmazást

Meghívásával hajthatja végre az alkalmazás most már a **CreateIoTHub** metódus előtt most felépíti és futtatja azt.

1. Adja hozzá a következő kódot végén a **fő** módszert:

    ```csharp
    CreateIoTHub(client);
    Console.ReadLine();
    ```

2. Kattintson a **Build** , majd **megoldás**. Javíthatja az esetleges hibákat.

3. Kattintson a **Debug** , majd **Start Debugging** az alkalmazás futtatásához. A központi telepítés futtatása több percig is eltarthat.

4. Ellenőrizze, hogy az alkalmazás fel az új IoT hub, látogasson el a [Azure-portálon] [ lnk-azure-portal] és tekintse meg az erőforrások listáját. Másik megoldásként használhatja a **Get-AzureRmResource** PowerShell-parancsmagot.

> [!NOTE]
> A mintaalkalmazás ad hozzá egy S1 Standard IoT-központot, amelynek kell fizetni. Az IoT hub használatával törölheti a [Azure-portálon] [ lnk-azure-portal] vagy használatával a **Remove-AzureRmResource** PowerShell-parancsmag, amikor elkészült.

## <a name="next-steps"></a>További lépések
Most egy IoT-központot egy C# programban egy Azure Resource Manager sablon használatával telepített, akkor érdemes lehet további felfedezése:

* Olvassa el a képességeit a [IoT-központ erőforrás-szolgáltató REST API][lnk-rest-api].
* Olvasási [Azure Resource Manager áttekintése] [ lnk-azure-rm-overview] tudhat meg többet az Azure Resource Manager képességeit.

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [C SDK bemutatása][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-storage-account]:../storage/common/storage-create-storage-account.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
