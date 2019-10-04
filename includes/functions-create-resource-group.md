---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 0fba49ec5a610d03ece2dce7fa5bb0d9d094ab4c
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203153"
---
## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat (például a függvényalkalmazásokat, az adatbázisokat és a tárfiókokat).

A következő példában létrehozunk egy `myResourceGroup` nevű erőforráscsoportot.  
Ha nem használja a Cloud Shellt, először jelentkezzen `az login`be a használatával.

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

Az erőforráscsoportot és az erőforrásokat általában egy közeli [régióban](https://azure.microsoft.com/global-infrastructure/regions/) hozza létre. 
