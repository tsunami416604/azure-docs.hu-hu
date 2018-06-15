---
title: Egy Azure Kubernetes szolgáltatás (AKS) fürt létrehozása
description: Hozzon létre egy AKS fürtöt a parancssori felületen vagy az Azure-portálon.
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 02/12/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 00672b6272ce9c775621e519c327c0b8368bc220
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935069"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster"></a>Egy Azure Kubernetes szolgáltatás (AKS) fürt létrehozása

Az Azure Kubernetes szolgáltatás (AKS) fürtöt vagy az Azure parancssori felület, vagy az Azure-portálon hozhatja létre.

## <a name="azure-cli"></a>Azure CLI

Használja a [az aks létrehozása] [ az-aks-create] parancsot a AKS fürt létrehozásához.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster
```

Az alábbi beállítások érhetők el a a `az aks create` parancsot.

| Argumentum | Leírás | Szükséges |
|---|---|:---:|
| `--name` `-n` | A felügyelt fürt erőforrás nevét. | igen |
| `--resource-group` `-g` | Az Azure Kubernetes erőforráscsoport neve. | igen |
| `--admin-username` `-u` | Felhasználónév a Linux virtuális gépek számára.  Alapértelmezett: azureuser. | nem |
| ` --client-secret` | A szolgáltatásnévhez társított titkos kulcs. | nem |
| `--dns-name-prefix` `-p` | DNS-előtagot a fürtök nyilvános IP-cím. | nem |
| `--generate-ssh-keys` | Hiányzik az SSH nyilvános és titkos kulcs fájlok hoz létre. | nem |
| `--kubernetes-version` `-k` | A fürt, például "1.7.9" vagy "1.8.2" létrehozásához használandó Kubernetes verzióját.  Alapértelmezett: 1.7.7. | nem |
| `--no-wait` | Ne várja meg, a hosszú futású művelet befejezéséhez. | nem |
| `--node-count` `-c` | A csomópont-készletek a csomópontok alapértelmezett számát.  Alapértelmezett: 3. | nem |
| `--node-osdisk-size` | A csomópont-készlet virtuális gép GB osDisk mérete. | nem |
| `--node-vm-size` `-s` | A virtuális gép mérete.  Alapértelmezett: standard D1 v2 típusú. | nem |
| `--service-principal` | Fürt-hitelesítéshez használt egyszerű. | nem |
| `--ssh-key-value` | SSH kulcsfájl érték vagy kulcs elérési útvonala.  Alapértelmezett: ~ /.ssh/id_rsa.pub. | nem |
| `--tags` | Lemezterület elválasztott címkéket a "key [= érték]" formátumban. Használjon "törölje a meglévő címkék. | nem |

## <a name="azure-portal"></a>Azure Portal

További információk az Azure portálon AKS fürt telepítése, lásd: az Azure Kubernetes szolgáltatás (AKS) [az Azure portál gyors üzembe helyezés][aks-portal-quickstart].

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az_aks_create
[aks-portal-quickstart]: kubernetes-walkthrough-portal.md
