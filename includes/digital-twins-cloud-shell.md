---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – a Cloud Shell és a IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612892"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása

Egy Cloud Shell ablak megnyitása után először jelentkezzen be, és állítsa be a rendszerhéj környezetét az előfizetéséhez ehhez a munkamenethez. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Ha első alkalommal használta ezt az előfizetést az Azure digitális Twins szolgáltatásban, futtassa ezt a parancsot az Azure Digital Twins-névtérben való regisztráláshoz. (Ha nem biztos abban, hogy a múltban is elvégezte a futtatást, akkor is.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ezután futtassa a következő parancsot a Cloud Shell-példányban az Azure CLI-hez készült Microsoft Azure IoT-bővítmény hozzáadásához.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> Ez a cikk a nevű Azure IoT bővítmény legújabb verzióját használja `azure-iot` . Az örökölt verzió neve `azure-iot-cli-ext` . Egyszerre csak egy verziót kell telepíteni. A parancsot használhatja a `az extension list` jelenleg telepített bővítmények érvényesítéséhez.
> A paranccsal `az extension remove --name azure-cli-iot-ext` távolíthatja el a bővítmény örökölt verzióját.
> A `az extension add --name azure-iot` bővítmény új verziójának hozzáadásához használja a következőt:. A telepített bővítmények megtekintéséhez használja a következőt: `az extension list` .

> [!TIP]
> A futtatásával `az dt -h` megtekintheti a legfelső szintű Azure digitális Twins-parancsokat.