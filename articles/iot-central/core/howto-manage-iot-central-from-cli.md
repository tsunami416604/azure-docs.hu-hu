---
title: Az IoT Central kezelése az Azure CLI-ből | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan hozhat létre és kezelhető az IoT Central alkalmazás CLI használatával. Az alkalmazást megtekintheti, módosíthatja és eltávolíthatja a CLI használatával.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: df24a2dc6e9bd058a2f8b1355b8760653ed3128a
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365522"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central kezelése az Azure CLI-ből

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Az IoT Central-alkalmazások létrehozása és kezelése helyett az [Azure IoT Central alkalmazáskezelő](https://aka.ms/iotcentral) webhelyén az [Azure CLI](/cli/azure/) segítségével kezelheti alkalmazásait.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI-t a helyi gépen szeretné futtatni, [olvassa el az Azure CLI telepítése](/cli/azure/install-azure-cli)című témakört. Ha helyileg futtatja az Azure CLI-t, az **az bejelentkezési** paranccsal jelentkezzen be az Azure-ba, mielőtt kipróbálná a jelen cikkben szereplő parancsokat.

> [!TIP]
> Ha a CLI-parancsokat egy másik Azure-előfizetésben kell futtatnia, olvassa el [az Aktív előfizetés módosítása](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)című témakört.

## <a name="install-the-extension"></a>A bővítmény telepítése

A cikkben szereplő parancsok az **azure-iot** CLI-bővítmény részét képezik. A bővítmény telepítéséhez futtassa a következő parancsot:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Alkalmazás létrehozása

Az [az iotcentral app create](/cli/azure/iotcentral/app#az-iotcentral-app-create) paranccsal hozzon létre egy IoT Central-alkalmazást az Azure-előfizetésben. Példa:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iotcentral app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview@1.0.0" \
  --display-name "My Custom Display Name"
```

Ezek a parancsok először létrehozegy erőforráscsoportot az USA keleti régiójában az alkalmazáshoz. Az alábbi táblázat az **az iotcentral app create** parancshoz használt paramétereket ismerteti:

| Paraméter         | Leírás |
| ----------------- | ----------- |
| resource-group    | Az alkalmazást tartalmazó erőforráscsoport. Ennek az erőforráscsoportnak már léteznie kell az előfizetésben. |
| location          | Alapértelmezés szerint ez a parancs az erőforráscsoport helyét használja. Jelenleg létrehozhat egy IoT Central alkalmazást **Az Ausztrália,** **Ázsia csendes-óceáni,** **Európa,** **Egyesült Államok**, Egyesült **Királyság**és **Japán** földrajzi. |
| név              | Az alkalmazás neve az Azure Portalon. |
| Aldomain         | Az alkalmazás URL-címében lévő altartomány. A példában az alkalmazás `https://mysubdomain.azureiotcentral.com`URL-címe . |
| Sku               | Jelenleg **st1** vagy **ST2**is használható. Lásd: [Azure IoT Central díjszabás.](https://azure.microsoft.com/pricing/details/iot-central/) |
| sablon          | A használandó alkalmazássablon. További információt az alábbi táblázat tartalmaz. |
| megjelenítési név      | Az alkalmazás neve a felhasználói felületen látható módon. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Saját alkalmazások megtekintése

Az [az iotcentral app list parancs](/cli/azure/iotcentral/app#az-iotcentral-app-list) segítségével sorolja fel az IoT Central-alkalmazásokat, és tekintse meg a metaadatokat.

## <a name="modify-an-application"></a>Alkalmazás módosítása

Az [iotcentral app update](/cli/azure/iotcentral/app#az-iotcentral-app-update) paranccsal frissítheti az IoT Central-alkalmazások metaadatait. Például az alkalmazás megjelenítendő nevének módosításához:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Az [iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) parancs segítségével törölje az IoT Central alkalmazást. Példa:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy megtanulta, hogyan kezelheti az Azure IoT Central alkalmazásokat az Azure CLI-ből, az alábbiakban a következő javasolt lépést javasolta:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)
