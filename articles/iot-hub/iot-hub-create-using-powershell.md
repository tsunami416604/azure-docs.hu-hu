---
title: Egy PowerShell-parancsmag segítségével az Azure IoT Hub létrehozása |} A Microsoft Docs
description: Hogyan egy PowerShell-parancsmag használatával hozzon létre egy IoT hubot.
author: robinsh
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 6462673f6c3992aacbaee168eafc6bdb1b2fa944
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56733470"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>A New-AzIotHub parancsmaggal IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Bevezetés

Azure PowerShell-parancsmagok segítségével létrehozása és kezelése az Azure IoT-központokat. Ez az oktatóanyag bemutatja, hogyan az IoT hub létrehozása a PowerShell használatával.

Ez az útmutató végrehajtásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

A Cloud Shellt használja, ha Ön már bejelentkezett az előfizetéshez. Ha futtatja a Powershellt helyileg helyette, adja meg a jelentkezzen be az Azure-előfizetéshez az alábbi parancsot:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Egy erőforráscsoport üzembe helyezéséhez egy IoT hubra van szüksége. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.

Az IoT hub egy erőforráscsoport létrehozásához használja a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) parancsot. Ez a példa létrehoz egy nevű erőforráscsoportot **MyIoTRG1** a a **USA keleti Régiójában** régió:

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az előző lépésben létrehozott erőforráscsoportot az IoT hub létrehozásához használja a [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) parancsot. Ez a példa létrehoz egy **S1** nevű hub **MyTestIoTHub** a a **USA keleti Régiójában** régió:

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Az IoT hub nevére globálisan egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Az előfizetés használatával listázhatja az összes IoT-központok a [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) parancsot:

```azurepowershell-interactive
Get-AzIotHub
```

Ez a példa bemutatja a Standard IoT Hub S1, az előző lépésben létrehozott.

Az IoT hub használatával törölheti az [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) parancsot:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Azt is megteheti, eltávolíthatja az erőforráscsoport és az összes erőforrást tartalmaz használatával a [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) parancsot:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>További lépések

Most egy IoT hubot, egy PowerShell-parancsmag segítségével telepítette fel, ha szeretne további ismerje meg, tekintse meg a következő cikkeket:

* [PowerShell-parancsmagok használata az IoT hub](https://docs.microsoft.com/powershell/module/az.iothub/).

* [Az IoT Hub erőforrás-szolgáltató REST API-val](https://docs.microsoft.com/rest/api/iothub/iothubresource).

Az IoT Hub fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [Bevezetés a C SDK-t](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Részletesebb megismerése az IoT Hub képességeit, tekintse meg:

* [Edge-eszközök mesterséges Intelligencia telepítése az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
