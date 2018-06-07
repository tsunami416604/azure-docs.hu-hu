---
title: Létrehoz egy IoT-központot Azure CLI-vel (azure.js) |} Microsoft Docs
description: Tudnivalók az Azure IoT-központ a platformok közötti Azure parancssori felület (azure.js) használatával.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: kgremban
ms.openlocfilehash: 5b8a7ded940f59bba63556e844c45bd7cfb8eb63
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34632091"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Létrehoz egy IoT-központot, az Azure parancssori felület használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Bevezetés

Azure parancssori felület (azure.js) létrehozását és kezelését a Azure IoT-központok programozott módon használhatja. Ez a cikk bemutatja, hogyan használható az Azure parancssori felület (azure.js) létrehoz egy IoT-központot.

A következő CLI-verziók egyikével elvégezheti a feladatot:

* Az Azure CLI (azure.js) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellel ebben a cikkben leírtak szerint.
* [Az Azure CLI 2.0 (az.py)](iot-hub-create-using-cli.md) - CLI következő generációs erőforrás felügyeleti telepítési modell.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Az Azure CLI 0.10.4] [ lnk-CLI-install] vagy újabb. Ha már rendelkezik az Azure parancssori felület telepítve, az aktuális verzió parancsot a parancssorba a következő paranccsal ellenőrizheti:

```azurecli
azure --version
```

> [!NOTE]
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Azure Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md). Az Azure CLI Azure Resource Manager módban kell lennie:
>
> ```azurecli
> azure config mode arm
> ```

## <a name="set-your-azure-account-and-subscription"></a>Az Azure-fiók és -előfizetés beállítása

1. A parancssorba a következő parancs beírásával bejelentkezési azonosító:

   ```azurecli
    azure login
   ```

   A javasolt webböngésző és a kód segítségével hitelesíteni.
1. Ha több Azure-előfizetéssel rendelkezik, a csatlakoztatása az Azure ad hozzáférést az összes Azure-előfizetést a hitelesítő adatok társított. Az Azure-előfizetések megtekintése és részre, ahol meghatározhatja azt, amelyiket a alapértelmezés szerint a parancs segítségével:

   ```azurecli
    azure account list
   ```

   Az előfizetési kontextust, amely alatt a parancsok használata a többi futtatni kívánt beállítása:

   ```azurecli
    azure account set <subscription name>
   ```

1. Ha még nem rendelkezik egy erőforráscsoport, létrehozhat egy nevű **exampleResourceGroup**:

   ```azurecli
    azure group create -n exampleResourceGroup -l westus
   ```

> [!TIP]
> A cikk [Azure-erőforrások és csoportok kezelése az Azure parancssori felület használatával] [ lnk-CLI-arm] Azure-erőforrások kezelése az Azure parancssori felület használatával kapcsolatos további információk.

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Kötelező paraméter:

```azurecli
azure iothub create -g <resource-group> -n <name> -l <location> -s <sku-name> -u <units>
```

* **Erőforráscsoport**. Az erőforráscsoport neve. A kis-és nagybetűket alfanumerikus, aláhúzásjelet, és kötőjelet tartalmazhat, 1-64 hossza érvénytelen.
* **Név** Az IoT hub létrehozni neve. A kis-és nagybetűket formátuma alfanumerikus és kötőjelet tartalmazhat, 3 – 50 hossza.
* **Hely**. A hely (azure régió/adatközpont) az IoT-központ telepítéséhez.
* **Termékváltozat**. A termékváltozat, egyik neve: [F1, S1, S2, S3]. Minden egyes sku kapcsolatos részletekért lásd: [Azure IoT Hub árképzési](https://azure.microsoft.com/pricing/details/iot-hub/). Alapszintű rétegek jelenleg csak a portálon keresztül érhető el. 
* **egységek**. A kiépített egységek száma. Egység-korlátok, lásd: [Azure IoT Hub árképzési](https://azure.microsoft.com/pricing/details/iot-hub/).

[!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

Tekintse meg az összes paramétereinek létrehozása, használhatja a help paranccsal parancssorban:

```azurecli
azure iothub create -h
```

Gyors példa: az IoT-központ létrehozásához nevű **exampleIoTHubName** erőforráscsoportban **exampleResourceGroup**, a következő parancsot:

```azurecli
azure iothub create -g exampleResourceGroup -n exampleIoTHubName -l westus -k s1 -u 1
```

> [!NOTE]
> Az Azure parancssori felület létrehozza az S1 Standard IoT-központ, amelynek kell fizetni. Az IoT hub törlése **exampleIoTHubName** használatával a következő parancsot:
>
> ```azurecli
> azure iothub delete -g exampleResourceGroup -n exampleIoTHubName
> ```

## <a name="next-steps"></a>További lépések

Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikket:

* [Az IoT-SDK][lnk-sdks]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Az IoT-központ kezelése az Azure portál használatával][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-CLI-install]:../cli-install-nodejs.md
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-CLI-arm]: ../azure-resource-manager/xplat-cli-azure-resource-manager.md

[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-portal]: iot-hub-create-through-portal.md 
