---
title: Létrehoz egy IoT-központot Azure CLI-vel (az.py) |} Microsoft Docs
description: Tudnivalók az Azure IoT-központ a platformok közötti Azure CLI 2.0 (az.py) használatával.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: ''
ms.service: iot-hub
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.openlocfilehash: c89ecc31754d355d6469d54dcd45e1cc2ef106f0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2018
---
# <a name="create-an-iot-hub-using-the-azure-cli-20"></a>Létrehoz egy IoT-központot, az Azure CLI 2.0 használatával

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## <a name="introduction"></a>Bevezetés

Azure CLI 2.0 (az.py) hozhat létre és kezelhet programozott módon Azure IoT-központok. Ez a cikk bemutatja, hogyan használható az Azure CLI 2.0 (az.py) létrehoz egy IoT-központot.

A következő CLI-verziók egyikével elvégezheti a feladatot:

* [Az Azure CLI (azure.js)](iot-hub-create-using-cli-nodejs.md) – a parancssori felületen a klasszikus és resource management üzembe helyezési modellek.
* Az Azure CLI 2.0 (az.py) - CLI következő generációs erőforrás felügyeleti telepítési modell ebben a cikkben leírtak szerint.

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Aktív Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy [ingyenes fiókot][lnk-free-trial].
* [Azure CLI 2.0][lnk-CLI-install].

## <a name="sign-in-and-set-your-azure-account"></a>Jelentkezzen be, és állítsa be az Azure-fiókjával

Jelentkezzen be Azure-fiókjába, és válassza ki előfizetését.

1. A parancssorban futtassa a [login parancsot][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Kövesse az utasításokat a kóddal történő hitelesítéshez, és jelentkezzen be az Azure-fiókjába webböngészőből.

2. Ha több Azure-előfizetéssel rendelkezik, az Azure-ba történő bejelentkezéssel hozzáfér a hitelesítő adatokhoz tartozó összes Azure-fiókhoz. Az alábbi [paranccsal jelenítheti meg az elérhető Azure-fiókokat][lnk-az-account-command]:
    
    ```azurecli
    az account list 
    ```

    Az alábbi parancs segítségével válassza ki azt az előfizetést, amelyet az IoT Hub létrehozásához szükséges parancsok futtatásához kíván használni. Használhatja az előző parancs kimenetéből származó előfizetésnevet vagy -azonosítót:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

## <a name="create-an-iot-hub"></a>IoT Hub létrehozása

Az Azure parancssori felület használatával hozzon létre egy erőforráscsoportot, és vegye fel az IoT-központ.

1. Amikor létrehoz egy IoT-központot, erőforráscsoportban kell létrehoznia. Használhat meglévő erőforráscsoportot, vagy futtathatja a következő [parancsot erőforráscsoport létrehozásához][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > Az előző példában az erőforráscsoport az USA nyugati régiójában jön létre. Az `az account list-locations -o table` parancs futtatásával megtekintheti az elérhető helyek listáját.
    >
    >

2. Futtassa a következő [parancs létrehoz egy IoT-központot] [ lnk-az-iot-command] az erőforráscsoportban, globálisan egyedi névvel az IoT hub:
    
    ```azurecli
    az iot hub create --name {your iot hub name} --resource-group {your resource group name} --sku S1
    ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


> [!NOTE]
> Az előző parancs létrehozza az IoT-központ az S1 az IP-címek, amelynek kell fizetni. További információkért lásd: [Azure IoT Hub árképzési][lnk-iot-pricing].
>

## <a name="remove-an-iot-hub"></a>Távolítsa el az IoT-központ

Használhatja az Azure parancssori felület a [egyedi erőforrás törlése][lnk-az-resource-command], például egy IoT-központ, vagy törölje az erőforráscsoportot és az ahhoz tartozó összes erőforrást, beleértve az IoT-központok.

IoT Hub törléséhez futtassa a következő parancsot:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Erőforráscsoport és az ahhoz tartozó összes erőforrás törléséhez futtassa a következő parancsot:

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>További lépések
Az IoT-központ fejlesztésével kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* [IoT Hub fejlesztői útmutató][lnk-devguide]

Az IoT-központ képességeit további megismeréséhez lásd:

* [Az IoT-központ kezelése az Azure portál használatával][lnk-portal]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
