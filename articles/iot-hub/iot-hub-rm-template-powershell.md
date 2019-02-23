---
title: Hozzon létre egy Azure IoT Hub (PowerShell) sablon használatával |} A Microsoft Docs
description: Hogyan IoT Hub létrehozása a PowerShell-lel az Azure Resource Manager-sablon használatával.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 474360bfa874298b35b1e7c754545ff576013d50
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734744"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Hozzon létre egy IoT hubon az Azure Resource Manager-sablon (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Használhatja az Azure Resource Manager hozhat létre, és az Azure IoT hubs programozással felügyelheti. Ez az oktatóanyag bemutatja, hogyan IoT hub létrehozása a PowerShell-lel az Azure Resource Manager-sablon használatával.

> [!NOTE]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Az Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk ismerteti az Azure Resource Manager üzemi modell használatával.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure PowerShell 1.0] [ lnk-powershell-install] vagy újabb.

> [!TIP]
> A cikk [az Azure PowerShell az Azure Resource Manager] [ lnk-powershell-arm] hozhat létre Azure-erőforrások PowerShell- és Azure Resource Manager-sablonok használatával kapcsolatos további információkat biztosít.

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

Adja meg egy PowerShell-parancssort, jelentkezzen be az Azure-előfizetéshez az alábbi parancsot:

```powershell
Connect-AzAccount
```

Ha több Azure-előfizetéssel rendelkezik, az Azure-bA bejelentkezik hozzáférést, az összes Azure-előfizetések a hitelesítő adatokhoz tartozó. Használja a következő parancs használható elérhető Azure-előfizetések listázásához:

```powershell
Get-AzSubscription
```

Az alábbi parancs segítségével válassza ki azt az előfizetést, amelyet az IoT Hub létrehozásához szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

```powershell
Select-AzSubscription `
    -SubscriptionName "{your subscription name}"
```

A következő parancsok segítségével Fedezze fel, ahol üzembe egy IoT hubot és a jelenleg támogatott API-verzió:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Hozzon létre egy erőforráscsoportot, amelybe az IoT hub az IoT hub a támogatott helyek a következő paranccsal tartalmaz. Ez a példa létrehoz egy nevű erőforráscsoportot **MyIoTRG1**:

```powershell
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>IoT hub létrehozása sablon beküldése

Egy JSON-sablon használatával hozzon létre egy IoT hubot az erőforráscsoportban. Az Azure Resource Manager-sablonok segítségével módosíthatja egy meglévő IoT hubbal.

1. Egy szövegszerkesztő használatával hozzon létre egy Azure Resource Manager-sablon nevű **template.json** egy új standard IoT hub létrehozása a következő erőforrás-definícióval. Ebben a példában az IoT Hub hozzáadja a **USA keleti Régiójában** régió, két fogyasztói csoportot hoz létre (**cg1** és **cg2**) az Event Hub-kompatibilis végponthoz, és használja a  **2016-02-03** API-verzió. Ez a sablon emellett arra számít, hogy az IoT hub nevére paraméterként adja át nevű **hubName**. A helyek, amelyek támogatják az IoT Hub aktuális listáját lásd: [Azure állapotlapján][lnk-status].

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

2. Mentse az Azure Resource Manager sablon fájlt a helyi gépen. Ez a példa feltételezi, hogy egy nevű mappába menti **c:\templates**.

3. Futtassa a következő parancsot az új IoT hub, az IoT hub nevére átadott paraméterként üzembe helyezéséhez. Ebben a példában az IoT hub nevére van `abcmyiothub`. Az IoT hub nevére globálisan egyedinek kell lennie:

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
  [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. A kimenet megjeleníti a kulcsokat a létrehozott IoT hub.

5. Annak ellenőrzéséhez, hogy az alkalmazás hozzá az új IoT hubot, látogasson el a [az Azure portal] [ lnk-azure-portal] és erőforrások listájának megtekintése. Másik megoldásként használhatja a **Get-AzResource** PowerShell-parancsmagot.

> [!NOTE]
> Ez a példa az alkalmazás hozzáadása egy S1 Standard IoT hubot, amelyhez számítunk fel díjat. Törölheti az IoT hubon keresztül a [az Azure portal] [ lnk-azure-portal] vagy a **Remove-AzResource** PowerShell-parancsmagot, ha elkészült.

## <a name="next-steps"></a>További lépések

Most egy IoT hubot, a PowerShell-lel az Azure Resource Manager-sablon használatával telepített, akkor érdemes vizsgálódáshoz:

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
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/powershell-azure-resource-manager.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
