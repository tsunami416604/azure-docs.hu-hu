---
title: IoT-központ létrehozása az Azure CLI használatával | Microsoft dokumentumok
description: Megtudhatja, hogyan használhatja az Azure CLI-parancsokat egy erőforráscsoport létrehozásához, majd hozzon létre egy IoT-központot az erőforráscsoportban. Azt is megtudhatja, hogyan távolíthatja el a hubot.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e7253747d95917e34725cc7c6c034b1306f65e50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284719"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>IoT-központ létrehozása az Azure CLI használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy IoT-központot az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez Azure-előfizetésre van szüksége. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be és állítsa be Azure-fiókját

Ha az Azure CLI-t helyileg futtatja a Cloud Shell használata helyett, be kell jelentkeznie az Azure-fiókjába.

A parancssorban futtassa a [login parancsot](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

   ```azurecli
   az login
   ```

Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure CLI használatával hozzon létre egy erőforráscsoportot, majd adjon hozzá egy IoT-központot.

1. Amikor létrehoz egy IoT hubot, létre kell hoznia azt egy erőforráscsoportban. Használhat meglévő erőforráscsoportot, vagy futtathatja a következő [parancsot erőforráscsoport létrehozásához](https://docs.microsoft.com/cli/azure/resource):
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az elérhető helyek listáját a következő parancs futtatásával tekintheti meg: 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. Futtassa a következő [parancsot egy IoT hub létrehozásához](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) az erőforráscsoportban, az IoT hub globálisan egyedi neve használatával:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Az előző parancs létrehoz egy IoT hubot az S1 tarifacsomagban, amelyért díjat kell fizetnie. További információ: [Azure IoT Hub díjszabása.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="remove-an-iot-hub"></a>IoT-központ eltávolítása

Az Azure CLI használatával [törölheti az egyes erőforrásokat,](https://docs.microsoft.com/cli/azure/resource)például egy IoT-központot, vagy törölheti az erőforráscsoportot és annak összes erőforrását, beleértve az IoT-központokat is.

[IoT-központ törléséhez](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Erőforráscsoport és annak összes erőforrásának [törléséhez futtassa](https://docs.microsoft.com/cli/azure/group#az-group-delete) a következő parancsot:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>További lépések

Az IoT-központ használatáról az alábbi cikkekben olvashat bővebben:

* [Az IoT Hub fejlesztői útmutatója](iot-hub-devguide.md)
* [Az Azure Portal használata az IoT Hub kezeléséhez](iot-hub-create-through-portal.md)
