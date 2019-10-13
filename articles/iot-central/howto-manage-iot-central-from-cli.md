---
title: IoT Central kezelése az Azure CLI-vel | Microsoft Docs
description: IoT Central kezelése az Azure CLI-vel.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 42a2b2d7962f3efd71639f65e9e6f6f5d6783afb
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286423"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central kezelése az Azure CLI-vel

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett az [Azure CLI](/cli/azure/) használatával kezelheti az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a helyi gépen szeretné futtatni az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. Amikor helyileg futtatja az Azure CLI-t, az az **login** paranccsal jelentkezzen be az Azure-ba, mielőtt kipróbálja a jelen cikkben szereplő parancsokat.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Az az [iotcentral app Create](/cli/azure/iotcentral/app#az-iotcentral-app-create) paranccsal hozzon létre egy IoT Central alkalmazást az Azure-előfizetésében. Példa:

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
  --sku S1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Ezek a parancsok először létrehoznak egy erőforráscsoportot az alkalmazáshoz az USA keleti régiójában. Az alábbi táblázat az az **iotcentral app Create** paranccsal használt paramétereket ismerteti:

| Paraméter         | Leírás |
| ----------------- | ----------- |
| resource-group    | Az alkalmazást tartalmazó erőforráscsoport. Ez az erőforráscsoport már léteznie kell az előfizetésben. |
| location          | Alapértelmezés szerint ez a parancs az erőforráscsoport helyét használja. Jelenleg létrehozhat egy IoT Central alkalmazást az **USA keleti**régiójában, az **USA nyugati**régiójában **, Észak-Európában**vagy Nyugat- **Európai** régióban, illetve **Ausztráliában** vagy **Ázsia és a csendes-óceáni térség** földrajzi régióban. |
| név              | Az alkalmazás neve a Azure Portalban. |
| Altartomány         | Az alkalmazás URL-címében szereplő altartomány. A példában az alkalmazás URL-címe https://mysubdomain.azureiotcentral.com. |
| SKU               | Jelenleg az egyetlen érték az **S1** (standard szint). Lásd: az [Azure IoT Central díjszabása](https://azure.microsoft.com/pricing/details/iot-central/). |
| sablon          | A használni kívánt alkalmazás sablonja. További információkért tekintse meg a következő táblázatot: |
| megjelenítendő név      | Az alkalmazás neve, ahogy az a felhasználói felületen látható. |

**Alkalmazás-sablonok**

| Sablon neve            | Leírás |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |
| iotc-demo@1.0.0          | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
| iotc-devkit-sample@1.0.0 | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Akkor használja ezt a sablont, ha egy eszköz fejlesztője ezen eszközök bármelyikével kísérletezik. |

> [!NOTE]
> Az **előzetes verziójú alkalmazás** sablonja jelenleg csak az **észak-európai** és az **USA középső** régiójában érhető el.

## <a name="view-your-applications"></a>Saját alkalmazások megtekintése

Az az [iotcentral app List](/cli/azure/iotcentral/app#az-iotcentral-app-list) paranccsal listázhatja IoT Central alkalmazásait, és megtekintheti a metaadatokat.

## <a name="modify-an-application"></a>Alkalmazás módosítása

Az az [iotcentral app Update](/cli/azure/iotcentral/app#az-iotcentral-app-update) paranccsal frissítheti egy IoT Central alkalmazás metaadatait. Például az alkalmazás megjelenítendő nevének módosításához:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

IoT Central alkalmazás törléséhez használja az az [iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) parancsot. Példa:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan kezelheti az Azure IoT Central-alkalmazásait az Azure CLI-vel, itt látható a következő lépés:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)
