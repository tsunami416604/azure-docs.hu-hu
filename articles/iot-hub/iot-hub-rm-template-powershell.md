---
title: Azure-IoT Hub létrehozása sablon használatával (PowerShell) | Microsoft Docs
description: Egy Azure Resource Manager-sablon használata az Azure PowerShell IoT Hub létrehozásához.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: a1f878361a96c5584f43b31135d79ff799f66efa
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2020
ms.locfileid: "75976626"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>IoT hub létrehozása Azure Resource Manager sablon használatával (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Megtudhatja, hogyan hozhat létre egy IoT Hub és egy fogyasztói csoportot egy Azure Resource Manager sablon használatával. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. További információ a Resource Manager-sablonok fejlesztéséről: [Azure Resource Manager dokumentáció](https://docs.microsoft.com/azure/azure-resource-manager/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az ebben a rövid útmutatóban használt Resource Manager-sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)származik. A sablon egy másolata:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

A sablon létrehoz egy Azure IOT hubot három végponttal (eventhub, felhőből eszközre és üzenetküldéssel) és egy fogyasztói csoporttal. További sablon-példákat az [Azure Gyorsindítás sablonjai](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)című témakörben talál. Az IOT hub-sablon sémája [itt](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)található.

A sablonok üzembe helyezésének számos módja van.  Ebben az oktatóanyagban Azure PowerShell használ.

A PowerShell-szkript futtatásához válassza a **kipróbálás** lehetőséget az Azure Cloud Shell megnyitásához. A szkript beillesztéséhez kattintson a jobb gombbal a rendszerhéjra, majd válassza a Beillesztés parancsot:

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

Ahogy a PowerShell-szkriptből is látható, a használt sablon az Azure Gyorsindítás sablonjaiból származik. A saját használatához először fel kell töltenie a sablonfájlt a Cloud shellbe, majd a `-TemplateFile` kapcsoló használatával kell megadnia a fájlnevet.  Példaként tekintse meg [a sablon üzembe helyezése](../azure-resource-manager/templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)című témakört.

## <a name="next-steps"></a>Következő lépések

Most, hogy Azure Resource Manager sablonnal telepített egy IoT hub-t, érdemes megvizsgálnia az alábbiakat:

* További információ a [IoT hub erőforrás-szolgáltató REST API][lnk-rest-api]képességeiről.
* A Azure Resource Manager képességeinek megismeréséhez olvassa el [Azure Resource Manager áttekintést][lnk-azure-rm-overview] .
* A sablonokban használandó JSON-szintaxis és-tulajdonságok megtekintéséhez lásd: [Microsoft. Devices erőforrástípusok](/azure/templates/microsoft.devices/iothub-allversions).

Ha többet szeretne megtudni a IoT Hub fejlesztéséről, tekintse meg a következő cikkeket:

* [A C SDK bemutatása][lnk-c-sdk]
* [Azure IoT SDK-k][lnk-sdks]

A IoT Hub képességeinek további megismeréséhez lásd:

* [AI üzembe helyezése az Edge-eszközökön Azure IoT Edge][lnk-iotedge]

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
