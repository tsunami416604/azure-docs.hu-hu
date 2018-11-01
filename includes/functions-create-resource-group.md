---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: d44865dc3189a7f9dc05106baf9f4d120e5e8bf6
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2018
ms.locfileid: "50133916"
---
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például a függvényalkalmazásokat, az adatbázisokat és a tárfiókokat).

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.  
Ha nem a Cloud Shellt használja, először be kell jelentkeznie az `az login` használatával.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```
Az erőforráscsoportot és az erőforrásokat általában a közelében található régiókban hozhatja létre. Az összes Azure App Service-csomagot támogató hely megtekintéséhez futtassa az [az appservice list-locations](/cli/azure/appservice#az-appservice-list-locations) parancsot.