---
title: Egy Azure Kubernetes szolgáltatás (AKS) fürt törlése
description: Törlés és AKS-fürtöt a parancssori felületen vagy az Azure portálon.
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: article
ms.date: 2/05/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: e006466d1450471900a8635c49d3bc6c3a73d476
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
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