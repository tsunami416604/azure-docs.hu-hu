---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 3f3e7c54ec59c42ed102da682fc9aa077709ef3f
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2020
ms.locfileid: "78262490"
---
## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Ha nem rendelkezik IoT-hubhoz, kövesse az [alábbi utasításokat, és hozzon létre egyet](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

Ha helyileg használja az Azure CLI-t, először jelentkezzen be az Azure-előfizetésbe `az login`használatával. Ha ezeket a parancsokat a Azure Cloud Shell futtatja, a rendszer automatikusan bejelentkezett.

Ha helyileg használja az Azure CLI-t, a `az` verziójának **2.0.73** vagy újabbnak kell lennie. a Azure Cloud Shell a legújabb verziót használja. A `az --version` parancs használatával keresse meg a számítógépen telepített verziót.

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