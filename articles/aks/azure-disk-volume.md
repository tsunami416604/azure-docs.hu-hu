---
title: Azure-lemezeket használata AKS
description: Azure-lemezeket használata AKS
services: container-service
author: neilpeterson
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/21/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4af4620ff7a17cae76c4d5f2cf1a30ce4a3dccd8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "34597067"
---
# <a name="volumes-with-azure-disks"></a>Az Azure-lemezeket kötetek

Tároló-alapú alkalmazások gyakran kell elérni, és egy külső adatmennyiség adatok megőrzéséhez. Azure-lemezeket a külső adattár használható. Ez a cikk adatokat Azure lemezzel Azure Kubernetes szolgáltatás (AKS)-fürtben lévő Kubernetes kötetként.

Kubernetes köteteken további információkért lásd: [Kubernetes kötetek][kubernetes-volumes].

## <a name="create-an-azure-disk"></a>Az Azure lemez létrehozása

Egy Azure által kezelt lemezre Kubernetes kötet csatlakoztatása, mielőtt a lemez már léteznie kell a AKS **csomópont** erőforráscsoportot. Az erőforráscsoport neve az beszerzése a [az erőforrás megjelenítése] [ az-resource-show] parancsot.

```azurecli-interactive
$ az resource show --resource-group myResourceGroup --name myAKSCluster --resource-type Microsoft.ContainerService/managedClusters --query properties.nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Használja a [az lemez létrehozása] [ az-disk-create] parancsot lemezt az Azure létrehozásához.

Frissítés `--resource-group` az előző lépésben összegyűjtött az erőforráscsoport nevét és `--name` az Ön által választott névre.

```azurecli-interactive
az disk create \
  --resource-group MC_myResourceGroup_myAKSCluster_eastus \
  --name myAKSDisk  \
  --size-gb 20 \
  --query id --output tsv
```

A lemez létrehozása, a következőhöz hasonló kimenetnek kell megjelennie. Ez az érték használatos, ha a lemez csatlakoztatása Lemezazonosító.

```console
/subscriptions/<subscriptionID>/resourceGroups/MC_myAKSCluster_myAKSCluster_eastus/providers/Microsoft.Compute/disks/myAKSDisk
```

## <a name="mount-disk-as-volume"></a>Csatlakoztassa a lemezt kötetként

A kötet konfigurálása a tároló spec csatlakoztassa azokat a pod lemezt az Azure.

Hozzon létre egy új fájlt `azure-disk-pod.yaml` a következő tartalommal. Frissítés `diskName` nevű az újonnan létrehozott lemezt, és `diskURI` lemez azonosítóval. Emellett tekintse meg a `mountPath`, ez az az elérési utat, amelyen a lemezt az Azure a fogyasztanak csatlakoztatva van.

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

Használjon kubectl fogyasztanak létrehozásához.

```azurecli-interactive
kubectl apply -f azure-disk-pod.yaml
```

Egy Azure-csatlakoztatni lemezzel most már rendelkezik egy futó pod a `/mnt/azure`.

## <a name="next-steps"></a>További lépések

További tudnivalók az Azure-lemezeket Kubernetes kötetek.

> [!div class="nextstepaction"]
> [Azure-lemezeket Kubernetes beépülő modul][kubernetes-disks]

<!-- LINKS - external -->
[kubernetes-disks]: https://github.com/kubernetes/examples/blob/master/staging/volumes/azure_disk/README.md
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/

<!-- LINKS - internal -->
[az-disk-list]: /cli/azure/disk#az_disk_list
[az-disk-create]: /cli/azure/disk#az_disk_create
[az-group-list]: /cli/azure/group#az_group_list
[az-resource-show]: /cli/azure/resource#az-resource-show
