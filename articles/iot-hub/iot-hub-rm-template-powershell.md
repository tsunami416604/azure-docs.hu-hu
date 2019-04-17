---
title: Hozzon létre egy Azure IoT Hub (PowerShell) sablon használatával |} A Microsoft Docs
description: Hogyan lehet egy Azure Resource Manager-sablon használatával IoT Hub létrehozása az Azure PowerShell használatával.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: d23d3824c477d3bba4e4900bee355376f1317f92
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609173"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Hozzon létre egy IoT hubon az Azure Resource Manager-sablon (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Útmutató egy IoT hubot és a egy fogyasztói csoport létrehozása Azure Resource Manager-sablon használatával. A Resource Manager-sablonok JSON-fájlok, melyek az adott megoldáshoz telepítendő erőforrásokat határozzák meg. Resource Manager-sablonokkal való fejlesztésével kapcsolatos további információkért lásd: [Azure Resource Manager dokumentációjában](https://docs.microsoft.com/azure/azure-resource-manager/).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

A rendszer a rövid útmutatóban használt Resource Manager-sablon [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/). Itt látható a sablon egy példányát:

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

A sablon létrehoz egy Azure Iot hub három végpontok (eventhub, felhőből az eszközre és üzenetkezelés) és a egy fogyasztói csoportot. További sablonminták, lásd: [Azure gyorsindítási sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular). Az Iot Hub sablonséma található [Itt](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions).

Többféleképpen sablon üzembe helyezéséhez.  Ebben az oktatóanyagban használhatja a Powershellt.

A PowerShell-szkript futtatásához válassza **kipróbálás** az Azure Cloud shell megnyitásához. Illessze be a parancsfájlt, kattintson a jobb gombbal a rendszerhéj, és válassza a beillesztés:

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

Amint láthatja, a PowerShell-parancsprogram, használt sablon Azure gyorsindítási sablonok származik. Használja a saját, kell először töltse fel a sablon fájlt a Cloud shellben, és használja a `-TemplateFile` kapcsolót, hogy adja meg a fájlnevet.  Egy vonatkozó példáért lásd: [helyezheti üzembe a sablont](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template).

## <a name="next-steps"></a>További lépések

Most egy IoT hubra az Azure Resource Manager-sablon használatával telepített, érdemes vizsgálódáshoz:

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
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
