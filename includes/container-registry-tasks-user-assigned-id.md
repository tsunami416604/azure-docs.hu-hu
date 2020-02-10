---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ceda7bd6bd165df1eece555c6ce8a9a6c863b2c1
ms.sourcegitcommit: 323c3f2e518caed5ca4dd31151e5dee95b8a1578
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2020
ms.locfileid: "77112330"
---
### <a name="create-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás létrehozása

Hozzon létre egy *myACRTasksId* nevű identitást az előfizetésben az az [Identity Create][az-identity-create] parancs használatával. Használhatja ugyanazt az erőforráscsoportot, amelyet korábban használt egy tároló-beállításjegyzék létrehozásához, vagy egy másikat.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRTasksId
```

A felhasználó által hozzárendelt identitás a következő lépésekben való konfigurálásához használja az az [Identity show][az-identity-show] parancsot az identitás erőforrás-azonosítójának, résztvevő-azonosítójának és ügyfél-azonosítójának a változókban való tárolásához.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myACRTasksId --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show