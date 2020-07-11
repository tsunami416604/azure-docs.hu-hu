---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – a Cloud Shell és a IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277971"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása

Egy Cloud Shell ablak megnyitása után először jelentkezzen be, és állítsa be a rendszerhéj környezetét az előfizetéséhez ehhez a munkamenethez. Futtassa ezeket a parancsokat a Cloud Shellban:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Az előfizetését az előfizetés neve alapján is beállíthatja. Használja a következő parancsot: 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Ha első alkalommal használta ezt az előfizetést az Azure digitális Twins szolgáltatásban, futtassa ezt a parancsot az Azure Digital Twins-névtérben való regisztráláshoz. (Ha nem biztos abban, hogy a múltban is elvégezte a futtatást, akkor is.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ezután adja hozzá az [**Azure CLI-hez készült Microsoft Azure IoT-bővítményt**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) a Cloud Shellhoz, hogy engedélyezze a parancsokat az Azure Digital Twins és más IoT-szolgáltatások használatával való interakcióhoz. 

Először futtassa ezt a parancsot a már telepített bővítmények listájának megtekintéséhez.

```azurecli-interactive
az extension list
```

A kimenetben keresse meg az `"name"` egyes listaelemek mezőjét, és tekintse meg a bővítmények nevét.

A kimenet használatával határozza meg, hogy a következő parancsok melyike fusson a bővítmény telepítéséhez (több is futtatható).
* Ha a lista a `azure-iot` következőket tartalmazza: már rendelkezik a kiterjesztéssel. A parancs futtatásával győződjön meg arról, hogy rendelkezik a legújabb frissítéssel:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Ha a lista **nem** tartalmazza a következőt `azure-iot` : telepítenie kell a bővítményt. Használja a következő parancsot:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Ha a lista tartalmazza a `azure-iot-cli-ext` következőt: Ez a bővítmény örökölt verziója. Egyszerre csak egy bővítményt kell telepíteni, ezért el kell távolítania az örökölt bővítményt. Használja a következő parancsot:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```

Most már készen áll az Azure Digital Twins-vel való együttműködésre a Cloud Shell.

Ezt bármikor ellenőrizheti, ha `az dt -h` az elérhető legfelső szintű Azure digitális Twins-parancsok listáját szeretné megtekinteni.