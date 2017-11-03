---
title: "PowerShell-parancsmag használatával Azure IoT Hub létrehozása |} Microsoft Docs"
description: "Hogyan lehet egy PowerShell-parancsmag segítségével létrehoz egy IoT-központot."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2017
ms.author: dobett
ms.openlocfilehash: 02227adeb8a9a7463506efa44ddc2977f8aae65a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-iot-hub-using-the-new-azurermiothub-cmdlet"></a>A New-AzureRmIotHub parancsmaggal IoT hub létrehozása

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Bevezetés

Azure PowerShell-parancsmagok segítségével létrehozása és kezelése az Azure IoT-központok. Ez az oktatóanyag bemutatja, hogyan az IoT-központ létrehozása a PowerShell használatával.

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Ez a cikk az Azure Resource Manager telepítési modell használatát bemutatja.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. <br/>Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure PowerShell-parancsmagok][lnk-powershell-install].

## <a name="connect-to-your-azure-subscription"></a>Csatlakozás az Azure-előfizetéshez
Adja meg egy PowerShell-parancssort, jelentkezzen be az Azure-előfizetéshez a következő parancsot:

```powershell
Login-AzureRmAccount
```

Ha több Azure-előfizetéssel rendelkezik, a jelentkezik be az Azure ad hozzáférést az összes Azure-előfizetést a hitelesítő adatok társított. Használja a következő parancsot a rendelkezésre álló használata Azure-előfizetések listázásához:

```powershell
Get-AzureRMSubscription
```

Az alábbi parancs segítségével válassza ki, hogy az IoT hub létrehozására szolgáló parancsok futtatásához használni kívánt előfizetést. Az előfizetés neve vagy azonosítója is használhatja, ha az előző parancs kimenetében:

```powershell
Select-AzureRMSubscription `
    -SubscriptionName "{your subscription name}"
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Az IoT-központ telepítéséhez erőforráscsoport van szüksége. Használjon egy meglévő erőforráscsoportot, vagy hozzon létre egy újat.

A következő paranccsal felderítése a helyek, ahol az IoT-központ telepítheti:

```powershell
((Get-AzureRmResourceProvider `
  -ProviderNamespace Microsoft.Devices).ResourceTypes `
  | Where-Object ResourceTypeName -eq IoTHubs).Locations
```

Hozzon létre egy erőforráscsoportot az IoT hub IoT hub támogatott helyek egyikén, használja a következő parancsot. Ebben a példában egy nevű erőforráscsoportot hoz létre **MyIoTRG1** a a **USA keleti régiója** régió:

```powershell
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az IoT-központ az előző lépésben létrehozott erőforráscsoport létrehozásához használja a következő parancsot. Ez a példa létrehoz egy **S1** nevű hub **MyTestIoTHub** a a **USA keleti régiója** régió:

```powershell
New-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub `
    -SkuName S1 -Units 1 `
    -Location "East US"
```

Az IoT hub nevét egyedinek kell lennie.

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Az IoT-központok listázhatja az előfizetéshez a következő parancsot:

```powershell
Get-AzureRmIotHub
```

Az előző példában hozzáad egy S1 Standard IoT-központot, amelynek kell fizetni. Az IoT hub, az alábbi parancs segítségével törölheti:

```powershell
Remove-AzureRmIotHub `
    -ResourceGroupName MyIoTRG1 `
    -Name MyTestIoTHub
```

Azt is megteheti távolíthatja el az erőforráscsoportot és az összes erőforrást tartalmaz a következő parancsot:

```powershell
Remove-AzureRmResourceGroup -Name MyIoTRG1
```

## <a name="next-steps"></a>Következő lépések

Most egy IoT-központot egy PowerShell-parancsmag használatával telepített, akkor érdemes lehet további felfedezése:

* Fedezze fel más [PowerShell-parancsmagok használata az IoT hub][lnk-iothub-cmdlets].
* Olvassa el a képességeit a [IoT-központ erőforrás-szolgáltató REST API][lnk-rest-api].

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [C SDK bemutatása][lnk-c-sdk]
* [Az Azure IoT SDK-k][lnk-sdks]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Egy eszköz szimulálva IoT oldala][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-powershell-install]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[lnk-iothub-cmdlets]: https://docs.microsoft.com/powershell/module/azurerm.iothub/
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: iot-hub-linux-iot-edge-simulated-device.md
