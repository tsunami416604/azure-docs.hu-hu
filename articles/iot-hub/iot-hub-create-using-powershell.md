---
title: Hozzon létre egy Azure IoT Hub ot PowerShell-parancsmag használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan használhatja a PowerShell-parancsmagokat egy erőforráscsoport létrehozásához, majd hozzon létre egy IoT-központot az erőforráscsoportban. Azt is megtudhatja, hogyan távolíthatja el a hubot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.author: robinsh
ms.openlocfilehash: 9c49f7ac744ee516aefc1571d50264132035ba8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73890602"
---
# <a name="create-an-iot-hub-using-the-new-aziothub-cmdlet"></a>IoT-központ létrehozása az Új-AzIotHub-parancsmag használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Bevezetés

Az Azure PowerShell-parancsmagokkal azure IoT-központokat hozhat létre és kezelhet. Ez az oktatóanyag bemutatja, hogyan hozhat létre egy IoT hub powershellnel.

Az útmutató befejezéséhez Azure-előfizetésre van szüksége. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez

Ha a Cloud Shellt használja, már be van jelentkezve az előfizetésbe. Ha ehelyett helyileg futtatja a PowerShellt, írja be a következő parancsot az Azure-előfizetésbe való bejelentkezéshez:

```powershell
# Log into Azure account.
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Egy IOt-központ üzembe helyezéséhez erőforráscsoportra van szükség. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Erőforráscsoport létrehozásához az IoT hub, használja a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/New-azResourceGroup) parancsot. Ez a példa létrehoz egy **MyIoTRG1** nevű erőforráscsoportot az **USA keleti régiójában:**

```azurepowershell-interactive
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Ha az előző lépésben létrehozott erőforráscsoportban hozzon létre egy IoT-központot, használja a [New-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/New-azIotHub) parancsot. Ez a példa létrehoz egy **S1** hub nevű **MyTestIoTHub** az **USA keleti régiójában:**

```azurepowershell-interactive
New-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Az IoT hub nevének globálisan egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

A [Get-AzIotHub](https://docs.microsoft.com/powershell/module/az.IotHub/Get-azIotHub) paranccsal az előfizetésében lévő összes IoT-központot listázhatja:

```azurepowershell-interactive
Get-AzIotHub
```

Ebben a példában az előző lépésben létrehozott S1 standard IoT Hub látható.

Az IoT-hub az [Remove-AzIotHub](https://docs.microsoft.com/powershell/module/az.iothub/remove-aziothub) paranccsal törölhető:

```azurepowershell-interactive
Remove-AzIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Másik lehetőségként eltávolíthatja az erőforráscsoportot és a benne lévő összes erőforrást az [Eltávolítás-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.Resources/Remove-azResourceGroup) paranccsal:

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>További lépések

Most egy PowerShell-parancsmag használatával telepített egy IoT-központot, ha további lehetőségeket szeretne feltárni, tekintse meg a következő cikkeket:

* [PowerShell-parancsmagok az IoT hub használatával való munkához.](https://docs.microsoft.com/powershell/module/az.iothub/)

* [IoT Hub erőforrás-szolgáltató REST API.](https://docs.microsoft.com/rest/api/iothub/iothubresource)

Ha többet szeretne tudni az IoT Hub fejlesztéseiről, olvassa el az alábbi cikkeket:

* [Bevezetés a C SDK-ba](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK-k](iot-hub-devguide-sdks.md)

Az IoT Hub képességeinek további megismeréséhez lásd:

* [Mesterséges intelligencia telepítése peremeszközökön az Azure IoT Edge szolgáltatással](../iot-edge/tutorial-simulate-device-linux.md)
