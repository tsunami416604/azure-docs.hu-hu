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
## <a name="prepare-an-iot-hub"></a>IoT-központ előkészítése

A rövid útmutató befejezéséhez az Azure-előfizetésben is szüksége van egy Azure IoT-hubra. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. Ha nem rendelkezik IoT-központtal, kövesse [az alábbi utasításokat a létrehozáshoz.](../articles/iot-hub/iot-hub-create-using-cli.md)

Ha az Azure CLI-t helyileg használja, először `az login`jelentkezzen be az Azure-előfizetésébe a használatával. Ha ezeket a parancsokat az Azure Cloud Shellben futtatja, automatikusan be van jelentkezve.

Ha az Azure CLI-t helyileg `az` használja, a verziónak **2.0.73-as** vagy újabb verziónak kell lennie; az Azure Cloud Shell a legújabb verziót használja. A `az --version` parancs segítségével ellenőrizze a számítógépre telepített verziót.

Futtassa a következő parancsot, és adja hozzá a Microsoft Azure IoT-bővítményt az Azure CLI-hez a példányhoz:

```azurecli-interactive
az extension add --name azure-iot
```

Futtassa a következő parancsot az eszköz identitásának létrehozásához az IoT hubban. Cserélje le a **YourIoTHubName** és **a YourDeviceID** helyőrzők a saját _IoT Hub-név_ és egy _eszközazonosítót_ a választás.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Futtassa a következő parancsot az imént regisztrált eszköz _eszközkapcsolati karakterláncának_ lekérni (megjegyzés későbbi használatra):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
