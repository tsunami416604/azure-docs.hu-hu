---
title: Hozzon létre egy Azure IoT Hub sablon (PowerShell) | Microsoft dokumentumok
description: Azure Resource Manager-sablon használata az Azure PowerShell használatával ioT Hub létrehozásához.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75976626"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>IoT-központ létrehozása az Azure Resource Manager-sablonnal (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Megtudhatja, hogyan hozhat létre egy IoT Hub- és egy fogyasztói csoportot egy Azure Resource Manager-sablon használatával. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Az Erőforrás-kezelő sablonjainak fejlesztéséről az [Azure Resource Manager dokumentációjában](https://docs.microsoft.com/azure/azure-resource-manager/)olvashat bővebben.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rövid útmutatóban használt Resource Manager-sablon az [Azure gyorsindítási sablonjaiból származik.](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/) Itt van egy példányát a sablon:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

A sablon létrehoz egy Azure Iot hub három végpont (eventhub, felhő-eszköz és üzenetküldés), és egy fogyasztói csoport. További sablonmintákért tekintse meg [az Azure gyorsindítási sablonjait.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular) Az Iot Hub sablonséma [itt](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)található.

A sablonok üzembe helyezésének számos módja van.  Az Azure PowerShell ebben az oktatóanyagban.

A PowerShell-parancsfájl futtatásához válassza **a Próbálja ki** az Azure Cloud rendszerhéj megnyitásához. A parancsfájl beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, és válassza a Beillesztés parancsot:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

Ahogy a PowerShell-parancsfájlból is láthatja, a használt sablon az Azure gyorsindítási sablonjaiból származik. A saját használatához először fel kell töltenie a sablonfájlt a `-TemplateFile` felhőbeli rendszerhéjba, majd a kapcsolóval meg kell adnia a fájl nevét.  Például a Sablon telepítése című [témakörben olvashat.](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)

## <a name="next-steps"></a>További lépések

Most már üzembe helyezett egy IoT-központot egy Azure Resource Manager-sablon használatával, további lehetőségeket is megvizsgálhat:

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
[lnk-powershell-arm]: ../azure-resource-manager/management/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
