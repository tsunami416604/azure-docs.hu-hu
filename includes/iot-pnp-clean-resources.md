---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453703"
---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha további IoT Plug and Play-cikkekkel szeretné folytatni, megtarthatja és újra felhasználhatja az ebben a rövid útmutatóban használt erőforrásokat. Ellenkező esetben törölheti az ebben a rövid útmutatóban létrehozott erőforrásokat a további költségek elkerülése érdekében.

A hub és a regisztrált eszköz egyszerre törölheti a teljes erőforráscsoport törlésével a következő parancsot az Azure CLI. (Ne használja azonban ezt, ha ezek az erőforrások megosztanak egy erőforráscsoportot más erőforrásokkal, amelyekkel különböző célokra rendelkezik.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Csak az IoT hub törléséhez futtassa a következő parancsot az Azure CLI használatával:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Ha csak az IoT hubon regisztrált eszközidentitást szeretné törölni, futtassa a következő parancsot az Azure CLI használatával:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Előfordulhat, hogy el szeretné távolítani a klónozott mintafájlokat a fejlesztői gépről.