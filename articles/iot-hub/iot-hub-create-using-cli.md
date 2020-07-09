---
title: IoT Hub létrehozása az Azure CLI használatával | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre egy erőforráscsoportot az Azure CLI-parancsokkal, majd hogyan hozhat létre IoT hubot az erőforráscsoporthoz. Azt is megtudhatja, hogyan távolíthatja el a hubot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708076"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>IoT hub létrehozása az Azure CLI használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy IoT hubot az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

A útmutató végrehajtásához Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be Azure-fiókját

Ha a Cloud Shell használata helyett helyileg futtatja az Azure CLI-t, be kell jelentkeznie az Azure-fiókjába.

A parancssorban futtassa a [login parancsot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure CLI használatával hozzon létre egy erőforráscsoportot, majd vegyen fel egy IoT hubot.

1. IoT hub létrehozásakor létre kell hoznia egy erőforráscsoportot. Használhat meglévő erőforráscsoportot, vagy futtathatja a következő [parancsot erőforráscsoport létrehozásához](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az elérhető helyszínek listáját a következő parancs futtatásával tekintheti meg: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. A következő parancs futtatásával [hozzon létre egy IoT hubot](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) az erőforráscsoporthoz az IoT hub globálisan egyedi nevének használatával:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Az előző parancs egy IoT hubot hoz létre az S1 díjszabási szinten, amelynek számlázása folyamatban van. További információ: [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>IoT Hub eltávolítása

Az Azure CLI-vel [törölhet egy adott erőforrást](https://docs.microsoft.com/cli/azure/resource), például egy IoT hubot, vagy törölhet egy erőforráscsoportot és annak összes erőforrását, beleértve az IoT-hubokat is.

Az [IoT hub törléséhez](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Egy erőforráscsoport és az összes erőforrás [törléséhez](https://docs.microsoft.com/cli/azure/group#az-group-delete) futtassa a következő parancsot:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az IoT hub használatáról, tekintse meg a következő cikkeket:

* [IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [A Azure Portal használata a IoT Hub kezeléséhez](iot-hub-create-through-portal.md)
