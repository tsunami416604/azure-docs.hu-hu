---
title: IoT Central kezelése az Azure CLI-vel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet IoT Central alkalmazást a parancssori felület használatával. Az alkalmazást a parancssori felület használatával tekintheti meg, módosíthatja és távolíthatja el.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: bd87f15ff63edf1da447faf986cad2f9591610dd
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87502970"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central kezelése az Azure CLI-vel

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett az [Azure CLI](/cli/azure/) használatával kezelheti az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha a helyi gépen szeretné futtatni az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. Amikor helyileg futtatja az Azure CLI-t, az az **login** paranccsal jelentkezzen be az Azure-ba, mielőtt kipróbálja a jelen cikkben szereplő parancsokat.

> [!TIP]
> Ha egy másik Azure-előfizetésben kell futtatnia a CLI-parancsokat, tekintse meg [az aktív előfizetés módosítása](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#change-the-active-subscription)című témakört.

## <a name="install-the-extension"></a>A bővítmény telepítése

A cikkben szereplő parancsok az **Azure-IOT** CLI bővítmény részét képezik. A bővítmény telepítéséhez futtassa a következő parancsot:

```azurecli-interactive
az extension add --name azure-iot
```

## <a name="create-an-application"></a>Alkalmazás létrehozása

Az az [IOT Central app Create](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-create) paranccsal hozzon létre egy IoT Central alkalmazást az Azure-előfizetésében. Például:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

Ezek a parancsok először létrehoznak egy erőforráscsoportot az alkalmazáshoz az USA keleti régiójában. Az alábbi táblázat az az **IOT Central app Create** paranccsal használt paramétereket ismerteti:

| Paraméter         | Leírás |
| ----------------- | ----------- |
| resource-group    | Az alkalmazást tartalmazó erőforráscsoport. Ez az erőforráscsoport már léteznie kell az előfizetésben. |
| location          | Alapértelmezés szerint ez a parancs az erőforráscsoport helyét használja. Jelenleg IoT Central alkalmazást hozhat létre **Ausztráliában**, **Ázsia és a csendes-óceáni térségban**, **Európában**, **Egyesült Államokban**, az **Egyesült Királyságban**és a **japán** földrajzi területeken. |
| name              | Az alkalmazás neve a Azure Portalban. |
| altartomány         | Az alkalmazás URL-címében szereplő altartomány. A példában az alkalmazás URL-címe: `https://mysubdomain.azureiotcentral.com` . |
| SKU               | Jelenleg használhatja a **ST1** vagy a **ST2**. Lásd: az [Azure IoT Central díjszabása](https://azure.microsoft.com/pricing/details/iot-central/). |
| sablon          | A használni kívánt alkalmazás sablonja. További információt az alábbi táblázat tartalmaz. |
| megjelenítendő név      | Az alkalmazás neve, ahogy az a felhasználói felületen látható. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Saját alkalmazások megtekintése

Az az [IOT Central app List](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-list) paranccsal listázhatja IoT Central alkalmazásait, és megtekintheti a metaadatokat.

## <a name="modify-an-application"></a>Alkalmazás módosítása

Az az [IOT Central app Update](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-update) paranccsal frissítheti egy IoT Central alkalmazás metaadatait. Például az alkalmazás megjelenítendő nevének módosításához:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

IoT Central alkalmazás törléséhez használja az az [IOT Central app delete](/cli/azure/iot/central/app?view=azure-cli-latest#az-iot-central-app-delete) parancsot. Például:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásait az Azure CLI-vel, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Alkalmazás felügyelése](howto-administer.md)
