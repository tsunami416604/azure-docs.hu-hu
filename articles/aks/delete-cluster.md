---
title: Az Azure Kubernetes Service (AKS)-fürt törlése
description: Törlés és az AKS-fürt a parancssori felület vagy az Azure portállal.
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: c8eab17a5c635560d9a5274eb038845238968e02
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39439935"
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Az Azure Kubernetes Service (AKS)-fürt törlése

Azure Kubernetes Service-fürt törlésekor az erőforráscsoport, amelyben a fürt üzembe lett helyezve marad, azonban minden AKS-kapcsolódó erőforrás törlődjön. Ez a dokumentum bemutatja, hogyan törölhet egy AKS-fürtöt az Azure CLI-vel és az Azure portal használatával.

Törölje a fürtöt, mellett törölhetők, amely is törli az AKS-fürtöt az erőforráscsoport, amelyben telepítve lett.

## <a name="azure-cli"></a>Azure CLI

Használja a [az aks törlése] [ az-aks-delete] parancs törli az AKS-fürtöt.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Az alábbi lehetőségek érhetők el a `az aks delete` parancsot.

| Argumentum | Leírás | Szükséges |
|---|---|:---:|
| `--name` `-n` | Erőforrás neve a felügyelt fürt. | igen |
| `--resource-group` `-g` | Az Azure Kubernetes Service-erőforráscsoport nevére. | igen |
| `--no-wait` | Ne várja meg, amíg a hosszú ideig futó művelet befejeződik. | nem |
| `--yes` `-y` | Nem kér megerősítést. | nem |

## <a name="azure-portal"></a>Azure Portal

Az Azure-portálon keresse meg az Azure Kubernetes Service (AKS)-erőforrást tartalmazó erőforráscsoportot, válassza ki az erőforrást, és kattintson **törlése**. A törlési művelet megerősítését kéri.

![AKS-fürt portal törlése](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az-aks-delete