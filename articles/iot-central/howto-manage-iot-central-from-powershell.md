---
title: IoT-központ kezelése az Azure PowerShell-lel |} A Microsoft Docs
description: IoT-központ kezelése az Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 01/14/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 04726249809656344c8f81164d5deed5af321e71
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54355616"
---
# <a name="manage-iot-central-from-azure-powershell"></a>IoT-központ kezelése az Azure PowerShell-lel

Létrehozásához és IoT-központ alkalmazások kezelése – az IoT-központ [alkalmazáskezelő](https://aka.ms/iotcentral) lapon használhatja [Azure PowerShell-lel](https://docs.microsoft.com/powershell/azure/overview) az alkalmazások kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha inkább a helyi gépen az Azure PowerShell futtatásához, tekintse meg [Azure PowerShell-modul telepítéséhez](https://docs.microsoft.com/powershell/azure/install-az-ps). Azure PowerShell helyi futtatásakor használja a **Connect-AzAccount** parancsmag való bejelentkezéshez az Azure-ba, mielőtt megpróbálná a parancsmagok ebben a cikkben.

## <a name="install-the-iot-central-module"></a>Az IoT-központ modul telepítése

A következő parancs futtatásával ellenőrizze a [IoT-központ modul](https://docs.microsoft.com/powershell/module/az.iotcentral/) telepítve van a PowerShell környezetben:

```PowerShell
Get-InstalledModule -name Az.I*
```

Ha nem adja meg a telepített modulok listájának **Az.IotCentral**, futtassa a következő parancsot:

```PowerShell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Alkalmazás létrehozása

Használja a [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) parancsmag IoT központi alkalmazás létrehozása az Azure-előfizetésében. Példa:

```PowerShell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```PowerShell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

A szkript először létrehoz egy erőforráscsoportot a keleti régiójában, az alkalmazás. A következő táblázat ismerteti a használt paraméterek a **New-AzIotCentralApp** parancsot:

|Paraméter         |Leírás |
|------------------|------------|
|ResourceGroupName |Az erőforráscsoport, amely tartalmazza az alkalmazást. Ennek az erőforráscsoportnak már léteznie kell az előfizetésben. |
|Hely |Alapértelmezés szerint ez a parancsmag használja az erőforráscsoport helyét. Jelenleg az IoT Central alkalmazáshoz hozhat létre a **USA keleti RÉGIÓJA**, **USA nyugati RÉGIÓJA**, **Észak-Európa**, vagy **Nyugat-Európa** régióban. |
|Name (Név)              |Az Azure Portalon az alkalmazás neve. |
|Altartomány         |Az altartomány az alkalmazás URL-címét. A példában az alkalmazás URL-cím van https://mysubdomain.azureiotcentral.com. |
|SKU               |Jelenleg az egyetlen érték **S1** (standard szintű). Lásd: [Azure IoT Central díjszabás](https://azure.microsoft.com/pricing/details/iot-central/). |
|Sablon          | Az alkalmazás sablont szeretné használni. További információkért lásd az alábbi táblázatot: |
|Megjelenítendő név       |Az alkalmazás a felhasználói felületen megjelenített neve. |

**Az alkalmazás**

|Sablon neve  |Leírás |
|---------------|------------|
|iotc-default@1.0.0 |Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |
|iotc-demo@1.0.0    |Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
|iotc-devkit-sample@1.0.0 |Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Sablon használata, ha ezek az eszközök bármelyikével kísérletezés eszköz fejlesztők. |

## <a name="view-your-iot-central-applications"></a>Az IoT-központ alkalmazások megtekintése

Használja a [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) parancsmag az IoT-központ alkalmazások listázása és megtekintése a metaadatokat.

## <a name="modify-an-application"></a>Módosítsa az alkalmazást

Használja a [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) parancsmagot, hogy frissíteni azokat a metaadatokat az IoT Central alkalmazáshoz. Például módosíthatja az alkalmazás megjelenített neve:

```PowerShell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Használja a [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) parancsmag egy IoT-központ alkalmazás törléséhez. Példa:

```PowerShell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central alkalmazásait az Azure PowerShell, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)
