---
author: baanders
description: fájl belefoglalása az Azure Digital Twins-hoz – a Cloud Shell és a IoT-bővítmény beállítása
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 8a3efc9ba8fc8ffd8c0eca4340e1948c388c0a13
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494961"
---
Az Azure Digital Twins használatának megkezdéséhez nyissa meg [Azure Cloud Shell](https://shell.azure.com) ablakban az első teendő, hogy jelentkezzen be, és állítsa be a rendszerhéj környezetét az előfizetéséhez ehhez a munkamenethez. Futtassa ezeket a parancsokat a Cloud Shellban:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> A fenti parancsban szereplő azonosító helyett az előfizetés nevét is használhatja. 

Ha első alkalommal használta ezt az előfizetést az Azure digitális Twins szolgáltatásban, futtassa ezt a parancsot az Azure Digital Twins-névtérben való regisztráláshoz. (Ha nem biztos abban, hogy a múltban is elvégezte a futtatást, akkor is.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Ezután adja hozzá az [**Azure CLI-hez készült Microsoft Azure IoT-bővítményt**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) a Cloud Shellhoz, hogy engedélyezze a parancsokat az Azure Digital Twins és más IoT-szolgáltatások használatával való interakcióhoz. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Most már készen áll az Azure Digital Twins-vel való együttműködésre a Cloud Shell.

Ezt bármikor ellenőrizheti, ha `az dt -h` az elérhető legfelső szintű Azure digitális Twins-parancsok listáját szeretné megtekinteni.