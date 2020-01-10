---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453703"
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

Előfordulhat, hogy el kívánja távolítani a klónozott minta fájlokat a fejlesztői gépről.