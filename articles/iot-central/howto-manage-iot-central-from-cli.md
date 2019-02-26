---
title: IoT-központ kezelése az Azure CLI |} A Microsoft Docs
description: IoT-központ kezelése az Azure parancssori felület.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/07/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 9e5d842cece316bc9c53e1e8583f40a0f222b91d
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808626"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT-központ kezelése az Azure CLI

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

Létrehozásához és IoT-központ alkalmazások kezelése – az IoT-központ [alkalmazáskezelő](https://aka.ms/iotcentral) lapon használhatja [Azure CLI-vel](/cli/azure/) az alkalmazások kezeléséhez.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha inkább a futtatása az Azure CLI-vel a helyi gépen, [az Azure CLI telepítése](/cli/azure/install-azure-cli). Azure CLI helyi futtatásakor használja a **az bejelentkezési** való bejelentkezéshez az Azure-ba, mielőtt megpróbálná ebben a cikkben szereplő parancsok parancsot.

## <a name="create-an-application"></a>Alkalmazás létrehozása

Használja a [az iotcentral app létrehozása](/cli/azure/iotcentral/app#az-iotcentral-app-create) parancs IoT központi alkalmazás létrehozása az Azure-előfizetésében. Példa:

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

Ezek a parancsok a keleti régiójában, az alkalmazás a először hozzon létre egy erőforráscsoportot. A következő táblázat ismerteti a használt paraméterek a **az iotcentral app létrehozása** parancsot:

| Paraméter         | Leírás |
| ----------------- | ----------- |
| resource-group    | Az erőforráscsoport, amely tartalmazza az alkalmazást. Ennek az erőforráscsoportnak már léteznie kell az előfizetésben. |
| location          | Ez a parancs alapértelmezés szerint használja az erőforráscsoport helyét. Jelenleg az IoT Central alkalmazáshoz hozhat létre a **USA keleti RÉGIÓJA**, **USA nyugati RÉGIÓJA**, **Észak-Európa**, vagy **Nyugat-Európa** régióban. |
| név              | Az Azure Portalon az alkalmazás neve. |
| subdomain         | Az altartomány az alkalmazás URL-címét. A példában az alkalmazás URL-cím van https://mysubdomain.azureiotcentral.com. |
| termékváltozat               | Jelenleg az egyetlen érték **S1** (standard szintű). Lásd: [Azure IoT Central díjszabás](https://azure.microsoft.com/pricing/details/iot-central/). |
| sablon          | Az alkalmazás sablont szeretné használni. További információkért lásd az alábbi táblázatot: |
| megjelenítendő neve      | Az alkalmazás a felhasználói felületen megjelenített neve. |

**Az alkalmazás**

| Sablon neve            | Leírás |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el. |
| iotc-demo@1.0.0          | Létrehoz egy alkalmazást, amely már tartalmaz egy eszközsablont egy hűtött eladóautomatához. Használja ezt a sablont az Azure IoT Central megismerésének megkezdéséhez. |
| iotc-devkit-sample@1.0.0 | Létrehoz egy eszközsablonokat tartalmazó alkalmazást, amelyekkel csatlakoztathat egy MXChip vagy Raspberry Pi eszközt. Sablon használata, ha ezek az eszközök bármelyikével kísérletezés eszköz fejlesztők. |

## <a name="view-your-applications"></a>Saját alkalmazások megtekintése

Használja a [az iotcentral Alkalmazáslista](/cli/azure/iotcentral/app#az-iotcentral-app-list) parancsot az IoT-központ alkalmazások listázása és megtekintése a metaadatokat.

## <a name="modify-an-application"></a>Módosítsa az alkalmazást

Használja a [az iotcentral alkalmazásfrissítési](/cli/azure/iotcentral/app#az-iotcentral-app-update) paranccsal frissíteni azokat a metaadatokat az IoT Central alkalmazáshoz. Például módosíthatja az alkalmazás megjelenített neve:

```azurecli-interactive
az iotcentral app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Alkalmazás eltávolítása

Használja a [az iotcentral app delete](/cli/azure/iotcentral/app#az-iotcentral-app-delete) parancsot egy IoT-központ alkalmazás törléséhez. Példa:

```azurecli-interactive
az iotcentral app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte az Azure IoT Central alkalmazások felügyelete az Azure parancssori felületen, Íme a javasolt következő lépésre:

> [!div class="nextstepaction"]
> [Az alkalmazás felügyelete](howto-administer.md)
