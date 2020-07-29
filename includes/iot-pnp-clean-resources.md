---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336895"
---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a további IoT Plug and Play cikkekkel, megtarthatja és újra felhasználhatja a cikkben használt erőforrásokat. Ellenkező esetben törölheti a cikkben létrehozott erőforrásokat a további díjak elkerülése érdekében.

A hubot és a regisztrált eszközt egyszerre is törölheti, ha törli a teljes erőforráscsoportot az alábbi Azure CLI-paranccsal. Ne használja ezt a parancsot, ha ezek az erőforrások megosztanak egy erőforráscsoportot a megőrizni kívánt egyéb erőforrásokkal.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Ha csak az IoT hubot szeretné törölni, futtassa az alábbi parancsot az Azure CLI használatával:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Ha csak az IoT hub-ban regisztrált eszköz identitását szeretné törölni, futtassa az alábbi parancsot az Azure CLI használatával:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Előfordulhat, hogy el kívánja távolítani a klónozott minta fájlokat a fejlesztői gépről.
