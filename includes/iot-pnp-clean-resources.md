---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152013"
---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a további IoT Plug and Play cikkekkel, megtarthatja és újra felhasználhatja az ebben a rövid útmutatóban használt erőforrásokat. Ellenkező esetben törölheti az ebben a rövid útmutatóban létrehozott erőforrásokat a további díjak elkerülése érdekében.

A hub és a regisztrált eszköz egyszerre is törölhető a teljes erőforráscsoport törlésével a következő paranccsal az Azure CLI-hez. (Ne használja ezt, azonban ha ezek az erőforrások megosztanak egy erőforráscsoportot más, különböző célokra használt erőforrásokkal.)

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

Érdemes lehet eltávolítani a klónozott SDK-fájlokat is a fejlesztői gépről.