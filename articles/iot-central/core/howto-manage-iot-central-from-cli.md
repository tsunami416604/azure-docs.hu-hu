---
title: IoT Central kezelése az Azure CLI-vel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és kezelhet IoT Central alkalmazást a parancssori felület használatával. Az alkalmazást a parancssori felület használatával tekintheti meg, módosíthatja és távolíthatja el.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 8526eb50faf300892c66ac186eac25adecf62231
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77019026"
---
# <a name="manage-iot-central-from-azure-cli"></a>IoT Central kezelése az Azure CLI-vel

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

IoT Central alkalmazások az [azure IoT Central Application Manager](https://aka.ms/iotcentral) webhelyén való létrehozása és kezelése helyett az [Azure CLI](/cli/azure/) használatával kezelheti az alkalmazásokat.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

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
  --sku ST1 --template "iotc-demo@1.0.0" \
  --display-name "My Custom Display Name"
```

Ezek a parancsok először létrehoznak egy erőforráscsoportot az alkalmazáshoz az USA keleti régiójában. Az alábbi táblázat az az **iotcentral app Create** paranccsal használt paramétereket ismerteti:

| Paraméter         | Leírás |
| ----------------- | ----------- |
| resource-group    | Az alkalmazást tartalmazó erőforráscsoport. Ez az erőforráscsoport már léteznie kell az előfizetésben. |
| location          | Alapértelmezés szerint ez a parancs az erőforráscsoport helyét használja. Jelenleg létrehozhat egy IoT Central alkalmazást az **USA keleti**régiójában, az **USA nyugati**régiójában **, Észak-Európában**vagy Nyugat- **Európai** régióban, illetve **Ausztráliában** vagy **Ázsia és a csendes-óceáni térség** földrajzi régióban. |
| név              | Az alkalmazás neve a Azure Portalban. |
| altartomány         | Az alkalmazás URL-címében szereplő altartomány. A példában az alkalmazás URL-címe https://mysubdomain.azureiotcentral.com. |
| SKU               | Jelenleg használhatja a **ST1** vagy a **ST2**. Lásd: az [Azure IoT Central díjszabása](https://azure.microsoft.com/pricing/details/iot-central/). |
| sablon          | A használni kívánt alkalmazás sablonja. További információkért tekintse meg a következő táblázatot: |
| megjelenítendő név      | Az alkalmazás neve, ahogy az a felhasználói felületen látható. |

**Alkalmazás-sablonok**

| Sablon neve            | Leírás |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | Létrehoz egy üres alkalmazást, amelybe a saját eszközsablonjait és eszközeit helyezheti el.
| iotc-pnp-preview@1.0.0   | Létrehoz egy üres Plug and Play (előzetes verzió) alkalmazást, amely a saját eszközök sablonjaival és eszközeivel tölthető fel. |
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
