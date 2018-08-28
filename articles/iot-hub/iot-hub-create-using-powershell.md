---
title: Egy PowerShell-parancsmag segítségével az Azure IoT Hub létrehozása |} A Microsoft Docs
description: Hogyan egy PowerShell-parancsmag használatával hozzon létre egy IoT hubot.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2018
ms.author: robinsh
ms.openlocfilehash: f9903781a998db8192e3958ae386b7420f56fd31
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2018
ms.locfileid: "43045626"
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>A New-AzureRmIotHub parancsmaggal IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Bevezetés

Azure PowerShell-parancsmagok segítségével létrehozása és kezelése az Azure IoT-központokat. Ez az oktatóanyag bemutatja, hogyan az IoT hub létrehozása a PowerShell használatával.

Ez az útmutató végrehajtásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

A Cloud Shellt használja, ha Ön már bejelentkezett az előfizetéshez. Ha futtatja a Powershellt helyileg helyette, adja meg a jelentkezzen be az Azure-előfizetéshez az alábbi parancsot:

```powershell
# Log into Azure account.
Login-AzureRMAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport üzembe helyezéséhez egy IoT hubra van szüksége. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.

Az IoT hub egy erőforráscsoport létrehozásához használja a [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup) parancsot. Ez a példa létrehoz egy nevű erőforráscsoportot **MyIoTRG1** a a **USA keleti Régiójában** régió:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az előző lépésben létrehozott erőforráscsoportot az IoT hub létrehozásához használja a [New-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/New-AzureRmIotHub) parancsot. Ez a példa létrehoz egy **S1** nevű hub **MyTestIoTHub** a a **USA keleti Régiójában** régió:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Az IoT hub nevére globálisan egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Az előfizetés használatával listázhatja az összes IoT-központok a [Get-AzureRmIotHub](https://docs.microsoft.com/powershell/module/AzureRM.IotHub/Get-AzureRmIotHub) parancsot:

```powershell
Get-AzureRmIotHub
```

Ez a példa bemutatja a Standard IoT Hub S1, az előző lépésben létrehozott. 

Az IoT hub használatával törölheti az [Remove-AzureRmIotHub](https://docs.microsoft.com/powershell/module/azurerm.iothub/remove-azurermiothub) parancsot:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Azt is megteheti, eltávolíthatja az erőforráscsoport és az összes erőforrást tartalmaz használatával a [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/AzureRM.Resources/Remove-AzureRmResourceGroup) parancsot:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>További lépések

Most egy IoT hubot, egy PowerShell-parancsmag segítségével telepítette fel, ha szeretne további ismerje meg, tekintse meg a következő cikkeket:

* [PowerShell-parancsmagok használata az IoT hub](https://docs.microsoft.com/powershell/module/azurerm.iothub/).

* [Az IoT Hub erőforrás-szolgáltató REST API-val](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Bevezetés a C SDK-t](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)