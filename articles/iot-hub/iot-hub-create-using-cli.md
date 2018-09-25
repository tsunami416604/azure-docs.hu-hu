---
title: Hozzon létre egy IoT hubra az Azure CLI-vel |} A Microsoft Docs
description: Tudnivalók az Azure CLI-vel az Azure IoT hub létrehozása.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 90830c4e27e90af6c9d77509844696f64e4909f0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994768"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Hozzon létre egy IoT hubra az Azure CLI használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Ez a cikk bemutatja, hogyan hozhat létre egy IoT hubra az Azure CLI használatával.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához Azure-előfizetés szükséges. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókkal

Ha futtatja az Azure CLI helyileg a Cloud Shell használata helyett, jelentkezzen be az Azure-fiókjával szeretné.

A parancssorban futtassa a [login paranccsal](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli):

    ```azurecli
    az login
    ```

Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure CLI használatával hozzon létre egy erőforráscsoportot, és vegye fel az IoT hubra.

1. Amikor létrehoz egy IoT hubot, egy erőforráscsoportban kell létrehoznia. Használjon egy meglévő erőforráscsoportot, vagy futtassa a következő [parancs használatával hozzon létre egy erőforráscsoportot](https://docs.microsoft.com/cli/azure/resource):
    
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

2. Futtassa a következő [paranccsal hozzon létre egy IoT hubot](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create) az erőforráscsoportban, globálisan egyedi név az IoT hub használatával:
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Az előző parancs létrehoz egy IoT hubot az S1 tarifacsomagot, amelynek számítunk fel díjat. További információkért lásd: [Azure IoT Hub díjszabás](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Távolítsa el az IoT Hub

Használhatja az Azure CLI-vel [törölhet egyedi erőforrásokat](https://docs.microsoft.com/cli/azure/resource), például egy IoT hubot, vagy a delete egy erőforráscsoportot és az ahhoz tartozó összes erőforrást, beleértve a bármely IoT-központok.

A [egy IoT hubot törli](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete), futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

A [egy erőforráscsoport törlése](https://docs.microsoft.com/cli/azure/group#az-group-delete) és az ahhoz tartozó összes erőforrást, futtassa a következő parancsot:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>További lépések

Az IoT hub használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [Az IoT Hub fejlesztői útmutató](iot-hub-devguide.md)
* [Az IoT Hub kezelése az Azure portal használatával](iot-hub-create-through-portal.md)
