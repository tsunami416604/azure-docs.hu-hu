---
title: Sablon (PowerShell) segítségével Azure IoT Hub létrehozása |} Microsoft Docs
description: Hogyan használható az Azure Resource Manager-sablonok az IoT-központ létrehozása a PowerShell használatával.
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 7eade855-c289-4ffb-b5ef-02be8c5f670f
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 0e5f95d98f772b226e162f601939bc94bf8fb78b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Létrehoz egy IoT-központot, Azure Resource Manager sablonnal (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager hozhat létre és kezelhet programozott módon Azure IoT-központok. Ez az oktatóanyag bemutatja, hogyan Azure Resource Manager-sablonok segítségével az IoT-központ létrehozása a PowerShell használatával.

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk az Azure Resource Manager telepítési modell használatát bemutatja.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure PowerShell 1.0] [ lnk-powershell-install] vagy újabb.

> [!TIP]
> A cikk [az Azure PowerShell használata Azure Resource Managerrel] [ lnk-powershell-arm] PowerShell és az Azure Resource Manager sablonok létrehozása az Azure-erőforrások használatával kapcsolatos további információk.

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

Adja meg egy PowerShell-parancssort, jelentkezzen be az Azure-előfizetéshez a következő parancsot:

```powershell
Connect-AzureRmAccount
```

Ha több Azure-előfizetéssel rendelkezik, a jelentkezik be az Azure ad hozzáférést az összes Azure-előfizetést a hitelesítő adatok társított. Használja a következő parancsot a rendelkezésre álló használata Azure-előfizetések listázásához:

```powershell
Get-AzureRMSubscription
```

Az alábbi parancs segítségével válassza ki azt az előfizetést, amelyet az IoT Hub létrehozásához szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

A következő parancsok segítségével felderítése, ahol az IoT-központ és a jelenleg támogatott API-verziók telepítheti:

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Hozzon létre egy erőforráscsoportot az IoT hub, az alábbi paranccsal egy IoT-központ támogatott helyeket tartalmaz. Ebben a példában egy nevű erőforráscsoportot hoz létre **MyIoTRG1**:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Az IoT-központ létrehozása sablon küldése

A JSON-sablon használatával létrehoz egy IoT-központot az erőforráscsoportban. Az Azure Resource Manager-sablon segítségével módosíthatja egy meglévő IoT-központot.

1. Egy szövegszerkesztő használatával hozzon létre egy Azure Resource Manager-sablon neve **template.json** egy új, normál IoT-központ létrehozásához a következő erőforrás-definícióval. Ebben a példában az IoT-központ hozzáadja a **USA keleti régiója** régió, létrehoz két felhasználói csoportok (**cg1** és **cg2**) Event Hub-kompatibilis végpontot, és használja a  **2016-02-03** API-verzió. Ez a sablon is vár, hogy az IoT-központnév paraméterként adjon át nevű **hubName**. A helyek, amelyek támogatják az IoT-központ aktuális listáját lásd: [Azure állapot][lnk-status].

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
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
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

2. Mentse az Azure Resource Manager sablon fájlt a helyi számítógépen. Ebben a példában feltételezzük, hogy nevű mappába menti **c:\templates**.

3. A következő paranccsal telepítheti az új IoT hub, amely az IoT hub nevét átadott paraméterként. Ebben a példában az IoT-központ neve nem `abcmyiothub`. Az IoT hub nevét globálisan egyedinek kell lennie:

    ```powershell
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. A kimenet a kulcsokat az IoT hub létrehozott jeleníti meg.

5. Ellenőrizze, hogy az alkalmazás fel az új IoT hub, látogasson el a [Azure-portálon] [ lnk-azure-portal] és tekintse meg az erőforrások listáját. Másik megoldásként használhatja a **Get-AzureRmResource** PowerShell-parancsmagot.

> [!NOTE]
> A mintaalkalmazás ad hozzá egy S1 Standard IoT-központot, amelynek kell fizetni. Az IoT hub használatával törölheti a [Azure-portálon] [ lnk-azure-portal] vagy használatával a **Remove-AzureRmResource** PowerShell-parancsmag, amikor elkészült.

## <a name="next-steps"></a>További lépések

Most egy IoT-központot, a PowerShell segítségével az Azure Resource Manager-sablon használatával telepített, akkor érdemes lehet további felfedezése:

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
[lnk-powershell-install]: /powershell/azure/install-azurerm-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
