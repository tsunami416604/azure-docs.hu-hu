---
title: Egy Azure Kubernetes szolgáltatás (AKS) fürt törlése
description: Törlés és AKS-fürtöt a parancssori felületen vagy az Azure portálon.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 6391e36eff60634e07a90c1e6b5f0f44ee60d46b
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="delete-an-azure-kubernetes-service-aks-cluster"></a>Egy Azure Kubernetes szolgáltatás (AKS) fürt törlése

Az Azure Kubernetes szolgáltatás fürt törlésekor az erőforráscsoportot, amely a fürt tették elérhetővé telepítésre marad, de AKS kapcsolatos összes erőforrások törlése. Ez a dokumentum bemutatja, hogyan az Azure CLI és az Azure portál használatával AKS fürt törlése.

Törölje a fürtöt, mellett törölhetők, amely is törli a AKS fürt az erőforráscsoportot, ahol telepítve volt.

## <a name="azure-cli"></a>Azure CLI

Használja a [az aks törlése] [ az-aks-delete] parancs futtatásával törölheti a AKS fürtöt.

```azurecli-interactive
az aks delete --resource-group myResourceGroup --name myAKSCluster
```

Az alábbi beállítások érhetők el a a `az aks delete` parancsot.

| Argumentum | Leírás | Szükséges |
|---|---|:---:|
| `--name` `-n` | A felügyelt fürt erőforrás nevét. | igen |
| `--resource-group` `-g` | Az Azure Kubernetes erőforráscsoport neve. | igen |
| `--no-wait` | Ne várja meg, a hosszú futású művelet befejezéséhez. | nem |
| `--yes` `-y` | Nem kell megerősítést kérni. | nem |

## <a name="azure-portal"></a>Azure Portal

Az Azure-portálon, keresse meg az Azure Kubernetes szolgáltatás (AKS) erőforrást tartalmazó erőforráscsoportot, válassza ki az erőforrást, majd kattintson **törlése**. A delete művelet megerősítését kéri.

![AKS fürt portál törlése](media/container-service-delete-cluster/delete-aks-portal.png)

<!-- LINKS - internal -->
[az-aks-delete]: /cli/azure/aks?view=azure-cli-latest#az_aks_delete