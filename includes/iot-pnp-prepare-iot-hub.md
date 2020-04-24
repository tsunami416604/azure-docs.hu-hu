---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: a3340eb9e53afa83c35109bad7d22f81413dd644
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "80234168"
---
## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) . Ha nem rendelkezik IoT-hubhoz, kövesse az [alábbi utasításokat, és hozzon létre egyet](../articles/iot-hub/iot-hub-create-using-cli.md).

Ha helyileg használja az Azure CLI-t, először jelentkezzen be az Azure-előfizetésbe a használatával `az login`. Ha ezeket a parancsokat a Azure Cloud Shell futtatja, a rendszer automatikusan bejelentkezett.

Ha helyileg használja az Azure CLI-t, akkor `az` a verziónak **2.0.73** vagy újabbnak kell lennie. a Azure Cloud Shell a legújabb verziót használja. A `az --version` parancs használatával keresse meg a számítógépen telepített verziót.

A következő parancs futtatásával adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a példányhoz:

```azurecli-interactive
az extension add --name azure-iot
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDeviceID** helyőrzőket a saját _IoT hub nevére_ és az Ön által választott _eszköz-azonosítóra_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Futtassa a következő parancsot az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ lekéréséhez (jegyezze fel később a használatot):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
