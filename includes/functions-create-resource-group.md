---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: c20f86fe7fdcfc7ecc940923a8c98fa1fbf4cf65
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178895"
---
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például a függvényalkalmazásokat, az adatbázisokat és a tárfiókokat).

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.  
Ha nem a Cloud Shellt használja, először be kell jelentkeznie az `az login` használatával.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. Az összes Azure App Service-csomagot támogató hely megtekintéséhez futtassa az [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations) parancsot.
