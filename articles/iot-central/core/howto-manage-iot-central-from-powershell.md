---
title: IoT Central kezelése Azure PowerShellból | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet IoT Central-alkalmazásokat Azure PowerShellból.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 9ca1c8f4a8e92babf65ec049b3784882bd3af689
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82744980"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Az IoT Central kezelése az Azure PowerShellből

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) használatával kezelheti az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a Azure PowerShell a helyi gépen szeretné futtatni, tekintse meg [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps)című témakört. Ha Azure PowerShell helyileg futtatja, a következő parancsmagok kipróbálása előtt jelentkezzen be az Azure-ba a **Csatlakozás-AzAccount** parancsmag használatával.

> [!TIP]
> Ha egy másik Azure-előfizetésben kell futtatnia a PowerShell-parancsokat, tekintse meg [az aktív előfizetés módosítása](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription)című témakört.

## <a name="install-the-iot-central-module"></a>A IoT Central modul telepítése

A következő parancs futtatásával győződjön meg arról, hogy a [IoT Central modul](https://docs.microsoft.com/powershell/module/az.iotcentral/) telepítve van a PowerShell-környezetben:

```powershell
Get-InstalledModule -name Az.I*
```

Ha a telepített modulok listája nem tartalmazza az **az. IotCentral**, futtassa a következő parancsot:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Alkalmazás létrehozása

A [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) parancsmag használatával hozzon létre egy IoT Central alkalmazást az Azure-előfizetésében. Például:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

A szkript először egy erőforráscsoportot hoz létre az USA keleti régiójában az alkalmazáshoz. A **New-AzIotCentralApp** paranccsal használt paramétereket a következő táblázat ismerteti:

|Paraméter         |Leírás |
|------------------|------------|
|ResourceGroupName |Az alkalmazást tartalmazó erőforráscsoport. Ez az erőforráscsoport már léteznie kell az előfizetésben. |
|Hely |Alapértelmezés szerint ez a parancsmag az erőforráscsoport helyét használja. Jelenleg IoT Central alkalmazást hozhat létre **Ausztráliában**, **Ázsia és a csendes-óceáni térségban**, **Európában**, **Egyesült Államokban**, az **Egyesült Királyságban**és a **japán** földrajzi területeken. |
|Name              |Az alkalmazás neve a Azure Portalban. |
|Altartomány         |Az alkalmazás URL-címében szereplő altartomány. A példában az alkalmazás URL-címe: `https://mysubdomain.azureiotcentral.com` . |
|SKU               |Jelenleg használhatja a **ST1** vagy a **ST2**. Lásd: az [Azure IoT Central díjszabása](https://azure.microsoft.com/pricing/details/iot-central/). |
|Sablon          | A használni kívánt alkalmazás sablonja. További információt az alábbi táblázat tartalmaz. |
|DisplayName       |Az alkalmazás neve, ahogy az a felhasználói felületen látható. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>IoT Central-alkalmazások megtekintése

A [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) parancsmag használatával listázhatja IoT Central alkalmazásait, és megtekintheti a metaadatokat.

## <a name="modify-an-application"></a>Alkalmazás módosítása

A [set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) parancsmag segítségével frissítheti egy IoT Central alkalmazás metaadatait. Például az alkalmazás megjelenítendő nevének módosításához:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

IoT Central alkalmazás törléséhez használja a [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) parancsmagot. Például:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásokat a Azure PowerShellból, a következő lépés a javasolt lépés:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)
