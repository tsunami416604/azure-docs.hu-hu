---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 10/24/2019
ms.openlocfilehash: 6b6c481a7443953a70fe3db45121f5f9982d8710
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152030"
---
## <a name="prepare-an-iot-hub"></a>IoT hub előkészítése

A rövid útmutató elvégzéséhez szüksége lesz egy Azure IoT hub-ra is az Azure-előfizetésében. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. Ha nem rendelkezik IoT-hubhoz, kövesse az [alábbi utasításokat, és hozzon létre egyet](../articles/iot-hub/iot-hub-create-using-cli.md).

> [!IMPORTANT]
> A nyilvános előzetes verzióban a IoT Plug and Play funkciói csak az **USA középső**régiójában, Észak- **Európában**és Kelet- **japán** régióban létrehozott IoT-hubokon érhetők el.

A következő parancs futtatásával adja hozzá az Azure CLI-hez készült Microsoft Azure IoT-bővítményt a Cloud Shell-példányhoz:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

A következő parancs futtatásával hozza létre az eszköz identitását az IoT hub-ban. Cserélje le a **YourIoTHubName** és a **YourDeviceID** helyőrzőket a saját _IoT hub nevére_ és az Ön által választott _eszköz-azonosítóra_ .

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Futtassa a következő parancsot az imént regisztrált eszközhöz tartozó _eszköz-kapcsolódási karakterlánc_ lekéréséhez (jegyezze fel később a használatot):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDevice> --output table
```

Futtassa a következő parancsot a hub _IoT hub-beli kapcsolódási karakterláncának_ lekéréséhez (jegyezze fel később a használatra):

```azurecli-interactive
az iot hub show-connection-string --hub-name <YourIoTHubName> --output table
```