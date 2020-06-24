---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – a Cloud Shell és a IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296967"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell munkamenet beállítása

Egy Cloud Shell ablak megnyitása után először jelentkezzen be, és állítsa be a rendszerhéj környezetét az előfizetéséhez ehhez a munkamenethez. Futtassa ezeket a parancsokat a Cloud Shellban:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Ha első alkalommal használta ezt az előfizetést az Azure digitális Twins szolgáltatásban, futtassa ezt a parancsot az Azure Digital Twins-névtérben való regisztráláshoz. (Ha nem biztos abban, hogy a múltban is elvégezte a futtatást, akkor is.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ezután adja hozzá az [**Azure CLI-hez készült Microsoft Azure IoT-bővítményt**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) a Cloud Shellhoz, hogy engedélyezze a parancsokat az Azure Digital Twins és más IoT-szolgáltatások használatával való interakcióhoz. Ezzel a paranccsal adhatja hozzá a bővítményt:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Ha korábban már telepítette a bővítményt, a kimenetben az "Azure-IOT" bővítmény már telepítve van. " Ha ez történik, futtassa a következőt, és győződjön meg arról, hogy rendelkezik a legújabb frissítéssel: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

Most már készen áll az Azure Digital Twins-vel való együttműködésre a Cloud Shell.

Ezt bármikor ellenőrizheti, ha `az dt -h` az elérhető legfelső szintű Azure digitális Twins-parancsok listáját szeretné megtekinteni.