---
title: Az IoT Central kezelése az Azure PowerShellből | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan hozhat létre és kezelhet ioT Central-alkalmazásokat az Azure PowerShellből.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 42d853c9cf53c1c6921fbd1816ec2298c9c3583e
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365543"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Az IoT Central kezelése az Azure PowerShellből

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Az IoT Central-alkalmazások létrehozása és kezelése helyett az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) segítségével kezelheti alkalmazásait.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure PowerShellt a helyi gépen szeretné futtatni, [olvassa el az Azure PowerShell-modul telepítése című témakört.](https://docs.microsoft.com/powershell/azure/install-az-ps) Ha helyileg futtatja az Azure PowerShellt, a **Connect-AzAccount** parancsmag segítségével jelentkezzen be az Azure-ba, mielőtt megpróbálná a cikkben szereplő parancsmagokat.

> [!TIP]
> Ha egy másik Azure-előfizetésben kell futtatnia a PowerShell-parancsokat, olvassa el [az Aktív előfizetés módosítása](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription)című témakört.

## <a name="install-the-iot-central-module"></a>Az IoT Central modul telepítése

Futtassa a következő parancsot, és ellenőrizze, hogy az [IoT Central modul](https://docs.microsoft.com/powershell/module/az.iotcentral/) telepítve van-e a PowerShell-környezetben:

```powershell
Get-InstalledModule -name Az.I*
```

Ha a telepített modulok listája nem tartalmazza az **Az.IotCentral**szolgáltatást, futtassa a következő parancsot:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Alkalmazás létrehozása

A [New-AzIotCentralApp-parancsmag](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) használatával hozzon létre egy IoT Central-alkalmazást az Azure-előfizetésében. Példa:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview@1.0.0" `
  -DisplayName "My Custom Display Name"
```

A parancsfájl először létrehoz egy erőforráscsoportot az USA keleti régiójában az alkalmazáshoz. Az alábbi táblázat a **New-AzIotCentralApp** paranccsal használt paramétereket ismerteti:

|Paraméter         |Leírás |
|------------------|------------|
|ResourceGroupName |Az alkalmazást tartalmazó erőforráscsoport. Ennek az erőforráscsoportnak már léteznie kell az előfizetésben. |
|Hely |Alapértelmezés szerint ez a parancsmag az erőforráscsoport helyét használja. Jelenleg létrehozhat egy IoT Central alkalmazást **Az Ausztrália,** **Ázsia csendes-óceáni,** **Európa,** **Egyesült Államok**, Egyesült **Királyság**és **Japán** földrajzi. |
|Név              |Az alkalmazás neve az Azure Portalon. |
|Altartomány         |Az alkalmazás URL-címében lévő altartomány. A példában az alkalmazás `https://mysubdomain.azureiotcentral.com`URL-címe . |
|SKU               |Jelenleg **st1** vagy **ST2**is használható. Lásd: [Azure IoT Central díjszabás.](https://azure.microsoft.com/pricing/details/iot-central/) |
|Sablon          | A használandó alkalmazássablon. További információt az alábbi táblázat tartalmaz. |
|DisplayName       |Az alkalmazás neve a felhasználói felületen látható módon. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>IoT Central-alkalmazások megtekintése

A [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) parancsmag segítségével sorolja fel az IoT Central-alkalmazásokat, és tekintse meg a metaadatokat.

## <a name="modify-an-application"></a>Alkalmazás módosítása

A [Set-AzIotCentralApp-parancsmag](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) segítségével frissítse az IoT Central alkalmazás metaadatait. Például az alkalmazás megjelenítendő nevének módosításához:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Az [Eltávolítás-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) parancsmag használatával törölheti az IoT Central alkalmazást. Példa:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kezelheti az Azure IoT Central-alkalmazásokat az Azure PowerShellből, az alábbiakban a következő javasolt lépést ismertetem:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)
