---
title: Azure-lemezek használata az aks-sel
description: Azure-lemezek használata az aks-sel
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: f807264dc2c2e07ccd175fb1b0427b7ce9e9f524
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/06/2018
ms.locfileid: "37868245"
---
# <a name="volumes-with-azure-disks"></a>Az Azure-lemezek kötetek

Tárolóalapú alkalmazások gyakran kell eléréséhez, és a egy külső adatmennyiség adatok megőrzése. Azure-lemezek a külső adatok tárolását is használható. Ez a cikk részletesen bemutatja, hogyan egy Azure-lemez az Azure Kubernetes Service (AKS)-fürtben Kubernetes kötetként.

A Kubernetes-köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Hozzon létre egy Azure-lemez

Az Azure által felügyelt lemezt, egy Kubernetes-kötet csatlakoztatása, mielőtt a lemez szerepelniük kell az AKS **csomópont** erőforráscsoportot. Az erőforráscsoport nevét az első a [az resource show] [ az-resource-show] parancsot.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Használja a [az lemez létrehozása] [ az-disk-create] parancsot az Azure-lemez létrehozásához.

Frissítés `--resource-group` az előző lépésben gyűjtött az erőforráscsoport nevére és `--name` egy Ön által választott nevére.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

A lemez létrehozása után az alábbihoz hasonló kimenetnek kell megjelennie. Ez az érték a lemez Azonosítóját, amely használatos, amikor csatlakoztatni a lemezt.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```
> [!NOTE]
> Azure managed disksbe számlázása Termékváltozat által egy adott méretet. Ezen SKU-k és közé eső 32GiB S4 vagy P4 szintű lemezek 4TiB S50-es vagy P50 lemez. Továbbá, az átviteli sebesség és IOPS-teljesítmény egy prémium szintű felügyelt lemez a Termékváltozat és a példány mérete az AKS-fürt csomópontjainak függ. Lásd: [díjszabás és a felügyelt lemezek teljesítményének][managed-disk-pricing-performance].

> [!NOTE]
> Ha a lemez hozzon létre egy külön erőforráscsoportban van szüksége, is kell a fürthöz tartozó egyszerű szolgáltatásról az Azure Kubernetes Service (AKS) ad hozzá a lemezt a tartalmazó erőforráscsoportot a `Contributor` szerepkör. 
>

## <a name="mount-disk-as-volume"></a>Lemez csatolása kötetként

Csatlakoztassa az Azure-lemez be a pod a kötetet a tároló specifikációja konfigurálásával.

Hozzon létre egy új fájlt `azure-disk-pod.yaml` a következő tartalommal. Frissítés `diskName` az újonnan létrehozott lemez nevét és `diskURI` lemez azonosítóval. Továbbá jegyezze fel a `mountPath`, azaz az elérési utat, amelyen az Azure-lemez található a pod csatlakoztatva van.

```yaml
apiVersion: v1
kind: Pod
metadata:
 name: azure-disk-pod
spec:
 containers:
  - image: microsoft/sample-aks-helloworld
    name: azure
    volumeMounts:
      - name: azure
        mountPath: /mnt/azure
 volumes:
      - name: azure
        azureDisk:
          kind: Managed
          diskName: myAKSDisk
          diskURI: /subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

A kubectl használatával hozzon létre a pod.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Egy Azure-lemez csatlakoztatásának helye a most már rendelkezik egy futó pod a `/mnt/azure`.

## <a name="next-steps"></a>További lépések

További tudnivalók a Kubernetes-kötetek Azure-lemezek használatával.

> [!div class="nextstepaction"]
> [Kubernetes-beépülő modul Azure-lemezek][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[managed-disk-pricing-performance]: https://azure.microsoft.com/pricing/details/managed-disks/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
