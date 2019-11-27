---
title: IoT Central kezelése Azure PowerShellból | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan hozhat létre és kezelhet IoT Central-alkalmazásokat Azure PowerShellból.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 506eb38a2844ed8e8eb9739b116d7647bc1810ec
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480285"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Az IoT Central kezelése az Azure PowerShellből

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [Azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett az [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) használatával kezelheti az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a Azure PowerShell a helyi gépen szeretné futtatni, tekintse meg [a Azure PowerShell modul telepítése](https://docs.microsoft.com/powershell/azure/install-az-ps)című témakört. Ha Azure PowerShell helyileg futtatja, a következő parancsmagok kipróbálása előtt jelentkezzen be az Azure-ba a **Csatlakozás-AzAccount** parancsmag használatával.

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
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

A szkript először létrehoz egy erőforráscsoportot az alkalmazás keleti régiójában. A **New-AzIotCentralApp** paranccsal használt paramétereket a következő táblázat ismerteti:

|Paraméter         |Leírás |
|------------------|------------|
|ResourceGroupName |Az alkalmazást tartalmazó erőforráscsoport. Ez az erőforráscsoport már léteznie kell az előfizetésben. |
|Hely |Alapértelmezés szerint ez a parancsmag az erőforráscsoport helyét használja. Jelenleg IoT Central alkalmazást hozhat létre a **Egyesült Államok**, **Ausztráliában**, **Ázsia és a csendes-óceáni térség**vagy az **Európa** helyein.  |
|Name (Név)              |Az alkalmazás neve a Azure Portalban. |
|Subdomain         |Az alkalmazás URL-címében szereplő altartomány. A példában az alkalmazás URL-címe https://mysubdomain.azureiotcentral.com. |
|SKU               |Jelenleg az egyetlen érték az **S1** (standard szint). Lásd: az [Azure IoT Central díjszabása](https://azure.microsoft.com/pricing/details/iot-central/). |
|Sablon          | A használni kívánt alkalmazás sablonja. További információkért tekintse meg a következő táblázatot: |
|DisplayName       |Az alkalmazás neve, ahogy az a felhasználói felületen látható. |

**Általánosan elérhető funkciókkal rendelkező alkalmazás-sablonok**

| Sablon neve            | Leírás |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |
| iotc-demo@1.0.0          | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
| iotc-devkit-sample@1.0.0 | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Akkor használja ezt a sablont, ha egy eszköz fejlesztője ezen eszközök bármelyikével kísérletezik. |


**Nyilvános előzetes funkciókkal rendelkező alkalmazás-sablonok**

| Sablon neve            | Leírás |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | Létrehoz egy üres Plug and Play előnézeti alkalmazást, amellyel feltöltheti a saját eszközök sablonjait és eszközeit. |
| iotc-condition@1.0.0     | Létrehoz egy alkalmazást egy áruházbeli elemzés – feltétel-figyelési sablonnal. Ezzel a sablonnal csatlakozhat és figyelheti a tárolási környezetet. |
| iotc-consumption@1.0.0   | Létrehoz egy alkalmazást a víz-felhasználási megfigyelési sablonnal. Ezzel a sablonnal figyelheti és szabályozhatja a víz áramlását. |
| iotc-distribution@1.0.0  | Létrehoz egy alkalmazást egy digitális terjesztési sablonnal. Ezzel a sablonnal a fő eszközök és műveletek digitalizing javíthatja a raktár kimeneti hatékonyságát. |
| iotc-inventory@1.0.0     | Egy intelligens leltár-felügyeleti sablonnal rendelkező alkalmazást hoz létre. Ezzel a sablonnal automatizálhatja a fogadást, a termékek mozgatását, a ciklusok leltározását és az érzékelők nyomon követését. |
| iotc-logistics@1.0.0     | Létrehoz egy alkalmazást egy csatlakoztatott logisztikai sablonnal. Ezzel a sablonnal valós időben nyomon követheti a szállítást a levegő, a víz és a föld között, ahol a hely és a feltétel figyelése történik. |
| iotc-meter@1.0.0         | Létrehoz egy alkalmazást az intelligens mérőműszer-figyelési sablonnal. Ezzel a sablonnal figyelheti az energiafogyasztást, a hálózati állapotot, és azonosíthatja a trendeket az ügyfélszolgálat és az Intelligens Fogyasztásmérők felügyeletének javítása érdekében.  |
| iotc-patient@1.0.0       | Létrehoz egy alkalmazást folyamatos beteg-figyelési sablonnal. Ezzel a sablonnal kiterjesztheti a betegellátás, a visszafogadás és a betegségek kezelését. |
| iotc-power@1.0.0         | Létrehoz egy alkalmazást a napelemes figyelési sablonnal. Ezzel a sablonnal figyelhetők a napelemek állapota, az energiatermelés trendjei. |
| iotc-quality@1.0.0       | Létrehoz egy alkalmazást a vízminőség-figyelési sablonnal. Ez a sablon a víz minőségének digitális figyelésére használható.|
| iotc-store@1.0.0         | Létrehoz egy alkalmazást egy áruházbeli elemzés – pénztár sablonnal. Ezzel a sablonnal figyelheti és kezelheti a pénztári folyamatot a tárolón belül. |
| iotc-waste@1.0.0         | Egy csatlakoztatott hulladékkezelési sablonnal rendelkező alkalmazást hoz létre. Ezzel a sablonnal figyelhetők a hulladéktároló tárolók és a küldő mezők operátorai. |

> [!NOTE]
> Az előzetes verziójú alkalmazás sablonjai jelenleg csak az **Európa** és **Egyesült Államok** helyen érhetők el.

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

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásokat a Azure PowerShellból, a következő lépés a javasolt lépés:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)
